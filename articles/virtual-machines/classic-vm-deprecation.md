---
title: 1. března 2023 odcházíme do důchodu s virtuálními počítači Azure Classic.
description: Článek poskytuje přehled na vysoké úrovni o klasickém vyřazení virtuálních zařízení
services: virtual-machines
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 764567bffd2a08ebb5beb17e3063998848b3f110
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127332"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migrace prostředků IaaS do Správce prostředků Azure do 1. 

V roce 2014 jsme spustili IaaS ve Správci prostředků Azure a od té doby vylepšujeme možnosti. Vzhledem k tomu, [že Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) má teď plné možnosti IaaS a další vylepšení, jsme zastaralé správy virtuálních stránek IaaS prostřednictvím Azure Service Manager v únoru 28, 2020 a tato funkce bude plně vyřazena na 1 března 2023. 

Dnes asi 90 % virtuálních počítačů IaaS používá Azure Resource Manager. Pokud používáte prostředky IaaS prostřednictvím Azure Service Manager (ASM), začněte plánovat migraci nyní [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/)a do 1.

Klasické virtuální aplikace se budou v ynominu dodržovat [zásady moderního životního cyklu](https://support.microsoft.com/help/30881/modern-lifecycle-policy) pro vyřazení.

## <a name="how-does-this-affect-me"></a>Co to pro mě znamená? 

1) února 2020 zákazníci, kteří nevyužili virtuální počítače IaaS prostřednictvím Azure Service Manager (ASM) v měsíci únoru 2020, už nebudou moct vytvářet klasické virtuální počítače. 
2) 1. března 2023 už zákazníci nebudou moct spouštět virtuální počítače IaaS pomocí Správce služeb Azure a všechny, které jsou pořád spuštěné nebo přidělené, se zastaví a budou deallocated. 
2) 1. března 2023 budou předplatná, která nemigrovala do Správce prostředků Azure, informována o časových osách pro odstranění zbývajících klasických virtuálních počítačů.  

Následující služby a funkce Azure **nebudou** ovlivněny tímto vyřazením: 
- Cloud Services 
- Účty **úložiště,** které se nepoužívají klasické virtuální chody 
- Virtuální sítě (Virtuální sítě) **nepoužívané** klasickými virtuálními počítači. 
- Další klasické zdroje

## <a name="what-actions-should-i-take"></a>Jaké kroky mám podniknout? 

- Začněte plánovat migraci do Správce prostředků Azure ještě dnes. 

- [Přečtěte si další informace](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview) o migraci klasických [virtuálních](./linux/migration-classic-resource-manager-plan.md) aplikací pro Linux a [Windows](./windows/migration-classic-resource-manager-plan.md) do Správce prostředků Azure.

- Další informace najdete v [často kladené otázky týkající se klasické migrace Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq)

- V případě technických dotazů a problémů [se obraťte na podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

- Pro další otázky, které nejsou součástí FAQ a zpětnou vazbu, komentář níže.
