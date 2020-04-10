---
title: Spuštění sady Runbook v Azure Automation
description: Popisuje podrobnosti o tom, jak se zpracovává runbook v Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: conceptual
ms.openlocfilehash: 1907eb7cde482927ee8e6b0a2522158f05c1808f
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010932"
---
# <a name="runbook-execution-in-azure-automation"></a>Spuštění sady Runbook v Azure Automation

Sady Runbook se provádějí na základě logiky definované uvnitř nich. Pokud je runbook přerušen, runbook se restartuje na začátku. Toto chování vyžaduje, abyste zapisovali sady Runbook, které podporují restartování, pokud dojde k přechodným problémům.

Spuštění sady Runbook v Azure Automation vytvoří úlohu, což je jedna instance spuštění sady Runbook. Každá úloha má přístup k prostředkům Azure tím, že navazuje připojení k vašemu předplatnému Azure. Úloha má přístup k prostředkům ve vašem datovém centru pouze v případě, že tyto prostředky jsou přístupné z veřejného cloudu.

Azure Automation přiřadí pracovníka ke spuštění každé úlohy během spuštění sady Runbook. Zatímco pracovníci jsou sdíleny mnoha účty Azure, úlohy z různých účtů automatizace jsou izolované od sebe navzájem. Nemáte kontrolu nad tím, který pracovník bude obsluhovat vaši žádost o práci.

Když zobrazíte seznam runbooků na webu Azure Portal, zobrazí se stav každé úlohy, která byla spuštěna pro každou runbook. Azure Automation ukládá protokoly úloh po dobu maximálně 30 dnů. 

