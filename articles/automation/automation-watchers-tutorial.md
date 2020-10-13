---
title: Sledování aktualizovaných souborů pomocí úlohy sledovacího procesu Azure Automation
description: V tomto článku se dozvíte, jak vytvořit úlohu sledovacího procesu v účtu Azure Automation ke sledování nových souborů vytvořených ve složce.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 38963a8e1bfdbde50439ed871aa33e9aaa830d35
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185649"
---
# <a name="track-updated-files-with-a-watcher-task"></a>Sledování aktualizovaných souborů pomocí úlohy sledovacího procesu

Azure Automation používá úlohu sledovacího procesu k vyhledání událostí a triggeru akcí pomocí PowerShellových runbooků. Úloha sledovacího procesu obsahuje dvě části, sledovací proces a akci. Sada Runbook sledovacího procesu se spouští v intervalu definovaném v úloze sledovacího procesu a výstupy dat do sady Runbook akce. 

> [!NOTE]
> Úlohy sledovacího procesu nejsou podporované v Azure Čína Vianet 21.

> [!IMPORTANT]
> Od května 2020 je pro použití Azure Logic Apps podporovaný způsob, jak monitorovat události, plánovat opakující se úlohy a aktivovat akce. Přečtěte si téma [plánování a spouštění opakujících se automatizovaných úloh, procesů a pracovních postupů s Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

V tomto kurzu se seznámíte s vytvořením úlohy sledovacího procesu, který se bude monitorovat při přidání nového souboru do adresáře. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Import Runbooku sledovacího procesu
> * Vytvoření proměnné automatizace
> * Vytvoření Runbooku akce
> * Vytvoření úlohy sledovacího procesu
> * Aktivace sledovacího procesu
> * Kontrola výstupu

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující:

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet služby Automation](./index.yml) , který bude obsahovat sledovací proces a runbooky akcí a úlohu sledovacího procesu.
* [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) , ve kterém je spuštěna úloha sledovacího procesu.
* PowerShellové Runbooky. Úlohy sledovacích procesů nepodporují Runbooky pracovního postupu PowerShellu.

## <a name="import-a-watcher-runbook"></a>Import Runbooku sledovacího procesu

V tomto kurzu se k vyhledání nových souborů v adresáři používá Runbook sledovacího procesu s názvem **Watch-NewFile** . Sada Runbook sledovacích procesů načte poslední známou dobu zápisu do souborů ve složce a podívá se na všechny soubory, které jsou novější než tento vodoznak.

