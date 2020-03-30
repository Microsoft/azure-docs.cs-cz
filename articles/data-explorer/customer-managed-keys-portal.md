---
title: Konfigurace klíčů spravovaných zákazníkem pomocí portálu Azure
description: Tento článek popisuje, jak nakonfigurovat šifrování klíčů spravovaných zákazníkem na vašich datech v Průzkumníku dat Azure.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: c014ed2c25711677617d3bf8ff5d2f0f968a3b14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80301041"
---
# <a name="configure-customer-managed-keys-using-the-azure-portal"></a>Konfigurace klíčů spravovaných zákazníkem pomocí portálu Azure

> [!div class="op_single_selector"]
> * [Portál](customer-managed-keys-portal.md)
> * [C #](customer-managed-keys-csharp.md)
> * [Šablona Azure Resource Manageru](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-in-the-azure-portal"></a>Povolení šifrování pomocí klíčů spravovaných zákazníkem na webu Azure Portal

Tento článek ukazuje, jak povolit šifrování klíčů spravovaných zákazníkem pomocí portálu Azure. Ve výchozím nastavení používá šifrování Azure Data Explorer u klíčů spravovaných společností Microsoft. Nakonfigurujte cluster Průzkumníka dat Azure tak, aby používal klíče spravované zákazníky, a zadejte klíč, který chcete k clusteru přidružit.

1. Na [webu Azure Portal](https://portal.azure.com/)přejděte na prostředek [clusteru Azure Data Explorer.](create-cluster-database-portal.md#create-a-cluster) 
1.  > V **Settings**levém podokně portálu vyberte**Nastavení šifrování.**
1. V podokně **Šifrování** vyberte **Zapnuto** pro nastavení **klíče spravovaného zákazníkem.**
1. Klepněte na **tlačítko Vybrat klávesu**.

    ![Konfigurace klíčů spravovaných zákazníkem](media/customer-managed-keys-portal/cmk-encryption-setting.png)

1. V okně **Vybrat klíč z trezoru klíčů Azure** vyberte existující trezor **klíčů** z rozevíracího seznamu. Pokud vyberete **Možnost Vytvořit nový** a [vytvoříte nový trezor klíčů](/azure/key-vault/quick-create-portal#create-a-vault), budete přesměrováni na obrazovku Vytvořit trezor **klíčů.**

1. Vyberte **klávesu**.
1. Vyberte **možnost Verze**.
1. Klepněte na **tlačítko Vybrat**.

    ![Výběr klíče z trezoru klíčů Azure](media/customer-managed-keys-portal/cmk-key-vault.png)

1. V podokně **Šifrování,** které nyní obsahuje klíč, vyberte **Uložit**. Když je vytvoření CMK úspěšné, zobrazí se zpráva o úspěchu v **oznámeních**.

    ![Uložení klíče spravovaného zákazníkem](media/customer-managed-keys-portal/cmk-encryption-setting.png)

Povolením klíčů spravovaných zákazníky pro cluster Azure Data Explorer vytvoříte systém přiřazenou identitu pro cluster, pokud neexistuje. Kromě toho budete poskytovat požadovaná oprávnění zobrazení clusteru Průzkumníka dat Azure ve vybraném trezoru klíčů a získáte vlastnosti trezoru klíčů. 

> [!NOTE]
> Chcete-li po vytvoření vybrat **možnost Vypnuto,** odeberte klíč spravovaný zákazníkem.

## <a name="next-steps"></a>Další kroky

* [Zabezpečené clustery Azure Data Explorer v Azure](security.md)
* [Zabezpečte svůj cluster v Azure Data Explorer – Portál Azure](manage-cluster-security.md) povolením šifrování v klidovém stavu.
* [Konfigurace klíčů spravovaných zákazníkem pomocí šablony Azure Resource Manager](customer-managed-keys-resource-manager.md)
* [Konfigurace klíčů spravovaných zákazníkem pomocí c #](customer-managed-keys-csharp.md)



