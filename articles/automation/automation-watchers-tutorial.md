---
title: Sledování aktualizovaných souborů pomocí úlohy sledovacího procesu Azure Automation
description: V tomto článku se dozvíte, jak vytvořit úlohu sledovacího procesu v účtu Azure Automation ke sledování nových souborů vytvořených ve složce.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: ca4c4013e0044811a5bac8786996761b8a5c45f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97682755"
---
# <a name="track-updated-files-with-a-watcher-task"></a>Sledování aktualizovaných souborů pomocí úlohy sledovacího procesu

Azure Automation používá úlohu sledovacího procesu k vyhledání událostí a triggeru akcí pomocí PowerShellových runbooků. Úloha sledovacího procesu obsahuje dvě části, sledovací proces a akci. Sada Runbook sledovacího procesu se spouští v intervalu definovaném v úloze sledovacího procesu a výstupy dat do sady Runbook akce.

> [!NOTE]
> Úlohy sledovacího procesu nejsou podporované v Azure Čína Vianet 21.

> [!IMPORTANT]
> Od května 2020 je doporučený a podporovaný způsob, jak monitorovat události, naplánovat opakované úlohy a aktivovat akce pomocí Azure Logic Apps. Přečtěte si téma [plánování a spouštění opakujících se automatizovaných úloh, procesů a pracovních postupů s Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Tento článek vás provede procesem vytvoření úlohy sledovacího procesu, který se bude monitorovat při přidání nového souboru do adresáře. Získáte informace o těchto tématech:

* Import Runbooku sledovacího procesu
* Vytvoření proměnné automatizace
* Vytvoření Runbooku akce
* Vytvoření úlohy sledovacího procesu
* Aktivace sledovacího procesu
* Kontrola výstupu

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku jsou potřeba následující:

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet služby Automation](./index.yml) , který bude obsahovat sledovací proces a runbooky akcí a úlohu sledovacího procesu.
* [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) , ve kterém je spuštěna úloha sledovacího procesu.
* PowerShellové Runbooky. Úlohy sledovacích procesů nepodporují Runbooky pracovního postupu PowerShellu.

## <a name="import-a-watcher-runbook"></a>Import Runbooku sledovacího procesu

Tento článek používá ke hledání nových souborů v adresáři sadu Runbook sledovacích procesů s názvem **Watch-NewFile** . Sada Runbook sledovacích procesů načte poslední známou dobu zápisu do souborů ve složce a podívá se na všechny soubory, které jsou novější než tento vodoznak.

