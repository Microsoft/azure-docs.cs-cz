---
title: Vyřazování virtuálních počítačů Azure Classic do 1. března 2023
description: Článek poskytuje podrobný přehled klasického vyřazení virtuálních počítačů
services: virtual-machines
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 9f3fd59fa040ab46a5fc4ef8272a17cba4c631b8
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925771"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migrace prostředků IaaS do Azure Resource Manager do 1. března 2023 

V 2014 jsme na Azure Resource Manager spustili IaaS a vylepšili jsme možnosti, které se od verze nezměnily. Vzhledem k tomu, že [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) nyní má úplné možnosti IaaS a další zálohy, zakázali jsme správu virtuálních počítačů s IaaS prostřednictvím Azure Service Manager od 28. února 2020 a tato funkce bude plně vyřazena 1. března 2023. 

V dnešní době se o 90% virtuálních počítačů s IaaS používají Azure Resource Manager. Pokud používáte prostředky IaaS prostřednictvím služby Azure Service Manager (ASM), zahajte vám plánování migrace hned a od 1. března 2023, abyste mohli využít [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/).

Klasické virtuální počítače budou po vyřazení za [moderní zásady životního cyklu](https://support.microsoft.com/help/30881/modern-lifecycle-policy) .

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

- [Přečtěte si další informace](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview) o migraci klasických virtuálních počítačů se systémy [Linux](./linux/migration-classic-resource-manager-plan.md) a [Windows](./windows/migration-classic-resource-manager-plan.md) do Azure Resource Manager.

- Další informace najdete v [nejčastějších dotazech k migraci z modelu Classic na Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq)

- V případě technických otázek a problémů [kontaktujte podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

- Další otázky nejsou součástí NEJČASTĚJŠÍch dotazů a zpětná vazba, komentář níže.
