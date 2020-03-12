---
title: Řešení chyb s Azure Automation Runbooky
description: Naučte se řešit problémy, se kterými se můžete setkat s Azure Automation Runbooky.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 9786129207ead804bdd6c9439dc82168959e7db9
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129357"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Řešení chyb pomocí runbooků

Pokud dojde k chybám při provádění sad Runbook v Azure Automation, můžete pomocí následujícího postupu diagnostikovat problémy.

1. **Ujistěte se, že se váš skript Runbooku úspěšně spouští na vašem místním počítači:**  Odkazy na jazyky a výukové moduly najdete v dokumentaci k [prostředí PowerShell](/powershell/scripting/overview) nebo [dokumentaci Pythonu](https://docs.python.org/3/) .

   Místní spuštění skriptu může zjišťovat a řešit běžné chyby, jako například:

   - **Chybějící moduly**
   - **Chyby syntaxe**
   - **Logické chyby**

2. Prozkoumejte [streamy chyb](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output) **sady Runbook** pro konkrétní zprávy a porovnejte je s chybami uvedenými níže.

3. **Ujistěte se, že uzly a pracovní prostor služby Automation mají požadované moduly:** Pokud vaše sada Runbook importuje nějaké moduly, ujistěte se, že jsou k dispozici pro váš účet Automation pomocí kroků uvedených v části [Import modulů](../shared-resources/modules.md#import-modules). Podle pokynů v části [aktualizace modulů Azure v Azure Automation](..//automation-update-azure-modules.md)aktualizujte moduly na nejnovější verzi. Další informace o řešení potíží najdete v tématu [řešení potíží s moduly](shared-resources.md#modules).

Pokud je váš Runbook pozastaven nebo neočekávaně, dojde k chybě:

* [Kontrolovat stavy úlohy](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) definují stavy Runbooku a některé možné příčiny.
* [Přidejte do Runbooku další výstup](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) , abyste mohli zjistit, co se stane, než se Runbook pozastaví.
* [Zpracujte všechny výjimky](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions) , které jsou vyvolány vaší úlohou.

## <a name="login-azurerm"></a>Scénář: Spusťte přihlášení – AzureRMAccount a přihlaste se.

### <a name="issue"></a>Problém

Při provádění Runbooku se zobrazí následující chyba:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Příčina

K této chybě může dojít, pokud nepoužíváte účet Spustit jako nebo vypršela platnost účtu Spustit jako. Viz [Správa účtů spustit jako Azure Automation](https://docs.microsoft.com/azure/automation/manage-runas-account).

Tato chyba má dvě primární příčiny:

* Různé verze modulů AzureRM
* Snažíte se získat přístup k prostředkům v samostatném předplatném.

### <a name="resolution"></a>Řešení

Pokud se tato chyba zobrazí po aktualizaci jednoho modulu AzureRM, měli byste všechny moduly AzureRM aktualizovat na stejnou verzi.

Pokud se snažíte získat přístup k prostředkům v jiném předplatném, můžete nakonfigurovat oprávnění podle následujících pokynů.

1. Přejít na účet Spustit jako pro automatizaci a zkopírovat ID aplikace a kryptografický otisk.
  ![kopírovat ID aplikace a kryptografický otisk](../media/troubleshoot-runbooks/collect-app-id.png)
1. Přejít na Access Control předplatného, kde není hostovaný účet Automation, a přidejte nové přiřazení role.
  ![Řízení přístupu](../media/troubleshoot-runbooks/access-control.png)
1. Přidejte ID aplikace, které jste shromáždili v předchozím kroku. Vyberte oprávnění Přispěvatel.
   ![přidání přiřazení rolí](../media/troubleshoot-runbooks/add-role-assignment.png)
1. Zkopírujte název odběru pro další krok.
1. Nyní můžete použít následující kód Runbooku k otestování oprávnění z účtu Automation k druhému předplatnému.

    Nahraďte "\<CertificateThumbprint\>" hodnotou, kterou jste zkopírovali v kroku #1, a hodnotou "\<Subscription\>", kterou jste zkopírovali v kroku #4.

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

## <a name="unable-to-find-subscription"></a>Scénář: Nejde najít předplatné Azure.

### <a name="issue"></a>Problém

Při práci s rutinou **Select-AzureSubscription** nebo **Select-AzureRmSubscription** se zobrazí následující chyba:

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Chyba

K této chybě může dojít, pokud:

* Název předplatného není platný.
* Uživatel Azure Active Directory, který se pokouší získat podrobnosti o předplatném, není nakonfigurovaný jako správce předplatného.

### <a name="resolution"></a>Řešení

Pomocí následujících kroků zjistíte, jestli jste se ověřili do Azure a máte přístup k předplatnému, které se pokoušíte vybrat:

1. Chcete-li se ujistit, že funguje samostatně, otestujte skript mimo Azure Automation.
2. Před spuštěním rutiny **Select-AzureSubscription** nezapomeňte spustit rutinu **Add-AzureAccount** .
3. Přidejte `Disable-AzureRmContextAutosave –Scope Process` na začátek Runbooku. Toto volání rutiny zajistí, že se jakékoli přihlašovací údaje použijí pouze pro spuštění aktuální sady Runbook.
4. Pokud se tato chybová zpráva zobrazuje stále, upravte kód přidáním parametru *AzureRmContext* pro rutinu **Add-AzureAccount** a poté spusťte kód.

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

## <a name="auth-failed-mfa"></a>Scénář: ověřování do Azure selhalo, protože je povolené Multi-Factor Authentication.

### <a name="issue"></a>Problém

Při ověřování v Azure pomocí uživatelského jména a hesla Azure se zobrazí následující chyba:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Příčina

Pokud máte na svém účtu Azure Multi-Factor Authentication, nemůžete k ověřování v Azure použít Azure Active Directoryho uživatele. Místo toho je třeba použít certifikát nebo instanční objekt k ověření v Azure.

### <a name="resolution"></a>Řešení

Pokud chcete použít certifikát s rutinami modelu nasazení Azure Classic, přečtěte si téma [Vytvoření a přidání certifikátu pro správu služeb Azure](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx). Pokud chcete použít instanční objekt s rutinami Azure Resource Manager, přečtěte si téma [Vytvoření instančního objektu pomocí Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md) a [ověřování instančního objektu pomocí Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

## <a name="get-serializationsettings"></a>Scénář: v datových proudech úlohy o metodě get_SerializationSettings se zobrazí chyba.

### <a name="issue"></a>Problém

V datových proudech úlohy pro Runbook se zobrazí následující chyba:

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

Tato chyba je způsobená použitím rutin AzureRM a AZ v sadě Runbook. Tato situace nastane, když naimportujete `Az` před importem `AzureRM`.

### <a name="resolution"></a>Řešení

Rutiny AZ a AzureRM se nedají importovat a používat ve stejné sadě Runbook. Další informace o AZ rutinách v Azure Automation najdete v tématu [AZ modul Support in Azure Automation](../az-modules.md).

## <a name="task-was-cancelled"></a>Scénář: sada Runbook se nezdařila s chybou: úloha byla zrušena.

### <a name="issue"></a>Problém

Vaše sada Runbook se nezdařila s chybou, která je podobná následujícímu příkladu:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Příčina

Tato chyba může být způsobena používáním zastaralých modulů Azure.

### <a name="resolution"></a>Řešení

Tuto chybu můžete vyřešit tak, že aktualizujete moduly Azure na nejnovější verzi.

Ve svém účtu Automation klikněte na **moduly**a pak na **aktualizovat moduly Azure**. Tato aktualizace trvá přibližně 15 minut, až po dokončení opětovného spuštění Runbooku, který se nezdařil. Další informace o aktualizaci modulů najdete v tématu [aktualizace modulů Azure v Azure Automation](../automation-update-azure-modules.md).

## <a name="runbook-auth-failure"></a>Scénář: sady Runbook selžou při práci s více předplatnými

### <a name="issue"></a>Problém

Při spouštění Runbooků se sada Runbook nedokáže spravovat prostředky Azure.

### <a name="cause"></a>Příčina

Sada Runbook při spuštění nepoužívá správný kontext.

### <a name="resolution"></a>Řešení

Kontext předplatného může být ztracen při vyvolání více sad Runbook. Chcete-li zajistit, aby byl kontext předplatného předán runbooků, předejte kontext rutině v parametru *AzureRmContext* . Použijte rutinu **Disable-AzureRmContextAutosave** s oborem **procesu** , abyste zajistili, že zadané přihlašovací údaje se použijí jenom pro aktuální Runbook.

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

Další informace najdete v tématu [práce s více předplatnými](../automation-runbook-execution.md#working-with-multiple-subscriptions).

## <a name="not-recognized-as-cmdlet"></a>Scénář: termín není známý jako název rutiny, funkce, skriptu.

### <a name="issue"></a>Problém

Vaše sada Runbook se nezdařila s chybou, která je podobná následujícímu příkladu:

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Příčina

K této chybě může dojít z následujících důvodů:

* Modul obsahující rutinu se neimportuje do účtu Automation.
* Modul obsahující rutinu je importován, ale je zastaralý.

### <a name="resolution"></a>Řešení

Tuto chybu lze vyřešit provedením jedné z následujících úloh:

Pokud je modul modulem Azure, přečtěte si téma [jak aktualizovat Azure PowerShell moduly v Azure Automation](../automation-update-azure-modules.md) a Naučte se, jak aktualizovat moduly v účtu Automation.

Pokud se jedná o samostatný modul, ujistěte se, že je modul importovaný v účtu Automation.

## <a name="job-attempted-3-times"></a>Scénář: došlo k pokusu o spuštění úlohy Runbooku třikrát, ale nepovedlo se ji spustit pokaždé.

### <a name="issue"></a>Problém

Vaše sada Runbook se nezdařila s následující chybou.

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Příčina

K této chybě dochází z důvodu některého z následujících problémů:

* Limit paměti. Úloha může selhat, pokud používá více než 400 MB paměti. Dokumentované limity paměti přidělené izolovanému prostoru (sandbox) najdete v části [omezení služby Automation](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits). 

* Síťové sokety. Izolované prostory Azure jsou omezené na 1000 současných síťových soketů. Viz [omezení služby Automation](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

* Modul je nekompatibilní. Závislosti modulu nemusí být správné. V takovém případě sada Runbook obvykle vrací příkaz, který **nebyl nalezen** , nebo **nemůže vytvořit vázání zprávy s parametrem** .

* Žádné ověřování pomocí služby Active Directory pro izolovaný prostor (sandbox) Váš Runbook se pokusil zavolat spustitelný nebo podproces, který běží v izolovaném prostoru Azure. Konfigurace sad Runbook pro ověřování pomocí Azure AD pomocí knihovny Azure Active Directory Authentication Library (ADAL) není podporována.

* Příliš mnoho dat výjimky. Runbook se pokusil zapsat příliš mnoho dat výjimky do výstupního datového proudu.

### <a name="resolution"></a>Řešení

* Limit paměti, síťové sokety. Navrhované způsoby práce s omezeními paměti jsou rozdělení zatížení mezi více sad Runbook, zpracování méně dat v paměti, vyhněte se zbytečným výstupům z vašich runbooků a vezměte v úvahu, kolik kontrolních bodů se zapisuje do pracovního postupu PowerShellu. runbooků. K vymazání proměnných použijte metodu Clear, například `$myVar.clear`, a pomocí `[GC]::Collect` spusťte uvolňování paměti okamžitě. Tyto akce snižují nároky na paměť Runbooku během běhu.

* Modul je nekompatibilní. Aktualizujte moduly Azure pomocí následujících kroků v tématu [Postup aktualizace Azure PowerShellch modulů v Azure Automation](../automation-update-azure-modules.md).

* Bez ověřování pomocí knihovny ADAL pro izolovaný prostor (sandbox). Při ověřování ve službě Azure AD pomocí Runbooku se ujistěte, že je modul Azure AD dostupný v účtu Automation. Ujistěte se, že jste účtu Spustit jako udělili potřebná oprávnění k provádění úloh, které sada Runbook automatizuje.

  Pokud vaše sada Runbook nemůže volat spustitelný soubor nebo podproces spuštěný v izolovaném prostoru Azure, použijte sadu Runbook na [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Hybridní procesy nejsou omezeny omezeními paměti a sítě, které mají Azure Sandbox.

* Příliš mnoho dat výjimky. Výstupní datový proud úlohy má omezení 1 MB. Zajistěte, aby sada Runbook v bloku try/catch obsahovala volání spustitelného souboru nebo podprocesu. Pokud operace vyvolávají výjimku, kód zapíše zprávu z výjimky do proměnné Automation. Tato technika zabrání v zápisu zprávy do výstupního datového proudu úlohy.

## <a name="sign-in-failed"></a>Scénář: přihlášení k účtu Azure selhalo.

### <a name="issue"></a>Problém

Při práci s rutinou **Add-AzureAccount** nebo **Connect-AzureRmAccount** se zobrazí jedna z následujících chyb:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Příčina

K této chybě dochází, pokud název assetu přihlašovacích údajů není platný. K této chybě může dojít také v případě, že uživatelské jméno a heslo, které jste použili k nastavení assetu přihlašovacích údajů automatizace, nejsou platné.

### <a name="resolution"></a>Řešení

Chcete-li zjistit, co je chybné, proveďte následující kroky:

1. Ujistěte se, že nemáte žádné speciální znaky. Tyto znaky zahrnují **\@** znak v názvu assetu přihlašovacích údajů automatizace, který používáte pro připojení k Azure.
2. Ověřte, že je možné použít uživatelské jméno a heslo, které je uložené v přihlašovacích údajích Azure Automation v místním editoru prostředí PowerShell ISE. Správnost zadání uživatelského jména a hesla můžete provést tak, že spustíte následující rutiny v prostředí PowerShell ISE:

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Pokud se ověření nepovede místně, znamená to, že jste nevytvořili správně přihlašovací údaje Azure Active Directory. Pokud chcete účet Azure Active Directory správně nastavit, přečtěte si téma [ověřování do Azure pomocí Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) blogového příspěvku.

4. Pokud se zdá, že se jedná o přechodnou chybu, zkuste k rutině pro ověřování přidat logiku opakování, aby se ověřování zajistilo jako robustnější.

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

## <a name="child-runbook-object"></a>Scénář: odkaz na objekt není nastavený na instanci objektu.

### <a name="issue"></a>Problém

Při vyvolání podřízeného Runbooku s přepínačem `-Wait` se zobrazí následující chyba a výstupní datový proud obsahuje objekt:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Příčina

Rutina **Start-AzureRmAutomationRunbook** nezpracovává výstupní datový proud správně, pokud datový proud obsahuje objekty.

### <a name="resolution"></a>Řešení

Doporučuje se implementovat logiku cyklického dotazování a načíst výstup pomocí rutiny [Get-AzureRmAutomationJobOutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) . Ukázka této logiky je definována níže.

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

## <a name="fails-deserialized-object"></a>Scénář: sada Runbook se nezdařila z důvodu deserializace objektu

### <a name="issue"></a>Problém

Vaše sada Runbook se nezdařila s chybou:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Příčina

Pokud je vaše sada Runbook pracovním postupem PowerShellu, ukládá složité objekty v deserializovaném formátu pro zachování stavu Runbooku, pokud je pracovní postup pozastaven.

### <a name="resolution"></a>Řešení

Tento problém opravíte některým z následujících tří řešení:

* Pokud vytváříte kanál komplexních objektů z jedné rutiny na jinou, zabalte tyto rutiny do InlineScript.
* Předejte název nebo hodnotu, kterou potřebujete ze složitého objektu, místo předání celého objektu.
* Použijte PowerShellový Runbook místo Runbooku pracovního postupu PowerShellu.

## <a name="quota-exceeded"></a>Scénář: úloha sady Runbook selhala, protože byla překročena přidělená kvóta

### <a name="issue"></a>Problém

Vaše úloha Runbooku se nezdařila s chybou:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Příčina

K této chybě dojde, pokud provádění úlohy překročí kvótu Free 500-minut pro váš účet. Tato kvóta se vztahuje na všechny typy úloh spuštění úlohy. Některé z těchto úloh testuje úlohu, spouštíte úlohu z portálu, spouštíte úlohu pomocí webhooků nebo naplánujete úlohu, která se má spustit, a to pomocí Azure Portal nebo vašeho datacentra. Další informace o cenách pro automatizaci najdete v tématu [ceny služby Automation](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Řešení

Pokud chcete používat více než 500 minut zpracování za měsíc, musíte změnit předplatné z úrovně Free na úroveň Basic. Můžete upgradovat na úroveň Basic provedením následujících kroků:

1. Přihlaste se ke svému předplatnému Azure.
2. Vyberte účet Automation, který chcete upgradovat.
3. Klikněte na **Nastavení**a pak na **ceny**.
4. Kliknutím na **Povolit** v dolní části stránky upgradujte svůj účet na úroveň **Basic** .

## <a name="cmdlet-not-recognized"></a>Scénář: rutina se nerozpoznala při provádění Runbooku.

### <a name="issue"></a>Problém

Vaše úloha Runbooku se nezdařila s chybou:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Příčina

Tato chyba je způsobená tím, že modul PowerShellu nemůže najít rutinu, kterou používáte v Runbooku. Je možné, že modul, který obsahuje rutinu, v účtu chybí, je v konfliktu s názvem Runbooku nebo rutina existuje i v jiném modulu a automatizace nemůže tento název přeložit.

### <a name="resolution"></a>Řešení

Problém vyřeší některá z následujících řešení:

* Ověřte, zda jste zadali správný název rutiny.
* Ujistěte se, že rutina existuje ve vašem účtu Automation a že neexistují žádné konflikty. Chcete-li ověřit, zda je rutina přítomna, otevřete sadu Runbook v režimu úprav a vyhledejte rutinu, kterou chcete najít v knihovně nebo spusťte `Get-Command <CommandName>`. Jakmile ověříte, že je rutina pro účet k dispozici a že nedochází ke konfliktu názvů s jinými rutinami nebo sadami Runbook, přidejte rutinu na plátno a ujistěte se, že v Runbooku používáte platnou sadu parametrů.
* Pokud dojde ke konfliktu názvů a tato rutina je k dispozici ve dvou různých modulech, můžete tento problém vyřešit pomocí plně kvalifikovaného názvu rutiny. Můžete například použít **ModuleName\CmdletName**.
* Pokud spouštíte místní Runbook v rámci skupiny Hybrid Worker, ujistěte se, že modul a rutina jsou nainstalovány v počítači, který je hostitelem hybridního pracovního procesu.

## <a name="long-running-runbook"></a>Scénář: Nepodařilo se dokončit dlouho běžící sadu Runbook.

### <a name="issue"></a>Problém

Sada Runbook se po uplynutí 3 hodin zobrazí v zastaveném stavu. Může se zobrazit také tato chyba:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Toto chování je záměrné v izolovaném prostoru (sandbox) v Azure kvůli [spravedlivému monitorování sdílených](../automation-runbook-execution.md#fair-share) procesů v rámci Azure Automation. Pokud se proces spustí déle než tři hodiny, bude při automatickém sdílení zastaveno sady Runbook. Stav sady Runbook, která se nachází po nepřiměřeném časovém limitu sdílení, se liší podle typu Runbooku. Runbooky PowerShellu a Python jsou nastavené na stav zastaveno. Runbooky pracovních postupů PowerShellu jsou nastaveny na neúspěšné.

### <a name="cause"></a>Příčina

Sada Runbook běžela prostřednictvím limitu 3 hodin, který je povolený pro sdílení v izolovaném prostoru Azure.

### <a name="resolution"></a>Řešení

Jedním z doporučených řešení je spuštění sady Runbook na [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md).

Hybridní pracovní procesy nejsou omezené o 3 hodinovou sadu Runbook s reálným podílem, kterou mají Azure sandboxy. Runbooky spuštěné na hybridních pracovních procesech Runbooku by se měly vyvíjet pro podporu chování restartování, pokud dojde k neočekávaným problémům s místní infrastrukturou.

Další možností je optimalizovat sadu Runbook vytvořením [podřízených runbooků](../automation-child-runbooks.md). Pokud vaše sada Runbook projde stejnou funkcí u několika prostředků, například v databázové operaci na několika databázích, můžete funkci přesunout do podřízeného Runbooku. Každá podřízená sada Runbook se spouští paralelně v samostatném procesu. Toto chování zkrátí celkovou dobu, po kterou se nadřazený Runbook dokončí.

Rutiny PowerShellu, které umožňují podřízený scénář sady Runbook:

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) – Tato rutina umožňuje spustit Runbook a předat parametry Runbooku.

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob) – Pokud existují operace, které je třeba provést po dokončení podřízeného Runbooku, tato rutina vám umožní zjistit stav úlohy pro každou podřízenou položku.

## <a name="expired webhook"></a>Scénář: stav: 400 Chybný požadavek při volání Webhooku

### <a name="issue"></a>Problém

Při pokusu o vyvolání Webhooku pro Azure Automation sadu Runbook se zobrazí následující chyba:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Příčina

Webhook, který se pokoušíte volat, je buď zakázán, nebo vypršela jeho platnost.

### <a name="resolution"></a>Řešení

Pokud je Webhook zakázaný, můžete Webhook znovu povolit prostřednictvím Azure Portal. po vypršení platnosti Webhooku je potřeba Webhook odstranit a znovu vytvořit. Webhook se dá [obnovit jenom v](../automation-webhooks.md#renew-webhook) případě, že ještě nevypršela jeho platnost.

## <a name="429"></a>Scénář: 429: frekvence požadavků je aktuálně příliš velká...

### <a name="issue"></a>Problém

Při spuštění rutiny **Get-AzureRmAutomationJobOutput** se zobrazí následující chybová zpráva:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Příčina

K této chybě může dojít při načítání výstupu úlohy z Runbooku, který má mnoho [podrobných streamů](../automation-runbook-output-and-messages.md#verbose-stream).

### <a name="resolution"></a>Řešení

Tuto chybu můžete vyřešit dvěma způsoby:

* Upravte sadu Runbook a snižte počet datových proudů úloh, které vygeneruje.
* Snižte počet datových proudů, které se mají načíst při spuštění rutiny. Pokud chcete postupovat podle tohoto chování, můžete nastavit hodnotu parametru *Stream* pro rutinu **Get-AzureRmAutomationJobOutput** , aby se načetly jenom výstupní datové proudy. 

## <a name="cannot-invoke-method"></a>Scénář: úloha PowerShellu se nezdařila s chybou: nelze vyvolat metodu.

### <a name="issue"></a>Problém

Při spuštění úlohy PowerShellu v Runbooku běžícím v Azure se zobrazí následující chybová zpráva:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Příčina

Tato chyba může znamenat, že Runbooky spuštěné v izolovaném prostoru Azure nemůžou běžet v [plném jazykovém režimu](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="resolution"></a>Řešení

Tuto chybu můžete vyřešit dvěma způsoby.

* Místo použití funkce **Start-Job**použijte příkaz **Start-AzureRmAutomationRunbook** a spusťte sadu Runbook.
* Pokud má vaše sada Runbook tuto chybovou zprávu, zkuste ji spustit na Hybrid Runbook Worker.

Další informace o tomto chování a dalších chování sady Runbook Azure Automation naleznete v tématu [chování sady Runbook](../automation-runbook-execution.md#runbook-behavior).

## <a name="scenario-linux-hybrid-runbook-worker-receives-a-prompt-for-a-password-when-signing-a-runbook"></a>Scénář: Linux Hybrid Runbook Worker při podepisování Runbooku zobrazit výzvu k zadání hesla

### <a name="issue"></a>Problém

Spuštění příkazu **sudo** pro Linux Hybrid Runbook Worker načte neočekávanou výzvu k zadání hesla.

### <a name="cause"></a>Příčina

Účet **nxautomationuser** pro agenta Log Analytics pro Linux není správně nakonfigurovaný v souboru sudoers. Hybrid Runbook Worker potřebuje odpovídající konfiguraci oprávnění účtu a dalších dat, aby mohla podepisovat Runbooky na Linux Runbook Worker.

### <a name="resolution"></a>Řešení

* Ujistěte se, že Hybrid Runbook Worker má na počítači spustitelný soubor GnuPG (GPG).

* Ověřte konfiguraci účtu **nxautomationuser** v souboru sudoers. Viz [spouštění Runbooků na Hybrid Runbook Worker](../automation-hrw-run-runbooks.md)

## <a name="scenario-cmdlet-failing-in-pnp-powershell-runbook-on-azure-automation"></a>Scénář: selhání rutiny v Runbooku PowerShellu PnP v Azure Automation

### <a name="issue"></a>Problém

Když sada Runbook zapíše objekt generovaný PowerShellem PnP do výstupního Azure Automation přímo, výstup rutiny nemůže streamovat zpět do automatizace.

### <a name="cause"></a>Příčina

K tomuto problému nejčastěji dochází, když Azure Automation zpracovává Runbooky, které vyvolávají rutiny PowerShellu pro PnP, například **Add-pnplistitem**, bez zachycení vrácených objektů.

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

## <a name="other"></a>Můj problém není uvedený výše.

Níže uvedené části obsahují další běžné chyby a poskytují podpůrnou dokumentaci, která vám pomůžou problém vyřešit.

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>Hybrid Runbook Worker nespouští úlohy nebo nereaguje

Pokud spouštíte úlohy na Hybrid Runbook Worker, nikoli v Azure Automation, možná budete muset [vyřešit problémy samotného hybridního pracovního procesu](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker).

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>Runbook selže s chybou typu Žádná oprávnění

Účty Spustit jako nemusí mít stejná oprávnění oproti prostředkům Azure jako váš aktuální účet. Ujistěte se, že váš účet Spustit jako má [oprávnění pro přístup k jakýmkoli prostředkům](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) používaným ve vašem skriptu.

### <a name="issues-passing-parameters-into-webhooks"></a>Problémy s předáváním parametrů do webhooků

Nápovědu k předávání parametrů do webhooků najdete v tématu [Spuštění runbooku z Webhooku](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters).

### <a name="issues-using-az-modules"></a>Problémy s použitím AZ modules

Použití AZ modules a AzureRM modulů ve stejném účtu Automation se nepodporuje. Další podrobnosti najdete v tématu [AZ modules in Runbooky](https://docs.microsoft.com/azure/automation/az-modules) .

### <a name="inconsistent-behavior-in-runbooks"></a>Nekonzistentní chování runbooků

Postupujte podle pokynů v části [Spuštění Runbooku](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior) , abyste se vyhnuli problémům s souběžnými úlohami, provedli jsme vytváření prostředků víckrát nebo jiné logiky s časováním v sadách Runbook.

### <a name="runbook-fails-with-the-error-no-permission-forbidden-403-or-some-variation"></a>Sada Runbook se nezdařila s chybou bez oprávnění, zakázáno (403) nebo některé varianty

Účty Spustit jako nemusí mít stejná oprávnění oproti prostředkům Azure jako váš aktuální účet. Ujistěte se, že váš účet Spustit jako má [oprávnění pro přístup k jakýmkoli prostředkům](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) používaným ve vašem skriptu.

### <a name="runbooks-were-working-but-suddenly-stopped"></a>Runbooky fungovaly, ale najednou fungovat přestaly

* Zajistěte, aby účet Spustit jako nevypršel. Viz [obnovení certifikace](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Pokud k zahájení Runbooku používáte [Webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook) , zajistěte, aby Webhook nevypršel.

### <a name="passing-parameters-into-webhooks"></a>Předávání parametrů do webhooků

Nápovědu k předávání parametrů do webhooků najdete v tématu [Spuštění runbooku z Webhooku](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters).

### <a name="using-az-modules"></a>Používání modulů Az

Použití AZ modules a AzureRM modulů ve stejném účtu Automation se nepodporuje. Viz [AZ modules in runbookys](https://docs.microsoft.com/azure/automation/az-modules).

### <a name="using-self-signed-certificates"></a>Používání certifikátů podepsaných svým držitelem

Pokud chcete používat certifikáty podepsané svým držitelem, přečtěte si téma [Vytvoření nového certifikátu](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate).

### <a name="access-denied-when-using-azure-sandbox-for-a-runbook"></a>Přístup odepřen při použití izolovaného prostoru (sandbox) Azure pro Runbook

Izolovaný prostor Azure zabraňuje přístupu ke všem nezpracovaným serverům COM. Například aplikace nebo sada Runbook v izolovaném prostoru nemůže volat do rozhraní WMI (Windows Management Instrumentation) (WMI) nebo do služby Instalační služba systému Windows (msiserver. exe). Podrobnosti o použití izolovaného prostoru naleznete [v tématu Spuštění Runbooku v Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-execution.md).

## <a name="recommended-documents"></a>Doporučené dokumenty

* [Spuštění Runbooku v Azure Automation](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Spuštění sady Runbook v Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