Runbook si můžete stáhnout z [Azure Automation organizaci GitHubu](https://github.com/azureautomation).

1. Přejděte na stránku Azure Automation organizaci GitHubu pro [Watch-NewFile.ps1](https://github.com/azureautomation/watcher-action-that-processes-events-triggerd-by-a-watcher-runbook).
2. Pokud chcete sadu Runbook stáhnout z GitHubu, vyberte na pravé straně stránky **kód** a pak vyberte **Stáhnout ZIP** . tím stáhnete celý kód v souboru ZIP.
3. Extrahujte obsah a [importujte sadu Runbook](manage-runbooks.md#import-a-runbook-from-the-azure-portal).

Tento Runbook můžete také naimportovat do účtu Automation z portálu pomocí následujících kroků.

1. Otevřete svůj účet Automation a klikněte na stránku sady Runbook.
2. Klikněte na **Procházet galerii** a v rozevíracím seznamu **zdroj** vyberte **GitHub**.
3. Vyhledejte **Runbook sledovacího** procesu, vyberte **Runbook sledovacího procesu, který hledá nové soubory v adresáři**, a klikněte na **importovat**.
4. Dejte Runbooku název a popis a kliknutím na **OK** importujte Runbook do svého účtu Automation.
5. Vyberte **Upravit** a pak klikněte na **publikovat**. Po zobrazení výzvy klikněte na **Ano** pro publikování Runbooku.

## <a name="create-an-automation-variable"></a>Vytvoření proměnné automatizace

[Proměnná automatizace](./shared-resources/variables.md) slouží k ukládání časových razítek, která předchozí sada Runbook čte a ukládá z každého souboru.

1. V části **sdílené prostředky** vyberte **proměnné** a klikněte na **+ přidat proměnnou**.
1. Jako název zadejte **Watch-NewFileTimestamp** .
1. Pro typ vyberte datum a čas.
1. Kliknutím na **vytvořit** Vytvořte proměnnou automatizace.

## <a name="create-an-action-runbook"></a>Vytvoření Runbooku akce

V úloze sledovacího procesu se používá Runbook akce, který slouží k tomu, aby se data předala ze sady Runbook sledovacích procesů. Z [Azure Automation organizace GitHubu](https://github.com/azureautomation)musíte importovat předdefinovanou sadu Runbook s názvem **Process-NewFile** .

Postup vytvoření sady Runbook akce:

1. Přejděte na stránku Azure Automation organizaci GitHubu pro [Process-NewFile.ps1](https://github.com/azureautomation/watcher-action-that-processes-events-triggerd-by-a-watcher-runbook).
2. Pokud chcete sadu Runbook stáhnout z GitHubu, vyberte na pravé straně stránky **kód** a pak vyberte **Stáhnout ZIP** . tím stáhnete celý kód v souboru ZIP.
3. Extrahujte obsah a [importujte sadu Runbook](manage-runbooks.md#import-a-runbook-from-the-azure-portal).

Tento Runbook můžete také naimportovat do účtu Automation z Azure Portal:

1. Přejděte do svého účtu Automation a v části **Automatizace procesu** vyberte **Runbooky** .
1. Klikněte na **Procházet galerii** a v rozevíracím seznamu **zdroj** vyberte **GitHub**.
1. Vyhledejte **akci sledovacího** procesu, vyberte **akci sledovacího procesu, která zpracovává události aktivované sadou sledovacích procesů**, a klikněte na **importovat**.
1. Dejte Runbooku název a popis a kliknutím na **OK** importujte Runbook do svého účtu Automation.
1. Vyberte **Upravit** a pak klikněte na **publikovat**. Po zobrazení výzvy klikněte na **Ano** pro publikování Runbooku.

## <a name="create-a-watcher-task"></a>Vytvoření úlohy sledovacího procesu

V tomto kroku nakonfigurujete úlohu sledovacího procesu odkazující na sledovací proces a runbooky akcí, které jsou definovány v předchozích částech.

1. Přejděte do svého účtu Automation a v části **Automatizace procesu** vyberte **úlohy sledovacích** procesů.
1. Vyberte stránku úlohy sledovacího procesu a klikněte na **+ Přidat úlohu sledovacího** procesu.
1. Jako název zadejte **WatchMyFolder** .

1. Vyberte **Konfigurovat sledovací** proces a zvolte Runbook **Watch-NewFile** .

1. Zadejte následující hodnoty parametrů:

   * **FOLDERPATH** – složka na Hybrid Runbook Worker, kde se vytvoří nové soubory, například **d:\examplefiles**.
   * Rozšíření **rozšíření pro** konfiguraci. Ponechte prázdné pro zpracování všech přípon souborů.
   * **REkurze** – rekurzivní operace. Tuto hodnotu nechte jako výchozí.
   * **Nastavení spuštění** – nastavení pro spuštění sady Runbook. Vyberte hybridní pracovní proces.

1. Klikněte na **OK** a pak **Vyberte** , abyste se vrátili na stránku sledovacího procesu.
1. Vyberte **Konfigurovat akci** a zvolte Runbook **Process-NewFile** .
1. Zadejte následující hodnoty parametrů:

   * **EventData** – data události. Ponechte prázdné. Data se předávají ze sady Runbook sledovacích procesů.
   * **Nastavení spuštění** – nastavení pro spuštění sady Runbook. Ponechte jako Azure, protože tato sada Runbook běží v Azure Automation.

1. Klikněte na **OK** a pak **Vyberte** , abyste se vrátili na stránku sledovacího procesu.
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

1. Přejděte do svého účtu Automation a v části **Automatizace procesu** vyberte **úlohy sledovacích** procesů.
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

Další informace o vytváření vlastních sad Runbook najdete v tématu [Vytvoření Runbooku PowerShellu](learn/automation-tutorial-runbook-textual-powershell.md).
