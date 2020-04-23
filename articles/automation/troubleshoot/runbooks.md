---
title: Řešení potíží s Azure Automationmi chybami sady Runbook
description: Naučte se řešit problémy, se kterými se můžete setkat s Azure Automation Runbooky.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 73f79145f63e0d8afee7596f1f8231a054ef1c2e
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82097689"
---
# <a name="troubleshoot-runbook-errors"></a>Řešení chyb Runbooku

 Tento článek popisuje různé chyby sady Runbook, které mohou nastat, a způsob jejich řešení.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pro váš účet Automation můžete aktualizovat moduly na nejnovější verzi pomocí [postupu aktualizace modulů Azure PowerShell v Azure Automation](../automation-update-azure-modules.md).

## <a name="diagnosing-runbook-issues"></a>Diagnostikování problémů sady Runbook

Když při spuštění sady Runbook v Azure Automation dojde k chybám, můžete pomocí následujícího postupu diagnostikovat problémy.

1. **Ujistěte se, že se váš skript Runbooku úspěšně spouští na vašem místním počítači.** 

    Odkazy na jazyky a výukové moduly najdete v dokumentaci k [prostředí PowerShell](/powershell/scripting/overview) nebo [dokumentaci Pythonu](https://docs.python.org/3/) . Místní spuštění skriptu může zjišťovat a řešit běžné chyby, jako například:

      * Chybějící moduly
      * Chyby syntaxe
      * Logické chyby

2. **Prozkoumejte [streamy chyb](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output)sady Runbook.**

    Podívejte se na tyto datové proudy pro konkrétní zprávy a porovnejte je s chybami popsanými v tomto článku.

3. **Ujistěte se, že vaše uzly a pracovní prostor služby Automation mají požadované moduly.** 

    Pokud vaše sada Runbook importuje nějaké moduly, ověřte, že jsou k dispozici pro váš účet Automation pomocí kroků uvedených v části [Import modulů](../shared-resources/modules.md#importing-modules). Podle pokynů v části [aktualizace modulů Azure v Azure Automation](..//automation-update-azure-modules.md)aktualizujte moduly na nejnovější verzi. Další informace o řešení potíží najdete v tématu [řešení potíží s moduly](shared-resources.md#modules).

4. **Proveďte v případě, že se váš Runbook pozastaví nebo neočekávaně dojde k chybě.**

    * [Kontrolovat stavy úlohy](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) definují stavy Runbooku a některé možné příčiny.
    * [Přidejte do Runbooku další výstup](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) , abyste mohli zjistit, co se stane, než se Runbook pozastaví.
    * [Zpracujte všechny výjimky](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions) , které jsou vyvolány vaší úlohou.

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Scénář: Spusťte přihlášení – AzureRMAccount a přihlaste se.

### <a name="issue"></a>Problém

Při provádění Runbooku se zobrazí následující chyba:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Příčina

K této chybě může dojít, pokud nepoužíváte účet Spustit jako nebo vypršela platnost účtu Spustit jako. Viz [Správa účtů spustit jako Azure Automation](https://docs.microsoft.com/azure/automation/manage-runas-account).

Tato chyba má dvě primární příčiny:

* Existují různé verze modulu AzureRM nebo AZ.
* Snažíte se získat přístup k prostředkům v samostatném předplatném.

### <a name="resolution"></a>Řešení

Pokud se tato chyba zobrazí po aktualizaci jednoho AzureRM nebo AZ Module, měli byste všechny moduly aktualizovat na stejnou verzi.

Pokud se snažíte získat přístup k prostředkům v jiném předplatném, můžete nakonfigurovat oprávnění podle následujících pokynů.

1. Přejít na účet Spustit jako pro automatizaci a zkopírovat ID aplikace a kryptografický otisk.

    ![ID a kryptografický otisk kopírování](../media/troubleshoot-runbooks/collect-app-id.png)

1. Přejít na Access Control předplatného, kde není hostovaný účet Automation, a přidejte nové přiřazení role.

    ![Řízení přístupu](../media/troubleshoot-runbooks/access-control.png)

1. Přidejte dříve shromážděné ID aplikace. Vyberte oprávnění Přispěvatel.

    ![Přidat přiřazení role](../media/troubleshoot-runbooks/add-role-assignment.png)

1. Zkopírujte název předplatného.

1. Nyní můžete použít následující kód Runbooku k otestování oprávnění z účtu Automation k druhému předplatnému. Nahraďte `"\<CertificateThumbprint\>"` hodnotou zkopírovanou v kroku 1. Nahraďte `"\<SubscriptionName\>"` hodnotou zkopírovanou v kroku 4.

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>Scénář: Nejde najít předplatné Azure.

### <a name="issue"></a>Problém

Při práci s rutinou `Select-AzureSubscription`, `Select-AzureRMSubscription`nebo `Select-AzSubscription` se zobrazí následující chyba:

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Chyba

K této chybě může dojít, pokud:

* Název předplatného není platný.
* Uživatel Azure Active Directory, který se pokouší získat podrobnosti o předplatném, není nakonfigurovaný jako správce předplatného.
* Tato rutina není k dispozici.

### <a name="resolution"></a>Řešení

Použijte následující postup k určení, jestli jste se ověřili do Azure a máte přístup k předplatnému, které se pokoušíte vybrat.

1. Abyste se ujistili, že váš skript funguje samostatně, otestujte ho mimo Azure Automation.
2. Ujistěte se, že skript před spuštěním `Select-*` rutiny spustí rutinu [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) .
3. Přidejte `Disable-AzContextAutosave –Scope Process` na začátek Runbooku. Toto volání rutiny zajistí, že se jakékoli přihlašovací údaje použijí pouze pro spuštění aktuální sady Runbook.
4. Pokud se tato chybová zpráva zobrazuje stále, upravte kód přidáním `AzContext` parametru pro `Connect-AzAccount`a poté spusťte kód.

   ```powershell
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Scénář: ověřování do Azure selhalo, protože je povolené Multi-Factor Authentication.

### <a name="issue"></a>Problém

Při ověřování v Azure pomocí uživatelského jména a hesla Azure se zobrazí následující chyba:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Příčina

Pokud máte na svém účtu Azure Multi-Factor Authentication, nemůžete k ověřování v Azure použít Azure Active Directoryho uživatele. Místo toho je potřeba k ověření použít certifikát nebo instanční objekt.

### <a name="resolution"></a>Řešení

Pokud chcete použít certifikát s rutinami modelu nasazení Azure Classic, přečtěte si téma [Vytvoření a přidání certifikátu pro správu služeb Azure](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx). Pokud chcete použít instanční objekt s rutinami Azure Resource Manager, přečtěte si téma [Vytvoření instančního objektu pomocí Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md) a [ověřování instančního objektu pomocí Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

## <a name="scenario-you-see-an-error-in-your-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Scénář: v datových proudech úlohy o metodě get_SerializationSettings se zobrazí chyba.

### <a name="issue"></a>Problém

V datových proudech úlohy pro Runbook se zobrazí následující chyba:

```error
Connect-AzAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AZAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```

### <a name="cause"></a>Příčina

Tato chyba je způsobená použitím rutin AzureRM a AZ Module v sadě Runbook. K tomu dojde, když naimportujete modul AZ před importem modulu AzureRM.

### <a name="resolution"></a>Řešení

Rutiny AZ a AzureRM se nedají importovat a používat ve stejné sadě Runbook. Další informace o AZ rutinách v Azure Automation najdete v tématu [Správa modulů v Azure Automation](../shared-resources/modules.md).

## <a name="scenario-the-runbook-fails-with-the-error-a-task-was-canceled"></a><a name="task-was-cancelled"></a>Scénář: sada Runbook se nezdařila s chybou: úloha byla zrušena.

### <a name="issue"></a>Problém

Vaše sada Runbook se nezdařila s chybou, která je podobná následujícímu příkladu:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Příčina

Tato chyba může být způsobena používáním zastaralých modulů Azure.

### <a name="resolution"></a>Řešení

Tuto chybu můžete vyřešit tak, že aktualizujete moduly Azure na nejnovější verzi. 

1. Ve svém účtu Automation klikněte na **moduly**a pak **aktualizujte moduly Azure**. 
2. Tato aktualizace trvá přibližně 15 minut. Po dokončení znovu spusťte sadu Runbook, která selhala.

Další informace o aktualizaci modulů najdete v tématu [aktualizace modulů Azure v Azure Automation](../automation-update-azure-modules.md).

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Scénář: sady Runbook selžou při práci s více předplatnými

### <a name="issue"></a>Problém

Při spouštění Runbooků se sada Runbook nedokáže spravovat prostředky Azure.

### <a name="cause"></a>Příčina

Sada Runbook při spuštění nepoužívá správný kontext.

### <a name="resolution"></a>Řešení

Kontext předplatného může být ztracen, když sada Runbook vyvolá více sad Runbook. Chcete-li zajistit, aby byl kontext předplatného předán do sad Runbook, je nutné, aby sada `Start-AzureRmAutomationRunbook` Runbook klienta předávala kontext rutině v `AzureRmContext` parametru. Použijte `Disable-AzureRmContextAutosave` rutinu s `Scope` parametrem nastaveným `Process` na, abyste zajistili, že zadané přihlašovací údaje se použijí jenom pro aktuální Runbook. Další informace najdete v tématu [práce s více předplatnými](../automation-runbook-execution.md#working-with-multiple-subscriptions).

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –wait
```

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-script"></a><a name="not-recognized-as-cmdlet"></a>Scénář: termín není známý jako název rutiny, funkce, skriptu.

### <a name="issue"></a>Problém

Vaše sada Runbook se nezdařila s chybou, která je podobná následujícímu příkladu:

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Příčina

K této chybě může dojít z následujících důvodů:

* Modul obsahující rutinu se neimportuje do účtu Automation.
* Modul obsahující rutinu je importován, ale je zastaralý.

### <a name="resolution"></a>Řešení

Chcete-li tuto chybu vyřešit, proveďte jednu z následujících úloh. 

* Informace o modulu Azure najdete v tématu [jak aktualizovat Azure PowerShell moduly v Azure Automation](../automation-update-azure-modules.md) a Naučte se, jak aktualizovat moduly v účtu Automation.

* U modulu mimo Azure se ujistěte, že je modul importovaný do svého účtu Automation.

## <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-failed-to-start-each-time"></a><a name="job-attempted-3-times"></a>Scénář: došlo k pokusu o spuštění úlohy Runbooku třikrát, ale nepovedlo se ji spustit pokaždé.

### <a name="issue"></a>Problém

Sada Runbook se nezdařila s následující chybou:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Příčina

K této chybě dochází z důvodu některého z následujících problémů:

* Limit paměti. Úloha může selhat, pokud používá více než 400 MB paměti. Dokumentované limity paměti přidělené izolovanému prostoru (sandbox) najdete v části [omezení služby Automation](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits). 

* Síťové sokety. Izolované prostory Azure jsou omezené na 1000 současných síťových soketů. Viz [omezení služby Automation](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

* Modul je nekompatibilní. Závislosti modulu nemusí být správné. V takovém případě sada Runbook obvykle vrátí zprávu `Command not found` nebo `Cannot bind parameter` .

* Žádné ověřování pomocí služby Active Directory pro izolovaný prostor (sandbox) Váš Runbook se pokusil zavolat spustitelný nebo podproces, který běží v izolovaném prostoru Azure. Konfigurace sad Runbook pro ověřování pomocí Azure AD pomocí knihovny Azure Active Directory Authentication Library (ADAL) není podporována.

* Příliš mnoho dat výjimky. Runbook se pokusil zapsat příliš mnoho dat výjimky do výstupního datového proudu.

### <a name="resolution"></a>Řešení

* Limit paměti, síťové sokety. Navrhované způsoby práce s omezeními paměti jsou rozdělení zatížení mezi více sad Runbook, zpracování méně dat v paměti, vyhněte se zbytečným výstupům z vašich runbooků a vezměte v úvahu, kolik kontrolních bodů se zapisuje do runbooků PowerShellového pracovního postupu. Použijte metodu Clear, například `$myVar.clear`, pro vymazání proměnných a použití `[GC]::Collect` pro okamžité spuštění uvolňování paměti. Tyto akce snižují nároky na paměť Runbooku během běhu.

* Modul je nekompatibilní. Aktualizujte moduly Azure pomocí následujících kroků v tématu [Postup aktualizace Azure PowerShellch modulů v Azure Automation](../automation-update-azure-modules.md).

* Žádné ověřování pomocí služby Active Directory pro izolovaný prostor (sandbox) Při ověřování ve službě Azure AD pomocí Runbooku se ujistěte, že je modul Azure AD dostupný v účtu Automation. Ujistěte se, že jste účtu Spustit jako udělili potřebná oprávnění k provádění úloh, které sada Runbook automatizuje.

  Pokud vaše sada Runbook nemůže volat spustitelný soubor nebo podproces spuštěný v izolovaném prostoru Azure, použijte sadu Runbook na [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Hybridní procesy nejsou omezeny omezeními paměti a sítě, které mají Azure Sandbox.

* Příliš mnoho dat výjimky. Výstupní datový proud úlohy má omezení 1 MB. Zajistěte, aby sada Runbook zahrnovala volání spustitelného souboru nebo `try` podprocesu pomocí bloků a `catch` . Pokud operace vyvolávají výjimku, kód zapíše zprávu z výjimky do proměnné Automation. Tato technika zabrání v zápisu zprávy do výstupního datového proudu úlohy.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Scénář: přihlášení k účtu Azure selhalo.

### <a name="issue"></a>Problém

Při práci s `Connect-AzAccount` rutinou se zobrazí jedna z následujících chyb:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Příčina

K těmto chybám dochází, pokud název assetu přihlašovacích údajů není platný. Můžou se taky vyskytnout v případě, že uživatelské jméno a heslo, které jste použili k nastavení assetu přihlašovacích údajů Automation, nejsou platné.

### <a name="resolution"></a>Řešení

Chcete-li zjistit, co je chybné, proveďte následující kroky:

1. Ujistěte se, že nemáte žádné speciální znaky. Tyto znaky obsahují `\@` znak v názvu assetu přihlašovacích údajů automatizace, který používáte pro připojení k Azure.
2. Zkontrolujte, jestli můžete použít uživatelské jméno a heslo, které jsou uložené v přihlašovacích údajích Azure Automation v místním editoru prostředí PowerShell ISE. V prostředí PowerShell ISE spusťte následující rutiny.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount –Credential $Cred
   ```

3. Pokud se ověření nepovede místně, nenastavíte správně své přihlašovací údaje Azure Active Directory. Pokud chcete účet Azure Active Directory správně nastavit, přečtěte si téma [ověřování v Azure pomocí blogu Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) Blogový příspěvek.

4. Pokud se zdá, že chyba je přechodná, zkuste do rutiny ověřování přidat logiku opakování, aby se ověřování zajistilo robustnější.

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
       $connectionResult = Connect-AzAccount `
                              -ServicePrincipal `
                              -Tenant $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>Scénář: nesprávný odkaz na objekt při volání Add-AzAccount

### <a name="issue"></a>Problém

Tato chyba se zobrazí při práci s `Add-AzAccount`, což je alias pro `Connect-AzAccount` rutinu:

```error
Add-AzAccount : Object reference not set to an instance of an object
```

### <a name="cause"></a>Příčina

K této chybě může dojít, pokud sada Runbook neprovádí správné kroky před `Add-AzAccount` voláním pro přidání účtu Automation. Příkladem jednoho z nezbytných kroků je přihlášení pomocí účtu Spustit jako. Správné operace, které se mají použít ve vašem Runbooku, najdete v tématu [Spuštění Runbooku v Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-execution).

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Scénář: odkaz na objekt není nastavený na instanci objektu.

### <a name="issue"></a>Problém

Při vyvolání podřízeného Runbooku s `Wait` parametrem a výstupním datovým proudem, který obsahuje objekt, se zobrazí následující chyba:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Příčina

`Start-AzAutomationRunbook`nezpracovává výstupní datový proud správně, pokud datový proud obsahuje objekty.

### <a name="resolution"></a>Řešení

Doporučuje se implementovat logiku cyklického dotazování a načíst výstup pomocí rutiny [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) . Ukázka této logiky je definována níže.

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzAutomationJob
}

$jobResults | Get-AzAutomationJobOutput | Get-AzAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>Scénář: sada Runbook se nezdařila z důvodu deserializace objektu

### <a name="issue"></a>Problém

Vaše sada Runbook se nezdařila s chybou:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Příčina

Pokud je vaše sada Runbook pracovním postupem PowerShellu, ukládá složité objekty v deserializovaném formátu pro zachování stavu Runbooku, pokud je pracovní postup pozastaven.

### <a name="resolution"></a>Řešení

Tento problém můžete vyřešit pomocí některého z následujících řešení.

* Pokud vytváříte kanál komplexních objektů z jedné rutiny na jinou, zabalte tyto rutiny do `InlineScript` aktivity.
* Předejte název nebo hodnotu, kterou potřebujete ze složitého objektu, místo předání celého objektu.
* Použijte PowerShellový Runbook místo Runbooku pracovního postupu PowerShellu.

## <a name="scenario-runbook-job-fails-because-allocated-quota-exceeded"></a><a name="quota-exceeded"></a>Scénář: úloha sady Runbook selhala, protože byla překročena přidělená kvóta

### <a name="issue"></a>Problém

Vaše úloha Runbooku se nezdařila s chybou:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Příčina

K této chybě dojde, pokud provádění úlohy překročí kvótu Free 500-minut pro váš účet. Tato kvóta se vztahuje na všechny typy úloh spuštění úlohy. Některé z těchto úloh testuje úlohu, spouštíte úlohu z portálu, spouštíte úlohu pomocí webhooků nebo naplánujete úlohu, která se má spustit, a to pomocí Azure Portal nebo vašeho datacentra. Další informace o cenách pro automatizaci najdete v tématu [ceny služby Automation](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Řešení

Pokud chcete použít více než 500 minut zpracování za měsíc, změňte předplatné z úrovně Free na úroveň Basic.

1. Přihlaste se ke svému předplatnému Azure.
2. Vyberte účet Automation, který chcete upgradovat.
3. Klikněte na **Nastavení**a pak na **ceny**.
4. Kliknutím na **Povolit** na stránce dole upgradujte svůj účet na úroveň Basic.

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Scénář: rutina se nerozpoznala při provádění Runbooku.

### <a name="issue"></a>Problém

Vaše úloha Runbooku se nezdařila s chybou:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Příčina

Tato chyba je způsobená tím, že modul PowerShellu nemůže najít rutinu, kterou používáte v Runbooku. Je možné, že modul, který obsahuje rutinu, v účtu chybí, je v konfliktu s názvem Runbooku nebo rutina existuje i v jiném modulu a automatizace nemůže tento název přeložit.

### <a name="resolution"></a>Řešení

K vyřešení problému použijte kterékoli z následujících řešení.

* Ujistěte se, že jste zadali správný název rutiny.
* Ujistěte se, že rutina existuje ve vašem účtu Automation a že neexistují žádné konflikty. Chcete-li ověřit, zda je rutina přítomna, otevřete sadu Runbook v režimu úprav a vyhledejte rutinu, kterou chcete najít v knihovně, `Get-Command <CommandName>`nebo spusťte příkaz. Jakmile ověříte, že je rutina pro účet k dispozici a že nedochází ke konfliktu názvů s jinými rutinami nebo sadami Runbook, přidejte rutinu na plátno a ujistěte se, že v Runbooku používáte platnou sadu parametrů.
* Pokud dojde ke konfliktu názvů a tato rutina je k dispozici ve dvou různých modulech, vyřešte tento problém pomocí plně kvalifikovaného názvu rutiny. Můžete například použít `ModuleName\CmdletName`.
* Pokud spouštíte místní Runbook v rámci skupiny Hybrid Worker, ujistěte se, že modul a rutina jsou nainstalovány v počítači, který je hostitelem hybridního pracovního procesu.

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Scénář: Nepodařilo se dokončit dlouho běžící sadu Runbook.

### <a name="issue"></a>Problém

Sada Runbook se po uplynutí 3 hodin zobrazí v zastaveném stavu. Může se zobrazit také tato chyba:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Toto chování je záměrné v izolovaném prostoru (sandbox) v Azure kvůli [spravedlivému monitorování sdílených](../automation-runbook-execution.md#fair-share) procesů v rámci Azure Automation. Pokud se proces spustí déle než tři hodiny, bude při automatickém sdílení zastaveno sady Runbook. Stav sady Runbook, která se nachází po nepřiměřeném časovém limitu sdílení, se liší podle typu Runbooku. Runbooky PowerShellu a Python jsou nastavené na stav zastaveno. Runbooky pracovních postupů PowerShellu jsou nastaveny na neúspěšné.

### <a name="cause"></a>Příčina

Sada Runbook běžela prostřednictvím limitu 3 hodin, který je povolený pro sdílení v izolovaném prostoru Azure.

### <a name="resolution"></a>Řešení

Jedním z doporučených řešení je spuštění sady Runbook na [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Hybridní pracovní procesy nejsou omezené o 3 hodinovou sadu Runbook s reálným podílem, kterou mají Azure sandboxy. Runbooky spuštěné na hybridních pracovních procesech Runbooku by se měly vyvíjet pro podporu chování restartování, pokud dojde k neočekávaným problémům s místní infrastrukturou.

Dalším řešením je optimalizovat sadu Runbook vytvořením [podřízených runbooků](../automation-child-runbooks.md). Pokud vaše sada Runbook projde stejnou funkcí u několika prostředků, například v databázové operaci na několika databázích, můžete funkci přesunout do podřízeného Runbooku. Každá podřízená sada Runbook se spouští paralelně v samostatném procesu. Toto chování zkrátí celkovou dobu, po kterou se nadřazený Runbook dokončí.

Rutiny PowerShellu, které umožňují podřízený scénář sady Runbook:

* [Start – AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0). Tato rutina vám umožní spustit runbook a předat do něj parametry.

* [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0). Pokud existují operace, které je třeba provést po dokončení podřízeného Runbooku, tato rutina vám umožní zjistit stav úlohy pro každou podřízenou položku.

## <a name="scenario-status-400-bad-request-when-calling-a-webhook"></a><a name="expired webhook"></a>Scénář: stav: 400 Chybný požadavek při volání Webhooku

### <a name="issue"></a>Problém

Při pokusu o vyvolání Webhooku pro Azure Automation sadu Runbook se zobrazí následující chyba:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Příčina

Webhook, který se pokoušíte volat, je buď zakázán, nebo vypršela jeho platnost.

### <a name="resolution"></a>Řešení

Pokud je Webhook zakázaný, můžete Webhook znovu povolit prostřednictvím Azure Portal. Pokud vypršela platnost Webhooku, musíte ho odstranit a pak znovu vytvořit. Webhook se dá [obnovit jenom v](../automation-webhooks.md#renew-webhook) případě, že ještě nevypršela jeho platnost.

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Scénář: 429: frekvence požadavků je aktuálně příliš velká...

### <a name="issue"></a>Problém

Při spuštění `Get-AzAutomationJobOutput` rutiny se zobrazí následující chybová zpráva:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Příčina

K této chybě může dojít při načítání výstupu úlohy z Runbooku, který má mnoho [podrobných streamů](../automation-runbook-output-and-messages.md#verbose-stream).

### <a name="resolution"></a>Řešení

Chcete-li vyřešit tuto chybu, proveďte jednu z následujících akcí.

* Upravte sadu Runbook a snižte počet datových proudů úloh, které vygeneruje.

* Snižte počet datových proudů, které se mají načíst při spuštění rutiny. K tomu můžete nastavit hodnotu `Stream` parametru pro rutinu [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) , aby se načetly jenom výstupní datové proudy. 

## <a name="scenario-powershell-job-fails-with-error-cannot-invoke-method"></a><a name="cannot-invoke-method"></a>Scénář: úloha PowerShellu se nezdařila s chybou: nelze vyvolat metodu.

### <a name="issue"></a>Problém

Při spuštění úlohy PowerShellu v Runbooku běžícím v Azure se zobrazí následující chybová zpráva:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Příčina

Tato chyba může znamenat, že Runbooky spuštěné v izolovaném prostoru Azure nemohou běžet v [plném jazykovém režimu](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="resolution"></a>Řešení

Tuto chybu můžete vyřešit dvěma způsoby.

* Místo použití funkce [Start-Job](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/start-job?view=powershell-7)použijte příkaz [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) a spusťte sadu Runbook.
* Zkuste spustit Runbook na Hybrid Runbook Worker.

Další informace o tomto chování a dalších chování sady Runbook Azure Automation naleznete v tématu [chování sady Runbook](../automation-runbook-execution.md#runbook-behavior).

## <a name="scenario-linux-hybrid-runbook-worker-receives-a-prompt-for-a-password-when-signing-a-runbook"></a>Scénář: Linux Hybrid Runbook Worker při podepisování Runbooku zobrazit výzvu k zadání hesla

### <a name="issue"></a>Problém

Spuštění `sudo` příkazu pro Linux Hybrid Runbook Worker načte neočekávanou výzvu k zadání hesla.

### <a name="cause"></a>Příčina

Účet **nxautomationuser** pro agenta Log Analytics pro Linux není správně nakonfigurovaný v souboru **sudoers** . Hybrid Runbook Worker potřebuje odpovídající konfiguraci oprávnění účtu a dalších dat, aby mohla podepisovat Runbooky na Linux Runbook Worker.

### <a name="resolution"></a>Řešení

* Ujistěte se, že Hybrid Runbook Worker má na počítači spustitelný soubor GnuPG (GPG).

* Ověřte konfiguraci účtu **nxautomationuser** v souboru **sudoers** . Viz [spouštění Runbooků na Hybrid Runbook Worker](../automation-hrw-run-runbooks.md)

## <a name="scenario-cmdlet-failing-in-pnp-powershell-runbook-on-azure-automation"></a>Scénář: selhání rutiny v Runbooku PowerShellu PnP v Azure Automation

### <a name="issue"></a>Problém

Když sada Runbook zapíše objekt generovaný PowerShellem PnP do výstupního Azure Automation přímo, výstup rutiny nemůže streamovat zpět do automatizace.

### <a name="cause"></a>Příčina

K tomuto problému obvykle dochází, když Azure Automation zpracovává Runbooky, které vyvolávají rutiny PowerShellu pro `add-pnplistitem`PNP, například bez zachycení vrácených objektů.

### <a name="resolution"></a>Řešení

Upravte skripty, abyste přiřadili jakékoli návratové hodnoty proměnným, aby se rutiny nepokoušely o zápis celých objektů do standardního výstupu. Skript může přesměrovat výstupní datový proud do rutiny, jak je znázorněno níže.

```azurecli
  $null = add-pnplistitem
```

Pokud váš skript analyzuje výstup rutiny, skript musí uložit výstup do proměnné a manipulovat s proměnnou namísto pouhého streamování výstupu.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-invalid-status-code-forbidden-when-using-key-vault-inside-a-runbook"></a>Scénář: neplatný stavový kód zakázán při použití Key Vault v Runbooku

### <a name="issue"></a>Problém

Při pokusu o přístup k Key Vault prostřednictvím sady Azure Automation Runbook se zobrazí následující chyba:

```error
Operation returned an invalid status code 'Forbidden' 
```

### <a name="cause"></a>Příčina

Možné příčiny tohoto problému:

* Nepoužívejte účet Spustit jako.
* Nedostatečná oprávnění

### <a name="resolution"></a>Řešení

#### <a name="not-using-run-as-account"></a>Nepoužívat účet Spustit jako

Postupujte podle kroků v [kroku 5 – přidání ověřování pro správu prostředků Azure](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell#add-authentication-to-manage-azure-resources) , abyste měli jistotu, že používáte účet Spustit jako pro přístup k Key Vault. 

#### <a name="insufficient-permissions"></a>Nedostatečná oprávnění

Postupujte podle kroků v [Přidání oprávnění Key Vault](https://docs.microsoft.com/azure/automation/manage-runas-account#add-permissions-to-key-vault) , abyste zajistili, že účet Spustit jako má dostatečná oprávnění pro přístup k Key Vault. 

## <a name="my-problem-isnt-listed-above"></a><a name="other"></a>Můj problém není uvedený výše.

Níže uvedené části obsahují další běžné chyby a poskytují podpůrnou dokumentaci, která vám pomůžou problém vyřešit.

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>Hybrid Runbook Worker nespouští úlohy nebo nereaguje

Pokud spouštíte úlohy na Hybrid Runbook Worker, nikoli v Azure Automation, možná budete muset [vyřešit problémy samotného hybridního pracovního procesu](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker).

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>Sada Runbook se nezdařila bez oprávnění nebo některé varianty

Účty Spustit jako nemusí mít stejná oprávnění oproti prostředkům Azure jako váš aktuální účet. Ujistěte se, že váš účet Spustit jako má [oprávnění pro přístup k jakýmkoli prostředkům](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) používaným ve vašem skriptu.

### <a name="issues-passing-parameters-into-webhooks"></a>Problémy s předáváním parametrů do webhooků

Nápovědu k předávání parametrů do webhooků najdete v tématu [Spuštění runbooku z Webhooku](../automation-webhooks.md#parameters-used-when-the-webhook-starts-a-runbook).

### <a name="issues-using-az-modules"></a>Problémy s použitím AZ modules

Použití AZ modules a AzureRM modulů ve stejném účtu Automation se nepodporuje. Další podrobnosti najdete v tématu [AZ modules in Runbooky](https://docs.microsoft.com/azure/automation/az-modules) .

### <a name="inconsistent-behavior-in-runbooks"></a>Nekonzistentní chování runbooků

Postupujte podle pokynů v části [Spuštění Runbooku](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior) , abyste se vyhnuli problémům s souběžnými úlohami, prostředky, které se vytváří několikrát, nebo jiná logika časování v sadách Runbook.

### <a name="runbook-fails-with-the-error-no-permission-forbidden-403-or-some-variation"></a>Sada Runbook se nezdařila s chybou bez oprávnění, zakázáno (403) nebo některé varianty

Účty Spustit jako nemusí mít stejná oprávnění oproti prostředkům Azure jako váš aktuální účet. Ujistěte se, že váš účet Spustit jako má [oprávnění pro přístup k jakýmkoli prostředkům](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) používaným ve vašem skriptu.

### <a name="runbooks-were-working-but-suddenly-stopped"></a>Runbooky fungovaly, ale najednou fungovat přestaly

* Zajistěte, aby účet Spustit jako nevypršel. Viz [obnovení certifikace](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Pokud k zahájení Runbooku používáte [Webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook) , zajistěte, aby Webhook nevypršel.

### <a name="passing-parameters-into-webhooks"></a>Předávání parametrů do webhooků

Nápovědu k předávání parametrů do webhooků najdete v tématu [Spuštění runbooku z Webhooku](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters-used-when-the-webhook-starts-a-runbook).

### <a name="using-az-modules"></a>Používání modulů Az

Použití AZ modules a AzureRM modulů ve stejném účtu Automation se nepodporuje. Viz [AZ modules in runbookys](https://docs.microsoft.com/azure/automation/az-modules).

### <a name="using-self-signed-certificates"></a>Používání certifikátů podepsaných svým držitelem

Pokud chcete používat certifikáty podepsané svým držitelem, přečtěte si téma [Vytvoření nového certifikátu](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate).

### <a name="access-denied-when-using-azure-sandbox-for-a-runbook"></a>Přístup odepřen při použití izolovaného prostoru (sandbox) Azure pro Runbook

Izolovaný prostor Azure zabraňuje přístupu ke všem nezpracovaným serverům COM. Například aplikace nebo sada Runbook v izolovaném prostoru nemůže volat do rozhraní WMI (Windows Management Instrumentation) (WMI) nebo do služby Instalační služba systému Windows (msiserver. exe). Podrobnosti o použití izolovaného prostoru naleznete [v tématu Spuštění Runbooku v Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-execution).

## <a name="recommended-documents"></a>Doporučené dokumenty

* [Spuštění Runbooku v Azure Automation](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Spouštění runbooků ve službě Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se [@AzureSupport](https://twitter.com/azuresupport) s nástrojem – oficiální Microsoft Azure účet pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure s správnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
