---
title: Správa runbooků v Azure Automation
description: Tento článek popisuje, jak spravovat runbooky v Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: ad2a34691a00f217db6cf6835eefed18c8862d32
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547923"
---
# <a name="manage-runbooks-in-azure-automation"></a>Správa runbooků v Azure Automation

Runbook můžete přidat do Azure Automation buď [vytvořením nového nebo](#create-a-runbook) [importem existujícího](#import-a-runbook) z souboru nebo [Galerie runbooků](automation-runbook-gallery.md). Tento článek obsahuje informace o vytváření a importu runbooků ze souboru. Všechny podrobnosti o přístupu ke komunitním runbookům a modulům můžete získat v [runbooku a galeriích modulů pro Azure Automation](automation-runbook-gallery.md).

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="create-a-runbook"></a>Vytvoření runbooku

Nový runbook můžete vytvořit v Azure Automation pomocí jednoho z portálů Azure nebo Windows PowerShellu. Po vytvoření sady Runbook ji můžete upravit pomocí informací v [pracovním postupu learningového prostředí PowerShell](automation-powershell-workflow.md) a [vytváření grafických funkcí v Azure Automation](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Vytvoření sady Runbook na webu Azure Portal

1. Na webu Azure Portal otevřete účet Automation.
2. V rozbočovači vyberte **Runbooky** v části **Automatizace procesů,** abyste otevřeli seznam runbooků.
3. Klepněte **na tlačítko Vytvořit runbook**.
4. Zadejte název sady Runbook a vyberte jeho [typ](automation-runbook-types.md). Název runbooku musí začínat písmenem a může obsahovat písmena, čísla, podtržítka a pomlčky.
5. Kliknutím na **Vytvořit** vytvořte runbook a otevřete editor.

### <a name="create-a-runbook-with-powershell"></a>Vytvoření runbooku s PowerShellem

Rutinu [New-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) můžete použít k vytvoření prázdné [runbooku pracovního postupu prostředí PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Pomocí `Type` parametru můžete zadat jeden z `New-AzAutomationRunbook`typů runbooků definovaných pro .

Následující příklad ukazuje, jak vytvořit novou prázdnou runbook.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Import sady Runbook

Novou runbook v Azure Automation můžete vytvořit importem skriptu PowerShellu nebo pracovního postupu PowerShellu (**.ps1**), exportovanégrafické sady runbook (**.graphrunbook**) nebo skriptu Pythonu 2 (**.py**).  Je nutné zadat [typ sady Runbook,](automation-runbook-types.md) který je vytvořen během importu, s přihlédnutím k následujícím aspektům.

* Soubor **PS1,** který neobsahuje pracovní postup, lze importovat do [sady Runbook prostředí PowerShell](automation-runbook-types.md#powershell-runbooks) nebo [do sady Runbook workflow prostředí PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Pokud ji importujete do sady runbook pracovního postupu prostředí PowerShell, převede se na pracovní postup. V takovém případě jsou komentáře zahrnuty do runbooku k popisu změn, které byly provedeny.

* Soubor **PS1** obsahující pracovní postup prostředí PowerShell lze importovat pouze do [sady runbook workflow prostředí PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Pokud soubor obsahuje více pracovních postupů prostředí PowerShell, import se nezdaří. Každý pracovní postup je nutné uložit do vlastního souboru a importovat každý zvlášť.

* Soubor **PS1** obsahující pracovní postup prostředí PowerShell by neměl být importován do [sady Runbook prostředí PowerShell](automation-runbook-types.md#powershell-runbooks), protože jej skriptovací stroj Prostředí PowerShell nedokáže rozpoznat.

* Soubor **Graphrunbook** lze importovat pouze do [novégrafické sady Runbook](automation-runbook-types.md#graphical-runbooks). Všimněte si, že můžete vytvořit pouze grafické runbook ze souboru **.graphrunbook.**

### <a name="import-a-runbook-from-a-file-with-the-azure-portal"></a>Import runbooku ze souboru pomocí portálu Azure

Následující postup můžete použít k importu souboru skriptu do Azure Automation.

> [!NOTE]
> Soubor **PS1** lze importovat jenom do runbooku pracovního postupu prostředí PowerShell pomocí portálu.

1. Na webu Azure Portal otevřete účet Automation.
2. V rozbočovači vyberte **Runbooky** v části **Automatizace procesů,** abyste otevřeli seznam runbooků.
3. Klepněte **na položku Importovat sady Runbook**.
4. Klikněte na **Soubor sady Runbook** a vyberte soubor, který chcete importovat.
5. Pokud je pole **Název** povoleno, máte možnost změnit název sady Runbook. Název musí začínat písmenem a může obsahovat písmena, číslice, podtržítka a pomlčky.
6. [Typ runbooku](automation-runbook-types.md) je automaticky vybrán, ale po zohlednění příslušných omezení můžete typ změnit.
7. Klikněte na **Vytvořit**. Nový runbook se zobrazí v seznamu runbooků pro účet Automation.
8. Před [spuštěním runbooku](#publish-a-runbook) je nutné ji publikovat.

> [!NOTE]
> Po importu grafické sady Runbook nebo grafické sady Runbook workflow prostředí PowerShell ji můžete převést na jiný typ. Nelze však převést jednu z těchto grafických sad runbook na textovou runbook.

### <a name="import-a-runbook-from-a-script-file-with-windows-powershell"></a>Import sady Runbook ze souboru skriptu pomocí prostředí Windows PowerShell

Pomocí rutiny [Import-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) importujte soubor skriptu jako koncept runbooku pracovního postupu prostředí PowerShell. Pokud runbook již existuje, import se nezdaří, pokud nepoužijete `Force` parametr s rutinou.

Následující příklad ukazuje, jak importovat soubor skriptu do sady Runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="test-a-runbook"></a>Testování runbooku

Při testování runbooku je spuštěna [verze konceptu](#publish-a-runbook) a všechny akce, které provede, jsou dokončeny. Není vytvořena žádná historie úloh, ale datové [proudy Výstup](automation-runbook-output-and-messages.md#output-stream) a [Upozornění a Chyba](automation-runbook-output-and-messages.md#message-streams) jsou zobrazeny v podokně Výstup test. Zprávy do [datového proudu Verbose](automation-runbook-output-and-messages.md#message-streams) jsou zobrazeny `VerbosePreference` v podokně Výstup pouze v případě, že proměnná](automation-runbook-output-and-messages.md#preference-variables) je nastavena na pokračovat.

I když je verze konceptu spuštěna, runbook se stále provádí normálně a provádí všechny akce proti prostředkům v prostředí. Z tohoto důvodu byste měli testovat pouze runbooky na neprodukční prostředky.

Postup testování každého [typu runbooku](automation-runbook-types.md) je stejný. Neexistuje žádný rozdíl v testování mezi textovým editorem a grafickým editorem na webu Azure Portal.

1. Otevřete verzi konceptu runbooku v [textovém editoru](automation-edit-textual-runbook.md) nebo [grafickém editoru](automation-graphical-authoring-intro.md).
1. Kliknutím na tlačítko **Testovat** otevřete stránku Test.
1. Pokud má runbook parametry, jsou uvedeny v levém podokně, kde můžete zadat hodnoty, které mají být použity pro test.
1. Pokud chcete spustit test na [hybridní runbook worker](automation-hybrid-runbook-worker.md), změňte nastavení **spuštění** na hybridní **pracovník** a vyberte název cílové skupiny.  V opačném případě ponechejte výchozí **Azure** spustit test v cloudu.
1. Klepnutím na tlačítko **Start** zahájíte test.
1. Pomocí tlačítek v podokně Výstup můžete zastavit nebo pozastavit [pracovní postup prostředí PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) nebo [grafickou](automation-runbook-types.md#graphical-runbooks) runbooku během jeho testování. Když Runbook pozastavíte, dokončí aktuální aktivitu a teprve pak se pozastaví. Pozastavený Runbook nejde zastavit ani znova spustit.
1. Zkontrolujte výstup z runbooku v podokně Výstup.

## <a name="publish-a-runbook"></a>Publikování runbooku

Když vytvoříte nebo importujete novou runbook, musíte ji před spuštěním publikovat. Každý runbook v Azure Automation má koncept verze a publikované verze. Spouštět se dá jenom publikovaná verze a upravovat jde jenom koncept. Jakékoliv změny konceptu nemají vliv na publikovanou verzi. Když by měla být verze Koncept k dispozici, publikujete ji a přepíšete aktuální publikovanou verzi verzí Konceptu.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Publikování runbooku na webu Azure Portal

1. Otevřete runbook na webu Azure Portal.
2. Klikněte na **Upravit**.
3. V reakci na ověřovací zprávu klikněte na **Publikovat** a potom na **Ano.**

### <a name="publish-a-runbook-using-powershell"></a>Publikování runbooku pomocí PowerShellu

Pomocí rutiny [Publish-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) publikujte runbook s prostředím Windows PowerShell. Následující příklad ukazuje, jak publikovat ukázkový runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

### <a name="schedule-a-runbook-in-the-azure-portal"></a>Plánování sady Runbook na webu Azure Portal

Po publikování runbooku můžete naplánovat její provoz.

1. Otevřete runbook na webu Azure Portal.
2. V části **Zdroje** **vyberte Možnost Plány** .
3. Vyberte **přidat plán**.
4. V podokně Spustit seznam plán vyberte **Propojit plán se souborem Runbook**.
5. V podokně Plán **zvolte Vytvořit nový plán.**
6. Do podokna Nový plán zadejte název, popis a další parametry. 
7. Po vytvoření plánu jej zvýrazněte a klepněte na tlačítko **OK**. Nyní by měl být propojen s vaší runbook.
8. Vyhledejte e-mail ve své poštovní schránce, který vás upozorní na stav runbooku.

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak můžete využívat runbook a galerii modulů PowerShell, najdete v [tématu Runbook a galerie modulů pro Azure Automation](automation-runbook-gallery.md).
* Další informace o úpravách runbooků PowerShellu a Pracovního postupu prostředí PowerShell pomocí textového editoru najdete [v tématu Úpravy textových runbooků v Azure Automation](automation-edit-textual-runbook.md).
* Další informace o vytváření grafických knih runbooků najdete [v tématu Vytváření grafických funkcí v Azure Automation](automation-graphical-authoring-intro.md).
