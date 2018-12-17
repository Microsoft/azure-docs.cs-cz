---
title: Vytváření úlohy sledovacího procesu v účtu Azure Automation.
description: Zjistěte, jak vytvořit úlohu sledovacího procesu v účtu Azure Automation, který chcete sledovat dostupnost nových souborů ve složce.
services: automation
ms.service: automation
ms.component: process-automation
author: eamonoreilly
ms.author: eamono
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 4068ce42babb786ca33e1b0d41fdfefc7d3f78be
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52282768"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Vytvoření Azure Automation watcher úkolů ke sledování změn souborů na místním počítači

Azure Automation používá sledovat události a akce s Powershellovými runbooky aktivovat úlohy sledovacího procesu služby. Tento kurz vás provede vytvořením úlohy sledovacího procesu chcete sledovat, když se přidá nový soubor do adresáře.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Importovat runbook sledovacích procesů
> * Vytvoření proměnné Automation
> * Vytvoření runbooku akce
> * Vytvoření úlohy sledovacího procesu
> * Aktivační události sledování
> * Zkontrolujte výstup

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující:

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](automation-offering-get-started.md) pro uložení sledovací proces, runbooky akcí a úlohu sledovacího procesu.
* A [procesu hybrid runbook worker](automation-hybrid-runbook-worker.md) kde běží úlohy sledovacího procesu.

## <a name="import-a-watcher-runbook"></a>Importovat runbook sledovacích procesů

Tento kurz používá runbook sledovacích procesů s názvem **Watch NewFile** nových souborů v adresáři. Runbook sledovacích procesů načte čas poslední známé zápis do souborů ve složce a zobrazuje všechny soubory, které jsou novější než tuto mez. V tomto kroku importujete této sady runbook do účtu automation.

1. Otevřete svůj účet Automation a klikněte **sady Runbook** stránky.
2. Klikněte na **procházet galerii** tlačítko.
3. Vyhledejte "Runbook sledovacích procesů", vyberte **runbook sledovacích procesů, která hledá nové soubory v adresáři** a vyberte **Import**.
  ![Importovat automation runbook z uživatelského rozhraní](media/automation-watchers-tutorial/importsourcewatcher.png)
1. Dejte runbooku název a popis a vyberte **OK** a importujte runbook do účtu Automation.
1. Vyberte **upravit** a potom klikněte na tlačítko **publikovat**. Při zobrazení výzvy vyberte **Ano** Publikovat sadu runbook.

## <a name="create-an-automation-variable"></a>Vytvoření proměnné Automation

[Proměnná služby automation](automation-variables.md) se používá k ukládání časová razítka, která čte předchozí sadu runbook a ukládá z každého souboru.

1. Vyberte **proměnné** pod **SDÍLENÉ prostředky** a vyberte **+ přidat proměnnou**.
1. Jako název zadejte "Watch-NewFileTimestamp"
1. Vyberte pro typ DateTime.
1. Klikněte na **vytvořit** tlačítko. Tím se vytvoří proměnná služby automation.

## <a name="create-an-action-runbook"></a>Vytvoření runbooku akce

Runbook služby akce slouží k práci s daty do něho předaný ze sady runbook sledovacích procesů v úlohy sledovacího procesu. Runbooky pracovních postupů Powershellu nepodporuje úlohy sledovacího procesu služby, je nutné použít Powershellové runbooky. V tomto kroku aktualizujete importovat runbook předem definované akce s názvem "Proces NewFile".

1. Přejděte do účtu automation a vyberte **sady Runbook** pod **AUTOMATIZACE PROCESŮ** kategorie.
1. Klikněte na **procházet galerii** tlačítko.
1. Vyhledejte "Akce sledovacího procesu" a vyberte **akce sledovacího procesu, který zpracovává události aktivované runbook sledovacích procesů** a vyberte **Import**.
  ![Importovat runbook akce z uživatelského rozhraní](media/automation-watchers-tutorial/importsourceaction.png)
