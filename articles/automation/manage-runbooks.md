---
title: Správa runbooků v Azure Automation
description: V tomto článku se dozvíte, jak spravovat Runbooky v Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/24/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a172189d8b52a80fc50e7d8c882859f7855aeca8
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830083"
---
# <a name="manage-runbooks-in-azure-automation"></a>Správa runbooků v Azure Automation

Runbook můžete přidat do Azure Automation tím, že vytvoříte nový nebo naimportujete existující soubor ze souboru nebo [Galerie runbooků](automation-runbook-gallery.md). Tento článek poskytuje informace pro správu sady Runbook importované ze souboru. Všechny podrobnosti o přístupu k sadám Runbook a modulům v sadách [Runbook a modulech můžete najít v galerii pro Azure Automation](automation-runbook-gallery.md).

## <a name="create-a-runbook"></a>Vytvoření runbooku

Pomocí Azure Portal nebo Windows PowerShellu vytvořte nový Runbook v Azure Automation. Po vytvoření se Runbook dá upravit pomocí informací v části:

* [Upravit text Runbook v Azure Automation](automation-edit-textual-runbook.md)
* [Informace o klíčových konceptech pracovního postupu prostředí Windows PowerShell pro Runbooky Automation](automation-powershell-workflow.md)
* [Správa balíčků Python 2 v Azure Automation](python-packages.md)
* [Správa balíčků Python 3 (Preview) v Azure Automation](python-3-packages.md)

### <a name="create-a-runbook-in-the-azure-portal"></a>Vytvoření Runbooku v Azure Portal

1. Na webu Azure Portal otevřete účet Automation.
2. Z centra vyberte v části **Automatizace procesu** **Runbooky** a otevřete seznam runbooků.
3. Klikněte na **vytvořit Runbook**.
4. Zadejte název Runbooku a vyberte jeho [typ](automation-runbook-types.md). Název Runbooku musí začínat písmenem a může obsahovat písmena, číslice, podtržítka a pomlčky.
5. Kliknutím na **vytvořit** vytvořte Runbook a otevřete Editor.

### <a name="create-a-runbook-with-powershell"></a>Vytvoření Runbooku pomocí PowerShellu

K vytvoření prázdné sady Runbook použijte rutinu [New-AzAutomationRunbook](/powershell/module/az.automation/new-azautomationrunbook) . Pomocí `Type` parametru zadejte jeden z typů runbooků definovaných pro `New-AzAutomationRunbook` .

Následující příklad ukazuje, jak vytvořit novou prázdnou sadu Runbook.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    Name                  = 'NewRunbook'
    ResourceGroupName     = 'MyResourceGroup'
    Type                  = 'PowerShell'
}
New-AzAutomationRunbook @params
```

## <a name="import-a-runbook"></a>Import Runbooku

Můžete importovat skript PowerShellu nebo PowerShellového pracovního postupu (**. ps1**), grafický Runbook (**. graphrunbook**) nebo skript Python 2 nebo Python 3 (**. py**), abyste mohli vytvořit vlastní Runbook. Je nutné zadat [typ Runbooku](automation-runbook-types.md) , který se vytvoří během importu, přičemž vezme v úvahu následující skutečnosti.

* Soubor **. ps1** , který neobsahuje pracovní postup, můžete importovat buď do [Runbooku PowerShellu](automation-runbook-types.md#powershell-runbooks) , nebo do [Runbooku pracovního postupu PowerShellu](automation-runbook-types.md#powershell-workflow-runbooks). Pokud ho naimportujete do Runbooku pracovního postupu PowerShellu, převede se na pracovní postup. V tomto případě jsou komentáře součástí Runbooku a popisují provedené změny.

* Do [Runbooku pracovního postupu PowerShellu](automation-runbook-types.md#powershell-workflow-runbooks)můžete importovat jenom soubor **. ps1,** který obsahuje pracovní postup PowerShellu. Pokud soubor obsahuje několik pracovních postupů PowerShellu, import se nezdařil. Každý pracovní postup musíte uložit do vlastního souboru a naimportovat každý samostatně.

* Neimportujte soubor **. ps1** obsahující pracovní postup PowerShellu do [Runbooku PowerShellu](automation-runbook-types.md#powershell-runbooks), protože ho skriptovací modul PowerShellu nedokáže rozpoznat.

* Importujte pouze soubor **. graphrunbook** do nového [grafického Runbooku](automation-runbook-types.md#graphical-runbooks).

### <a name="import-a-runbook-from-the-azure-portal"></a>Import Runbooku z Azure Portal

Pomocí následujícího postupu můžete do Azure Automation importovat soubor skriptu.

> [!NOTE]
> Soubor **. ps1** můžete importovat jenom do Runbooku pracovního postupu PowerShellu pomocí portálu.

1. Na webu Azure Portal otevřete účet Automation.
2. V části **Automatizace procesu** vyberte **Runbooky** a otevřete seznam runbooků.
3. Klikněte na **importovat Runbook**.
4. Klikněte na **soubor sady Runbook** a vyberte soubor, který chcete importovat.
5. Pokud je pole **název** povolené, máte možnost změnit název Runbooku. Název musí začínat písmenem a může obsahovat písmena, číslice, podtržítka a pomlčky.
6. [Typ Runbooku](automation-runbook-types.md) se vybere automaticky, ale po přijetí platných omezení do účtu můžete typ změnit.
7. Klikněte na **Vytvořit**. Nová sada Runbook se zobrazí v seznamu sad Runbook pro účet Automation.
8. Runbook je nutné [publikovat](#publish-a-runbook) před tím, než je možné jej spustit.

> [!NOTE]
> Po importu grafického Runbooku ho můžete převést na jiný typ. Grafický Runbook ale nemůžete převést na textový Runbook.

### <a name="import-a-runbook-with-windows-powershell"></a>Import Runbooku pomocí Windows PowerShellu

Pomocí rutiny [Import-AzAutomationRunbook](/powershell/module/az.automation/import-azautomationrunbook) importujte soubor skriptu jako koncept Runbooku. Pokud sada Runbook již existuje, import se nepovede, pokud nepoužijete `Force` parametr s rutinou.

Následující příklad ukazuje, jak importovat soubor skriptu do sady Runbook.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    Name                  = 'Sample_TestRunbook'
    ResourceGroupName     = 'MyResourceGroup'
    Type                  = 'PowerShell'
    Path                  = 'C:\Runbooks\Sample_TestRunbook.ps1'
}
Import-AzAutomationRunbook @params
```

