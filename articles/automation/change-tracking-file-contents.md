---
title: Zobrazit změny obsahu souboru s využitím Azure Automation
description: Chcete-li zobrazit obsah souboru, který se změnil použijte funkci Změna obsahu soubor sledování změn.
services: automation
ms.service: automation
ms.component: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 07/03/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0582505d66bbef3064359fa4047676c4ba60b4e9
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37871630"
---
# <a name="view-contents-of-a-file-that-is-being-tracked-with-change-tracking"></a>Zobrazit obsah souboru, který je sledován pomocí řešení Change Tracking

Sledování souborů obsahu vám umožní zobrazit obsah souboru před a po změně, která je sledována pomocí Change Tracking. K tomuto účelu uloží obsah souboru do účtu úložiště po každé změně.

## <a name="requirements"></a>Požadavky

* Účet úložiště úrovně standard s využitím modelu nasazení Resource Manageru se vyžaduje pro ukládání obsahu souboru. Není vhodné používat Premium a účty úložiště pro model nasazení classic. Další informace o účtech úložiště najdete v tématu [účty Azure storage](../storage/common/storage-create-storage-account.md)

* Účet úložiště používané může mít pouze 1 účet Automation, které jsou připojené.

* [Sledování změn](automation-change-tracking.md) je povolená ve vašem účtu Automation.

## <a name="enable-file-content-tracking"></a>Povolit sledování obsahu souboru

1. Na webu Azure Portal otevřete svůj účet Automation a pak vyberte **řešení Change tracking**.
2. V horní nabídce vyberte **upravit nastavení**.
3. Vyberte **obsah souboru** a klikněte na tlačítko **odkaz**. Tím se otevře **přidat umístění obsahu pro řešení Change Tracking** podokně.

   ![povolit](./media/change-tracking-file-contents/enable.png)

4. Vyberte předplatné a účet úložiště pro ukládání obsahu souboru. Pokud chcete povolit sledování obsahu souboru pro všechny existující sledované soubory, vyberte **na** pro **nahrát obsah souboru pro všechna nastavení**. Toto můžete změnit pro každé cesty souboru později.

   ![účet úložiště](./media/change-tracking-file-contents/storage-account.png)

5. Po povolení účtu úložiště a identifikátorů URI pro SAS se zobrazí. Identifikátory URI SAS vyprší za 365 dnů a může být znovu vytvořena po kliknutí **znovu vygenerovat** tlačítko.

   ![Vypsat klíče účtu](./media/change-tracking-file-contents/account-keys.png)

## <a name="add-a-file"></a>Přidání souboru

Následující postup vás provede povolením sledování změn pro soubor:

1. Na **upravit nastavení** stránce **řešení Change Tracking**, vyberte buď **soubory Windows** nebo **soubory Linuxu** kartu a klikněte na tlačítko  **Přidat**

1. Potřebné informace pro cestu k souboru a vyberte **True** pod **nahrát obsah souboru pro všechna nastavení**. Toto nastavení umožňuje sledování pro tuto cestu k souboru, pouze obsah souboru.

   ![Přidání souboru linuxu](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="viewing-the-contents-of-a-tracked-file"></a>Zobrazení obsahu souboru sledované

1. Jakmile pro soubor nebo soubor v cestě byla zjištěna změna, zobrazí na portálu. Vyberte ze seznamu změn změny souboru. **Změnit podrobnosti** zobrazí podokno.

   ![Zobrazit změny](./media/change-tracking-file-contents/change-list.png)

1. Na **změnit podrobnosti** stránce se zobrazí standardní před a po souboru informace, v levé horní části, klikněte na tlačítko **zobrazit změny obsahu souboru** zobrazíte obsah souboru.

  ![Podrobnosti o změně](./media/change-tracking-file-contents/change-details.png)

1. Nová stránka zobrazuje obsah souboru v zobrazení vedle sebe. Můžete také vybrat **vložené** zobrazení vloženého změn.

  ![zobrazení změn v souboru](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="next-steps"></a>Další postup

Navštivte tento kurz na další informace o použití řešení sledování změn:

> [!div class="nextstepaction"]
> [Řešení potíží se změnami ve vašem prostředí](automation-tutorial-troubleshoot-changes.md)

* Použití [prohledávání protokolů v Log Analytics](../log-analytics/log-analytics-log-searches.md) zobrazíte podrobné data řešení change tracking.
