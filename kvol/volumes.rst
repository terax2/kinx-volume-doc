==================
kvol - volumes API
==================


API List
========

+----------------------------------------------------------+-----------------------------------------------+
|Endpoint                                                  |Description                                    |
+==========================================================+===============================================+
|:ref:`GET /api/kvol/list <ts_list>`                       |대상존의 볼륨타입의 백업존 목록을 출력한다.    |
+----------------------------------------------------------+-----------------------------------------------+
|:ref:`GET /api/kvol/show <ts_show>`                       |대상존의 볼륨타입의 백업존 상세정보를 출력한다.|
+----------------------------------------------------------+-----------------------------------------------+
|:ref:`GET /api/kvol/create <ts_create>`                   |스냅샷을 지정된 타존 볼륨으로 생성한다.        |
+----------------------------------------------------------+-----------------------------------------------+
|:ref:`GET /api/kvol/createlist <ts_createlist>`           |스냅샷의 타존 볼륨 생성 진행 상황 확인.        |
+----------------------------------------------------------+-----------------------------------------------+
|:ref:`GET /api/kvol/recovery <ts_recovery>`               |존 전체 장애시 해당볼륨을 타 존으로 복구처리.  |
+----------------------------------------------------------+-----------------------------------------------+
|:ref:`GET /api/kvol/recoverylist <ts_recoverylist>`       |타 존으로 복구처리 진행상황 확인.              |
+----------------------------------------------------------+-----------------------------------------------+


API Contents
============

.. _ts_list:

.. http:get:: /api/kvol/list

   대상존의 볼륨타입의 백업존 목록을 출력한다.

   **요청 예**:

   .. sourcecode:: http

      GET /api/kvol/list?zone=mitaka1&volume-type-id=6a21db69-dc6b-4423-834c-679ae153a638 HTTP/1.1
      Host: example.com

   **응답 예**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "backup": [
          {
            "cluster_ip": "182.161.123.103",
            "name": "vol01",
            "seq": 2,
            "svm_ip": "182.161.123.109",
            "svm_name": "k_svm_nfs_bak",
            "zone_id": "kilo1"
          },
          {
            "cluster_ip": "182.161.123.103",
            "name": "vol01",
            "seq": 4,
            "svm_ip": "182.161.123.120",
            "svm_name": "k_svm_nfs_bak2",
            "zone_id": "kilo2"
          }
        ]
      }


   :queryparam string zone: * **(필수)** 해당 존(zone) ID 
   :queryparam string volume-type-id: * **(필수)** 해당 존의 오픈스택에 등록된 볼륨타입의 ID


   :reqheader Authorization:  basic auth (user: gabianet, passwd: :http:get:`/api/token` 에서 발급받은 인증 토큰)
   :resheader Content-Type: json만을 지원
   :statuscode 200: no error
   :statuscode 204: 해당 데이터가 없음
   :statuscode 400: 요청 파라미터 오류
   :statuscode 401: Token이 expire되거나, 올바르지 않음
   :statuscode 405: 내부 서버 오류



.. _ts_show:

.. http:get:: /api/kvol/show
   
   대상존의 볼륨타입의 백업존 상세정보를 출력한다.

   **요청 예**:

   .. sourcecode:: http

      GET /api/kvol/show?src-zone=mitaka1&src-volume-type-id=6a21db69-dc6b-4423-834c-679ae153a638 HTTP/1.1
      Host: example.com

   **응답 예**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "backup-detail": [
          {
            "dst-path": "182.161.123.109:/vol01",
            "dst-svm-name": "k_svm_nfs_bak",
            "dst-zone": "kilo1",
            "src-path": "182.161.123.106:/vol01",
            "src-svm-name": "m_svm_nfs",
            "src-vol-type-id": "6a21db69-dc6b-4423-834c-679ae153a638",
            "src-zone": "mitaka1",
            "status": "active"
          },
          {
            "dst-path": "182.161.123.120:/vol01",
            "dst-svm-name": "k_svm_nfs_bak2",
            "dst-zone": "kilo2",
            "src-path": "182.161.123.106:/vol01",
            "src-svm-name": "m_svm_nfs",
            "src-vol-type-id": "6a21db69-dc6b-4423-834c-679ae153a638",
            "src-zone": "mitaka1",
            "status": "active"
          }
        ]
      }


   :queryparam string src-zone: * **(필수)** 해당 존(zone) ID 
   :queryparam string src-volume-type-id: * **(필수)** 해당 존의 오픈스택에 등록된 볼륨타입의 ID
   :queryparam string dst-zone: * **(선택)** 백업 존(zone) ID 
    * 미입력시 default는 ``None (전체)``.

 
   :reqheader Authorization:  basic auth (user: gabianet, passwd: :http:get:`/api/token` 에서 발급받은 인증 토큰)
   :resheader Content-Type: json만을 지원
   :statuscode 200: no error
   :statuscode 204: 해당 데이터가 없음
   :statuscode 400: 요청 파라미터 오류
   :statuscode 401: Token이 expire되거나, 올바르지 않음
   :statuscode 405: 내부 서버 오류





