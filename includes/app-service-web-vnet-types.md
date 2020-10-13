---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/01/2020
ms.author: ccompy
ms.openlocfilehash: 481bd4f50eb527bcad2ba79b5ba4b9df4b872bfc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91739900"
---
* Víceklientské systémy, které podporují celou škálu cenových plánů s výjimkou izolovaného.
* App Service Environment, který se nasadí do vaší virtuální sítě a podporuje aplikace izolovaného cenového plánu.

Funkce integrace virtuální sítě se používá ve víceklientské aplikacích. Pokud je vaše aplikace v [App Service Environment][ASEintro], pak už je ve virtuální síti a nevyžaduje použití funkce integrace virtuální sítě k přístupu k prostředkům ve stejné virtuální síti. Další informace o všech funkcích sítě najdete v tématu [App Service síťové funkce][Networkingfeatures].

Integrace virtuální sítě poskytuje vaší aplikaci přístup k prostředkům ve vaší virtuální síti, ale neuděluje příchozímu privátnímu přístupu do vaší aplikace z virtuální sítě. Přístup k privátnímu webu znamená, že aplikace je přístupná jenom z privátní sítě, například z Azure Virtual Network. Integrace virtuální sítě se používá jenom pro odchozí volání z vaší aplikace do vaší virtuální sítě. Funkce integrace virtuální sítě se chová odlišně, když se používá u virtuální sítě ve stejné oblasti a virtuální síti v jiných oblastech. Funkce integrace virtuální sítě má dvě varianty:

* **Místní integrace virtuální**sítě: když se připojíte k Azure Resource Manager virtuální sítě ve stejné oblasti, musíte mít ve virtuální síti, ve které se integruje, vyhrazenou podsíť.
* **Brána – požadovaná integrace virtuální**sítě: když se připojíte k virtuální síti v jiných oblastech nebo k klasické virtuální síti ve stejné oblasti, budete potřebovat bránu Azure Virtual Network zřízenou v cílové síti VNET.

Funkce integrace virtuální sítě:

* Vyžadovat cenové tarify Standard, Premium, PremiumV2, PremiumV3 nebo elastické Premium
* Podporují protokoly TCP a UDP.
* Pracujte s Azure App Service aplikacemi a aplikacemi Function App.

Integrace virtuální sítě nepodporují, třeba:

* Připojování jednotky.
* Integrace služby Active Directory.
* Názv.

Brána – požadovaná integrace virtuální sítě poskytuje přístup k prostředkům jenom v cílové virtuální síti nebo v sítích připojených k cílové síti VNet s partnerským vztahem nebo VPN. Brána – požadovaná integrace virtuální sítě nepovoluje přístup k prostředkům dostupným v rámci připojení Azure ExpressRoute nebo funguje s koncovými body služby.

Bez ohledu na použitou verzi poskytuje integrace virtuální sítě přístup k prostředkům ve vaší virtuální síti, ale neuděluje příchozímu privátnímu přístupu do vaší aplikace z virtuální sítě. Přístup k soukromému webu znamená, že vaše aplikace bude přístupná jenom z privátní sítě, například z virtuální sítě Azure. Integrace virtuální sítě je jenom pro odchozí volání z vaší aplikace do vaší virtuální sítě.

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
