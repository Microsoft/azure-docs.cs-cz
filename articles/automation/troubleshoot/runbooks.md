---
title: Řešení potíží s Runbooky Azure Automation
description: Zjistěte, jak řešit problémy pomocí runbooků Azure Automation
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 01/17/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 231dd3789a20b649efd99a6b88f6e429e2626bd3
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54391327"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Řešení potíží s runbooky

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Chyby ověřování při práci s runbooky Azure Automation.

### <a name="sign-in-failed"></a>Scénář: Přihlaste se k účtu Azure se nezdařilo

#### <a name="issue"></a>Problém

Při práci s se zobrazí následující chyba `Add-AzureAccount` nebo `Connect-AzureRmAccount` rutiny.
:

```
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>Příčina

K této chybě dochází, pokud název assetu přihlašovacích údajů není platný nebo pokud nejsou platné uživatelské jméno a heslo, které jste použili k nastavení služby Automation asset přihlašovacích údajů.

#### <a name="resolution"></a>Řešení

Pokud chcete zjistit, co je špatně, proveďte následující kroky:  

1. Ujistěte se, že není nutné žádné speciální znaky, včetně **@** znak v názvu asset přihlašovacích údajů Automation, který používáte pro připojení k Azure.  
2. Zkontrolujte, jestli můžete použít uživatelské jméno a heslo, které se ukládají v přihlašovacích údajích Azure Automation v místním prostředí PowerShell ISE editor. Můžete provést Zkontrolujte uživatelské jméno a heslo jsou správné spuštěním následující rutiny v prostředí PowerShell ISE:  

   ```powershell
   $Cred = Get-Credential  
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred  
   #Using Azure Resource Manager  
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Pokud se ověření nezdaří místně, znamená to, že nebyly správně nastavený přihlašovacích údajů Azure Active Directory. Odkazovat na [ověřování v Azure pomocí Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) blogový příspěvek se získat účet služby Azure Active Directory zařídit správné nastavení.  

4. Pokud to vypadá o přechodnou chybu, zkuste přidat logiku opakování vaše rutině authentication robustnější provést ověřování.

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
   $LogonAttempt++
   # Logging in to Azure...
   $connectionResult = Connect-AzureRmAccount `
      -ServicePrincipal `
      -TenantId $servicePrincipalConnection.TenantId `
      -ApplicationId $servicePrincipalConnection.ApplicationId `
      -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

   Start-Sleep -Seconds 30
   }
   ```

### <a name="unable-to-find-subscription"></a>Scénář: Nepovedlo se najít předplatné Azure

#### <a name="issue"></a>Problém

Při práci s se zobrazí následující chyba `Select-AzureSubscription` nebo `Select-AzureRmSubscription` rutiny:

```
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Chyba

K této chybě dochází v případě, že název předplatného není platný nebo pokud uživatele Azure Active Directory, který se pokouší získat podrobnosti o předplatném není nakonfigurovaný jako správce předplatného.

#### <a name="resolution"></a>Řešení

K určení, zda jste správně ověření do Azure a přístup k předplatnému, které se snažíte vyberte, proveďte následující kroky:  

1. Skript mimo Azure Automation. abyste měli jistotu, že funguje samostatná otestujte.
2. Ujistěte se, že spouštíte `Add-AzureAccount` dřív, než spustíte rutiny `Select-AzureSubscription` rutiny. 
3. Přidat `Disable-AzureRmContextAutosave –Scope Process` na začátku své sadě runbook. Tím se zajistí, že žádné přihlašovací údaje platí pouze pro provádění aktuální sady runbook.
4. Pokud se stále zobrazí tato chybová zpráva, upravte kód tak, že přidáte **AzureRmContext** následující parametr `Add-AzureAccount` rutiny a pak spusťte kód.

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

### <a name="auth-failed-mfa"></a>Scénář: Ověřování do Azure se nezdařila, protože je povolené ověřování službou Multi-Factor Authentication

#### <a name="issue"></a>Problém

Při ověřování do Azure pomocí Azure uživatelského jména a hesla, zobrazí se následující chyba:

