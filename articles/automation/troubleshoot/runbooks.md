---
title: Řešení chyb s Azure Automation Runbooky
description: Naučte se řešit problémy s Azure Automation Runbooky.
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 759422ea8c327ae67278354217dac4c60b32f7a9
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68850323"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Řešení chyb pomocí runbooků

Tento článek podrobně popisuje běžné problémy se sadami Runbook a jejich řešení.

## <a name="steps-to-troubleshoot-runbooks"></a>Postup řešení potíží se sadami Runbook

Pokud dojde k chybám při provádění sad Runbook v Azure Automation, můžete problém diagnostikovat pomocí následujících kroků.

1. **Ujistěte se, že se váš skript Runbooku úspěšně spouští na vašem místním počítači:**  Odkazy na jazyky a výukové moduly najdete v dokumentaci k [prostředí PowerShell](/powershell/scripting/overview) nebo [dokumentaci Pythonu](https://docs.python.org/3/) .

   Místní spuštění skriptu může zjišťovat a řešit běžné chyby, jako například:

   - **Chybějící moduly**
   - **Chyby syntaxe**
   - **Logické chyby**

2. **Ujistěte se, že uzly a pracovní prostor služby Automation mají požadované moduly:** Pokud vaše sada Runbook importuje nějaké moduly, ujistěte se, že jsou k dispozici v účtu Automation pomocí kroků uvedených v části [Import modulů](../shared-resources/modules.md#import-modules). Další informace najdete v tématu [řešení potíží s moduly](shared-resources.md#modules).

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Chyby ověřování při práci s Azure Automation Runbooky

### <a name="login-azurerm"></a>Případě Spuštění přihlášení – AzureRMAccount pro přihlášení

#### <a name="issue"></a>Problém

Při provádění Runbooku se zobrazí následující chyba:

```error
Run Login-AzureRMAccount to login.
```

#### <a name="cause"></a>Příčina

Tato chyba má dvě primární příčiny:

* Různé verze modulů AzureRM
* Pokoušíte se o přístup k prostředkům v samostatném předplatném.

#### <a name="resolution"></a>Řešení

Pokud se tato chyba zobrazí po aktualizaci jednoho modulu AzureRM, měli byste všechny moduly AzureRM aktualizovat na stejnou verzi.

Pokud se snažíte získat přístup k prostředkům v jiném předplatném, můžete nakonfigurovat oprávnění podle následujících pokynů.

1. Přejít na účet Spustit jako účtu Automation a zkopírovat ID aplikace a kryptografický otisk.
  ![Kopírovat ID aplikace a kryptografický otisk](../media/troubleshoot-runbooks/collect-app-id.png)
1. Přejít na Access Control předplatného, kde není hostovaný účet Automation, a přidejte nové přiřazení role.
  ![Řízení přístupu](../media/troubleshoot-runbooks/access-control.png)
1. Přidejte ID aplikace, které jste shromáždili v předchozím kroku. Vyberte oprávnění Přispěvatel.
   ![Přidat přiřazení role](../media/troubleshoot-runbooks/add-role-assignment.png)
1. Zkopírujte název odběru pro další krok.
1. Nyní můžete použít následující kód Runbooku k otestování oprávnění z účtu Automation k druhému předplatnému.

    Nahraďte "\<CertificateThumbprint\>" hodnotou, kterou jste zkopírovali v kroku #1\>, a\<hodnotu "Subscription", kterou jste zkopírovali v kroku #4.

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

### <a name="sign-in-failed"></a>Případě Přihlášení k účtu Azure selhalo.

#### <a name="issue"></a>Problém

Při práci s `Add-AzureAccount` rutinami nebo `Connect-AzureRmAccount` se zobrazí následující chyba:

```error
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>Příčina

K této chybě dochází, pokud název assetu přihlašovacích údajů není platný. K této chybě může dojít také v případě, že uživatelské jméno a heslo, které jste použili k nastavení assetu přihlašovacích údajů automatizace, nejsou platné.

#### <a name="resolution"></a>Řešení

Chcete-li zjistit, co je chybné, proveďte následující kroky:

1. Ujistěte se, že nemáte žádné speciální znaky. Tyto znaky obsahují **\@** znak v názvu assetu přihlašovacích údajů automatizace, který používáte pro připojení k Azure.
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
   # Logging in to Azure...
   $connectionResult = Connect-AzureRmAccount `
      -ServicePrincipal `
      -TenantId $servicePrincipalConnection.TenantId `
      -ApplicationId $servicePrincipalConnection.ApplicationId `
      -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

   Start-Sleep -Seconds 30
   }
   ```

### <a name="unable-to-find-subscription"></a>Případě Nepovedlo se najít předplatné Azure.

#### <a name="issue"></a>Problém

Při práci s `Select-AzureSubscription` rutinami nebo `Select-AzureRmSubscription` se zobrazí následující chyba:

```error
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Chyba

K této chybě může dojít, pokud:

* Název předplatného není platný.

* Uživatel Azure Active Directory, který se pokouší získat podrobnosti o předplatném, není nakonfigurovaný jako správce předplatného.

#### <a name="resolution"></a>Řešení

Pomocí následujících kroků zjistíte, jestli jste se ověřili do Azure a máte přístup k předplatnému, které se pokoušíte vybrat:

1. Chcete-li se ujistit, že funguje samostatně, otestujte skript mimo Azure Automation.
2. `Add-AzureAccount` Před`Select-AzureSubscription` spuštěním rutiny nezapomeňte spustit rutinu.
3. Přidejte `Disable-AzureRmContextAutosave –Scope Process` na začátek Runbooku. Tato rutina zajišťuje, že se jakékoli přihlašovací údaje použijí pouze pro spuštění aktuální sady Runbook.
4. Pokud se tato chybová zpráva zobrazuje stále, upravte kód přidáním parametru **AzureRmContext** za `Add-AzureAccount` rutinou a následným spuštěním kódu.

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

### <a name="auth-failed-mfa"></a>Případě Ověřování do Azure se nepovedlo, protože je povolené Multi-Factor Authentication.

#### <a name="issue"></a>Problém

Při ověřování v Azure pomocí uživatelského jména a hesla Azure se zobrazí následující chyba:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>Příčina

Pokud máte na svém účtu Azure Multi-Factor Authentication, nemůžete k ověřování v Azure použít Azure Active Directoryho uživatele. Místo toho je třeba použít certifikát nebo instanční objekt k ověření v Azure.

#### <a name="resolution"></a>Řešení

Pokud chcete použít certifikát s rutinami modelu nasazení Azure Classic, přečtěte si téma [Vytvoření a přidání certifikátu pro správu služeb Azure.](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Pokud chcete použít instanční objekt s rutinami Azure Resource Manager, přečtěte si téma [Vytvoření instančního objektu pomocí Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md) a [ověřování instančního objektu pomocí Azure Resource Manager.](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="common-errors-when-working-with-runbooks"></a>Běžné chyby při práci se sadami Runbook

### <a name="child-runbook-object"></a>Podřízený Runbook vrátí chybu, pokud výstupní datový proud obsahuje objekty, nikoli jednoduché datové typy.

#### <a name="issue"></a>Problém

Při vyvolávání podřízeného Runbooku s `-Wait` přepínačem se zobrazí následující chyba a výstupní datový proud obsahuje objekt:

```error
Object reference not set to an instance of an object
```

#### <a name="cause"></a>Příčina

Došlo k známému problému, kdy rutina [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) nezpracovává výstupní datový proud správně, pokud obsahuje objekty.

#### <a name="resolution"></a>Řešení

Chcete-li tento problém vyřešit, doporučuje se místo toho implementovat logiku cyklického dotazování a načíst výstup pomocí rutiny [Get-AzureRmAutomationJobOutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) . Ukázka této logiky je definována v následujícím příkladu.

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

### <a name="get-serializationsettings"></a>Případě V streamech úlohy o metodě get_SerializationSettings se zobrazí chyba.

#### <a name="issue"></a>Problém

V datových proudech úlohy pro sadu Runbook se zobrazí tato zpráva:

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

#### <a name="cause"></a>Příčina

Tato chyba je způsobená použitím rutin AzureRM a AZ v sadě Runbook. Tato situace nastane, když `Az` importujete před `AzureRM`importem.

#### <a name="resolution"></a>Řešení

Rutiny AZ a AzureRM nejde naimportovat a použít ve stejné sadě Runbook. Další informace o AZ Support in Azure Automation najdete v tématu [AZ modul Support in Azure Automation](../az-modules.md).

### <a name="task-was-cancelled"></a>Případě Sada Runbook se nezdařila s chybou: Úloha byla zrušena.

#### <a name="issue"></a>Problém

Vaše sada Runbook se nezdařila s chybou, která je podobná následujícímu příkladu:

```error
Exception: A task was canceled.
```

#### <a name="cause"></a>Příčina

Tato chyba může být způsobena používáním zastaralých modulů Azure.

#### <a name="resolution"></a>Řešení

Tuto chybu můžete vyřešit tak, že aktualizujete moduly Azure na nejnovější verzi.

Ve svém účtu Automation klikněte na **moduly**a pak na **aktualizovat moduly Azure**. Tato aktualizace trvá přibližně 15 minut, až po dokončení opětovného spuštění Runbooku, který se nezdařil. Další informace o aktualizaci modulů najdete v tématu [aktualizace modulů Azure v Azure Automation](../automation-update-azure-modules.md).

### <a name="runbook-auth-failure"></a>Případě Selhání sad Runbook při práci s více předplatnými

#### <a name="issue"></a>Problém

Při provádění sad Runbook `Start-AzureRmAutomationRunbook`se nedokáže sada Runbook spravovat prostředky Azure.

#### <a name="cause"></a>Příčina

Sada Runbook při spuštění nepoužívá správný kontext.

#### <a name="resolution"></a>Řešení

Při práci s více předplatnými může být kontext předplatného ztracen při vyvolávání sad Runbook. Chcete-li zajistit, aby byl kontext předplatného předán runbooků, `AzureRmContext` přidejte do rutiny parametr a předejte mu kontext. Doporučuje se také použít `Disable-AzureRmContextAutosave` rutinu s oborem **procesu** , aby se zajistilo, že použitá pověření se použijí jenom pro aktuální Runbook.

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

### <a name="not-recognized-as-cmdlet"></a>Případě Sada Runbook se nezdařila z důvodu chybějící rutiny

#### <a name="issue"></a>Problém

Vaše sada Runbook se nezdařila s chybou, která je podobná následujícímu příkladu:

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

#### <a name="cause"></a>Příčina

K této chybě může dojít z některého z následujících důvodů:

* Modul obsahující rutinu se neimportuje do účtu Automation.
* Modul obsahující rutinu je importován, ale je zastaralý.

#### <a name="resolution"></a>Řešení

Tuto chybu lze vyřešit provedením jedné z následujících úloh:

Pokud je modul modulem Azure, přečtěte si téma [jak aktualizovat Azure PowerShell moduly v Azure Automation](../automation-update-azure-modules.md) a Naučte se, jak aktualizovat moduly v účtu Automation.

Pokud se jedná o samostatný modul, ujistěte se, že je modul importovaný v účtu Automation.

### <a name="job-attempted-3-times"></a>Případě Došlo k pokusu o spuštění úlohy Runbooku třikrát, ale nepovedlo se ji spustit pokaždé.

#### <a name="issue"></a>Problém

Vaše sada Runbook se nezdařila s chybou:

```error
The job was tried three times but it failed
```

#### <a name="cause"></a>Příčina

K této chybě dochází z důvodu některého z následujících problémů:

* Limit paměti. Popsaná omezení velikosti paměti pro izolovaný prostor (sandbox) se nachází v [omezeních služby Automation](../../azure-subscription-service-limits.md#automation-limits). Úloha může selhat, pokud používá více než 400 MB paměti.

* Síťové sokety. Izolované prostory Azure jsou omezené na 1000 současných síťových soketů, jak je popsáno v tématu [omezení služby Automation](../../azure-subscription-service-limits.md#automation-limits).

* Modul je nekompatibilní. K této chybě může dojít, pokud závislosti modulu nejsou správné a pokud nejsou, sada Runbook obvykle vrátí "příkaz nenalezen" nebo "nelze vytvořit vazby na parametr".

* Runbook se pokusil zavolat spustitelný nebo podproces v Runbooku, který běží v izolovaném prostoru Azure. Tento scénář není v izolovaném prostoru Azure podporován.

* Runbook se pokusil zapsat příliš mnoho dat výjimky do výstupního datového proudu.

#### <a name="resolution"></a>Řešení

Problém vyřeší některá z následujících řešení:

* Navrhovaný způsob, jak pracovat v rámci limitu paměti, je rozdělení zatížení mezi více sad Runbook, nikoli zpracování tolika dat v paměti, nemusíte psát zbytečný výstup z vašich runbooků nebo zvážit, kolik kontrolních bodů do pracovního postupu PowerShellu zapíšete. runbooků. Můžete použít metodu Clear, například `$myVar.clear()` pro vymazání proměnné a použití `[GC]::Collect()` pro okamžité spuštění uvolňování paměti. Tyto akce snižují nároky na paměť Runbooku během běhu.

* Aktualizujte moduly Azure pomocí následujících kroků, [jak aktualizovat Azure PowerShell moduly v Azure Automation](../automation-update-azure-modules.md).

* Dalším řešením je spuštění sady Runbook na [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Hybridní procesy nejsou omezeny omezeními paměti a sítě, které jsou v Azure sandboxech.

* Pokud potřebujete volat proces (například. exe nebo subprocess. Call) v sadě Runbook, je nutné spustit sadu Runbook na [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md).

* Výstupní datový proud úlohy má omezení 1 MB. Ujistěte se, že jste do bloku try/catch zachytili volání do spustitelného souboru nebo podprocesu. Pokud vyvolají výjimku, zapište zprávu z této výjimky do proměnné automatizace. Tím zabráníte v zápisu do výstupního datového proudu úlohy.

### <a name="fails-deserialized-object"></a>Případě Sada Runbook se nezdařila z důvodu deserializace objektu

#### <a name="issue"></a>Problém

Vaše sada Runbook se nezdařila s chybou:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>Příčina

Pokud je vaše sada Runbook pracovním postupem PowerShellu, ukládá složité objekty v deserializovaném formátu pro zachování stavu Runbooku, pokud je pracovní postup pozastaven.

#### <a name="resolution"></a>Řešení

Tento problém opravíte některým z následujících tří řešení:

* Pokud vytváříte kanál komplexních objektů z jedné rutiny na jinou, zabalte tyto rutiny do InlineScript.
* Předejte název nebo hodnotu, kterou potřebujete ze složitého objektu, místo předání celého objektu.
* Použijte PowerShellový Runbook místo Runbooku pracovního postupu PowerShellu.

### <a name="runbook-fails"></a>Případě Tato sada Runbook se nezdařila, ale funguje místně

#### <a name="issue"></a>Problém

Váš skript se při spuštění jako Runbook nezdařil, ale funguje místně.

#### <a name="cause"></a>Příčina

Váš skript může selhat, pokud je spuštěn jako Runbook z některého z následujících důvodů:

* Authentication issues
* Požadované moduly nejsou naimportovány nebo nejsou aktuální.
* Váš skript může vyžadovat zásah uživatele.
* Některé moduly vytvářejí předpoklady týkající se knihoven, které jsou k dispozici v počítačích se systémem Windows. Tyto knihovny nemusí být k dispozici v izolovaném prostoru (sandbox).
* Některé moduly spoléhají na verzi rozhraní .NET, která se liší od verze dostupné v izolovaném prostoru (sandboxu).

#### <a name="resolution"></a>Řešení

Tento problém může vyřešit kterýkoli z následujících řešení:

* Ověřte, že se správně ověřujete v [Azure](../manage-runas-account.md).
* Ujistěte se, že [jsou moduly Azure naimportované a aktuální](../automation-update-azure-modules.md).
* Ověřte, že žádná z rutin nezobrazuje výzvu k zadání informací. Toto chování se v sadách Runbook nepodporuje.
* Ověřte, zda cokoli, co je součástí vašeho modulu, má závislost na něčem, co není součástí modulu.
* Azure sandboxy používají .NET Framework 4.7.2, pokud modul používá vyšší verzi, nebude fungovat. V takovém případě byste měli použít [Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md)

Pokud žádné z těchto řešení nevyřešilo vaše problemReview, požádejte o konkrétní podrobnosti v [protokolu úloh](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) na to, proč se sada Runbook mohla nezdařila.

### <a name="quota-exceeded"></a>Případě Úloha Runbooku selhala, protože se překročila přidělená kvóta.

#### <a name="issue"></a>Problém

Vaše úloha Runbooku se nezdařila s chybou:

```error
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>Příčina

K této chybě dojde, pokud provádění úlohy překročí kvótu Free 500-minut pro váš účet. Tato kvóta se vztahuje na všechny typy úloh spuštění úlohy. Některé z těchto úloh mohou testovat úlohu, spouštět úlohu z portálu, spouštět úlohy pomocí webhooků nebo naplánování úlohy ke spuštění pomocí Azure Portal nebo ve vašem datovém centru. Další informace o cenách pro automatizaci najdete v tématu [ceny služby Automation](https://azure.microsoft.com/pricing/details/automation/).

#### <a name="resolution"></a>Řešení

Pokud chcete používat více než 500 minut zpracování za měsíc, musíte změnit předplatné z úrovně Free na úroveň Basic. Můžete upgradovat na úroveň Basic provedením následujících kroků:

1. Přihlásit k předplatnému Azure
2. Vyberte účet Automation, který chcete upgradovat.
3. Klikněte na **Nastavení** > **ceny**.
4. Kliknutím na **Povolit** v dolní části stránky upgradujte svůj účet na úroveň **Basic** .

### <a name="cmdlet-not-recognized"></a>Případě Při provádění Runbooku se nerozpoznala rutina.

#### <a name="issue"></a>Problém

Vaše úloha Runbooku se nezdařila s chybou:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>Příčina

Tato chyba je způsobená tím, že modul PowerShellu nemůže najít rutinu, kterou používáte v Runbooku. Příčinou této chyby je, že modul, který obsahuje rutinu, v účtu chybí, je v konfliktu s názvem sady Runbook nebo rutina existuje i v jiném modulu a automatizace nemůže tento název přeložit.

#### <a name="resolution"></a>Řešení

Problém vyřeší některá z následujících řešení:

* Ověřte, zda jste zadali správný název rutiny.
* Ujistěte se, že rutina existuje ve vašem účtu Automation a že neexistují žádné konflikty. Chcete-li ověřit, zda je rutina přítomna, otevřete sadu Runbook v režimu úprav a vyhledejte rutinu, kterou chcete najít v knihovně `Get-Command <CommandName>`nebo spustit. Jakmile ověříte, že je rutina pro účet k dispozici a že nedochází ke konfliktu názvů s jinými rutinami nebo sadami Runbook, přidejte ji na plátno a ujistěte se, že ve své sadě Runbook používáte platnou sadu parametrů.
* Pokud dojde ke konfliktu názvů a tato rutina je k dispozici ve dvou různých modulech, můžete tento problém vyřešit pomocí plně kvalifikovaného názvu rutiny. Můžete například použít **ModuleName\CmdletName**.
* Pokud spouštíte místní Runbook v rámci skupiny Hybrid Worker, ujistěte se, že je modul a rutina nainstalovaná na počítači, který je hostitelem hybridního pracovního procesu.

### <a name="long-running-runbook"></a>Případě Dlouho běžící sada Runbook se nedokáže dokončit

#### <a name="issue"></a>Problém

Sada Runbook se po uplynutí 3 hodin zobrazí v zastaveném stavu. Může se zobrazit také chyba:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

Toto chování je záměrné vzhledem k tomu, že se jedná o monitorování procesů v Azure v izolovaném prostoru (sandbox) v rámci Azure Automation. Pokud se spustí déle než tři hodiny, vytvoří se při automatickém sdílení automaticky stav Runbooku. Stav sady Runbook, která se nachází po dobu nespravedlivého sdílení, se liší podle typu Runbooku. Runbooky PowerShellu a Python jsou nastavené na stav zastaveno. Runbooky pracovních postupů PowerShellu jsounastaveny na neúspěšné.

#### <a name="cause"></a>Příčina

Sada Runbook běžela přes 3 hodiny s povoleným poctivým sdílením v izolovaném prostoru Azure.

#### <a name="resolution"></a>Řešení

Jedním z doporučených řešení je spuštění sady Runbook na [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md).

Hybridní pracovní procesy nejsou omezeny [spravedlivým](../automation-runbook-execution.md#fair-share) omezením sady Runbook za 3 hodiny, které jsou v Azure sandboxy. Runbooky spuštěné na hybridních pracovních procesech Runbooku by se měly vyvíjet pro podporu chování restartování, pokud dojde k neočekávaným problémům s místní infrastrukturou.

Další možností je optimalizovat sadu Runbook vytvořením [podřízených runbooků](../automation-child-runbooks.md). Pokud vaše sada Runbook projde stejnou funkcí na několika prostředcích, například databázovou operací na několika databázích, můžete tuto funkci přesunout do podřízeného Runbooku. Každá z těchto podřízených runbooků paralelně provádí samostatné procesy. Toto chování zkrátí celkovou dobu, po kterou se nadřazený Runbook dokončí.

Rutiny PowerShellu, které umožňují podřízený scénář sady Runbook:

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) – Tato rutina umožňuje spustit Runbook a předat parametry Runbooku.

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob) – Pokud existují operace, které je třeba provést po dokončení podřízeného Runbooku, tato rutina vám umožní zjistit stav úlohy pro každou podřízenou položku.

### <a name="expired webhook"></a>Případě Stav: 400 Chybný požadavek při volání Webhooku

#### <a name="issue"></a>Problém

Při pokusu o vyvolání Webhooku pro Azure Automation sadu Runbook se zobrazí následující chyba:

```error
400 Bad Request : This webhook has expired or is disabled
```

#### <a name="cause"></a>Příčina

Webhook, který se pokoušíte volat, je buď zakázán, nebo vypršela jeho platnost.

#### <a name="resolution"></a>Řešení

Pokud je Webhook zakázaný, můžete Webhook znovu povolit prostřednictvím Azure Portal. po vypršení platnosti Webhooku je potřeba Webhook odstranit a znovu vytvořit. Webhook se dá [obnovit jenom v](../automation-webhooks.md#renew-webhook) případě, že ještě nevypršela jeho platnost.

### <a name="429"></a>Případě 429: Frekvence požadavků je momentálně příliš vysoká. Zkuste to prosím znovu.

#### <a name="issue"></a>Problém

Při spuštění `Get-AzureRmAutomationJobOutput` rutiny se zobrazí následující chybová zpráva:

```error
429: The request rate is currently too large. Please try again
```

#### <a name="cause"></a>Příčina

K této chybě může dojít při načítání výstupu úlohy z Runbooku, který má [](../automation-runbook-output-and-messages.md#verbose-stream)mnoho podrobných streamů.

#### <a name="resolution"></a>Řešení

Tuto chybu můžete vyřešit dvěma způsoby:

* Upravte sadu Runbook a snižte počet datových proudů úloh, které vygeneruje.
* Snižte počet datových proudů, které se mají načíst při spuštění rutiny. Chcete-li postupovat podle tohoto chování, `-Stream Output` můžete zadat parametr `Get-AzureRmAutomationJobOutput` rutiny pro načtení pouze výstupních datových proudů. 

### <a name="cannot-invoke-method"></a>Případě Úloha PowerShellu se nezdařila s chybou: Nejde vyvolat metodu.

#### <a name="issue"></a>Problém

Při spuštění úlohy PowerShellu v Runbooku běžícím v Azure se zobrazí následující chybová zpráva:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

#### <a name="cause"></a>Příčina

K této chybě může dojít při spuštění úlohy PowerShellu v sadě Runbook spuštěné v Azure. K tomuto chování může dojít, protože Runbooky spuštěné v izolovaném prostoru Azure nemusí běžet v [plném jazykovém režimu](/powershell/module/microsoft.powershell.core/about/about_language_modes)).

#### <a name="resolution"></a>Řešení

Tuto chybu můžete vyřešit dvěma způsoby:

* Místo používání `Start-Job`použijte `Start-AzureRmAutomationRunbook` ke spuštění Runbooku.
* Pokud má sada Runbook tuto chybovou zprávu, spusťte ji na Hybrid Runbook Worker

Další informace o tomto chování a dalších chování sady Runbook Azure Automation naleznete v tématu [chování sady Runbook](../automation-runbook-execution.md#runbook-behavior).

## <a name="next-steps"></a>Další postup

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
