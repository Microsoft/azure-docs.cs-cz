---
title: Vystavení serveru WebSocket pro Application Gateway
description: Tento článek poskytuje informace o tom, jak vystavit Server WebSocket pro Application Gateway s řadičem příchozího přenosu dat pro clustery AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 1f068c9d98a827afd16da01bdc40cbb6ca5dc465
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79297828"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>Vystavení serveru WebSocket pro Application Gateway

Jak je uvedeno v dokumentaci k Application Gateway v2 – [poskytuje nativní podporu protokolů WebSocket a HTTP/2](features.md#websocket-and-http2-traffic). Pamatujte na to, že u Application Gateway i Kubernetes příchozího přenosu dat neexistuje žádné uživatelsky konfigurovatelné nastavení pro selektivní povolení nebo zakázání podpory protokolu WebSocket.

V níže uvedeném YAML nasazení Kubernetes se zobrazuje minimální konfigurace, která se používá k nasazení serveru WebSocket, který je stejný jako při nasazení běžného webového serveru:
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

Vzhledem k tomu, že jsou splněné všechny požadavky a máte Application Gateway řízený Kubernetes příchozím voláním v AKS, by výše uvedené nasazení vedlo k vystavení serveru WebSockets na portu 80 veřejné IP adresy vašeho Application Gateway a `ws.contoso.com` doméně.

Následující příkaz složeného testu vyzkouší nasazení serveru WebSocket:
```sh
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>Sondy stavu protokolu WebSocket

Pokud vaše nasazení explicitně nedefinuje testy stavu, Application Gateway by se pokusila získat na koncovém bodu serveru WebSocket požadavek HTTP.
V závislosti na implementaci serveru ([tady je něco, co se vám líbí](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)) může být potřeba zadat záhlaví konkrétního protokolu WebSocket (`Sec-Websocket-Version` pro instanci).
Vzhledem k tomu, že Application Gateway nepřidává hlavičky protokolu WebSocket, odpověď testu stavu Application Gateway ze serveru WebSocket bude pravděpodobně `400 Bad Request`.
V důsledku toho Application Gateway označí, že vaše lusky nejsou v pořádku, což bude mít za následek `502 Bad Gateway` pro uživatele serveru WebSocket.
Aby k tomu nedocházelo, možná budete muset přidat obslužnou rutinu HTTP GET pro kontrolu stavu na váš server (`/health` pro instanci, která vrací `200 OK`).
