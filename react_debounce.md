
```javascript
import React, { Component } from 'react';
import { connect } from 'react-redux';
import { is } from 'immutable';
import ListPreference from "../../components/ListPreference";
import ListFilter from "../../components/ListFilter";
import ListArticle from "../../components/ListArticle";
import PaginationFloat from '../../components/PaginationFloat';
import { callApi } from '../../clientApiMiddleware';
import { getDocumentList, patchCurrentPages, deletePaginationFloat, scrollGetDocumentList, patchCurrentPagesText, articlePreview, articleChecked, articleUncheck, articleSimilar, articleUnSimilar, articleSimilarRemove, deleteDocumentList } from './actions';
import { updateTmplFold, triggerPreview, togArticleCheckbox, togArticleSimilar } from '../HomePage/actions';
import $ from "jquery";
import "./appear";
import ListPagination from '../../components/ListPagination';
import _ from 'lodash';
// import { withStyles, debounce, killEvent, handleRenderError } from 'react-decoration';

class ListArticleContainer extends Component {
  constructor(props) {
    super(props);
      this.debounceScroll = _.debounce(this.infiScrolling.bind(this), 100);
  }
  componentWillMount() {
    // get document list for "page initial" and "fullpage reload".
    const { folderRef, currentFolderId, articlePrefers, getDocumentListFunc, articleFilters, paginationFloat } = this.props;
    const folderID = currentFolderId.get('currentFolderId');

    // hard code for develoging
    const dateMapping = {
      "今天（0點到現在）": "today",
      昨天: "yesterday",
      最近三天: "last-3-days",
      最近一周: "last-week",
      最近兩週: "last-2-weeks",
      最近一個月: "last-month",
      最近三個月: "last-3-months",
      最近六個月: "last-6-months"
    };

    const mediaNameMapping = {
      總覽: "all",
      全部: "all",
      報刊: "print",
      雜誌: "",
      網站: "web",
      廣播: "boardcast",
      論壇: "wlforum",
      博客: "wlblog",
      視頻: "video",
      App: "app",
      "fa fa-weibo name": "microblog",
      "fa fa-wechat name": "",
      "fa fa-facebook-official name": "",
      "fa fa-youtube-play name": "",
      "fa fa-instagram name": "",
      搜狐: "",
      百家号: "",
      微信: "weixin"
    };

    const sortOrderMapping = {
      我的最愛: "ASC:favour",
      最近收到的時間: "DESC:tracktime",
      "發布日期 (由最新至最舊)": "DESC:date",
      "發布日期 (由最舊至最新)": "ASC:date",
      "相關程度 (由高至低)": "DESC:score",
      "媒體名稱 (A 至 Z)": "ASC:pub-name",
      "媒體名稱 (Z 至 A)": "DESC:pub-name",
      "編者 排序": "ASC:custom-folder-doc",
      自選媒體排序: "ASC:custom-pub-name",
      最高點擊率: "DESC:viewCount",
      最高電郵發送率: "DESC:sendCount",
      最新文章: "DESC:date",
      依媒體名稱: "ASC:pub-name",
      最高標題相關度: "DESC:score"
    };

    const DateRange = dateMapping[articleFilters.get("dateRange")] || "today";
    const MediaType = mediaNameMapping[articleFilters.get("mediaName")] || "all";
    const SortOrder = sortOrderMapping[articlePrefers.get("articleListSorting")] || "DESC:tracktime";
    const NumberPerPage = articlePrefers.get("articleListNumPerPage") || "50";


    if (typeof folderID === 'string' && folderID !== "") {
      const Refs = folderRef.get('folderRef');
      const catPath = Refs[folderID].catPath;
      if (catPath) {
        getDocumentListFunc(catPath, DateRange, MediaType, SortOrder, NumberPerPage, "1");
      }
    }
  }

  shouldComponentUpdate(nextProps, nextState) {
  // this lifeCycle will be trigger when user chose another FolderID
    const { folderRef, currentFolderId, articleFilters, getDocumentListFunc, articlePrefers, paginationFloat, resetPaginationFloat, documentList } = this.props;

    // Does "current FolderID" change ?
    const nextFoldID = nextProps.currentFolderId.get('currentFolderId') || "";
    const isFolderIDChange = currentFolderId.get('currentFolderId') !== nextFoldID;


    // hard code for developing
    const dateMapping = {
      "今天（0點到現在）": "today",
      昨天: "yesterday",
      最近三天: "last-3-days",
      最近一周: "last-week",
      最近兩週: "last-2-weeks",
      最近一個月: "last-month",
      最近三個月: "last-3-months",
      最近六個月: "last-6-months"
    };

    // Does "date range" change?
    const nextDateRange = dateMapping[nextProps.articleFilters.get("dateRange")] || "today";
    const isDateRangeChange = dateMapping[articleFilters.get("dateRange")] !== nextDateRange;

    const mediaNameMapping = {
      總覽: "all",
      全部: "all",
      報刊: "print",
      雜誌: "",
      網站: "web",
      廣播: "boardcast",
      論壇: "wlforum",
      博客: "wlblog",
      視頻: "video",
      App: "app",
      "fa fa-weibo name": "microblog",
      "fa fa-wechat name": "",
      "fa fa-facebook-official name": "",
      "fa fa-youtube-play name": "",
      "fa fa-instagram name": "",
      搜狐: "",
      百家号: "",
      微信: "weixin"
    };

    // Does "date range" change?
    const nextMediaType = mediaNameMapping[nextProps.articleFilters.get("mediaName")] || "all";
    const isMediaNameChange = mediaNameMapping[articleFilters.get("mediaName")] !== nextMediaType;


    // 先這樣吧，先把能看到的參數都放進去！

// 這邊可能會有很多 or 判斷！
// 日期不同 || curID不同 || mediaType不同 || mediaName不同 || language不同 ||
// cluster不同 || pageSize不同 || sortOrder不同 || excerptEnable || metadataFields
    const sortOrderMapping = {
      我的最愛: "ASC:favour",
      最近收到的時間: "DESC:tracktime",
      "發布日期 (由最新至最舊)": "DESC:date",
      "發布日期 (由最舊至最新)": "ASC:date",
      "相關程度 (由高至低)": "DESC:score",
      "媒體名稱 (A 至 Z)": "ASC:pub-name",
      "媒體名稱 (Z 至 A)": "DESC:pub-name",
      "編者 排序": "ASC:custom-folder-doc",
      自選媒體排序: "ASC:custom-pub-name",
      最高點擊率: "DESC:viewCount",
      最高電郵發送率: "DESC:sendCount",
      最新文章: "DESC:date",
      依媒體名稱: "ASC:pub-name",
      最高標題相關度: "DESC:score"
    };

    const nextSortOrder = sortOrderMapping[nextProps.articlePrefers.get("articleListSorting")] || "DESC:tracktime";
    const isSortOrderChange = sortOrderMapping[articlePrefers.get("articleListSorting")] !== nextSortOrder;


    const nextNumberPerPage = nextProps.articlePrefers.get("articleListNumPerPage") || "10";
    const isNumberPerPage = articlePrefers.get("articleListNumPerPage") !== nextNumberPerPage;

    const nextPaginationFloat = nextProps.paginationFloat;


    if (typeof nextFoldID === 'string' && nextFoldID !== "" &&
        (isFolderIDChange ||
        isDateRangeChange ||
        isSortOrderChange ||
        isMediaNameChange ||
        isNumberPerPage)) {
      const Refs = folderRef.get('folderRef');
      const catPath = Refs[nextFoldID].catPath;
      if (catPath) {
        resetPaginationFloat(); // reset PaginationFloat e.x. totalPages, currentPage
        getDocumentListFunc(catPath, nextDateRange, nextMediaType, nextSortOrder, nextNumberPerPage, "1");
      }
      return true;
    } else if (!is(paginationFloat, nextPaginationFloat)) {
      return true; // "totalPages change" or "infinite scrolling" after recive new DocumentsList.
    }
    return true;
  }


  onScrollMove = (e) => {
    e.persist();
    this.debounceScroll(e);
  }

  infiScrolling(e) {
    // there are two purpose here.
    // 1. infinite Scrolling to load further data
    // 2. switch currentPage's text when scrolling to certain page content.

    const { paginationFloat, articlePrefers, documentList, scrollGetDocument } = this.props;
    const totalPages = paginationFloat.get('totalpages');
    const curPage = paginationFloat.get('page');
    const itemsPerPage = articlePrefers.get("articleListNumPerPage");
    const docList = documentList.get("documentList");
    const docCount = Number(docList.docCount);
    const scrollHeight = (e.target.scrollTop + window.innerHeight);

    const filter = $("#com_list_filter").appear().height() || 0;
    const pref = $("#com_list_preference").appear().height() || 0;
    const sep = $(".com-list-pagination").appear().height() || 0;
    const allAritElement = $("[id*=com_list_article-]");

    if (allAritElement[0]) {
      const articleHeight = $(`#${allAritElement[0].id}`).appear().height() || 0;

      let lowerNum = 0 + filter + pref;
      let upperNum = articleHeight + filter + pref;

      // detect scrolling into which "#com_list_article-" area, then change pageText
      allAritElement.map(a => {
        if (lowerNum + ((sep + articleHeight) * a) < scrollHeight &&
          scrollHeight < upperNum + ((sep + articleHeight) * a)) {
          const elementID = allAritElement[a].id;
          const scrollPage = elementID.substring(elementID.indexOf("-") + 1, elementID.length);
          // console.log("Scroll 到第 " + scrollPage + " 頁了")
          this.props.currentPagesText(scrollPage);
        }
        return "";
      });

      // const pageText = paginationFloat.get('pageText');
      if (e.target.scrollHeight > 1000) {
        const curPageText = Number(paginationFloat.get('pageText'));
        if ((e.target.scrollTop + window.innerHeight) > e.target.scrollHeight + 1) {
          if (curPageText * itemsPerPage < docCount) {
            // load next page's data.
            scrollGetDocument(curPageText + 1);
          }
        }
      }
    }
  }

  render() {
    const { folderRef, currentFolderId, paginationFloat, currentPages, documentList, articlePrefers, curArticleInfo, articleCheckedState, articleSimilarState, articlePreviewFunc, articleCheckedFunc, articleUncheckFunc, articleSimilarFunc, articleUnSimilarFunc, curAtricleClickFunc, togArticleCheckboxFunc, togArticleSimilarFunc, articleSimilarRemoveFunc } = this.props;

    const folderID = currentFolderId.get('currentFolderId');
    let folderName = "";
    if (typeof folderID === 'string' && folderID !== "") {
      const Refs = folderRef.get('folderRef');
      folderName = Refs[folderID].name;
    }

    const totalPages = paginationFloat.get('totalpages'); // get total pages number.
    const curPage = paginationFloat.get('page');
    const pageText = paginationFloat.get("pageText");
    const docObject = documentList.get('documentList');
    const itemsPerPage = articlePrefers.get("articleListNumPerPage");
    let docList = [];
    const listArticle = [];
    if (docObject.documents) {
      docList = docObject.documents;
      let pageCount = curPage;
      if (docList.length > itemsPerPage) {
        // the number of article more than a page, must be splice
        let loopIdx = 0;
        let inputArray = [];
        let listLength = docList.length;

        for (listLength; listLength > 0; listLength -= itemsPerPage) {
          inputArray = docList.slice(loopIdx * itemsPerPage, (itemsPerPage * (loopIdx + 1)))

          loopIdx += 1;

          if (listArticle.length > 0) {
            // Top dont need ListPagination
            listArticle.push(<ListPagination key={Math.floor(Math.random() * 100 + 1)} curPage={pageCount} totalPages={totalPages} currentPages={currentPages} />);
          }
          listArticle.push(<ListArticle
            key={pageCount}
            pageCount={pageCount}
            documentList={inputArray}
            curArticleInfo={curArticleInfo}
            articleCheckedState={articleCheckedState}
            articleSimilarState={articleSimilarState}
            articlePrefers={articlePrefers}
            paginationFloat={paginationFloat}
            articlePreviewFunc={articlePreviewFunc}
            articleCheckedFunc={articleCheckedFunc}
            articleUncheckFunc={articleUncheckFunc}
            articleSimilarFunc={articleSimilarFunc}
            articleUnSimilarFunc={articleUnSimilarFunc}
            curAtricleClickFunc={curAtricleClickFunc}
            togArticleCheckboxFunc={togArticleCheckboxFunc}
            togArticleSimilarFunc={togArticleSimilarFunc}
            articleSimilarRemoveFunc={articleSimilarRemoveFunc}
          />);
          pageCount += 1;
        }
      } else if (docList.length > 0) {
        listArticle.push(<ListArticle
          key={pageCount}
          pageCount={pageCount}
          documentList={docList}
          curArticleInfo={curArticleInfo}
          articleCheckedState={articleCheckedState}
          articleSimilarState={articleSimilarState}
          articlePrefers={articlePrefers}
          paginationFloat={paginationFloat}
          articlePreviewFunc={articlePreviewFunc}
          articleCheckedFunc={articleCheckedFunc}
          articleUncheckFunc={articleUncheckFunc}
          articleSimilarFunc={articleSimilarFunc}
          articleUnSimilarFunc={articleUnSimilarFunc}
          curAtricleClickFunc={curAtricleClickFunc}
          togArticleCheckboxFunc={togArticleCheckboxFunc}
          togArticleSimilarFunc={togArticleSimilarFunc}
          articleSimilarRemoveFunc={articleSimilarRemoveFunc}
        />);
        pageCount += 1;
      }
    }

    const loadingSpin = documentList.get('loading') ?
    <i className="fa fa-spinner fa-spin" style={{ fontSize: "48px"}}></i> :
    null;

    return (
      <div className="fold fold-1" onScroll={this.onScrollMove}>
        <div className="fold-body">
          <div className="container-fluid">
            <div className="row">
              <div className="col-xs-12">

                <ListPreference folderName={folderName} />
                <ListFilter />
                {listArticle}
                {loadingSpin}
                <PaginationFloat totalPages={totalPages} curPage={curPage} currentPages={currentPages} pageText={pageText} />

              </div>
            </div>
          </div>
        </div>
      </div>
    );
  }
}

