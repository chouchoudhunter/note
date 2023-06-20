### 发起多个并发请求:

```dart
response = await Future.wait([dio.post("/info"), dio.get("/token")]);
```
https://github.com/JunAILiang/flutter_dio_util/blob/master/lib/dio_util/dio_util.dart
https://www.liujunmin.com/flutter/dio_encapsulation.html
前言
本文会手把手教你该怎么去封装一个类库，平时在我们的工作中都是拿着别人的造好的轮子在使用，这篇文章将带你怎么去自己造轮子，以后再碰到别的类库需要对其进行封装的时候提供一个的思路和方法。

为什么需要封装Dio？
在前面的文章中，我们对Dio的基本使用、请求库对比、源码分析，我们知道Dio 的使用非常的简单，那为什么还需要进行封装呢？有两点如下：

1、代码迁移
当组件库方法发生重要改变需要迁移的时候如果有多处地方用到，那么需要对使用到的每个文件都进行修改，非常的繁琐而且很容易出问题。

2、请求库切换
当不需要Dio 库的时候，我们可以随时方便切换到别的网络请求库，当然Dio 目前内置支持使用第三方库的适配器。

3、统一配置
因为一个应用程序基本都是统一的配置方式，所以我们可以针对拦截器 、转换器 、 缓存 、统一处理错误 、代理配置、证书校验 等多个配置进行统一管理。

使用单例模式进行Dio封装
为什么使用单例模式？
因为我们的应用程序在每个页面中都会用到网络请求，那么如果我们每次请求的时候都去实例化一个Dio，无非是增加了系统不必要的开销，而使用单例模式对象一旦创建每次访问都是同一个对象，不需要再次实例化该类的对象。

创建单例类
这是通过静态变量的私有构造器来创建的单例模式

class DioUtil {

  factory DioUtil() => _getInstance();
  static DioUtil get instance => _getInstance();
  static DioUtil _instance;

  DioUtil._init() {
    // 初始化
  }
  static DioUtil _getInstance() {
    if (_instance == null) {
      _instance = DioUtil._init();
    }
    return _instance;
  }
}
对Dio请求进行初始化
我们对 超时时间 、响应时间 、BaseUrl 进行统一设置

/// 连接超时时间
static const int CONNECT_TIMEOUT = 60*1000;
/// 响应超时时间
static const int RECEIVE_TIMEOUT = 60*1000;

/// 声明Dio变量
Dio _dio;

DioUtil._init() {
  if (_dio == null) {
    /// 初始化基本选项
    BaseOptions options = BaseOptions(
      baseUrl: "http://localhost:8080",
      connectTimeout: CONNECT_TIMEOUT,
      receiveTimeout: RECEIVE_TIMEOUT
    );

    /// 初始化dio
    _dio = Dio(options);
  }
}
对Restful APi风格进行统一封装
因为不管是get()还是post()请求，Dio 内部最终都会调用request 方法，只是传入的method 不一样，所以我们这里定义一个枚举类型在一个方法中进行处理

enum DioMethod {
  get,
  post,
  put,
  delete,
  patch,
  head,
}

/// 请求类
Future<T> request<T>(String path, {
  DioMethod method = DioMethod.get,
  Map<String, dynamic> params,
  data,
  CancelToken cancelToken,
  Options options,
  ProgressCallback onSendProgress,
  ProgressCallback onReceiveProgress,
}) async {
  const _methodValues = {
    DioMethod.get: 'get',
    DioMethod.post: 'post',
    DioMethod.put: 'put',
    DioMethod.delete: 'delete',
    DioMethod.patch: 'patch',
    DioMethod.head: 'head'
  };


  options ??= Options(method: _methodValues[method]);
  try {
    Response response;
    response = await _dio.request(path,
                                  data: data,
                                  queryParameters: params,
                                  cancelToken: cancelToken,
                                  options: options,
                                  onSendProgress: onSendProgress,
                                  onReceiveProgress: onReceiveProgress
                                 );
    return response.data;
  } on DioError catch (e) {
    throw e;
  }
}
拦截器
介绍
我们已经把Restful API 风格简化成了一个方法，通过DioMethod 来标明不同的请求方式。在我们平时开发的过程中，需要在请求前、响应前、错误时对某一些接口做特殊的处理，那我们就需要用到拦截器。Dio 为我们提供了自定义拦截器功能，很容易轻松的实现对请求、响应、错误时进行拦截

