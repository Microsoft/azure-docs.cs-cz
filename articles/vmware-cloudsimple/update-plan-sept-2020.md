---
title: Aktualizace řešení Azure VMware od CloudSimple. září 2020
description: V tomto článku se dozvíte, co očekávat během této operace údržby a změny v privátním cloudu.
author: dikamath
ms.author: dikamath
ms.date: 09/3/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
manager: dikamath
ms.openlocfilehash: ae9c1ba5259e95ed030d7099e5dafe2d4f7935b4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89447854"
---
# <a name="azure-vmware-solution-by-cloudsimple-september-2020-update"></a>Aktualizace řešení Azure VMware od CloudSimple. září 2020

Důležitá aktualizace služby řešení Azure VMware se provede v září. E-mailové oznámení, které se odešle jako součást údržby, bude obsahovat časovou osu údržby. V tomto článku se dozvíte, co očekávat během této operace údržby a změny v privátním cloudu.

> [!NOTE]
> Toto je upgrade bez rušivého narušování. Během upgradu se může zobrazit jedna z redundantních komponent.

## <a name="vmware-infrastructure-upgrade"></a>Upgrade infrastruktury VMware

Infrastruktura VMware vašeho privátního cloudu se aktualizuje na novější verzi. Patří sem aktualizace komponent vCenter, ESXi, NSX-T a hybridního cloudového rozšíření (HCX) (pokud jsou nasazené) vašeho privátního cloudu.

Během upgradu se do vašeho privátního cloudu přidá nový uzel před tím, než se uzel zařadí do režimu údržby pro operaci upgradu. Tím se zajistí, že se během procesu upgradu zachová kapacita vašeho privátního cloudu a dostupnost vašeho privátního cloudu. Během upgradu komponent VMware se můžou zobrazit budíky zobrazené na vašem webovém uživatelském rozhraní vCenter. Alarmy jsou součástí operací údržby prováděných týmem provozu služby.

**Verze součásti**

- ESXi 6.7 U3
- vCenter 6.7
- Síti vSAN 6,7
- NSX datové centrum 2.5.1
- HCX 3.5.2

## <a name="datacenter-updates"></a>Aktualizace datového centra

Tato aktualizace zahrnuje aktualizace infrastruktury Datacenter. V průběhu období údržby budou provedeny nerušivé aktualizace. Během procesu aktualizace si všimnete, že dojde ke snížení redundance. Výstrahy můžou být vygenerovány v infrastruktuře privátního cloudu VMware, okruhy ExpressRoute, připojení GlobalReach a všech zařízeních VPN typu Site-to-site, která souvisí s jedním z virtuálních počítačů s nákupem. To je v průběhu aktualizace normální, protože komponenty budou v rámci aktualizace restartovány.

-   Je-li síť VPN typu Site-to-site nasazena jako jediná instance (ne HA), bude pravděpodobně nutné znovu vytvořit připojení k síti VPN.

-   Pokud používáte připojení VPN typu Point-to-site, bude nutné znovu vytvořit připojení k síti VPN.

## <a name="post-update"></a>Po aktualizaci

Po dokončení aktualizací by se měly zobrazit novější verze komponent VMware. Pokud si všimnete jakýchkoli problémů nebo máte nějaké dotazy, obraťte se na náš tým podpory otevřením [lístku podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