ListArticleContainer.propTypes = {

};


const mapStateToProps = state => ({
  folderRef: state.get('folderRef'),
  currentFolderId: state.get('currentFolderId'),
  articleFilters: state.get('articleFilter'),
  articlePrefers: state.get('articlePrefer'),
  paginationFloat: state.get('paginationFloat'),
  documentList: state.get('documentList'),
  curArticleInfo: state.get('articlePreview'),
  articleCheckedState: state.get('articleChecked'),
  articleSimilarState: state.get('articleSimilar')
});

const mapDispatchToProps = (dispatch) => ({
  getDocumentListFunc: (catPath, nextDateRange, nextMediaType, nextSortOrder, numberPerPage, startIdx) => dispatch(getDocumentList(catPath, nextDateRange, nextMediaType, nextSortOrder, numberPerPage, startIdx)),
  currentPages: (curPage) => dispatch(patchCurrentPages(curPage)),
  resetPaginationFloat: () => dispatch(deletePaginationFloat()),
  scrollGetDocument: (curPageText) => dispatch(scrollGetDocumentList(curPageText)),
  currentPagesText: (curPageText) => dispatch(patchCurrentPagesText(curPageText)),
  articlePreviewFunc: (docID) => dispatch(articlePreview(docID)),
  articleCheckedFunc: (docID) => dispatch(articleChecked(docID)),
  articleUncheckFunc: (docID) => dispatch(articleUncheck(docID)),
  articleSimilarFunc: (docID) => dispatch(articleSimilar(docID)),
  articleUnSimilarFunc: (docID) => dispatch(articleUnSimilar(docID)),
  curAtricleClickFunc: (currentArticle, docId) => dispatch(triggerPreview(currentArticle, docId)),
  togArticleCheckboxFunc: (articleCheckedState, isArticleChecked) => dispatch(togArticleCheckbox(articleCheckedState, isArticleChecked)),
  togArticleSimilarFunc: (articleSimilarState, docId) => dispatch(togArticleSimilar(articleSimilarState, docId)),
  articleSimilarRemoveFunc: () => dispatch(articleSimilarRemove())
});

export default connect(mapStateToProps, mapDispatchToProps)(ListArticleContainer);

```
