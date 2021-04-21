---
title: Řešení potíží s Azure Automation Runbook
description: Tento článek popisuje, jak řešit problémy s Azure Automation Runbooky a řešit problémy.
services: automation
ms.date: 02/11/2021
ms.topic: troubleshooting
ms.custom: has-adal-ref, devx-track-azurepowershell
ms.openlocfilehash: 7964bc62aefc912a0f61744841784600575c98de
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831217"
---
# <a name="troubleshoot-runbook-issues"></a>Řešení problémů s runbooky

 Tento článek popisuje problémy sady Runbook, které mohou nastat, a způsob jejich řešení. Obecné informace najdete v tématu [Spuštění Runbooku v Azure Automation](../automation-runbook-execution.md).

## <a name="diagnose-runbook-issues"></a>Diagnostikovat problémy Runbooku

Když při spuštění sady Runbook v Azure Automation dojde k chybám, můžete při diagnostice těchto potíží použít následující postup:

1. Ujistěte se, že se váš skript Runbooku úspěšně spouští na vašem místním počítači.

    Referenční materiály pro jazyky a moduly učení najdete v dokumentaci k [PowerShellu](/powershell/scripting/overview) nebo [dokumentaci k Pythonu](https://docs.python.org/3/). Spuštění skriptu lokálně může zjišťovat a řešit běžné chyby, jako například:

      * Chybějící moduly
      * Chyby syntaxe
      * Logické chyby

1. Prozkoumejte [streamy chyb](../automation-runbook-output-and-messages.md#runbook-output)sady Runbook.

    Podívejte se na tyto datové proudy pro konkrétní zprávy a porovnejte je s chybami popsanými v tomto článku.

1. Ujistěte se, že vaše uzly a pracovní prostor služby Automation mají požadované moduly.

    Pokud vaše sada Runbook importuje nějaké moduly, ověřte, že jsou k dispozici pro váš účet Automation pomocí kroků v části [Import modulů](../shared-resources/modules.md#import-modules). Aktualizujte moduly PowerShellu na nejnovější verzi podle pokynů v tématu [aktualizace Azure PowerShell moduly v Azure Automation](../automation-update-azure-modules.md). Další informace o řešení potíží najdete v tématu [řešení potíží s moduly](shared-resources.md#modules).

1. Pokud je váš Runbook pozastaven nebo neočekávaně, dojde k chybě:

    * [Obnovte certifikát](../manage-runas-account.md#cert-renewal) , pokud vypršela platnost účtu Spustit jako.
    * [Obnovte Webhook](../automation-webhooks.md#renew-a-webhook) , pokud se pokoušíte použít Webhook, jehož platnost vypršela, aby se Runbook spustil.
    * [Zkontrolujte stav úlohy](../automation-runbook-execution.md#job-statuses) a určete aktuální stavy Runbooku a některé možné příčiny problému.
    * [Přidejte do Runbooku další výstup](../automation-runbook-output-and-messages.md#working-with-message-streams) , abyste mohli zjistit, co se stane, než se Runbook pozastaví.
    * [Zpracujte všechny výjimky](../automation-runbook-execution.md#exceptions) , které jsou vyvolány vaší úlohou.

1. Tento krok proveďte, pokud úloha sady Runbook nebo prostředí na Hybrid Runbook Worker nereaguje.

    Pokud vaše Runbooky spouštíte na Hybrid Runbook Worker, nikoli v Azure Automation, možná budete muset [vyřešit problémy samotného hybridního pracovního procesu](hybrid-runbook-worker.md).

## <a name="scenario-runbook-fails-with-a-no-permission-or-forbidden-403-error"></a><a name="runbook-fails-no-permission"></a>Scénář: sada Runbook se nezdařila s oprávněním nebo zakázanou chybou 403

### <a name="issue"></a>Problém

Vaše sada Runbook se nezdařila s oprávněním nebo zakázanou chybou 403 nebo ekvivalentní.

### <a name="cause"></a>Příčina

Účty Spustit jako nemusí mít stejná oprávnění jako u prostředků Azure jako aktuální účet Automation. 

### <a name="resolution"></a>Řešení

Ujistěte se, že váš účet Spustit jako má [oprávnění pro přístup k jakýmkoli prostředkům](../../role-based-access-control/role-assignments-portal.md) používaným ve vašem skriptu.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Scénář: přihlášení k účtu Azure selhalo.

### <a name="issue"></a>Problém

Při práci s rutinou se zobrazí jedna z následujících chyb `Connect-AzAccount` :

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Příčina

K těmto chybám dochází, pokud název assetu přihlašovacích údajů není platný. Můžou se taky vyskytnout v případě, že uživatelské jméno a heslo, které jste použili k nastavení assetu přihlašovacích údajů Automation, nejsou platné.

### <a name="resolution"></a>Řešení

Chcete-li zjistit, co je chybné, postupujte podle následujících kroků:

1. Ujistěte se, že nemáte žádné speciální znaky. Tyto znaky obsahují `\@` znak v názvu assetu přihlašovacích údajů automatizace, který používáte pro připojení k Azure.
1. Zkontrolujte, jestli můžete použít uživatelské jméno a heslo, které jsou uložené v přihlašovacích údajích Azure Automation v místním editoru prostředí PowerShell ISE. V prostředí PowerShell ISE spusťte následující rutiny.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount -Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount -Credential $Cred
   ```

1. Pokud se ověření nepovede místně, nenastavíte správně své přihlašovací údaje pro Azure Active Directory (Azure AD). Pokud chcete správně nastavit účet Azure AD, přečtěte si článek [ověřování v Azure pomocí Azure Active Directory](../automation-use-azure-ad.md).

1. Pokud se zdá, že chyba je přechodná, zkuste do rutiny ověřování přidat logiku opakování, aby se ověřování zajistilo robustnější.

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

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Scénář: spuštění Login-AzureRMAccount pro přihlášení

### <a name="issue"></a>Problém

Při spuštění sady Runbook se zobrazí následující chyba:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Příčina

K této chybě může dojít, pokud nepoužíváte účet Spustit jako nebo vypršela platnost účtu Spustit jako. Další informace najdete v tématu [Přehled účtů spustit jako Azure Automation](../automation-security-overview.md#run-as-accounts).

Tato chyba má dvě primární příčiny:

* Existují různé verze modulu AzureRM nebo AZ.
* Snažíte se získat přístup k prostředkům v samostatném předplatném.

### <a name="resolution"></a>Řešení

Pokud se tato chyba zobrazí po aktualizaci jednoho AzureRM nebo AZ Module, aktualizujte všechny moduly na stejnou verzi.

Pokud se pokoušíte o přístup k prostředkům v jiném předplatném, postupujte podle těchto kroků a nakonfigurujte oprávnění:

1. Přejít na účet Spustit jako pro automatizaci a zkopírovat **ID aplikace** a **kryptografický otisk**.

    ![Kopírovat ID aplikace a kryptografický otisk](../media/troubleshoot-runbooks/collect-app-id.png)

1. Přejděte do **řízení přístupu** k předplatnému, kde *není hostovaný účet* Automation, a přidejte nové přiřazení role.

    ![Řízení přístupu](../media/troubleshoot-runbooks/access-control.png)

1. Přidejte dříve shromážděné **ID aplikace** . Vyberte oprávnění **Přispěvatel** .

    ![Přidat přiřazení role](../media/troubleshoot-runbooks/add-role-assignment.png)

1. Zkopírujte název předplatného.

1. Nyní můžete použít následující kód Runbooku k otestování oprávnění z účtu Automation k druhému předplatnému. Nahraďte `<CertificateThumbprint>` hodnotou zkopírovanou v kroku 1. Nahraďte `"<SubscriptionName>"` hodnotou zkopírovanou v kroku 4.

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

Při práci s `Select-AzureSubscription` rutinou, nebo se zobrazí následující chyba `Select-AzureRMSubscription` `Select-AzSubscription` :

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Chyba

K této chybě může dojít, pokud:

* Název předplatného není platný.
* Uživatel Azure AD, který se pokouší získat podrobnosti o předplatném, není nakonfigurovaný jako správce předplatného.
* Tato rutina není k dispozici.

### <a name="resolution"></a>Řešení

Pomocí těchto kroků zjistíte, jestli jste se ověřili do Azure a máte přístup k předplatnému, které se pokoušíte vybrat:

1. Abyste se ujistili, že váš skript funguje samostatně, otestujte ho mimo Azure Automation.
1. Ujistěte se, že skript před spuštěním rutiny spustí rutinu [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) `Select-*` .
1. Přidejte `Disable-AzContextAutosave -Scope Process` na začátek Runbooku. Tato rutina zajišťuje, že se jakékoli přihlašovací údaje použijí pouze pro spuštění aktuální sady Runbook.
1. Pokud se chybová zpráva zobrazuje stále, upravte kód přidáním `AzContext` parametru pro `Connect-AzAccount` a poté spusťte kód.

   ```powershell
   Disable-AzContextAutosave -Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Scénář: sady Runbook selžou při práci s více předplatnými

### <a name="issue"></a>Problém

Při spouštění Runbooků se sada Runbook nedokáže spravovat prostředky Azure.

### <a name="cause"></a>Příčina

Sada Runbook při spuštění nepoužívá správný kontext. Důvodem může být to, že se Runbook nesnaží získat přístup k nesprávnému předplatnému.

Můžou se zobrazit chyby, jako je tato:

```error
Get-AzVM : The client '<automation-runas-account-guid>' with object id '<automation-runas-account-guid>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope '/subscriptions/<subcriptionIdOfSubscriptionWichDoesntContainTheVM>/resourceGroups/REsourceGroupName/providers/Microsoft.Compute/virtualMachines/VMName '.
   ErrorCode: AuthorizationFailed
   StatusCode: 403
   ReasonPhrase: Forbidden Operation
   ID : <AGuidRepresentingTheOperation> At line:51 char:7 + $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $UNBV... +
```

### <a name="resolution"></a>Řešení

Kontext předplatného může být ztracen, když sada Runbook vyvolá více sad Runbook. Chcete-li se vyhnout nechtěnému pokusu o přístup k nesprávnému předplatnému, postupujte podle pokynů níže.

* Abyste se vyhnuli odkazování na špatné předplatné, zakažte v automatizaci v sadě Runbook ukládání kontextu pomocí následujícího kódu na začátku každé sady Runbook.

   ```azurepowershell-interactive
   Disable-AzContextAutosave -Scope Process
   ```

* Rutiny Azure PowerShell podporují `-DefaultProfile` parametr. Přidali jsme všechny rutiny AZ a AzureRm, které podporují spouštění více skriptů PowerShellu ve stejném procesu a umožňují určit kontext a které předplatné použít pro jednotlivé rutiny. Pomocí sad Runbook byste při vytváření sady Runbook měli objekt kontextu Uložit (tj. když se účet přihlásí) a pokaždé, když se změní, a při zadání rutiny AZ odkazovat na kontext.

   > [!NOTE]
   > Objekt kontextu byste měli předat i v případě, že pracujete s kontextem přímo pomocí rutin, jako je [set-AzContext](/powershell/module/az.accounts/Set-AzContext) nebo [Select-AzSubscription](/powershell/module/servicemanagement/azure.service/set-azuresubscription).

   ```azurepowershell-interactive
   $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName 
   $context = Add-AzAccount `
             -ServicePrincipal `
             -TenantId $servicePrincipalConnection.TenantId `
             -ApplicationId $servicePrincipalConnection.ApplicationId `
             -Subscription 'cd4dxxxx-xxxx-xxxx-xxxx-xxxxxxxx9749' `
             -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
   $context = Set-AzContext -SubscriptionName $subscription `
       -DefaultProfile $context
   Get-AzVm -DefaultProfile $context
   ```
  
## <a name="scenario-authentication-to-azure-fails-because-multifactor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Scénář: ověřování do Azure selhává, protože je povolené vícefaktorové ověřování.

### <a name="issue"></a>Problém

Při ověřování v Azure pomocí uživatelského jména a hesla Azure se zobrazí následující chyba:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Příčina

Pokud máte ve svém účtu Azure vícefaktorové ověřování, nemůžete k ověření v Azure použít Azure Active Directoryho uživatele. Místo toho je potřeba k ověření použít certifikát nebo instanční objekt.

### <a name="resolution"></a>Řešení

Pokud chcete použít účet Spustit jako pro Classic s rutinami modelu nasazení Azure Classic, přečtěte si téma [Vytvoření účtu Spustit jako pro Azure Classic pro správu služeb Azure](../automation-create-standalone-account.md#create-a-classic-run-as-account). Pokud chcete použít instanční objekt s rutinami Azure Resource Manager, přečtěte si téma [Vytvoření instančního objektu pomocí Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md) a [ověřování instančního objektu pomocí Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

## <a name="scenario-runbook-fails-with-a-task-was-canceled-error-message"></a><a name="task-was-cancelled"></a>Scénář: sada Runbook se nezdařila s chybovou zprávou "úloha byla zrušena"

### <a name="issue"></a>Problém

Vaše sada Runbook se nezdařila s chybou, která je podobná následujícímu příkladu:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Příčina

Tato chyba může být způsobena používáním zastaralých modulů Azure.

### <a name="resolution"></a>Řešení

Tuto chybu můžete vyřešit tak, že aktualizujete moduly Azure na nejnovější verzi:

1. V účtu Automation vyberte **moduly** a pak vyberte **aktualizovat moduly Azure**.
1. Tato aktualizace trvá přibližně 15 minut. Po dokončení spusťte znovu sadu Runbook, která selhala.

Další informace o aktualizaci modulů najdete v tématu [aktualizace modulů Azure v Azure Automation](../automation-update-azure-modules.md).

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-or-script"></a><a name="not-recognized-as-cmdlet"></a>Scénář: termín není známý jako název rutiny, funkce nebo skriptu.

### <a name="issue"></a>Problém

Vaše sada Runbook se nezdařila s chybou, která je podobná následujícímu příkladu:

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Příčina

K této chybě může dojít z následujících důvodů:

* Modul, který obsahuje rutinu, se do účtu Automation neimportuje.
* Modul, který obsahuje rutinu, je importován, ale je zastaralý.

### <a name="resolution"></a>Řešení

Chcete-li vyřešit tuto chybu, proveďte jeden z následujících kroků:

* Informace o modulu Azure najdete v tématu [jak aktualizovat Azure PowerShell moduly v Azure Automation](../automation-update-azure-modules.md) a Naučte se, jak aktualizovat moduly v účtu Automation.
* U modulu mimo Azure se ujistěte, že je modul importovaný do svého účtu Automation.

## <a name="scenario-cmdlet-fails-in-pnp-powershell-runbook-on-azure-automation"></a>Scénář: rutina selhala v Runbooku PowerShellu PnP v Azure Automation

### <a name="issue"></a>Problém

Když sada Runbook zapíše objekt generovaný PowerShellem PnP do výstupního Azure Automation přímo, výstup rutiny nemůže streamovat zpět do automatizace.

### <a name="cause"></a>Příčina

K tomuto problému obvykle dochází, když Azure Automation zpracovává Runbooky, které vyvolávají rutiny PowerShellu pro PnP, například `add-pnplistitem` bez zachycení vrácených objektů.

### <a name="resolution"></a>Řešení

Upravte skripty, abyste přiřadili jakékoli návratové hodnoty proměnným, aby se rutiny nepokoušely o zápis celých objektů do standardního výstupu. Skript může přesměrovat výstupní datový proud do rutiny, jak je znázorněno zde.

```azurecli
  $null = add-pnplistitem
```

Pokud váš skript analyzuje výstup rutiny, skript musí uložit výstup do proměnné a manipulovat s proměnnou namísto pouhého streamování výstupu.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Scénář: rutina se nerozpoznala při provádění Runbooku.

### <a name="issue"></a>Problém

Vaše úloha Runbooku se nezdařila s chybou:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Příčina

Tato chyba je způsobená tím, že modul PowerShellu nemůže najít rutinu, kterou používáte v Runbooku. Je možné, že modul, který obsahuje rutinu, v účtu chybí, je v konfliktu s názvem Runbooku nebo rutina existuje i v jiném modulu a automatizace nemůže tento název přeložit.

### <a name="resolution"></a>Řešení

K vyřešení problému použijte kterékoli z následujících řešení:

* Ujistěte se, že jste zadali správný název rutiny.
* Ujistěte se, že rutina existuje ve vašem účtu Automation a že neexistují žádné konflikty. Chcete-li ověřit, zda je rutina přítomna, otevřete sadu Runbook v režimu úprav a vyhledejte rutinu, kterou chcete najít v knihovně, nebo spusťte příkaz `Get-Command <CommandName>` . Po ověření, že je rutina pro účet k dispozici a že nedochází ke konfliktu názvů s jinými rutinami nebo sadami Runbook, přidejte rutinu na plátno. Ujistěte se, že používáte platnou sadu parametrů v Runbooku.
* Pokud dojde ke konfliktu názvů a tato rutina je k dispozici ve dvou různých modulech, vyřešte tento problém pomocí plně kvalifikovaného názvu rutiny. Můžete například použít `ModuleName\CmdletName`.
* Pokud spouštíte místní Runbook v rámci skupiny Hybrid Worker, ujistěte se, že modul a rutina jsou nainstalovány v počítači, který je hostitelem hybridního pracovního procesu.

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>Scénář: nesprávný odkaz na objekt při volání Add-AzAccount

### <a name="issue"></a>Problém

Tato chyba se zobrazí při práci s `Add-AzAccount` , což je alias pro `Connect-AzAccount` rutinu:

```error
Add-AzAccount : Object reference not set to an instance of an object
```

### <a name="cause"></a>Příčina

K této chybě může dojít, pokud sada Runbook neprovádí správné kroky před voláním `Add-AzAccount` pro přidání účtu Automation. Příkladem jednoho z nezbytných kroků je přihlášení pomocí účtu Spustit jako. Správné operace, které se mají použít ve vašem Runbooku, najdete v tématu [Spuštění Runbooku v Azure Automation](../automation-runbook-execution.md).

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Scénář: odkaz na objekt není nastavený na instanci objektu.

### <a name="issue"></a>Problém

Následující chyba se zobrazí při vyvolání podřízeného Runbooku s `Wait` parametrem a výstupní datový proud obsahuje objekt:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Příčina

Pokud datový proud obsahuje objekty, `Start-AzAutomationRunbook` nezpracovává výstupní datový proud správně.

### <a name="resolution"></a>Řešení

Implementujte logiku cyklického dotazování a pomocí rutiny [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) načtěte výstup. Ukázka této logiky je definována zde:

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
while($false -eq (IsJobTerminalState $job.Status) -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzAutomationJob
}

$job | Get-AzAutomationJobOutput | Get-AzAutomationJobOutputRecord | Select-Object -ExpandProperty Value
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

Tento problém můžete vyřešit pomocí některého z následujících řešení:

* Pokud vytváříte kanál komplexních objektů z jedné rutiny na jinou, zabalte tyto rutiny do `InlineScript` aktivity.
* Předejte název nebo hodnotu, kterou potřebujete ze složitého objektu, místo předání celého objektu.
* Použijte PowerShellový Runbook místo Runbooku pracovního postupu PowerShellu.

## <a name="scenario-400-bad-request-status-when-calling-a-webhook"></a><a name="expired webhook"></a>Scénář: 400 chybný stav žádosti při volání Webhooku

### <a name="issue"></a>Problém

Při pokusu o vyvolání Webhooku pro Azure Automation sadu Runbook se zobrazí následující chyba:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Příčina

Webhook, který se pokoušíte volat, je buď zakázán, nebo vypršela jeho platnost. 

### <a name="resolution"></a>Řešení

Pokud je Webhook zakázaný, můžete ho znovu povolit prostřednictvím Azure Portal. Pokud vypršela platnost Webhooku, musíte ho odstranit a znovu vytvořit. Webhook se dá [obnovit jenom v](../automation-webhooks.md#renew-a-webhook) případě, že ještě nevypršela jeho platnost. 

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Scénář: 429: frekvence požadavků je aktuálně příliš velká.

### <a name="issue"></a>Problém

Při spuštění rutiny se zobrazí následující chybová zpráva `Get-AzAutomationJobOutput` :

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Příčina

K této chybě může dojít při načítání výstupu úlohy z Runbooku, který má mnoho [podrobných streamů](../automation-runbook-output-and-messages.md#write-output-to-verbose-stream).

### <a name="resolution"></a>Řešení

Chcete-li vyřešit tuto chybu, proveďte jednu z následujících akcí:

* Upravte sadu Runbook a snižte počet datových proudů úloh, které vygeneruje.
* Snižte počet datových proudů, které se mají načíst při spuštění rutiny. K tomu můžete nastavit hodnotu `Stream` parametru pro rutinu [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) , aby se načetly jenom výstupní datové proudy. 

## <a name="scenario-runbook-job-fails-because-allocated-quota-was-exceeded"></a><a name="quota-exceeded"></a>Scénář: úloha Runbooku se nezdařila, protože byla překročena přidělená kvóta

### <a name="issue"></a>Problém

Vaše úloha Runbooku se nezdařila s chybou:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Příčina

K této chybě dojde, pokud provádění úlohy překročí kvótu Free 500-minut pro váš účet. Tato kvóta se vztahuje na všechny typy úloh spuštění úlohy. Některé z těchto úloh testuje úlohu, spouštíte úlohu z portálu, spouštíte úlohu pomocí webhooků nebo naplánujete úlohu, která se má spustit, a to pomocí Azure Portal nebo vašeho datacentra. Další informace o cenách pro automatizaci najdete v tématu [ceny služby Automation](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Řešení

Pokud chcete použít více než 500 minut zpracování za měsíc, změňte předplatné z úrovně Free na úroveň Basic:

1. Přihlaste se ke svému předplatnému Azure.
1. Vyberte účet Automation, který chcete upgradovat.
1. Vyberte **Nastavení** a pak vyberte **ceny**.
1. Vyberte **Povolit** na stránce dole a upgradujte svůj účet na úroveň Basic.

## <a name="scenario-runbook-output-stream-greater-than-1-mb"></a><a name="output-stream-greater-1mb"></a>Scénář: výstupní datový proud Runbooku větší než 1 MB

### <a name="issue"></a>Problém

Sada Runbook spuštěná v izolovaném prostoru Azure se nezdařila s následující chybou:

```error
The runbook job failed due to a job stream being larger than 1MB, this is the limit supported by an Azure Automation sandbox.
```

### <a name="cause"></a>Příčina

K této chybě dochází, protože se Runbook pokusil zapsat příliš mnoho dat výjimky do výstupního datového proudu.

### <a name="resolution"></a>Řešení

Výstupní datový proud úlohy má omezení 1 MB. Zajistěte, aby sada Runbook zahrnovala volání spustitelného souboru nebo podprocesu pomocí `try` `catch` bloků a. Pokud operace vyvolávají výjimku, kód zapíše zprávu z výjimky do proměnné Automation. Tato technika zabrání v zápisu zprávy do výstupního datového proudu úlohy. U spuštěných úloh Hybrid Runbook Worker se výstupní datový proud zkrátil na 1 MB, ale nezobrazí se žádná chybová zpráva.

## <a name="scenario-runbook-job-start-attempted-three-times-but-fails-to-start-each-time"></a><a name="job-attempted-3-times"></a>Scénář: došlo k pokusu o spuštění úlohy Runbooku třikrát, ale nespustí se pokaždé, když se nezdaří.

### <a name="issue"></a>Problém

Sada Runbook se nezdařila s následující chybou:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Příčina

K této chybě dochází z důvodu některého z následujících problémů:

* **Limit paměti.** Úloha může selhat, pokud používá více než 400 MB paměti. Dokumentované limity paměti přidělené izolovanému prostoru (sandbox) najdete v části [omezení služby Automation](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits). 

* **Síťové sokety.** Izolované prostory Azure jsou omezené na 1 000 současných síťových soketů. Další informace najdete v tématu [omezení služby Automation](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

* **Modul je nekompatibilní.** Závislosti modulu nemusí být správné. V takovém případě sada Runbook obvykle vrátí `Command not found` zprávu nebo `Cannot bind parameter` .

* **Žádné ověřování pomocí služby Active Directory pro izolovaný prostor (sandbox)** Váš Runbook se pokusil zavolat spustitelný nebo podproces, který běží v izolovaném prostoru Azure. Konfigurace sad Runbook pro ověřování pomocí Azure AD pomocí knihovny Azure Active Directory Authentication Library (ADAL) není podporována.

### <a name="resolution"></a>Řešení

* **Limit paměti, síťové sokety.** Navrhované způsoby práce s omezeními paměti jsou rozdělení zatížení mezi více sad Runbook, zpracování méně dat v paměti, vyhněte se zbytečným výstupům z vašich runbooků a vezměte v úvahu, kolik kontrolních bodů se zapisuje do runbooků PowerShellového pracovního postupu. Použijte metodu Clear, například `$myVar.clear` , pro vymazání proměnných a použití `[GC]::Collect` pro okamžité spuštění uvolňování paměti. Tyto akce snižují nároky na paměť Runbooku během běhu.

* **Modul je nekompatibilní.** Aktualizujte moduly Azure pomocí následujících kroků v tématu [Postup aktualizace Azure PowerShellch modulů v Azure Automation](../automation-update-azure-modules.md).

* **Žádné ověřování pomocí služby Active Directory pro izolovaný prostor (sandbox)** Při ověřování ve službě Azure AD pomocí Runbooku se ujistěte, že je modul Azure AD dostupný v účtu Automation. Ujistěte se, že jste účtu Spustit jako udělili potřebná oprávnění k provádění úloh, které sada Runbook automatizuje.

  Pokud vaše sada Runbook nemůže volat spustitelný soubor nebo podproces spuštěný v izolovaném prostoru Azure, použijte sadu Runbook na [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Hybridní procesy nejsou omezeny omezeními paměti a sítě, které mají Azure Sandbox.

## <a name="scenario-powershell-job-fails-with-cannot-invoke-method-error-message"></a><a name="cannot-invoke-method"></a>Scénář: úloha PowerShellu se nezdařila s chybovou zprávou "nelze vyvolat metodu"

### <a name="issue"></a>Problém

Při spuštění úlohy PowerShellu v Runbooku, který běží v Azure, se zobrazí následující chybová zpráva:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Příčina

Tato chyba může znamenat, že Runbooky, které běží v izolovaném prostoru Azure, nemůžou běžet v [plném jazykovém režimu](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="resolution"></a>Řešení

Tuto chybu můžete vyřešit dvěma způsoby:

* Místo použití funkce [Start-Job](/powershell/module/microsoft.powershell.core/start-job)použijte příkaz [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) a spusťte sadu Runbook.
* Zkuste spustit Runbook na Hybrid Runbook Worker.

Další informace o tomto chování a dalších chování sady Runbook Azure Automation naleznete v tématu [spuštění sady Runbook v Azure Automation](../automation-runbook-execution.md).

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Scénář: Nepodařilo se dokončit dlouho běžící sadu Runbook.

### <a name="issue"></a>Problém

Sada Runbook se po skončení běhu po dobu tří hodin zobrazí v zastaveném stavu. Může se zobrazit také tato chyba:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Toto chování je záměrné v izolovaném prostoru (sandbox) v Azure kvůli [spravedlivému monitorování sdílených](../automation-runbook-execution.md#fair-share) procesů v rámci Azure Automation. Pokud se proces spustí déle než tři hodiny, bude při automatickém sdílení zastaveno sady Runbook. Stav sady Runbook, která se nachází po nepřiměřeném časovém limitu sdílení, se liší podle typu Runbooku. Runbooky PowerShellu a Python jsou nastavené na stav zastaveno. Runbooky pracovních postupů PowerShellu jsou nastaveny na neúspěšné.

### <a name="cause"></a>Příčina

Sada Runbook běžela přes tříměsíční limit, který je povolený pro sdílení v izolovaném prostoru Azure.

### <a name="resolution"></a>Řešení

Jedním z doporučených řešení je spuštění sady Runbook na [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Hybridní pracovní procesy nejsou omezené o tříměsíční omezení pro sadu Runbook pro reálný podíl, které mají Azure sandboxy. Runbooky spuštěné na hybridních pracovních procesech Runbooku by se měly vyvíjet pro podporu chování restartování, pokud dojde k neočekávaným potížím s místní infrastrukturou.

Dalším řešením je optimalizovat sadu Runbook vytvořením [podřízených runbooků](../automation-child-runbooks.md). Pokud vaše sada Runbook projde stejnou funkcí u několika prostředků, například v databázové operaci na několika databázích, můžete funkci přesunout do podřízeného Runbooku. Každá podřízená sada Runbook se spouští paralelně v samostatném procesu. Toto chování zkrátí celkovou dobu, po kterou se nadřazený Runbook dokončí.

Rutiny PowerShellu, které umožňují podřízený scénář sady Runbook:

* [Start – AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook). Tato rutina vám umožní spustit runbook a předat do něj parametry.
* [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob). Pokud existují operace, které je třeba provést po dokončení podřízeného Runbooku, tato rutina vám umožní zjistit stav úlohy pro každou podřízenou položku.

## <a name="scenario-error-in-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Scénář: Chyba v datových proudech úlohy týkající se metody get_SerializationSettings

### <a name="issue"></a>Problém

V datových proudech úlohy pro Runbook se zobrazí následující chyba:

```error
Connect-AzAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```

### <a name="cause"></a>Příčina

Tato chyba je pravděpodobně způsobena použitím neúplné migrace z AzureRM k AZ moduls v Runbooku. Tato situace může způsobit, Azure Automation spustit úlohu Runbooku pomocí pouze modulů AzureRM a pak spustit jinou úlohu pomocí pouze AZ modules, který vede k chybě izolovaného prostoru (sandbox).

### <a name="resolution"></a>Řešení

Nedoporučujeme používat rutiny AZ a AzureRM ve stejné sadě Runbook. Další informace o správném použití modulů najdete v tématu [migrace na az modules](../shared-resources/modules.md#migrate-to-az-modules).

## <a name="scenario-access-denied-when-using-azure-sandbox-for-runbook-or-application"></a><a name="access-denied-azure-sandbox"></a>Scénář: přístup byl odepřen při použití izolovaného prostoru (sandbox) Azure pro Runbook nebo aplikaci.

### <a name="issue"></a>Problém

Když se váš Runbook nebo aplikace pokusí spustit v izolovaném prostoru Azure, prostředí odmítne přístup.

### <a name="cause"></a>Příčina

K tomuto problému může dojít, protože sandboxy Azure brání přístupu ke všem nezpracovaným serverům COM. Například aplikace nebo sada Runbook v izolovaném prostoru nemůže volat do rozhraní WMI (Windows Management Instrumentation) (WMI) nebo do služby Instalační služba systému Windows (msiserver.exe).

### <a name="resolution"></a>Řešení

Podrobnosti o použití izolovaných prostorů Azure najdete v tématu [prostředí pro spuštění sady Runbook](../automation-runbook-execution.md#runbook-execution-environment).

## <a name="scenario-invalid-forbidden-status-code-when-using-key-vault-inside-a-runbook"></a>Scénář: neplatný stavový kód zakázané při použití Key Vault v Runbooku

### <a name="issue"></a>Problém

Při pokusu o přístup k Azure Key Vault prostřednictvím sady Azure Automation Runbook se zobrazí následující chyba:

```error
Operation returned an invalid status code 'Forbidden'
```

### <a name="cause"></a>Příčina

Možné příčiny tohoto problému:

* Nepoužívejte účet Spustit jako.
* Nedostatečná oprávnění

### <a name="resolution"></a>Řešení

#### <a name="not-using-a-run-as-account"></a>Nepoužívat účet Spustit jako

Postupujte podle [kroku 5 – přidejte ověřování pro správu prostředků Azure](../learn/automation-tutorial-runbook-textual-powershell.md#step-5---add-authentication-to-manage-azure-resources) , abyste měli jistotu, že používáte účet Spustit jako pro přístup k Key Vault.

#### <a name="insufficient-permissions"></a>Nedostatečná oprávnění

[Přidejte oprávnění pro Key Vault](../manage-runas-account.md#add-permissions-to-key-vault) , abyste měli jistotu, že účet Spustit jako má dostatečná oprávnění pro přístup k Key Vault.

## <a name="recommended-documents"></a>Doporučené dokumenty

* [Spuštění sady Runbook v Azure Automation](../automation-runbook-execution.md)
* [Spuštění Runbooku v Azure Automation](../start-runbooks.md)

## <a name="next-steps"></a>Další kroky

Pokud tady nevidíte svůj problém nebo nemůžete tento problém vyřešit, zkuste další podporu vyzkoušet u některého z následujících kanálů:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) oficiálního Microsoft Azure účtu pro zlepšení prostředí pro zákazníky. Podpora Azure vás připojí k komunitě Azure, kde najdete odpovědi, podporu a odborníky.
* Pokud potřebujete další pomoc, můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/)a vyberte **získat podporu**.
