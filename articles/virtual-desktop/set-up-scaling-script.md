---
title: Relace škálování hostí Azure Automation – Azure
description: Jak automaticky škálovat hostitele relací Virtuální plochy Windows pomocí Azure Automation.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3a853dc32f8716f3f2ba32896a7a4a239efcc5bd
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349870"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Škálování hostitelů relací pomocí Azure Automation

Celkovýpočet nákladů na nasazení virtuální plochy windows můžete snížit škálováním virtuálních počítačů (VMs). To znamená vypnutí a zrušení umístění hostitelských virtuálních počítačů relace během doby mimo špičku, pak je znovu zapnout a přerozdělovat je ve špičce.

V tomto článku se dozvíte o škálovacínástroj vytvořený pomocí Azure Automation a Azure Logic Apps, které automaticky škálují virtuální počítače hostitele relací ve vašem prostředí Virtuální desktop windows. Chcete-li se dozvědět, jak používat nástroj pro změnu měřítka, přejděte na [požadavky](#prerequisites).

## <a name="how-the-scaling-tool-works"></a>Jak nástroj pro změnu měřítka funguje

Nástroj pro škálování poskytuje možnost automatizace s nízkými náklady pro zákazníky, kteří chtějí optimalizovat náklady na virtuální počítač hostitele relace.

Pomocí nástroje pro změnu měřítka můžete:
 
- Naplánujte virtuální mích spuštění a zastavení na základě pracovní doby ve špičce a mimo špičku.
- Horizontální navýšení kapacity virtuálních počítačů na základě počtu relací na jádro procesoru.
- Škálování ve virtuálních hodinách během mimo špičku hodin, takže minimální počet hostitelských virtuálních jevů relace spuštěna.

Nástroj pro škálování používá kombinaci runbooků Azure Automation PowerShell, webhooků a aplikací Azure Logic Apps. Když se nástroj spustí, Azure Logic Apps volá webhook u spuštění runbooku Azure Automation. Runbook pak vytvoří úlohu.

Během doby využití ve špičce úloha zkontroluje aktuální počet relací a kapacitu virtuálního počítače aktuálního spuštěného hostitele relací pro každý hostitelský fond. Tyto informace používá k výpočtu, pokud spuštěné hostované virtuální počítače relace mohou podporovat existující relace na základě parametru *SessionThresholdPerCPU* definovaného pro soubor **createazurelogicapp.ps1.** Pokud virtuální virtuální soudy hostitele relace nemůže podporovat existující relace, úloha spustí další virtuální chod hostitele relace ve fondu hostitelů.

>[!NOTE]
>*SessionThresholdPerCPU* neomezuje počet relací na virtuálním počítači. Tento parametr určuje jenom při spuštění nových virtuálních připojení k vyrovnávání zatížení připojení. Chcete-li omezit počet relací, musíte podle pokynů [Set-RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) odpovídajícím způsobem nakonfigurovat parametr *MaxSessionLimit.*

Během doby využití mimo špičku úloha určuje, které hostované virtuální počítače relace by měly být vypnuty na základě parametru *MinimumNumberOfRDSH.* Úloha nastaví hostitelské virtuální uživatele relace do režimu vypouštění, aby se zabránilo připojení nových relací k hostitelům. Pokud nastavíte parametr *LimitSecondsToForceLogOffUser* na nenulovou kladnou hodnotu, úloha upozorní všechny aktuálně přihlášené uživatele, aby uložili svou práci, počkejte na konfigurované množství času a potom vynutí, aby se uživatelé odhlásili. Jakmile jsou odhlášeny všechny uživatelské relace na hostitelském virtuálním počítači relace, úloha virtuální počítač vypne.

Pokud nastavíte parametr *LimitSecondsToForceLogOffUser* na nulu, úloha umožní nastavení konfigurace relace v zadaných zásadách skupiny pro zpracování odhlašování uživatelských relací. Chcete-li**Policies** > zobrazit tyto zásady skupiny, **přejděte** > na informace o časových**limitech relací**terminálového serveru Terminálového serveru > **terminálové služby služby Windows Components** > **Terminal** > **Services** > .**Administrative Templates** Pokud existují nějaké aktivní relace na hostitelský virtuální počítač relace, úloha opustí hostrelace virtuálního počítači spuštěna. Pokud neexistují žádné aktivní relace, úloha vypne virtuální ho virtuálního soudu hostitele relace.

Úloha se spouští pravidelně na základě nastaveného intervalu opakování. Tento interval můžete změnit na základě velikosti prostředí Virtuální plocha systému Windows, ale nezapomeňte, že spuštění a vypnutí virtuálních počítačů může nějakou dobu trvat, takže nezapomeňte zpoždění zohlednit. Doporučujeme nastavit interval opakování na každých 15 minut.

Nástroj má však také následující omezení:

- Toto řešení platí jenom pro hostované virtuální chod s dravou relace.
- Toto řešení spravuje virtuální počítače v libovolné oblasti, ale lze použít jenom ve stejném předplatném jako váš účet Azure Automation a Azure Logic Apps.

>[!NOTE]
>Nástroj pro škálování řídí režim vyrovnávání zatížení hostitelského fondu, který je škálování. Nastaví jej na šířku první vyrovnávání zatížení pro špičku i mimo špičku.

## <a name="prerequisites"></a>Požadavky

Než začnete nastavovat nástroj pro změnu měřítka, ujistěte se, že máte připravené následující věci:

- [Klient a fond hostitelů virtuální plochy Windows](create-host-pools-arm-template.md)
- Virtuální počítače fondu relací nakonfigurované a registrované ve službě Windows Virtual Desktop
- Uživatel s [přístupem přispěvatele k](../role-based-access-control/role-assignments-portal.md) předplatnému Azure

Stroj, který používáte k nasazení nástroje, musí mít: 

- Prostředí Windows PowerShell 5.1 nebo novější
- Modul Microsoft Az PowerShell

Pokud máte všechno připraveno, tak začneme.

## <a name="create-an-azure-automation-account"></a>Vytvoření účtu Azure Automation

Nejprve budete potřebovat účet Azure Automation ke spuštění runbooku Prostředí PowerShell. Účet si můžete nastavit takto:

1. Spusťte Windows PowerShell jako správce.
2. Spusťte následující rutinu a přihlaste se ke svému účtu Azure.

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >Váš účet musí mít práva přispěvatele na předplatné Azure, které chcete nasadit nástroj škálování na.

3. Spusťte následující rutinu a stáhněte si skript pro vytvoření účtu Azure Automation:

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazureautomationaccount.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazureautomationaccount.ps1"
     ```

4. Spusťte následující rutinu, abyste provedli skript a vytvořili účet Azure Automation:

     ```powershell
     .\createazureautomationaccount.ps1 -SubscriptionID <azuresubscriptionid> -ResourceGroupName <resourcegroupname> -AutomationAccountName <name of automation account> -Location "Azure region for deployment"
     ```

5. Výstup rutiny bude obsahovat identifikátor URI webhooku. Ujistěte se, že uchováte záznam identifikátoru URI, protože ho použijete jako parametr při nastavu plánu spuštění pro aplikace Azure Logic.

6. Po nastavení účtu Azure Automation se přihlaste k předplatnému Azure a zkontrolujte, jestli se váš účet Azure Automation a příslušná sada runbook zobrazily ve vaší zadané skupině prostředků, jak je znázorněno na následujícím obrázku:

   ![Obrázek stránky přehled Azure zobrazující nově vytvořený účet automatizace a runbook.](media/automation-account.png)

  Chcete-li zkontrolovat, zda je váš webhook tam, kde má být, vyberte název sady Runbook. Dále přejděte do části Zdroje sady Runbook a vyberte **Webhooks**.

## <a name="create-an-azure-automation-run-as-account"></a>Vytvoření účtu Azure Automation Run Run As

Teď, když máte účet Azure Automation, budete taky muset vytvořit účet Azure Automation Run As pro přístup k prostředkům Azure.

Účet [Azure Automation Run As](../automation/manage-runas-account.md) poskytuje ověřování pro správu prostředků v Azure pomocí rutin Azure. Když vytvoříte účet Spustit jako, vytvoří nového hlavního uživatele služby ve službě Azure Active Directory a přiřadí roli přispěvatele uživateli hlavního účtu služby na úrovni předplatného, azure spustit jako účet je skvělý způsob, jak bezpečně ověřit pomocí certifikáty a hlavní název služby bez nutnosti ukládat uživatelské jméno a heslo do objektu pověření. Další informace o oprávněních spustit jako, naleznete [v tématu Omezení oprávnění účtu Spustit jako](../automation/manage-runas-account.md#limiting-run-as-account-permissions).

Každý uživatel, který je členem role Správci předplatného a spolusprávce masy předplatného, může vytvořit účet Spustit jako podle pokynů v další části.

Vytvoření účtu Spustit jako ve vašem účtu Azure:

1. Na webu Azure Portal vyberte **Všechny služby**. V seznamu zdrojů zadejte a vyberte **účty automatizace**.

2. Na stránce **Účty automatizace** vyberte název účtu automation.

3. V podokně na levé straně okna vyberte **spustit jako účty** v části Nastavení účtu.

4. Vyberte **Azure Spustit jako účet**. Když se zobrazí podokno **Přidat Azure Spustit jako účet,** zkontrolujte informace o přehledu a pak vyberte **Vytvořit** a spusťte proces vytváření účtu.

5. Počkejte několik minut, než Azure vytvoří účet Spustit jako. Průběh vytváření můžete sledovat v nabídce oznámení.

6. Po dokončení procesu vytvoří prostředek s názvem AzureRunAsConnection v zadaném účtu automatizace. Prostředek připojení obsahuje ID aplikace, ID klienta, ID předplatného a kryptografický otisk certifikátu. Zapamatujte si ID aplikace, protože ho budete používat později.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Vytvoření přiřazení role ve virtuální ploše windows

Dále je třeba vytvořit přiřazení role tak, aby AzureRunAsConnection můžete komunikovat s Windows Virtual Desktop. Nezapomeňte se pomocí PowerShellu přihlásit pomocí účtu, který má oprávnění k vytváření přiřazení rolí.

Nejprve si stáhněte a importujte [modul Windows Virtual Desktop PowerShell,](/powershell/windows-virtual-desktop/overview/) který se použije v relaci PowerShellu, pokud jste tak ještě neučinili. Spusťte následující rutiny prostředí PowerShell pro připojení k virtuální ploše Windows a zobrazení vašich klientů.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

Když najdete klienta s fondy hostitelů, které chcete škálovat, postupujte podle pokynů v [části Vytvoření účtu Azure Automation](#create-an-azure-automation-account) a pomocí názvu klienta, který jste získali z předchozí rutiny v následující rutině, vytvořte přiřazení role:

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Vytvoření aplikace Azure Logic a plánu provádění

Nakonec budete muset vytvořit aplikaci Azure Logic App a nastavit plán provádění pro nový nástroj škálování.

1.  Spuštění prostředí Windows PowerShell jako správce

2.  Spusťte následující rutinu a přihlaste se ke svému účtu Azure.

     ```powershell
     Login-AzAccount
     ```

3. Spusťte následující rutinu a stáhněte soubor skriptu createazurelogicapp.ps1 do místního počítače.

     ```powershell
     Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1" -OutFile "your local machine path\ createazurelogicapp.ps1"
     ```

4. Spusťte následující rutinu pro přihlášení k virtuální ploše systému Windows pomocí účtu, který má oprávnění vlastníka rds nebo přispěvatele RDS.

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. Spusťte následující skript PowerShellu a vytvořte aplikaci Azure Logic a plán spuštění.

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

     Po spuštění skriptu aplikace logiky by se měl zobrazit ve skupině prostředků, jak je znázorněno na následujícím obrázku.

     ![Obrázek stránky s přehledem pro ukázkovou aplikaci Azure Logic.](media/logic-app.png)

Chcete-li provést změny plánu provádění, jako je například změna intervalu opakování nebo časového pásma, přejděte do plánovače automatického škálování a vyberte **upravit,** chcete-li přejít do Návrháře aplikací logiky.

![Obrázek Návrháře logických aplikací. Nabídky Opakování a Webhook, které umožňují uživateli upravovat časy opakování a soubor webhooku jsou otevřené.](media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Správa nástroje pro změnu velikosti

Teď, když jste vytvořili nástroj pro změnu měřítka, můžete získat přístup k jeho výstupu. Tato část popisuje několik funkcí, které by mohly být užitečné.

### <a name="view-job-status"></a>Zobrazení stavu úlohy

Můžete zobrazit souhrnný stav všech úloh sady Runbook nebo zobrazit podrobnější stav konkrétní úlohy sady Runbook na portálu Azure.

Na pravé straně vybraného účtu automatizace můžete v části "Statistika úlohy" zobrazit seznam souhrnů všech úloh runbooku. Otevření stránky **Úlohy** na levé straně okna zobrazuje aktuální stavy úloh, časy zahájení a doby dokončení.

![Snímek obrazovky se stránkou stavu úlohy.](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Zobrazení protokolů a výstupu nástroje pro změnu velikosti

Protokoly operací horizontálnínavýšení kapacity a škálování můžete zobrazit otevřením runbooku a výběrem názvu úlohy.

Přejděte do sady Runbook (výchozí název je WVDAutoScaleRunbook) ve skupině prostředků, která hostuje účet Azure Automation, a vyberte **Přehled**. Na stránce s přehledem vyberte úlohu v části Poslední úlohy, chcete-li zobrazit výstup nástroje pro změnu měřítka, jak je znázorněno na následujícím obrázku.

![Obrázek výstupního okna pro nástroj pro změnu měřítka.](media/tool-output.png)

## <a name="report-issues"></a>Nahlášení potíží

Pokud narazíte na nějaké problémy s nástrojem pro změnu měřítka, můžete je nahlásit na [stránce RDS GitHub](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4a-WVD-scaling-logicapps).
