---
title: Zobrazit změny obsahu souboru pomocí Azure Automation
description: Pomocí funkce změny obsahu souboru Change Tracking můžete zobrazit obsah souboru, který se změnil.
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 57c3c2c7a0c923921c727ccea7839940457bc1ee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81682999"
---
# <a name="view-contents-of-a-file-that-is-being-tracked-with-change-tracking"></a>Zobrazit obsah souboru, který je sledován pomocí Change Tracking

Sledování obsahu souborů umožňuje zobrazit obsah souboru před a po změně, která je sledována pomocí Change Tracking. Pokud to chcete provést, uloží se obsah souboru do účtu úložiště, jakmile dojde ke každé změně.

## <a name="requirements"></a>Požadavky

* Pro ukládání obsahu souboru se vyžaduje účet úložiště úrovně Standard, který používá model nasazení Správce prostředků. Účty úložiště pro model nasazení úrovně Premium a Classic by se neměly používat. Další informace o účtech úložiště najdete v tématu [informace o účtech Azure Storage](../storage/common/storage-create-storage-account.md) .

* Použitý účet úložiště může mít jenom 1 připojený účet Automation.

* Ve vašem účtu Automation je povolený [Change Tracking](automation-change-tracking.md) .

## <a name="enable-file-content-tracking"></a>Povolit sledování obsahu souboru

1. V Azure Portal otevřete svůj účet Automation a pak vyberte **Change Tracking (sledování změn**).
2. V horní nabídce vyberte **Upravit nastavení**.
3. Vyberte **soubor obsahu** a klikněte na **odkaz**. Tím se otevře podokno **Přidat umístění obsahu pro Change Tracking** .

   ![aby](./media/change-tracking-file-contents/enable.png)

4. Vyberte předplatné a účet úložiště, který chcete použít k uložení obsahu souboru do. Pokud chcete povolit sledování obsahu souborů pro všechny existující sledované soubory, vyberte **zapnuto** pro **nahrání obsahu souboru pro všechna nastavení**. Můžete to změnit pro každou cestu k souboru později.

   ![nastavit účet úložiště](./media/change-tracking-file-contents/storage-account.png)

5. Po povolení se zobrazí účet úložiště a identifikátory URI SAS. Platnost identifikátorů URI SAS vyprší po 365 dnech a je možné ji znovu vytvořit kliknutím na tlačítko **znovu vygenerovat** .

   ![výpis klíčů účtu](./media/change-tracking-file-contents/account-keys.png)

## <a name="add-a-file"></a>Přidat soubor

Následující kroky vás provedou zapnutím sledování změn pro soubor:

1. Na stránce Upravit nastavení **Change Tracking**vyberte buď kartu **soubory Windows** , nebo **soubory pro Linux** a klikněte na **Přidat** .

1. Vyplňte informace pro cestu k souboru a v části **nahrát obsah souboru pro všechna nastavení**vyberte true. Toto nastavení povoluje sledování obsahu souborů pouze pro tuto cestu k souboru.

   ![Přidat soubor pro Linux](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="viewing-the-contents-of-a-tracked-file"></a>Zobrazení obsahu sledovaného souboru

1. Po zjištění změny souboru nebo souboru v cestě se zobrazí na portálu. Vyberte změnu souboru ze seznamu změn. Zobrazí se podokno Podrobnosti o změně.

   ![Zobrazit seznam změn](./media/change-tracking-file-contents/change-list.png)

1. V podokně Podrobnosti o změně se zobrazí standardní informace o souboru před a za. Chcete-li zobrazit obsah souboru, vyberte možnost **Zobrazit změny obsahu souboru** .

   ![změnit podrobnosti](./media/change-tracking-file-contents/change-details.png)

1. Na nové stránce se zobrazí obsah souboru v zobrazení vedle sebe. Můžete také vybrat možnost **inline (vložit** ) a zobrazit tak vložené zobrazení změn.

   ![Zobrazit změny souborů](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="next-steps"></a>Další kroky

Další informace o používání řešení najdete v kurzu o Change Tracking.

> [!div class="nextstepaction"]
> [Řešení potíží se změnami ve vašem prostředí](automation-tutorial-troubleshoot-changes.md)

* K zobrazení podrobných dat sledování změn použijte [prohledávání protokolů v protokolu Azure monitor](../log-analytics/log-analytics-log-searches.md) .

