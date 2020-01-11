---
title: Řešení chyb přesunutí
description: Použití Azure Resource Manageru k přesunutí prostředků do nové skupiny prostředků nebo předplatného.
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 5a65f7daa0f5e3b1c8c6ddfdbecc0ff7d53e5afd
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75891262"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Řešení potíží s přesunutím prostředků Azure do nové skupiny prostředků nebo předplatného

Tento článek popisuje návrhy, které vám pomůžou vyřešit problémy při přesunu prostředků.

## <a name="upgrade-a-subscription"></a>Upgrade předplatného

Pokud ve skutečnosti chcete upgradovat předplatné Azure (jako je například přepínání z bezplatného na průběžné platby), budete muset převést své předplatné.

* Upgrade bezplatné zkušební verze, najdete v článku [upgradujte vaše předplatné Microsoft Imagine Azure nebo bezplatná zkušební verze na průběžné platby](../../billing/billing-upgrade-azure-subscription.md).
* Chcete-li změnit účet s průběžnými platbami, [změnit předplatné Azure s průběžnými platbami na jinou nabídku](../../billing/billing-how-to-switch-azure-offer.md).

Pokud nelze převést předplatné [vytvoření žádosti o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Vyberte **správy předplatných** typ problému.

## <a name="service-limitations"></a>Omezení služby

Některé služby vyžadují při přesunu prostředků další předpoklady. Pokud přesouváte následující služby, ujistěte se, že jste zkontrolovali pokyny a omezení.

* [App Services](./move-limitations/app-service-move-limitations.md)
* [Služby Azure DevOps](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Model nasazení Classic](./move-limitations/classic-model-move-limitations.md)
* [Sítě](./move-limitations/networking-move-limitations.md)
* [Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Virtual Machines](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>Velké požadavky

Pokud je to možné, přerušení velké přesune do operací přesunu samostatných. Správce prostředků okamžitě vrátí chybu, pokud je v jedné operaci více než 800 prostředků. Ale přesun prostředků menší než 800 selhat také podle vypršení časového limitu.

## <a name="resource-not-in-succeeded-state"></a>Prostředek není v úspěšném stavu.

Když se zobrazí chybová zpráva s informacemi o tom, že prostředek nelze přesunout, protože není v úspěšném stavu, může se jednat o závislý prostředek, který tento přesun blokuje. Kód chyby je obvykle **MoveCannotProceedWithResourcesNotInSucceededState**.

Pokud zdrojová nebo cílová skupina prostředků obsahuje virtuální síť, při přesunu se zkontrolují stavy všech závislých prostředků pro virtuální síť. Tato kontrolu zahrnuje tyto prostředky přímo a nepřímo závislé na virtuální síti. Pokud některý z těchto prostředků je ve stavu selhání, přesun se zablokuje. Pokud třeba virtuální počítač, který používá virtuální síť, selhal, přesun se zablokuje. Přesunutí je blokováno i v případě, že virtuální počítač není jedním z přesouvaných prostředků a není v jedné ze skupin prostředků pro přesunutí.

Pokud se zobrazí tato chyba, máte dvě možnosti. Buď přesuňte prostředky do skupiny prostředků, která nemá virtuální síť, nebo se obraťte na [podporu](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Další kroky

Příkazy pro přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](move-resource-group-and-subscription.md).