错误统一处理
我们发现虽然Dio框架已经封装了一个DioError类库，但如果需要对返回的错误进行统一弹窗处理或者路由跳转等就只能自定义了

请求前统一处理
在我们发送请求的时候会碰到几种情况，比如需要对非open开头的接口自动加上一些特定的参数，获取需要在请求头增加统一的token

响应前统一处理
在我们请求接口前可以对响应数据进行一些基础的处理，比如对响应的结果进行自定义封装，还可以针对单独的url 做特殊处理等。

自定义拦截器实现
import 'package:dio/dio.dart';
import 'package:flutter_dio/dio_util/dio_response.dart';

class DioInterceptors extends Interceptor {
  @override
  void onRequest(RequestOptions options, RequestInterceptorHandler handler) {

    // 对非open的接口的请求参数全部增加userId
    if (!options.path.contains("open")) {
      options.queryParameters["userId"] = "xxx";
    }

    // 头部添加token
    options.headers["token"] = "xxx";

    // 更多业务需求

    handler.next(options);

    // super.onRequest(options, handler);
  }

  @override
  void onResponse(Response response, ResponseInterceptorHandler handler) {

    // 请求成功是对数据做基本处理
    if (response.statusCode == 200) {
      response.data = DioResponse(code: 0, message: "请求成功啦", data: response);
    } else {
      response.data = DioResponse(code: 1, message: "请求失败啦", data: response);
    }

    // 对某些单独的url返回数据做特殊处理
    if (response.requestOptions.baseUrl.contains("???????")) {
      //....
    }

    // 根据公司的业务需求进行定制化处理

    // 重点
    handler.next(response);
  }

  @override
  void onError(DioError err, ErrorInterceptorHandler handler) {
    switch(err.type) {
        // 连接服务器超时
      case DioErrorType.connectTimeout:
        {
          // 根据自己的业务需求来设定该如何操作,可以是弹出框提示/或者做一些路由跳转处理
        }
        break;
        // 响应超时
      case DioErrorType.receiveTimeout:
        {
          // 根据自己的业务需求来设定该如何操作,可以是弹出框提示/或者做一些路由跳转处理
        }
        break;
        // 发送超时
      case DioErrorType.sendTimeout:
        {
          // 根据自己的业务需求来设定该如何操作,可以是弹出框提示/或者做一些路由跳转处理
        }
        break;
        // 请求取消
      case DioErrorType.cancel:
        {
          // 根据自己的业务需求来设定该如何操作,可以是弹出框提示/或者做一些路由跳转处理
        }
        break;
        // 404/503错误
      case DioErrorType.response:
        {
          // 根据自己的业务需求来设定该如何操作,可以是弹出框提示/或者做一些路由跳转处理
        }
        break;
        // other 其他错误类型
      case DioErrorType.other:
        {

        }
        break;

    }
    super.onError(err, handler);
  }
}


class DioResponse<T> {

  /// 消息(例如成功消息文字/错误消息文字)
  final String message;
  /// 自定义code(可根据内部定义方式)
  final int code;
  /// 接口返回的数据
  final T data;
  /// 需要添加更多
  /// .........

  DioResponse({
    this.message,
    this.data,
    this.code,
  });

  @override
  String toString() {
    StringBuffer sb = StringBuffer('{');
    sb.write("\"message\":\"$message\"");
    sb.write(",\"errorMsg\":\"$code\"");
    sb.write(",\"data\":\"$data\"");
    sb.write('}');
    return sb.toString();
  }
}

class DioResponseCode {
  /// 成功
  static const int SUCCESS = 0;
  /// 错误
  static const int ERROR = 1;
  /// 更多
}
转换器
介绍
转换器Transformer 用于对请求数据和响应数据进行编解码处理。Dio实现了一个默认转换器DefaultTransformer作为默认的 Transformer. 如果想对请求/响应数据进行自定义编解码处理，可以提供自定义转换器

为什么需要转换器？
我们看了转换器的介绍，发现和拦截器的功能差不多，那为什么还要存在转换器，有两点：

和拦截器解耦
不修改原始请求数据
执行流程：请求拦截器 » 请求转换器 » 发起请求 » 响应转换器 » 响应拦截器 » 最终结果。

请求转换器
只会被用于 ‘PUT’、 ‘POST’、 ‘PATCH’方法，因为只有这些方法才可以携带请求体(request body)

响应转换器
会被用于所有请求方法的返回数据。

自定义转换器实现
import 'dart:async';
import 'package:dio/dio.dart';

