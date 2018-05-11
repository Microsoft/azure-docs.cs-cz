---
title: Verze API poskytovatele prostředků podporované profily v zásobníku Azure | Microsoft Docs
description: Další informace o verzi Azure Resource Manager podporuje profily v zásobníku Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: ee4321b905396f78e7dad9248b9e377dad250a13
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>Verze API poskytovatele prostředků podporované profily v Azure zásobníku

Můžete najít poskytovatele prostředků a čísla verzí pro každý profil API používá zásobník Azure v tomto článku. V tabulkách v tomto článku jsou uvedeny verze podporované pro každý poskytovatel prostředků a rozhraní API verze profilů. Každý poskytovatel prostředků obsahuje sadu prostředků typy a počty konkrétní verzi.

Profil rozhraní API používá tři zásady vytváření názvů:
 - nejnovější
 - Rrrr mm-dd hybridní
 - rrrr mm-dd profil

Vysvětlení profilů rozhraní API a cadence vydání verze pro zásobník Azure najdete v tématu [profily verze spravovat rozhraní API v Azure zásobníku](azure-stack-version-profiles.md).

> [!Note]  
> **Nejnovější** rozhraní API profil obsahuje nejnovější verzi API poskytovatele prostředků a není uvedené v tomto článku.

## <a name="overview-of-2018--03-01-hybrid"></a>Přehled 2018 – 03-01hybridní

| Poskytovatel prostředků | verze rozhraní API. |
|-----------------------------------------------|-----------------------------------------------------|
| Microsoft.Compute | 2017-03-30 |
| Microsoft.Network | 2017-10-01<br>Brána sítě VPN bude 2017-03-01 |
| Microsoft.Storage (datové roviny) | 2017-04-17 |
| Microsoft.Storage (rovině řízení) | 2016-01-01 |
| Společnosti Microsoft. Web | 2016-08-01<br>což je poslední (od teď) v Azure |
| Microsoft.KeyVault | 2016-10-01 (není změna) |
| Microsoft.Resources (Azure Resource Manager sám sebe) | 2016-02-01 |
| Microsoft.Authorization (zásad operace) | 2015-11-01 |
| Microsoft.Insights | 2015-11-01 |
| Microsoft.Keyvault | 2016-10-01 |
| Zásada | 2016-10-01 |
| Prostředky | 2016-10-01 |
| Resources_Links | 2016-10-01 |
| Resources_Locks | 2016-10-01 |
| Odběry | 2016-10-01 |

