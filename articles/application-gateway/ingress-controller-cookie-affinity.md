---
title: Povolit spřažení na základě souborů cookie pomocí Application Gateway
description: Tento článek poskytuje informace o tom, jak povolit spřažení na základě souborů cookie pomocí Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a7806cf9518090539ba540a9a522af1aae2691f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397412"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Povolit spřažení na základě souborů cookie pomocí Application Gateway
Jak je uvedeno v [dokumentaci k Azure Application Gateway](./application-gateway-components.md#http-settings), Application Gateway podporuje spřažení na základě souborů cookie, což znamená, že může směrovat následný provoz z uživatelské relace na stejný server ke zpracování.

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