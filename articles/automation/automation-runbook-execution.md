---
title: Spuštění sady Runbook v Azure Automation
description: Popisuje podrobnosti o zpracování sady Runbook ve Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: conceptual
ms.openlocfilehash: 6a51e57bd2411c19dfd5e7740f9e918d0bd09e27
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372457"
---
# <a name="runbook-execution-in-azure-automation"></a>Spuštění sady Runbook v Azure Automation

Sady Runbook se spouštějí na základě logiky definované v nich. Pokud dojde k přerušení Runbooku, sada Runbook se na začátku restartuje. Toto chování vyžaduje, abyste napsali Runbooky, které podporují restart, pokud dojde k přechodným problémům.

Spuštění sady Runbook v Azure Automation vytvoří úlohu. Úloha je jediná instance spuštění sady Runbook. Každá úloha má přístup k prostředkům Azure tím, že vytvoří připojení k vašemu předplatnému Azure. Úloha má přístup k prostředkům ve vašem datovém centru, pokud jsou tyto prostředky přístupné z veřejného cloudu.

Azure Automation přiřadí pracovnímu procesu spuštění jednotlivých úloh během provádění Runbooku. I když jsou pracovní procesy sdíleny pomocí mnoha účtů Azure, úlohy z různých účtů Automation jsou od sebe izolované. Nemáte kontrolu nad tím, které služby pracovního procesu vaše žádost o úlohy vyžadují.

Když zobrazíte seznam runbooků v Azure Portal, zobrazí se stav každé úlohy, která byla spuštěna pro jednotlivé sady Runbook. Azure Automation ukládá protokoly úloh po dobu maximálně 30 dnů. 

