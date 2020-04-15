---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 04/15/2020
ms.author: ccompy
ms.openlocfilehash: c31a5aaa9866a4ce97cd3cd59a8e363834f70587
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312846"
---
* Víceklientské systémy, které podporují celou řadu cenových plánů s výjimkou izolované.
* Prostředí služby App Service Environment, které se nasazuje do vaší virtuální sítě a podporuje aplikace izolovaného cenového plánu.

Funkce integrace virtuální sítě se používá ve víceklientských aplikacích. Pokud je vaše aplikace v [prostředí Služby app service][ASEintro], pak je už ve virtuální síti a nevyžaduje použití funkce integrace virtuální sítě k dosažení prostředků ve stejné virtuální síti. Další informace o všech síťových funkcích naleznete v tématu [Síťové funkce služby App Service][Networkingfeatures].

Integrace virtuální sítě poskytuje vaší aplikaci přístup k prostředkům ve vaší virtuální síti, ale neuděluje příchozí soukromý přístup k vaší aplikaci z virtuální sítě. Přístup k privátnímu webu znamená zpřístupnění aplikace jenom z privátní sítě, například z virtuální sítě Azure. Integrace virtuální sítě se používá jenom k odchozím voláním z vaší aplikace do vaší virtuální sítě. Funkce integrace virtuální sítě se chová jinak, když se používá s virtuální sítí ve stejné oblasti a s virtuální sítí v jiných oblastech. Funkce integrace virtuální sítě má dvě varianty:

* **Regionální integrace virtuální sítě:** Když se připojíte k virtuálním sítím Azure Resource Manager ve stejné oblasti, musíte mít vyhrazenou podsíť ve virtuální síti, se kterou integrujete.
* **Integrace virtuální sítě požadovaná bránou**: Když se připojíte k virtuální síti v jiných oblastech nebo ke klasické virtuální síti ve stejné oblasti, potřebujete bránu virtuální sítě Azure zřízenou v cílové virtuální síti.

Funkce integrace virtuální sítě:

* Vyžadovat cenový plán Standard, Premium, PremiumV2 nebo Elastic Premium.
* Podpora Protokolů TCP a UDP.
* Pracujte s aplikacemi azure app service a funkčními aplikacemi.

Existují některé věci, které integrace virtuální sítě nepodporuje, jako například:

* Montáž pohonu.
* Integrace služby Active Directory.
* Netbios.

Integrace virtuální sítě vyžadované bránou poskytuje přístup k prostředkům jenom v cílové virtuální síti nebo v sítích připojených k cílové virtuální síti s partnerským vztahem nebo virtuálními sítěmi. Integrace virtuální sítě vyžadované bránou neumožňuje přístup k prostředkům dostupným napříč připojeními Azure ExpressRoute ani pracuje s koncovými body služby.

Bez ohledu na použitou verzi umožňuje integrace virtuální sítě vaší aplikaci přístup k prostředkům ve vaší virtuální síti, ale neuděluje příchozí soukromý přístup k vaší aplikaci z virtuální sítě. Přístup k privátnímu webu znamená zpřístupnění vaší aplikace jenom z privátní sítě, například z virtuální sítě Azure. Integrace virtuální sítě je jenom pro odchozí volání z vaší aplikace do vaší virtuální sítě.

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
