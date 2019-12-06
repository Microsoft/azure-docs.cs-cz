---
title: Zobrazit změny obsahu souboru pomocí Azure Automation
description: Chcete-li zobrazit obsah souboru, který se změnil, použijte funkci změny obsahu souborů v sledování změn.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 07/03/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4ab88aa2dc604172f00d875353dabba61fd101af
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850580"
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

   ![povolit](./media/change-tracking-file-contents/enable.png)

4. Vyberte předplatné a účet úložiště, který chcete použít k uložení obsahu souboru do. Pokud chcete povolit sledování obsahu souborů pro všechny existující sledované soubory, vyberte **zapnuto** pro **nahrání obsahu souboru pro všechna nastavení**. Můžete to změnit pro každou cestu k souboru později.

   ![nastavit účet úložiště](./media/change-tracking-file-contents/storage-account.png)

5. Po povolení se zobrazí účet úložiště a identifikátory URI SAS. Platnost identifikátorů URI SAS vyprší po 365 dnech a je možné ji znovu vytvořit kliknutím na tlačítko **znovu vygenerovat** .

   ![výpis klíčů účtu](./media/change-tracking-file-contents/account-keys.png)

## <a name="add-a-file"></a>Přidat soubor

Následující kroky vás provedou zapnutím sledování změn pro soubor:

1. Na stránce **Upravit nastavení** **Change Tracking**vyberte kartu **soubory Windows** nebo **soubory systému Linux** a klikněte na **Přidat** .

1. Vyplňte informace pro cestu k souboru a v části **nahrát obsah souboru pro všechna nastavení**vyberte **true** . Toto nastavení povoluje sledování obsahu souborů pouze pro tuto cestu k souboru.

   ![Přidat soubor pro Linux](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="viewing-the-contents-of-a-tracked-file"></a>Zobrazení obsahu sledovaného souboru

1. Po zjištění změny souboru nebo souboru v cestě se zobrazí na portálu. Vyberte změnu souboru ze seznamu změn. Zobrazí se podokno **Podrobnosti o změně** .

   ![Zobrazit seznam změn](./media/change-tracking-file-contents/change-list.png)

1. Na stránce **Podrobnosti o změně** se zobrazí standardní informace před a po souboru, v levém horním rohu klikněte na **Zobrazit změny obsahu souboru** a zobrazí se obsah souboru.

   ![Změnit podrobnosti](./media/change-tracking-file-contents/change-details.png)

1. Na nové stránce se zobrazí obsah souboru v zobrazení vedle sebe. Můžete také vybrat možnost **inline (vložit** ) a zobrazit tak vložené zobrazení změn.

   ![Zobrazit změny souborů](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="next-steps"></a>Další kroky

Další informace o používání řešení najdete v kurzu o Change Tracking.

> [!div class="nextstepaction"]
> [Řešení potíží se změnami ve vašem prostředí](automation-tutorial-troubleshoot-changes.md)

* K zobrazení podrobných dat sledování změn použijte [prohledávání protokolů v protokolu Azure monitor](../log-analytics/log-analytics-log-searches.md) .

