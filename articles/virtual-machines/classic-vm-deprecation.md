---
title: Vyřazování virtuálních počítačů Azure Classic do 1. března 2023
description: Článek poskytuje podrobný přehled klasického vyřazení virtuálních počítačů
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 8c3e55d13c0b5fbf5b813f1669587836bf8b5e2d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86503009"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migrace prostředků IaaS do Azure Resource Manager do 1. března 2023 

V 2014 jsme na Azure Resource Manager spustili IaaS a vylepšili jsme možnosti, které se od verze nezměnily. Vzhledem k tomu, že [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) nyní má úplné možnosti IaaS a další zálohy, zakázali jsme správu virtuálních počítačů s IaaS prostřednictvím Azure Service Manager od 28. února 2020 a tato funkce bude plně vyřazena 1. března 2023. 

V dnešní době se o 90% virtuálních počítačů s IaaS používají Azure Resource Manager. Pokud používáte prostředky IaaS prostřednictvím služby Azure Service Manager (ASM), zahajte vám plánování migrace hned a od 1. března 2023, abyste mohli využít [Azure Resource Manager](../azure-resource-manager/management/index.yml).

Klasické virtuální počítače budou za [moderní zásady životního cyklu](https://support.microsoft.com/help/30881/modern-lifecycle-policy) pro vyřazení.

## <a name="how-does-this-affect-me"></a>Co to pro mě znamená? 

1) Od 28. února 2020 zákazníci, kteří nevyužili virtuální počítače s IaaS prostřednictvím služby Azure Service Manager (ASM) v měsíci z února 2020, už nebudou moct vytvářet klasické virtuální počítače. 
2) Od 1. března 2023 už zákazníci nebudou moct IaaS virtuální počítače s využitím Azure Service Manager a všechny, které jsou pořád spuštěné nebo přidělené, se zastaví a zruší její přidělení. 
2) Od 1. března 2023 budou předplatná, která nebyla migrována do Azure Resource Manager, informována o časových osách pro odstranění všech zbývajících klasických virtuálních počítačů.  

Tyto služby a funkce **Azure nebudou** ovlivněny tímto vyřazením: 
- Cloud Services 
- Účty úložiště, které **se** nepoužívají v klasických virtuálních počítačích 
- Virtuální sítě (virtuální sítě) **se** nepoužívají v klasických virtuálních počítačích. 
- Jiné klasické prostředky

## <a name="what-actions-should-i-take"></a>Jaké akce mám provést? 

- Začněte plánovat migraci na Azure Resource Manager dnes. 

- [Přečtěte si další informace](./windows/migration-classic-resource-manager-overview.md) o migraci klasických virtuálních počítačů se systémy [Linux](./linux/migration-classic-resource-manager-plan.md) a [Windows](./windows/migration-classic-resource-manager-plan.md) do Azure Resource Manager.

- Další informace najdete v [nejčastějších dotazech k migraci z modelu Classic na Azure Resource Manager](./windows/migration-classic-resource-manager-faq.md)

- V případě technických otázek, problémů a přidávání předplatných do seznamu povolených [kontaktů se obraťte na podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

- Další otázky nejsou součástí NEJČASTĚJŠÍch dotazů a zpětná vazba, komentář níže.
