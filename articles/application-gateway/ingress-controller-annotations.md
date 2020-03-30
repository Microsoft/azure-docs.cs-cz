---
title: Poznámky řadiče příchozího přenosu dat aplikační brány
description: Tento článek obsahuje dokumentaci k poznámkám specifickým pro řadič příchozího přenosu dat aplikační brány.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: f54381ddcd11a2e4a24d30d812468da85b5403de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335824"
---
# <a name="annotations-for-application-gateway-ingress-controller"></a>Poznámky pro řadič příchozího přenosu dat aplikační brány 

## <a name="introductions"></a>Úvody

Prostředek Příchozí přenos dat Kubernetes může být anotován s libovolnými páry klíč/hodnota. AGIC spoléhá na poznámky k programování funkcí aplikační brány, které nelze konfigurovat prostřednictvím příchozího přenosu dat YAML. Ingress poznámky jsou použity pro všechny nastavení HTTP, back-endové fondy a naslouchací procesy odvozené z prostředku příchozího přenosu dat.

## <a name="list-of-supported-annotations"></a>Seznam podporovaných poznámk

Pro příchozí zdroj, který má být sledován AGIC, musí `kubernetes.io/ingress.class: azure/application-gateway`být **anotován** s . Teprve pak AGIC bude pracovat s ingress prostředku v otázce.

| Klíč poznámky | Typ hodnoty | Výchozí hodnota | Povolené hodnoty
| -- | -- | -- | -- |
| [appgw.ingress.kubernetes.io/backend-path-prefix](#backend-path-prefix) | `string` | `nil` | |
| [appgw.ingress.kubernetes.io/ssl-redirect](#tls-redirect) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining](#connection-draining) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining-timeout](#connection-draining) | `int32`(v sekundách) | `30` | |
| [appgw.ingress.kubernetes.io/cookie-based-affinity](#cookie-based-affinity) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/request-timeout](#request-timeout) | `int32`(v sekundách) | `30` | |
| [appgw.ingress.kubernetes.io/use-private-ip](#use-private-ip) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/backend-protocol](#backend-protocol) | `string` | `http` | `http`, `https` |

## <a name="backend-path-prefix"></a>Předpona cesty back-end

Tato anotace umožňuje přepsání cesty back-endurčené v příchozím přenosu dat s předponou zadanou v této anotaci. To umožňuje uživatelům vystavit služby, jejichž koncové body se liší od názvů koncových bodů používaných k vystavení služby v prostředku příchozího přenosu dat.

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
Ve výše uvedeném příkladu jsme definovali prostředek příchozího přenosu dat s názvem `go-server-ingress-bkprefix` s poznámkou . `appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"` Anotace říká aplikační bráně k vytvoření nastavení HTTP, které bude mít `/hello` `/test/`předpon cesty pro cestu do .

> [!NOTE] 
> Ve výše uvedeném příkladu máme definováno pouze jedno pravidlo. Poznámky jsou však použitelné pro celý prostředek příchozího přenosu dat, takže pokud uživatel definoval více pravidel, předpona back-endové cesty by byla nastavena pro každou ze zadaných cest. Proto pokud uživatel chce různá pravidla s různými předčíslí cesty (i pro stejnou službu), budou muset definovat různé prostředky příchozího přenosu dat.

## <a name="tls-redirect"></a>Přesměrování TLS

Aplikační bránu [lze nakonfigurovat tak,](https://docs.microsoft.com/azure/application-gateway/application-gateway-redirect-overview) aby automaticky přesměrovala adresy URL HTTP na jejich protějšky HTTPS. Pokud je tato anotace k dispozici a tls je správně nakonfigurován, Kubernetes Ingress řadič vytvoří [pravidlo směrování s konfigurací přesměrování](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal#add-a-routing-rule-with-a-redirection-configuration) a použít změny na aplikační bránu. Vytvořené přesměrování bude http `301 Moved Permanently`.

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

## <a name="connection-draining"></a>Vypouštění připojení

`connection-draining`: Tato poznámka umožňuje uživatelům určit, zda má být připojení vypouštěno.
`connection-draining-timeout`: Tato poznámka umožňuje uživatelům určit časový rámec, po kterém bude application gateway ukončit požadavky na vypouštěcí koncový bod back-endu.

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

## <a name="cookie-based-affinity"></a>Spřažení založené na souborech cookie

Tato anotace umožňuje určit, zda má být povolena spřažení založená na souborech cookie.

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

Tato anotace umožňuje zadat časový rozsah požadavku v sekundách, po kterém application gateway se nezdaří požadavek, pokud není přijata odpověď.

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

Tato anotace nám umožňuje určit, zda chcete vystavit tento koncový bod na privátní IP aplikační brány.

> [!NOTE]
> * Aplikační brána nepodporuje více IP adresy na stejném portu (příklad: 80/443). Příchozí přenos dat `appgw.ingress.kubernetes.io/use-private-ip: "false"` s poznámkou a další s `appgw.ingress.kubernetes.io/use-private-ip: "true"` on `HTTP` způsobí, že AGIC nezdaří při aktualizaci aplikační brány.
> * Pro aplikační bránu, která nemá privátní IP adresy, ingresses s `appgw.ingress.kubernetes.io/use-private-ip: "true"` budou ignorovány. To se projeví v protokolech řadiče a příchozí `NoPrivateIP` přenos y události pro příchozí přenosy dat s upozorněním.


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

## <a name="backend-protocol"></a>Back-endový protokol

Tato anotace nám umožňuje určit protokol, který by měla aplikační brána používat při rozhovoru s pody. Podporované protokoly: `http`,`https`

> [!NOTE]
> * Zatímco certifikáty podepsané svým držitelem jsou podporovány v application `https` gateway, v současné době AGIC podporuje pouze v případě, že pody používají certifikát podepsaný známou certifikační autoritou.
> * Ujistěte se, že nepoužíváte port 80 s HTTPS a port 443 s HTTP na podech.

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