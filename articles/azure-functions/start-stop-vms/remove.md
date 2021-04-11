---
title: Přehled možností spuštění/zastavení virtuálních počítačů v2 (Preview)
description: Tento článek popisuje, jak odebrat funkci spustit nebo zastavit virtuální počítače v2 (Preview).
services: azure-functions
ms.subservice: ''
ms.date: 03/30/2021
ms.topic: conceptual
ms.openlocfilehash: b4308be8f7494c1cb6f6b4839fc5a2e9717668e3
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111566"
---
# <a name="how-to-remove-startstop-vms-v2-preview"></a>Jak odebrat virtuální počítače spustit nebo zastavit v2 (Preview)

Po povolení funkce spustit nebo zastavit virtuální počítače v2 (Preview) pro správu stavu spuštění virtuálních počítačů Azure se můžete rozhodnout ji přestat používat. Odebrání této funkce se dá udělat tak, že odstraníte vyhrazenou skupinu prostředků, ve které se budou ukládat tyto prostředky, v podpoře spouštěcích nebo stop virtuálních počítačů v2 (Preview):

- Aplikace Azure Functions
- Plány v Azure Logic Apps
- Instance Application Insights
- Účet služby Azure Storage

## <a name="delete-the-dedicated-resource-group"></a>Odstranit vyhrazenou skupinu prostředků

Pokud chcete odstranit skupinu prostředků, postupujte podle kroků uvedených v článku [Azure Resource Manager skupiny prostředků a odstraňování prostředků](../../azure-resource-manager/management/delete-resource-group.md) .

## <a name="next-steps"></a>Další kroky

Pokud chcete tuto funkci znovu nasadit, přečtěte si téma [nasazení Start/Stop v2](deploy.md) (Preview).