---
title: Vytvoření úlohy sledovacího procesu v účtu Azure Automation
description: Naučte se, jak vytvořit úlohu sledovacího procesu v účtu Azure Automation ke sledování nových souborů vytvořených ve složce.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 5dc6145940883ff6f4446ad67c399cdf4931d38e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75419744"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Vytvoření úloh sledovacího procesu Azure Automation ke sledování změn souborů v místním počítači

Azure Automation využívá úlohy sledovacích procesů ke sledování událostí a akcí triggerů pomocí PowerShellových runbooků. V tomto kurzu se seznámíte s vytvořením úlohy sledovacího procesu, který se bude monitorovat při přidání nového souboru do adresáře.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Import Runbooku sledovacího procesu
> * Vytvoření proměnné automatizace
> * Vytvoření Runbooku akce
> * Vytvořit úlohu sledovacího procesu
> * Aktivace sledovacího procesu
> * Kontrola výstupu

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující:

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet služby Automation](automation-offering-get-started.md) , který bude obsahovat sledovací proces a runbooky akcí a úlohu sledovacího procesu.
* [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) , ve kterém je spuštěna úloha sledovacího procesu.

> [!NOTE]
> Úlohy sledovacího procesu se v Azure Čína nepodporují.

## <a name="import-a-watcher-runbook"></a>Import Runbooku sledovacího procesu

V tomto kurzu se k vyhledání nových souborů v adresáři používá Runbook sledovacího procesu s názvem **Watch-NewFile** . Sada Runbook sledovacích procesů načte poslední známou dobu zápisu do souborů ve složce a podívá se na všechny soubory, které jsou novější než tento vodoznak.

