---
title: Povolit spřažení na základě souborů cookie pomocí Application Gateway
description: Tento článek poskytuje informace o tom, jak povolit spřažení na základě souborů cookie pomocí Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3af2705fedbb9c19d4f128e8e997d3fa73f8b5a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84807969"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Povolit spřažení na základě souborů cookie pomocí Application Gateway
Jak je uvedeno v [dokumentaci k Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings), Application Gateway podporuje spřažení na základě souborů cookie, což znamená, že může směrovat následný provoz z uživatelské relace na stejný server ke zpracování.

## <a name="example"></a>Příklad
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