class DioTransformer extends DefaultTransformer {
  @override
  Future<String> transformRequest(RequestOptions options) async {
    // 如果请求的数据接口是List<String>那我们直接抛出异常
    if (options.data is List<String>) {
      throw DioError(
        error: "你不能直接发送List数据到服务器",
        requestOptions: options,
      );
    } else {
      return super.transformRequest(options);
    }
  }

  @override
  Future transformResponse(RequestOptions options, ResponseBody response) async {
    // 例如我们响应选项里面没有自定义某些头部数据,那我们就可以自行添加
    options.extra['myHeader'] = 'abc';
    return super.transformResponse(options, response);
  }
}
刷新Token
在开发过程中，客户端和服务器打交道的时候，往往会用一个token来做校验，因为每个公司处理刷新token的逻辑都不一样，我这里举一个简单的例子

我们需要给所有的请求头中添加一个refreshToken，如果refreshToken不存在，我们先去请求refreshToken，获取到refreshToken后，再发起后续请求。 由于请求refreshToken的过程是异步的，我们需要在请求过程中锁定后续请求（因为它们需要refreshToken), 直到refreshToken请求成功后，再解锁

import 'package:dio/dio.dart';
import 'package:flutter_dio/dio_util/dio_util.dart';

class DioTokenInterceptors extends Interceptor {
  @override
  void onRequest(RequestOptions options, RequestInterceptorHandler handler) {
    if (options.headers['refreshToken'] == null) {
      DioUtil.instance.dio.lock();
      Dio _tokenDio = Dio();
      _tokenDio..get("http://localhost:8080/getRefreshToken").then((d) {
        options.headers['refreshToken'] = d.data['data']['token'];
        handler.next(options);
      }).catchError((error, stackTrace) {
        handler.reject(error, true);
      }) .whenComplete(() {
        DioUtil.instance.dio.unlock();
      }); // unlock the dio
    } else {
      options.headers['refreshToken'] = options.headers['refreshToken'];
      handler.next(options);
    }
  }

  @override
  void onResponse(Response response, ResponseInterceptorHandler handler) async {

    // 响应前需要做刷新token的操作

    super.onResponse(response, handler);
  }

  @override
  void onError(DioError err, ErrorInterceptorHandler handler) {
    super.onError(err, handler);
  }
}
取消请求
为什么我们需要有取消请求的功能，如果当我们的页面在发送请求时，用户主动退出当前界面或者app应用程序退出的时候数据还没有响应，那我们就需要取消该网络请求，防止不必要的错误。

/// 取消请求token
CancelToken _cancelToken = CancelToken();

/// 取消网络请求
void cancelRequests({CancelToken token}) {
  token ?? _cancelToken?.cancel("cancelled");
}
cookie管理
cookie介绍
由服务器生成的一小段文本信息，发送给浏览器，浏览器把 cookie 以kv形式保存到本地某个目录下的文本文件内，下一次请求同一网站时会把该 cookie 发送给服务器。

原理
客户端发送一个请求(http请求+用户认证信息)到服务器
认证成功，服务器发送一个HttpResponse响应到客户端，其中包含Set-Cookie的头部
客户端提取并保存 cookie 于内存或磁盘
再次请求时，HttpRequest请求中会包含一个已认证的 Cookie 的头部
服务器解析cookie，获取 cookie 中客户端的相关信息
服务器返回响应数据
使用
cookie 的使用需要用到两个第三方组件 dio_cookie_manager 和 cookie_jar

cookie_jar：Dart 中 http 请求的 cookie 管理器，通过它您可以轻松处理复杂的 cookie 策略和持久化 cookie
dio_cookie_manager： CookieManager 拦截器可以帮助我们自动管理请求/响应 cookie。 CookieManager 依赖于 cookieJar 包
导入文件
dio_cookie_manager: ^2.0.0
cookie_jar: ^3.0.1
/// cookie
CookieJar cookieJar = CookieJar();

/// 添加cookie管理器
_dio.interceptors.add(CookieManager(cookieJar));

List<Cookie> cookies = [
  Cookie("xxx", xxx),
  // ....
];

//Save cookies            
DioUtil.instance.cookieJar.saveFromResponse(Uri.parse(BaseUrl.url), cookies);