Tento proces importu lze provést prostřednictvím [Galerie prostředí PowerShell](https://www.powershellgallery.com).

1. Pro [Watch-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd)přejděte na stránku galerie.
2. Na kartě **Azure Automation** klikněte na **nasadit a Azure Automation**.

Tento Runbook můžete také naimportovat do účtu Automation z portálu pomocí následujících kroků.

1. Otevřete svůj účet Automation a klikněte na stránku sady Runbook.
2. Klikněte na **Procházet galerii**.
3. Vyhledejte **Runbook sledovacího**procesu, vyberte **Runbook sledovacího procesu, který hledá nové soubory v adresáři**, a klikněte na **importovat**.
  ![Import Runbooku Automation z uživatelského rozhraní](media/automation-watchers-tutorial/importsourcewatcher.png)
4. Dejte Runbooku název a popis a kliknutím na **OK** importujte Runbook do svého účtu Automation.
5. Vyberte **Upravit** a pak klikněte na **publikovat**. Po zobrazení výzvy klikněte na **Ano** pro publikování Runbooku.

## <a name="create-an-automation-variable"></a>Vytvoření proměnné automatizace

[Proměnná automatizace](./shared-resources/variables.md) slouží k ukládání časových razítek, která předchozí sada Runbook čte a ukládá z každého souboru.

1. V části **sdílené prostředky** vyberte **proměnné** a klikněte na **+ přidat proměnnou**.
1. Jako název zadejte Watch-NewFileTimestamp.
1. Pro typ vyberte datum a čas.
1. Kliknutím na **vytvořit** Vytvořte proměnnou automatizace.

## <a name="create-an-action-runbook"></a>Vytvoření Runbooku akce

V úloze sledovacího procesu se používá Runbook akce, který slouží k tomu, aby se data předala ze sady Runbook sledovacích procesů. Z [Galerie prostředí PowerShell](https://www.powershellgallery.com)musíte importovat předdefinovanou sadu Runbook s názvem **Process-NewFile** . 

Postup vytvoření sady Runbook akce:

1. Pro [Process-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf)přejděte na stránku galerie.
2. Na kartě **Azure Automation** klikněte na **nasadit a Azure Automation**.

Tento Runbook můžete také naimportovat do účtu Automation z Azure Portal:

1. Přejděte do svého účtu Automation a v části **Automatizace procesu**vyberte **Runbooky** .
1. Klikněte na **Procházet galerii**.
1. Vyhledejte **akci sledovacího**procesu, vyberte **akci sledovacího procesu, která zpracovává události aktivované sadou sledovacích procesů**, a klikněte na **importovat**.
  ![Importovat Runbook akce z uživatelského rozhraní](media/automation-watchers-tutorial/importsourceaction.png)
1. Dejte Runbooku název a popis a kliknutím na **OK** importujte Runbook do svého účtu Automation.
1. Vyberte **Upravit** a pak klikněte na **publikovat**. Po zobrazení výzvy klikněte na **Ano** pro publikování Runbooku.

## <a name="create-a-watcher-task"></a>Vytvoření úlohy sledovacího procesu

V tomto kroku nakonfigurujete úlohu sledovacího procesu odkazující na sledovací proces a runbooky akcí, které jsou definovány v předchozích částech.

1. Přejděte do svého účtu Automation a v části **Automatizace procesu**vyberte **úlohy sledovacích** procesů.
1. Vyberte stránku úlohy sledovacího procesu a klikněte na **+ Přidat úlohu sledovacího**procesu.
1. Jako název zadejte **WatchMyFolder** .

1. Vyberte **Konfigurovat sledovací** proces a zvolte Runbook **Watch-NewFile** .

1. Zadejte následující hodnoty parametrů:

   * **FOLDERPATH** – složka na Hybrid Runbook Worker, kde se vytvoří nové soubory, například **d:\examplefiles**.
   * Rozšíření **rozšíření pro** konfiguraci. Ponechte prázdné pro zpracování všech přípon souborů.
   * **REkurze** – rekurzivní operace. Tuto hodnotu nechte jako výchozí.
   * **Nastavení spuštění** – nastavení pro spuštění sady Runbook. Vyberte hybridní pracovní proces.

1. Klikněte na **OK**a pak **Vyberte** , abyste se vrátili na stránku sledovacího procesu.
1. Vyberte **Konfigurovat akci** a zvolte Runbook **Process-NewFile** .
1. Zadejte následující hodnoty parametrů:

   * **EventData** – data události. Ponechte prázdné. Data se předávají ze sady Runbook sledovacích procesů.
   * **Nastavení spuštění** – nastavení pro spuštění sady Runbook. Ponechte jako Azure, protože tato sada Runbook běží v Azure Automation.

1. Klikněte na **OK**a pak **Vyberte** , abyste se vrátili na stránku sledovacího procesu.
1. Kliknutím na **OK** vytvořte úlohu sledovacího procesu.

![Konfigurace akce sledovacího procesu z uživatelského rozhraní](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Aktivace sledovacího procesu

Musíte spustit test, jak je popsáno níže, abyste měli jistotu, že úloha sledovacího procesu funguje podle očekávání. 

1. Vzdálená Hybrid Runbook Worker. 
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

1. Přejděte do svého účtu Automation a v části **Automatizace procesu**vyberte **úlohy sledovacích** procesů.
1. Vyberte úlohu sledovacího procesu **WatchMyFolder**.
1. Kliknutím na **Zobrazit streamy sledovacího** procesu v části **streamy** zjistíte, že sledovací proces našel nový soubor a spustil Runbook akce.
1. Chcete-li zobrazit úlohy sady Runbook akce, klikněte na tlačítko **Zobrazit úlohy sledovacích procesů**. Pro zobrazení podrobností o úloze je možné vybrat každou úlohu.

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
> * Vytvoření úlohy sledovacího procesu
> * Aktivace sledovacího procesu
> * Kontrola výstupu

Pomocí tohoto odkazu se dozvíte více o vytváření vlastních sad Runbook.

> [!div class="nextstepaction"]
> [Vytvoření runbooku v PowerShellu](learn/automation-tutorial-runbook-textual-powershell.md)