```
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>Příčina

Pokud máte služby Multi-Factor authentication na vašem účtu Azure, nemůžete použít uživatele služby Azure Active Directory pro ověření do Azure. Místo toho musíte použít certifikát nebo instančního objektu pro ověření do Azure.

#### <a name="resolution"></a>Řešení

Pomocí rutin modelu nasazení Azure classic pomocí certifikátu, najdete v tématu [vytváření a přidání certifikátu pro správu služeb Azure.](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Použití instančního objektu s rutiny Azure Resource Manageru, najdete v tématu [vytváří se instanční objekt pomocí webu Azure portal](../../active-directory/develop/howto-create-service-principal-portal.md) a [ověřování instančního objektu pomocí Azure Resource Manageru.](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="common-errors-when-working-with-runbooks"></a>Běžné chyby při práci s runbooky

###<a name="child-runbook-object"></a>Podřízený runbook vrátí chybu, pokud do výstupního datového proudu obsahuje objekty, spíše než jednoduché datové typy

#### <a name="issue"></a>Problém

Zobrazí následující chybová zpráva při vyvolání childrunbook s `-Wait` přepínače a výstupní datový proud obsahuje a objektu:

```
Object reference not set to an instance of an object
```

#### <a name="cause"></a>Příčina

Je známý problém ve kterém [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) nezpracovává výstupního datového proudu správně pokud obsahuje objekty.

#### <a name="resolution"></a>Řešení

To se doporučuje, abyste místo toho implementovat logiku dotazování a použít vyřešit [Get-AzureRmAutomationJobOutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) rutina pro načtení výstupu. V následujícím příkladu je definován ukázku tuto logiku.

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzureRmAutomationJob
}

$jobResults | Get-AzureRmAutomationJobOutput | Get-AzureRmAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

### <a name="task-was-cancelled"></a>Scénář: Sada runbook selže s chybou: Úloha byla zrušena

#### <a name="issue"></a>Problém

Vaše sada runbook selže s chybou podobně jako v následujícím příkladu:

```
Exception: A task was canceled.
```

#### <a name="cause"></a>Příčina

Tuto chybu může způsobovat pomocí zastaralého moduly Azure.

#### <a name="resolution"></a>Řešení

Tuto chybu lze vyřešit aktualizací moduly Azure na nejnovější verzi.

Ve vašem účtu Automation, klikněte na tlačítko **moduly**a klikněte na tlačítko **moduly Azure aktualizace**. Aktualizace trvá přibližně 15 minut, po dokončení opětovné spuštění sady runbook, který došlo k selhání. Další informace o aktualizaci modulů najdete v tématu [moduly Azure aktualizace ve službě Azure Automation](../automation-update-azure-modules.md).

### <a name="runbook-auth-failure"></a>Scénář: Sady Runbook nezdaří při práci s několika předplatnými

#### <a name="issue"></a>Problém

Při spuštění sady runbook pomocí `Start-AzureRmAutomationRunbook`, selhání sady runbook ke správě prostředků Azure.

#### <a name="cause"></a>Příčina

Sada runbook není pomocí správného kontextu při spuštění.

#### <a name="resolution"></a>Řešení

Práce s několika předplatnými, kontext předplatného může dojít ke ztrátě při vyvolání sady runbook. Chcete-li mít jistotu, že kontext předplatného je předán do sady runbook, přidejte `AzureRmContext` parametr rutiny a předání kontextu do něj. Doporučujeme také použít `Disable-AzureRmContextAutosave` rutinu s **procesu** obory a ujistěte se, že přihlašovací údaje, které se používají pouze pro aktuální sadu runbook.

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRmContext $AzureContext `
    –Parameters $params –wait
```

### <a name="not-recognized-as-cmdlet"></a>Scénář: Sada runbook selže z důvodu chybějící rutiny

#### <a name="issue"></a>Problém

Vaše sada runbook selže s chybou podobně jako v následujícím příkladu:

```
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

#### <a name="cause"></a>Příčina

Tato chyba může být způsobeno z následujících důvodů:

1. Modul, který obsahuje rutinu není importován do účtu automation
2. Modul, který obsahuje rutinu je naimportovány, ale je zastaralá

