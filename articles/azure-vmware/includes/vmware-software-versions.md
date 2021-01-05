---
title: Verze softwaru VMware
description: Podporované verze softwaru VMware pro řešení Azure VMware.
ms.topic: include
ms.date: 12/29/2020
ms.openlocfilehash: c6ba2904bab6c6f44001cafed1bd4cbdeb786373
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825076"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


Aktuální verze softwaru softwaru VMware používané v clusterech privátního cloudu řešení Azure VMware jsou:

| Software              |    Verze   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6,7 U3    | 
| ESXi                  |    6,7 U3    | 
| Síti vSAN                  |    6,7 U3    |
| NSX-T                 |      2.5     |


>[!NOTE]
>NSX-T je jediná podporovaná verze NSX.

Pro všechny nové clustery v privátním cloudu odpovídá verze softwaru, co je aktuálně spuštěná. Pro všechny nové privátní cloudy v rámci předplatného se nainstaluje nejnovější verze softwarového zásobníku. Další informace najdete v tématu [požadavky na verzi softwaru VMware](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

Upgrady sady prostředků privátního cloudu udržují software v rámci jedné verze nejnovější verze softwarového balíčku od VMware. Verze privátního cloudového softwaru se mohou lišit od nejaktuálnější verze jednotlivých softwarových komponent (ESXi, NSX-T, vCenter, síti vSAN). Obecné zásady a procesy upgradu pro software platformy řešení Azure VMware, který je popsaný v tématu [aktualizace a upgrady privátního cloudu](../concepts-upgrades.md), najdete v tématu.

