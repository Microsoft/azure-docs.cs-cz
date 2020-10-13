---
title: Koncepty – API Management
description: Přečtěte si, jak API Management chránit rozhraní API spuštěná na virtuálních počítačích řešení Azure VMware (VM).
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 346d0f795c3d19b115ced771991263cce2104217
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91262973"
---
# <a name="api-management-to-publish-and-protect-apis-running-on-azure-vmware-solution-based-vms"></a>API Management k publikování a ochraně rozhraní API běžících na virtuálních počítačích založených na řešení Azure VMware

Microsoft Azure [API Management](https://azure.microsoft.com/services/api-management/) umožňuje vývojářům a týmům v DevOps bezpečně publikovat buď pro interní, nebo externí uživatele.

I když je tato možnost nabízena v několika SKU, umožňuje integraci se službou Azure Virtual Network jenom k publikování rozhraní API spuštěných v úlohách řešení Azure VMware. Tyto dvě SKU bezpečně umožňují připojení mezi službou API Management a back-endu. SKU pro vývojáře je určeno pro vývoj a testování, zatímco skladové položky Premium jsou určené pro produkční nasazení.

V případě back-end služeb, které běží na virtuálních počítačích řešení Azure VMware, je konfigurace v API Management ve výchozím nastavení stejná jako u místních back-end služeb. U interních i externích nasazení API Management konfiguruje virtuální IP (VIP) Nástroj pro vyrovnávání zatížení jako koncový bod back-endu, když je back-end server umístěný za Load Balancer NSX na straně řešení Azure VMware.

## <a name="external-deployment"></a>Externí nasazení

Externí nasazení publikuje rozhraní API spotřebovaná externími uživateli pomocí veřejného koncového bodu. Vývojáři a DevOps technici mohou spravovat rozhraní API prostřednictvím Azure Portal nebo PowerShellu a portálu pro vývojáře API Management.

Diagram externího nasazení zobrazuje celý proces a příslušné aktéry (zobrazené v horní části). Objekty actor jsou:

- **Správci:** Představuje tým správce nebo DevOps, který spravuje řešení Azure VMware prostřednictvím mechanismů Azure Portal a automatizace, jako je PowerShell nebo Azure DevOps.

- **Uživatelé:**  Představuje uživatele vystavených rozhraní API a představuje uživatele i služby, které rozhraní API využívají.

Tok přenosů prochází API Management instance, která vyabstrakce back-end služby, připojenou k rozbočovači virtuální sítě. Brána ExpressRoute směruje provoz do ExpressRoute Global Reach kanál a dosáhne NSX Load Balancer distribuce příchozího provozu do různých instancí back-end služby.

API Management má veřejné rozhraní API Azure a doporučuje se aktivovat službu Azure DDOS Protection. 

:::image type="content" source="media/api-management/external-deployment.png" alt-text="Externí nasazení – API Management pro řešení Azure VMware":::


## <a name="internal-deployment"></a>Interní nasazení

Interní nasazení publikuje rozhraní API spotřebovaná interními uživateli nebo systémy. DevOps tým a rozhraní API pro vývojáře používají stejné nástroje pro správu a portál pro vývojáře jako v externím nasazení.

Interní nasazení můžou být [s Azure Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md) k vytvoření veřejného a zabezpečeného koncového bodu pro rozhraní API s využitím jeho schopností a vytvořením hybridního nasazení, které umožňuje různé scénáře.  Rozhraní API využívá své schopnosti a vytváří hybridní nasazení, které umožňuje různé scénáře.

* Použijte stejný prostředek API Management pro využití interních i externích uživatelů.

* Mít jeden API Management prostředek s podmnožinou rozhraní API definovanou a dostupnou pro externí uživatele.

* Poskytněte snadný způsob, jak přepínat přístup k API Managementům z veřejného Internetu.

Níže uvedený diagram nasazení znázorňuje uživatele, kteří můžou být interní nebo externí, přičemž každý typ přistupuje ke stejným nebo jiným rozhraním API.

V interním nasazení se rozhraní API zveřejňují na stejnou instanci API Management. Před API Management se Application Gateway nasazeny s aktivovanou funkcí WAF (Azure Web Application firewall) a sadou naslouchacího procesu HTTP a pravidel pro filtrování provozu, což zveřejňuje jenom podmnožinu back-end služeb spuštěných v řešení Azure VMware.

* Interní provoz se směruje prostřednictvím brány ExpressRoute, aby se Azure Firewall, a pak API Management, jestli jsou pravidla pro přenos dat navázána nebo přímo API Management.  

* Externí provoz vstoupí do Azure prostřednictvím Application Gateway, která používá vrstvu externí ochrany pro API Management.


:::image type="content" source="media/api-management/internal-deployment.png" alt-text="Externí nasazení – API Management pro řešení Azure VMware":::