.. _ts_create:

.. http:get:: /api/kvol/create
   
   스냅샷을 지정된 타존 볼륨으로 생성한다.

   **요청 예**:

   .. sourcecode:: http

      GET /api/kvol/create?src-zone=mitaka1&src-volume-type-id=6a21db69-dc6b-4423-834c-679ae153a638&src-snapshot-id=b1c8b4d8-29e2-437a-830c-a012aed39c70&dst-zone=kilo1&dst-tenant-id=e21dbcb88d314eefaf953c3de2aacea5&dst-volume-type-id=45856d65-9021-4702-b8a6-62d5326b8f4e HTTP/1.1
      Host: example.com

   **응답 예**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "message": "snapshot volume creating ...",
        "rowkey": 152
      }


   :queryparam string src-zone: * **(필수)** 오리지날 존(zone) ID 
   :queryparam string src-volume-type-id: * **(필수)** 오리지날 존의 오픈스택에 등록된 볼륨타입의 ID
   :queryparam string src-snapshot-id: * **(필수)** 볼륨 생성 대상이 되는 snapshot-id
   :queryparam string bootable: * **(선택)** 해당 snapshot 이 부팅가능 볼륨인지 여부
    * (yes/ no) ``yes``: yes, ``no``: yes
    * 미입력시 default는 ``no``.
   :queryparam string dst-zone: * **(필수)** 복구 존(zone) ID 
   :queryparam string dst-tenant-id: * **(필수)** 복구 존의 tenant-id
   :queryparam string dst-volume-type-id: * **(필수)** 복구 존의 오픈스택에 등록된 복구될 볼륨타입 ID

 
   :reqheader Authorization:  basic auth (user: gabianet, passwd: :http:get:`/api/token` 에서 발급받은 인증 토큰)
   :resheader Content-Type: json만을 지원
   :statuscode 200: no error
   :statuscode 204: 해당 데이터가 없음
   :statuscode 400: 요청 파라미터 오류
   :statuscode 401: Token이 expire되거나, 올바르지 않음
   :statuscode 405: 내부 서버 오류





.. _ts_createlist:

.. http:get:: /api/kvol/createlist
   
   대상존의 볼륨타입의 백업존 상세정보를 출력한다.

   **요청 예**:

   .. sourcecode:: http

      GET /api/kvol/createlist?rowkey=152 HTTP/1.1
      Host: example.com

   **응답 예**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "clist": [
          {
            "create_end": "2018-09-11 02:38:51",
            "create_start": "2018-09-11 02:37:41",
            "dst_svm_name": "k_svm_nfs",
            "dst_zone_id": "kilo1",
            "new_volume_id": "280f3419-2b65-46cf-8b39-6d22ce41eac9",
            "org_snapshot_id": "b1c8b4d8-29e2-437a-830c-a012aed39c70",
            "org_zone_id": "mitaka1",
            "result_msg": "OK",
            "src_svm_name": "k_svm_nfs_bak",
            "src_zone_id": "kilo1",
            "status": "SUCCESS"
          }
        ]
      }


   :queryparam int rowkey: * **(선택)** create 명령 실행후 결과 rowkey 값
    * 미입력시 default는 ``0``.
   :queryparam int limit: * **(선택)** createlist 갯수를 지정, 가장 최근 리스트 갯수만큼 출력
    * 미입력시 default는 ``5``.

 
   :reqheader Authorization:  basic auth (user: gabianet, passwd: :http:get:`/api/token` 에서 발급받은 인증 토큰)
   :resheader Content-Type: json만을 지원
   :statuscode 200: no error
   :statuscode 204: 해당 데이터가 없음
   :statuscode 400: 요청 파라미터 오류
   :statuscode 401: Token이 expire되거나, 올바르지 않음
   :statuscode 405: 내부 서버 오류






