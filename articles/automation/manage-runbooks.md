---
title: Správa runbooků v Azure Automation
description: Tento článek popisuje, jak spravovat Runbooky v Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: a1229ee389b41625554fb2869089b08a3cb9cb6d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81676515"
---
# <a name="manage-runbooks-in-azure-automation"></a>Správa runbooků v Azure Automation

Runbook můžete přidat do Azure Automation tím, že [vytvoříte nový](#creating-a-runbook) nebo [naimportujete existující](#importing-a-runbook) soubor ze souboru nebo [Galerie runbooků](automation-runbook-gallery.md). Tento článek poskytuje informace o vytváření a importování runbooků ze souboru. Všechny podrobnosti o přístupu k sadám Runbook a modulům v galerii runbooků [a modulům](automation-runbook-gallery.md)můžete získat Azure Automation.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pro váš účet Automation můžete aktualizovat moduly na nejnovější verzi pomocí [postupu aktualizace modulů Azure PowerShell v Azure Automation](automation-update-azure-modules.md).

## <a name="creating-a-runbook"></a>Vytvoření Runbooku

Novou sadu Runbook můžete vytvořit v Azure Automation pomocí některého z portálů Azure nebo Windows PowerShellu. Až se Runbook vytvoří, můžete ho upravit pomocí informací v části [výuka pracovního postupu prostředí PowerShell](automation-powershell-workflow.md) a [vytváření grafického obsahu v Azure Automation](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Vytvoření Runbooku v Azure Portal

1. Na webu Azure Portal otevřete účet Automation.
2. Z centra vyberte v části **Automatizace procesu** **Runbooky** a otevřete seznam runbooků.
3. Klikněte na **vytvořit Runbook**.
4. Zadejte název Runbooku a vyberte jeho [typ](automation-runbook-types.md). Název Runbooku musí začínat písmenem a může obsahovat písmena, číslice, podtržítka a pomlčky.
5. Kliknutím na **vytvořit** vytvořte Runbook a otevřete Editor.

### <a name="create-a-runbook-with-powershell"></a>Vytvoření Runbooku pomocí PowerShellu

Pomocí rutiny [New-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) můžete vytvořit prázdnou [sadu Runbook pracovního postupu PowerShellu](automation-runbook-types.md#powershell-workflow-runbooks). Pomocí `Type` parametru zadejte jeden z typů runbooků definovaných pro `New-AzAutomationRunbook`.

Následující příklad ukazuje, jak vytvořit novou prázdnou sadu Runbook.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="importing-a-runbook"></a>Import Runbooku

Novou sadu Runbook můžete vytvořit v Azure Automation importem skriptu PowerShellu nebo pracovního postupu PowerShellu (**. ps1**), exportovaného grafického Runbooku (**. graphrunbook**) nebo skriptu Python2 (**. py**).  Je nutné zadat [typ Runbooku](automation-runbook-types.md) , který se vytvoří během importu, přičemž vezme v úvahu následující skutečnosti.

* Soubor **. ps1** , který neobsahuje pracovní postup, můžete importovat buď do [Runbooku PowerShellu](automation-runbook-types.md#powershell-runbooks) , nebo do [Runbooku pracovního postupu PowerShellu](automation-runbook-types.md#powershell-workflow-runbooks). Pokud ho naimportujete do Runbooku pracovního postupu PowerShellu, převede se na pracovní postup. V tomto případě jsou komentáře součástí Runbooku a popisují změny, které byly provedeny.

* Do [Runbooku pracovního postupu PowerShellu](automation-runbook-types.md#powershell-workflow-runbooks)můžete importovat jenom soubor **. ps1,** který obsahuje pracovní postup PowerShellu. Pokud soubor obsahuje několik pracovních postupů PowerShellu, import se nezdařil. Každý pracovní postup musíte uložit do vlastního souboru a naimportovat každý samostatně.

* Neimportujte soubor **. ps1** obsahující pracovní postup PowerShellu do [Runbooku PowerShellu](automation-runbook-types.md#powershell-runbooks), protože ho skriptovací modul PowerShellu nedokáže rozpoznat.

* Soubor **. graphrunbook** můžete importovat pouze do nového [grafického Runbooku](automation-runbook-types.md#graphical-runbooks). Všimněte si, že můžete vytvořit pouze grafický Runbook ze souboru **. graphrunbook** .

### <a name="import-a-runbook-from-a-file-with-the-azure-portal"></a>Import Runbooku ze souboru s Azure Portal

Pomocí následujícího postupu můžete do Azure Automation importovat soubor skriptu.

> [!NOTE]
> Soubor **. ps1** můžete importovat jenom do Runbooku pracovního postupu PowerShellu pomocí portálu.

1. Na webu Azure Portal otevřete účet Automation.
2. Z centra vyberte v části **Automatizace procesu** **Runbooky** a otevřete seznam runbooků.
3. Klikněte na **importovat Runbook**.
4. Klikněte na **soubor sady Runbook** a vyberte soubor, který chcete importovat.
5. Pokud je pole **název** povolené, máte možnost změnit název Runbooku. Název musí začínat písmenem a může obsahovat písmena, číslice, podtržítka a pomlčky.
6. [Typ Runbooku](automation-runbook-types.md) se vybere automaticky, ale po přijetí platných omezení do účtu můžete typ změnit.
7. Klikněte na **Vytvořit**. Nová sada Runbook se zobrazí v seznamu sad Runbook pro účet Automation.
8. Runbook je nutné [publikovat](#publishing-a-runbook) před tím, než je možné jej spustit.

> [!NOTE]
> Po importu grafického Runbooku nebo grafického Runbooku pracovního postupu PowerShellu ho můžete převést na jiný typ. Nemůžete však převést jednu z těchto grafických runbooků na textovou sadu Runbook.

### <a name="import-a-runbook-from-a-script-file-with-windows-powershell"></a>Import Runbooku ze souboru skriptu pomocí Windows PowerShellu

Pomocí rutiny [Import-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) importujte soubor skriptu jako koncept Runbooku pracovního postupu PowerShellu. Pokud sada Runbook již existuje, import se nepovede, pokud nepoužijete `Force` parametr s rutinou.

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

## <a name="testing-a-runbook"></a>Testování Runbooku

Při testování Runbooku se spustí [koncept verze](#publishing-a-runbook) a všechny akce, které provádí, se dokončí. Nevytvoří se žádná historie úlohy, ale Stream [výstupů](automation-runbook-output-and-messages.md#output-stream) a [Upozornění a chyb](automation-runbook-output-and-messages.md#message-streams) se zobrazí v podokně výstup testu. Zprávy do [podrobného datového proudu](automation-runbook-output-and-messages.md#message-streams) se zobrazí v podokně výstup pouze v případě, že je proměnná [VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) nastavena `Continue`na hodnotu.

I když je verze konceptu spuštěna, sada Runbook se stále provádí normálně a provede všechny akce s prostředky v prostředí. Z tohoto důvodu byste měli testovat pouze Runbooky na neprodukčních prostředcích.

Postup testování každého [typu Runbooku](automation-runbook-types.md) je stejný. Při testování mezi textovým editorem a grafickým editorem v Azure Portal nedochází k žádnému rozdílu.

1. Otevřete verzi konceptu sady Runbook v [textovém editoru](automation-edit-textual-runbook.md) nebo v [grafickém editoru](automation-graphical-authoring-intro.md).
1. Kliknutím na tlačítko **test** otevřete stránku test.
1. Pokud má sada Runbook parametry, jsou uvedeny v levém podokně, kde můžete zadat hodnoty, které mají být použity pro test.
1. Pokud chcete spustit test na [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md), změňte **nastavení spuštění** na **Hybrid Worker** a vyberte název cílové skupiny.  V opačném případě ponechte výchozí **Azure** spustit test v cloudu.
1. Kliknutím na tlačítko **Spustit** spusťte test.
1. Pomocí tlačítek v podokně výstup můžete zastavit nebo pozastavit [pracovní postup PowerShellu](automation-runbook-types.md#powershell-workflow-runbooks) nebo [grafický](automation-runbook-types.md#graphical-runbooks) Runbook při jeho testování. Když Runbook pozastavíte, dokončí aktuální aktivitu a teprve pak se pozastaví. Pozastavený Runbook nejde zastavit ani znova spustit.
1. Zkontrolujte výstup Runbooku v podokně výstup.

## <a name="publishing-a-runbook"></a>Publikování Runbooku

Když vytváříte nebo importujete novou sadu Runbook, je nutné ji publikovat, abyste ji mohli spustit. Každá sada Runbook v Azure Automation má verzi konceptu a publikovanou verzi. Spouštět se dá jenom publikovaná verze a upravovat jde jenom koncept. Jakékoliv změny konceptu nemají vliv na publikovanou verzi. V případě, že by měla být verze konceptu dostupná, publikujete ji a přepsat aktuální publikovanou verzi verzí konceptu.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Publikování Runbooku v Azure Portal

1. Otevřete Runbook v Azure Portal.
2. Klikněte na **Upravit**.
3. Klikněte na **publikovat** a **potom v** reakci na ověřovací zprávu potvrďte.

### <a name="publish-a-runbook-using-powershell"></a>Publikování Runbooku pomocí PowerShellu

K publikování Runbooku pomocí prostředí Windows PowerShell použijte rutinu [Publish-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) . Následující příklad ukazuje, jak publikovat ukázkovou sadu Runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="scheduling-a-runbook-in-the-azure-portal"></a>Plánování Runbooku v Azure Portal

Po publikování můžete Runbook naplánovat na jeho provoz.

1. Otevřete Runbook v Azure Portal.
2. V části **prostředky**vyberte **plány** .
3. Vyberte **Přidat plán**.
4. V podokně naplánovat Runbook vyberte **připojit plán k sadě Runbook**.
5. V podokně plán vyberte **vytvořit nový plán** .
6. Zadejte název, popis a další parametry v podokně nový plán. 
7. Po vytvoření plánu ho zvýrazněte a klikněte na **OK**. Měla by být teď propojená s vaší sadou Runbook.
8. Vyhledejte v poštovní schránce e-mail, který vás upozorní na stav Runbooku.

## <a name="next-steps"></a>Další kroky

* Pokud se chcete dozvědět, jak můžete využít galerii modulů Runbook a PowerShellu, přečtěte si téma [Galerie runbooků a modulů pro Azure Automation](automation-runbook-gallery.md).
* Další informace o úpravách runbooků PowerShellu a PowerShellového pracovního postupu pomocí textového editoru najdete [v tématu Úprava textových runbooků v Azure Automation](automation-edit-textual-runbook.md).
* Další informace o vytváření grafického Runbooku najdete v tématu věnovaném [vytváření grafických postupů v Azure Automation](automation-graphical-authoring-intro.md).
