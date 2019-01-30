---
title: Verze rozhraní API poskytovatele prostředků podporované profily ve službě Azure Stack | Dokumentace Microsoftu
description: Přečtěte si o Azure Resource Manageru verze podporuje profily ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 6866c6d208468e53b04ede1b5e596f0ea625c49d
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55248465"
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>Verze rozhraní API poskytovatele prostředků podporované profily ve službě Azure Stack

Vyhledejte poskytovatele prostředků a čísla verze pro každý profil rozhraní API používané ve službě Azure Stack v tomto článku. V tabulkách v tomto článku jsou uvedeny verze podporované pro každý poskytovatel prostředků a rozhraní API verze profilů. Každý poskytovatel prostředků obsahuje sadu typů prostředků a čísla konkrétní verzi.

Profil rozhraní API používá tři zásady vytváření názvů:

 - **nejnovější**
 - **yyyy-mm-dd-hybrid**
 - **yyyy-mm-dd-profile**

Vysvětlení profilů rozhraní API a verze vydávání verzí pro službu Azure Stack najdete v tématu [profilů verzí API spravovat ve službě Azure Stack](azure-stack-version-profiles.md).

> [!Note]  
> **Nejnovější** profil rozhraní API obsahuje nejnovější verzi rozhraní API poskytovatele prostředků a není uvedená v tomto článku.

## <a name="overview-of-2018--03-01-hybrid"></a>Přehled 2018-03-01hybridní

| Poskytovatel prostředků | verze API-version |
|-----------------------------------------------|-----------------------------------------------------|
| Microsoft.Compute | 2017-03-30 |
| Microsoft.Network | 2017-10-01<br>VPN Gateway bude 2017-03-01 |
| Microsoft.Storage (rovina dat) | 2017-04-17 |
| Microsoft.Storage (rovina řízení) | 2016-01-01 |
| Microsoft Web | 2016-08-01<br>což je nejnovější vydání v Azure (od této chvíle) |
| Microsoft.KeyVault | 2016-10-01 (ne změnit) |
| Microsoft.Resources (Azure Resource Manageru samotné) | 2016-02-01 |
| Microsoft.Authorization (operace zásad) | 2015-11-01 |
| Microsoft.Insights | 2015-11-01 |
| Microsoft.Keyvault | 2016-10-01 |
| Zásada | 2016-10-01 |
| Zdroje a prostředky | 2016-10-01 |
| Resources_Links | 2016-10-01 |
| Resources_Locks | 2016-10-01 |
| Předplatná | 2016-10-01 |

