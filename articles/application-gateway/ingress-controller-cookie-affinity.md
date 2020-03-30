---
title: Povolení spřažení založené na souborech cookie pomocí brány aplikace
description: Tento článek obsahuje informace o tom, jak povolit spřažení založené na souborech cookie s aplikační bránou.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: adacd805e736524fd7956c4bbc0ad402980b4cd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795977"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Povolení spřažení založené na souborech cookie pomocí aplikační brány
Jak je uvedeno v [dokumentaci k aplikační bráně Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings), aplikační brána podporuje spřažení založenou na souborech cookie, což znamená, že může směrovat následný provoz z relace uživatele na stejný server pro zpracování.

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
