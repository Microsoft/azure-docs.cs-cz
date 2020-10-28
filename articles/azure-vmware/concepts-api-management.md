---
title: Koncepty – API Management
description: Přečtěte si, jak API Management chránit rozhraní API spuštěná na virtuálních počítačích řešení Azure VMware (VM).
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: f412ee81fc77435f2586a31c1bf6f6bdf22c66e2
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92670359"
---
# <a name="api-management-to-publish-and-protect-apis-running-on-azure-vmware-solution-based-vms"></a>API Management k publikování a ochraně rozhraní API běžících na virtuálních počítačích založených na řešení Azure VMware

Microsoft Azure [API Management](https://azure.microsoft.com/services/api-management/) umožňuje bezpečné publikování interních nebo externích uživatelů.  Pouze skladové jednotky pro vývojáře a vývoj umožňují integraci služby Azure Virtual Network publikovat rozhraní API spuštěná v úlohách řešení Azure VMware.  Obě SKU bezpečně umožňují připojení mezi službou API Management a back-endu. 

>[!NOTE]
>SKU pro vývojáře je určeno pro vývoj a testování, zatímco skladové položky Premium jsou určené pro produkční nasazení.

Konfigurace API Management je stejná pro back-endové služby, které běží nad virtuálními počítači řešení Azure VMware a místně. U obou nasazení API Management v nástroji pro vyrovnávání zatížení jako koncový bod back-end nakonfigurovat virtuální IP adresu (VIP), když je back-end server umístěný za Load Balancer NSX v řešení Azure VMware. 


## <a name="external-deployment"></a>Externí nasazení

Externí nasazení publikuje rozhraní API spotřebovaná externími uživateli pomocí veřejného koncového bodu. Vývojáři a DevOps technici mohou spravovat rozhraní API prostřednictvím Azure Portal nebo PowerShellu a portálu pro vývojáře API Management.

Diagram externího nasazení zobrazuje celý proces a příslušné aktéry (zobrazené v horní části). Objekty actor jsou:

- **Správci:** Představuje tým správce nebo DevOps, který spravuje řešení Azure VMware prostřednictvím mechanismů Azure Portal a automatizace, jako je PowerShell nebo Azure DevOps.

- **Uživatelé:**  Představuje vystavená vydaná rozhraní API a představuje uživatele a služby, které rozhraní API spotřebovávají.

Tok přenosů prochází pomocí instance API Management, která vyabstrakce back-end služby, připojenou k virtuální síti centrální sítě. Brána ExpressRoute směruje provoz do kanálu ExpressRoute Global Reach a dosáhne NSX Load Balancer distribuce příchozího provozu do různých instancí služby back-end.

API Management má veřejné rozhraní API Azure a doporučuje se aktivovat službu Azure DDOS Protection. 

:::image type="content" source="media/api-management/external-deployment.png" alt-text="Externí nasazení – API Management pro řešení Azure VMware":::


## <a name="internal-deployment"></a>Interní nasazení

Interní nasazení publikuje rozhraní API spotřebovaná interními uživateli nebo systémy. DevOps tým a rozhraní API pro vývojáře používají stejné nástroje pro správu a portál pro vývojáře jako v externím nasazení.

Interní nasazení se dá provést [pomocí Azure Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md) k vytvoření veřejného a zabezpečeného koncového bodu pro rozhraní API.  Funkce brány se používají k vytvoření hybridního nasazení, které umožňuje různé scénáře.  

* Použijte stejný prostředek API Management pro využití interních i externích uživatelů.

* Mít jeden API Management prostředek s podmnožinou rozhraní API definovanou a dostupnou pro externí uživatele.

* Poskytněte snadný způsob, jak přepínat přístup k API Managementům z veřejného Internetu.

Níže uvedený diagram nasazení znázorňuje uživatele, kteří můžou být interní nebo externí, přičemž každý typ přistupuje ke stejným nebo jiným rozhraním API.

V interním nasazení se rozhraní API zveřejňují na stejnou instanci API Management. Před API Management se Application Gateway nasazeny s aktivovanou schopností firewallu webových aplikací (WAF) Azure. Také nasazeno, sada naslouchací procesů HTTP a pravidla pro filtrování provozu, což zveřejňuje jenom podmnožinu back-end služeb spuštěných v řešení Azure VMware.


* Interní trasy provozu prostřednictvím brány ExpressRoute k Azure Firewall a pak API Management, přímo nebo prostřednictvím pravidel provozu.   

* Externí provoz vstoupí do Azure prostřednictvím Application Gateway, která používá vrstvu externí ochrany pro API Management.


:::image type="content" source="media/api-management/internal-deployment.png" alt-text="Externí nasazení – API Management pro řešení Azure VMware" lightbox="media/api-management/internal-deployment.png":::