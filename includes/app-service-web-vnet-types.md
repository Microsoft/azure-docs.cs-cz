---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: da43e639779dd4946cb014be777e3a6db0366e24
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671529"
---
* Víceklientské systémy, které podporují celou škálu cenových plánů s výjimkou izolovaného
* App Service Environment (pomocného mechanismu), který se nasadí do vaší virtuální sítě a podporuje aplikace izolovaného cenového plánu

Tento dokument projde funkcí integrace virtuální sítě, která je určena pro použití v aplikacích s více klienty. Pokud je vaše aplikace v [App Service Environment][ASEintro], pak už je ve virtuální síti a nevyžaduje použití funkce integrace virtuální sítě k přístupu k prostředkům ve stejné virtuální síti. Podrobnosti o všech funkcích sítě najdete v článku [funkce App Service sítě][Networkingfeatures] .

Integrace virtuální sítě poskytuje vaší aplikaci přístup k prostředkům ve vaší virtuální síti, ale neuděluje příchozímu privátnímu přístupu do vaší aplikace z virtuální sítě. Přístup k privátnímu webu znamená, že aplikace je přístupná jenom z privátní sítě, jako je třeba z Azure Virtual Network. Integrace virtuální sítě je jenom pro odchozí volání z vaší aplikace do vaší virtuální sítě. Funkce integrace virtuální sítě se chová jinak, když se používá s virtuální sítě ve stejné oblasti a v virtuální sítě v jiných oblastech. Funkce integrace virtuální sítě má dvě variace.

* Místní integrace virtuální sítě – při připojování k Správce prostředků virtuální sítě ve stejné oblasti musíte mít ve virtuální síti, ve které se integruje, vyhrazenou podsíť. 
* Brána vyžaduje integraci virtuální sítě – při připojování k virtuální sítě v jiných oblastech nebo v klasické virtuální síti ve stejné oblasti potřebujete bránu Virtual Network zřízenou v cílové virtuální síti VNet.

Funkce integrace virtuální sítě:

* vyžadování cenového plánu Standard, Premium, PremiumV2 nebo elastické Premium 
* Podpora protokolů TCP a UDP
* práce s aplikacemi App Service a aplikacemi funkcí

Integrace virtuální sítě nepodporuje zahrnutí následujících věcí:

* připojení jednotky
* Integrace služby AD 
* NetBIOS

Požadovaná integrace virtuální sítě pro bránu poskytuje přístup k prostředkům v cílové virtuální síti nebo v sítích připojených k cílové virtuální síti s partnerským vztahem nebo VPN. Požadovaná integrace virtuální sítě brány nepovoluje přístup k prostředkům dostupným napříč ExpressRoute připojeními nebo pracuje s koncovými body služby. 

Bez ohledu na použitou verzi poskytuje integrace virtuální sítě přístup k prostředkům ve vaší virtuální síti, ale neuděluje příchozímu privátnímu přístupu do vaší aplikace z virtuální sítě. Přístup k soukromému webu znamená, že aplikace je přístupná jenom z privátní sítě, jako je například z Azure Virtual Network. Integrace virtuální sítě je jenom pro odchozí volání z vaší aplikace do vaší virtuální sítě. 

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features