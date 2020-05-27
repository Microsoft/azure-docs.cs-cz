---
title: Odebrání virtuálních počítačů z Azure Automation Change Tracking a inventáře
description: Tento článek popisuje, jak odebrat virtuální počítače z Change Tracking a inventáře.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 22cb49c414e21e5c47330f2c67fc2cf30e3364b2
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836664"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>Odebrání virtuálních počítačů z řešení Change Tracking a Inventory

Až skončíte s nasazováním změn do virtuálních počítačů ve vašem prostředí, můžete je odebrat z funkce [Change Tracking a inventáře](change-tracking.md) .

1. Ve svém účtu Automation v části **Správa konfigurace**vyberte možnost **sledování změn** nebo **inventarizace** .

2. Pomocí následujícího příkazu Identifikujte identifikátor UUID virtuálního počítače, který chcete odebrat ze správy.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. V pracovním prostoru Log Analytics v části **Obecné**Získejte přístup k uloženým hledáním.

4. V případě uloženého hledání `MicrosoftDefaultComputerGroup` klikněte na tlačítko se třemi tečkami vpravo a vyberte **Upravit**. 

5. Odeberte UUID pro virtuální počítač.

6. Opakujte postup pro všechny ostatní virtuální počítače, které chcete odebrat.

7. Uložte uložené hledání, až budete hotovi s jeho úpravou. 

## <a name="next-steps"></a>Další kroky

* Pokud chcete pokračovat v práci s Change Tracking a inventářem, přečtěte si téma [správa Change Tracking a inventáře](change-tracking-file-contents.md).
* Řešení obecných problémů s funkcemi najdete v tématu [řešení potíží s Change Tracking a inventářem](troubleshoot/change-tracking.md).