<template xmlns:v-bind="http://www.w3.org/1999/xhtml" xmlns:v-on="">
  <div class="swagger-main">
    <div class="switch">
      <span style="cursor:pointer;" @click="switchA=0" :class="[switchA==0?'active':'']">接口说明</span>
      <span style="cursor:pointer;" @click="switchA=1" :class="[switchA==1?'active':'']">在线调试</span>
    </div>
    <div v-show="switchA==0" style="" class="swagger-content">
      <ul class="content-list" style="">
        <li><span>接口url</span>
          <div>
            <span>{{swaggerCategory[countTo] && swaggerCategory[countTo].pathName ? swaggerCategory[countTo].pathName : ""}}</span>
          </div>
        </li>
        <li><span>接口名称</span>
          <div>
            <span>{{swaggerCategory[countTo] && swaggerCategory[countTo].pathInfo ? swaggerCategory[countTo].pathInfo.summary : ""}}</span>
          </div>
        </li>
        <li><span>请求方式</span>
          <div><span>{{swaggerCategory[countTo] ? swaggerCategory[countTo].name : ""}}</span></div>
        </li>
        <li><span>consumes</span>
          <div>
            <span>{{swaggerCategory[countTo] && swaggerCategory[countTo].pathInfo && swaggerCategory[countTo].pathInfo.consumes ? swaggerCategory[countTo].pathInfo.consumes[0] : ""}}</span>
          </div>
        </li>
        <li><span>produces</span>
          <div>
            <span>{{swaggerCategory[countTo] && swaggerCategory[countTo].pathInfo && swaggerCategory[countTo].pathInfo.produces ? swaggerCategory[countTo].pathInfo.produces[0] : ""}}</span>
          </div>
        </li>
        <li><span>请求参数</span>
          <div>
            <div class="request-table"
                 v-if="swaggerCategory[countTo]&&swaggerCategory[countTo].pathInfo&&swaggerCategory[countTo].pathInfo.parameters">
              <ul>
                <li class="table-tr table-head">
                  <span class="table-td">参数名称</span>
                  <span class="table-td">说明</span>
                  <span class="table-td">类型</span>
                  <span class="table-td">条件</span>
                  <span class="table-td">in</span>
                  <span class="table-td">是否必须</span>
                </li>
                <div v-for="(item,key) in InterfaceRequest">
                  <form-fold :depth="0" :properties="item.properties&&item.properties.properties" :keyTo="key"
                             :item="item"></form-fold>
                </div>
              </ul>
              <!--  <table style="table-layout: fixed;    width: 100%;">
                  <thead>
                  <tr>
                    <th style="width: 20%;">参数名称</th>
                    <th>说明</th>
                    <th style="width: 10%;">类型</th>
                    <th style="width: 20%;">条件</th>
                    <th style="width: 5%;">in</th>
                    <th style="width: 10%;">是否必须</th>
                  </tr>
                  </thead>
                  <tbody>
                  <div  v-for="(item,key) in InterfaceRequest">
                    <form-fold :depth="0" :properties="item.properties&&item.properties.properties" :keyTo="key"
                               :item="item"></form-fold>
                  </div>
                  </tbody>
                </table>-->
            </div>
            <span v-else>暂无</span>
          </div>
        </li>
        <li><span>响应Model</span>
          <div v-if="typeof jsonObject=='array'||typeof jsonObject=='object' ">
            {
            <ul v-for="(item,key) in jsonObject">
              <Json-View v-bind:obj="item" :keyTo="key" v-bind:indentation="indentation"></Json-View>
            </ul>
            }
          </div>
          <div v-else>无</div>
        </li>
        <li><span>响应参数说明</span>
          <div class="ResponseParameter">
            <span v-if="(typeof InterfaceResponse) != 'object'">{{InterfaceResponse}}</span>
            <ul v-else>
              <li class="head"><span>参数名称</span><span>类型</span><span>说明</span></li>
              <div v-for="(item,key) in InterfaceResponse">
                <form-fold :name="'response'" :depth="0" :properties="item.properties" :keyTo="key"
                           :item="item"></form-fold>
              </div>
              <!--</li>-->
            </ul>
          </div>
        </li>
        <li>
          <span>响应</span>
          <div class="ResponseCode">
            <span v-show="(typeof InterfaceResponse) != 'object'">{{InterfaceResponse}}</span>
            <ul v-show="(typeof InterfaceResponse) == 'object'">
              <li class="head"><span>状态码</span><span>说明</span><span>Schema</span></li>
              <li v-for="(item,index) in InterfaceResponseCode">
                <div>
                  <span>{{index}}</span>
                </div>
                <div>
                  <span>{{item.description ? item.description : "无"}}</span>
                </div>
                <div>
                  <a v-if="responseCodeSchema(item)" @click="responseCodePreToggle(index)" class="fontColor"
                     href="javascript:">{{responseCodePre && responseCodePre[index] && responseCodePre[index] == true ? '收缩' : '展开'}}</a>
                  <span v-if="responseCodeSchema(item)" v-show="!responseCodePre[index]">{{item.schema ?(item.schema.$ref ? responseObjectName : (item.schema.type && item.schema.type === "array" && item.schema.items) ? item.schema.items : "无") : "无"}}</span>
                  <span v-show="responseCodePre[index]"
                    :class="{format: responseCodeSchema(item)&&responseCodePre[index]}">{{item.schema ? (item.schema.$ref ? jsonObject : (item.schema.type && item.schema.type === "array" && item.schema.items) ? jsonObject : "无") : "无"}}
                </span>
                </div>
              </li>
            </ul>
          </div>
        </li>
      </ul>
    </div>
    <div v-show="switchA==1" class="debugging-content">
      <!-- 此处为接收 -->
      <submit-form v-on:deleteCopyChildForm="deleteChildForm" v-on:getCollection="getForm" :childForm.sync="childForm"
                   :bg="bg" v-on:shijian="fatherValue"
                   :parameterValue="parameterValue" :leftDropDownBoxContent="leftDropDownBoxContent"
                   v-if="swaggerCategory[countTo]&&swaggerCategory[countTo].pathInfo"
                   :swaggerCategory="swaggerCategory" :selected="selected" :count="count" :countTo="countTo"
                   :InterfaceRequest="InterfaceRequest">
      </submit-form>
      <div class="debugging-result" v-show="resultShow">
      <span style="cursor:pointer;" @click="debugging='content'"
            :class="[debugging=='content'?'active':'']">响应内容</span>
        <span style="cursor:pointer;" @click="debugging='cookies'"
              :class="[debugging=='cookies'?'active':'']">Cookies</span>
        <span style="cursor:pointer;" @click="debugging='header'"
              :class="[debugging=='header'?'active':'']">Header</span>
        <span style="cursor:pointer;" @click="debugging='curl'" :class="[debugging=='curl'?'active':'']">curl方式</span>
        <b>Time:<a href="javascript:"> {{requestTime}}  ms</a></b>
        <div class="result-content">
          <div class="content" v-show="debugging=='content'">
            <div v-if="isJsonObject">
              {
              <ul>
                <li v-for="(item,key) in jsonObjectTo">
                  <Json-View v-bind:obj="item" :keyTo="key" v-bind:indentation="indentation"></Json-View>
                </li>
              </ul>
              }
            </div>
            <li v-else>{{jsonObjectTo}}</li>
          </div>
          <div v-show="debugging=='cookies'">
            <span>暂无</span>
          </div>
          <div class="debugging-header" v-show="debugging=='header'">
            <ul style="border: 1px solid #ddd;">
              <li class="head"><span>请求头</span><span>value</span></li>
              <li><span>Date</span><span>{{debugResponse && debugResponse.headers  && debugResponse.headers['Date']}}</span></li>
              <li><span>transfer-encoding</span><span>{{debugResponse && debugResponse.headers  && debugResponse.headers['transfer-encoding']}}</span></li>
              <li><span>x-application-context</span><span>{{debugResponse && debugResponse.headers  && debugResponse.headers['x-application-context']}}</span></li>
              <li>
                <span>content-type</span><span>{{debugResponse && debugResponse.headers  && debugResponse.headers['content-type']}}</span>
              </li>
              <li><span>response-code</span><span>{{debugResponse && debugResponse.status}}</span></li>
            </ul>
          </div>
          <div class="debugging-curl" v-show="debugging=='curl'">
            <div>{{curlMode}}</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script type="text/ecmascript-6">
  import {mapState, mapMutations, mapActions} from 'vuex'
  import FormFold from './formFold.vue'
  import {deepCopy, basicTypeInit} from './../util/util'
  import SubmitForm from './submitForm.vue'
  import JsonView from './jsonView.vue'

  export default {
    name: "app",
    data() {
      return {
        isJsonObject: false,
        childForm: {},
        indentation: 1,
        jsonObject: "",
        jsonObjectTo: "",
        switchA: 0,
        resultShow: false,
        debugging: 'content',
        curlMode: "",
        linkageSection: "",
        parameterValue: {},
        responseCodePre: [],
        responseObjectName:""
      }
    },
    computed: {
      /**
       * @return {string}
       */
      ...mapState(['authorization']),
      InterfaceResponse: function () {/* 响应参数 */
        let resp = deepCopy(this.swaggerCategory[this.countTo] && this.swaggerCategory[this.countTo].pathInfo && this.swaggerCategory[this.countTo].pathInfo.responses);
        let respBasis = false;
        let respState;
        if (resp === undefined) {
          return '加载失败';
        }
        for (let key in resp) {
          if (parseInt(key) >= 200 && parseInt(key) <= 299) {
            respBasis = true;
            respState = key;
            break;
          }
        }
        if (respBasis) {
          let ok = resp[respState];
          if (ok.hasOwnProperty("schema")) {
            let schema = ok["schema"];
            let ref = (schema["type"] && schema["type"] === "array" && schema["items"]) ? schema["items"].$ref : schema["$ref"];
            let regex = new RegExp("#/definitions/(.*)$", "ig");
            if (regex.test(ref)) {
              let refType = RegExp.$1;
              let deftion = undefined;
              let definition = {};
              definition[refType] = this.formatRequest(ref);

              deftion = this.JSONinit(refType);
              this.jsonObject = deftion;
              return definition;
            } else {
              //未发现ref属性
              if (schema.hasOwnProperty("type")) {
                this.jsonObject = schema["type"];
                return schema["type"];
              }
              return "无";
            }
          } else {
            return "无";
          }

        } else {
          return "没有指定响应成功信息";
        }
      },
      InterfaceResponseCode: function () {/*  响应码 */
        let resp = deepCopy(this.swaggerCategory[this.countTo] && this.swaggerCategory[this.countTo].pathInfo && this.swaggerCategory[this.countTo].pathInfo.responses);
        let respBasis = false;
        let respState;
        if (resp === undefined) {
          return "加载失败";
        }
        for (let key in resp) {
          if (parseInt(key) >= 200 && parseInt(key) <= 299) {
            respBasis = true;
            respState = key;
            break;
          }
        }
        if (respBasis) {
          return resp;
        }
        return {};
      },
      InterfaceRequest: function () {/* 请求参数 */
        if (!this.swaggerCategory[this.countTo] && this.swaggerCategory[this.countTo].pathInfo && this.swaggerCategory[this.countTo].pathInfo.parameters) {
          return false;
        }
        /* 请求参数的遍历 */
        let result = {};
        let parameters = deepCopy(this.swaggerCategory[this.countTo].pathInfo.parameters);
        let definitions = deepCopy(this.leftDropDownBoxContent.definitions);
        if (parameters === undefined) {
          return result;
        }
        for (let i in parameters) {
          if ((parameters[i].schema && parameters[i].schema.$ref) || parameters[i].$ref) {
            result[i] = parameters[i];
            result[i]['properties'] = this.formatRequest((parameters[i].schema && parameters[i].schema.$ref) || parameters[i].$ref);
          } else {
            result[i] = parameters[i];
          }
        }
        let resultCopy = deepCopy(result);
        for (let key in resultCopy) {
          /* 如果该字段没有type属性且存在子字段，子字段内有类型type属性 */
          if ((!resultCopy[key].type && resultCopy[key].properties && resultCopy[key].properties.type === "object") || resultCopy[key].type === 'array' && resultCopy[key].properties) {
            /* 包含子字段 */
            if (resultCopy[key].type === 'array' && resultCopy[key].properties) {
              this.parameterValue[key] = [];
              this.parameterValue[key].push(this.iniObject(resultCopy[key].properties.properties))
            } else {
              this.parameterValue[key] = {};
              this.parameterValue[key] = this.iniObject(resultCopy[key].properties.properties);
            }
          } else {
            /* 不包含子字段 */
            this.parameterValue[key] = basicTypeInit(resultCopy[key].type);
          }
        }
        //提取数据传递给子数据
        this.childForm = [];
        for (let key in result) {
          let array = {};
          this.childForm[key] = {};
          array['name'] = result && result[key] && result[key]['name'];
          array['default'] = this.parameterValue[key];
          array['required'] = result && result[key] && result[key]['required'];
          this.childForm[key] = deepCopy(array);
        }
        return result;
      },
      debugResponse() {/* 从请求中获取到的响应参数 */
        return this.$store.state.debugRequest.debugResponse;
      },
      requestTime(){
        return this.$store.state.debugRequest.requestTime;
      },
      authorizeObj(){
        return this.$store.state.debugRequest.authorizeObj;
      },
      isExistSecurity(){
        let is = this.swaggerCategory && this.swaggerCategory[this.countTo] && this.swaggerCategory[this.countTo].pathInfo && this.swaggerCategory[this.countTo].pathInfo.security;
        return !!is;
      }
    },
    watch: {
      countTo: function () {
        this.switchA = 0;
        this.resultShow = false;
      }
    },
    methods: {
      ...mapActions(["carriedSend"]),
      ...mapMutations(["initialization","send"]),
      responseCodeSchema: function (item) {/* 响应码部分 数据是否存在Schema字段 */
        if(item.schema && item.schema.type && item.schema.type === 'array' && item.schema.items){
          return true;
        }
        if(item.schema && item.schema.$ref){
          this.responseObjectName=item.schema.$ref.match("#/definitions/(.*)")[1];
          return true;
        }
        return false;
      },
      responseCodePreToggle: function (index) {/* 响应码部分数据JSON格式化展开收缩切换 */
        if (this.responseCodePre && this.responseCodePre[index] && this.responseCodePre[index]) {
          this.$set(this.responseCodePre, index, !this.responseCodePre[index])
        } else {
          this.$set(this.responseCodePre, index, true)
        }
      },
      deleteChildForm: function (key) {
        this.$delete(this.childForm, key);
      },
      fatherValue: function (myValue) {
        this.$set(this.parameterValue, 0, myValue);
        this.resultShow = !this.resultShow;
        this.resultShow = !this.resultShow;
      },
      tickRequired: function (item, event) {
        for (let key in this.InterfaceRequest) {
          if (!this.InterfaceRequest[key].required) {
            return false;
          }
        }
        return true;
      },
      iniObject: function (properties) {/* 传入对象，对其进行类型初始化 */
        let obj = {};
        for (let key in properties) {
          if ((properties[key].type && properties[key].properties && properties[key].type === "object") || (properties[key].type === 'array' && properties[key].properties)) {
            /* 包含子字段 */
            if (properties[key].type === 'array' && properties[key].properties) {
              obj[key] = {};
              obj[key] = (Object.values(this.iniObject(properties[key].properties)))
            } else {
              obj[key] = this.iniObject(properties[key].properties)
            }
          } else {
            /* 不包含子字段 */
            obj[key] = basicTypeInit(properties[key].type)
          }
        }
        return obj;
      },
      formatterJson: function (text_value) {
        let res = "";
        for (let i = 0, j = 0, k = 0, ii, ele; i < text_value.length; i++) {//k:缩进，j:""个数
          ele = text_value.charAt(i);
          if (j % 2 === 0 && ele === "}") {
            k--;
            for (ii = 0; ii < k; ii++) ele = "    " + ele;
            ele = "\n" + ele;
          }
          else if (j % 2 === 0 && ele === "{") {
            ele += "\n";
            k++;
            //debugger;
            for (ii = 0; ii < k; ii++) ele += "    ";
          }
          else if (j % 2 === 0 && ele === ",") {
            ele += "\n";
            for (ii = 0; ii < k; ii++) ele += "    ";
          }
          else if (ele === "\"") j++;
          res += ele;
        }
        return res;
      },
      formatRequest: function (itemsRef) {/* 传入#/definitions/User，进行格式化 */
        let result = {};
        if (itemsRef === undefined || itemsRef === null || (typeof  itemsRef) !== "string") {
          return result;
        }
        let objName = itemsRef.match("#/definitions/(.*)") && itemsRef.match("#/definitions/(.*)")[1];
        let definitions = deepCopy(this.leftDropDownBoxContent.definitions);
        if (objName === null || objName === undefined || definitions === undefined) {
          return result;
        }
        for (let key in definitions) {
          if (key.toLowerCase() === objName.toLowerCase()) {
            result = deepCopy(definitions[key]);
            let properties = definitions[key].properties;
            if (properties === undefined || properties === null || (typeof properties) === "string") {
              return result;
            }
            for (let k in properties) {
              if ((properties[k].items && properties[k].items.$ref) || properties[k].$ref) {
                let Ref = (properties[k].items && properties[k].items.$ref) ? (properties[k].items && properties[k].items.$ref) : properties[k].$ref;
                if (properties[k].type === 'array') {
                  result.properties[k].properties = [];
                  let adds = this.formatRequest(Ref);
                  adds.name === undefined || adds.name === null ? "" : adds['name'] = Ref.match("#/definitions/(.*)")[1].toLowerCase();
                  result.properties[k].properties.push(adds);
                  continue;
                }
                //  result.properties[k].properties={};
                // result.properties[k].properties[Ref.match("#/definitions/(.*)")[1].toLowerCase()]=this.formatRequest(Ref);
                result.properties[k] = this.formatRequest(Ref);
              } else {
                result.properties[k] = properties[k];
              }
            }
          }
        }
        return result;
      },
      titleCase5: function (str) {
        return str.toLowerCase().replace(/( |^)[a-z]/g, (L) => L.toUpperCase());
      },
      JSONinit: function (refType) {
        let _this = this;
        let definitionsArray = deepCopy(_this.leftDropDownBoxContent && _this.leftDropDownBoxContent.definitions);
        let deftion = undefined;
        if (definitionsArray === undefined) {
          return deftion;
        }
        for (let i in definitionsArray) {
          if (i === refType) {
            deftion = definitionsArray[i].properties;
            break
          }
        }
        if (deftion === null || deftion === undefined) {
          let obj = new Object();
          obj[refType] = deftion;
          return obj;
        }
        for (let key in deftion) {
          if (deftion[key].$ref && deftion[key].type === "array") {
            deftion[key] = {};
            continue;
          }
          if (deftion[key].$ref) {
            let schema = deftion[key];
            let ref = (schema["type"] && schema["type"] === "array" && schema["items"]) ? schema["items"].$ref : schema["$ref"];
            let regex = new RegExp("#/definitions/(.*)$", "ig");
            if ((typeof ref === "string") && regex.test(ref)) {
              let a = ref.match("#/definitions/(.*)");
              let refType2 = (ref.match("#/definitions/(.*)") === null ? "" : ref.match("#/definitions/(.*)")[1]);
              deftion[key] = this.JSONinit(refType2);
              continue;
            }
          }
          if (deftion[key].type === "array" && deftion[key].items) {
            let schema = deftion[key];
            let ref = (schema["type"] && schema["type"] === "array" && schema["items"]) ? schema["items"].$ref : schema["$ref"];
            let regex = new RegExp("#/definitions/(.*)$", "ig");
            if ((typeof ref === "string") && regex.test(ref)) {
              let a = ref.match("#/definitions/(.*)");
              let refType2 = (ref.match("#/definitions/(.*)") === null ? "" : ref.match("#/definitions/(.*)")[1]);
              deftion[key] = [];
              deftion[key].push(this.JSONinit(refType2));
              continue;
            }
          }
          if (deftion[key].type === "array") {
            deftion[key] = [];
            continue;
          }
          if (deftion[key].type === "boolean") {
            deftion[key] = true;
            continue;
          }
          if (deftion[key].type === "integer") {
            deftion[key] = 0;
            continue;
          }
          if (deftion[key].type === "string") {
            deftion[key] = "";
            continue;
          }
        }
        return deftion;
      },
      getForm: function (data,param) {/* param为假设存在文件上传时所传输的文件对象 */
        let _this = this;
        let result = [];
        for (let key in data) {
          if (data[key].required) {
            let obj = [];
            obj.push(data[key].name);
            obj.push(data[key].default);

            obj.push(_this.swaggerCategory[_this.countTo].pathInfo.parameters[key]);
            result.push(obj);
          }
        }
        _this.stitchUrl(result,param);/* param为假设存在文件上传时所传输的文件对象 */
      },
      stitchUrl: function (result,param) {
        let _this = this;
        let url = (_this.swaggerCategory && _this.swaggerCategory[_this.countTo] && _this.swaggerCategory[_this.countTo].pathName) ? _this.swaggerCategory[_this.countTo].pathName : '',
          params = {},
          headerParams = {'Content-Type':'application/json;charset=UTF-8'},
          reqdata = "",
          bodyparams = "";
//
        if (typeof (_this.leftDropDownBoxContent.basePath) !== "undefined" && _this.leftDropDownBoxContent.basePath !== "") {
          if (_this.leftDropDownBoxContent.basePath !== "/") {
            url = _this.leftDropDownBoxContent.basePath + url;
          }
        }
        let isQuery = false;
        for (let i = 0, n = result.length; i < n; i++) {
          if (result[i][2]["in"] === "path") {
            url = url.replace("{" + result[i][0] + "}", result[i][1]);
          } else if (result[i][2]["in"] === "query") {
            url += ((isQuery ? '&' : isQuery = '?') + result[i][0] + '=' + result[i][1]);
          } else {
            if (result[i][2]["in"] === "body") {
              bodyparams += JSON.stringify(result[i][1]);
            } else {
              if (result[i][2]["in"] === "header") {
                headerParams[result[i][0]] = result[i][1];
              } else {
                result[i][1] ? params[result[i][0]] = result[i][1] : '';
              }
            }
          }
        }
        for (let j = 0, k = result.length; j < k; j++) {
          if (result && result[j] && result[j][2] && result[j][2]["in"] && result[j][2]["in"] === "body") {
            reqdata = bodyparams;
            break;
          } else {
            reqdata = params;
          }
        }
        let jsonReqdata = reqdata;
        /* 判断调试请求中是否有Security字段 */
        if (this.isExistSecurity) {/* headerParams */
          for (let key in this.authorizeObj) {
            headerParams[key] = this.authorizeObj[key];
          }
        }
        /*  判断是否为文件类型上传 */
        for(let key in reqdata){
          if(param!==undefined&&reqdata[key]&&reqdata[key]['in']&&reqdata[key]['in']==='formData'&&reqdata[key]['type']&&reqdata[key]['type']==='file'){
            headerParams['Content']='multipart/form-data; charset=utf-8';
            reqdata=param;
          }
        }
        let urlAssembly = _this.leftDropDownBoxContent.host.indexOf("http")===0?(_this.leftDropDownBoxContent.host + url):("http://" + _this.leftDropDownBoxContent.host + url);
        _this.$store.dispatch('carriedSend', {
          url: urlAssembly,
          headerParams: headerParams,
          type: _this.swaggerCategory[this.countTo].name,
          data: reqdata
        }).then(function () {
          _this.StitchingCurl(headerParams, jsonReqdata)
        })

      },
      StitchingCurl: function (headerParams, reqdata) {
        console.log("Curl处理")
        let _this = this;
        let headerss = "";
        let contentUrl = `'${_this.debugResponse && _this.debugResponse.config && _this.debugResponse.config.url}'`;
        let curlAccept = ` --header   'Accept: ${ _this.debugResponse.headers && _this.debugResponse.headers['content-type']}'`;
        for (let key in headerParams) {
          headerss += `${key}: ${headerParams[key]}`;
        }
        /*  生成curl命令组成部分 */
        /* 头部数据 */
        headerss !== "" ? headerss = ` --header '${ headerss}' ` : "";
        let contentType = ` --header  'Content-Type:   ${ _this.debugResponse.headers && _this.debugResponse.headers['content-type']}' `
        if (_this.swaggerCategory[this.countTo].name.toLowerCase() === 'get') {
          let curlTable = `curl -X ${_this.swaggerCategory[this.countTo].name.toUpperCase()}  --header 'Accept:  ${ _this.debugResponse&&_this.debugResponse.headers&&_this.debugResponse.headers['content-type'] }' ${headerss} ${contentUrl}`;
          _this.curlMode = curlTable;
        } else {
          /* d data 非头部附带数据,只用于非get类型请求 */
          let curlData = ` -d  '${reqdata ? this.formatterJson(reqdata) : ""}' `;
          let curlTable = `curl -X  ${_this.swaggerCategory[this.countTo].name.toUpperCase()} ${contentType}   ${curlAccept}   ${headerss}  ${reqdata === '{}' ? "" : curlData}   ${contentUrl}`;
          _this.curlMode = curlTable;
        }
        /* 响应内容JSON序列化 */
        try {
          let obj = (typeof this.debugResponse.data === 'object' ? this.debugResponse.data : JSON.parse(this.debugResponse.data));
          if (typeof obj === 'object' && obj) {
            this.isJsonObject = true;
            this.jsonObjectTo = obj;
          } else {
            this.isJsonObject = false;
            this.jsonObjectTo = String(this.debugResponse.data);
          }
        } catch (e) {
          this.isJsonObject = false;
          this.jsonObjectTo = String(this.debugResponse.data);
        }
        this.resultShow = true;
        /* 显示结果 */
      },
      failureJump:function () {/* 请求失败时跳转至登录路由 */
        this.$router.push('swagger-login.html');
        console.log("请进行身份验证后使用！")
      }
    },
    props: ['swaggerCategory', 'selected', 'count', 'countTo', 'bg', 'leftDropDownBoxContent'],
    components: {FormFold, SubmitForm, JsonView},
    created(){
      let methods=this.failureJump;
      this.initialization(methods);
    }
  }
