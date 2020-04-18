---
title: Vytvoření úlohy sledovacího procesu v účtu Azure Automation
description: Zjistěte, jak vytvořit úlohu sledovacího procesu v účtu Azure Automation sledovat nové soubory vytvořené ve složce.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 1175350e7f9f4db92d7d59eba0cc66ac4bb49f5f
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617357"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Vytvoření úloh sledování azure automatizace ke sledování změn souborů v místním počítači

Azure Automation používá úlohu sledovacího procesu k hledání událostí a aktivaci akcí pomocí runbooků Prostředí PowerShell. Úloha sledovacího procesu obsahuje dvě části, sledovací proces a akci. Soubor runbook sledovacích zařízení se spouští v intervalu definovaném v úloze sledovacího procesu a výstupy dat do runbooku akce. 

Tento kurz vás provede vytvořením úlohy sledovacího procesu, která bude sledovat přidání nového souboru do adresáře. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Import sady Runbook sledovacího procesu
> * Vytvoření proměnné automatizace
> * Vytvoření runbooku akcí
> * Vytvoření úlohy sledovacího procesu
> * Spuštění sledovacího procesu
> * Kontrola výstupu

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující:

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet automatizace](automation-offering-get-started.md) pro uložení sledovacích a akčních runbooků a úlohy sledovacího procesu.
* [Hybridní pracovník runbooku,](automation-hybrid-runbook-worker.md) kde je spuštěna úloha sledovacího procesu.
* Runbooky prostředí PowerShell. Runbooky pracovního postupu prostředí PowerShell nejsou podporovány úlohami sledovacích procesů.

> [!NOTE]
> Sledovací procesy nejsou v Azure China podporované.

## <a name="import-a-watcher-runbook"></a>Import sady Runbook sledovacího procesu

Tento kurz používá spuštění sledovacího programu s názvem **Watch-NewFile** k hledání nových souborů v adresáři. Soubor Runbook sledovacího procesu načte poslední známý čas zápisu do souborů ve složce a prozkoumá všechny soubory novější než tento vodoznak.

