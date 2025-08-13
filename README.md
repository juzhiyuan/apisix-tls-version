# APISIX TLS

A working configuration that enables TLS 1.1 for Apache APISIX.

## Overview

1. Self-Generated Certificate：[https://regery.com/en/security/ssl-tools/self-signed-certificate-generator](https://regery.com/en/security/ssl-tools/self-signed-certificate-generator)
2. APISIX Supported TLS Versions
   1. 3.13
      1. JSONSchema: [https://github.com/apache/apisix/blob/master/apisix/schema_def.lua#L851](https://github.com/apache/apisix/blob/master/apisix/schema_def.lua#L851)
      2. Versions: TLS1.1, TLS1.2, TLS1.3
   2. 1.41
      1. Versions: TLSv1 TLSv1.1 TLSv1.2 TLSv1.3

## Deploy APISIX

```sh
$ docker run -d \
  --name etcd-quickstart \
  --network=apisix-quickstart-net \
  -e ALLOW_NONE_AUTHENTICATION=yes \
  -e ETCD_ADVERTISE_CLIENT_URLS=http://etcd-quickstart:2379 \
  bitnami/etcd:3.5.7

$ docker run -d \
    --name apisix-quickstart \
    --network=apisix-quickstart-net \
    -p9080:9080 -p9180:9180 -p9443:9443/tcp -p9443:9443/udp -p9090:9092 -p9100:9100 -p9091:9091 \
    -e APISIX_DEPLOYMENT_ETCD_HOST="[\"http://etcd-quickstart:2379\"]" \
    -v $(pwd)/apisix/config.yaml:/usr/local/apisix/conf/config.yaml \
    apache/apisix:3.13.0-ubuntu

$ ./adc sync -f adc.yaml
```

## Default Behavior

```sh
curl https://localhost:9443/uuid -v -k

* Host localhost:9443 was resolved.
* IPv6: ::1
* IPv4: 127.0.0.1
*   Trying [::1]:9443...
* Connected to localhost (::1) port 9443
* ALPN: curl offers h2,http/1.1
* (304) (OUT), TLS handshake, Client hello (1):
* (304) (IN), TLS handshake, Server hello (2):
* (304) (IN), TLS handshake, Unknown (8):
* (304) (IN), TLS handshake, Certificate (11):
* (304) (IN), TLS handshake, CERT verify (15):
* (304) (IN), TLS handshake, Finished (20):
* (304) (OUT), TLS handshake, Finished (20):
* SSL connection using TLSv1.3 / AEAD-AES256-GCM-SHA384 / [blank] / UNDEF
* ALPN: server accepted h2
* Server certificate:
*  subject: CN=localhost; O=Regery, https://regery.com; C=UA
*  start date: Aug 13 00:00:00 2025 GMT
*  expire date: Aug 13 06:43:14 2125 GMT
*  issuer: CN=Regery Self-Signed Certificate; O=Regery, https://regery.com; C=UA
*  SSL certificate verify result: unable to get local issuer certificate (20), continuing anyway.
* using HTTP/2
* [HTTP/2] [1] OPENED stream for https://localhost:9443/uuid
* [HTTP/2] [1] [:method: GET]
* [HTTP/2] [1] [:scheme: https]
* [HTTP/2] [1] [:authority: localhost:9443]
* [HTTP/2] [1] [:path: /uuid]
* [HTTP/2] [1] [user-agent: curl/8.7.1]
* [HTTP/2] [1] [accept: */*]
> GET /uuid HTTP/2
> Host: localhost:9443
> User-Agent: curl/8.7.1
> Accept: */*
>
* Request completely sent off
< HTTP/2 200
< content-type: application/json
< content-length: 53
< date: Wed, 13 Aug 2025 06:50:48 GMT
< access-control-allow-origin: *
< access-control-allow-credentials: true
< server: APISIX/3.13.0
<
{
  "uuid": "8600123e-726f-4ba1-9a6a-f9ef7865a48e"
}
* Connection #0 to host localhost left intact
```

## TLS 1.2

```sh
curl --tls-max 1.2 --tlsv1.2 https://localhost:9443/uuid -v -k

* Host localhost:9443 was resolved.
* IPv6: ::1
* IPv4: 127.0.0.1
*   Trying [::1]:9443...
* Connected to localhost (::1) port 9443
* ALPN: curl offers h2,http/1.1
* (304) (OUT), TLS handshake, Client hello (1):
* (304) (IN), TLS handshake, Server hello (2):
* TLSv1.2 (IN), TLS handshake, Certificate (11):
* TLSv1.2 (IN), TLS handshake, Server key exchange (12):
* TLSv1.2 (IN), TLS handshake, Server finished (14):
* TLSv1.2 (OUT), TLS handshake, Client key exchange (16):
* TLSv1.2 (OUT), TLS change cipher, Change cipher spec (1):
* TLSv1.2 (OUT), TLS handshake, Finished (20):
* TLSv1.2 (IN), TLS change cipher, Change cipher spec (1):
* TLSv1.2 (IN), TLS handshake, Finished (20):
* SSL connection using TLSv1.2 / ECDHE-RSA-AES128-GCM-SHA256 / [blank] / UNDEF
* ALPN: server accepted h2
* Server certificate:
*  subject: CN=localhost; O=Regery, https://regery.com; C=UA
*  start date: Aug 13 00:00:00 2025 GMT
*  expire date: Aug 13 06:43:14 2125 GMT
*  issuer: CN=Regery Self-Signed Certificate; O=Regery, https://regery.com; C=UA
*  SSL certificate verify result: unable to get local issuer certificate (20), continuing anyway.
* using HTTP/2
* [HTTP/2] [1] OPENED stream for https://localhost:9443/uuid
* [HTTP/2] [1] [:method: GET]
* [HTTP/2] [1] [:scheme: https]
* [HTTP/2] [1] [:authority: localhost:9443]
* [HTTP/2] [1] [:path: /uuid]
* [HTTP/2] [1] [user-agent: curl/8.7.1]
* [HTTP/2] [1] [accept: */*]
> GET /uuid HTTP/2
> Host: localhost:9443
> User-Agent: curl/8.7.1
> Accept: */*
>
* Request completely sent off
< HTTP/2 200
< content-type: application/json
< content-length: 53
< date: Wed, 13 Aug 2025 06:53:37 GMT
< access-control-allow-origin: *
< access-control-allow-credentials: true
< server: APISIX/3.13.0
<
{
  "uuid": "dfb7de54-6e47-46f5-9d60-7780a154f34e"
}
* Connection #0 to host localhost left intact
```

## TLS 1.1 Before Modification

```sh
curl --tls-max 1.1 --tlsv1.1 https://localhost:9443/uuid -v -k

* Host localhost:9443 was resolved.
* IPv6: ::1
* IPv4: 127.0.0.1
*   Trying [::1]:9443...
* Connected to localhost (::1) port 9443
* ALPN: curl offers h2,http/1.1
* (304) (OUT), TLS handshake, Client hello (1):
* LibreSSL/3.3.6: error:1404B410:SSL routines:ST_CONNECT:sslv3 alert handshake failure
* Closing connection
curl: (35) LibreSSL/3.3.6: error:1404B410:SSL routines:ST_CONNECT:sslv3 alert handshake failure
```

## TLS 1.1 After Modification

After using the customized [config](./apisix/config.yaml) file with TLS 1.1 enabled.

```sh
curl --tls-max 1.1 --tlsv1.1 https://localhost:9443/uuid -v -k

* Host localhost:9443 was resolved.
* IPv6: ::1
* IPv4: 127.0.0.1
*   Trying [::1]:9443...
* Connected to localhost (::1) port 9443
* ALPN: curl offers h2,http/1.1
* (304) (OUT), TLS handshake, Client hello (1):
* (304) (IN), TLS handshake, Server hello (2):
* TLSv1.1 (IN), TLS handshake, Certificate (11):
* TLSv1.1 (IN), TLS handshake, Server key exchange (12):
* TLSv1.1 (IN), TLS handshake, Server finished (14):
* TLSv1.1 (OUT), TLS handshake, Client key exchange (16):
* TLSv1.1 (OUT), TLS change cipher, Change cipher spec (1):
* TLSv1.1 (OUT), TLS handshake, Finished (20):
* TLSv1.1 (IN), TLS change cipher, Change cipher spec (1):
* TLSv1.1 (IN), TLS handshake, Finished (20):
* SSL connection using TLSv1.1 / ECDHE-RSA-AES128-SHA / [blank] / UNDEF
* ALPN: server accepted h2
* Server certificate:
*  subject: CN=localhost; O=Regery, https://regery.com; C=UA
*  start date: Aug 13 00:00:00 2025 GMT
*  expire date: Aug 13 06:43:14 2125 GMT
*  issuer: CN=Regery Self-Signed Certificate; O=Regery, https://regery.com; C=UA
*  SSL certificate verify result: unable to get local issuer certificate (20), continuing anyway.
* using HTTP/2
* [HTTP/2] [1] OPENED stream for https://localhost:9443/uuid
* [HTTP/2] [1] [:method: GET]
* [HTTP/2] [1] [:scheme: https]
* [HTTP/2] [1] [:authority: localhost:9443]
* [HTTP/2] [1] [:path: /uuid]
* [HTTP/2] [1] [user-agent: curl/8.7.1]
* [HTTP/2] [1] [accept: */*]
> GET /uuid HTTP/2
> Host: localhost:9443
> User-Agent: curl/8.7.1
> Accept: */*
> 
* Request completely sent off
< HTTP/2 200 
< content-type: application/json
< content-length: 53
< date: Wed, 13 Aug 2025 11:53:49 GMT
< access-control-allow-origin: *
< access-control-allow-credentials: true
< server: APISIX/3.13.0
< 
{
  "uuid": "765bc9f7-4e47-4cb1-8b3f-0977854639c9"
}
* Connection #0 to host localhost left intact
```