Následující diagram znázorňuje životní cyklus úlohy sady Runbook pro [sady Runbook prostředí PowerShell](automation-runbook-types.md#powershell-runbooks), [grafické sady Runbook](automation-runbook-types.md#graphical-runbooks)a [runbooky pracovního postupu prostředí PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).

![Stavy úloh – pracovní postup prostředí PowerShell](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="where-to-run-your-runbooks"></a>Kde spustit runbooky

Runbooky v Azure Automation můžou běžet na izolovaném prostoru Azure nebo na [hybridním pracovníkovi runbooku](automation-hybrid-runbook-worker.md). Většinu runbooků lze snadno spustit v izolovaném prostoru Azure, sdíleném prostředí, které můžete použít více úloh. Úlohy používající stejnou oblast složky síní jsou vázány omezeními prostředků izolovaného prostoru.

Hybridní pracovník sady Runbook můžete spustit runbooky přímo v počítači, který je hostitelem role, a proti místním prostředkům v prostředí. Azure Automation ukládá a spravuje runbooky a pak je doručuje do jednoho nebo více přiřazených počítačů.

V následující tabulce jsou uvedeny některé úlohy provádění runbooku s doporučeným prostředím pro spuštění uvedeným pro každou z nich.

|Úkol|Nejlepší volba|Poznámky|
|---|---|---|
|Integrace s prostředky Azure|Azure Sandbox|Hostování v Azure, ověřování je jednodušší. Pokud používáte hybridní runbook worker na virtuálním počítači Azure, můžete použít [spravované identity pro prostředky Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources).|
|Získání optimálního výkonu pro správu prostředků Azure|Azure Sandbox|Skript je spuštěn ve stejném prostředí, které má menší latenci.|
|Minimalizujte provozní náklady|Azure Sandbox|Neexistuje žádná režie výpočetní ch od povzdál a není potřeba virtuálního počítače.|
|Spuštění dlouhotrvajícího skriptu|Hybrid Runbook Worker|Karantény zabezpečení Azure mají [omezení na prostředky](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Interakce s místními službami|Hybrid Runbook Worker|Může mít přístup přímo k hostitelskému počítači.|
|Vyžadovat software a spustitelné soubory jiných výrobců|Hybrid Runbook Worker|Spravujete operační systém a můžete nainstalovat software.|
|Sledování souboru nebo složky pomocí runbooku|Hybrid Runbook Worker|Použijte [úlohu sledovací ho sledovacího](automation-watchers-tutorial.md) procesu u hybridního pracovníka runbooku.|
|Spuštění skriptu náročného na prostředky|Hybrid Runbook Worker| Karantény zabezpečení Azure mají [omezení na prostředky](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Použití modulů se specifickými požadavky| Hybrid Runbook Worker|Tady je několik příkladů:</br> WinSCP - závislost na winscp.exe </br> Správa iIS – závislost na povolení služby IIS.|
|Instalace modulu s instalačním programem|Hybrid Runbook Worker|Moduly pro izolované pískoviště musí podporovat kopírování.|
|Použití runbooků nebo modulů, které vyžadují verzi rozhraní .NET Framework odlišnou od verze 4.7.2|Hybrid Runbook Worker|Karantény zabezpečení automatizace mají rozhraní .NET Framework 4.7.2 a neexistuje žádný způsob, jak ji upgradovat.|
|Spouštění skriptů, které vyžadují zvýšení oprávnění|Hybrid Runbook Worker|Karantény neumožňují výšku. S hybridním pracovníkem runbooku můžete vypnout příkaz Řízení řízení o řízení a použít **příkaz Invoke-Command** při spuštění příkazu, který vyžaduje zvýšení oprávnění.|
|Spouštění skriptů, které vyžadují přístup ke službám WMI|Hybrid Runbook Worker|Úlohy spuštěné v karanténách zabezpečení v cloudu nemají přístup ke službě WMI. |

## <a name="runbook-behavior"></a>Chování runbooku

### <a name="creating-resources"></a>Vytváření zdrojů

Pokud váš soubor Runbook vytvoří prostředek, skript by měl zkontrolovat, zda prostředek již existuje před pokusem o jeho vytvoření. Zde je základní příklad.

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exist, creating"
    New-AzureRMVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="supporting-time-dependent-scripts"></a>Podpora skriptů závislých na čase

Sady Runbook musí být robustní a schopné zpracování přechodných chyb, které mohou způsobit restartování nebo selhání. Pokud se spuštění nezdaří, Azure Automation ji znovu vyzkouší.

Pokud vaše runbook obvykle běží v časovém omezení, mají skript implementovat logiku pro kontrolu doby spuštění. Tato kontrola zajišťuje spuštění operací, jako je například spuštění, vypnutí nebo horizontální navýšení kapacity pouze v určitých časech.

> [!NOTE]
> Místní čas v procesu izolovaného prostoru Azure je nastavena na UTC. Výpočty pro datum a čas v sadách Runbook musí tuto skutečnost vzít v úvahu.

### <a name="tracking-progress"></a>Sledování průběhu

Je vhodné vytvářet runbooky jako modulární povahy a strukturovat logiku runbooku tak, aby ji bylo možné snadno znovu použít a restartovat. Sledování průběhu v runbooku je dobrý způsob, jak zajistit, že logika runbooku se spustí správně, pokud existují problémy. Je možné sledovat průběh runbooku pomocí externího zdroje, jako je například účet úložiště, databáze nebo sdílené soubory. Můžete vytvořit logiku v aplikaci Runbook a nejprve zkontrolovat stav poslední provedené akce. Potom na základě výsledku kontroly logika můžete přeskočit nebo pokračovat konkrétní úkoly v runbooku.

### <a name="preventing-concurrent-jobs"></a>Zabránění souběžným úlohám

Některé runbooky se chovají podivně, pokud běží navíce více úloh najednou. V tomto případě je důležité, aby runbook implementoval logiku k určení, zda již existuje spuštěná úloha. Zde je základní příklad.

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

### <a name="working-with-multiple-subscriptions"></a>Práce s několika předplatnými

Chcete-li se vypořádat s více předplatnými, musí váš runbook použít rutinu [Disable-AzContextAutosave,](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) aby se zajistilo, že kontext ověřování není načten z jiného runbooku spuštěného ve stejné izolované masivu. Runbook také používá`AzContext` parametr na rutinách modulu Az a předá mu správný kontext.

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

### <a name="handling-exceptions"></a>Zpracování výjimek

Tato část popisuje některé způsoby zpracování výjimek nebo občasných problémů v sadách Runbook.

#### <a name="erroractionpreference"></a>ErrorActionPreference

Proměnná [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) určuje, jak prostředí PowerShell reaguje na neukončující chybu. Ukončující chyby vždy ukončit a nejsou ovlivněny *ErrorActionPreference*.

Pokud runbook `ErrorActionPreference`používá , obvykle neukončující chyba, `Get-ChildItem` jako je **PathNotFound** z rutiny zastaví runbook z dokončení. Následující příklad ukazuje použití `ErrorActionPreference`. Konečný `Write-Output` příkaz se nikdy nespustí, protože se skript zastaví.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Zkuste chytit konečně

[Zkuste Catch Finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) se používá ve skriptech prostředí PowerShell pro zpracování ukončujících chyb. Skript můžete použít tento mechanismus zachytit konkrétní výjimky nebo obecné výjimky. Příkaz `catch` by měl být použit ke sledování nebo pokusu o zpracování chyb. Následující příklad se pokusí stáhnout soubor, který neexistuje. Zachytí `System.Net.WebException` výjimku a vrátí poslední hodnotu pro všechny ostatní výjimky.

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

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) lze použít ke generování ukončující chyby. Tento mechanismus může být užitečné při definování vlastní logiku v runbooku. Pokud skript splňuje kritérium, které by měl `throw` zastavit, může použít příkaz zastavit. Následující příklad používá tento příkaz k zobrazení parametru požadované funkce.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Použití spustitelných nebo volajících procesů

Sady Runbook, které se spouštějí v karanténách zabezpečení Azure, nepodporují volající procesy, jako jsou spustitelné soubory (**soubory EXE)** nebo dílčí procesy.  Důvodem je, že azure izolovaného prostoru je sdílený proces spuštěn v kontejneru, který nemusí mít přístup ke všem podkladovým api. Pro scénáře, které vyžadují software jiného výrobce nebo volání dílčích procesů, se doporučuje spustit runbook na [hybridní množiny runbook .](automation-hybrid-runbook-worker.md)

### <a name="accessing-device-and-application-characteristics"></a>Přístup k charakteristikám zařízení a aplikace

Úlohy sady Runbook, které běží v karanténách zabezpečení Azure, nemají přístup k žádným charakteristikám zařízení nebo aplikace. Nejběžnější rozhraní API používané k dotazování metriky výkonu v systému Windows je WMI, s některými z běžných metrik je využití paměti a procesoru. Nezáleží však na tom, jaké rozhraní API se používá, protože úlohy spuštěné v cloudu nemají přístup k implementaci Microsoftu webové podnikové správy (WBEM). Tato platforma je postavena na společném informačním modelu (CIM), který poskytuje oborové standardy pro definování vlastností zařízení a aplikací.

## <a name="handling-errors"></a>Zpracování chyb

Sady Runbook musí být schopny zpracovávat chyby. Prostředí PowerShell má dva typy chyb, ukončující a neukončující. Ukončení chyb zastavit spuštění runbooku, když k nim dojde. Soubor Runbook se zastaví se stavem úlohy Failed.

Neukončující chyby umožňují skript pokračovat i poté, co k nim dojde. Příkladem neukončující chyby je chyba, ke které `Get-ChildItem` dochází, když runbook používá rutinu s cestou, která neexistuje. Prostředí PowerShell vidí, že cesta neexistuje, vyvolá chybu a pokračuje do další složky. Chyba v tomto případě nenastaví stav úlohy sady Runbook na Nepodařilo se a úloha může být dokonce dokončena. Chcete-li vynutit zastavení runbooku při neukončující chybě, můžete použít `-ErrorAction Stop` rutinu.

## <a name="handling-jobs"></a>Manipulace s úlohami

Prostředí spuštění můžete znovu použít pro úlohy ze stejného účtu Automatizace. Jeden runbook může mít mnoho úloh spuštěna najednou. Čím více úloh spustíte současně, tím častěji mohou být odeslány do stejné hospodařící skříně.

Úlohy spuštěné ve stejném procesu izolovaného prostoru se mohou vzájemně ovlivňovat. Jedním z příkladů `Disconnect-AzAccount` je spuštění rutiny. Spuštění této rutiny odpojí každou úlohu runbooku v procesu sdílené izolované ho složky.

Úlohy prostředí PowerShell spuštěné z runbooku, který běží v izolovaném prostoru Azure, nemusí běžet v režimu úplného jazyka. Další informace o jazykových režimech Prostředí PowerShell najdete v [tématu Režimy jazyků Prostředí PowerShell](/powershell/module/microsoft.powershell.core/about/about_language_modes). Další podrobnosti o interakci s úlohami v Azure Automation najdete v [tématu Načítání stavu úlohy pomocí PowerShellu](#retrieving-job-status-using-powershell).

### <a name="job-statuses"></a>Stavy úloh

Následující tabulka popisuje stavy, které jsou možné pro úlohu.

| Status | Popis |
|:--- |:--- |
| Dokončeno |Úloha se úspěšně dokončila. |
| Failed |Zkompilovat grafickou knihu nebo runbook pracovního postupu prostředí PowerShell. Spuštění skriptu prostředí PowerShell se nezdařilo nebo úloha měla výjimku. Viz [Typy runbooků Azure Automation](automation-runbook-types.md).|
| Nezdařilo se, čekání na prostředky |Úloha se nezdařila, protože třikrát dosáhla limitu [spravedlivého podílu](#fair-share) a pokaždé byla spuštěna ze stejného kontrolního bodu nebo od začátku runbooku. |
| Ve frontě |Úloha čeká na prostředky na pracovníka automatizace k dispozici tak, aby bylo možné spustit. |
| Spouštění |Úloha byla přiřazena pracovníkovi a systém ji spouští. |
| Obnovování |Systém pokračuje v práci poté, co byla pozastavena. |
| Spuštěno |Úloha je spuštěná. |
| Běh, čekání na prostředky |Úloha byla uvolněna, protože dosáhla limitu spravedlivého podílu. Bude pokračovat krátce od posledního kontrolního bodu. |
| Zastaveno |Úlohu uživatel zastavil před tím, než se dokončila. |
| Zastavování |Systém zastavuje úlohu. |
| Dočasně blokován. |Platí pouze pro [grafické sady runbooky a runbooky pracovního postupu prostředí PowerShell.](automation-runbook-types.md) Úlohu pozastavil uživatel, systém nebo příkaz v Runbooku. Pokud runbook nemá kontrolní bod, začíná od začátku. Pokud má kontrolní bod, může začít znovu a pokračovat od jeho poslední kontrolní bod. Systém pozastaví runbook pouze v případě, že dojde k výjimce. Ve výchozím `ErrorActionPreference` nastavení je proměnná nastavena na Pokračovat, což znamená, že úloha stále běží na chybě. Pokud je proměnná předvoleb nastavena na Hodnotu Zastavit, úloha se pozastaví při chybě.  |
| Pozastavování |Platí pouze pro [grafické sady runbooky a runbooky pracovního postupu prostředí PowerShell.](automation-runbook-types.md) Systém se pokouší pozastavit úlohu na žádost uživatele. Runbook se může pozastavit až po dosažení následujícího kontrolního bodu. Pokud již prošel poslední kontrolní bod, dokončí před jeho pozastavení. |

### <a name="viewing-job-status-from-the-azure-portal"></a>Zobrazení stavu úlohy z portálu Azure

Můžete zobrazit souhrnný stav všech úloh sady Runbook nebo přejít k podrobnostem konkrétní úlohy sady Runbook na webu Azure Portal. Můžete také nakonfigurovat integraci s pracovním prostorem Log Analytics tak, aby přesměrovalstav úlohy runbooku a datové proudy úloh. Další informace o integraci s protokoly Azure Monitor, najdete [v tématu předávání stavu úlohy a toky úloh z automatizace do protokolů Azure Monitor](automation-manage-send-joblogs-log-analytics.md).

Na pravé straně vybraného účtu Automatizace se zobrazí souhrn všech úloh runbooku na dlaždici **Statistika úloh.**

![Dlaždice Statistika úlohy](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Tato dlaždice zobrazuje počet a grafické znázornění stavu úlohy pro každou provedenou úlohu.

Kliknutím na dlaždici se zobrazí stránka Úlohy, která obsahuje souhrnný seznam všech provedených úloh. Na této stránce se zobrazuje stav, název runbooku, čas zahájení a čas dokončení každé úlohy.

![Stránka Úlohy účtu automatizace](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Seznam úloh můžete filtrovat výběrem **možnosti Filtrovat úlohy**. Filtrujte podle konkrétního runbooku, stavu úlohy nebo volby z rozevíracího seznamu a zadejte časový rozsah pro hledání.

![Stav úlohy filtru](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Případně můžete zobrazit souhrnné podrobnosti o úloze pro konkrétní runbook výběrem tohoto runbooku ze stránky Runbook v účtu Automation a pak vyberete dlaždici **Úlohy.** Tato akce představuje stránku Úlohy. Zde můžete kliknutím na záznam úlohy zobrazit jeho podrobnosti a výstup.

![Stránka Úlohy účtu automatizace](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="viewing-the-job-summary"></a>Zobrazení souhrnu úlohy

Výše popsaný souhrn úloh umožňuje podívat se na seznam všech úloh, které byly vytvořeny pro konkrétní soubor Runbook, a jejich nejnovější stav. Chcete-li zobrazit podrobné informace a výstup pro úlohu, klepněte na její název v seznamu. Podrobné zobrazení úlohy zahrnuje hodnoty parametrů runbooku, které byly této úloze poskytnuty.

Úlohy Runbooku můžete zobrazit takto.

1. Na webu Azure Portal vyberte **Automatizace** a pak vyberte název účtu Automation.
2. V rozbočovači vyberte **Runbooky** v části **Automatizace procesů**.
3. Na stránce Runbook vyberte runbook ze seznamu.
3. Na stránce vybraného runbooku klikněte na dlaždici **Úlohy.**
4. Klikněte na jednu z úloh v seznamu a zobrazte její podrobnosti a výstup na stránce podrobností o úloze sady Runbook.

### <a name="retrieving-job-status-using-powershell"></a>Načítání stavu úlohy pomocí PowerShellu

Pomocí `Get-AzAutomationJob` rutiny načtěte úlohy vytvořené pro runbook a podrobnosti o konkrétní úloze. Pokud spustíte runbook s `Start-AzAutomationRunbook`použitím PowerShellu , vrátí výslednou úlohu. Použijte [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) k načtení výstupu úlohy.

Následující příklad získá poslední úlohu pro ukázkovou runbook a zobrazí její stav, hodnoty uvedené pro parametry sady Runbook a výstup úlohy.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

Následující příklad načte výstup pro konkrétní úlohu a vrátí každý záznam. Pokud existuje výjimka pro jeden ze záznamů, skript zapíše výjimku namísto hodnoty. Toto chování je užitečné, jako výjimky mohou poskytnout další informace, které nemusí být zaznamenány normálně během výstupu.

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

## <a name="getting-details-from-the-activity-log"></a>Získání podrobností z protokolu aktivit

Podrobnosti runbooku, například osobu nebo účet, který spustil runbook, můžete načíst z protokolu aktivit pro účet Automation. Následující příklad prostředí PowerShell poskytuje poslednímu uživateli, který spouštěje zadanou runbook.

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

## <a name="sharing-resources-among-runbooks"></a><a name="fair-share"></a>Sdílení prostředků mezi runbooky

Chcete-li sdílet prostředky mezi všechny sady Runbook v cloudu, Azure Automation dočasně uvolní nebo zastaví jakoukoli úlohu, která běží déle než tři hodiny. Úlohy pro [sady Runbook prostředí PowerShell](automation-runbook-types.md#powershell-runbooks) a [sady Runbook Python](automation-runbook-types.md#python-runbooks) jsou zastaveny a nejsou restartovány a stav úlohy se zastaví.

Pro dlouhotrvající úlohy se doporučuje použít hybridní pracovník runbooku. Hybridní pracovníci runbooku nejsou omezeni spravedlivým podílem a nemají omezení na to, jak dlouho může runbook spustit. Ostatní [omezení](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) úloh platí pro karantény zabezpečení Azure i hybridní runbook pracovníků. Zatímco hybridní pracovníci runbooku nejsou omezeni limitem spravedlivého sdílení 3 hodiny, měli byste vyvinout runbooky, které se budou spouštět na pracovnících, kteří podporují restartování z neočekávaných problémů s místní infrastrukturou.

Další možností je optimalizace sady Runbook pomocí podřízených runbooků. Runbook může například cykinovat prostřednictvím stejné funkce na několika prostředcích, jako je například databázová operace v několika databázích. Tuto funkci můžete přesunout do [podřízeného runbooku](automation-child-runbooks.md) a nechat ji volat pomocí `Start-AzAutomationRunbook`aplikace Runbook . Podřízené runbooky se spouštějí paralelně v samostatných procesech.

Použití podřízených runbooků zkracovou celkovou dobu, po kterou má nadřazená sada Runbook dokončit. Sada Runbook může `Get-AzAutomationJob` pomocí rutiny zkontrolovat stav úlohy podřízeného runbooku, pokud má po dokončení podřízeného zařízení stále operace, které je třeba provést.

## <a name="next-steps"></a>Další kroky

* Další informace o metodách, které se dá použít ke spuštění sady Runbook v Azure Automation, najdete v [tématu Spuštění runbooku v Azure Automation](automation-starting-a-runbook.md).
* Další informace o prostředí PowerShell, včetně jazykových odkazů a výukových modulů, najdete v dokumentu [PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
