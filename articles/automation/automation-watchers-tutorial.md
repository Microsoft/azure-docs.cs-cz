---
title: Vytvoření úlohy sledovacího procesu v účtu Azure Automation.
description: Postup vytvoření úlohy sledovacího procesu v Azure Automation. účet, který chcete sledovat pro nové soubory vytvořené ve složce.
services: automation
ms.service: automation
author: eamonoreilly
ms.author: eamono
ms.topic: article
ms.date: 03/19/2017
ms.openlocfilehash: 8cd5f77d9711ffc95e6a55e97297a23fd87c6bb7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Vytvoření úlohy můžete sledovat změny souboru v místním počítači sledovacího procesu Azure Automation.

Automatizace Azure používá úlohy sledovacího procesu služby a sledovat události spuštění akcí. Tento kurz vás provede procesem vytvoření úlohy sledovacího procesu monitorování při přidání nového souboru do jiného adresáře.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Import sady runbook sledovacích procesů
> * Vytvořte proměnnou automatizace
> * Vytvoření sady runbook akci
> * Vytvoření úlohy sledovacího procesu
> * Aktivační události sledovacích procesů
> * Zkontrolujte výstup

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující:

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](automation-offering-get-started.md) pro uložení sledovacích procesů a akce sad runbook a úlohy sledovacího procesu.
* A [hybridní pracovní proces runbooku](automation-hybrid-runbook-worker.md) kde spuštěna úloha sledovacích procesů.

## <a name="import-a-watcher-runbook"></a>Import sady runbook sledovacích procesů

Tento kurz používá sledovacích procesů runbook s názvem **sledovat NewFile** hledání nových souborů v adresáři. Sada runbook sledovacích procesů načte poslední známé zápis do souborů ve složce a zjistí všechny soubory, které jsou novější než tuto mez. V tomto kroku importujete této sady runbook do vašeho účtu automation.

1. Otevřete svůj účet Automation a klikněte na **Runbooky** stránky.
1. Klikněte na **procházet galerii** tlačítko.
1. Vyhledejte "Sledovacích procesů runbook", vyberte **sledovacích procesů runbooku, který vyhledá nové soubory v adresáři** a vyberte **Import**.
  ![Import sady automation runbook z uživatelského rozhraní](media/automation-watchers-tutorial/importsourcewatcher.png)
1. Dejte runbooku název a popis a vyberte **OK** k importu sady runbook do vašeho účtu Automation.
1. Vyberte **upravit** a pak klikněte na **publikovat**. Při zobrazení výzvy vyberte **Ano** Publikovat sadu runbook.

## <a name="create-an-automation-variable"></a>Vytvořte proměnnou automatizace

[Proměnné automation](automation-variables.md) se používá k ukládání časová razítka, který čte předchozí sady runbook a ukládá z každého souboru. 

1. Vyberte **proměnné** pod **SDÍLENÉ prostředky** a vyberte **+ přidat proměnnou**.
1. Jako název zadejte "Sledovat-NewFileTimestamp"
1. Vyberte pro typ DateTime.
1. Klikněte na **vytvořit** tlačítko. Tím se vytvoří proměnné automation.

## <a name="create-an-action-runbook"></a>Vytvoření sady runbook akci

Runbook služby akce se používá v úloze sledovacích procesů tak, aby fungoval na datech předaný ze sady runbook sledovacích procesů. V tomto kroku aktualizujete import předem definované akce runbook s názvem "Proces-NewFile".

1. Přejděte na svůj účet automation a vyberte **Runbooky** pod **AUTOMATIZACE procesu** kategorie.
1. Klikněte na **procházet galerii** tlačítko.
1. Vyhledejte "Action sledovacích procesů" a vyberte **sledovacích procesů akci, která zpracovává události aktivované sledovacích procesů runbook** a vyberte **Import**.
  ![Importovat runbook akce z uživatelského rozhraní](media/automation-watchers-tutorial/importsourceaction.png)
