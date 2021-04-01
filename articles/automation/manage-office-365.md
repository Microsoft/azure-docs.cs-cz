---
title: Správa služeb Office 365 s využitím služby Azure Automation
description: V tomto článku se dozvíte, jak pomocí Azure Automation spravovat služby předplatného Office 365.
services: automation
ms.date: 11/05/2020
ms.topic: conceptual
ms.openlocfilehash: 70c8892969a3b13175c60a4e20e0cf9086112abe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93398041"
---
# <a name="manage-office-365-services"></a>Správa služeb Office 365

Pro správu předplatných služeb Office 365 můžete použít Azure Automation pro produkty, jako je Microsoft Word a Microsoft Outlook. Interakce s Office 365 jsou povolené službou [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md). Projděte si téma [použití Azure AD v Azure Automation k ověření v Azure](automation-use-azure-ad.md).

## <a name="prerequisites"></a>Požadavky

Ke správě předplatných služeb Office 365 v Azure Automation potřebujete následující:

* Předplatné Azure. Viz [Průvodce rozhodnutím předplatného](/azure/cloud-adoption-framework/decision-guides/subscriptions/).
* Automatizační objekt v Azure, který bude uchovávat přihlašovací údaje uživatelského účtu a runbooky. Další informace najdete v [úvodu k Azure Automation](./automation-intro.md).
* Azure AD . Projděte si téma [použití Azure AD v Azure Automation k ověření v Azure](automation-use-azure-ad.md).
* Tenant Office 365 s účtem. Viz [Nastavení tenanta Office 365](/sharepoint/dev/spfx/set-up-your-developer-tenant).

## <a name="install-the-msonline-and-msonlineext-modules"></a>Instalace modulů MSOnline a MSOnlineExt

Použití sady Office 365 v rámci Azure Automation vyžaduje Microsoft Azure Active Directory pro prostředí Windows PowerShell ( `MSOnline` modul). Budete také potřebovat modul [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35) , který zjednodušuje správu Azure AD v prostředích s jedním a několika klienty. Nainstalujte moduly, jak je popsáno v tématu [použití Azure AD v Azure Automation k ověření v Azure](automation-use-azure-ad.md).

>[!NOTE]
>Pokud chcete používat PowerShell MSOnline, musíte být členem služby Azure AD. Uživatelé typu Host nemohou použít modul.

## <a name="create-an-azure-automation-account"></a>Vytvoření účtu Azure Automation

K dokončení kroků v tomto článku potřebujete účet v Azure Automation. Viz [Vytvoření účtu Azure Automation](automation-quickstart-create-account.md).
 
## <a name="add-msonline-and-msonlineext-as-assets"></a>Přidání MSOnline a MSOnlineExt jako assetů

Teď přidejte nainstalované moduly MSOnline a MSOnlineExt, aby se povolily funkce Office 365. Další informace najdete [v tématu Správa modulů v Azure Automation](shared-resources/modules.md).

1. V Azure Portal vyberte **účty Automation**.
2. Vyberte svůj účet Automation.
3. V části **sdílené prostředky** vyberte **Galerie modulů** .
4. Vyhledejte MSOnline.
5. Vyberte `MSOnline` modul prostředí PowerShell a kliknutím na **importovat** Importujte modul jako prostředek.
6. Opakujte kroky 4 a 5 a vyhledejte a importujte `MSOnlineExt` modul.

## <a name="create-a-credential-asset-optional"></a>Vytvoření assetu přihlašovacích údajů (volitelné)

