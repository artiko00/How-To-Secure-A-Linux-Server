# Asegurar nginx

> **Nota del Traductor:** Esta es una traducción al español de la guía "[Secure nginx](nginx.md)". Se mantienen términos técnicos en inglés siguiendo convenciones de la industria.

Recurso global: [webdock.io](https://webdock.io/en/docs/how-guides/security-guides/how-to-configure-security-headers-in-nginx-and-apache)

* [Deshabilitar tokens del server](https://nginx.org/en/docs/http/ngx_http_core_module.html#server_tokens)

  ```nginx
  server_tokens off;
  ```

* [Referencia de Content Security Policy](https://content-security-policy.com/)

  ```nginx
  add_header Content-Security-Policy "default-src 'self';" always;
  ```

* [X-frame-options](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options#sameorigin)

  ```nginx
  add_header X-Frame-Options SAMEORIGIN always;
  ```

* [X-Xss-Protection: block](https://docs.nginx.com/nginx-management-suite/acm/how-to/policies/proxy-response-headers/)

  ```nginx
  add_header X-Xss-Protection "1; mode=block" always;
  ```

* [strict origin](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referrer-Policy#strict-origin)

  ```nginx
  add_header Referrer-Policy "strict-origin" always;
  ```

* [Permissions Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Permissions-Policy)

  ```nginx
  add_header Permissions-Policy "geolocation=(),midi=(),sync-xhr=(),microphone=(),camera=(),magnetometer=(),gyroscope=(),fullscreen=(self),payment=()";
  ```

* [Content sniffing](https://en.wikipedia.org/wiki/Content_sniffing)

  ```nginx
  add_header X-Content-Type-Options nosniff always;
  ```