//Get cookies   
List<Cookie> cookies = DioUtil.instance.cookieJar.loadForRequest(Uri.parse(BaseUrl.url));
网络接口缓存
为什么使用缓存?
因为在我们平时的开发过程中，会碰到一种情况，在进行网络请求时，我们希望能正常访问到上次的数据，对于用户的体验比较好，而不是展示一个空白的页面，该缓存主要是 《Flutter实战》网络接口缓存 提供参考。

使用shared_preferences持久化
我们在程序退出后内存缓存将会消失，所以我们用shared_preferences 进行磁盘缓存数据。

import 'dart:collection';
import 'package:dio/dio.dart';
import 'package:flutter_dio/dio_util/dio_util.dart';

class CacheObject {
  CacheObject(this.response)
    : timeStamp = DateTime.now().millisecondsSinceEpoch;
  Response response;
  int timeStamp;

  @override
  bool operator ==(other) {
    return response.hashCode == other.hashCode;
  }

  @override
  int get hashCode => response.realUri.hashCode;
}

class DioCacheInterceptors extends Interceptor {
  // 为确保迭代器顺序和对象插入时间一致顺序一致，我们使用LinkedHashMap
  var cache = LinkedHashMap<String, CacheObject>();

  @override
  void onRequest(RequestOptions options, RequestInterceptorHandler handler) {
    if (!DioUtil.CACHE_ENABLE) return super.onRequest(options, handler);
    // 通过refresh字段来判断是否刷新缓存
    bool refresh = options.extra["refresh"] == true;
    if (refresh) {
      // 删除本地缓存
      delete(options.uri.toString());
    }
    // 只有get请求才开启缓存
    if (options.extra["noCache"] != true &&
        options.method.toLowerCase() == 'get') {
      String key = options.extra["cacheKey"] ?? options.uri.toString();
      var ob = cache[key];
      if (ob != null) {
        //若缓存未过期，则返回缓存内容
        if ((DateTime.now().millisecondsSinceEpoch - ob.timeStamp) / 1000 <
            DioUtil.MAX_CACHE_AGE) {
          return handler.resolve(cache[key].response);
        } else {
          //若已过期则删除缓存，继续向服务器请求
          cache.remove(key);
        }
      }
    }
    super.onRequest(options, handler);
  }

  @override
  void onResponse(Response response, ResponseInterceptorHandler handler) {
    // 把响应的数据保存到缓存
    if (DioUtil.CACHE_ENABLE) {
      _saveCache(response);
    }

    super.onResponse(response, handler);
  }

  @override
  void onError(DioError err, ErrorInterceptorHandler handler) {
    // TODO: implement onError
    super.onError(err, handler);
  }


  _saveCache(Response object) {
    RequestOptions options = object.requestOptions;
    if (options.extra["noCache"] != true &&
        options.method.toLowerCase() == "get") {
      // 如果缓存数量超过最大数量限制，则先移除最早的一条记录
      if (cache.length == DioUtil.MAX_CACHE_COUNT) {
        cache.remove(cache[cache.keys.first]);
      }
      String key = options.extra["cacheKey"] ?? options.uri.toString();
      cache[key] = CacheObject(object);
    }
  }

  void delete(String key) {
    cache.remove(key);
  }
}
代理配置
在我们用flutter进行抓包的时候需要配置Dio代理。由DefaultHttpClientAdapter 提供了一个onHttpClientCreate 回调来设置底层 HttpClient的代理。

/// 设置Http代理(设置即开启)
void setProxy({
  String proxyAddress,
  bool enable = false
}) {
  if (enable) {
    (_dio.httpClientAdapter as DefaultHttpClientAdapter).onHttpClientCreate =
      (HttpClient client) {
      client.findProxy = (uri) {
        return proxyAddress;
      };
      client.badCertificateCallback =
        (X509Certificate cert, String host, int port) => true;
    };
  }
}
证书校验
用于验证正在访问的网站是否真实。提供安全性，因为证书和域名绑定，并且由根证书机构签名确认。

/// 设置https证书校验
void setHttpsCertificateVerification({
  String pem,
  bool enable = false
}) {
  if (enable) {
    (_dio.httpClientAdapter as DefaultHttpClientAdapter).onHttpClientCreate  = (client) {
      client.badCertificateCallback=(X509Certificate cert, String host, int port){
        if(cert.pem==pem){ // 验证证书
          return true;
        }
        return false;
      };
    };
  }
}

统一日志打印
日志打印主要是帮助我们开发时进行辅助排错

/// 开启日志打印
void openLog() {
	_dio.interceptors.add(LogInterceptor(responseBody: true));
}

DioUtil().openLog();