## <a name="handle-resources"></a>Zpracování prostředků

Pokud vaše sada Runbook vytvoří [prostředek](automation-runbook-execution.md#resources), skript by měl ověřit, zda prostředek již existuje, než se pokusí ho vytvořit. Tady je základní příklad.

```powershell
$vmName = 'WindowsVM1'
$rgName = 'MyResourceGroup'
$myCred = Get-AutomationPSCredential 'MyCredential'

$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $rgName
if (-not $vmExists) {
    Write-Output "VM $vmName does not exist, creating"
    New-AzVM -Name $vmName -ResourceGroupName $rgName -Credential $myCred
} else {
    Write-Output "VM $vmName already exists, skipping"
}
```

## <a name="retrieve-details-from-activity-log"></a>Načíst podrobnosti z protokolu aktivit

Můžete načíst podrobnosti o sadě Runbook, jako je osoba nebo účet, který spustil Runbook, z [protokolu aktivit](automation-runbook-execution.md#activity-logging) pro účet Automation. Následující příklad prostředí PowerShell poskytuje poslednímu uživateli spuštění zadané sady Runbook.

```powershell-interactive
$SubID = '00000000-0000-0000-0000-000000000000'
$AutoRgName = 'MyResourceGroup'
$aaName = 'MyAutomationAccount'
$RunbookName = 'MyRunbook'
$StartTime = (Get-Date).AddDays(-1)

$params = @{
    ResourceGroupName = $AutoRgName
    StartTime         = $StartTime
}
$JobActivityLogs = (Get-AzLog @params).Where( { $_.Authorization.Action -eq 'Microsoft.Automation/automationAccounts/jobs/write' })

$JobInfo = @{}
foreach ($log in $JobActivityLogs) {
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($null -eq $JobInfo[$log.SubmissionTimestamp] -and $JobResource.Properties.Runbook.Name -eq $RunbookName) {
        # Get runbook
        $jobParams = @{
            ResourceGroupName     = $AutoRgName
            AutomationAccountName = $aaName
            Id                    = $JobResource.Properties.JobId
        }
        $Runbook = Get-AzAutomationJob @jobParams | Where-Object RunbookName -EQ $RunbookName

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | Sort-Object Key -Descending | Select-Object -First 1
```

## <a name="track-progress"></a>Sledování průběhu

Je dobrým zvykem vytvářet Runbooky, které mají být v podstatě, s logikou, kterou je možné snadno použít a snadno restartovat. Sledování průběhu v Runbooku zajistí, že se logika sady Runbook spustí správně, pokud dojde k problémům.

Průběh Runbooku můžete sledovat pomocí externího zdroje, jako je například účet úložiště, databáze nebo sdílené soubory. Vytvořte v Runbooku logiku, abyste nejdřív zkontrolovali stav poslední provedené akce. V závislosti na výsledcích kontroly může logika přeskočit nebo pokračovat v sadě Runbook konkrétní úkoly.

## <a name="prevent-concurrent-jobs"></a>Zabránit souběžným úlohám

Některé Runbooky se chovají nezvykle, pokud jsou spouštěny v několika úlohách současně. V tomto případě je důležité, aby sada Runbook implementovala logiku k určení, zda již existuje spuštěná úloha. Tady je základní příklad.

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
$cnParams = @{
    ServicePrincipal      = $true
    Tenant                = $connection.TenantId
    ApplicationId         = $connection.ApplicationId
    CertificateThumbprint = $connection.CertificateThumbprint
}
Connect-AzAccount @cnParams
$AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzContext $AzureContext

# Check to see if it is already running
$runningCount = ($jobs.Where( { $_.Status -eq 'Running' })).count

if (($jobs.Status -contains 'Running' -and $runningCount -gt 1 ) -or ($jobs.Status -eq 'New')) {
    # Exit code
    Write-Output "Runbook [$runbookName] is already running"
    exit 1
} else {
    # Insert Your code here
}
```

## <a name="handle-transient-errors-in-a-time-dependent-script"></a>Zpracování přechodných chyb v rámci skriptu závislého na čase

Vaše Runbooky musí být robustní a schopné zpracovávat [chyby](automation-runbook-execution.md#errors), včetně přechodných chyb, které mohou způsobit restartování nebo selhání. Pokud se sada Runbook nezdařila, Azure Automation ji znovu pokusí.

Pokud se sada Runbook obvykle spouští v rámci časového omezení, skript implementuje logiku pro kontrolu doby spuštění. Tato kontrolu zajistí spouštění operací, jako je spuštění, vypnutí nebo horizontální navýšení kapacity, jenom během určitých časů.

> [!NOTE]
> Místní čas v procesu izolovaného prostoru (sandbox) Azure je nastavený na UTC. Výpočty pro datum a čas ve vašich sadách Runbook musí tuto skutečnost vzít v úvahu.

## <a name="work-with-multiple-subscriptions"></a>Práce s několika předplatnými

Sada Runbook musí umožňovat práci s [předplatnými](automation-runbook-execution.md#subscriptions). Například pro zpracování více předplatných sada Runbook používá rutinu [Disable-AzContextAutosave](/powershell/module/Az.Accounts/Disable-AzContextAutosave) . Tato rutina zajišťuje, že se kontext ověřování nenačte z jiné sady Runbook spuštěné ve stejném izolovaném prostoru (sandbox). Sada Runbook také používá `Get-AzContext` rutinu k načtení kontextu aktuální relace a přiřadí ji k proměnné `$AzureContext` .

```powershell
Disable-AzContextAutosave -Scope Process

$connection = Get-AutomationConnection -Name AzureRunAsConnection
$cnParams = @{
    ServicePrincipal      = $true
    Tenant                = $connection.TenantId
    ApplicationId         = $connection.ApplicationId
    CertificateThumbprint = $connection.CertificateThumbprint
}
Connect-AzAccount @cnParams

$ChildRunbookName = 'ChildRunbookDemo'
$aaName = 'MyAutomationAccount'
$rgName = 'MyResourceGroup'

$startParams = @{
    ResourceGroupName     = $rgName
    AutomationAccountName = $aaName
    Name                  = $ChildRunbookName
    DefaultProfile        = $AzureContext
}
Start-AzAutomationRunbook @startParams
```

## <a name="work-with-a-custom-script"></a>Práce s vlastním skriptem

> [!NOTE]
> Na hostiteli nemůžete normálně spouštět vlastní skripty a runbooky s nainstalovaným agentem Log Analytics.

Použití vlastního skriptu:

1. Vytvořte účet Automation a získejte [roli přispěvatele](automation-role-based-access-control.md).
2. [Připojte účet k pracovnímu prostoru Azure](../security-center/security-center-enable-data-collection.md).
3. Povolí [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md), [Update Management](./update-management/overview.md)nebo jinou funkci automatizace. 
4. Pokud máte na počítači se systémem Linux, budete potřebovat vysoké oprávnění. Přihlaste se a [vypněte kontroly podpisů](automation-linux-hrw-install.md#turn-off-signature-validation).

## <a name="test-a-runbook"></a>Testování runbooku

Při testování Runbooku se spustí [koncept verze](#publish-a-runbook) a všechny akce, které provádí, se dokončí. Nevytvoří se žádná historie úlohy, ale Stream [výstupů](automation-runbook-output-and-messages.md#use-the-output-stream) a [Upozornění a chyb](automation-runbook-output-and-messages.md#working-with-message-streams) se zobrazí v podokně výstup testu. Zprávy do [podrobného datového proudu](automation-runbook-output-and-messages.md#write-output-to-verbose-stream) se zobrazí v podokně výstup pouze v případě, že je proměnná [VerbosePreference](automation-runbook-output-and-messages.md#work-with-preference-variables) nastavena na hodnotu `Continue` .

I když je verze konceptu spuštěna, sada Runbook se stále provádí normálně a provede všechny akce s prostředky v prostředí. Z tohoto důvodu byste měli testovat pouze Runbooky na neprodukčních prostředcích.

Postup testování každého [typu Runbooku](automation-runbook-types.md) je stejný. Při testování mezi textovým editorem a grafickým editorem v Azure Portal nedochází k žádnému rozdílu.

1. Otevřete verzi konceptu sady Runbook v [textovém editoru](automation-edit-textual-runbook.md) nebo v [grafickém editoru](automation-graphical-authoring-intro.md).
1. Kliknutím na **test** otevřete stránku test.
1. Pokud má Runbook parametry, jsou uvedeny v levém podokně, kde můžete zadat hodnoty, které mají být použity pro test.
1. Pokud chcete spustit test na [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md), změňte **nastavení spuštění** na **Hybrid Worker** a vyberte název cílové skupiny.  V opačném případě ponechte výchozí **Azure** spustit test v cloudu.
1. Kliknutím na **Spustit spusťte** test.
1. Pomocí tlačítek v podokně výstup můžete zastavit nebo pozastavit [pracovní postup PowerShellu](automation-runbook-types.md#powershell-workflow-runbooks) nebo [grafický](automation-runbook-types.md#graphical-runbooks) Runbook při jeho testování. Když Runbook pozastavíte, dokončí aktuální aktivitu a teprve pak se pozastaví. Pozastavený Runbook nejde zastavit ani znova spustit.
1. Zkontrolujte výstup Runbooku v podokně výstup.

## <a name="publish-a-runbook"></a>Publikování Runbooku

Když vytváříte nebo importujete novou sadu Runbook, je nutné ji publikovat, abyste ji mohli spustit. Každá sada Runbook v Azure Automation má verzi konceptu a publikovanou verzi. Spouštět se dá jenom publikovaná verze a upravovat jde jenom koncept. Jakékoliv změny konceptu nemají vliv na publikovanou verzi. V případě, že by měla být verze konceptu dostupná, publikujete ji a přepsat aktuální publikovanou verzi verzí konceptu.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Publikování Runbooku v Azure Portal

1. Otevřete Runbook v Azure Portal.
2. Klikněte na **Upravit**.
3. Klikněte na **publikovat** a **potom v** reakci na ověřovací zprávu potvrďte.

### <a name="publish-a-runbook-using-powershell"></a>Publikování Runbooku pomocí PowerShellu

K publikování Runbooku použijte rutinu [Publish-AzAutomationRunbook](/powershell/module/Az.Automation/Publish-AzAutomationRunbook) . 

```azurepowershell-interactive
$aaName = "MyAutomationAccount"
$RunbookName = "Sample_TestRunbook"
$rgName = "MyResourceGroup"

$publishParams = @{
    AutomationAccountName = $aaName
    ResourceGroupName     = $rgName
    Name                  = $RunbookName
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="schedule-a-runbook-in-the-azure-portal"></a>Naplánování runbooku na webu Azure Portal

Po publikování Runbooku můžete naplánovat jeho operaci:

1. Otevřete Runbook v Azure Portal.
2. V části **prostředky** vyberte **plány** .
3. Vyberte **Přidat plán**.
4. V podokně naplánovat Runbook vyberte **připojit plán k sadě Runbook**.
5. V podokně plán vyberte **vytvořit nový plán** .
6. Zadejte název, popis a další parametry v podokně nový plán.
7. Po vytvoření plánu ho zvýrazněte a klikněte na **OK**. Měla by být teď propojená s vaší sadou Runbook.
8. Vyhledejte v poštovní schránce e-mail, který vás upozorní na stav Runbooku.

## <a name="obtain-job-statuses"></a>Získat stavy úlohy

### <a name="view-statuses-in-the-azure-portal"></a>Zobrazit stavy v Azure Portal

Podrobnosti o zpracování úloh v Azure Automation jsou k dispozici v [úlohách](automation-runbook-execution.md#jobs). Až budete připraveni zobrazit své úlohy Runbooku, použijte Azure Portal a přistoupit k vašemu účtu Automation. Napravo se zobrazí souhrn všech úloh sady Runbook v **statistikách úlohy**.

![Dlaždice statistiky úlohy](./media/manage-runbooks/automation-account-job-status-summary.png)

Souhrn zobrazuje počet a grafické znázornění stavu úlohy pro každou spuštěnou úlohu.

Kliknutím na dlaždici zobrazíte stránku úlohy, která obsahuje souhrnný seznam všech spuštěných úloh. Tato stránka zobrazuje stav, název sady Runbook, čas spuštění a čas dokončení pro každou úlohu.

:::image type="content" source="./media/manage-runbooks/automation-account-jobs-status-blade.png" alt-text="Snímek obrazovky stránky úlohy":::

Seznam úloh můžete filtrovat výběrem možnosti **filtrovat úlohy**. Vyfiltrujte konkrétní sadu Runbook, stav úlohy nebo volbu v rozevíracím seznamu a zadejte časový rozsah hledání.

![Filtrovat stav úlohy](./media/manage-runbooks/automation-account-jobs-filter.png)

Případně můžete zobrazit souhrn podrobností úlohy pro konkrétní Runbook tak, že na stránce sady Runbook v účtu Automation vyberete tuto sadu Runbook a pak vyberete **úlohy**. Tato akce zobrazí stránku úlohy. Odtud můžete kliknutím na záznam úlohy zobrazit jeho podrobnosti a výstup.

:::image type="content" source="./media/manage-runbooks/automation-runbook-job-summary-blade.png" alt-text="Snímek stránky úlohy se zvýrazněným tlačítkem chyby":::

### <a name="retrieve-job-statuses-using-powershell"></a>Načtení stavů úloh pomocí PowerShellu

Pomocí rutiny [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob) můžete načíst úlohy vytvořené pro Runbook a podrobnosti konkrétní úlohy. Pokud spustíte sadu Runbook pomocí nástroje `Start-AzAutomationRunbook` , vrátí výslednou úlohu. K načtení výstupu úlohy použijte [příkaz Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) .

Následující příklad načte poslední úlohu ukázkového Runbooku a zobrazí její stav, hodnoty zadané pro parametry Runbooku a výstup úlohy.

```azurepowershell-interactive
$getJobParams = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Runbookname           = 'Test-Runbook'
}
$job = (Get-AzAutomationJob @getJobParams | Sort-Object LastModifiedDate -Desc)[0]
$job | Select-Object JobId, Status, JobParameters

$getOutputParams = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Id                    = $job.JobId
    Stream                = 'Output'
}
Get-AzAutomationJobOutput @getOutputParams
```

Následující příklad načte výstup pro konkrétní úlohu a vrátí každý záznam. Pokud existuje [výjimka](automation-runbook-execution.md#exceptions) pro jeden ze záznamů, skript místo hodnoty zapíše výjimku. Toto chování je užitečné, protože výjimky mohou poskytnout další informace, které nemusí být během výstupu protokolovány normálně.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Stream                = 'Any'
}
$output = Get-AzAutomationJobOutput @params

foreach ($item in $output) {
    $jobOutParams = @{
        AutomationAccountName = 'MyAutomationAccount'
        ResourceGroupName     = 'MyResourceGroup'
        Id                    = $item.StreamRecordId
    }
    $fullRecord = Get-AzAutomationJobOutputRecord @jobOutParams

    if ($fullRecord.Type -eq 'Error') {
        $fullRecord.Value.Exception
    } else {
        $fullRecord.Value
    }
}
```

## <a name="next-steps"></a>Další kroky

* Další informace o správě sad Runbook naleznete [v tématu Spuštění Runbooku v Azure Automation](automation-runbook-execution.md).
* Postup přípravy Runbooku PowerShellu najdete [v tématu Úprava textových runbooků v Azure Automation](automation-edit-textual-runbook.md).
* Řešení potíží se spouštěním sady Runbook najdete v tématu [řešení potíží](troubleshoot/runbooks.md)se sadou Runbook.