#### <a name="resolution"></a>Řešení

Tuto chybu lze vyřešit provedením jedné z následujících úloh:

Pokud modul je modul služby Azure, přečtěte si téma [aktualizace modulů Azure Powershellu ve službě Azure Automation](../automation-update-azure-modules.md) informace o aktualizaci modulů ve vašem účtu automation.

Je samostatný modul, ujistěte se, že v modulu k importu ve vašem účtu Automation.

### <a name="job-attempted-3-times"></a>Scénář: Spuštění úlohy sady runbook došlo k pokusu o třikrát po sobě, ale její spuštění pokaždé, když se nezdařilo

#### <a name="issue"></a>Problém

Vaše sada runbook selže s chybou:

```
The job was tried three times but it failed
```

#### <a name="cause"></a>Příčina

Tato chyba může být způsobeno z následujících důvodů:

1. Limit paměti. Nepřispějete na tom, kolik paměti je přidělen izolovaném prostoru se nachází v umístění [omezení služby Automation](../../azure-subscription-service-limits.md#automation-limits). Úlohy se může selhat, pokud aplikace používá více než 400 MB paměti.

2. Síťové sokety. Azure izolovaných prostorů jsou omezena na 1 000 souběžných síťové sokety, jak je popsáno v [omezení služby Automation](../../azure-subscription-service-limits.md#automation-limits).

3. Nekompatibilní modul. Této chybě může dojít, pokud modul závislosti nejsou správné, a pokud nejsou, vaše sada runbook obvykle vrací "Příkaz nebyl nalezen" nebo "Nelze vytvořit vazbu parametru" zprávy.

#### <a name="resolution"></a>Řešení

Některé z následujících řešení tento problém vyřešit:

* Navrhované metody pro práci v rámci omezení paměti jsou rozdělit zatížení mezi více runbooky, není zpracování co nejvíce dat v paměti, nikoli k zápisu zbytečné výstup z vaší sady runbook, nebo zvažte počet kontrolních bodů zápisu do vašich pracovních postupů prostředí PowerShell sady runbook. Clear – metoda můžete použít například `$myVar.clear()` vymazání proměnné a použití `[GC]::Collect()` spustit uvolňování paměti okamžitě, tím se snižuje nároky na paměť pro sady runbook za běhu.

* Aktualizovat moduly Azure pomocí následujících kroků [aktualizace modulů Azure Powershellu ve službě Azure Automation](../automation-update-azure-modules.md).  

* Druhým řešením je spouštět sadu runbook [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Hybridní pracovní procesy nejsou omezeny omezení paměti a sítě, které jsou Azure karantény.

### <a name="fails-deserialized-object"></a>Scénář: Sada Runbook selže z důvodu deserializovaný objekt

#### <a name="issue"></a>Problém

Vaše sada runbook selže s chybou:

```
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>Příčina

Pokud vaše sada runbook v pracovním postupu Powershellu, ukládá komplexních objektů ve formátu deserializovat k uchování stavu vaší sady runbook, pokud pracovní postup se pozastaví.

#### <a name="resolution"></a>Řešení

Tento problém vyřešit některé z následujících tří řešení:

1. Pokud jste přesměrujete složité objekty z jedné rutiny do druhého, zabalte tyto rutiny v bloku InlineScript.
2. Název nebo hodnotu, která je nutné předejte z komplexní objekt místo předávání celý objekt.
3. Místo pracovních postupů Powershellu použijte Powershellový runbook.

### <a name="quota-exceeded"></a>Scénář: Úloha Runbooku se nezdařila, protože překročil přidělenou kvótu.

#### <a name="issue"></a>Problém

Vaše úloha runbooku se nezdaří s chybou:

```
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>Příčina

Této chybě dochází při provádění úlohy překročí 500 minut bezplatnou kvótu pro váš účet. Tato kvóta se vztahuje na všechny typy spuštění úloh, jako je například testování úlohy, spouští úlohu z portálu, provádění úlohy pomocí webhooků a plánování úloh ke spuštění s použitím portálu Azure nebo ve vašem datovém centru. Další informace o cenách pro službu Automation najdete v tématu [ceny služby Automation](https://azure.microsoft.com/pricing/details/automation/).

#### <a name="resolution"></a>Řešení

Pokud chcete využívat víc než 500 minut zpracování za měsíc, budete muset změnit předplatné z bezplatné vrstvy na úrovni Basic. Můžete upgradovat na úroveň Basic podle následujících kroků:  

1. Přihlaste se ke svému předplatnému Azure.  
2. Vyberte účet Automation, který chcete upgradovat.  
3. Klikněte na **nastavení** > **ceny**.
4. Klikněte na tlačítko **povolit** v dolní části stránky můžete upgradovat svůj účet na **základní** vrstvy.

### <a name="cmdlet-not-recognized"></a>Scénář: Rutina není rozpoznán po spuštění sady runbook

#### <a name="issue"></a>Problém

Vaše úloha runbooku se nezdaří s chybou:

```
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>Příčina

Tato chyba je nastává, když modul Powershellu nejde najít rutiny, které používáte ve své sadě runbook. To může být způsobeno modulu, který obsahuje rutinu chybí z účtu, dojde ke konfliktu názvu pomocí názvu sady runbook nebo rutina také existuje v jiném modulu a automatizace nemůže přeložit název.

#### <a name="resolution"></a>Řešení

Některé z následujících řešení tento problém vyřešit:  

* Zkontrolujte, zda jste zadali název rutiny správně.  
* Ujistěte se, že rutina existuje ve vašem účtu Automation a že nebyly zjištěny žádné konflikty. Chcete-li ověřit, zda je k dispozici rutiny, otevřete sadu runbook v režimu úprav a vyhledání rutiny, které chcete najít v knihovně nebo spuštění `Get-Command <CommandName>`. Jakmile ověříte, které rutina je k dispozici na účet a že nejsou žádné název je v konfliktu s jinými rutinami nebo sady runbook, přidejte ho na plátno a ujistěte se, že používáte platný parametr nastavení v sadě runbook.  
* Pokud máte ke konfliktu názvů a rutina je k dispozici ve dvou různých modulech, můžete vyřešit pomocí plně kvalifikovaného názvu pro rutinu. Například můžete použít **ModuleName\CmdletName**.  
* Pokud se spuštění sady runbook v místním ve skupině hybrid worker, ujistěte se, že je nainstalován modul a rutiny na počítači, který je hostitelem procesu hybrid worker.

### <a name="long-running-runbook"></a>Scénář: Dokončení dlouho spuštěná sada runbook selhalo

#### <a name="issue"></a>Problém

Vaše sada runbook zobrazuje **Zastaveno** stavu po spuštění pro 3 hodiny. Také můžete obdržet chybu:

```
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

Toto chování je záměrné Azure pomocí sandboxů kvůli sledování "Spravedlivé sdílení" procesů v rámci Azure Automation, který automaticky zastaví sadu runbook, pokud se provede déle než tři hodiny. Stav sady runbook, která překračuje spravedlivé sdílení časový limit se liší podle typu runbook. Prostředí PowerShell a Python sady runbook nastaveno **Zastaveno** stav. Runbooky pracovních postupů Powershellu jsou nastaveny na **neúspěšné**.

#### <a name="cause"></a>Příčina

Sada runbook spustil přes 3 hodiny limit povolený spravedlivé sdílení v Sandboxu Azure.

#### <a name="resolution"></a>Řešení

Jeden doporučená řešení je ke spuštění sady runbook [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md).

Hybridní pracovní procesy nejsou omezeny [spravedlivé sdílení](../automation-runbook-execution.md#fair-share) limit runbook 3 hodiny jsou Azure karantény. Zatímco proces Hybrid Runbook Worker nejsou omezeny limit spravedlivé sdílení 3 hodiny, runbooky spuštěné v hybridní pracovní procesy Runbooku by měly být vypracovány dál k podpoře restartování chování, pokud dojde k problémům neočekávaný místní infrastruktury.

Další možností je optimalizace sadu runbook tak, že vytvoříte [podřízené runbooky](../automation-child-runbooks.md). Pokud vaše sada runbook prochází stejnou funkci na celé řadě zdrojů, jako je například databázová operace na několik databází, můžete tuto funkci přesunout do podřízeného runbooku. Každá z těchto podřízených runbooků spustí paralelně v oddělených procesech. Toto chování snižuje celkovou dobu pro nadřazený runbook k dokončení.

Rutiny Powershellu, které umožňují scénáře podřízené sady runbook jsou:

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) – Tato rutina umožňuje spuštění sady runbook a předání parametrů do runbooku

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob) – Tato rutina umožňuje kontrole stavu úlohy pro každý podřízený prvek, pokud operace, které je třeba provést po dokončení podřízeného runbooku.

### <a name="expired webhook"></a>Scénář: Stav: 400 – Chybný požadavek při vyvolání webhooku

#### <a name="issue"></a>Problém

Při pokusu o vyvolání webhook pro runbook Azure Automation, se zobrazí následující chyba.

```error
400 Bad Request : This webhook has expired or is disabled
```

#### <a name="cause"></a>Příčina

Webhook, který se snažíte volat je zakázáno nebo vypršela platnost.

#### <a name="resolution"></a>Řešení

Pokud je webhook zakázaný, můžete znovu povolit webhook prostřednictvím webu Azure portal. Když webhooku vypršela, musí se webhook odstranit a znovu vytvořit. Je možné pouze [obnovit webhooku](../automation-webhooks.md#renew-webhook) pokud ho ještě nevypršela.

### <a name="429"></a>Scénář: 429: Rychlost požadavků je aktuálně příliš velká. Zkuste to prosím znovu.

#### <a name="issue"></a>Problém

Při spuštění se zobrazí následující chybová zpráva `Get-AzureRmAutomationJobOutput` rutiny:

```
429: The request rate is currently too large. Please try again
```

#### <a name="cause"></a>Příčina

K této chybě může dojít při získávání výstupu úlohy ze sady runbook, který má mnoho [streamy verbose](../automation-runbook-output-and-messages.md#verbose-stream).

#### <a name="resolution"></a>Řešení

Existují dva způsoby, jak vyřešit tuto chybu:

* Upravit sadu runbook a snížení počtu datové proudy úlohy, které vydává.
* Snižte počet datových proudů, která se má načíst při spuštění rutiny. Pokud chcete postupovat podle tohoto chování, můžete zadat `-Stream Output` parametr `Get-AzureRmAutomationJobOutput` rutina pro načtení pouze výstupním datovým proudům. 

## <a name="common-errors-when-importing-modules"></a>Běžné chyby při importu modulů

### <a name="module-fails-to-import"></a>Scénář: Chyby modulu k importu nebo rutin nelze provést po dokončení importu

#### <a name="issue"></a>Problém

Modul nejde importovat nebo se importuje správně, ale jsou extrahovány žádné rutiny.

#### <a name="cause"></a>Příčina

Je několik běžných příčin, které modul nemusí úspěšně importovat do Azure Automation:

* Struktura neodpovídá struktuře Automation musí být v.
* Modul je závislá na jiný modul, který nebyl nasazen do vašeho účtu Automation.
* Modul chybí jeho závislosti do složky.
* `New-AzureRmAutomationModule` Rutina se používá k nahrání modulu a nedali úložiště úplnou cestu nebo nebyly načteny modulu pomocí adresy URL veřejně přístupná.

#### <a name="resolution"></a>Řešení

Některé z následujících řešení tento problém vyřešit:

* Ujistěte se, že modul má tento formát: ModuleName.Zip **->** název modulu nebo číslo verze **->** (ModuleName.psm1, ModuleName.psd1)
* Otevření souboru .psd1 a zjistěte, jestli modul mají všechny závislosti. Pokud ano, nahrajte do účtu Automation tyto moduly.
* Ujistěte se, že jsou ve složce modulu všechny odkazované knihovny DLL debuggle.

## <a name="next-steps"></a>Další postup

Pokud nezobrazila váš problém nebo nelze vyřešit vaše potíže, navštíví některý z následujících kanálů pro další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.