Je volitelné vytvořit Asset přihlašovacích údajů pro administrativního uživatele Office 365, který má oprávnění ke spuštění vašeho skriptu. Může to ale přispět k tomu, aby se zabránilo odhalení uživatelských jmen a hesel ve skriptech PowerShellu. Pokyny najdete v tématu [Vytvoření assetu přihlašovacích údajů](automation-use-azure-ad.md#create-a-credential-asset).

## <a name="create-an-office-365-service-account"></a>Vytvoření účtu služby Office 365

Chcete-li spustit služby předplatného systému Office 365, potřebujete účet služby Office 365 s oprávněními k tomu, co chcete. Můžete použít jeden účet globálního správce, jeden účet na službu nebo spustit jednu funkci nebo skript. V každém případě vyžaduje účet služby složitá a bezpečná hesla. Viz [Nastavení Office 365 pro firmy](/microsoft-365/admin/setup/setup).

## <a name="connect-to-the-azure-ad-online-service"></a>Připojení ke službě Azure AD online

>[!NOTE]
>Pokud chcete používat rutiny modulu MSOnline, musíte je spustit z Windows PowerShellu. PowerShell Core tyto rutiny nepodporuje.

K připojení ke službě Azure AD z předplatného sady Office 365 můžete použít modul MSOnline. Připojení používá uživatelské jméno a heslo sady Office 365 nebo používá službu Multi-Factor Authentication (MFA). Můžete se připojit pomocí Azure Portal nebo příkazového řádku Windows PowerShellu (nemusí se nacházet vyššími oprávněními).

Příklad PowerShellu je uveden níže. Rutina [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) vyzve k zadání přihlašovacích údajů a uloží je do `Msolcred` proměnné. Pak rutina [Connect-MsolService](/powershell/module/msonline/connect-msolservice) použije přihlašovací údaje pro připojení ke službě Azure Directory Online Service. Pokud se chcete připojit ke konkrétnímu prostředí Azure, použijte `AzureEnvironment` parametr.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

Pokud neobdržíte žádné chyby, úspěšně jste se připojili. Rychlý test je spuštění rutiny sady Office 365, například `Get-MsolUser` a zobrazení výsledků. Pokud se zobrazí chyby, počítejte s tím, že běžný problém je nesprávné heslo.

>[!NOTE]
>K připojení ke službě Azure AD z předplatného Office 365 můžete použít také modul AzureRM nebo AZ Module. Rutina Main pro připojení je [Connect-AzureAD](/powershell/module/azuread/connect-azuread). Tato rutina podporuje `AzureEnvironmentName` parametr pro konkrétní prostředí sady Office 365.

## <a name="create-a-powershell-runbook-from-an-existing-script"></a>Vytvoření Runbooku PowerShellu z existujícího skriptu

Přístup k funkcím sady Office 365 ze skriptu prostředí PowerShell. Tady je příklad skriptu pro přihlašovací údaje s názvem `Office-Credentials` s uživatelským jménem `admin@TenantOne.com` . Používá `Get-AutomationPSCredential` se k importu přihlašovacích údajů pro Office 365.

```powershell
$emailFromAddress = "admin@TenantOne.com"
$emailToAddress = "servicedesk@TenantOne.com"
$emailSMTPServer = "outlook.office365.com"
$emailSubject = "Office 365 License Report"

$credObject = Get-AutomationPSCredential -Name "Office-Credentials"
Connect-MsolService -Credential $credObject

$O365Licenses = Get-MsolAccountSku | Out-String
Send-MailMessage -Credential $credObject -From $emailFromAddress -To $emailToAddress -Subject $emailSubject -Body $O365Licenses -SmtpServer $emailSMTPServer -UseSSL
```

## <a name="run-the-script-in-a-runbook"></a>Spuštění skriptu v Runbooku

Můžete použít svůj skript v sadě Azure Automation Runbook. Pro účely tohoto příkladu použijeme typ Runbooku prostředí PowerShell.

1. Vytvořte novou PowerShellový Runbook. Další informace najdete [v tématu Vytvoření sady runbook Azure Automation](./automation-quickstart-create-runbook.md).
2. Z účtu Automation v části **Automatizace procesu** vyberte **Runbooky** .
3. Vyberte novou sadu Runbook a klikněte na **Upravit**.
4. Zkopírujte skript a vložte ho do textového editoru pro sadu Runbook.
5. Vyberte **assety**, potom rozbalte **přihlašovací údaje** a ověřte, jestli je zde přihlašovací údaje pro Office 365.
6. Klikněte na **Uložit**.
7. Vyberte **podokno test** a potom kliknutím na **Spustit** zahajte testování Runbooku. Viz [Správa runbooků v Azure Automation](./manage-runbooks.md).
8. Po dokončení testování ukončete z podokna test.

## <a name="publish-and-schedule-the-runbook"></a>Publikování a Naplánování runbooku

Pokud chcete publikovat a potom naplánovat Runbook, přečtěte si téma [Správa runbooků v Azure Automation](./manage-runbooks.md).

## <a name="next-steps"></a>Další kroky

* Podrobnosti o použití přihlašovacích údajů najdete [v tématu Správa přihlašovacích údajů v Azure Automation](shared-resources/credentials.md).
* Informace o modulech najdete v tématu [Správa modulů v Azure Automation](shared-resources/modules.md).
* Pokud potřebujete Runbook spustit, přečtěte si téma [Spuštění Runbooku v Azure Automation](start-runbooks.md).
* Podrobnosti o PowerShellu najdete v tématu [dokumentace k PowerShellu](/powershell/scripting/overview).
