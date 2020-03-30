---
title: Poradce při potížích s přesunutím
description: Pomocí Správce prostředků Azure přesuňte prostředky do nové skupiny prostředků nebo předplatného.
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 5a65f7daa0f5e3b1c8c6ddfdbecc0ff7d53e5afd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75891262"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Řešení potíží s přesunem prostředků Azure do nové skupiny prostředků nebo nového předplatného

Tento článek obsahuje návrhy, které pomáhají řešit problémy při přesouvání prostředků.

## <a name="upgrade-a-subscription"></a>Upgrade předplatného

Pokud opravdu chcete upgradovat předplatné Azure (například přechod z bezplatného na platbu průběžně), musíte předplatné převést.

* Pokud chcete upgradovat bezplatnou zkušební verzi, [přečtěte si informace o upgradu bezplatné zkušební verze nebo předplatného Microsoft Imagine Azure na průběžný platby](../../billing/billing-upgrade-azure-subscription.md).
* Pokud chcete změnit účet s průběžným platbaem, [přečtěte si, že změníte předplatné Azure s průběžným platbou na jinou nabídku](../../billing/billing-how-to-switch-azure-offer.md).

Pokud se vám nepodaří převést předplatné, [vytvořte žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Vyberte **službu Správa předplatného** pro typ problému.

## <a name="service-limitations"></a>Omezení služeb

Některé služby vyžadují další důležité informace při přesouvání prostředků. Pokud přesouváte následující služby, zkontrolujte pokyny a omezení.

* [App Services](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Model nasazení Classic](./move-limitations/classic-model-move-limitations.md)
* [Síťové služby](./move-limitations/networking-move-limitations.md)
* [Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Virtuální počítače](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>Velké požadavky

Pokud je to možné, rozbít velké přesune do samostatné operace přesunutí. Správce prostředků okamžitě vrátí chybu, pokud existuje více než 800 prostředků v jedné operaci. Přesunutí méně než 800 prostředků však může také selhat vypršení časového limitu.

## <a name="resource-not-in-succeeded-state"></a>Zdroj není v úspěšném stavu

Když se zobrazí chybová zpráva, která označuje, že prostředek nelze přesunout, protože není v úspěšném stavu, může být ve skutečnosti závislý prostředek, který blokuje přesun. Kód chyby je obvykle **MoveCannotProceedWithResourcesNotInSucceededState**.

Pokud zdrojová nebo cílová skupina prostředků obsahuje virtuální síť, stavy všech závislých prostředků pro virtuální síť jsou kontrolovány během přesunu. Kontrola zahrnuje tyto prostředky přímo a nepřímo závislé na virtuální síti. Pokud některý z těchto prostředků jsou ve stavu selhání, přesunutí je blokován. Například pokud virtuální počítač, který používá virtuální síť se nezdařilo, přesunutí je blokován. Přesunutí je blokováno i v případě, že virtuální počítač není jedním z prostředků, které se přesouvají, a není v jedné ze skupin prostředků pro přesun.

Když se zobrazí tato chyba, máte dvě možnosti. Přesuňte prostředky do skupiny prostředků, která nemá virtuální síť, nebo [se obraťte na podporu](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Další kroky

Příkazy k přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](move-resource-group-and-subscription.md).
