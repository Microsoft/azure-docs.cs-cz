---
title: Odebrání virtuálních počítačů z Azure Automation Update Management
description: Tento článek popisuje, jak odebrat počítače spravované pomocí Update Management.
services: automation
ms.topic: conceptual
ms.date: 07/28/2020
ms.custom: mvc
ms.openlocfilehash: d7f7e4aa8b2c192688020b4449c8750f94af29f6
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450144"
---
# <a name="remove-vms-from-update-management"></a>Odebrání virtuálních počítačů z Update Managementu

Až skončíte s správou aktualizací ve vašich virtuálních počítačích ve vašem prostředí, můžete zastavit správu virtuálních počítačů pomocí funkce [Update Management](update-mgmt-overview.md) .

## <a name="sign-into-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na web [Azure Portal](https://portal.azure.com).

## <a name="to-remove-your-vms"></a>Odebrání virtuálních počítačů

1. Z účtu Automation v části **Správa aktualizací**vyberte **Správa aktualizací** .

2. Pomocí následujícího příkazu Identifikujte identifikátor UUID počítače, který chcete odebrat ze správy.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. V pracovním prostoru Log Analytics v části **Obecné**Získejte přístup k uloženým hledáním pro konfiguraci oboru `MicrosoftDefaultScopeConfig-Updates` .

4. V případě uloženého hledání `MicrosoftDefaultComputerGroup` klikněte na tlačítko se třemi tečkami vpravo a vyberte **Upravit**.

5. Odeberte UUID pro virtuální počítač.

6. Opakujte postup pro všechny ostatní virtuální počítače, které chcete odebrat.

7. Uložte uložené hledání, až budete hotovi s jeho úpravou.

>[!NOTE]
>Počítače se pořád zobrazují i po jejich registraci, protože hlásíme na všech počítačích vyhodnocených za posledních 24 hodin. Po odpojení počítače musíte počkat 24 hodin, než se dostanou do seznamu.

## <a name="next-steps"></a>Další kroky

Pokud chcete znovu povolit správu virtuálního počítače, přečtěte si téma [povolení Update Management procházením Azure Portal](update-mgmt-enable-portal.md) nebo [povolením Update Management z virtuálního počítače Azure](update-mgmt-enable-vm.md).