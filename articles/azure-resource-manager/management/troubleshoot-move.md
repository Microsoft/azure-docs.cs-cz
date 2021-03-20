---
title: Řešení chyb přesunutí
description: Řešení potíží s přesunutím prostředků do nové skupiny prostředků nebo předplatného.
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 41b1e2435caf9874f3582a3394664c7b7f5a8d29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90054158"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Řešení potíží s přesunem prostředků Azure do nové skupiny prostředků nebo nového předplatného

Tento článek popisuje návrhy, které vám pomůžou vyřešit problémy při přesunu prostředků.

## <a name="upgrade-a-subscription"></a>Upgrade předplatného

Pokud ve skutečnosti chcete upgradovat předplatné Azure (jako je například přepínání z bezplatného na průběžné platby), budete muset převést své předplatné.

* Pokud chcete upgradovat bezplatnou zkušební verzi, přečtěte si téma [upgrade bezplatné zkušební verze nebo Microsoft Imagine předplatné Azure na průběžné platby](../../cost-management-billing/manage/upgrade-azure-subscription.md).
* Pokud chcete změnit účet s průběžnými platbami, přečtěte si téma [Změna předplatného s průběžnými platbami Azure na jinou nabídku](../../cost-management-billing/manage/switch-azure-offer.md).

Pokud předplatné nepřevedete, [vytvořte žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Jako typ problému vyberte **Správa předplatného** .

## <a name="service-limitations"></a>Omezení služby

Některé služby vyžadují při přesunu prostředků další předpoklady. Pokud přesouváte následující služby, ujistěte se, že jste zkontrolovali pokyny a omezení.

* [App Services](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Model nasazení Classic](./move-limitations/classic-model-move-limitations.md)
* [Sítě](./move-limitations/networking-move-limitations.md)
* [Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Virtual Machines](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>Velké požadavky

Pokud je to možné, přerušit velké přesunuté operace do samostatných operací přesunutí. Správce prostředků okamžitě vrátí chybu, pokud je v jedné operaci více než 800 prostředků. Přechod na méně než 800 prostředků ale může selhat také vypršením časového limitu.

## <a name="resource-not-in-succeeded-state"></a>Prostředek není v úspěšném stavu.

Když se zobrazí chybová zpráva s informacemi o tom, že prostředek nelze přesunout, protože není v úspěšném stavu, může se jednat o závislý prostředek, který tento přesun blokuje. Kód chyby je obvykle **MoveCannotProceedWithResourcesNotInSucceededState**.

Pokud zdrojová nebo cílová skupina prostředků obsahuje virtuální síť, při přesunu se zkontrolují stavy všech závislých prostředků pro virtuální síť. Tato kontrolu zahrnuje tyto prostředky přímo a nepřímo závislé na virtuální síti. Pokud některý z těchto prostředků je ve stavu selhání, přesun se zablokuje. Pokud třeba virtuální počítač, který používá virtuální síť, selhal, přesun se zablokuje. Přesunutí je blokováno i v případě, že virtuální počítač není jedním z přesouvaných prostředků a není v jedné ze skupin prostředků pro přesunutí.

Pokud se zobrazí tato chyba, máte dvě možnosti. Buď přesuňte prostředky do skupiny prostředků, která nemá virtuální síť, nebo se obraťte na [podporu](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Další kroky

Příkazy pro přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](move-resource-group-and-subscription.md).
