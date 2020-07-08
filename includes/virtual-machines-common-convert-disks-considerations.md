---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 091e4dc0759ce1b227349d5d1b6b08b9eda66ee0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84237659"
---
* Převod vyžaduje restartování virtuálního počítače, proto naplánujte migraci virtuálních počítačů během už existujícího časového období údržby. 

* Převod je nevratný. 

* Mějte na paměti, že uživatelé s rolí [Přispěvatel virtuálních počítačů](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) nebudou moct měnit velikost virtuálního počítače (protože by mohly předběžné konverze). Důvodem je to, že virtuální počítače se spravovanými disky vyžadují, aby měl uživatel na discích s operačním systémem oprávnění Microsoft. COMPUTE/disks/Write.

* Nezapomeňte převod otestovat. Před migrací v produkčním prostředí proveďte migraci testovacího virtuálního počítače.

* Během převodu virtuální počítač uvolníte. Virtuální počítač obdrží novou IP adresu při spuštění po převodu. V případě potřeby můžete virtuálnímu počítači [přiřadit statickou IP adresu](../articles/virtual-network/public-ip-addresses.md).

* Zkontrolujte minimální verzi agenta virtuálního počítače Azure, který je potřebný k podpoře procesu převodu. Informace o tom, jak ověřit a aktualizovat verzi agenta, najdete v tématu [Podpora minimální verze pro agenty virtuálních počítačů v Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) .