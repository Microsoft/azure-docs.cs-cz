---
title: Správa služeb Office 365 s využitím služby Azure Automation
description: Vysvětluje, jak pomocí Azure Automation spravovat služby předplatného Office 365.
services: automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9cb505ced907b143fbd6a5f4f30c818092005bb8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80550419"
---
# <a name="manage-office-365-services-using-azure-automation"></a>Správa služeb Office 365 s využitím služby Azure Automation

Pro správu předplatných služeb Office 365 můžete použít Azure Automation pro produkty, jako je Microsoft Word a Microsoft Outlook. Interakce s Office 365 jsou povolené službou [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis). Projděte si téma [použití Azure AD v Azure Automation k ověření v Azure](automation-use-azure-ad.md).

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pro váš účet Automation můžete aktualizovat moduly na nejnovější verzi pomocí [postupu aktualizace modulů Azure PowerShell v Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Požadavky

Ke správě předplatných služeb Office 365 v Azure Automation potřebujete následující:

* Předplatné Azure. Viz [Průvodce rozhodnutím předplatného](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/).
* Automatizační objekt v Azure, který bude uchovávat přihlašovací údaje uživatelského účtu a runbooky. Další informace najdete v [úvodu k Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro).
* Služba Azure AD. Projděte si téma [použití Azure AD v Azure Automation k ověření v Azure](automation-use-azure-ad.md).
* Tenant Office 365 s účtem. Viz [Nastavení tenanta Office 365](https://docs.microsoft.com/sharepoint/dev/spfx/set-up-your-developer-tenant).

## <a name="installing-the-msonline-and-msonlineext-modules"></a>Instalace modulů MSOnline a MSOnlineExt

Použití sady Office 365 v rámci Azure Automation vyžaduje Microsoft Azure Active Directory pro prostředí Windows`MSOnline` PowerShell (modul). Budete také potřebovat modul [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35), který zjednodušuje správu Azure AD v prostředích s jedním a několika klienty. Nainstalujte moduly, jak je popsáno v tématu [použití Azure AD v Azure Automation k ověření v Azure](automation-use-azure-ad.md).

>[!NOTE]
>Pokud chcete používat PowerShell MSOnline, musíte být členem služby Azure AD. Uživatelé typu Host nemohou použít modul.

## <a name="creating-an-azure-automation-account"></a>Vytváří se účet Azure Automation.

K dokončení kroků v tomto článku potřebujete účet v Azure Automation. Viz [Vytvoření účtu Azure Automation](automation-quickstart-create-account.md).
 
## <a name="adding-msonline-and-msonlineext-as-assets"></a>Přidání MSOnline a MSOnlineExt jako assetů

Teď přidejte nainstalované moduly MSOnline a MSOnlineExt, aby se povolily funkce Office 365. Další informace najdete [v tématu Správa modulů v Azure Automation](shared-resources/modules.md).

1. V Azure Portal vyberte **účty Automation**.
2. Vyberte svůj účet Automation.
3. V části **sdílené prostředky**vyberte **Galerie modulů** .
4. Vyhledejte MSOnline.
5. Vyberte modul `MSOnline` prostředí PowerShell a kliknutím na **importovat** Importujte modul jako prostředek.
6. Opakujte kroky 4 a 5 a vyhledejte a importujte `MSOnlineExt` modul. 

## <a name="creating-a-credential-asset-optional"></a>Vytvoření assetu přihlašovacích údajů (volitelné)

Je volitelné vytvořit Asset přihlašovacích údajů pro administrativního uživatele Office 365, který má oprávnění ke spuštění vašeho skriptu. Může to ale přispět k tomu, aby se zabránilo odhalení uživatelských jmen a hesel ve skriptech PowerShellu. Pokyny najdete v tématu [Vytvoření assetu přihlašovacích údajů](automation-use-azure-ad.md#creating-a-credential-asset).

## <a name="creating-an-office-365-service-account"></a>Vytvoření účtu služby Office 365

Chcete-li spustit služby předplatného systému Office 365, potřebujete účet služby Office 365 s oprávněními k tomu, co chcete. Můžete použít jeden účet globálního správce, jeden účet na službu nebo spustit jednu funkci nebo skript. V každém případě vyžaduje účet služby složitá a bezpečná hesla. Viz [Nastavení Office 365 pro firmy](https://docs.microsoft.com/microsoft-365/admin/setup/setup?view=o365-worldwide). 

## <a name="connecting-to-the-azure-ad-online-service"></a>Připojení ke službě Azure AD online

>[!NOTE]
>Pokud chcete používat rutiny modulu MSOnline, musíte je spustit z Windows PowerShellu. PowerShell Core tyto rutiny nepodporuje.

K připojení ke službě Azure AD z předplatného sady Office 365 můžete použít modul MSOnline. Připojení používá uživatelské jméno a heslo sady Office 365 nebo používá službu Multi-Factor Authentication (MFA). Můžete se připojit pomocí Azure Portal nebo příkazového řádku Windows PowerShellu (nemusí se nacházet vyššími oprávněními).

Příklad PowerShellu je uveden níže. Rutina [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) vyzve k zadání přihlašovacích údajů a uloží `Msolcred` je do proměnné. Pak rutina [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) použije přihlašovací údaje pro připojení ke službě Azure Directory Online Service. Pokud se chcete připojit ke konkrétnímu prostředí Azure, použijte `AzureEnvironment` parametr.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

Pokud neobdržíte žádné chyby, úspěšně jste se připojili. Rychlý test je spuštění rutiny sady Office 365, například `Get-MsolUser`a zobrazení výsledků. Pokud se zobrazí chyby, počítejte s tím, že běžný problém je nesprávné heslo.

>[!NOTE]
>K připojení ke službě Azure AD z předplatného Office 365 můžete použít také modul AzureRM nebo AZ Module. Rutina Main pro připojení je [Connect-AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0). Tato rutina podporuje `AzureEnvironmentName` parametr pro konkrétní prostředí sady Office 365.

## <a name="creating-a-powershell-runbook-from-an-existing-script"></a>Vytvoření Runbooku PowerShellu z existujícího skriptu

Přístup k funkcím sady Office 365 ze skriptu prostředí PowerShell. Tady je příklad skriptu pro přihlašovací údaje s názvem `Office-Credentials` s uživatelským jménem. `admin@TenantOne.com` Používá `Get-AutomationPSCredential` se k importu přihlašovacích údajů pro Office 365.

```powershell
$emailFromAddress = "admin@TenantOne.com" 
$emailToAddress = "servicedesk@TenantOne.com" 
$emailSMTPServer = "outlook.office365.com" 
$emailSubject = "Office 365 License Report" 

$credObject = Get-AutomationPSCredential -Name "Office-Credentials" 
Connect-MsolService -Credential $credObject

$O365Licenses = Get-MsolAccountSku | Out-String 
Send-MailMessage -Credential $credObject -From $emailFromAddress -To $emailToAddress -Subject $emailSubject -Body 

$O365Licenses -SmtpServer $emailSMTPServer -UseSSL
```

## <a name="running-the-script-in-a-runbook"></a>Spuštění skriptu v Runbooku

Můžete použít svůj skript v sadě Azure Automation Runbook. Pro účely tohoto příkladu použijeme typ Runbooku prostředí PowerShell.

1. Vytvořte novou PowerShellový Runbook. Další informace najdete [v tématu Vytvoření sady runbook Azure Automation](https://docs.microsoft.com/azure/automation/automation-quickstart-create-runbook).
2. Z účtu Automation v části **Automatizace procesu**vyberte **Runbooky** .
3. Vyberte novou sadu Runbook a klikněte na **Upravit**.
4. Zkopírujte skript a vložte ho do textového editoru pro sadu Runbook.
5. Vyberte **assety**, potom rozbalte **přihlašovací údaje** a ověřte, jestli je zde přihlašovací údaje pro Office 365.
6. Klikněte na **Uložit**.
7. Vyberte **podokno test**a potom kliknutím na **Spustit** zahajte testování Runbooku. Viz [Správa runbooků v Azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks).
8. Po dokončení testování ukončete z podokna test.

## <a name="publishing-and-scheduling-the-runbook"></a>Publikování a plánování Runbooku

Pokud chcete publikovat a potom naplánovat Runbook, přečtěte si téma [Správa runbooků v Azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks).

## <a name="next-steps"></a>Další kroky

* V Azure Automation najdete informace o prostředcích přihlašovacích údajů automatizace v [prostředcích přihlašovacích údajů](shared-resources/credentials.md).
* Informace o tom, jak pracovat s moduly automatizace, najdete v tématu [Správa modulů v Azure Automation](shared-resources/modules.md) .
* Přehled správy runbooků najdete [v tématu Správa runbooků v Azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks).
* Další informace o metodách, které lze použít ke spuštění sady Runbook v Azure Automation, najdete v tématu [spuštění sady Runbook v Azure Automation](automation-starting-a-runbook.md).
* Další informace o PowerShellu, včetně referenčních modulů jazyka a výukových modulů, najdete v [dokumentaci k PowerShellu](https://docs.microsoft.com/powershell/scripting/overview).