---
title: Správa runbooků v Azure Automation
description: Tento článek popisuje, jak spravovat Runbooky v Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: e2f66f94415b7a10fe540cf1c796f4b93349895a
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855479"
---
# <a name="manage-runbooks-in-azure-automation"></a>Správa runbooků v Azure Automation

Runbook můžete přidat do Azure Automation tím, že [vytvoříte nový](#create-a-runbook) nebo [naimportujete existující](#import-a-runbook) soubor ze souboru nebo [Galerie runbooků](automation-runbook-gallery.md). Tento článek poskytuje informace o vytváření a importování runbooků ze souboru. Všechny podrobnosti o přístupu k sadám Runbook a modulům v galerii runbooků [a modulům](automation-runbook-gallery.md)můžete získat Azure Automation.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pro váš účet Automation můžete aktualizovat moduly na nejnovější verzi pomocí [postupu aktualizace modulů Azure PowerShell v Azure Automation](automation-update-azure-modules.md).

## <a name="create-a-runbook"></a>Vytvoření runbooku

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

## <a name="import-a-runbook"></a>Import Runbooku

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
8. Runbook je nutné [publikovat](#publish-a-runbook) před tím, než je možné jej spustit.

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

## <a name="handle-resources"></a>Zpracování prostředků

Pokud vaše sada Runbook vytvoří prostředek, skript by měl ověřit, zda prostředek již existuje, než se pokusí ho vytvořit. Tady je základní příklad.

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exist, creating"
    New-AzVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

## <a name="retrieve-details-from-activity-log"></a>Načíst podrobnosti z protokolu aktivit

Můžete načíst podrobnosti sady Runbook, jako je osoba nebo účet, který Runbook spustil, z protokolu aktivit pro účet Automation. Následující příklad prostředí PowerShell poskytuje poslednímu uživateli spuštění zadané sady Runbook.

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="track-progress"></a>Sledování průběhu

Je dobrým zvykem vytvářet Runbooky, které mají být v podstatě, s logikou, kterou je možné snadno použít a snadno restartovat. Sledování průběhu v sadě Runbook je dobrým způsobem, jak zajistit, že se logika sady Runbook spustí správně, pokud dojde k problémům. Je možné sledovat průběh Runbooku pomocí externího zdroje, jako je například účet úložiště, databáze nebo sdílené soubory. Můžete vytvořit logiku v Runbooku, abyste nejdřív zkontrolovali stav poslední provedené akce. V závislosti na výsledku kontroly může logika přeskočit nebo pokračovat v sadě Runbook konkrétní úkoly.

## <a name="prevent-concurrent-jobs"></a>Zabránit souběžným úlohám

Některé Runbooky se chovají nezvykle, pokud jsou spouštěny v několika úlohách současně. V tomto případě je důležité, aby sada Runbook implementovala logiku k určení, zda již existuje spuštěná úloha. Tady je základní příklad.

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzContext $AzureContext

# Check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

## <a name="handle-transient-errors-in-a-time-dependent-script"></a>Zpracování přechodných chyb v rámci skriptu závislého na čase

Vaše Runbooky musí být robustní a schopné zpracovat přechodné chyby, které mohou způsobit restartování nebo selhání. Pokud se sada Runbook nezdařila, Azure Automation ji znovu pokusí.

Pokud se sada Runbook obvykle spouští v rámci časového omezení, skript implementuje logiku pro kontrolu doby spuštění. Tato kontrolu zajistí spouštění operací, jako je spuštění, vypnutí nebo horizontální navýšení kapacity, jenom během určitých časů.

> [!NOTE]
> Místní čas v procesu izolovaného prostoru (sandbox) Azure je nastavený na UTC. Výpočty pro datum a čas ve vašich sadách Runbook musí tuto skutečnost vzít v úvahu.

## <a name="work-with-multiple-subscriptions"></a>Práce s několika předplatnými

Aby bylo možné řešit více předplatných, musí sada Runbook používat rutinu [Disable-AzContextAutosave](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) . Tato rutina zajišťuje, že se kontext ověřování nenačte z jiné sady Runbook spuštěné ve stejném izolovaném prostoru (sandbox). Sada Runbook používá také`AzContext` parametr na rutinách AZ Module a předá jí správný kontext.

```powershell
# Ensures that you do not inherit an AzContext in your runbook
Disable-AzContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationId $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

## <a name="work-with-a-custom-script"></a>Práce s vlastním skriptem

Na hostiteli nemůžete normálně spouštět vlastní skripty a runbooky s nainstalovaným agentem Log Analytics. Pokud je to nutné, postupujte takto:

1. Vytvořte účet Automation a získejte roli přispěvatele.
2. [Připojte účet k pracovnímu prostoru Azure](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection.md).
3. Povolí Hybrid Runbook Worker, Update Management nebo jinou funkci automatizace. 
4. Pokud máte na počítači se systémem Linux, budete potřebovat vysoké oprávnění. Přihlaste se a [vypněte kontroly podpisů](automation-linux-hrw-install.md#turn-off-signature-validation).

## <a name="test-a-runbook"></a>Testování runbooku

Při testování Runbooku se spustí [koncept verze](#publish-a-runbook) a všechny akce, které provádí, se dokončí. Nevytvoří se žádná historie úlohy, ale Stream [výstupů](automation-runbook-output-and-messages.md#output-stream) a [Upozornění a chyb](automation-runbook-output-and-messages.md#message-streams) se zobrazí v podokně výstup testu. Zprávy do [podrobného datového proudu](automation-runbook-output-and-messages.md#message-streams) se zobrazí v podokně výstup pouze v případě, že je proměnná [VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) nastavena `Continue`na hodnotu.

I když je verze konceptu spuštěna, sada Runbook se stále provádí normálně a provede všechny akce s prostředky v prostředí. Z tohoto důvodu byste měli testovat pouze Runbooky na neprodukčních prostředcích.

Postup testování každého [typu Runbooku](automation-runbook-types.md) je stejný. Při testování mezi textovým editorem a grafickým editorem v Azure Portal nedochází k žádnému rozdílu.

1. Otevřete verzi konceptu sady Runbook v [textovém editoru](automation-edit-textual-runbook.md) nebo v [grafickém editoru](automation-graphical-authoring-intro.md).
1. Kliknutím na tlačítko **test** otevřete stránku test.
1. Pokud má sada Runbook parametry, jsou uvedeny v levém podokně, kde můžete zadat hodnoty, které mají být použity pro test.
1. Pokud chcete spustit test na [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md), změňte **nastavení spuštění** na **Hybrid Worker** a vyberte název cílové skupiny.  V opačném případě ponechte výchozí **Azure** spustit test v cloudu.
1. Kliknutím na tlačítko **Spustit** spusťte test.
1. Pomocí tlačítek v podokně výstup můžete zastavit nebo pozastavit [pracovní postup PowerShellu](automation-runbook-types.md#powershell-workflow-runbooks) nebo [grafický](automation-runbook-types.md#graphical-runbooks) Runbook při jeho testování. Když Runbook pozastavíte, dokončí aktuální aktivitu a teprve pak se pozastaví. Pozastavený Runbook nejde zastavit ani znova spustit.
1. Zkontrolujte výstup Runbooku v podokně výstup.

## <a name="publish-a-runbook"></a>Publikování Runbooku

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

## <a name="schedule-a-runbook-in-the-azure-portal"></a>Naplánování runbooku v Azure Portal

Po publikování můžete Runbook naplánovat na jeho provoz.

1. Otevřete Runbook v Azure Portal.
2. V části **prostředky**vyberte **plány** .
3. Vyberte **Přidat plán**.
4. V podokně naplánovat Runbook vyberte **připojit plán k sadě Runbook**.
5. V podokně plán vyberte **vytvořit nový plán** .
6. Zadejte název, popis a další parametry v podokně nový plán. 
7. Po vytvoření plánu ho zvýrazněte a klikněte na **OK**. Měla by být teď propojená s vaší sadou Runbook.
8. Vyhledejte v poštovní schránce e-mail, který vás upozorní na stav Runbooku.

## <a name="obtain-job-statuses"></a>Získat stavy úlohy

### <a name="view-statuses-in-the-azure-portal"></a>Zobrazit stavy v Azure Portal

Napravo od vybraného účtu Automation uvidíte souhrn všech úloh sady Runbook na dlaždici **Statistika úlohy** . Podrobnosti o zpracování úloh v Azure Automation jsou k dispozici ve službě [Runbook Execution v Azure Automation](automation-runbook-execution.md#jobs).

![Dlaždice statistiky úlohy](./media/manage-runbooks/automation-account-job-status-summary.png)

Tato dlaždice zobrazuje počet a grafické znázornění stavu úlohy pro každou spuštěnou úlohu.

Kliknutím na dlaždici zobrazíte stránku úlohy, která obsahuje souhrnný seznam všech spuštěných úloh. Tato stránka zobrazuje stav, název sady Runbook, čas spuštění a čas dokončení pro každou úlohu.

![Stránka úlohy účtu Automation](./media/manage-runbooks/automation-account-jobs-status-blade.png)

Seznam úloh můžete filtrovat výběrem možnosti **filtrovat úlohy**. Vyfiltrujte konkrétní sadu Runbook, stav úlohy nebo volbu v rozevíracím seznamu a zadejte časový rozsah hledání.

![Filtrovat stav úlohy](./media/manage-runbooks/automation-account-jobs-filter.png)

Případně můžete zobrazit souhrn podrobností úlohy pro konkrétní Runbook tak, že na stránce sady Runbook v účtu Automation vyberete tuto sadu Runbook a pak vyberete dlaždici **úlohy** . Tato akce zobrazí stránku úlohy. Odtud můžete kliknutím na záznam úlohy zobrazit jeho podrobnosti a výstup.

![Stránka úlohy účtu Automation](./media/manage-runbooks/automation-runbook-job-summary-blade.png)

### <a name="retrieve-job-statuses-using-powershell"></a>Načtení stavů úloh pomocí PowerShellu

Pomocí rutiny [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0) můžete načíst úlohy vytvořené pro Runbook a podrobnosti konkrétní úlohy. Pokud sadu Runbook spustíte pomocí `Start-AzAutomationRunbook`prostředí PowerShell, vrátí výslednou úlohu. K načtení výstupu úlohy použijte [příkaz Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) .

Následující příklad načte poslední úlohu ukázkového Runbooku a zobrazí její stav, hodnoty zadané pro parametry Runbooku a výstup úlohy.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

Následující příklad načte výstup pro konkrétní úlohu a vrátí každý záznam. Pokud existuje výjimka pro jeden ze záznamů, skript místo hodnoty zapíše výjimku. Toto chování je užitečné, protože výjimky mohou poskytnout další informace, které nemusí být během výstupu protokolovány normálně.

```azurepowershell-interactive
$output = Get-AzAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="next-steps"></a>Další kroky

* Další informace o spuštění sad Runbook naleznete v tématu [Spuštění Runbooku v Azure Automation](automation-runbook-execution.md).
* Pokud se chcete dozvědět, jak můžete využít galerii modulů Runbook a PowerShellu, přečtěte si téma [Galerie runbooků a modulů pro Azure Automation](automation-runbook-gallery.md).
* Další informace o úpravách runbooků PowerShellu a PowerShellového pracovního postupu pomocí textového editoru najdete [v tématu Úprava textových runbooků v Azure Automation](automation-edit-textual-runbook.md).
* Další informace o vytváření grafického Runbooku najdete v tématu věnovaném [vytváření grafických postupů v Azure Automation](automation-graphical-authoring-intro.md).