Další seznam verzí pro každý typ prostředku pro zprostředkovatele v profilu rozhraní API najdete v tématu [podrobnosti 2018-03-01hybridní](#details-for-the-2018-03-01-hybrid) profilu.

## <a name="overview-of-2018-03-01-hybrid"></a>Přehled 2018-03-01hybridní

| Poskytovatel prostředků | verze API-version |
|------------------------------------------------|------------------------------|
| Microsoft.Compute | 2016-03-30 |
| Microsoft.Network | 2015-06-15 |
| Microsoft.Storage (rovina dat) | 2015-04-05  |
| Microsoft.Storage (rovina řízení) | 2016-01-01   |
| Microsoft.Websites | 2016-01-01 |
| Microsoft.KeyVault | 2016-10-01<br>(Ne změnit) |
| Microsoft.Resources<br>(Azure Resource Manageru samotné) | 2016-02-01 |
| Microsoft.Authorization<Br>(zásady operací) | 2015-11-01 |
| Microsoft.Insights | 2015-11-01 |
| Microsoft.Keyvault | 2016-10-01 |
| Zásada | 2015-10-01-preview |
| Zdroje a prostředky | 2016-02-01 |
| Resources_Links | 2016-09-01 |
| Resources_Locks | 2016-09-01 |
| Předplatná | 2016-06-1 |

Seznam verzí každý typ prostředku pro zprostředkovatele v profilu rozhraní API najdete v další části.

## <a name="details-for-the-2018-03-01-hybrid"></a>Podrobnosti o 2018-03-01hybridní

### <a name="microsoftauthorization"></a>Microsoft.Authorization

Řízení přístupu na základě role umožňuje spravovat akce, které můžou uživatelé ve vaší organizaci provádět s prostředky. Tato sada operací umožňuje definovat role, přiřazovat role uživatelům nebo skupinám a získávat informace o oprávněních. Další informace najdete v tématu [autorizace](/rest/api/authorization/).

| Typy prostředků | Verze rozhraní API |
|---------------------|--------------------|
| Zámky | 2017-04-01 |
| Operace | 2015-07-01 |
| Oprávnění | 2015-07-01 |
| Přiřazení zásad | 2016-12-01 (2017-06-01-preview) |
| Definice zásad | 2016-12-01 |
| Operace poskytovatele | 2015-07-01-preview |
| Přiřazení rolí | 2015-07-01 |
| Definice rolí | 2015-07-01 |

### <a name="microsoftcommerce"></a>Microsoft.Commerce

| Typ prostředku | Verze rozhraní API |
|----------------------------------|----------------------|
| Předplatná delegované poskytovatele | verze 2015-06-01 - preview |
| Agregace delegované využití | verze 2015-06-01 - preview |
| Odhad prostředků výdajů | verze 2015-06-01-preview |
| Operace | verze 2015-06-01 - preview |
| Agregace využití odběratele | verze 2015-06-01 - preview |
| Agregace využití | verze 2015-06-01 - preview |

### <a name="microsoftcompute"></a>Microsoft.Compute

Rozhraní API Azure Compute poskytují programový přístup k virtuálním počítačům a jejich pomocným prostředkům. Další informace najdete v tématu [Azure Compute](/rest/api/compute/).

| Typ prostředku | Verze rozhraní API |
|---------------------------------------------------------------|-------------|
| Skupiny dostupnosti | 2016-03-30 |
| Umístění | 2016-03-30 |
| Umístění/operace | 2016-03-30 |
| Umístění a vydavatele | 2016-03-30 |
| Umístění/využití | 2016-03-30 |
| Umístění/vmSizes | 2016-03-30 |
| Operace | 2016-03-30 |
| Virtuální počítače | 2016-03-30 |
| Virtual Machines/extensions | 2016-03-30 |
| Virtual Machine Scale Sets | 2016-03-30 |
| Virtual Machine Scale Sets/rozšíření | 2016-03-30 |
| Virtual Machine Scale Sets/síťová rozhraní | 2016-03-30 |
| Virtuální počítač Škálovací sady a virtuálních počítačů | 2016-03-30 |
| Virtuální počítače Škálovací sady a virtuálních počítačů/síťová | 2016-03-30 |

### <a name="microsoftgallery"></a>Microsoft.Gallery

| Typ prostředku | Verze rozhraní API |
|------------------|-------------|
| Kurátorování | 2015-04-01 |
| Obsah kurátorování | 2015-04-01 |
| Extrakt kurátorování | 2015-04-01 |
| Položky galerie | 2015-04-01 |
| Operace | 2015-04-01 |
| Portál | 2015-04-01 |
| Search | 2015-04-01 |
| Navrhnout | 2015-04-01 |

### <a name="microsoftinsights"></a>Microsoft.Insights

| Typy prostředků | Verze rozhraní API |
|--------------------|--------------------|
| Operace | 2015-04-01 |
| Typy událostí | 2015-04-01 |
| Kategorie události | 2015-04-01 |
| Definice metrik | 2018-01-01 |
| Metriky | 2018-01-01 |
| Nastavení diagnostiky | 2017-05-01-preview |
| Kategorie nastavení diagnostiky | 2017-05-01-preview |


### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

Správa vašeho klíče trezory klíčů, tajných kódů a certifikátů v trezorech klíčů. Další informace najdete v tématu [REST API služby Azure Key Vault odkaz](/rest/api/keyvault/).

| Typy prostředků | Verze rozhraní API |
|-------------------------|--------------|
| Operace | 2016-10-01 |
| Trezory | 2016-10-01 |
| Trezory Recovery Services nebo zásady přístupu | 2016-10-01 |
| Trezory/tajných klíčů | 2016-10-01 |

### <a name="microsoftnetwork"></a>Microsoft.Network

Výsledek volání operací je reprezentace seznamu dostupných síťových cloudových operací. Další informace najdete v tématu [operace REST API](/rest/api/operation/).

| Typy prostředků | Verze rozhraní API |
|---------------------------|--------------|
| Připojení | 2015-06-15 |
| Zóny DNS | 2016-04-01 |
| Nástroje pro vyrovnávání zatížení | 2015-06-15 |
| Brána místní sítě | 2015-06-15 |
| Umístění | 2016-04-01 |
| Umístění/operationResults | 2016-04-01 |
| Umístění/operace | 2016-04-01 |
| Umístění/využití | 2016-04-01 |
| Síťová rozhraní | 2015-06-15 |
| Network Security Groups (Skupiny zabezpečení sítě) | 2015-06-15 |
| Operace | 2015-06-15 |
| Veřejná IP adresa | 2015-06-15 |
| Směrovací tabulky | 2015-06-15 |
| Brána virtuální sítě | 2015-06-15 |
| Virtuální sítě | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

Azure Resource Manager umožňuje nasadit a spravovat infrastrukturu pro vaše řešení Azure. Uspořádání souvisejících prostředků ve skupinách prostředků a nasazení prostředků pomocí šablon JSON. Úvod k nasazování a správě prostředků pomocí Resource Manageru, najdete v článku [přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md).

| Typy prostředků | Verze rozhraní API |
|-----------------------------------------|-------------------|
| Registrace aplikací | 2015-01-01 |
| Zkontrolovat název prostředku | 2016-09-01 |
| Delegovaní poskytovatelé | 2015-01-01 |
| Delegovaní poskytovatelé/nabídky | 2015-01-01 |
| DelegatedProviders/offers/estimatePrice | 2015-01-01 |
| Nasazení | 2016-09-01 |
| Nasazení/operace | 2016-09-01 |
| Rozšíření metadat | 2015-01-01 |
| Odkazy | 2016-09-01 |
| Umístění | 2015-01-01 |
| Nabídky | 2015-01-01 |
| Operace | 2015-01-01 |
| Poskytovatelé | 2017-08-01 |
| Skupiny prostředků | 2016-09-01 |
| Zdroje a prostředky | 2016-09-01 |
| Předplatná | 2016-09-01 |
| Předplatné/umístění | 2016-09-01 |
| Výsledky předplatná/operace | 2016-09-01 |
| Předplatná a poskytovatelé | 2017-08-01 |
| Předplatných nebo skupinách prostředků | 2016-09-01 |
| Subscriptions/resourceGroups/resources | 2016-09-01 |
| Předplatná a prostředky | 2016-09-01 |
| Subscriptions/tagNames | 2016-09-01 |
| Subscriptions/tagNames/tagValues | 2016-09-01 |
| Tenanti | 2017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage 

Poskytovatel prostředků úložiště (SRP) umožňuje spravovat váš účet úložiště a klíče prostřednictvím kódu programu. Další informace najdete v tématu [odkaz na Azure Storage Resource Provider REST API](/rest/api/storagerp/).

| Typy prostředků | Verze rozhraní API |
|-------------------------|--------------|
| Zkontrolovat dostupnost názvu | 2016-01-01 |
| Umístění | 2016-01-01 |
| Umístění a kvóty | 2016-01-01 |
| Operace | 2016-01-01 |
| StorageAccounts | 2016-01-01 |
| Využití | 2016-01-01 |

## <a name="details-for-the-2018-03-01-hybrid"></a>Podrobnosti o 2018-03-01hybridní

### <a name="microsoft-authorization"></a>Autorizace Microsoft

| Typy prostředků | Verze rozhraní API |
|---------------------|---------------------------------|
| Zámky | 2017-04-01 |
| Operace | 2015-07-01 |
| Oprávnění | 2015-07-01 |
| Přiřazení zásad | 2016-12-01 (2017-06-01-preview) |
| Definice zásad | 2016-12-01 |
| Operace poskytovatele | 2015-07-01-preview |
| Přiřazení rolí | 2015-07-01 |
| Definice rolí | 2015-07-01 |

### <a name="microsoftcompute"></a>Microsoft.Compute

| Typ prostředku | Verze rozhraní API |
|---------------------------------------------------------------|-------------|
| Skupiny dostupnosti | 2016-03-30 |
| Umístění | 2016-03-30 |
| Umístění/operace | 2016-03-30 |
| Umístění a vydavatele | 2016-03-30 |
| Umístění/využití | 2016-03-30 |
| Umístění/vmSizes | 2016-03-30 |
| Operace | 2016-03-30 |
| Virtuální počítače | 2016-03-30 |
| Virtual Machines/extensions | 2016-03-30 |
| Virtual Machine Scale Sets | 2016-03-30 |
| Virtual Machine Scale Sets/rozšíření | 2016-03-30 |
| Virtual Machine Scale Sets/síťová rozhraní | 2016-03-30 |
| Virtuální počítač Škálovací sady a virtuálních počítačů | 2016-03-30 |
| Virtuální počítače Škálovací sady a virtuálních počítačů/síťová | 2016-03-30 |

### <a name="microsoftnetwork"></a>Microsoft.Network

| Typy prostředků | Verze rozhraní API |
|---------------------------|--------------|
| Připojení | 2015-06-15 |
| Zóny DNS | 2016-04-01 |
| Nástroje pro vyrovnávání zatížení | 2015-06-15 |
| Brána místní sítě | 2015-06-15 |
| Umístění | 2016-04-01 |
| Umístění/operationResults | 2016-04-01 |
| Umístění/operace | 2016-04-01 |
| Umístění/využití | 2016-04-01 |
| Síťová rozhraní | 2015-06-15 |
| Network Security Groups (Skupiny zabezpečení sítě) | 2015-06-15 |
| Operace | 2015-06-15 |
| Veřejná IP adresa | 2015-06-15 |
| Směrovací tabulky | 2015-06-15 |
| Brána virtuální sítě | 2015-06-15 |
| Virtuální sítě | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

| Typy prostředků | Verze rozhraní API |
|-----------------------------------------|--------------|
| Registrace aplikací | 2015-01-01 |
| Zkontrolovat název prostředku | 2016-09-01 |
| Delegovaní poskytovatelé | 2015-01-01 |
| Delegovaní poskytovatelé/nabídky | 2015-01-01 |
| DelegatedProviders/offers/estimatePrice | 2015-01-01 |
| Nasazení | 2016-09-01 |
| Nasazení/operace | 2016-09-01 |
| Rozšíření metadat | 2015-01-01 |
| Odkazy | 2016-09-01 |
| Umístění | 2015-01-01 |
| Nabídky | 2015-01-01 |
| Operace | 2015-01-01 |
| Poskytovatelé | 2017-08-01 |
| Skupiny prostředků | 2016-09-01 |
| Zdroje a prostředky | 2016-09-01 |
| Předplatná | 2016-09-01 |
| Předplatné/umístění | 2016-09-01 |
| Výsledky předplatná/operace | 2016-09-01 |
| Předplatná a poskytovatelé | 2017-08-01 |
| Předplatných nebo skupinách prostředků | 2016-09-01 |
| Subscriptions/resourceGroups/resources | 2016-09-01 |
| Předplatná a prostředky | 2016-09-01 |
| Subscriptions/tagNames | 2016-09-01 |
| Subscriptions/tagNames/tagValues | 2016-09-01 |
| Tenanti | 2017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage

| Typy prostředků | Verze rozhraní API |
|-------------------------|--------------|
| Zkontrolovat dostupnost názvu | 2016-01-01 |
| Umístění | 2016-01-01 |
| Umístění a kvóty | 2016-01-01 |
| Operace | 2016-01-01 |
| StorageAccounts | 2016-01-01 |
| Využití | 2016-01-01 |

## <a name="next-steps"></a>Další postup

* [Instalace PowerShellu pro Azure Stack](azure-stack-powershell-install.md)
* [Konfigurace prostředí PowerShell uživatele Azure stacku](azure-stack-powershell-configure-user.md)  
