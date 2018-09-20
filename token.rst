=========
token API
=========

API Contents
============

.. _token:

.. http:get:: /api/token
   
   Monitor 서비스 API 리소스 접근을 위한 토큰 가져오기

   **요청 예**:

   .. sourcecode:: http

      GET /api/token HTTP/1.1
      Host: example.com
      Authorization: Basic dGVzdHVzZXI6dGVzdHBhc3N3ZA==

   **응답 예**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
          "duration": 600,
          "token": "eyJhbGciOiJIUzI1NiIsImV4cCI6MTM5MzgxNDMxOSwiaWF0IjoxMzkzODEzNzE5fQ.eyJ1c2VybmFtZSI6IkdNMTMwMjQwNyJ9.c9tfGYZOqNn83zbRqlXCqP2IH9FLFeA29EPnVktpCp0"
      }

   :reqheader Authorization: Basic "[API계정:API비번] base64 encoding 스트링"
   :resheader Content-Type: json만을 지원
   :statuscode 200: no error
   :statuscode 401: 잘못된 토큰 or 시간 오류
   :statuscode 403: 계정 정보 오류
