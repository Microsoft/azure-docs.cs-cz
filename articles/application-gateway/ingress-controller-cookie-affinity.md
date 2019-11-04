---
title: Povolit spřažení na základě souborů cookie pomocí Application Gateway
description: Tento článek poskytuje informace o tom, jak povolit spřažení na základě souborů cookie pomocí Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2019
ms.author: caya
ms.openlocfilehash: cb1a403888f8dca78d1af7395095aa2bac39935e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513572"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Povolit spřažení na základě souborů cookie pomocí Application Gateway
Jak je uvedeno v [dokumentaci k Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings), Application Gateway podporuje spřažení na základě souborů cookie, což znamená, že může směrovat následný provoz z uživatelské relace na stejný server ke zpracování.

## <a name="example"></a>Příklad:
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```
