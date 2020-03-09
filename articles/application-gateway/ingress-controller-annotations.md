---
title: Poznámky k kontroleru Application Gateway pro příchozí přenosy
description: Tento článek poskytuje dokumentaci k poznámkám, které jsou specifické pro řadič Application Gateway příchozího přenosu dat.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a3583a5efd120733ce7f6b71a7594b5636593f99
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373494"
---
# <a name="annotations-for-application-gateway-ingress-controller"></a>Poznámky pro řadič Application Gateway pro příchozí přenos dat 

## <a name="introductions"></a>Přehled

K Kubernetes prostředku příchozího přenosu dat se dá pokomentovat libovolné páry klíč/hodnota. AGIC spoléhá na poznámky k funkcím Application Gateway programu, které se nedají konfigurovat prostřednictvím YAML příchozího přenosu dat. Příchozí poznámky se aplikují na všechna nastavení HTTP, back-endové fondy a naslouchací procesy odvozené z prostředku příchozího přenosu dat.

## <a name="list-of-supported-annotations"></a>Seznam podporovaných poznámek

Aby byl prostředek příchozího přenosu dat AGIC, **musí být označen** `kubernetes.io/ingress.class: azure/application-gateway`. V takovém případě bude AGIC pracovat pouze s dotyčným prostředkem příchozího přenosu dat.

| Klíč poznámky | Typ hodnoty | Výchozí hodnota | Povolené hodnoty
| -- | -- | -- | -- |
| [appgw.ingress.kubernetes.io/backend-path-prefix](#backend-path-prefix) | `string` | `nil` | |
| [appgw.ingress.kubernetes.io/ssl-redirect](#ssl-redirect) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining](#connection-draining) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining-timeout](#connection-draining) | `int32` (sekundy) | `30` | |
| [appgw.ingress.kubernetes.io/cookie-based-affinity](#cookie-based-affinity) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/request-timeout](#request-timeout) | `int32` (sekundy) | `30` | |
| [appgw.ingress.kubernetes.io/use-private-ip](#use-private-ip) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/backend-protocol](#backend-protocol) | `string` | `http` | `http`, `https` |

## <a name="backend-path-prefix"></a>Předpona cesty k back-endu

Tato poznámka umožňuje přepsat cestu back-end zadanou v prostředku příchozího přenosu předponou zadanou v této poznámce. To uživatelům umožňuje vystavit služby, jejichž koncové body se liší od názvů koncových bodů používaných k vystavení služby v prostředku příchozího přenosu dat.

### <a name="usage"></a>Využití

```yaml
appgw.ingress.kubernetes.io/backend-path-prefix: <path prefix>
```

### <a name="example"></a>Příklad

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-bkprefix
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```
V předchozím příkladu jsme definovali prostředek příchozího přenosu s názvem `go-server-ingress-bkprefix` s poznámkou `appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"`. Poznámka oznamuje službě Application Gateway, aby vytvořila nastavení HTTP, které bude mít přepsání předpony cesty pro cestu `/hello` `/test/`.

> [!NOTE] 
> Ve výše uvedeném příkladu máme definováno jenom jedno pravidlo. Poznámky se ale vztahují na celý prostředek příchozího přenosu dat, takže pokud uživatel definoval více pravidel, bude pro každou zadanou cestu nastavená Předpona cesty k back-endu. Proto pokud uživatel chce jiná pravidla s různými předponami cesty (dokonce i pro stejnou službu), musel by definovat různé prostředky příchozího přenosu dat.

## <a name="ssl-redirect"></a>Přesměrování SSL

Application Gateway je [možné nakonfigurovat](https://docs.microsoft.com/azure/application-gateway/application-gateway-redirect-overview) tak, aby automaticky přesměrovala adresy URL http na jejich protějšky https. Pokud je tato poznámka přítomná a protokol TLS je správně nakonfigurovaný, Kubernetes příchozí řadič vytvoří [pravidlo směrování s konfigurací přesměrování](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal#add-a-routing-rule-with-a-redirection-configuration) a použije změny v Application Gateway. Vytvořené přesměrování bude `301 Moved Permanently`HTTP.

### <a name="usage"></a>Využití

```yaml
appgw.ingress.kubernetes.io/ssl-redirect: "true"
```

### <a name="example"></a>Příklad

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-redirect
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  tls:
   - hosts:
     - www.contoso.com
     secretName: testsecret-tls
  rules:
  - host: www.contoso.com
    http:
      paths:
      - backend:
          serviceName: websocket-repeater
          servicePort: 80
```

## <a name="connection-draining"></a>Vyprazdňování připojení

`connection-draining`: Tato anotace umožňuje uživatelům určit, jestli se má povolit vyprazdňování připojení.
`connection-draining-timeout`: Tato anotace umožňuje uživatelům zadat časový limit, po jehož uplynutí Application Gateway ukončí požadavky na vyprázdnění koncového bodu back-endu.

### <a name="usage"></a>Využití

```yaml
appgw.ingress.kubernetes.io/connection-draining: "true"
appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
```

### <a name="example"></a>Příklad

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-drain
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/connection-draining: "true"
    appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="cookie-based-affinity"></a>Spřažení na základě souborů cookie

Tato poznámka umožňuje určit, jestli se má povolit spřažení na základě souborů cookie.

### <a name="usage"></a>Využití

```yaml
appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
```

### <a name="example"></a>Příklad

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-affinity
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="request-timeout"></a>Časový limit žádosti

Tato poznámka umožňuje zadat časový limit žádosti v sekundách, po kterém Application Gateway požadavek selže, pokud odpověď nepřijde.

### <a name="usage"></a>Využití

```yaml
appgw.ingress.kubernetes.io/request-timeout: "20"
```

### <a name="example"></a>Příklad

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/request-timeout: "20"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="use-private-ip"></a>Použít privátní IP adresu

Tato poznámka nám umožňuje určit, jestli se má tento koncový bod vystavit na privátní IP adresu Application Gateway.

> [!NOTE]
> * Application Gateway nepodporuje více IP adres na stejném portu (například: 80/443). Příchozí s anotací `appgw.ingress.kubernetes.io/use-private-ip: "false"` a další s `appgw.ingress.kubernetes.io/use-private-ip: "true"` na `HTTP` způsobí selhání AGIC při aktualizaci Application Gateway.
> * U Application Gateway, které nemají privátní IP adresu, budou příchozí přenosy s `appgw.ingress.kubernetes.io/use-private-ip: "true"` ignorovány. Tato akce se projeví v protokolech kontroleru a událostech příchozího přenosu dat `NoPrivateIP` upozornění.


### <a name="usage"></a>Využití
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

### <a name="example"></a>Příklad
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/use-private-ip: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="backend-protocol"></a>Back-end protokol

Tato poznámka nám umožňuje zadat protokol, který Application Gateway použít při komunikaci s lusky. Podporované protokoly: `http`, `https`

> [!NOTE]
> * I když jsou certifikáty podepsané svým držitelem podporovány v Application Gateway, v současné době AGIC podporuje `https` pouze v případě, že k používání certifikátu podepsaného známou certifikační autoritou používá.
> * Ujistěte se, že nepoužíváte port 80 s protokolem HTTPS a port 443 s protokolem HTTP v Luskech.

### <a name="usage"></a>Využití
```yaml
appgw.ingress.kubernetes.io/backend-protocol: "https"
```

### <a name="example"></a>Příklad
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-protocol: "https"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 443
```