1. Dejte runbooku název a popis a vyberte **OK** a importujte runbook do účtu Automation.
1. Vyberte **upravit** a potom klikněte na tlačítko **publikovat**. Při zobrazení výzvy vyberte **Ano** Publikovat sadu runbook.

## <a name="create-a-watcher-task"></a>Vytvoření úlohy sledovacího procesu

Úloha sledovacího procesu obsahuje dvě části. Sledovací proces a akce. Sledovací proces se spustí v intervalu definovaném v úlohy sledovacího procesu. Data ze sady runbook sledovacích procesů se předaly akci sady runbook. V tomto kroku nakonfigurujete úlohy sledovacího procesu odkazující na runbook sledovacích procesů a akce definované v předchozích krocích.

1. Přejděte do účtu automation a vyberte **úlohy sledovacího procesu služby** pod **AUTOMATIZACE PROCESŮ** kategorie.
1. Vyberte na stránce úlohy sledovacího procesu a klikněte na **+ přidat úlohu sledovacího procesu** tlačítko.
1. Jako název zadejte "WatchMyFolder".

1. Vyberte **sledovacího procesu konfigurace** a vyberte **Watch NewFile** sady runbook.

1. Zadejte následující hodnoty pro parametry:

   * **FOLDERPATH** – kde nové soubory vytvořené složky v procesu hybrid worker. d:\examplefiles
   * **ROZŠÍŘENÍ** – ponechte prázdné, pokud chcete zpracovat všechny přípony souborů.
   * **RECURSE** – ponechte tuto hodnotu jako výchozí.
   * **Parametry běhu** -vybrat hybridní pracovní proces.

1. Klikněte na tlačítko OK a pak vyberte se vrátíte na stránku sledovacích procesů.
1. Vyberte **konfigurace akce** a vyberte sadu runbook "Proces NewFile".
1. Zadejte následující hodnoty pro parametry:

   ***EVENTDATA** – ponechte prázdné. Data je předáno ze sady runbook sledovacích procesů.  
   ***Nastavení spuštění** ** – Azure, ponechte tato sada runbook běží ve službě Automation.

1. Klikněte na tlačítko **OK**a pak vyberte se vrátíte na stránku sledovacích procesů.
1. Klikněte na tlačítko **OK** k vytvoření úlohy sledovacího procesu.

![Konfigurace akce sledovacího procesu z uživatelského rozhraní](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Aktivační události sledování

K otestování sledovací proces funguje podle očekávání, je potřeba vytvořit testovací soubor.

Vzdáleně se připojte k procesu hybrid worker. Otevřít **Powershellu** a vytvořit testovací soubor ve složce.
  
```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

Následující příklad zobrazuje očekávaný výstup.

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>Zkontrolujte výstup

1. Přejděte do účtu automation a vyberte **úlohy sledovacího procesu služby** pod **AUTOMATIZACE PROCESŮ** kategorie.
1. Vyberte úlohy sledovacího procesu "WatchMyFolder".
1. Klikněte na **zobrazit streamy sledovacího procesu** pod **datové proudy** zobrazíte, že sledovací proces služby byl nalezen nový soubor a spuštění runbooku akce.
1. Chcete-li zobrazit akce úlohy runbooku, klikněte na **zobrazit úlohy akce sledovacího procesu**. Každá úloha může být vybrána v zobrazení Podrobnosti o úloze.

   ![Úlohy akce sledovacího procesu z uživatelského rozhraní](media/automation-watchers-tutorial/WatcherActionJobs.png)

Očekávaný výstup, když se najde nový soubor můžete vidět v následujícím příkladu:

```output
Message is Process new file...



Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Importovat runbook sledovacích procesů
> * Vytvoření proměnné Automation
> * Vytvoření runbooku akce
> * Vytvoření úlohy sledovacího procesu
> * Aktivační události sledování
> * Zkontrolujte výstup

Na tomto odkazu Další informace o vytváření vlastního runbooku.

> [!div class="nextstepaction"]
> [Můj první Powershellový runbook](automation-first-runbook-textual-powershell.md).
