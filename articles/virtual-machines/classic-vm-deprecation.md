---
title: Vyřazování virtuálních počítačů Azure Classic do 1. března 2023
description: Článek poskytuje podrobný přehled klasického vyřazení virtuálních počítačů
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 0b2b995a6fe4cedd14b2e4ceeddc5747ec2423cf
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2020
ms.locfileid: "88754799"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migrace prostředků IaaS do Azure Resource Manager do 1. března 2023 

V 2014 jsme na Azure Resource Manager spustili IaaS a vylepšili jsme možnosti, které se od verze nezměnily. Vzhledem k tomu, že [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) nyní má úplné možnosti IaaS a další zálohy, zakázali jsme správu virtuálních počítačů s IaaS prostřednictvím [Azure Service Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#what-is-azure-service-manager-and-what-does-it-mean-by-classic) od 28. února 2020 a tato funkce bude plně vyřazena 1. března 2023. 

V dnešní době se o 90% virtuálních počítačů s IaaS používají Azure Resource Manager. Pokud používáte prostředky IaaS prostřednictvím služby Azure Service Manager (ASM), zahajte vám plánování migrace hned a od 1. března 2023, abyste mohli využít [Azure Resource Manager](../azure-resource-manager/management/index.yml).

Klasické virtuální počítače budou za [moderní zásady životního cyklu](https://support.microsoft.com/help/30881/modern-lifecycle-policy) pro vyřazení.

## <a name="how-does-this-affect-me"></a>Co to pro mě znamená? 

- Od 28. února 2020 zákazníci, kteří nevyužili virtuální počítače s IaaS prostřednictvím služby Azure Service Manager (ASM) v měsíci z února 2020, už nebudou moct vytvářet klasické virtuální počítače. 
- Od 1. března 2023 už zákazníci nebudou moct IaaS virtuální počítače s využitím Azure Service Manager a všechny, které jsou pořád spuštěné nebo přidělené, se zastaví a zruší její přidělení. 
- Od 1. března 2023 budou předplatná, která nejsou migrována na Azure Resource Manager, informována o časových osách pro odstranění všech zbývajících klasických virtuálních počítačů.  

Tyto služby a funkce **Azure nebudou** ovlivněny tímto vyřazením: 
- Cloud Services 
- Účty úložiště, které **se** nepoužívají v klasických virtuálních počítačích 
- Virtuální sítě (virtuální sítě) **se** nepoužívají v klasických virtuálních počítačích. 
- Jiné klasické prostředky

## <a name="what-actions-should-i-take"></a>Jaké akce mám provést? 

- Začněte plánovat migraci na Azure Resource Manager dnes. 

- Vytvořte seznam všech ovlivněných virtuálních počítačů. Typ virtuálního počítače "Virtual Machines (Classic)" v okně [virtuálního počítače Azure Portal](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ClassicCompute%2FVirtualMachines) jsou všechny ovlivněné virtuální počítače v rámci předplatného. 

- [Přečtěte si další informace](./windows/migration-classic-resource-manager-overview.md) o migraci klasických virtuálních počítačů se systémy [Linux](./linux/migration-classic-resource-manager-plan.md) a [Windows](./windows/migration-classic-resource-manager-plan.md) do Azure Resource Manager.

- Další informace najdete v [nejčastějších dotazech k migraci z modelu Classic na Azure Resource Manager](./migration-classic-resource-manager-faq.md)

- V případě technických otázek, problémů a přidávání předplatných do seznamu povolených [kontaktů se obraťte na podporu](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"}).

- Další otázky nejsou součástí NEJČASTĚJŠÍch dotazů a zpětná vazba, komentář níže.

- Dokončete migraci co nejdříve, abyste zabránili obchodním dopadům a využili lepší výkon, & zabezpečení nových funkcí poskytovaných Azure Resource Manager. 

## <a name="what-resources-are-provided-to-me-for-this-migration"></a>Jaké prostředky jsou pro tuto migraci k dispozici?

- [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-virtual-machines-migration.html): podpora Microsoft & Community pro migraci

- [Podpora migrace do Azure](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"}): tým vyhrazené podpory pro technickou pomoc při migraci

- [Rychlé sledování společnosti Microsoft](https://www.microsoft.com/fasttrack): tým Microsoftu pro rychlé sledování může poskytnout technickou pomoc při migraci na oprávněné zákazníky. 

- Pokud vaše společnost nebo organizace spolupracuje s Microsoftem a/nebo spolupracuje s Microsoftem, jako je třeba (Cloud Solution Architect (CSA), správci technického účtu (TAMs)), pracujte s nimi pro další prostředky pro migraci. 