Tento proces importu lze provést prostřednictvím [Galerie prostředí PowerShell](https://www.powershellgallery.com).

1. Přejděte na stránku galerie [pro Watch-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd).
2. Na kartě **Azure Automation** klikněte na Deploy to **Azure Automation**.

Tuto runbook můžete také importovat do svého účtu automatizace z portálu pomocí následujících kroků.

1. Otevřete si účet Automation a klikněte na stránku Runbook.
2. Klepněte na **tlačítko Procházet galerii**.
3. Vyhledejte **soubor Runbook sledování**, vyberte soubor **runbook Sledovací modul, který hledá nové soubory v adresáři**, a klepněte na tlačítko **Importovat**.
  ![Spuštění runbook pro automatizaci importu z uživatelského rozhraní](media/automation-watchers-tutorial/importsourcewatcher.png)
4. Pojmenujte a popis sady Runbook a kliknutím na **OK** aplikaci runbook importujte do svého účtu Automation.
5. Vyberte **Upravit** a klepněte na **tlačítko Publikovat**. Po zobrazení výzvy klepněte na tlačítko **Ano** a publikujte soubor Runbook.

## <a name="create-an-automation-variable"></a>Vytvoření proměnné automatizace

[Proměnná automatizace](automation-variables.md) se používá k ukládání časových razítek, které předchozí runbook čte a ukládá z každého souboru.

1. V části **Sdílené prostředky** vyberte **Proměnné** a klepněte na **+ Přidat proměnnou**.
1. Zadejte Watch-NewFileTimestamp pro název.
1. Vyberte DateTime pro typ.
1. Kliknutím na **Vytvořit** vytvořte proměnnou Automatizace.

## <a name="create-an-action-runbook"></a>Vytvoření runbooku akcí

Runbook akce se používá v úloze sledovacího procesu k jednání s daty, která jí byla předána z runbooku sledovacího procesu. Z [Galerie prostředí PowerShell](https://www.powershellgallery.com)je nutné importovat předdefinovanou runbook akcí nazvanou **Process-NewFile** . 

Vytvoření runbooku akcí:

1. Přejděte na stránku galerie [process-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf).
2. Na kartě **Azure Automation** klikněte na Deploy to **Azure Automation**.

Tento runbook můžete také importovat do svého účtu Automation z portálu Azure:

1. Přejděte na svůj účet Automation a v části **Automatizace procesů**vyberte **Runbook** .
1. Klepněte na **tlačítko Procházet galerii**.
1. Hledat **akci Sledovací modul**, vyberte **akci Sledovací modul, která zpracovává události spouštěné souborem runbook sledovacích procesů**, a klepněte na tlačítko **Importovat**.
  ![Importovat runbook akcí z ui](media/automation-watchers-tutorial/importsourceaction.png)
1. Pojmenujte a popis sady Runbook a kliknutím na **OK** aplikaci runbook importujte do svého účtu Automation.
1. Vyberte **Upravit** a klepněte na **tlačítko Publikovat**. Po zobrazení výzvy klepněte na tlačítko **Ano** a publikujte soubor Runbook.

## <a name="create-a-watcher-task"></a>Vytvoření úlohy sledovacího procesu

V tomto kroku nakonfigurujete úlohu sledovacího procesu odkazující na sledovací modula a sady Runbook akce definované v předchozích částech.

1. Přejděte na svůj účet Automation a v části **Automatizace procesů**vyberte **možnost Sledování úloh** .
1. Vyberte stránku Úkoly sledovacího procesu a klepněte na **tlačítko + Přidat úkol sledovacího procesu**.
1. Jako název zadejte **WatchMyFolder.**

1. Vyberte **Konfigurovat sledovací proces** a zvolte runbook **Watch-NewFile.**

1. Pro parametry zadejte následující hodnoty:

   * **FOLDERPATH** - Složka v hybridním pracovníkovi runbooku, kde vznikají nové soubory, například **d:\examplefiles**.
   * **ROZŠÍŘENÍ** - Rozšíření pro konfiguraci. Chcete-li zpracovat všechny přípony souborů, ponechejte prázdné.
   * **RECURSE** - Rekurzivní operace. Ponechte tuto hodnotu jako výchozí.
   * **RUN SETTINGS** - Nastavení pro spuštění runbooku. Vyberte hybridního pracovníka.

1. Klepněte na **tlačítko OK**a potom **vyberte,** chcete-li se vrátit na stránku Sledovací proces.
1. Vyberte **Konfigurovat akci** a zvolte runbook **Process-NewFile.**
1. Pro parametry zadejte následující hodnoty:

   * **EVENTDATA** - Data události. Ponechte prázdné. Data jsou předávána z runbooku sledovacího procesu.
   * **Spustit nastavení** – nastavení pro spuštění runbooku. Ponechte jako Azure, protože tento runbook běží v Azure Automation.

1. Klepněte na **tlačítko OK**a potom **vyberte,** chcete-li se vrátit na stránku Sledovací proces.
1. Chcete-li vytvořit úlohu sledovacího procesu, klepněte na tlačítko **OK.**

![Konfigurace akce sledovacího procesu z ui](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Spuštění sledovacího procesu

Je nutné spustit test, jak je popsáno níže zajistit, že sledovací proces úlohy funguje podle očekávání. 

1. Vzdálené do hybridního pracovníka runbooku. 
2. Otevřete **PowerShell** a vytvořte testovací soubor ve složce.

```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

Následující příklad ukazuje očekávaný výstup.

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>Kontrola výstupu

1. Přejděte na svůj účet Automation a v části **Automatizace procesů**vyberte **možnost Sledování úloh** .
1. Vyberte úkol sledovacího procesu **WatchMyFolder**.
1. Klikněte na **Zobrazit datové proudy sledovacích modulů** v části **Datové proudy,** abyste zjistili, že sledovací proces našel nový soubor a spustil runbook akcí.
1. Chcete-li zobrazit úlohy runbooku akcí, klikněte na **Zobrazit úlohy akce sledovacího procesu**. Každá úloha může být vybrána pro zobrazení podrobností o úloze.

   ![Úlohy akce sledovacího procesu z ui](media/automation-watchers-tutorial/WatcherActionJobs.png)

Očekávaný výstup při nalezení nového souboru lze vidět v následujícím příkladu:

```output
Message is Process new file...

Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Import sady Runbook sledovacího procesu
> * Vytvoření proměnné automatizace
> * Vytvoření runbooku akcí
> * Vytvoření úlohy sledovacího procesu
> * Spuštění sledovacího procesu
> * Kontrola výstupu

Další informace o vytváření vlastních runbooků najdete na tomto odkazu.

> [!div class="nextstepaction"]
> [Můj první Runbook Prostředí PowerShell](automation-first-runbook-textual-powershell.md).

