---
title: Konfigurace předávání DNS pro Azure Red Hat OpenShift 4
description: Konfigurace předávání DNS pro Azure Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 6784c69c049945f3bd4977ecd647d57ec59bf9f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82232628"
---
# <a name="configure-dns-forwarding-on-an-azure-red-hat-openshift-4-cluster"></a>Konfigurace předávání DNS na clusteru Azure Red Hat OpenShift 4

Pokud chcete nakonfigurovat předávání DNS v clusteru Azure Red Hat OpenShift, budete muset změnit operátor DNS. Tato úprava umožní, aby vaše aplikace v luskech běžely v rámci clusteru, aby přeložily názvy hostované na privátním serveru DNS mimo cluster. Tyto kroky jsou popsané pro OpenShift 4,3 [zde](https://docs.openshift.com/container-platform/4.3/networking/dns-operator.html).

Pokud například chcete přeposláním všech požadavků DNS na adresu *. example.com přeložit serverem DNS 192.168.100.10, můžete konfiguraci operátora upravit spuštěním:
 
```bash
oc edit dns.operator/default
```
 
Tím se spustí editor a můžete nahradit `spec: {}` :
 
 ```yaml
spec:
  servers:
  - forwardPlugin:
      upstreams:
      - 192.168.100.10
    name: example-dns
    zones:
    - example.com
```

Uložte soubor a ukončete Editor.

## <a name="next-steps"></a>Další kroky
Další informace o předávání DNS pro OpenShift 4,3 [najdete tady](https://docs.openshift.com/container-platform/4.3/networking/dns-operator.html).