Více seznam verzí pro každý typ prostředku pro zprostředkovatele v profilu rozhraní api najdete v tématu [podrobnosti 2018-03-01hybridní](#details-for-the-2018-03-01-hybrid) profilu.

## <a name="overview-of-2017-03-09-profile"></a>Přehled 2017-03-09-profilu

| Poskytovatel prostředků | verze rozhraní API. |
|------------------------------------------------|------------------------------|
| Microsoft.Compute | 2016-03-30 |
| Microsoft.Network | 2015-06-15 |
| Microsoft.Storage (datové roviny) | 2015-04-05  |
| Microsoft.Storage (rovině řízení) | 2016-01-01   |
| Microsoft.Websites | 2016-01-01 |
| Microsoft.KeyVault | 2016-10-01<br>(Není změna) |
| Microsoft.Resources<br>(Azure Resource Manager sám sebe) | 2016-02-01 |
| Microsoft.Authorization<Br>(zásad operace) | 2015-11-01 |
| Microsoft.Insights | 2015-11-01 |
| Microsoft.Keyvault | 2016-10-01 |
| Zásada | 2015-10-01-preview |
| Prostředky | 2016-02-01 |
| Resources_Links | 2016-09-01 |
| Resources_Locks | 2016-09-01 |
| Odběry | 2016-06-1 |

Více seznam verzí pro každý typ prostředku pro zprostředkovatele v profilu rozhraní api najdete v tématu [podrobnosti 2017-03-09profil](#details-for-the-2017-03-09-profile)

## <a name="details-for-the-2018-03-01-hybrid"></a>Podrobnosti o 2018-03-01hybridní

### <a name="microsoftauthorization"></a>Microsoft.Authorization

Řízení přístupu na základě rolí používáte ke správě akce, které mohou uživatelé ve vaší organizaci provádět na prostředky. Tato sada operací umožňuje definovat role, přiřaďte role uživatele nebo skupiny a získat informace o oprávněních. Další informace najdete v tématu [autorizace](https://docs.microsoft.com/rest/api/authorization/).

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
| Poskytovatele delegovanou odběrů | 2015-06-01 - preview |
| Delegované využití agregace | 2015-06-01 - preview |
| Tráví odhad prostředků | 2015-06-01 – náhled |
| Operace | 2015-06-01 - preview |
| Odběratel využití agregace | 2015-06-01 - preview |
| Agregace využití | 2015-06-01 - preview |

### <a name="microsoftcompute"></a>Microsoft.Compute

Rozhraní API Azure výpočetní zajišťují programový přístup k virtuální počítače a jejich doprovodné materiály. Další informace najdete v tématu [Azure Compute](https://docs.microsoft.com/rest/api/compute/).

| Typ prostředku | Verze rozhraní API |
|---------------------------------------------------------------|-------------|
| Sady dostupnosti | 2016-03-30 |
| Umístění | 2016-03-30 |
| Umístění nebo operací | 2016-03-30 |
| Umístění nebo vydavatelů | 2016-03-30 |
| Umístění nebo použití | 2016-03-30 |
| Umístění nebo vmSizes | 2016-03-30 |
| Operace | 2016-03-30 |
| Virtual Machines | 2016-03-30 |
| Virtuální počítače nebo rozšíření | 2016-03-30 |
| Virtual Machine Scale Sets | 2016-03-30 |
| Sady škálování virtuálních počítačů nebo rozšíření | 2016-03-30 |
| Nastaví nebo síťových rozhraní škálování virtuálních počítačů | 2016-03-30 |
| Nastaví a virtuálních počítačů škálování virtuálního počítače | 2016-03-30 |
| Škálování virtuálních počítačů sady nebo virtuálních počítačů nebo networkInterfaces | 2016-03-30 |

### <a name="microsoftgallery"></a>Microsoft.Gallery

| Typ prostředku | Verze rozhraní API |
|------------------|-------------|
| Kurátorování | 2015-04-01 |
| Obsah kurátorování | 2015-04-01 |
| Extrakt kurátorování | 2015-04-01 |
| Položky galerie | 2015-04-01 |
| Operace | 2015-04-01 |
| Portál | 2015-04-01 |
| Hledat | 2015-04-01 |
| Navrhnout | 2015-04-01 |

### <a name="microsoftinsights"></a>Microsoft.Insights

| Typy prostředků | Verze rozhraní API |
|--------------------|--------------------|
| Pravidla výstrah | 2016-03-01 |
| Kategorie události | 2017-03-01-preview |
| Typy událostí | 2017-03-01-preview |
| Definice metrik | 2016-03-01 |
| Operace | 2015-04-01 |

### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

Správa vašeho klíče trezory a také klíčů, tajných klíčů a certifikátů v rámci vaší trezorů klíčů. Další informace najdete v tématu [REST API služby Azure Key Vault odkaz](https://docs.microsoft.com/rest/api/keyvault/).

| Typy prostředků | Verze rozhraní API |
|-------------------------|--------------|
| Operace | 2016-10-01 |
| trezory | 2016-10-01 |
| Trezory / zásady přístupu | 2016-10-01 |
| Trezory nebo tajných klíčů | 2016-10-01 |

### <a name="microsoftnetwork"></a>Microsoft.Network

Výsledek volání operací je reprezentace k dispozici seznam operací cloudové sítě. Další informace najdete v tématu [operace REST API](https://docs.microsoft.com/rest/api/operation/).

| Typy prostředků | Verze rozhraní API |
|---------------------------|--------------|
| Připojení | 2015-06-15 |
| Zóny DNS | 2016-04-01 |
| Služby vyrovnávání zatížení | 2015-06-15 |
| Brána místní sítě | 2015-06-15 |
| Umístění | 2016-04-01 |
| Umístění nebo operationResults | 2016-04-01 |
| Umístění nebo operací | 2016-04-01 |
| Umístění nebo použití | 2016-04-01 |
| Síťová rozhraní | 2015-06-15 |
| Network Security Groups (Skupiny zabezpečení sítě) | 2015-06-15 |
| Operace | 2015-06-15 |
| Veřejná IP adresa | 2015-06-15 |
| Směrovací tabulky | 2015-06-15 |
| Brána virtuální sítě | 2015-06-15 |
| Virtuální sítě | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

Azure Resource Manager umožňuje nasadit a spravovat infrastrukturu pro vaše řešení Azure. Uspořádání související prostředky ve skupinách prostředků a nasazení vašich prostředků pomocí šablony JSON. Úvod do nasazení a Správa prostředků pomocí Resource Manageru, najdete v části [přehled Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

| Typy prostředků | Verze rozhraní API |
|-----------------------------------------|-------------------|
| Registrace aplikace | 2015-01-01 |
| Zkontrolovat název prostředku | 2015-012016-09-01 |
| Delegované zprostředkovatelů | 2015-01-01 |
| Delegované poskytovatelů nebo nabídky | 2015-01-01 |
| DelegatedProviders/offers/estimatePrice | 2015-01-01 |
| Nasazení | 2016-0209-01 |
| Nasazení/operace | 2016-0209-01 |
| Rozšíření metadat | 2015-01-01 |
| Odkazy | 2015-012016-09-01 |
| Umístění | 2015-01-01 |
| Nabídky | 2015-01-01 |
| Operace | 2015-01-01 |
| Zprostředkovatelé | 2015-012017-08-01 |
| Skupiny prostředků | 2015-012016-09-01 |
| Prostředky | 2015-012016-09-01 |
| Odběry | 2015-012016-09-01 |
| Odběry nebo umístění | 2015-012016-09-01 |
| Výsledky odběry/operaci | 2015-012016-09-01 |
| Odběry/zprostředkovatelů | 2015-012017-08-01 |
| Odběry/skupiny zdrojů | 2015-012016-09-01 |
| Odběry, Skupinyprostředků nebo prostředky | 2015-012016-09-01 |
| Odběry nebo prostředky | 2015-012016-09-01 |
| Odběry/tagNames | 2016-0609-01 |
| Odběry, tagNames/tagValues | 2016-0609-01 |
| Klienti | 2015-012017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage 

Zprostředkovatel prostředků úložiště (SRP) umožňuje spravovat váš účet úložiště a klíče prostřednictvím kódu programu. Další informace najdete v tématu [referenci rozhraní API REST zprostředkovatele prostředků pro úložiště Azure](https://docs.microsoft.com/rest/api/storagerp/).

| Typy prostředků | Verze rozhraní API |
|-------------------------|--------------|
| Ověřit dostupnost názvu | 2016-01-01 |
| Umístění | 2016-01-01 |
| Umístění nebo kvóty | 2016-01-01 |
| Operace | 2016-01-01 |
| StorageAccounts | 2016-01-01 |
| Použití | 2016-01-01 |

## <a name="details-for-the-2017-03-09-profile"></a>Podrobnosti o 2017-03-09profil

### <a name="microsoft-authorization"></a>Microsoft Authorization

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
| Sady dostupnosti | 2016-03-30 |
| Umístění | 2016-03-30 |
| Umístění nebo operací | 2016-03-30 |
| Umístění nebo vydavatelů | 2016-03-30 |
| Umístění nebo použití | 2016-03-30 |
| Umístění nebo vmSizes | 2016-03-30 |
| Operace | 2016-03-30 |
| Virtual Machines | 2016-03-30 |
| Virtuální počítače nebo rozšíření | 2016-03-30 |
| Virtual Machine Scale Sets | 2016-03-30 |
| Sady škálování virtuálních počítačů nebo rozšíření | 2016-03-30 |
| Nastaví nebo síťových rozhraní škálování virtuálních počítačů | 2016-03-30 |
| Nastaví a virtuálních počítačů škálování virtuálního počítače | 2016-03-30 |
| Škálování virtuálních počítačů sady nebo virtuálních počítačů nebo networkInterfaces | 2016-03-30 |

### <a name="microsoftnetwork"></a>Microsoft.Network

| Typy prostředků | Verze rozhraní API |
|---------------------------|--------------|
| Připojení | 2015-06-15 |
| Zóny DNS | 2016-04-01 |
| Služby vyrovnávání zatížení | 2015-06-15 |
| Brána místní sítě | 2015-06-15 |
| Umístění | 2016-04-01 |
| Umístění nebo operationResults | 2016-04-01 |
| Umístění nebo operací | 2016-04-01 |
| Umístění nebo použití | 2016-04-01 |
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
| Registrace aplikace | 2015-01-01 |
| Zkontrolovat název prostředku | 2016-09-01 |
| Delegované zprostředkovatelů | 2015-01-01 |
| Delegované poskytovatelů nebo nabídky | 2015-01-01 |
| DelegatedProviders/offers/estimatePrice | 2015-01-01 |
| Nasazení | 2016-09-01 |
| Nasazení/operace | 2016-09-01 |
| Rozšíření metadat | 2015-01-01 |
| Odkazy | 2016-09-01 |
| Umístění | 2015-01-01 |
| Nabídky | 2015-01-01 |
| Operace | 2015-01-01 |
| Zprostředkovatelé | 2017-08-01 |
| Skupiny prostředků | 2016-09-01 |
| Prostředky | 2016-09-01 |
| Odběry | 2016-09-01 |
| Odběry nebo umístění | 2016-09-01 |
| Výsledky odběry/operaci | 2016-09-01 |
| Odběry/zprostředkovatelů | 2017-08-01 |
| Odběry/skupiny zdrojů | 2016-09-01 |
| Odběry, Skupinyprostředků nebo prostředky | 2016-09-01 |
| Odběry nebo prostředky | 2016-09-01 |
| Subscriptiosn/tagNames | 2016-09-01 |
| Odběry, tagNames/tagValues | 2016-09-01 |
| Klienti | 2017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage

| Typy prostředků | Verze rozhraní API |
|-------------------------|--------------|
| Ověřit dostupnost názvu | 2016-01-01 |
| Umístění | 2016-01-01 |
| Umístění nebo kvóty | 2016-01-01 |
| Operace | 2016-01-01 |
| StorageAccounts | 2016-01-01 |
| Použití | 2016-01-01 |

## <a name="next-steps"></a>Další postup

* [Instalace PowerShellu pro Azure Stack](azure-stack-powershell-install.md)
* [Konfigurace prostředí PowerShell Azure zásobník uživatele](azure-stack-powershell-configure-user.md)  