</script>
<style>


  /* 响应参数说明部分 */
  .ResponseParameter .head {
    font-size: 16px;
    font-weight: 700;
    background-color: #F8F8F8;
  }

  .ResponseParameter > ul {
    overflow: hidden;
    border: 1px solid #ddd;
    border-bottom: 0;
  }

  .ResponseParameter > ul li {
    overflow: hidden;
    border-bottom: 1px solid #ddd;
  }

  .ResponseParameter > ul li:last-child {
    border-bottom: 0;
  }

  .ResponseParameter > ul li > span {
    width: 30%;
    float: left;
    padding: 8px 4px;
    border-right: 1px solid #ddd;
  }

  .ResponseParameter .head span {
    text-align: center;
  }

  .ResponseParameter > ul li > span:last-child {
    border-right: 0;
    width: 35%;
  }

  /*  响应状态码部分 */
  .ResponseCode li.head {
    font-size: 16px;
    font-weight: 700;
    background-color: #F8F8F8;
  }

  .ResponseCode > ul {
    overflow: hidden;
    border: 1px solid #ddd;
  }

  .ResponseCode > ul li {
    overflow: hidden;
    border-bottom: 1px solid #ddd;
    position: relative;
  }

  .ResponseCode > ul li:last-child {
    border-bottom: 0;
  }

  .ResponseCode > ul li > div,
  .ResponseCode li.head span {
    width: 50%;
    float: left;
    position: relative;
    padding-bottom: 8px;
    box-sizing: border-box;
  }

  .ResponseCode li.head span {
    padding: 8px 4px;
    border-right: 1px solid #ddd;
  }

  .ResponseCode > ul li > div:first-child,
  .ResponseCode li.head span:first-child {
    width: 15%;
  }

  .ResponseCode > ul li > div:last-child,
  .ResponseCode li.head span:last-child {
    border-right: 0;
    width: 35%;
  }

  .ResponseCode > ul li > div:last-child a {
    text-decoration: none;
    font-size: 12px;
    position: absolute;
    right: 0;
    top: 5px;
  }

  .ResponseCode > ul li > div span {
    padding-bottom: 1000px;
    margin-bottom: -1000px;
    display: block;
    border-right: 1px solid #ddd;
    padding-top: 8px;
    padding-left: 4px;
    padding-right: 4px;
  }

  .ResponseCode > ul li div span.format {
    white-space: pre-wrap;
  }

  .ResponseCode > ul li > div:last-child span {
    border-right: 0;
  }

  /* 调试结果区域样式 */
  .debugging-result {
    text-align: left;
    font-size: 0;
  }

  .debugging-result > span {
    font-size: 14px;
    display: inline-block;
    padding: 10px 15px;
    border: 1px solid #EBEBEB;
    position: relative;
    border-right: 0;
    bottom: -1px;
  }

  .debugging-result > b {
    font-size: 12px;
    float: right;
    color: #858585;
    margin-top: 12px;
  }

  .debugging-result > b a {
    text-decoration: none;
    color: #2E8BF0;
  }

  .debugging-result > span:last-of-type {
    border-right: 1px solid #EBEBEB;
  }

  .debugging-result > span.active {
    box-shadow: 0 3px #89BF05 inset;
    color: #89BF05;
    border-bottom: 1px solid transparent;
    border-bottom: 1px solid #FFFFFF;
  }

  .debugging-curl > div {
    color: #393939;
    font-size: 13px;
    white-space: pre;
    overflow-x: auto;
    padding: 15px;
  }

  .result-content {
    border-top: 1px solid #EBEBEB;
    padding-top: 15px;
  }

  .result-content > div {
    border: 1px solid #ddd;
    min-height: 210px;
    font-size: 16px;
    text-align: left;
    padding: 40px 15px 15px;
  }

  .debugging-header > ul {
    border: 1px solid #ddd;
  }

  .debugging-header li {
    border-bottom: 1px solid #ddd;
  }

  .debugging-header li > span {
    padding: 10px 15px 10px 5px;
    display: inline-block;
  }

  .debugging-header li > span:first-child {
    font-weight: 700;
    width: 38%;
    border-right: 1px solid #ddd;
  }

  .debugging-header li > span:last-child {
    width: 45%;
  }

  .debugging-header .head {
    font-weight: 700;
  }

  /* 请求参数表格 */
  .table-tr {
    border-bottom: 1px solid #ddd;
    overflow: hidden;
    word-wrap: break-word;
  }

  .table-head {
    font-size: 16px;
    font-weight: 700;
    background-color: #F8F8F8;
  }

  .table-head .table-td {
    padding: 8px 4px;
  }

  .table-td {
    border-right: 1px solid #ddd;
    width: 15%;
    float: left;
    padding: 8px 4px;
    text-align: left;
  }

  .table-td:last-child {
    border-right: 0;
  }

  /* 接口详细信息列表 */
  .swagger-content, .debugging-content {
    border-top: 1px solid #dbdbdb;
    padding: 20px 10px;
  }

  .content-list {
    text-align: left;
    margin: 0;
    padding: 15px;
    border: 1px solid #DDDDDD;
  }

  .content-list > li {
    list-style: none;
    text-align: left;
    border-bottom: 1px solid #DDDDDD;
    overflow: hidden;
  }

  .content-list > li > span {
    display: inline-block;
    width: 108px;
    padding: 10px 8px 8px;
  }

  .content-list > li > div {
    margin-left: 125px;
    padding: 10px;
  }

  .content-list > li > div > span {
    display: inline-block;
  }

  .content-list > li > div .request-table {
    padding: 8px;
  }

  .content-list > li > div .request-table > ul {
    border: 1px solid #ddd;
    border-bottom: 0;
  }

  .content-list > li > span:nth-child(1) {
    font-weight: 700;
    border-right: 1px solid #ddd;
    margin-bottom: -1000px;
    padding-bottom: 1000px;
    min-height: 26px;
    vertical-align: top;
    float: left;
  }

  /*  接口详细内容 */
  .swagger-main {
    box-shadow: 1px 1px 5px #f3f4ef;
    border: 1px solid #F3F4EF;
    margin-left: 43%;
    margin-right: 15px;
    transition: all 0.2s;
  }

  .switch {
    margin-top: 15px;
    text-align: left;
    padding-left: 20px;
    font-size: 0;
  }

  .switch span {
    font-size: 14px;
    display: inline-block;
    padding: 10px 15px;
    border: 1px solid #EBEBEB;
    position: relative;
    top: 1px;
  }

  .switch span:nth-child(1) {
    position: relative;
    right: -1px;
  }

  .switch span.active {
    box-shadow: 0 3px #89BF05 inset;
    color: #89BF05;
    border-bottom: 1px solid #FFFFFF;
  }

  /* 响应式 */
  @media screen and (min-width: 1600px) {
    .swagger-main {
      margin-left: 36%;
    }
  }

</style>