Tento proces importu lze provést prostřednictvím [Galerie prostředí PowerShell](https://www.powershellgallery.com).

1. Přejděte na stránku galerie pro [Watch-NewFile. ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd).
2. Na kartě **Azure Automation** klikněte na **nasadit a Azure Automation**.

Tento Runbook můžete také naimportovat do účtu Automation z portálu pomocí následujících kroků.

1. Otevřete svůj účet Automation a klikněte na stránku sady **Runbook** .
2. Klikněte na tlačítko **Procházet galerii** .
3. Vyhledejte "Runbook sledovacího procesu", vyberte **Runbook sledovacího procesu, který vyhledá nové soubory v adresáři** a vyberte **importovat**.
  ![importu sady Automation Runbook z uživatelského rozhraní](media/automation-watchers-tutorial/importsourcewatcher.png)
1. Dejte Runbooku název a popis a vyberte **OK** , aby se Runbook naimportoval do svého účtu Automation.
1. Vyberte **Upravit** a pak klikněte na **publikovat**. Po zobrazení výzvy vyberte **Ano** pro publikování Runbooku.

## <a name="create-an-automation-variable"></a>Vytvoření proměnné automatizace

[Proměnná automatizace](automation-variables.md) slouží k ukládání časových razítek, která předchozí sada Runbook čte a ukládá z každého souboru.

1. V části **sdílené prostředky** vyberte **proměnné** a vyberte **+ přidat proměnnou**.
1. Jako název zadejte Watch-NewFileTimestamp.
1. Pro typ vyberte datum a čas.
1. Klikněte na tlačítko **vytvořit** . Tím se vytvoří proměnná automatizace.

## <a name="create-an-action-runbook"></a>Vytvoření Runbooku akce

V úloze sledovacího procesu se používá Runbook akce, který slouží k tomu, aby se data předala ze sady Runbook sledovacích procesů. Runbooky pracovních postupů PowerShellu nejsou podporovány úlohami sledovacích procesů, a proto je nutné použít PowerShellové Runbooky. Je nutné importovat předem definovaný Runbook sady s názvem **Process-NewFile**.

Tento proces importu lze provést prostřednictvím [Galerie prostředí PowerShell](https://www.powershellgallery.com).

1. Přejděte na stránku galerie pro [Process-NewFile. ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf).
2. Na kartě **Azure Automation** klikněte na **nasadit a Azure Automation**.

Tento Runbook můžete také naimportovat do účtu Automation z portálu pomocí následujících kroků.

1. Přejděte do svého účtu Automation a v kategorii **Automatizace procesu** vyberte **Runbooky** .
1. Klikněte na tlačítko **Procházet galerii** .
1. Vyhledejte "akce sledovacího procesu" a vyberte **akci sledovacího procesu, která zpracovává události aktivované sadou sledovacích procesů,** a vyberte **importovat**.
  ![importovat Runbook akce z uživatelského rozhraní](media/automation-watchers-tutorial/importsourceaction.png)
1. Dejte Runbooku název a popis a vyberte **OK** , aby se Runbook naimportoval do svého účtu Automation.
1. Vyberte **Upravit** a pak klikněte na **publikovat**. Po zobrazení výzvy vyberte **Ano** pro publikování Runbooku.

## <a name="create-a-watcher-task"></a>Vytvořit úlohu sledovacího procesu

Úloha sledovacího procesu obsahuje dvě části. Sledovací proces a akce. Sledovací proces se spouští v intervalu definovaném v úloze sledovacího procesu. Data z Runbooku sledovacího procesu se předávají do sady Runbook akcí. V tomto kroku nakonfigurujete úlohu sledovacího procesu odkazující na sledovací proces a sady Runbook, které jsou definovány v předchozích krocích.

1. Přejděte do svého účtu Automation a v kategorii **Automatizace procesu** vyberte **úlohy sledovacích** procesů.
1. Vyberte stránku úlohy sledovacího procesu a klikněte na tlačítko **+ Přidat úlohu sledovacího** procesu.
1. Jako název zadejte "WatchMyFolder".

1. Vyberte **Konfigurovat sledovací** proces a vyberte Runbook **Watch-NewFile** .

1. Zadejte následující hodnoty parametrů:

   * **FOLDERPATH** – složka hybridního pracovního procesu, kde se vytvoří nové soubory. d:\examplefiles
   * **Rozšíření** – ponechte prázdné pro zpracování všech přípon souborů.
   * **REkurze** – ponechte tuto hodnotu jako výchozí.
   * **Nastavení spuštění** – vyberte hybridní pracovní proces.

1. Klikněte na OK a pak vyberte, abyste se vrátili na stránku sledovacího procesu.
1. Vyberte **Konfigurovat akci** a vyberte Runbook "proces-NewFile".
1. Zadejte následující hodnoty parametrů:

   * **EventData** – ponechte prázdné. Data se předávají ze sady Runbook sledovacích procesů.
   * **Nastavení spuštění** – ponechte jako Azure, protože tato sada Runbook běží ve službě Automation.

1. Klikněte na **OK**a pak vyberte, abyste se vrátili na stránku sledovacího procesu.
1. Kliknutím na **OK** vytvořte úlohu sledovacího procesu.

![Konfigurace akce sledovacího procesu z uživatelského rozhraní](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Aktivace sledovacího procesu

Chcete-li otestovat sledovací proces, který pracuje podle očekávání, je nutné vytvořit testovací soubor.

Vzdáleně do hybridního pracovního procesu. Otevřete **PowerShell** a vytvořte testovací soubor ve složce.

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

1. Přejděte do svého účtu Automation a v kategorii **Automatizace procesu** vyberte **úlohy sledovacích** procesů.
1. Vyberte úlohu sledovacího procesu "WatchMyFolder".
1. Kliknutím na **Zobrazit streamy sledovacího** procesu v části **streamy** zjistíte, že sledovací proces našel nový soubor a spustil Runbook akce.
1. Chcete-li zobrazit úlohy sady Runbook akce, klikněte na **úlohy zobrazení akce sledovacího**procesu. Každou úlohu můžete vybrat zobrazení podrobností o úloze.

   ![Úlohy akce sledovacího procesu z uživatelského rozhraní](media/automation-watchers-tutorial/WatcherActionJobs.png)

Očekávaný výstup, když je nalezen nový soubor, lze zobrazit v následujícím příkladu:

```output
Message is Process new file...



Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Import Runbooku sledovacího procesu
> * Vytvoření proměnné automatizace
> * Vytvoření Runbooku akce
> * Vytvořit úlohu sledovacího procesu
> * Aktivace sledovacího procesu
> * Kontrola výstupu

Pomocí tohoto odkazu se dozvíte více o vytváření vlastních sad Runbook.

> [!div class="nextstepaction"]
> [Můj první powershellový Runbook](automation-first-runbook-textual-powershell.md)

