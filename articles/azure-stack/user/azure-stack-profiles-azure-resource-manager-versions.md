---
title: Verze API poskytovatele prostředků podporované profily v zásobníku Azure | Microsoft Docs
description: Další informace o verzi Azure Resource Manager podporuje profily v zásobníku Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 2E21C8DE-D540-4C1C-A0EF-1B7125DB7A6E
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 8cc5ce1ec113df7ce92c54022dbe1b6219c8c235
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>Verze API poskytovatele prostředků podporované profily v Azure zásobníku

Poskytovatele prostředků Azure poskytuje prostředky, které můžete nasadit a spravovat prostřednictvím Správce Azure Resource Manager. Každý poskytovatel nabízí operací pro práci s prostředky. Některé běžné zprostředkovatelé prostředků zahrnují Microsoft.Compute, který poskytuje virtuální počítače, Microsoft.Storage, který poskytuje prostředků účtu úložiště, a Microsoft.Web, který poskytuje zdroje informací související s webovými aplikacemi. Další informace najdete v tématu [Zprostředkovatelé a typy prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services).

Následující tabulka pro každý poskytovatel prostředků uvádí podporovanou verzi verze rozhraní API pro Azure zásobníku při použití profilů.

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

Rozhraní API Azure výpočetní zajišťují programový přístup k virtuální počítače a jejich doprovodné materiály. Další informace najdete v tématu [Azure Compute](https://docs.microsoft.com/en-us/rest/api/compute/).

| Typ prostředku | Verze rozhraní API |
|---------------------------------------------------------------|-------------|
| Skupiny dostupnosti | 2016-03-30 |
| Umístění | 2016-03-30 |
| Umístění nebo operací | 2016-03-30 |
| Umístění nebo vydavatelů | 2016-03-30 |
| Umístění nebo použití | 2016-03-30 |
| Umístění nebo vmSizes | 2016-03-30 |
| Operace | 2016-03-30 |
| Virtuální počítače | 2016-03-30 |
| Virtuální počítače nebo rozšíření | 2016-03-30 |
| Škálovací sady virtuálních počítačů | 2016-03-30 |
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
| Search | 2015-04-01 |
| Navrhnout | 2015-04-01 |

### <a name="microsoftinsights"></a>Microsoft.Insights

| Typy prostředků | Verze rozhraní API |
|--------------------|--------------------|
| Pravidla upozornění | 2016-03-01 |
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

### <a name="microsoftkeyvaultadmin"></a>Microsoft.Keyvault.Admin

Správa vašeho klíče trezory a také klíčů, tajných klíčů a certifikátů v rámci vaší trezorů klíčů. Další informace najdete v tématu [REST API služby Azure Key Vault odkaz](https://docs.microsoft.com/rest/api/keyvault/).

| Typy prostředků | Verze rozhraní API |
|------------------|--------------------|
| Umístění | 2017-02-01-preview |
| Umístění nebo kvóty | 2017-02-01-preview |

### <a name="microsoftnetwork"></a>Microsoft.Network

Výsledek volání operací je reprezentace k dispozici seznam operací cloudové sítě. Další informace najdete v tématu [operace REST API](https://docs.microsoft.com/rest/api/operation/).

| Typy prostředků | Verze rozhraní API |
|---------------------------|--------------|
| Připojení | 2015-06-15 |
| DNS Zones | 2016-04-01 |
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
| Poskytovatelé | 2015-012017-08-01 |
| Skupiny prostředků | 2015-012016-09-01 |
| Zdroje a prostředky | 2015-012016-09-01 |
| Předplatná | 2015-012016-09-01 |
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
| Zkontrolovat dostupnost názvu | 2016-01-01 |
| Umístění | 2016-01-01 |
| Umístění nebo kvóty | 2016-01-01 |
| Operace | 2016-01-01 |
| storageAccounts | 2016-01-01 |
| Použití | 2016-01-01 |

## <a name="next-steps"></a>Další postup

* [Instalace PowerShellu pro Azure Stack](azure-stack-powershell-install.md)
* [Konfigurace prostředí PowerShell Azure zásobník uživatele](azure-stack-powershell-configure-user.md)  
