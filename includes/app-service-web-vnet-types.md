---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: da43e639779dd4946cb014be777e3a6db0366e24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671529"
---
* Systémy s více klienty, které podporují celou škálu cenových plánů s výjimkou izolovaných
* Prostředí služby App Service Environment (ASE), které se nasazuje do vaší virtuální sítě a podporuje aplikace izolovaného cenového plánu

Tento dokument prochází funkcí Integrace virtuální sítě, která je pro použití v aplikacích s více tenanty. Pokud je vaše aplikace v [prostředí Služby app service][ASEintro], pak je už ve virtuální síti a nevyžaduje použití funkce integrace virtuální sítě k dosažení prostředků ve stejné virtuální síti. Podrobnosti o všech síťových funkcích najdete v části [Síťové funkce služby App Service.][Networkingfeatures]

Integrace virtuální sítě poskytuje vaší aplikaci přístup k prostředkům ve vaší virtuální síti, ale neuděluje příchozí soukromý přístup k vaší aplikaci z virtuální sítě. Přístup k privátnímu webu znamená zpřístupnění aplikace jenom z privátní sítě, například z virtuální sítě Azure. Integrace virtuální sítě je jenom pro odchozí volání z vaší aplikace do vaší virtuální sítě. Funkce integrace virtuální sítě se při použití s virtuálními sítěmi ve stejné oblasti a s virtuálními sítěmi v jiných oblastech chová jinak. Funkce integrace virtuální sítě má dvě varianty.

* Regionální integrace virtuální sítě – při připojování k virtuálním sítím Správce prostředků ve stejné oblasti musíte mít vyhrazenou podsíť ve virtuální síti, se kterou integrujete. 
* Brána vyžaduje integraci virtuální sítě – při připojování k virtuálním sítím v jiných oblastech nebo ke klasické virtuální síti ve stejné oblasti potřebujete bránu virtuální sítě zřízenou v cílové virtuální síti.

Funkce integrace virtuální sítě:

* vyžadovat cenový plán Standard, Premium, PremiumV2 nebo Elastic Premium 
* podpora TCP a UDP
* práce s aplikacemi služby App Service a aplikacemi Function

Existují některé věci, které integrace virtuální sítě nepodporuje, včetně:

* montáž pohonu
* Integrace služby AD 
* NetBIOS

Integrace virtuální sítě vyžaduje bránu jenom poskytuje přístup k prostředkům v cílové virtuální síti nebo v sítích připojených k cílové virtuální síti s partnerským vztahem nebo virtuálními sítěmi. Integrace virtuální sítě vyžaduje bránu neumožňuje přístup k prostředkům dostupným v rámci připojení ExpressRoute ani pracuje s koncovými body služby. 

Bez ohledu na použitou verzi umožňuje integrace virtuální sítě vaší aplikaci přístup k prostředkům ve vaší virtuální síti, ale neuděluje příchozí soukromý přístup k vaší aplikaci z virtuální sítě. Přístup k privátnímu webu znamená zpřístupnění vaší aplikace jenom z privátní sítě, například z virtuální sítě Azure. Integrace virtuální sítě je jenom pro odchozí volání z vaší aplikace do vaší virtuální sítě. 

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features