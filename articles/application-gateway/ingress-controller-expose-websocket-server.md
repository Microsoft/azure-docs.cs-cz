---
title: Vystavit server WebSocket aplikační bráně
description: Tento článek obsahuje informace o tom, jak vystavit server WebSocket na aplikační bránu s řadičem příchozího přenosu dat pro clustery AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 1f068c9d98a827afd16da01bdc40cbb6ca5dc465
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297828"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>Vystavit server WebSocket aplikační bráně

Jak je uvedeno v dokumentaci Application Gateway v2 - [poskytuje nativní podporu pro protokoly WebSocket a HTTP/2](features.md#websocket-and-http2-traffic). Vezměte prosím na vědomí, že pro aplikační bránu a Příchozí přenos dat Kubernetes - neexistuje žádné uživatelsky konfigurovatelné nastavení pro selektivní povolení nebo zakázání podpory WebSocket.

Kubernetes nasazení YAML níže ukazuje minimální konfiguraci používanou k nasazení serveru WebSocket, což je stejné jako nasazení běžného webového serveru:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: websocket-server
spec:
  selector:
    matchLabels:
      app: ws-app
  replicas: 2
  template:
    metadata:
      labels:
        app: ws-app
    spec:
      containers:
        - name: websocket-app
          imagePullPolicy: Always
          image: your-container-repo.azurecr.io/websockets-app
          ports:
            - containerPort: 8888
      imagePullSecrets:
        - name: azure-container-registry-credentials

---

apiVersion: v1
kind: Service
metadata:
  name: websocket-app-service
spec:
  selector:
    app: ws-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8888

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-repeater
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: ws.contoso.com
      http:
        paths:
          - backend:
              serviceName: websocket-app-service
              servicePort: 80
```

Vzhledem k tomu, že jsou splněny všechny požadavky a máte aplikační bránu řízenou příchozím přenosem dat Kubernetes ve vašem AKS, výše uvedené `ws.contoso.com` nasazení by mělo za následek server WebSockets vystavený na portu 80 veřejné IP adresy a domény vaší aplikační brány.

Následující příkaz cURL by otestoval nasazení serveru WebSocket:
```sh
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>Sondy stavu websocketu

Pokud vaše nasazení explicitně nedefinuje sondy stavu, aplikace brána by pokus HTTP GET na koncovém bodě serveru WebSocket.
V závislosti na implementaci serveru[(zde je jeden milujeme)](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)`Sec-Websocket-Version` WebSocket konkrétní záhlaví může být požadováno (například).
Vzhledem k tomu, že aplikační brána nepřidává záhlaví WebSocket, bude pravděpodobně odpověď `400 Bad Request`sondy stavu brány aplikace ze serveru WebSocket .
V důsledku toho bude application gateway označit pody jako není `502 Bad Gateway` v pořádku, což nakonec za následek pro spotřebitele serveru WebSocket.
Chcete-li se tomu vyhnout, budete muset přidat obslužnou rutinu HTTP GET pro kontrolu stavu na server (například,`/health` který vrátí `200 OK`).