Následující diagram znázorňuje životní cyklus úlohy Runbooku pro [Runbooky PowerShell](automation-runbook-types.md#powershell-runbooks), [grafické Runbooky](automation-runbook-types.md#graphical-runbooks)a [Runbooky pracovních postupů PowerShellu](automation-runbook-types.md#powershell-workflow-runbooks).

![Stavy úlohy – workflow PowerShellu](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pro váš účet Automation můžete aktualizovat moduly na nejnovější verzi pomocí [postupu aktualizace modulů Azure PowerShell v Azure Automation](automation-update-azure-modules.md).

## <a name="where-to-run-your-runbooks"></a>Kde spustit vaše Runbooky

Runbooky v Azure Automation můžou běžet buď v izolovaném prostoru Azure, nebo v [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md). Většinu runbooků lze snadno spustit v izolovaném prostoru Azure, což je sdílené prostředí, které může používat více úloh. Úlohy používající stejný izolovaný prostor (sandbox) jsou vázány omezeními prostředků izolovaného prostoru (sandbox).

Můžete použít Hybrid Runbook Worker ke spouštění Runbooků přímo v počítači, který je hostitelem role, a s ohledem na místní prostředky v prostředí. Azure Automation ukládá a spravuje Runbooky a pak je doručí do jednoho nebo více přiřazených počítačů.

V následující tabulce jsou uvedeny některé úlohy spuštění sady Runbook s doporučeným spouštěcím prostředím uvedeným pro každé z nich.

|Úloha|Nejlepší volba|Poznámky:|
|---|---|---|
|Integrace s prostředky Azure|Azure Sandbox|Hostovaná v Azure je ověřování jednodušší. Pokud používáte Hybrid Runbook Worker na virtuálním počítači Azure, můžete použít [spravované identity pro prostředky Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources).|
|Získání optimálního výkonu pro správu prostředků Azure|Azure Sandbox|Skript se spouští ve stejném prostředí, které má méně latence.|
|Minimalizace provozních nákladů|Azure Sandbox|Neexistují žádné režijní náklady na výpočetní výkon a nepotřebují virtuální počítač.|
|Spustit dlouho běžící skript|Hybrid Runbook Worker|Izolované prostory Azure mají [omezení na prostředky](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Interakce s místními službami|Hybrid Runbook Worker|Může mít přímý přístup k hostitelskému počítači.|
|Vyžadování softwaru a spustitelných souborů třetích stran|Hybrid Runbook Worker|Můžete spravovat operační systém a instalovat software.|
|Monitorování souboru nebo složky pomocí Runbooku|Hybrid Runbook Worker|Použijte [úlohu sledovacího](automation-watchers-tutorial.md) procesu na Hybrid Runbook Worker.|
|Spuštění skriptu náročného na prostředky|Hybrid Runbook Worker| Izolované prostory Azure mají [omezení na prostředky](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Použití modulů s konkrétními požadavky| Hybrid Runbook Worker|Tady je několik příkladů:</br> WinSCP – závislost na WinSCP. exe </br> IISAdministration – závislost na povolování služby IIS.|
|Instalace modulu pomocí instalačního programu|Hybrid Runbook Worker|Moduly pro izolovaný prostor (sandbox) musí podporovat kopírování.|
|Používejte Runbooky nebo moduly, které vyžadují .NET Framework verze odlišná od 4.7.2.|Hybrid Runbook Worker|Izolované prostory pro automatizaci mají .NET Framework 4.7.2 a neexistuje žádný způsob, jak ji upgradovat.|
|Spouštění skriptů vyžadujících zvýšení oprávnění|Hybrid Runbook Worker|Izolované prostory neumožňují zvýšení oprávnění. Pomocí Hybrid Runbook Worker můžete vypnout nástroj řízení uživatelských účtů a použít příkaz **Invoke-Command** při spuštění příkazu, který vyžaduje zvýšení úrovně oprávnění.|
|Spouštění skriptů, které vyžadují přístup ke službě WMI|Hybrid Runbook Worker|Úlohy spuštěné v izolovaném prostoru (sandbox) v cloudu nemají přístup ke službě WMI. |

## <a name="runbook-behavior"></a>Chování sady Runbook

### <a name="creating-resources"></a>Vytváření prostředků

Pokud vaše sada Runbook vytvoří prostředek, skript by měl ověřit, zda prostředek již existuje, než se pokusí ho vytvořit. Tady je základní příklad.

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

Vaše Runbooky musí být robustní a schopné zpracovat přechodné chyby, které mohou způsobit restartování nebo selhání. Pokud se sada Runbook nezdařila, Azure Automation ji znovu pokusí.

Pokud se sada Runbook obvykle spouští v rámci časového omezení, skript implementuje logiku pro kontrolu doby spuštění. Tato kontrolu zajistí spouštění operací, jako je spuštění, vypnutí nebo horizontální navýšení kapacity, jenom během určitých časů.

> [!NOTE]
> Místní čas v procesu izolovaného prostoru (sandbox) Azure je nastavený na UTC. Výpočty pro datum a čas ve vašich sadách Runbook musí tuto skutečnost vzít v úvahu.

### <a name="tracking-progress"></a>Sledování průběhu

Je dobrým zvykem vytvářet Runbooky, které mají být v podstatě, strukturování logiky sady Runbook tak, aby bylo možné je znovu použít a snadno restartovat. Sledování průběhu v sadě Runbook je dobrým způsobem, jak zajistit, že se logika sady Runbook spustí správně, pokud dojde k problémům. Je možné sledovat průběh Runbooku pomocí externího zdroje, jako je například účet úložiště, databáze nebo sdílené soubory. Můžete vytvořit logiku v Runbooku, abyste nejdřív zkontrolovali stav poslední provedené akce. V závislosti na výsledku kontroly může logika přeskočit nebo pokračovat v sadě Runbook konkrétní úkoly.

### <a name="preventing-concurrent-jobs"></a>Prevence souběžných úloh

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

### <a name="working-with-multiple-subscriptions"></a>Práce s několika předplatnými

Aby bylo možné řešit více předplatných, musí sada Runbook použít rutinu [Disable-AzContextAutosave](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) , aby se zajistilo, že kontext ověřování není načten z jiné sady Runbook spuštěné ve stejném izolovaném prostoru (sandbox). Sada Runbook používá také parametr *AzContext* v rutinách AZ Module a předá jí správný kontext.

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

Tato část popisuje některé způsoby, jak zpracovávat výjimky nebo přerušované problémy ve vašich sadách Runbook.

#### <a name="erroractionpreference"></a>ErrorActionPreference

Proměnná [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) určuje, jak prostředí PowerShell reaguje na neukončující chybu. Ukončení chyb vždy končí a služba *ErrorActionPreference*je neovlivní.

Pokud sada Runbook používá *ErrorActionPreference*, při dokončení rutiny **GET-ChildItem** dojde k zastavení sady Runbook, například **PathNotFound** . Následující příklad ukazuje použití *ErrorActionPreference*. Konečný příkaz **pro zápis do výstupu** se nikdy nespustí, protože se skript zastaví.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Vyzkoušet catch finally

[Try catch finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) se ve skriptech PowerShellu používá ke zpracování ukončujících chyb. Skript může pomocí tohoto mechanismu zachytit konkrétní výjimky nebo obecné výjimky. Příkaz **catch** by měl sloužit ke sledování nebo pokusu o zpracování chyb. Následující příklad se pokusí stáhnout soubor, který neexistuje. Zachytí výjimku System .NET. WebException a vrátí poslední hodnotu jakékoli jiné výjimky.

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

#### <a name="throw"></a>Vyvolá

Operaci [throw](/powershell/module/microsoft.powershell.core/about/about_throw) lze použít k vygenerování ukončující chyby. Tento mechanismus může být užitečný při definování vlastní logiky v Runbooku. Pokud skript splňuje kritérium, které by ho mělo zastavit, může použít příkaz **throw** k zastavení. Následující příklad používá tento příkaz k zobrazení požadovaného parametru funkce.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Použití spustitelných souborů nebo volajících procesů

Sady Runbook, které běží v izolovaném prostoru (sandbox) Azure, nepodporují volání procesů, jako jsou spustitelné soubory (soubory **. exe** ) nebo podprocesy.  Důvodem je to, že izolovaný postup Azure je sdílený proces spuštěný v kontejneru, který nemusí mít přístup ke všem základním rozhraním API. Pro scénáře, které vyžadují software třetí strany nebo volání podprocesů, se doporučuje spustit sadu Runbook na [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).

### <a name="accessing-device-and-application-characteristics"></a>Přístup k vlastnostem zařízení a aplikací

Úlohy Runbooku, které běží v Azure sandboxs, nemají přístup k žádným charakteristikám zařízení nebo aplikací. Nejběžnější rozhraní API, které se používá k dotazování metrik výkonu ve Windows, je rozhraní WMI s některými běžnými metrikami paměti a využitím procesoru. Nezáleží ale na tom, jaké rozhraní API se používá, protože úlohy spuštěné v cloudu nemají přístup k implementaci WBEM (Web-Based Enterprise Management) od Microsoftu. Tato platforma je postavená na model CIM (Common Information Model) (CIM), která poskytuje oborové standardy pro definování charakteristik zařízení a aplikací.

## <a name="handling-errors"></a>Zpracování chyb

Vaše Runbooky musí být schopné zpracovávat chyby. Prostředí PowerShell má dva typy chyb, ukončení a neukončení. Ukončení chyb zastaví spuštění Runbooku, když k nim dojde. Sada Runbook se zastavila se stavem úlohy **selhalo**.

Neukončující chyby umožňují skriptu pokračovat i po jejich výskytu. Příkladem neukončující chyby je jeden, který nastane, když sada Runbook používá rutinu **GET-ChildItem** s cestou, která neexistuje. PowerShell uvidí, že cesta neexistuje, vyvolá chybu a pokračuje do další složky. Chyba v tomto případě nenastaví stav úlohy Runbooku na **neúspěšnou**a úloha může být dokonce dokončená. Chcete-li vynutit zastavení sady Runbook při neukončující chybě, můžete použít `-ErrorAction Stop` v rutině.

## <a name="handling-jobs"></a>Zpracování úloh

Spouštěcí prostředí můžete znovu použít pro úlohy ze stejného účtu Automation. Jedna sada Runbook může mít v jednom okamžiku více spuštěných úloh. Další úlohy, které spouštíte najednou, častěji je lze odeslat do stejného izolovaného prostoru (sandbox).

Úlohy spuštěné ve stejném procesu izolovaného prostoru (sandbox) můžou navzájem ovlivňovat. Jedním z příkladů je spuštění rutiny **Connect-AzAccount** . Provedení této rutiny odpojí každou úlohu Runbooku v procesu sdíleného izolovaného prostoru (sandboxu).

Úlohy PowerShellu spuštěné z Runbooku, který běží v izolovaném prostoru Azure, nemusí běžet v plném jazykovém režimu. Další informace o režimech jazyka PowerShell najdete v tématu [Jazykové režimy PowerShellu](/powershell/module/microsoft.powershell.core/about/about_language_modes). Další podrobnosti o interakci s úlohami v Azure Automation najdete v tématu [načítání stavu úlohy pomocí PowerShellu](#retrieving-job-status-using-powershell).

### <a name="job-statuses"></a>Stavy úlohy

Následující tabulka popisuje stavy, které jsou pro úlohu možné.

| Stav | Popis |
|:--- |:--- |
| Dokončeno |Úloha se úspěšně dokončila. |
| Neúspěšné |Nepovedlo se zkompilovat grafickou sadu Runbook pracovního postupu nebo PowerShellu. Runbook skriptu PowerShell se nepovedlo spustit, nebo došlo k výjimce úlohy. Viz [Azure Automation typy runbooků](automation-runbook-types.md).|
| Selhání, čekání na prostředky |Úloha se nezdařila, protože dosáhla limitu [reálného podílu](#fair-share) třikrát a zároveň začíná ze stejného kontrolního bodu nebo od začátku Runbooku. |
| Ve frontě |Úloha čeká, než budou dostupné prostředky pracovního procesu automatizace, aby bylo možné ji spustit. |
| Spouštění |Úloha byla přiřazena k pracovnímu procesu a systém ho spouští. |
| Obnovování |Systém obnovuje úlohu poté, co byla pozastavena. |
| Spuštěno |Úloha je spuštěná. |
| Spuštění, čekání na prostředky |Úloha byla uvolněna, protože dosáhla spravedlivého limitu sdílení. Brzy bude pokračovat od posledního kontrolního bodu. |
| Zastaveno |Úlohu uživatel zastavil před tím, než se dokončila. |
| Zastavování |Systém zastavuje úlohu. |
| Pozastaveno |Platí jenom pro [Runbooky grafických a powershellového pracovního postupu](automation-runbook-types.md) . Úlohu pozastavil uživatel, systém nebo příkaz v Runbooku. Pokud sada Runbook nemá kontrolní bod, začne od začátku. Pokud má kontrolní bod, může se znovu spustit a obnovit z posledního kontrolního bodu. Systém zastaví sadu Runbook pouze v případě, že dojde k výjimce. Ve výchozím nastavení je proměnná *ErrorActionPreference* nastavena na **pokračovat**, což znamená, že úloha je spuštěna na chybu. Pokud je proměnná preference nastavená na hodnotu **zastavit**, úloha se při chybě pozastaví.  |
| Pozastavování |Platí jenom pro [Runbooky grafických a powershellového pracovního postupu](automation-runbook-types.md) . Systém se pokouší pozastavit úlohu na žádost uživatele. Runbook se musí dostat do dalšího kontrolního bodu, než může být pozastavený. Pokud již prošl poslední kontrolní bod, byl dokončen před tím, než bude možné ho pozastavit. |

### <a name="viewing-job-status-from-the-azure-portal"></a>Zobrazení stavu úlohy z Azure Portal

Můžete zobrazit souhrnný stav všech úloh sady Runbook nebo přejít k podrobnostem o konkrétní úloze Runbooku v Azure Portal. Můžete také nakonfigurovat integraci s vaším pracovním prostorem Log Analytics pro přeposílání datových proudů úloh Runbooku. Další informace o integraci s protokoly Azure Monitor najdete v tématu [přeposílání datových proudů úloh a datových proudů úloh z automatizace do Azure monitor protokolů](automation-manage-send-joblogs-log-analytics.md).

Napravo od vybraného účtu Automation uvidíte souhrn všech úloh sady Runbook na dlaždici **Statistika úlohy** .

![Dlaždice statistiky úlohy](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Tato dlaždice zobrazuje počet a grafické znázornění stavu úlohy pro každou spuštěnou úlohu.

Kliknutím na dlaždici zobrazíte stránku **úlohy** , která obsahuje souhrnný seznam všech spuštěných úloh. Tato stránka zobrazuje stav, název sady Runbook, čas spuštění a čas dokončení pro každou úlohu.

![Stránka úlohy účtu Automation](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Seznam úloh můžete filtrovat výběrem možnosti **filtrovat úlohy**. Vyfiltrujte konkrétní sadu Runbook, stav úlohy nebo volbu v rozevíracím seznamu a zadejte časový rozsah hledání.

![Filtrovat stav úlohy](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Případně můžete zobrazit souhrn podrobností úlohy pro konkrétní Runbook tak, že **na stránce sady Runbook v** účtu Automation vyberete tuto sadu Runbook a pak vyberete dlaždici **úlohy** . Tato akce zobrazí stránku **úlohy** . Odtud můžete kliknutím na záznam úlohy zobrazit jeho podrobnosti a výstup.

![Stránka úlohy účtu Automation](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="viewing-the-job-summary"></a>Zobrazení souhrnu úlohy

Souhrn úlohy popsané výše vám umožňuje zobrazit seznam všech úloh, které byly vytvořeny pro konkrétní Runbook, a jejich nejnovější stav. Chcete-li zobrazit podrobné informace a výstupy pro úlohu, klikněte na její název v seznamu. Podrobné zobrazení úlohy zahrnuje hodnoty parametrů Runbooku, které byly k této úloze poskytnuty.

Úlohy Runbooku můžete zobrazit takto.

1. V Azure Portal vyberte **Automation** a potom vyberte název účtu Automation.
2. Z centra vyberte v části **Automatizace procesu** **Runbooky** .
3. Na stránce sady **Runbook** vyberte ze seznamu sadu Runbook.
3. Na stránce vybrané sady Runbook klikněte na dlaždici **úlohy** .
4. Klikněte na jednu z úloh v seznamu a zobrazte její podrobnosti a výstup na stránce Podrobnosti o úloze Runbooku.

### <a name="retrieving-job-status-using-powershell"></a>Načtení stavu úlohy pomocí prostředí PowerShell

Pomocí rutiny **Get-AzAutomationJob** můžete načíst úlohy vytvořené pro Runbook a podrobnosti konkrétní úlohy. Pokud sadu Runbook spustíte pomocí rutiny **Start-AzAutomationRunbook**, vrátí se výsledná úloha. K načtení výstupu úlohy použijte [příkaz Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) .

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

## <a name="getting-details-from-the-activity-log"></a>Získávání podrobností z protokolu aktivit

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

## <a name="fair-share"></a>Sdílení prostředků mezi sadami Runbook

Pokud chcete sdílet prostředky mezi všemi Runbooky v cloudu, Azure Automation dočasně uvolnit nebo zastaví jakoukoli úlohu, která běží po dobu delší než tři hodiny. Úlohy pro [powershellové Runbooky](automation-runbook-types.md#powershell-runbooks) a [Runbooky v Pythonu](automation-runbook-types.md#python-runbooks) se zastaví a nerestartují a stav úlohy se **zastaví**.

U dlouhotrvajících úloh se doporučuje použít Hybrid Runbook Worker. Hybridní pracovní procesy Runbooku nejsou omezené na poctivé sdílení a nemají omezení, jak dlouho může být sada Runbook spuštěna. Ostatní [omezení](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) úlohy platí pro Azure Sandbox a hybridní pracovní procesy Runbooku. I když se hybridní pracovní procesy Runbooku neomezují na 3 hodiny, měli byste vyvíjet Runbooky, aby se spouštěly na počítačích, které podporují restart z neočekávaných problémů s místní infrastrukturou.

Další možností je optimalizace sady Runbook pomocí podřízených runbooků. Sada Runbook může například projít stejnou funkcí u několika prostředků, například databázovou operací na několika databázích. Tuto funkci můžete přesunout do [podřízeného Runbooku](automation-child-runbooks.md) a nechat ji volat pomocí **Start-AzAutomationRunbook**. Podřízené runbooky se spouštějí paralelně v samostatných procesech.

Použití podřízených runbooků zkrátí celkovou dobu, po kterou se nadřazený Runbook dokončí. Sada Runbook může pomocí rutiny **Get-AzAutomationJob** kontrolovat stav úlohy podřízeného Runbooku, pokud stále obsahuje operace, které mají být provedeny po dokončení podřízeného objektu.

## <a name="next-steps"></a>Další kroky

* Další informace o metodách, které lze použít ke spuštění sady Runbook v Azure Automation, najdete v tématu [spuštění sady Runbook v Azure Automation](automation-starting-a-runbook.md).
* Další informace o PowerShellu, včetně referenčních modulů jazyka a výukových modulů, najdete v [dokumentaci k PowerShellu](https://docs.microsoft.com/powershell/scripting/overview).
