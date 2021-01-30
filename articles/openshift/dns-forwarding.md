---
title: Konfigurace předávání DNS pro Azure Red Hat OpenShift 4
description: Konfigurace předávání DNS pro Azure Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: aa18959d0dc78dedf8b57dc120ab6744afa9051f
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070589"
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