.. _ts_recovery:

.. http:get:: /api/kvol/recovery
   
   존 전체 장애시 해당볼륨을 타 존으로 복구처리.

   **요청 예**:

   .. sourcecode:: http

      GET /api/kvol/recovery?src-zone=mitaka1&src-volume-type-id=6a21db69-dc6b-4423-834c-679ae153a638&src-volume-id=b1c8b4d8-29e2-437a-830c-a012aed39c70&dst-zone=kilo1&dst-tenant-id=e21dbcb88d314eefaf953c3de2aacea5&dst-volume-type-id=45856d65-9021-4702-b8a6-62d5326b8f4e HTTP/1.1
      Host: example.com

   **응답 예**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "message": "recovery volume creating ...",
        "rowkey": 152
      }


   :queryparam string src-zone: * **(필수)** 오리지날 존(zone) ID 
   :queryparam string src-volume-type-id: * **(필수)** 오리지날 존의 오픈스택에 등록된 볼륨타입의 ID
   :queryparam string src-volume-id: * **(필수)** 볼륨 생성 대상이 되는 원본 volume-id
   :queryparam string bootable: * **(선택)** 해당 snapshot 이 부팅가능 볼륨인지 여부
    * (yes/ no) ``yes``: yes, ``no``: yes
    * 미입력시 default는 ``no``.
   :queryparam string dst-zone: * **(필수)** 복구 존(zone) ID 
   :queryparam string dst-tenant-id: * **(필수)** 복구 존의 tenant-id
   :queryparam string dst-volume-type-id: * **(필수)** 복구 존의 오픈스택에 등록된 복구될 볼륨타입 ID

 
   :reqheader Authorization:  basic auth (user: gabianet, passwd: :http:get:`/api/token` 에서 발급받은 인증 토큰)
   :resheader Content-Type: json만을 지원
   :statuscode 200: no error
   :statuscode 204: 해당 데이터가 없음
   :statuscode 400: 요청 파라미터 오류
   :statuscode 401: Token이 expire되거나, 올바르지 않음
   :statuscode 405: 내부 서버 오류





.. _ts_recoverylist:

.. http:get:: /api/kvol/recoverylist
   
   타 존으로 복구처리 진행상황 확인.

   **요청 예**:

   .. sourcecode:: http

      GET /api/kvol/recoverylist?rowkey=152 HTTP/1.1
      Host: example.com

   **응답 예**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "clist": [
          {
            "create_end": "2018-09-11 02:38:51",
            "create_start": "2018-09-11 02:37:41",
            "dst_svm_name": "k_svm_nfs",
            "dst_zone_id": "kilo1",
            "new_volume_id": "280f3419-2b65-46cf-8b39-6d22ce41eac9",
            "org_volume_id": "b1c8b4d8-29e2-437a-830c-a012aed39c70",
            "org_zone_id": "mitaka1",
            "result_msg": "OK",
            "src_svm_name": "k_svm_nfs_bak",
            "src_zone_id": "kilo1",
            "status": "SUCCESS"
          }
        ]
      }


   :queryparam int rowkey: * **(선택)** create 명령 실행후 결과 rowkey 값
    * 미입력시 default는 ``0``.
   :queryparam int limit: * **(선택)** createlist 갯수를 지정, 가장 최근 리스트 갯수만큼 출력
    * 미입력시 default는 ``5``.

 
   :reqheader Authorization:  basic auth (user: gabianet, passwd: :http:get:`/api/token` 에서 발급받은 인증 토큰)
   :resheader Content-Type: json만을 지원
   :statuscode 200: no error
   :statuscode 204: 해당 데이터가 없음
   :statuscode 400: 요청 파라미터 오류
   :statuscode 401: Token이 expire되거나, 올바르지 않음
   :statuscode 405: 내부 서버 오류

