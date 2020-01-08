---
title: Spuštění sady Runbook v Azure Automation
description: Popisuje podrobnosti o zpracování sady Runbook ve Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: conceptual
ms.openlocfilehash: 4f9fd3a94cf2b6d6ca077b7363e01085e134babd
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2020
ms.locfileid: "75658113"
---
# <a name="runbook-execution-in-azure-automation"></a>Spuštění sady Runbook v Azure Automation

Když spustíte Runbook v Azure Automation, vytvoří se úloha. Úloha je instance jednoho spuštění Runbooku. Ke spuštění každé úlohy je přiřazený Azure Automation pracovník. I když jsou pracovní procesy sdíleny pomocí mnoha účtů Azure, úlohy z různých účtů Automation jsou od sebe izolované. Nemusíte mít kontrolu nad tím, které služby pracovního procesu je požadavek na vaši úlohu. Jedna sada Runbook může mít v jednom okamžiku více spuštěných úloh. Spouštěcí prostředí pro úlohy ze stejného účtu Automation se dá znovu použít. Další úlohy, které spouštíte najednou, častěji je lze odeslat do stejného izolovaného prostoru (sandbox). Úlohy spuštěné ve stejném procesu izolovaného prostoru (sandbox) můžou vzájemně ovlivnit, v jednom příkladu je spuštěná rutina `Disconnect-AzureRMAccount`. Spuštění této rutiny způsobí odpojení každé úlohy Runbooku v procesu sdíleného izolovaného prostoru (sandboxu). Když zobrazíte seznam runbooků v Azure Portal, zobrazí se stav všech úloh, které byly spuštěny pro jednotlivé sady Runbook. Můžete zobrazit seznam úloh pro jednotlivé sady Runbook a sledovat jejich stav. Protokoly úloh se ukládají na maximum po dobu 30 dnů. Popis [různých stavů úlohy.](#job-statuses)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

Následující diagram znázorňuje životní cyklus úlohy Runbooku pro [Runbooky PowerShell](automation-runbook-types.md#powershell-runbooks), [grafické Runbooky](automation-runbook-types.md#graphical-runbooks) a [Runbooky pracovních postupů PowerShellu](automation-runbook-types.md#powershell-workflow-runbooks).

![Stavy úlohy – workflow PowerShellu](./media/automation-runbook-execution/job-statuses.png)

Vaše úlohy mají přístup k prostředkům Azure navázáním připojení k vašemu předplatnému Azure. Mají přístup k prostředkům ve vašem datovém centru, pokud jsou tyto prostředky přístupné z veřejného cloudu.

## <a name="where-to-run-your-runbooks"></a>Kde spustit vaše Runbooky

Runbooky v Azure Automation můžou běžet buď v izolovaném prostoru (sandbox) v Azure, nebo v [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md). Izolovaný prostor (sandbox) je sdílené prostředí v Azure, které může používat víc úloh. Úlohy používající stejný izolovaný prostor (sandbox) jsou vázány omezeními prostředků izolovaného prostoru (sandbox). Hybridní pracovní procesy Runbooku můžou spouštět Runbooky přímo v počítači, který je hostitelem role, a s prostředky v prostředí za účelem správy těchto místních prostředků. Sady Runbook jsou uloženy a spravovány v Azure Automation a poté dodány jednomu nebo více přiřazeným počítačům. Většinu runbooků lze snadno spustit v izolovaných prostorech Azure. Můžou se doporučit konkrétní scénáře, kdy se dá zvolit hybridní Runbook přes izolovaný prostor Azure pro spuštění Runbooku. V následující tabulce najdete seznam některých ukázkových scénářů:

|Úkol|Nejlepší volba|Poznámky|
|---|---|---|
|Integrace s prostředky Azure|Azure Sandbox|Hostovaná v Azure je ověřování jednodušší. Pokud používáte Hybrid Runbook Worker na virtuálním počítači Azure, můžete použít [spravované identity pro prostředky Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) .|
|Optimální výkon pro správu prostředků Azure|Azure Sandbox|Skript se spouští ve stejném prostředí, které zase má nižší latenci.|
|Minimalizace provozních nákladů|Azure Sandbox|Neexistují žádné režijní náklady na výpočetní výkon, není potřeba virtuální počítač.|
|Dlouho běžící skript|Hybridní pracovní proces runbooku|Izolované prostory Azure mají [omezení na prostředky](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) .|
|Interakce s místními službami|Hybridní pracovní proces runbooku|Může mít přístup přímo k hostitelskému počítači.|
|Vyžadování softwaru a spustitelných souborů třetích stran|Hybridní pracovní proces runbooku|Můžete spravovat operační systém a instalovat software.|
|Monitorování souboru nebo složky pomocí Runbooku|Hybridní pracovní proces runbooku|Použití [úlohy sledovacího](automation-watchers-tutorial.md) procesu v procesu Hybrid Runbook Worker|
|Skript náročný na prostředky|Hybridní pracovní proces runbooku| Izolované prostory Azure mají [omezení na prostředky](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) .|
|Používání modulů s konkrétními požadavky| Hybridní pracovní proces runbooku|Tady je několik příkladů:</br> **WinSCP** – závislost na WinSCP. exe </br> **IISAdministration** – vyžaduje, aby služba IIS byla povolená.|
|Nainstalovat modul, který vyžaduje Instalační program|Hybridní pracovní proces runbooku|Moduly pro izolovaný prostor (sandbox) musí být copiable.|
|Používání runbooků nebo modulů, které vyžadují .NET Framework odlišnou od 4.7.2|Hybridní pracovní proces runbooku|Izolované prostory pro automatizaci mají .NET Framework 4.7.2 a neexistuje žádný způsob, jak ji upgradovat.|
|Skripty, které vyžadují zvýšení oprávnění|Hybridní pracovní proces runbooku|Izolované prostory neumožňují zvýšení oprávnění. Chcete-li tento problém vyřešit, použijte Hybrid Runbook Worker a můžete vypnout nástroj řízení uživatelských účtů a použít `Invoke-Command` při spuštění příkazu, který vyžaduje zvýšení úrovně oprávnění.|
|Skripty, které vyžadují přístup ke službě WMI|Hybridní pracovní proces runbooku|Úlohy spuštěné v izolovaných prostorech v cloudu nemají [přístup ke službě WMI](#device-and-application-characteristics) .|

## <a name="runbook-behavior"></a>Chování sady Runbook

Runbooky se spouštějí na základě logiky, která je v nich definovaná. Pokud dojde k přerušení Runbooku, sada Runbook se na začátku restartuje. Toto chování vyžaduje, aby Runbooky byly zapsány způsobem, který podporuje restart, pokud došlo k přechodným problémům.

Úlohy PowerShellu spuštěné z Runbooku spuštěné v izolovaném prostoru Azure nemusí běžet v plném jazykovém režimu. Další informace o režimech jazyka PowerShell najdete v tématu [Jazykové režimy PowerShellu](/powershell/module/microsoft.powershell.core/about/about_language_modes). Další podrobnosti o tom, jak pracovat s úlohami v Azure Automation, najdete v tématu [načítání stavu úlohy pomocí PowerShellu](#retrieving-job-status-using-powershell) .

### <a name="creating-resources"></a>Vytváření prostředků

Pokud váš skript vytvoří prostředky, měli byste před opětovným pokusem o vytvoření ověřit, zda prostředek již existuje. V následujícím příkladu je uveden základní příklad:

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzureRmResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exists, creating"
    New-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="time-dependant-scripts"></a>Čas závislých skriptů

Při vytváření runbooků by se měla provést pečlivou pozornost. Jak bylo zmíněno dříve, Runbooky musí být vytvořeny způsobem, který je robustní a může zpracovávat přechodné chyby, které mohou způsobit restartování nebo selhání sady Runbook. Pokud se sada Runbook nepovede, zkusíme to znovu. Pokud se sada Runbook normálně spouští v rámci časového omezení, logika pro kontrolu doby provádění by měla být v Runbooku implementovaná, aby se zajistilo, že operace, jako je spuštění, vypnutí nebo horizontální navýšení kapacity, se spouští jenom v určitých časech.

> [!NOTE]
> Místní čas v procesu izolovaného prostoru (sandbox) Azure je nastavený na čas UTC. Výpočty pro datum a čas ve vašich sadách Runbook je potřeba vzít v úvahu.

### <a name="tracking-progress"></a>Sledování průběhu

Je dobrým zvykem vytvářet Runbooky, které jsou v podstatě modulární. To znamená strukturování logiky v sadě Runbook, aby ji bylo možné snadno znovu použít a restartovat. Sledování průběhu v sadě Runbook je dobrým způsobem, jak zajistit, že se logika v sadě Runbook spustí správně v případě, že došlo k problémům. Některé z možných způsobů, jak sledovat průběh Runbooku, je použití externího zdroje, jako jsou účty úložiště, databáze nebo sdílené soubory. Sledováním stavu externě můžete v Runbooku vytvořit logiku, abyste nejdřív zkontrolovali stav poslední akce, kterou sada Runbook provedla. Pak na základě výsledků přeskočte nebo pokračujte v sadě Runbook konkrétní úkoly.

### <a name="prevent-concurrent-jobs"></a>Zabránit souběžným úlohám

Některé Runbooky se mohou chovat nezvykle, pokud jsou spuštěny napříč více úlohami současně. V tomto případě je důležité implementovat logiku pro kontrolu, zda sada Runbook již obsahuje spuštěnou úlohu. Základní příklad toho, jak to lze provést, je vidět v následujícím příkladu:

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzureRmAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzureRmContext $AzureContext

# If then check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

### <a name="working-with-multiple-subscriptions"></a>Práce s několika předplatnými

Při vytváření runbooků, které se týkají více předplatných, musí sada Runbook použít rutinu [Disable-AzureRmContextAutosave](/powershell/module/azurerm.profile/disable-azurermcontextautosave) , aby se zajistilo, že váš kontext ověřování není načten z jiné sady Runbook, která může být spuštěna ve stejném izolovaném prostoru. Pak je potřeba použít parametr `-AzureRmContext` v rutinách `AzureRM` a předat mu správný kontext.

```powershell
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationID $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzureRmContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzureRmAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

### <a name="handling-exceptions"></a>Zpracování výjimek

Při vytváření skriptů je důležité, aby bylo možné zpracovávat výjimky a potenciální přerušované chyby. Následuje několik různých způsobů, jak zpracovávat výjimky nebo přerušované problémy se sadami Runbook:

#### <a name="erroractionpreference"></a>$ErrorActionPreference

Proměnná preference [$ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) určuje, jak prostředí PowerShell reaguje na neukončující chybu. Ukončení chyb neovlivní `$ErrorActionPreference`, vždy se ukončí. Když použijete `$ErrorActionPreference`, zastaví se sada Runbook z normálního neukončujícího chyb, jako je `PathNotFound` z rutiny `Get-ChildItem`. Následující příklad ukazuje použití `$ErrorActionPreference`. Poslední `Write-Output` řádek nebude nikdy spuštěn, protože se skript zastaví.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Vyzkoušet catch finally

[Try catch](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) se používá ve skriptech PowerShellu, které vám pomůžou zpracovat ukončující chyby. Pomocí try catch můžete zachytit konkrétní výjimky nebo obecné výjimky. Příkaz catch by měl sloužit ke sledování chyb nebo k pokusu o zpracování chyby. Následující příklad se pokusí stáhnout soubor, který neexistuje. Zachytí výjimku `System.Net.WebException`, pokud došlo k jiné výjimce, je vrácena poslední hodnota.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

#### <a name="throw"></a>Throw

Operaci [throw](/powershell/module/microsoft.powershell.core/about/about_throw) lze použít k vygenerování ukončující chyby. To může být užitečné při definování vlastní logiky v sadě Runbook. Pokud je splněno určité kritérium, které by měl skript zastavit, můžete skript zastavit pomocí `throw`. Následující příklad ukazuje počítač parametr funkce vyžadovaný pomocí `throw`.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Použití spustitelných souborů nebo volajících procesů

Runbooky spuštěné v Azure sandboxech nepodporují volání procesů (například. exe nebo subprocess. Call). Je to proto, že se sdílené procesy Azure sandboxy spouštějí v kontejnerech, což nemusí mít přístup ke všem základním rozhraním API. Ve scénářích, kdy potřebujete software třetí strany nebo volání dílčích procesů, doporučujeme spustit sadu Runbook na [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).

### <a name="device-and-application-characteristics"></a>Vlastnosti zařízení a aplikací

Úlohy Runbooku spuštěné v izolovaném prostoru Azure nemají přístup k žádným charakteristikám zařízení nebo aplikací. Nejběžnější rozhraní API, které se používá k dotazování metrik výkonu ve Windows, je rozhraní WMI. Některé z těchto běžných metrik jsou využití paměti a procesoru. Nezáleží ale na tom, jaké rozhraní API se používá. Úlohy spuštěné v cloudu nemají přístup k implementaci služby Microsoft Based Enterprise Management (WBEM), která je postavená na model CIM (Common Information Model) (CIM), což jsou oborové standardy pro definování charakteristik zařízení a aplikací.

## <a name="job-statuses"></a>Stavy úlohy

Následující tabulka popisuje různé stavy, které můžou u úlohy nastat. Prostředí PowerShell má dva typy chyb, ukončení a neukončující chyby. Ukončení chyb nastaví stav Runbooku na **neúspěšný** , pokud k nim dojde. Neukončující chyby umožňují, aby skript pokračoval i po jeho výskytu. Příkladem neukončující chyby je použití rutiny `Get-ChildItem` s cestou, která neexistuje. PowerShell uvidí, že cesta neexistuje, vyvolá chybu a pokračuje do další složky. Tato chyba by **nedokázala** nastavit stav Runbooku na failed a mohl by být označený jako **dokončený**. Chcete-li vynutit zastavení sady Runbook při neukončující chybě, můžete použít `-ErrorAction Stop` v rutině.

| Stav | Popis |
|:--- |:--- |
| Dokončeno |Úloha se úspěšně dokončila. |
| Selhalo |Pro [grafické a powershellové Runbooky pracovních postupů](automation-runbook-types.md)se Runbook nepodařilo zkompilovat. Pro [Runbooky skriptu PowerShell](automation-runbook-types.md)se nepovedlo spustit sadu Runbook nebo úloha měla výjimku. |
| Selhání, čekání na prostředky |Úloha se nezdařila, protože dosáhla limitu [reálného podílu](#fair-share) třikrát a zároveň začíná ze stejného kontrolního bodu nebo od začátku Runbooku. |
| Ve frontě |Úloha čeká, než budou dostupné prostředky pracovního procesu Automatizace, aby se dala spustit. |
| Spouštění |Úloha byla přiřazena k pracovnímu procesu a systém ho spouští. |
| Obnovování |Systém obnovuje úlohu poté, co byla pozastavena. |
| Spuštěno |Úloha je spuštěná. |
| Spuštění, čekání na prostředky |Úloha byla uvolněna, protože dosáhla [spravedlivého](#fair-share) limitu sdílení. Brzy pokračuje od posledního kontrolního bodu. |
| Zastaveno |Úlohu uživatel zastavil před tím, než se dokončila. |
| Zastavování |Systém zastavuje úlohu. |
| Pozastaveno |Úlohu pozastavil uživatel, systém nebo příkaz v Runbooku. Pokud sada Runbook nemá kontrolní bod, začne od začátku Runbooku. Pokud má kontrolní bod, může se znovu spustit a obnovit z posledního kontrolního bodu. Sada Runbook je systémem pozastavena pouze v případě, že dojde k výjimce. Ve výchozím nastavení je ErrorActionPreference nastaveno na **pokračovat**, což znamená, že úloha pokračuje v běhu na chybu. Pokud je tato proměnná předvoleb nastavená na **zastavit**, úloha se při chybě pozastaví. Platí jenom pro [Runbooky grafických a powershellového pracovního postupu](automation-runbook-types.md) . |
| Pozastavování |Systém se pokouší pozastavit úlohu na žádost uživatele. Runbook se může pozastavit až po dosažení následujícího kontrolního bodu. Pokud už prošl poslední kontrolní bod, pak ho dokončí před tím, než bude možné ho pozastavit. Platí jenom pro [Runbooky grafických a powershellového pracovního postupu](automation-runbook-types.md) . |

## <a name="viewing-job-status-from-the-azure-portal"></a>Zobrazení stavu úlohy z Azure Portal

Můžete zobrazit souhrnný stav všech úloh sady Runbook nebo přejít k podrobnostem o konkrétní úloze Runbooku v Azure Portal. Můžete také nakonfigurovat integraci s vaším pracovním prostorem Log Analytics pro přeposílání datových proudů úloh Runbooku. Další informace o integraci s protokoly Azure Monitor najdete v tématu [přeposílání datových proudů úloh a datových proudů úloh z automatizace do Azure monitor protokolů](automation-manage-send-joblogs-log-analytics.md).

### <a name="automation-runbook-jobs-summary"></a>Souhrn úloh Runbooku služby Automation

Napravo od vybraného účtu Automation uvidíte souhrn všech úloh sady Runbook v části **Statistika úlohy** .

![Dlaždice statistiky úlohy](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Tato dlaždice zobrazuje počet a grafické znázornění stavu úlohy pro všechny spuštěné úlohy.

Kliknutím na dlaždici zobrazíte stránku **úlohy** , která obsahuje souhrnný seznam všech spuštěných úloh. Tato stránka zobrazuje stav, časy spuštění a časy dokončení.

![Stránka úlohy účtu Automation](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Seznam úloh můžete filtrovat výběrem možnosti **filtrovat úlohy** a filtrovat podle konkrétní sady Runbook, stavu úlohy nebo z rozevíracího seznamu a časového rozsahu hledání v rámci.

![Filtrovat stav úlohy](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Případně můžete zobrazit souhrn podrobností úlohy pro konkrétní Runbook tak, že **na stránce sady Runbook v** účtu Automation vyberete tuto sadu Runbook a pak vyberete dlaždici **úlohy** . Tato akce zobrazí stránku **úlohy** a odtud můžete kliknutím na záznam úlohy zobrazit jeho podrobnosti a výstup.

![Stránka úlohy účtu Automation](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>Souhrn úlohy

Můžete zobrazit seznam všech úloh, které byly vytvořeny pro konkrétní sadu Runbook a jejich nejnovější stav. Tento seznam můžete filtrovat podle stavu úlohy a rozsahu dat poslední změny úlohy. Chcete-li zobrazit podrobné informace a výstup, klikněte na název úlohy. V podrobném zobrazení úlohy najdete hodnoty parametrů Runbooku poskytnuté pro tuto úlohu.

Úlohy Runbooku můžete zobrazit takto.

1. V Azure Portal vyberte **Automation** a potom vyberte název účtu Automation.
2. Z centra vyberte **Runbooky** a pak na stránce sady **Runbook** vyberte Runbook ze seznamu.
3. Na stránce vybrané sady Runbook klikněte na dlaždici **úlohy** .
4. Klikněte na jednu z úloh v seznamu a na stránce Podrobnosti o úloze Runbooku si můžete prohlédnout její podrobnosti a výstup.

## <a name="retrieving-job-status-using-powershell"></a>Načtení stavu úlohy pomocí prostředí PowerShell

Pomocí funkce [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) můžete načíst úlohy vytvořené pro Runbook a podrobnosti konkrétní úlohy. Pokud sadu Runbook spustíte pomocí rutiny [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook), vrátí se výsledná úloha. Výstup úlohy získáte pomocí [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) .

Následující ukázkové příkazy načtou poslední úlohu ukázkového Runbooku a zobrazí její stav, hodnoty zadané pro parametry Runbooku a výstup z úlohy.

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

Následující ukázka načte výstup pro konkrétní úlohu a vrátí každý záznam. V případě, že existovala výjimka pro jeden ze záznamů, je výjimka zapsána místo hodnoty. Toto chování je užitečné, protože výjimky mohou poskytovat další informace, které nemusí být během výstupu protokolovány normálně.

```azurepowershell-interactive
$output = Get-AzureRmAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzureRmAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
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

## <a name="get-details-from-activity-log"></a>Získat podrobnosti z protokolu aktivit

Další podrobnosti, jako je například osoba nebo účet, který spustil Runbook, mohou být načteny z protokolu aktivit pro účet Automation. Následující příklad prostředí PowerShell poskytuje poslednímu uživateli, který spouští sadu Runbook:

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzureRmLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzureRmResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzureRmAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hash table
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="fair-share"></a>Korektní sdílení

Pokud chcete sdílet prostředky mezi všemi Runbooky v cloudu, Azure Automation dočasně uvolnit nebo zastaví jakoukoli úlohu, která běží po dobu delší než tři hodiny. Úlohy pro [Runbooky založené na PowerShellu](automation-runbook-types.md#powershell-runbooks) a [Runbooky v Pythonu](automation-runbook-types.md#python-runbooks) se zastaví a nerestartují a stav úlohy se zobrazí jako zastaveno.

V případě dlouhotrvajících úloh se doporučuje použít [Hybrid Runbook Worker](automation-hrw-run-runbooks.md#job-behavior). Hybridní pracovní procesy Runbooku nejsou omezené na poctivé sdílení a nemají omezení, jak dlouho může být sada Runbook spuštěna. Ostatní [omezení](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) úlohy platí pro Azure Sandbox a hybridní pracovní procesy Runbooku. I když hybridní pracovní procesy Runbooku nejsou omezené o 3 hodinové omezení sdílení, je potřeba, aby se na nich spouštěly, aby podporovaly chování při restartování z neočekávaných potíží s místní infrastrukturou.

Další možností je optimalizace sady Runbook pomocí podřízených runbooků. Pokud vaše sada Runbook projde stejnou funkcí na několika prostředcích, například databázovou operací na několika databázích, můžete tuto funkci přesunout do [podřízeného Runbooku](automation-child-runbooks.md) a zavolat ji pomocí rutiny [Start-AzureRMAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) . Každá z těchto podřízených runbooků paralelně provádí samostatné procesy. Toto chování zkrátí celkovou dobu, po kterou se nadřazený Runbook dokončí. Pomocí rutiny [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob) v Runbooku můžete zjistit stav úlohy pro každý podřízený objekt, pokud existují operace, které se provádějí po dokončení podřízeného Runbooku.

## <a name="next-steps"></a>Další kroky

* Další informace o různých metodách, které lze použít ke spuštění sady Runbook v Azure Automation, naleznete v tématu Starting [a Runbook in Azure Automation](automation-starting-a-runbook.md)
* Další informace o PowerShellu, včetně referenčních modulů jazyka a výukových modulů, najdete v [dokumentaci k PowerShellu](https://docs.microsoft.com/powershell/scripting/overview).
