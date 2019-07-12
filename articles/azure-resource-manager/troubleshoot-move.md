---
title: Řešení chyb při přesunu prostředků Azure na nové předplatné nebo skupinu prostředků
description: Použití Azure Resource Manageru k přesunutí prostředků do nové skupiny prostředků nebo předplatného.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: e23d7c571a010e5bfb42e5f15368e0194272ed53
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723464"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Řešení potíží s přesun prostředků Azure do nové skupiny prostředků nebo předplatného

Tento článek nabízí návrhy k vyřešení problémů, při přesouvání prostředků.

## <a name="upgrade-a-subscription"></a>Upgradujte předplatné

Pokud chcete skutečně upgradovat vaše předplatné Azure (např. přechod z bezplatné na průběžné platby), musíte převést vaše předplatné.

* Upgrade bezplatné zkušební verze, najdete v článku [upgradujte vaše předplatné Microsoft Imagine Azure nebo bezplatná zkušební verze na průběžné platby](../billing/billing-upgrade-azure-subscription.md).
* Chcete-li změnit účet s průběžnými platbami, [změnit předplatné Azure s průběžnými platbami na jinou nabídku](../billing/billing-how-to-switch-azure-offer.md).

Pokud nelze převést předplatné [vytvoření žádosti o podporu Azure](../azure-supportability/how-to-create-azure-support-request.md). Vyberte **správy předplatných** typ problému.

## <a name="service-limitations"></a>Omezení služby

Některé služby vyžadují další aspekty při přesouvání prostředků. Pokud přesouváte i tyto služby, ujistěte se, že najdete pokyny a omezení.

* [App Services](./move-limitations/app-service-move-limitations.md)
* [Služby Azure DevOps](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Model nasazení Classic](./move-limitations/classic-model-move-limitations.md)
* [Recovery Services](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Virtual Machines](./move-limitations/virtual-machines-move-limitations.md)
* [Virtuální sítě](./move-limitations/virtual-network-move-limitations.md)

## <a name="large-requests"></a>Velké požadavky.

Pokud je to možné, přerušení velké přesune do operací přesunu samostatných. Resource Manager okamžitě vrátí chybu, pokud existuje více než 800 prostředky v rámci jedné operace. Ale přesun prostředků menší než 800 selhat také podle vypršení časového limitu.

## <a name="next-steps"></a>Další kroky

Příkazy pro přesun prostředky, najdete v části [přesunutí prostředků do nové skupiny prostředků nebo předplatného](resource-group-move-resources.md).
