.. monapi documentation master file, created by
   sphinx-quickstart on Fri Feb 28 00:01:52 2014.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

.. role:: centerline
    :class: centerline

.. role:: red-bold
    :class: red-bold

=======================================
KINX : ixcloud - REST API documentation
=======================================

Introduction
============

REST API 개발

* Resource 기반 URL
* API 에러는 HTTP status code 사용
* HTTPS 사용
* :centerline:`HTTP Basic Auth & auth token 인증`

  * :red-bold:`현재 ixcloud-kvol API는 사내 전용으로 인증을 하지 않음`
  

* 요청 및 응답은 json만 지원


API Design 보안 관련 고려사항
=============================

* XSS

  * HTML template을 전혀 사용하지 않는 백엔드라, 기본적으로 문제 안됨.
  * HTML Escape는 flask Jinja2에서 알아서 처리
  * attribute의 경우, 따옴표로 escape 처리

    * **"{{ abc }}"**

* CSRF

  * 계정 인증에 쿠키를 사용하지 않음.
  * expire 시간이 있는 token을 basic auth로 사용하여 위험 제거
    (사용자의 요구로 현재 24시간으로 설정되 있음. 문제 발생 소지 있음)

* JSON Security

  * CSRF를 이용한 javascript code로 json 데이터를 덤프할 수 있는 문제

  * javascript을 경우, 데이터 최상위에 **{...}** 가 있으면 에러라는 사실을 이용,
    모든 json 응답은 list라 하여도 list로 바로 시작하지 않고,
    **{ 'a': [... ] }** 형태로 한단계 감싸서 위협 제거

  * 기본적으로 token으로 인증하고, 쿠키를 사용하지 않아 CSRF가 문제가 안되기 때문에 괜찮으나,
    이러한 코딩 습관을 가지는 것이 중요


Contents
========

.. toctree::
   :maxdepth: 2

   token
   kvol/kvol

Authentication
==============
* 인증후 계정을 이용 `HTTP Basic Auth 
  <http://jmnote.com/wiki/HTTP_%EA%B8%B0%EB%B3%B8_%EC%9D%B8%EC%A6%9D>`_ [1]_ 로
  auth token을 발급 받음

  * :http:get:`/api/token`

    * Authorization: Basic [ **[API계정]:[API비번]** 문자열을 *base64* encoding한 literal ] ::

        즉, 이런 형태임
        Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==
      

* 발급 받은 token으로 HTTP basic auth로 각 리소스에 접근

  * Authorization Header의 *user* 는 반드시 **API계정**, *password* 에 발급받은 *token*

    * Authorization: Basic [ **[API계정]:[token]** 문자열을 *base64* encoding한 literal ] ::

        즉, 이런 형태임
        Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==




Error 요약
==========
아래의 몇가지 HTTP status code를 사용. API 추가에 따라 추가될 수 있음

* 200 OK: API 성공 (GET, PUT, PATCH 등 일반적인 성공)
* 201 Created: 새 리소스 생성 성공 (POST 및 PUT시)
* 204 No Content: 리소스 삭제 성공 (DELETE시)
* 400 Bad Request: 요청 파라미터 오류
* 401 Unauthorized: Token이 expire되거나, 올바르지 않음
* 404 Not Found: 해당 리소스가 없음
* 405 Method Not Allowed: 해당 http method를 지원하지 않음 (get만 되고, post나 put 불가 등)
* 409 Conflict: 동일 리소스가 이미 있음 (post시 이미 존재하여 추가 불가시)
* 500 Internal Server Error: API 내부 Service 오류
* 520 Unknown Error: 알수 없음

json의 message 부분에 에러 스트링을 포함({ "message": "No Data" })

method별 가능 response code::

   * GET                                            
                                                    
     * 200,           400, 401, 404, 405,      500  
                                                    
   * POST                                           
                                                    
     *      201,      400, 401,      405, 409, 500  
                                                    
   * PUT                                            
                                                    
     * 200, 201,      400, 401, 404, 405,      500  
                                                    
   * PATCH                                          
                                                    
     * 200,           400, 401, 404, 405,      500  
                                                    
   * DELETE                                         
                                                    
     *           204,      401, 404, 405,      500  

`일반적으로 REST API에서 사용하는 HTTP Status Code 참고 <http://www.restapitutorial.com/httpstatuscodes.html#>`_
(별 표시된 코드가 일반적으로 REST에서 쓰는 top 10 코드임)


검색
=======

:ref:`search`

.. rubric:: 각주

.. [1] HTTP 기본 인증. 
   "**username:password**" 형태로 스트링을 만들고,
   해당 스트링을 base64로 인코딩 후, 
   HTTP의 *Authorization* 헤더에 "**Basic** " *뒤에* 인코딩한 스트링을 *붙여서* 전송.
   `HTTP Basic Auth 상세 설명(Client 부분 참고)
   <http://en.wikipedia.org/wiki/Basic_access_authentication>`_