1. Dejte runbooku název a popis a vyberte **OK** k importu sady runbook do vašeho účtu Automation.
1. Vyberte **upravit** a pak klikněte na **publikovat**. Při zobrazení výzvy vyberte **Ano** Publikovat sadu runbook.

## <a name="create-a-watcher-task"></a>Vytvoření úlohy sledovacího procesu

Sledovací proces úlohy obsahuje dvě části. Sledovacích procesů a akce. Sledovacích procesů se spustí v intervalu definované v úloze sledovacích procesů. Data ze sady runbook sledovacích procesů je předána na runbook akce. V tomto kroku nakonfigurujete úlohy sledovacího procesu odkazování na sady runbook sledovacích procesů a akce definované v předchozích krocích.

1. Přejděte na svůj účet automation a vyberte **úlohy sledovacího procesu** pod **AUTOMATIZACE procesu** kategorie.
1. Vyberte stránku úlohy sledovacího procesu a klikněte na **+ přidat úloha sledovacích procesů** tlačítko.
1. Jako název zadejte "WatchMyFolder".

1. Vyberte **sledovacího procesu konfigurace** a vyberte **sledovat NewFile** sady runbook.

1. Zadejte následující hodnoty pro parametry:

   * **FOLDERPATH** -do složky na hybridní pracovní proces, kde získat vytváří nové soubory. d:\examplefiles
   * **ROZŠÍŘENÍ** -ponechat prázdné zpracovat všechny přípony souborů.
   * **RECURSE** -ponechte tuto hodnotu jako výchozí.
   * **NASTAVENÍ spuštění** -vyberte hybridní pracovní proces.

1. Klikněte na tlačítko OK a pak vyberte návrat na stránku sledovacích procesů.
1. Vyberte **konfigurace akce** a vyberte sadu runbook "Proces-NewFile".
1. Zadejte následující hodnoty pro parametry:

   *    **EVENTDATA** -ponechat prázdné. Data, je předaná ze sady runbook sledovacích procesů.  
   *    **Parametry spuštění** -nechte Azure Tato sada runbook běží ve službě Automation.

1. Klikněte na tlačítko **OK**a pak vyberte návrat na stránku sledovacích procesů.
1. Klikněte na tlačítko **OK** k vytvoření úlohy sledovacího procesu.

![Konfigurace akce sledovacích procesů z uživatelského rozhraní](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Aktivační události sledovacích procesů

K testování sledovacích procesů funguje podle očekávání, budete muset vytvořit testovací soubor.

Vzdálené do hybridní pracovní proces. Otevřete **prostředí PowerShell** a vytvořit testovací soubor ve složce.
  
   ```PowerShell-interactive
   New-Item -Name ExampleFile1.txt
   ```

Následující příklad ukazuje očekávaný výstup.

```
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>Zkontrolujte výstup

1. Přejděte na svůj účet automation a vyberte **úlohy sledovacího procesu** pod **AUTOMATIZACE procesu** kategorie.
1. Vyberte úkol sledovacích procesů "WatchMyFolder".
1. Klikněte na **zobrazit sledovacích procesů datových proudů** pod **datové proudy** zda sledovacích procesů nalezen nový soubor a spuštění runbooku akce.
1. Chcete-li zobrazit úlohy sady runbook akce, klikněte na **zobrazit úlohy sledovacího procesu akce**. Každá úloha může být vybraný zobrazení Podrobnosti úlohy.

   ![Akce úlohy sledovacího procesu z uživatelského rozhraní](media/automation-watchers-tutorial/WatcherActionJobs.png)

Očekávaný výstup, když se najde nový soubor můžete vidět v následujícím příkladu:

```
Message is Process new file...



Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Import sady runbook sledovacích procesů
> * Vytvořte proměnnou automatizace
> * Vytvoření sady runbook akci
> * Vytvoření úlohy sledovacího procesu
> * Aktivační události sledovacích procesů
> * Zkontrolujte výstup

Další informace o vytváření vlastních sad runbook na tento odkaz.

> [!div class="nextstepaction"]
> [Můj první Powershellový runbook](automation-first-runbook-textual-powershell.md).
