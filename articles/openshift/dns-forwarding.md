---
title: Konfigurace předávání DNS pro Azure Red Hat OpenShift 4
description: Konfigurace předávání DNS pro Azure Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 68cc5dbee5b96472d11bdfb3a8bf35d118638dcd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633865"
---
# <a name="configure-dns-forwarding-on-an-azure-red-hat-openshift-4-cluster"></a>Konfigurace předávání DNS na clusteru Azure Red Hat OpenShift 4

Pokud chcete nakonfigurovat předávání DNS v clusteru Azure Red Hat OpenShift, budete muset změnit operátor DNS. Tato úprava umožní, aby vaše aplikace v luskech běžely v rámci clusteru, aby přeložily názvy hostované na privátním serveru DNS mimo cluster. Tyto kroky jsou popsané pro OpenShift 4,6 [zde](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html).

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
Další informace o předávání DNS pro OpenShift 4,6 [najdete tady](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html).