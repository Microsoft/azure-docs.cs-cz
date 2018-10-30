---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: bf26af7fa4b1b31514fb82c5e28a85154b2e274a
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227518"
---
* Převod vyžaduje restartování virtuálního počítače, proto naplánujte migraci virtuálních počítačů během už existujícího časového období údržby. 

* Převod je nevratný. 

* Mějte na paměti, že všechny uživatele s [Přispěvatel virtuálních počítačů](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) role nebude možné změnit velikost virtuálního počítače (jak se může před převod). Je to proto, že virtuální počítače se spravovanými disky vyžadují, aby uživatel měl oprávnění Microsoft.Compute/disks/write pro disky s operačním systémem.

* Nezapomeňte převod otestovat. Před migrací v produkčním prostředí proveďte migraci testovacího virtuálního počítače.

* Během převodu virtuální počítač uvolníte. Virtuální počítač obdrží novou IP adresu při spuštění po převodu. V případě potřeby můžete virtuálnímu počítači [přiřadit statickou IP adresu](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md).

* Původní virtuální pevné disky a účet úložiště používané virtuálním počítačem před převodem se neodstraní. Budou se vám za ně i nadále účtovat poplatky. Abyste se vyhnuli účtování poplatků za tyto artefakty, po ověření dokončení převodu odstraňte původní objekty blob virtuálních pevných disků.

* Přečtěte si minimální verze agenta virtuálního počítače Azure, které jsou potřeba k podpoře proces převodu. Informace o tom, jak zkontrolovat a aktualizovat verzi agenta najdete v tématu [minimální podporované verze pro agenty virtuálních počítačů v Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)