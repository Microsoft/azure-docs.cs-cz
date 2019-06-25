---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 9f7e2760ef8bf06a2e680dce90c323672ca9d491
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66416058"
---
* Převod vyžaduje restartování virtuálního počítače, proto naplánujte migraci virtuálních počítačů během už existujícího časového období údržby. 

* Převod je nevratný. 

* Mějte na paměti, že všechny uživatele s [Přispěvatel virtuálních počítačů](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) role nebude možné změnit velikost virtuálního počítače (jak se může před převod). Je to proto, že virtuální počítače se spravovanými disky vyžadují, aby uživatel měl oprávnění Microsoft.Compute/disks/write pro disky s operačním systémem.

* Nezapomeňte převod otestovat. Před migrací v produkčním prostředí proveďte migraci testovacího virtuálního počítače.

* Během převodu virtuální počítač uvolníte. Virtuální počítač obdrží novou IP adresu při spuštění po převodu. V případě potřeby můžete virtuálnímu počítači [přiřadit statickou IP adresu](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md).

* Přečtěte si minimální verze agenta virtuálního počítače Azure, které jsou potřeba k podpoře proces převodu. Informace o tom, jak zkontrolovat a aktualizovat verzi agenta najdete v tématu [minimální podporované verze pro agenty virtuálních počítačů v Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)