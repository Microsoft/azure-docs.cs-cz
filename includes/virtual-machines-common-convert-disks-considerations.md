---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 9f7e2760ef8bf06a2e680dce90c323672ca9d491
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "66416058"
---
* Převod vyžaduje restartování virtuálního počítače, proto naplánujte migraci virtuálních počítačů během už existujícího časového období údržby. 

* Převod je nevratný. 

* Uvědomte si, že všichni uživatelé s rolí [přispěvatele virtuálního počítače](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) nebudou moct změnit velikost virtuálního počítače (jako by mohli předkonverzi). Důvodem je, že virtuální počítače se spravovanými disky vyžadují, aby uživatel měl oprávnění Microsoft.Compute/disks/write na discích operačního systému.

* Nezapomeňte převod otestovat. Před migrací v produkčním prostředí proveďte migraci testovacího virtuálního počítače.

* Během převodu virtuální počítač uvolníte. Virtuální počítač obdrží novou IP adresu při spuštění po převodu. V případě potřeby můžete virtuálnímu počítači [přiřadit statickou IP adresu](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md).

* Zkontrolujte minimální verzi agenta virtuálního počítače Azure, který je nutný pro podporu procesu převodu. Informace o tom, jak zkontrolovat a aktualizovat verzi agenta, najdete v [tématu Minimální podpora verzí pro agenty virtuálních her v Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)