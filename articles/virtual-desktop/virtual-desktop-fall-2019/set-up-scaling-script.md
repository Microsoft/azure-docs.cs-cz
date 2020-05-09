---
title: Škálování hostitelů relací Azure Automation – Azure
description: Automatické škálování hostitelů relací virtuálních počítačů s Windows pomocí Azure Automation.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 12b56519ab3fe9319f00841bd8a0cc795d99e950
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615458"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Škálování hostitelů relací pomocí Azure Automation

>[!IMPORTANT]
>Tento obsah se vztahuje na verzi 2019, která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows.

Celkové náklady na nasazení virtuálních klientů s Windows můžete snížit tak, že změníte velikost virtuálních počítačů (VM). To znamená vypnutí a zrušení přidělení virtuálních počítačů hostitele relace v době mimo špičku a jejich opětovné zapnutí a přerozdělení během špičky.

V tomto článku se dozvíte o nástroji pro škálování vytvořeném pomocí Azure Automation a Azure Logic Apps, které budou automaticky škálovat virtuální počítače hostitele relací ve vašem prostředí virtuálních počítačů s Windows. Pokud se chcete dozvědět, jak používat nástroj pro škálování, přeskočte dopředu s [požadavky](#prerequisites).

## <a name="how-the-scaling-tool-works"></a>Jak nástroj pro škálování funguje

Nástroj pro škálování nabízí možnost automatizace s nízkými náklady pro zákazníky, kteří chtějí optimalizovat náklady na virtuální počítače hostitele relace.

Nástroj pro škálování můžete použít k těmto akcím:
 
- Naplánujte, aby se virtuální počítače spouštěly a zastavily na základě špičky a špičky v pracovní době.
- Horizontální navýšení kapacity virtuálních počítačů na základě počtu relací na jádro procesoru.
- Škálování virtuálních počítačů v době mimo špičku ponechte minimální počet spuštěných virtuálních počítačů hostitele relace.

Nástroj pro škálování používá kombinaci Azure Automation PowerShellových runbooků, webhooků a Azure Logic Apps k fungování. Když se nástroj spustí, Azure Logic Apps volá Webhook, aby se spouštěl Azure Automation Runbook. Sada Runbook potom vytvoří úlohu.

Během špičky využívání úlohy zkontroluje aktuální počet relací a kapacitu virtuálního počítače aktuálně spuštěného hostitele relace pro každý fond hostitelů. Tyto informace používá k výpočtu, jestli virtuální počítače hostitele spuštěné relace můžou podporovat existující relace na základě parametru *SessionThresholdPerCPU* definovaného pro soubor **createazurelogicapp. ps1** . Pokud virtuální počítače hostitele relace nepodporují existující relace, spustí úloha další virtuální počítače hostitele relace ve fondu hostitelů.

>[!NOTE]
>*SessionThresholdPerCPU* neomezuje počet relací na virtuálním počítači. Tento parametr určuje, zda je nutné spustit nové virtuální počítače pro vyrovnávání zatížení připojení. Pokud chcete omezit počet relací, musíte podle pokynů [set-RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) nakonfigurovat parametr *MaxSessionLimit* odpovídajícím způsobem.

V době mimo špičku bude úloha určovat, které virtuální počítače hostitele relace by se měly vypnout na základě parametru *MinimumNumberOfRDSH* . Úloha nastaví virtuální počítače hostitele relace na režim vyprázdnění, aby se zabránilo novým relacím, které se připojují k hostitelům. Pokud nastavíte parametr *LimitSecondsToForceLogOffUser* na nenulovou kladnou hodnotu, úloha upozorní všechny aktuálně přihlášené uživatele, aby ušetřili svou práci, čekali nakonfigurovanou dobu a pak vynutila odhlášení uživatelů. Po odhlášení všech uživatelských relací na virtuálním počítači hostitele relace dojde k vypnutí tohoto virtuálního počítače.

Pokud nastavíte parametr *LimitSecondsToForceLogOffUser* na hodnotu nula, úloha umožní nastavení konfigurace relace v zadaných zásadách skupiny zpracovávat odhlašování uživatelských relací. Tyto zásady skupiny zobrazíte tak, že přejdete na**zásady** >  **Konfigurace** > počítače**šablony pro správu** > **části Windows součásti** > **Terminálové služby** > **časový limit relace****terminálového serveru** > . Pokud na virtuálním počítači hostitele relace existují aktivní relace, úloha ponechá virtuální počítač hostitele relace spuštěný. Pokud neexistují žádné aktivní relace, úloha vypne virtuální počítač hostitele relace.

Úloha se pravidelně spouští na základě nastaveného intervalu opakování. Tento interval můžete změnit na základě velikosti prostředí pro virtuální počítače s Windows, ale mějte na paměti, že spouštění a vypínání virtuálních počítačů může nějakou dobu trvat, takže si nezapomeňte vzít v úvahu zpoždění. Doporučujeme nastavit interval opakování na každých 15 minut.

Nástroj má však také následující omezení:

- Toto řešení se vztahuje pouze na virtuální počítače hostitele ve fondu.
- Toto řešení spravuje virtuální počítače v jakékoli oblasti, ale dá se použít jenom ve stejném předplatném jako účet Azure Automation a Azure Logic Apps.

>[!NOTE]
>Nástroj pro škálování řídí režim vyrovnávání zatížení fondu hostitelů, který je škálovatelný. Nastaví ji na geografickou vyrovnávání zatížení pro hodiny špičky i mimo špičku.

## <a name="prerequisites"></a>Požadavky

Než začnete s nastavením nástroje pro škálování, ujistěte se, že máte připravené následující akce:

- [Tenant a fond hostitelů virtuálních počítačů s Windows](create-host-pools-arm-template.md)
- Virtuální počítače fondu hostitele relace nakonfigurované a zaregistrované ve službě Virtual Desktop systému Windows
- Uživatel s [přístupem Přispěvatel](../../role-based-access-control/role-assignments-portal.md) v předplatném Azure

Počítač, který použijete k nasazení nástroje, musí mít: 

- Windows PowerShell 5,1 nebo novější
- Microsoft AZ PowerShell Module

Pokud máte všechno připravené, můžeme začít.

## <a name="create-an-azure-automation-account"></a>Vytvoření účtu Azure Automation

Nejdřív budete potřebovat účet Azure Automation ke spuštění Runbooku PowerShellu. Tady je postup nastavení účtu:

1. Spusťte Windows PowerShell jako správce.
2. Spusťte následující rutinu, abyste se přihlásili ke svému účtu Azure.

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >Váš účet musí mít oprávnění přispěvatele v předplatném Azure, na kterém chcete nástroj pro škálování nasadit.

3. Spuštěním následující rutiny Stáhněte skript pro vytvoření účtu Azure Automation:

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazureautomationaccount.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazureautomationaccount.ps1"
     ```

4. Spuštěním následující rutiny spusťte skript a vytvořte účet Azure Automation:

     ```powershell
     .\createazureautomationaccount.ps1 -SubscriptionID <azuresubscriptionid> -ResourceGroupName <resourcegroupname> -AutomationAccountName <name of automation account> -Location "Azure region for deployment"
     ```

5. Výstup rutiny bude obsahovat identifikátor URI Webhooku. Ujistěte se, že zachováte záznam identifikátoru URI, protože ho použijete jako parametr při nastavování plánu spouštění pro Azure Logic Apps.

6. Až nastavíte účet Azure Automation, přihlaste se ke svému předplatnému Azure a ujistěte se, že se Váš účet Azure Automation a relevantní sada Runbook objevily v zadané skupině prostředků, jak je znázorněno na následujícím obrázku:

![Obrázek stránky s přehledem Azure zobrazující nově vytvořený účet služby Automation a sadu Runbook.](../media/automation-account.png)

  Pokud chcete zjistit, jestli je Webhook tam, kde by měl být, vyberte název Runbooku. V dalším kroku přejdete do části prostředky Runbooku a vyberete **Webhooky**.

## <a name="create-an-azure-automation-run-as-account"></a>Vytvoření účtu Azure Automation spustit jako

Teď, když máte účet Azure Automation, budete také muset vytvořit účet Azure Automation spustit jako pro přístup k prostředkům Azure.

[Azure Automation účet Spustit jako](../../automation/manage-runas-account.md) poskytuje ověřování pro správu prostředků v Azure pomocí rutin Azure. Když vytvoříte účet Spustit jako, vytvoří se nový uživatel instančního objektu v Azure Active Directory a přiřadí roli přispěvatele k uživatelskému objektu služby na úrovni předplatného, účet Spustit jako pro Azure je skvělým způsobem, jak bezpečně ověřit s certifikáty a hlavní název služby, aniž byste museli ukládat uživatelské jméno a heslo v objektu přihlašovacích údajů. Další informace o ověřování spustit jako najdete v tématu [omezení oprávnění účtu Spustit jako](../../automation/manage-runas-account.md#limiting-run-as-account-permissions).

Každý uživatel, který je členem role správců předplatného a spolusprávcem předplatného, může vytvořit účet Spustit jako podle pokynů pro další oddíl.

Vytvoření účtu Spustit jako ve vašem účtu Azure:

1. V Azure Portal vyberte **všechny služby**. V seznamu prostředků zadejte a vyberte **účty Automation**.

2. Na stránce **účty Automation** vyberte název vašeho účtu Automation.

3. V podokně na levé straně okna vyberte **účty Spustit jako** v části nastavení účtu.

4. Vyberte **účet Spustit jako pro Azure**. Po zobrazení podokna **Přidat účet Spustit jako pro Azure** si přečtěte informace v přehledu a pak výběrem **vytvořit** spusťte proces vytváření účtů.

5. Počkejte několik minut, než Azure vytvoří účet Spustit jako. Průběh vytváření můžete sledovat v nabídce v části oznámení.

6. Po dokončení procesu se v zadaném účtu Automation vytvoří Asset s názvem AzureRunAsConnection. Asset připojení obsahuje ID aplikace, ID tenanta, ID předplatného a kryptografický otisk certifikátu. Zapamatujte si ID aplikace, protože ho použijete později.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Vytvoření přiřazení role na virtuálním počítači s Windows

Dále je potřeba vytvořit přiřazení role, aby AzureRunAsConnection mohl komunikovat s virtuálním počítačem s Windows. Nezapomeňte použít PowerShell pro přihlášení pomocí účtu, který má oprávnění k vytváření přiřazení rolí.

Nejdřív Stáhněte a importujte [modul PowerShellu virtuálního počítače s Windows](/powershell/windows-virtual-desktop/overview/) , který chcete použít v relaci PowerShellu, pokud jste to ještě neudělali. Spusťte následující rutiny PowerShellu, abyste se připojili k virtuální ploše Windows a zobrazili své klienty.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

Když najdete tenanta s fondy hostitelů, které chcete škálovat, postupujte podle pokynů v části [Vytvoření účtu Azure Automation](#create-an-azure-automation-account) a použijte název tenanta, který jste získali z předchozí rutiny v následující rutině pro vytvoření přiřazení role:

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Vytvoření aplikace logiky Azure a plánu spuštění

Nakonec budete muset vytvořit aplikaci logiky Azure a nastavit plán spouštění pro nový nástroj pro škálování.

1.  Otevřete Windows PowerShell jako správce.

2.  Spusťte následující rutinu, abyste se přihlásili ke svému účtu Azure.

     ```powershell
     Login-AzAccount
     ```

3. Spuštěním následující rutiny Stáhněte soubor skriptu createazurelogicapp. ps1 na svém místním počítači.

     ```powershell
     Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1" -OutFile "your local machine path\ createazurelogicapp.ps1"
     ```

4. Spuštěním následující rutiny se přihlaste k virtuálnímu počítači s Windows pomocí účtu, který má oprávnění vlastníka VP nebo RDS Přispěvatel.

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. Spusťte následující skript prostředí PowerShell pro vytvoření aplikace logiky Azure a plánovaného spuštění.

     ```powershell
     $resourceGroupName = Read-Host -Prompt "Enter the name of the resource group for the new Azure Logic App"
     
     $aadTenantId = Read-Host -Prompt "Enter your Azure AD tenant ID"

     $subscriptionId = Read-Host -Prompt "Enter your Azure Subscription ID"

     $tenantName = Read-Host -Prompt "Enter the name of your WVD tenant"

     $hostPoolName = Read-Host -Prompt "Enter the name of the host pool you'd like to scale"

     $recurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"

     $beginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"

     $endPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"

     $timeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"

     $sessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"

     $minimumNumberOfRdsh = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"

     $limitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, users will be signed out immediately"

     $logOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"

     $logOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

     $location = Read-Host -Prompt "Enter the name of the Azure region where you will be creating the logic app"

     $connectionAssetName = Read-Host -Prompt "Enter the name of the Azure RunAs connection asset"

     $webHookURI = Read-Host -Prompt "Enter the URI of the WebHook returned by when you created the Azure Automation Account"

     $automationAccountName = Read-Host -Prompt "Enter the name of the Azure Automation Account"

     $maintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don't want to be managed by this scaling tool"

     .\createazurelogicapp.ps1 -ResourceGroupName $resourceGroupName `
       -AADTenantID $aadTenantId `
       -SubscriptionID $subscriptionId `
       -TenantName $tenantName `
       -HostPoolName $hostPoolName `
       -RecurrenceInterval $recurrenceInterval `
       -BeginPeakTime $beginPeakTime `
       -EndPeakTime $endPeakTime `
       -TimeDifference $timeDifference `
       -SessionThresholdPerCPU $sessionThresholdPerCPU `
       -MinimumNumberOfRDSH $minimumNumberOfRdsh `
       -LimitSecondsToForceLogOffUser $limitSecondsToForceLogOffUser `
       -LogOffMessageTitle $logOffMessageTitle `
       -LogOffMessageBody $logOffMessageBody `
       -Location $location `
       -ConnectionAssetName $connectionAssetName `
       -WebHookURI $webHookURI `
       -AutomationAccountName $automationAccountName `
       -MaintenanceTagName $maintenanceTagName
     ```

     Po spuštění skriptu by se aplikace logiky měla zobrazit ve skupině prostředků, jak je znázorněno na následujícím obrázku.

     ![Obrázek stránky s přehledem pro ukázkovou aplikaci Azure Logic.](../media/logic-app.png)

Chcete-li provést změny v plánu spuštění, jako je například změna intervalu opakování nebo časového pásma, přejdete do plánovače automatického škálování a výběrem možnosti **Upravit** přejdete do návrháře Logic Apps.

![Obrázek návrháře Logic Apps. Nabídky opakování a Webhooku, které umožňují uživateli upravit časy opakování a soubor Webhooku, jsou otevřené.](../media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Správa nástroje pro škálování

Teď, když jste vytvořili Nástroj pro škálování, získáte přístup k jeho výstupu. Tato část popisuje několik funkcí, které můžete najít užitečné.

### <a name="view-job-status"></a>Zobrazení stavu úlohy

Můžete zobrazit souhrnnou stav všech úloh sady Runbook nebo zobrazit podrobnější stav konkrétní úlohy Runbooku v Azure Portal.

Napravo od vybraného účtu Automation v části Statistika úlohy můžete zobrazit seznam souhrnů všech úloh sady Runbook. Po otevření stránky **úlohy** na levé straně okna se zobrazí stav aktuální úlohy, časy spuštění a časy dokončení.

![Snímek obrazovky se stránkou stavu úlohy.](../media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Zobrazení protokolů a škálování výstupu nástroje

Protokoly o škálování na více instancí a operace škálování na více instancí můžete zobrazit otevřením Runbooku a výběrem názvu úlohy.

V rámci skupiny prostředků hostující účet Azure Automation přejděte na Runbook (výchozí název je WVDAutoScaleRunbook) a vyberte **Přehled**. Na stránce Přehled vyberte úlohu v části Nedávné úlohy, abyste zobrazili její výstup nástroje pro změnu velikosti, jak je znázorněno na následujícím obrázku.

![Obrázek okna výstup pro nástroj pro škálování](../media/tool-output.png)

## <a name="report-issues"></a>Nahlášení potíží

Pokud narazíte na nějaké problémy s nástrojem pro škálování, můžete je nahlásit na [stránce GitHub VP](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4a-WVD-scaling-logicapps).
