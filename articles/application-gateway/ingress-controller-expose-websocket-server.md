---
title: Vystavení serveru WebSocket pro Application Gateway
description: Tento článek poskytuje informace o tom, jak vystavit Server WebSocket pro Application Gateway s řadičem příchozího přenosu dat pro clustery AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 68d4ff7e4617136e4c58ce672f34de56e46f0229
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85207783"
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

Vzhledem k tomu, že jsou splněné všechny požadavky a máte Application Gateway řízený Kubernetes příchozím voláním ve vaší AKS, by výše uvedené nasazení vedlo k vystavení serveru WebSockets na portu 80 veřejné IP adresy vašeho Application Gateway a `ws.contoso.com` doméně.

Následující příkaz složeného testu vyzkouší nasazení serveru WebSocket:
```shell
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
V závislosti na implementaci serveru ([tady je něco, co se vám líbí](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)) může být nutné zadat záhlaví konkrétního protokolu WebSocket (např `Sec-Websocket-Version` .).
Vzhledem k tomu, že Application Gateway nepřidává hlavičky protokolu WebSocket, odpověď testu stavu Application Gateway ze serveru WebSocket bude pravděpodobně `400 Bad Request` .
V důsledku toho Application Gateway označí lusky jako chybné, což způsobí, že se bude `502 Bad Gateway` pro uživatele serveru WebSocket považovat za.
Aby k tomu nedocházelo, možná budete muset přidat obslužnou rutinu HTTP GET pro kontrolu stavu na váš server ( `/health` například, který vrátí `200 OK` ).
