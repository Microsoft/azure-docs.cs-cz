---
title: Vytvoření úlohy sledovacího procesu v účtu Azure Automation
description: Zjistěte, jak vytvořit úlohu sledovacího procesu v účtu Azure Automation sledovat nové soubory vytvořené ve složce.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 5dc6145940883ff6f4446ad67c399cdf4931d38e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75419744"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Vytvoření úloh sledování azure automatizace ke sledování změn souborů v místním počítači

Azure Automation používá úlohy sledovacích procesů ke sledování událostí a aktivaci akcí pomocí runbooků prostředí PowerShell. Tento kurz vás provede vytvořením úlohy sledovacího procesu, která bude sledovat přidání nového souboru do adresáře.

V tomto kurzu se naučíte:

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

> [!NOTE]
> Sledovací procesy nejsou v Azure China podporované.

## <a name="import-a-watcher-runbook"></a>Import sady Runbook sledovacího procesu

Tento kurz používá spuštění sledovacího programu s názvem **Watch-NewFile** k hledání nových souborů v adresáři. Soubor Runbook sledovacího procesu načte poslední známý čas zápisu do souborů ve složce a prozkoumá všechny soubory novější než tento vodoznak.

Tento proces importu lze provést prostřednictvím [Galerie prostředí PowerShell](https://www.powershellgallery.com).

1. Přejděte na stránku galerie [pro Watch-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd).
2. Na kartě **Azure Automation** klikněte na Deploy to **Azure Automation**.

Tuto runbook můžete také importovat do svého účtu automatizace z portálu pomocí následujících kroků.

1. Otevřete si účet Automation a klikněte na stránku **Runbook.**
2. Klikněte na tlačítko **Procházet galerii.**
3. Vyhledejte "Modul runbook u pozorovatele", vyberte **soubor runbook sledovacích modulů, který hledá nové soubory v adresáři,** a vyberte **importovat**.
  ![Spuštění runbook pro automatizaci importu z uživatelského rozhraní](media/automation-watchers-tutorial/importsourcewatcher.png)
1. Pojmenujte a popis sady Runbook a výběrem **možnosti OK** můžete runbook importovat do svého účtu Automation.
1. Vyberte **Upravit** a klepněte na **tlačítko Publikovat**. Po zobrazení výzvy vyberte **Ano** pro publikování runbooku.

## <a name="create-an-automation-variable"></a>Vytvoření proměnné automatizace

[Proměnná automatizace](automation-variables.md) se používá k ukládání časových razítek, které předchozí runbook čte a ukládá z každého souboru.

1. V části **SDÍLENÉ ZDROJE** vyberte **Proměnné** a vyberte **+ Přidat proměnnou**.
1. Zadejte "Watch-NewFileTimestamp" pro název
1. Vyberte DatumČas pro typ.
1. Klikněte na tlačítko **Vytvořit.** Tím se vytvoří proměnná automatizace.

## <a name="create-an-action-runbook"></a>Vytvoření runbooku akcí

Runbook akce se používá v úloze sledovacího procesu k jednání s daty, která jí byla předána z runbooku sledovacího procesu. Runbooky pracovního postupu prostředí PowerShell nejsou podporovány úlohami sledovacích postupů, musíte použít sady Runbook prostředí PowerShell. Musíte importovat předdefinovanou runbook akcí nazvanou **Process-NewFile**.

Tento proces importu lze provést prostřednictvím [Galerie prostředí PowerShell](https://www.powershellgallery.com).

1. Přejděte na stránku galerie [process-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf).
2. Na kartě **Azure Automation** klikněte na Deploy to **Azure Automation**.

Tuto runbook můžete také importovat do svého účtu automatizace z portálu pomocí následujících kroků.

1. Přejděte na svůj účet automatizace a vyberte **Runbook** v kategorii **AUTOMATIZACE PROCESŮ.**
1. Klikněte na tlačítko **Procházet galerii.**
1. Vyhledejte "Akci sledovacího procesu" a vyberte **akce Sledovací proces, která zpracovává události spouštěné souborem runbook watcheru,** a vyberte **Importovat**.
  ![Importovat runbook akcí z ui](media/automation-watchers-tutorial/importsourceaction.png)
1. Pojmenujte a popis sady Runbook a výběrem **možnosti OK** můžete runbook importovat do svého účtu Automation.
1. Vyberte **Upravit** a klepněte na **tlačítko Publikovat**. Po zobrazení výzvy vyberte **Ano** pro publikování runbooku.

## <a name="create-a-watcher-task"></a>Vytvoření úlohy sledovacího procesu

Úloha sledovacího procesu obsahuje dvě části. Pozorovatel a akce. Sledovací proces běží v intervalu definovaném v úloze sledovacího procesu. Data z runbooku sledovacího procesu jsou předána do runbooku akcí. V tomto kroku nakonfigurujete úlohu sledovacího procesu odkazující na sledovací modul a sady Runbook akce definované v předchozích krocích.

1. Přejděte na svůj účet automatizace a vyberte **sledovací procesy** v kategorii **AUTOMATIZACE PROCESŮ.**
1. Vyberte stránku Úkoly sledovacího procesu a klikněte na + Přidat tlačítko **úkolu sledovacího procesu.**
1. Jako název zadejte "WatchMyFolder".

1. Vyberte **Konfigurovat sledovací proces** a vyberte runbook **Watch-NewFile.**

1. Pro parametry zadejte následující hodnoty:

   * **FOLDERPATH** - Složka na hybridní pracovník, kde se vytvoří nové soubory. d:\examplefiles
   * **ROZŠÍŘENÍ** - Ponechat prázdné zpracovat všechny přípony souborů.
   * **RECURSE** - Ponechat tuto hodnotu jako výchozí.
   * **RUN SETTINGS** - Vyberte hybridního pracovníka.

1. Klepněte na tlačítko OK a potom vyberte, chcete-li se vrátit na stránku sledovacího procesu.
1. Vyberte **Konfigurovat akci** a vyberte runbook "Process-NewFile".
1. Pro parametry zadejte následující hodnoty:

   * **EVENTDATA** - Ponechte prázdné. Data jsou předávána z runbooku sledovacího procesu.
   * **Spusťte nastavení** – ponechte jako Azure, jak tento runbook běží ve službě Automatizace.

1. Klepněte na **tlačítko OK**a potom vyberte, chcete-li se vrátit na stránku sledovacího procesu.
1. Chcete-li vytvořit úlohu sledovacího procesu, klepněte na tlačítko **OK.**

![Konfigurace akce sledovacího procesu z ui](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Spuštění sledovacího procesu

Chcete-li otestovat, že sledovací proces funguje podle očekávání, je třeba vytvořit testovací soubor.

Vzdálené do hybridního pracovníka. Otevřete **PowerShell** a vytvořte testovací soubor ve složce.

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

1. Přejděte na svůj účet automatizace a vyberte **sledovací procesy** v kategorii **AUTOMATIZACE PROCESŮ.**
1. Vyberte úkol sledovacího procesu "WatchMyFolder".
1. Klikněte na **Zobrazit datové proudy sledovacích modulů** v části **Datové proudy, abyste zjistili,** že pozorovatel našel nový soubor a spustil runbook akcí.
1. Chcete-li zobrazit úlohy runbooku akcí, klikněte na **zobrazit úlohy akce sledovacího procesu**. Každá úloha může být vybrána jako zobrazení podrobností o úloze.

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

