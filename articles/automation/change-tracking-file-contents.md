---
title: Zobrazení změn obsahu souborů pomocí Azure Automation
description: Pomocí funkce změny obsahu souboru funkce Sledování změn zobrazíte obsah souboru, který se změnil.
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 57c3c2c7a0c923921c727ccea7839940457bc1ee
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682999"
---
# <a name="view-contents-of-a-file-that-is-being-tracked-with-change-tracking"></a>Zobrazení obsahu souboru, který je sledován pomocí funkce Sledování změn

Sledování obsahu souboru umožňuje zobrazit obsah souboru před a po změně, která je sledována pomocí funkce Sledování změn. Chcete-li to provést, uloží obsah souboru do účtu úložiště po každé změně dojde.

## <a name="requirements"></a>Požadavky

* Pro ukládání obsahu souborů je vyžadován standardní účet úložiště používající model nasazení Resource Manageru. Účty úložiště modelu premium a klasické nasazení by neměly být používány. Další informace o účtech úložiště najdete v tématu [O účtech úložiště Azure.](../storage/common/storage-create-storage-account.md)

* Použitý účet úložiště může mít připojený pouze 1 účet automatizace.

* [Sledování změn](automation-change-tracking.md) je povoleno ve vašem účtu Automatizace.

## <a name="enable-file-content-tracking"></a>Povolení sledování obsahu souborů

1. Na webu Azure Portal otevřete účet Automation a pak vyberte **Sledování změn**.
2. V horní nabídce vyberte **Upravit nastavení**.
3. Vyberte **Obsah souboru** a klepněte na **odkaz**. Otevře se **podokno Přidat umístění obsahu pro sledování změn.**

   ![Povolit](./media/change-tracking-file-contents/enable.png)

4. Vyberte účet předplatného a úložiště, do který chcete uložit obsah souboru. Pokud chcete povolit sledování obsahu souborů pro všechny existující sledované soubory, vyberte **možnost Zapnuto** pro **nahrání obsahu souboru pro všechna nastavení**. Tuto cestu můžete později změnit pro každou cestu k souboru.

   ![nastavení účtu úložiště](./media/change-tracking-file-contents/storage-account.png)

5. Po povolení se zobrazí účet úložiště a Identifikátor Uris SAS. Platnost identifikátorů Uris SAS vyprší po 365 dnech a lze ji znovu vytvořit klepnutím na tlačítko **Znovu vygenerovat.**

   ![klíče účtu seznamu](./media/change-tracking-file-contents/account-keys.png)

## <a name="add-a-file"></a>Přidání souboru

Následující kroky vás provedou zapnutím sledování změn pro soubor:

1. Na stránce Upravit nastavení **sledování změn**vyberte buď **soubory Systému Windows,** nebo kartu **Soubory Linuxu** a klepněte na **tlačítko Přidat.**

1. Vyplňte informace o cestě k souboru a v části Nahrát obsah souboru vyberte Možnost **Nahrát obsah souboru pro všechna nastavení**. Toto nastavení povoluje sledování obsahu souborů pouze pro tuto cestu k souboru.

   ![přidání linuxového souboru](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="viewing-the-contents-of-a-tracked-file"></a>Zobrazení obsahu sledovaného souboru

1. Jakmile byla zjištěna změna pro soubor nebo soubor v cestě, zobrazí se na portálu. Vyberte změnu souboru ze seznamu změn. Zobrazí se podokno Změnit podrobnosti.

   ![změny seznamu](./media/change-tracking-file-contents/change-list.png)

1. V podokně Změnit podrobnosti se zobrazí standard před a za informacemi o souboru. Vyberte **Zobrazit změny obsahu souboru,** chcete-li zobrazit obsah souboru.

   ![změna podrobností](./media/change-tracking-file-contents/change-details.png)

1. Na nové stránce se zobrazí obsah souboru v zobrazení vedle sebe. Můžete také vybrat **možnost Vřadit,** chcete-li zobrazit vstěžené zobrazení změn.

   ![zobrazení změn souborů](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="next-steps"></a>Další kroky

Další informace o používání řešení najdete v kurzu o sledování změn:

> [!div class="nextstepaction"]
> [Řešení potíží se změnami ve vašem prostředí](automation-tutorial-troubleshoot-changes.md)

* Pomocí [hledání protokolů v protokolech Azure Monitor](../log-analytics/log-analytics-log-searches.md) uobrazte podrobná data sledování změn.

