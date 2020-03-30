---
title: Poradce při potížích s runbooky Azure Automation
description: Zjistěte, jak řešit a řešit problémy, se kterými se můžete setkat se runbooky Azure Automation.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: b5d326d02587d6b5bd8fd73dcccfefdb13c47d57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500919"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Řešení potíží s runbooky

Pokud máte chyby při spouštění runbooků v Azure Automation, můžete použít následující kroky k diagnostice problémů.

1. **Ujistěte se, že váš skript runbook úspěšně spustí v místním počítači.** 

    Informace o jazykových referencích a výukových modulech najdete v [dokumentech powershellu](/powershell/scripting/overview) nebo [v Pythonu.](https://docs.python.org/3/) Místní spuštění skriptu může zjistit a vyřešit běžné chyby, například:

      * Chybějící moduly
      * Chyby syntaxe
      * Logické chyby

2. **Prozkoumejte [datové proudy chyb](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output)v souboru Runbook .**

    Podívejte se na tyto datové proudy pro konkrétní zprávy a porovnat je s chybami zdokumentované v tomto článku.

3. **Ujistěte se, že vaše uzly a pracovní prostor automatizace mají požadované moduly.** 

    Pokud vaše aplikace Runbook importuje všechny moduly, ověřte, zda jsou k dispozici pro váš účet Automation pomocí kroků uvedených v [části Import moduly](../shared-resources/modules.md#import-modules). Aktualizujte své moduly na nejnovější verzi podle pokynů na [update azure moduly v Azure Automation](..//automation-update-azure-modules.md). Další informace o řešení potíží naleznete [v tématu Poradce při potížích s moduly](shared-resources.md#modules).

4. **Pokud je runbook pozastavený nebo neočekávaně selže.**

    * [Zkontrolujte stavy úloh](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) definuje stavy runbooku a některé možné příčiny.
    * [Přidejte další výstup](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) do runbooku k identifikaci toho, co se stane před pozastavením runbooku.
    * [Zpracování všech výjimek,](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions) které jsou vyvolány vaší úlohou.

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Scénář: Spuštění přihlášení-AzureRMAccount pro přihlášení

### <a name="issue"></a>Problém

Při provádění runbooku se zobrazí následující chyba:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Příčina

K této chybě může dojít, pokud nepoužíváte účet Spustit jako nebo vypršela platnost účtu Spustit jako. Viz [Správa azure automatizace spustit jako účty](https://docs.microsoft.com/azure/automation/manage-runas-account).

Tato chyba má dvě hlavní příčiny:

* Existují různé verze modulu AzureRM nebo Az.
* Pokoušíte se získat přístup k prostředkům v samostatném předplatném.

### <a name="resolution"></a>Řešení

Pokud se zobrazí tato chyba po aktualizaci jednoho modulu AzureRM nebo Az, měli byste aktualizovat všechny moduly na stejnou verzi.

Pokud se pokoušíte získat přístup k prostředkům v jiném předplatném, můžete nakonfigurovat oprávnění podle následujících kroků.

1. Přejděte na účet Spustit automatizaci jako a zkopírujte ID aplikace a kryptografický otisk.
  ![Kopírování ID aplikace a kryptografický otisk](../media/troubleshoot-runbooks/collect-app-id.png)
1. Přejděte na řízení přístupu k předplatnému, kde není hostovaný účet Automation, a přidejte nové přiřazení role.
  ![Řízení přístupu](../media/troubleshoot-runbooks/access-control.png)
1. Přidejte ID aplikace shromážděné dříve. Vyberte oprávnění přispěvatele.
   ![Přidat přiřazení role](../media/troubleshoot-runbooks/add-role-assignment.png)
1. Zkopírujte název předplatného.
1. Nyní můžete použít následující kód runbooku k testování oprávnění z vašeho účtu Automation do jiného předplatného. Nahraďte `"\<CertificateThumbprint\>"` hodnotou, kterou jste zkopírovali v kroku 1. Nahraďte `"\<SubscriptionName\>"` hodnotou, kterou jste zkopírovali v kroku 4.

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzureRmSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzureRmSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzureRmContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>Scénář: Nelze najít předplatné Azure

### <a name="issue"></a>Problém

Při práci s rutinou `Select-AzureSubscription` nebo `Select-AzureRmSubscription` se zobrazí následující chyba:

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Chyba

K této chybě může dojít, pokud:

* Název předplatného není platný.
* Uživatel služby Azure Active Directory, který se pokouší získat podrobnosti o předplatném, není nakonfigurován jako správce předplatného.

### <a name="resolution"></a>Řešení

Podle následujících kroků zjistěte, jestli jste se ověřili v Azure a máte přístup k předplatnému, které se pokoušíte vybrat.

1. Chcete-li se ujistit, že váš skript funguje samostatně, otestujte jej mimo Azure Automation.
2. Před spuštěním rutiny `Add-AzureAccount` se ujistěte, `Select-AzureSubscription` že skript spustí rutinu.
3. Přidejte `Disable-AzureRmContextAutosave –Scope Process` na začátek runbooku. Toto volání rutiny zajišťuje, že všechna pověření platí pouze pro spuštění aktuálního runbooku.
4. Pokud se tato chybová zpráva stále zobrazuje, upravte kód přidáním parametru `AzureRmContext` pro rutinu `Add-AzureAccount` a pak kód spusťte.

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

## <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Scénář: Ověřování do Azure se nezdařilo, protože je povoleno vícefaktorové ověřování.

### <a name="issue"></a>Problém

Při ověřování v Azure pomocí uživatelského jména a hesla Azure se zobrazí následující chyba:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Příčina

Pokud máte vícefaktorové ověřování na vašem účtu Azure, nemůžete použít uživatele Azure Active Directory k ověření azure. Místo toho je třeba k ověření použít certifikát nebo instanční objekt.

### <a name="resolution"></a>Řešení

Pokud chcete použít certifikát s rutinami modelu klasického nasazení Azure, přečtěte si [informace o vytvoření a přidání certifikátu pro správu služeb Azure](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx). Pokud chcete použít instanční objekt s rutinami Azure Resource Manager, přečtěte si informace [o vytvoření instančního objektu pomocí portálu Azure portal](../../active-directory/develop/howto-create-service-principal-portal.md) a ověřování [instancí pomocí Správce prostředků Azure](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

## <a name="scenario-you-see-an-error-in-your-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Scénář: Zobrazí se chyba v datových proudech úloh o metodě get_SerializationSettings

### <a name="issue"></a>Problém

V datových proudech úloh se v ideachu runbooku zobrazí následující chyba:

```error
Connect-AzureRMAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzureRmAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

### <a name="cause"></a>Příčina

Tato chyba je způsobena pomocí rutin y modulu AzureRM a Az v runbooku. K tomu dochází při importu modulu Az před importem modulu AzureRM.

### <a name="resolution"></a>Řešení

Rutiny Az a AzureRM nelze importovat a používat ve stejném runbooku. Další informace o rutinách Az v Azure Automation najdete v tématu [Podpora modulu Az v Azure Automation](../az-modules.md).

## <a name="scenario-the-runbook-fails-with-the-error-a-task-was-canceled"></a><a name="task-was-cancelled"></a>Scénář: Spouštěč selže s chybou: Úloha byla zrušena

### <a name="issue"></a>Problém

Váš runbook se nezdaří s chybou podobnou následujícímu příkladu:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Příčina

Tato chyba může být způsobena pomocí zastaralých modulů Azure.

### <a name="resolution"></a>Řešení

Tuto chybu můžete vyřešit aktualizací modulů Azure na nejnovější verzi. 

1. V účtu Automation klikněte na **Moduly**a potom **aktualizujte moduly Azure**. 
2. Aktualizace trvá zhruba 15 minut. Po dokončení znovu spusťte sada Runbook, která se nezdařila.

Další informace o aktualizaci modulů najdete [v tématu Aktualizace modulů Azure v Azure Automation](../automation-update-azure-modules.md).

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Scénář: Sady Runbook se nezdaří při práci s více předplatnými

### <a name="issue"></a>Problém

Při provádění runbooků se runbooku nepodaří spravovat prostředky Azure.

### <a name="cause"></a>Příčina

Runbook nepoužívá správný kontext při spuštění.

### <a name="resolution"></a>Řešení

Kontext předplatného může dojít ke ztrátě, když runbook vyvolá více runbooků. Chcete-li zajistit, že kontext předplatného je předán do runbooků, `Start-AzureRmAutomationRunbook` mají klient `AzureRmContext` runbook předat kontext rutiny v parametru. Pomocí `Disable-AzureRmContextAutosave` rutiny s `Scope` nastaveným `Process` parametrem zajistíte, že zadaná pověření se použijí pouze pro aktuální sadu Runbook. Další informace naleznete v [tématu Práce s více předplatnými](../automation-runbook-execution.md#working-with-multiple-subscriptions).

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with Run As account
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

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-script"></a><a name="not-recognized-as-cmdlet"></a>Scénář: Termín není rozpoznán jako název rutiny, funkce, skriptu

### <a name="issue"></a>Problém

Váš runbook se nezdaří s chybou podobnou následujícímu příkladu:

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Příčina

K této chybě může dojít z následujících důvodů:

* Modul obsahující rutinu není importován do účtu Automatizace.
* Modul obsahující rutinu je importován, ale je zastaralý.

### <a name="resolution"></a>Řešení

Tuto chybu můžete vyřešit jedním z následujících úkolů. 

* V případě modulu Azure najdete v tématu [Jak aktualizovat moduly Azure PowerShell v Azure Automation,](../automation-update-azure-modules.md) kde se dozvíte, jak aktualizovat moduly v účtu Automation.

* Pro modul bez Azure, ujistěte se, že modul v importované do účtu automatizace.

## <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-failed-to-start-each-time"></a><a name="job-attempted-3-times"></a>Scénář: Spuštění úlohy runbooku byl pokus třikrát, ale nepodařilo se spustit pokaždé, když

### <a name="issue"></a>Problém

Spuštění runbooku se nezdaří s následující chybou:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Příčina

K této chybě dochází z důvodu jednoho z následujících problémů:

* Limit paměti. Úloha může selhat, pokud používá více než 400 MB paměti. Zdokumentovaná omezení paměti přidělená izolovanému prostoru se nacházejí v [limitech služby Automatizace](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits). 

* Síťové zásuvky. Karantény zabezpečení Azure jsou omezeny na 1000 souběžných síťových soketů. Viz [Omezení automatizačních služeb](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

* Modul nekompatibilní. Závislosti modulu nemusí být správné. V takovém případě váš runbook `Command not found` obvykle `Cannot bind parameter` vrátí nebo zprávy.

* Žádné ověřování pomocí služby Active Directory pro izolované hod. Vaše runbook se pokusil volat spustitelný soubor nebo dílčí proces, který běží v izolovaném prostoru Azure. Konfigurace runbooků pro ověřování pomocí Azure AD pomocí Azure Active Directory Authentication Library (ADAL) není podporována.

* Příliš mnoho dat výjimek. Aplikace Runbook se pokusila zapsat příliš mnoho dat výjimek do výstupního datového proudu.

### <a name="resolution"></a>Řešení

* Limit paměti, síťové zásuvky. Navrhované způsoby práce v rámci limitů paměti jsou rozdělit zatížení mezi více runbooků, zpracovat méně dat v paměti, vyhnout se zápisu zbytečného výstupu z vašich runbooků a zvážit, kolik kontrolních bodů je zapsáno do pracovního postupu prostředí PowerShell runbooky. Pomocí metody clear, `$myVar.clear`například , vymažte proměnné a použijte `[GC]::Collect` ke spuštění uvolňování paměti okamžitě. Tyto akce snižují nároky na paměť vašeho runbooku za běhu.

* Modul nekompatibilní. Aktualizujte moduly Azure podle kroků v části [Jak aktualizovat moduly Azure PowerShellu v Azure Automation](../automation-update-azure-modules.md).

* Žádné ověřování se službou Active Directory pro sandbox. Při ověřování azure ad s runbook, ujistěte se, že modul Azure AD je k dispozici ve vašem účtu Automation. Nezapomeňte udělit účtu Spustit jako potřebná oprávnění k provádění úloh, které systém Runbook automatizuje.

  Pokud vaše runbook nemůže volat spustitelný soubor nebo dílčí proces spuštěný v izolovaném prostoru Azure, použijte runbook na [hybridním pracovníkovi runbooku](../automation-hrw-run-runbooks.md). Hybridní pracovníci nejsou omezeny limity paměti a sítě, které mají karantény zabezpečení Azure.

* Příliš mnoho dat výjimek. Je limit 1 MB na výstupní datový proud úlohy. Ujistěte se, že vaše runbook obklopuje volání `try` spustitelného nebo dílčího procesu pomocí a `catch` bloky. Pokud operace vyvolat výjimku, mají kód napsat zprávu z výjimky do proměnné Automation. Tato technika zabraňuje zprávy zapsány do datového proudu výstupu úlohy.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Scénář: Přihlášení k účtu Azure se nezdařilo.

### <a name="issue"></a>Problém

Při práci s rutinou nebo `Add-AzureAccount` rutinou `Connect-AzureRmAccount` se zobrazí jedna z následujících chyb:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Příčina

K těmto chybám dochází, pokud název datového zdroje pověření není platný. Mohou také nastat, pokud uživatelské jméno a heslo, které jste použili k nastavení prostředku pověření automatizace nejsou platné.

### <a name="resolution"></a>Řešení

Chcete-li zjistit, co se děje, postupujte takto:

1. Ujistěte se, že nemáte žádné speciální znaky. Tyto znaky `\@` zahrnují znak v názvu prostředku pověření automatizace, který používáte pro připojení k Azure.
2. Zkontrolujte, jestli můžete použít uživatelské jméno a heslo, které jsou uložené v přihlašovacích údajích Azure Automation v místním editoru PowerShell ISE. Spusťte následující rutiny v prostředí PowerShell ISE.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Pokud se ověření nezdaří místně, nenastavili jste správně přihlašovací údaje služby Azure Active Directory. Chcete-li správně nastavit účet Azure Active Directory, podívejte [se na ověření Azure pomocí příspěvku](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) blogu Azure Active Directory.

4. Pokud se chyba jeví jako přechodná, zkuste do rutiny ověřování přidat logiku opakování, aby bylo ověřování robustnější.

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       #Logging in to Azure...
       $connectionResult = Connect-AzureRmAccount `
                              -ServicePrincipal `
                              -TenantId $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Scénář: Odkaz na objekt není nastaven na instanci objektu.

### <a name="issue"></a>Problém

Při vyvolání podřízeného runbooku s `Wait` parametrem se zobrazí následující chyba a výstupní datový proud obsahuje objekt:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Příčina

`Start-AzureRmAutomationRunbook`nezpracovává výstupní datový proud správně, pokud datový proud obsahuje objekty.

### <a name="resolution"></a>Řešení

Doporučuje se implementovat logiku dotazování a použít rutinu [Get-AzureRmAutomationJobOutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) k načtení výstupu. Ukázka této logiky je definována níže.

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

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>Scénář: Schůdka se nezdaří z důvodu rekonstruovaného objektu.

### <a name="issue"></a>Problém

Spuštění runbooku se nezdaří s chybou:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Příčina

Pokud je vaše runbook pracovní postup prostředí PowerShell, ukládá složité objekty v deserializovaném formátu zachovat stav runbooku, pokud je pracovní postup pozastaven.

### <a name="resolution"></a>Řešení

Tento problém můžete vyřešit pomocí některého z následujících řešení.

* Pokud potácíte složité objekty z jedné rutiny do druhé, `InlineScript` zabalte tyto rutiny do aktivity.
* Předajte název nebo hodnotu, kterou potřebujete ze složitého objektu namísto předání celého objektu.
* Místo runbooku pracovního postupu prostředí PowerShell použijte runbook prostředí PowerShell.

## <a name="scenario-runbook-job-fails-because-allocated-quota-exceeded"></a><a name="quota-exceeded"></a>Scénář: Úloha runbooku se nezdaří, protože byla překročena přidělená kvóta.

### <a name="issue"></a>Problém

Úloha runbooku se nezdaří s chybou:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Příčina

K této chybě dochází, když spuštění úlohy překročí bezplatnou kvótu 500 minut pro váš účet. Tato kvóta platí pro všechny typy úloh provádění úloh. Některé z těchto úloh testují úlohu, spouštějí úlohu z portálu, provádějí úlohu pomocí webhooků nebo plánují úlohu ke spuštění pomocí portálu Azure nebo vašeho datového centra. Další informace o cenách pro automatizaci najdete v [tématu Ceny automatizace](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Řešení

Pokud chcete použít více než 500 minut zpracování za měsíc, změňte předplatné z úrovně Free na úroveň Basic.

1. Přihlaste se ke svému předplatnému Azure.
2. Vyberte účet automatizace, který chcete upgradovat.
3. Klepněte na **položku Nastavení**a poté **na položku Ceny**.
4. Kliknutím na **Povolit** v dolní části stránky upgradujte svůj účet na úroveň Basic.

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Scénář: Rutina nebyla rozpoznána při provádění runbooku

### <a name="issue"></a>Problém

Úloha runbooku se nezdaří s chybou:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Příčina

Tato chyba je způsobena, když modul Prostředí PowerShell nemůže najít rutinu, kterou používáte v aplikaci Runbook. Je možné, že modul obsahující rutinu chybí v účtu, je název konflikt s názvem runbook, nebo rutina také existuje v jiném modulu a automatizace nelze přeložit název.

### <a name="resolution"></a>Řešení

K vyřešení problému použijte libovolné z následujících řešení.

* Ujistěte se, že jste správně zadali název rutiny.
* Ujistěte se, že rutina existuje ve vašem účtu automatizace a že neexistují žádné konflikty. Chcete-li ověřit, zda je rutina přítomna, otevřete runbook v režimu úprav a vyhledejte rutinu, kterou chcete najít v knihovně, nebo spusťte `Get-Command <CommandName>`. Jakmile ověříte, že rutina je k dispozici pro účet a že neexistují žádné názvy konflikty s jinými rutinami nebo runbooky, přidejte rutinu na plátno a ujistěte se, že používáte platný parametr nastavený v sadě Runbook.
* Pokud máte konflikt názvů a rutina je k dispozici ve dvou různých modulech, vyřešte problém pomocí plně kvalifikovaného názvu rutiny. Můžete například použít `ModuleName\CmdletName`.
* Pokud provádíte runbook místně v hybridní pracovní skupině, ujistěte se, že modul a rutina jsou nainstalovány v počítači, který hostuje hybridního pracovníka.

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Scénář: Dlouhotrvající sada Runbook se nepodařilo dokončit.

### <a name="issue"></a>Problém

Runbook se po spuštění po dobu 3 hodin zobrazí ve stavu Zastaveno. Může se také zobrazit tato chyba:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Toto chování je záměrné v karanténách zabezpečení Azure z důvodu spravedlivého [sdílení](../automation-runbook-execution.md#fair-share) monitorování procesů v rámci Azure Automation. Pokud proces provede déle než tři hodiny, spravedlivý podíl automaticky zastaví runbook. Stav runbooku, který překračuje časový limit spravedlivého sdílení, se liší podle typu runbooku. Sady runbooků PowerShellu a Pythonu jsou nastaveny na stav Zastaveno. Runbooky pracovního postupu prostředí PowerShell jsou nastaveny na Neúspěšné.

### <a name="cause"></a>Příčina

Runbook překročil 3hodinový limit povolený spravedlivým podílem v izolovaném prostoru Azure.

### <a name="resolution"></a>Řešení

Jedním z doporučených řešení je spuštění runbooku v [hybridním pracovníkovi runbooku](../automation-hrw-run-runbooks.md). Hybridní pracovníci nejsou omezeny 3hodinovýlimit reálného sdílení sady Runbook, který mají karantény zabezpečení Azure. Sady Runbook spouštěné v hybridních pracovnících runbooku by měly být vyvinuty tak, aby podporovaly chování restartování, pokud dojde k neočekávaným problémům s místní infrastrukturou.

Dalším řešením je optimalizace sady Runbook vytvořením [podřízených runbooků](../automation-child-runbooks.md). Pokud vaše runbook smyčky prostřednictvím stejné funkce na několik prostředků, například v databázi operace na několika databázích, můžete přesunout funkci do podřízené runbook. Každý podřízený runbook se spouští paralelně v samostatném procesu. Toto chování snižuje celkovou dobu dokončení nadřazeného runbooku.

Rutiny prostředí PowerShell, které umožňují podřízený scénář runbooku jsou:

* [Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook). Tato rutina vám umožní spustit runbook a předat do něj parametry.

* [Získejte AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob). Pokud existují operace, které je třeba provést po dokončení podřízeného runbooku, tato rutina umožňuje zkontrolovat stav úlohy pro každé dítě.

## <a name="scenario-status-400-bad-request-when-calling-a-webhook"></a><a name="expired webhook"></a>Scénář: Stav: 400 Chybný požadavek při volání webhooku

### <a name="issue"></a>Problém

Při pokusu o vyvolání webhooku pro runbook Azure Automation se zobrazí následující chyba:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Příčina

Webhook, který se pokoušíte volat, je zakázán nebo vypršela jeho platnost.

### <a name="resolution"></a>Řešení

Pokud je webhook uzakázaný, můžete webhook znovu povolit prostřednictvím portálu Azure. Pokud vypršela platnost webového háčku, je nutné jej odstranit a znovu vytvořit. [Webhooku](../automation-webhooks.md#renew-webhook) můžete obnovit pouze v případě, že ještě nevypršela jeho platnost.

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Scénář: 429: Míra požadavků je v současné době příliš velká...

### <a name="issue"></a>Problém

Při spuštění rutiny `Get-AzureRmAutomationJobOutput` se zobrazí následující chybová zpráva:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Příčina

K této chybě může dojít při načítání výstupu úlohy z runbooku, který má mnoho [datových proudů Verbose](../automation-runbook-output-and-messages.md#verbose-stream).

### <a name="resolution"></a>Řešení

Chcete-li tuto chybu vyřešit, proveďte jeden z následujících akcí.

* Upravte sady Runbook a snižte počet datových proudů úloh, které vysílá.

* Snižte počet datových proudů, které mají být načteny při spuštění rutiny. Chcete-li to provést, můžete `Stream` nastavit hodnotu parametru pro rutinu `Get-AzureRmAutomationJobOutput` načíst pouze výstupní datové proudy. 

## <a name="scenario-powershell-job-fails-with-error-cannot-invoke-method"></a><a name="cannot-invoke-method"></a>Scénář: Úloha prostředí PowerShell se nezdaří s chybou: Nelze vyvolat metodu

### <a name="issue"></a>Problém

Při spuštění úlohy Prostředí PowerShell v runbooku spuštěném v Azure se zobrazí následující chybová zpráva:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Příčina

Tato chyba může znamenat, že runbooky spuštěné v izolovaném prostoru Azure nelze spustit v [režimu úplný jazyk](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="resolution"></a>Řešení

Existují dva způsoby, jak tuto chybu vyřešit.

* Místo použití `Start-Job`použijte `Start-AzureRmAutomationRunbook` ke spuštění runbooku.
* Zkuste spustit runbook na hybridní mno žití.

Další informace o tomto chování a dalších chováních runbooků Azure Automation najdete v tématu [Chování runbooku](../automation-runbook-execution.md#runbook-behavior).

## <a name="scenario-linux-hybrid-runbook-worker-receives-a-prompt-for-a-password-when-signing-a-runbook"></a>Scénář: Linux Hybrid Runbook Worker obdrží výzvu k zadání hesla při podpisu runbooku

### <a name="issue"></a>Problém

Spuštění `sudo` příkazu pro linuxového hybridního pracovníka runbooku načte neočekávanou výzvu k zadání hesla.

### <a name="cause"></a>Příčina

Účet **nxautomationuser** pro agenta Log Analytics pro Linux není správně nakonfigurován v **souboru sudoers.** Hybridní runbook worker potřebuje odpovídající konfiguraci oprávnění účtu a dalších dat, aby mohl podepisovat runbooky na linuxovém pracovníkovi runbooku.

### <a name="resolution"></a>Řešení

* Ujistěte se, že hybridní runbook worker má v počítači spustitelný soubor GnuPG (GPG).

* Ověřte konfiguraci účtu **nxautomationuser** v **souboru sudoers.** Viz [Spuštění runbooků na hybridním pracovníkovi runbooku](../automation-hrw-run-runbooks.md)

## <a name="scenario-cmdlet-failing-in-pnp-powershell-runbook-on-azure-automation"></a>Scénář: Rutina selhává v runbooku PnP PowerShell v Azure Automation

### <a name="issue"></a>Problém

Když runbook zapíše pnp PowerShell generovaný objekt na výstup Azure Automation přímo, výstup rutiny nelze datový proud zpět do automatizace.

### <a name="cause"></a>Příčina

K tomuto problému nejčastěji dochází, když Azure Automation zpracovává runbooky, které `add-pnplistitem`vyvolávají rutiny PnP PowerShell, například , aniž by zachytily vrácené objekty.

### <a name="resolution"></a>Řešení

Upravte skripty a přiřaďte proměnným všechny vrácené hodnoty tak, aby se rutiny nepokoušely zapsat celé objekty do standardního výstupu. Skript může přesměrovat výstupní datový proud na rutinu, jak je znázorněno níže.

```azurecli
  $null = add-pnplistitem
```

Pokud skript analyzuje výstup rutiny, skript musí uložit výstup v proměnné a manipulovat s proměnnou namísto jednoduše streamování výstupu.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="my-problem-isnt-listed-above"></a><a name="other"></a>Můj problém není uveden výše

V následujících částech jsou uvedeny další běžné chyby a podpůrná dokumentace, která vám pomůže problém vyřešit.

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>Hybrid Runbook Worker nespouští úlohy nebo nereaguje

Pokud spouštěte úlohy na hybridní mno žití worker místo v Azure Automation, budete muset [řešit problémy hybridní pracovník sám](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker).

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>Sada Runbook se nezdaří bez oprávnění nebo nějaké varianty

Spustit jako účty nemusí mít stejná oprávnění vůči prostředkům Azure jako váš aktuální účet. Ujistěte se, že váš účet Spustit jako má [oprávnění pro přístup k prostředkům](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) používaným ve skriptu.

### <a name="issues-passing-parameters-into-webhooks"></a>Problémy předávání parametrů do webhooků

Nápovědu k předávání parametrů do webhooků najdete [v tématu Spuštění runbooku z webhooku](../automation-webhooks.md#parameters-used-when-the-webhook-starts-a-runbook).

### <a name="issues-using-az-modules"></a>Problémy s použitím modulů Az

Použití modulů Az a modulů AzureRM ve stejném účtu Automatizace není podporováno. Další podrobnosti najdete [v modulech Az v runbookech.](https://docs.microsoft.com/azure/automation/az-modules)

### <a name="inconsistent-behavior-in-runbooks"></a>Nekonzistentní chování runbooků

Postupujte podle pokynů v [spuštění sady Runbook,](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior) abyste se vyhnuli problémům se souběžnými úlohami, prostředky vytvářenými vícekrát nebo jinou logikou citlivou na čas v sadách Runbook.

### <a name="runbook-fails-with-the-error-no-permission-forbidden-403-or-some-variation"></a>Sada Runbook selže s chybou Žádné oprávnění, Zakázáno (403) nebo nějaká varianta

Spustit jako účty nemusí mít stejná oprávnění vůči prostředkům Azure jako váš aktuální účet. Ujistěte se, že váš účet Spustit jako má [oprávnění pro přístup k prostředkům](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) používaným ve skriptu.

### <a name="runbooks-were-working-but-suddenly-stopped"></a>Runbooky fungovaly, ale najednou fungovat přestaly

* Ujistěte se, že platnost účtu Spustit jako nevypršela. Viz [obnovení certifikace](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Pokud ke spuštění sady Runbook používáte [webhook,](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook) ujistěte se, že webhook uběhl.

### <a name="passing-parameters-into-webhooks"></a>Předávání parametrů do webhooků

Nápovědu k předávání parametrů do webhooků najdete [v tématu Spuštění runbooku z webhooku](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters-used-when-the-webhook-starts-a-runbook).

### <a name="using-az-modules"></a>Používání modulů Az

Použití modulů Az a modulů AzureRM ve stejném účtu Automatizace není podporováno. Viz [Moduly Az v sadách Runbook](https://docs.microsoft.com/azure/automation/az-modules).

### <a name="using-self-signed-certificates"></a>Použití certifikátů podepsaných svým držitelem

Informace o použití certifikátů podepsaných svým držitelem naleznete [v tématu Vytvoření nového certifikátu](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate).

### <a name="access-denied-when-using-azure-sandbox-for-a-runbook"></a>Přístup odepřen při použití azure izolovaného prostoru pro runbook

Azure izolovaného prostoru zabraňuje přístupu ke všem mimoprocesovým serverům COM. Například aplikace v izolovaném prostoru nebo runbook nelze volat do WMI (WMI) systému Windows nebo do služby Instalační služby systému Windows (msiserver.exe). Podrobnosti o použití izolovaného prostoru najdete v tématu [spuštění sady Runbook v Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-execution).

## <a name="recommended-documents"></a>Doporučené dokumenty

* [Spuštění sady Runbook v Azure Automation](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Spuštění sady Runbook v Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure .](https://azure.microsoft.com/support/forums/)
* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s – oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí propojením komunity Azure se správnými prostředky: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete nastolet incident podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.
