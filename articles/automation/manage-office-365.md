---
title: Správa služeb Office 365 pomocí Azure Automation
description: Říká, jak používat Azure Automation ke správě předplacených služeb Office 365.
services: automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9cb505ced907b143fbd6a5f4f30c818092005bb8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550419"
---
# <a name="manage-office-365-services-using-azure-automation"></a>Správa služeb Office 365 pomocí Azure Automation

Azure Automation můžete použít pro správu předplacených služeb Office 365 pro produkty, jako je Microsoft Word a Microsoft Outlook. Interakce s Office 365 jsou povolené [službou Azure Active Directory (Azure AD).](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) Najdete [v tématu Použití Azure AD v Azure Automation k ověření azure](automation-use-azure-ad.md).

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Požadavky

Ke správě předplacených služeb Office 365 v Azure Automation potřebujete následující.

* Předplatné Azure. Viz [Průvodce rozhodnutím o předplatném](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/).
* Objekt Automatizace v Azure pro uložení přihlašovacích údajů uživatelského účtu a runbooků. Viz [úvod do Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro).
* Azure AD: Najdete [v tématu Použití Azure AD v Azure Automation k ověření azure](automation-use-azure-ad.md).
* Tenant Office 365 s účtem. Viz [Nastavení klienta Office 365](https://docs.microsoft.com/sharepoint/dev/spfx/set-up-your-developer-tenant).

## <a name="installing-the-msonline-and-msonlineext-modules"></a>Instalace modulů MSOnline a MSOnlineExt

Použití Office 365 v rámci Azure Automation vyžaduje Microsoft`MSOnline` Azure Active Directory pro Windows PowerShell (modul). Budete také potřebovat modul [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35), který zjednodušuje správu Azure AD v jednoklientských a víceklientských prostředích. Nainstalujte moduly, jak je popsáno v [použití Azure AD v Azure Automation k ověření azure](automation-use-azure-ad.md).

>[!NOTE]
>Chcete-li použít Prostředí MSOnline PowerShell, musíte být členem Azure AD. Uživatelé typu Host nemohou modul používat.

## <a name="creating-an-azure-automation-account"></a>Vytvoření účtu Azure Automation

K dokončení kroků v tomto článku potřebujete účet v Azure Automation. Viz [Vytvoření účtu Azure Automation](automation-quickstart-create-account.md).
 
## <a name="adding-msonline-and-msonlineext-as-assets"></a>Přidání MSOnline a MSOnlineExt jako datových zdrojů

Nyní přidejte nainstalované moduly MSOnline a MSOnlineExt, které umožní funkci Office 365. Informace o [správě modulů v Azure Automation](shared-resources/modules.md).

1. Na portálu Azure vyberte **Účty automatizace**.
2. Zvolte svůj účet Automation.
3. V části **Sdílené prostředky**vyberte **Galerii modulů** .
4. Vyhledejte službu MSOnline.
5. Vyberte `MSOnline` modul PowerShell u něj a kliknutím na **Importovat** modul importujte jako datový zdroj.
6. Opakováním kroků 4 a 5 `MSOnlineExt` vyhledejte a importujte modul. 

## <a name="creating-a-credential-asset-optional"></a>Vytvoření datového zdroje přihlašovacích údajů (volitelného)

Je volitelné vytvořit datový zdroj přihlašovacích údajů pro uživatele office 365 pro správu, který má oprávnění ke spuštění skriptu. Může však pomoci zabránit odhalení uživatelských jmen a hesel ve skriptech prostředí PowerShell. Pokyny naleznete v [tématu Vytvoření datového zdroje přihlašovacích údajů](automation-use-azure-ad.md#creating-a-credential-asset).

## <a name="creating-an-office-365-service-account"></a>Vytvoření účtu služby Office 365

Pokud chcete provozovat předplacené služby Office 365, potřebujete účet služby Office 365 s oprávněními k tomu, co chcete. Můžete použít jeden globální účet správce, jeden účet na službu nebo mít jednu funkci nebo skript ke spuštění. V každém případě účet služby vyžaduje složité a bezpečné heslo. Viz [Nastavení Office 365 pro firmy](https://docs.microsoft.com/microsoft-365/admin/setup/setup?view=o365-worldwide). 

## <a name="connecting-to-the-azure-ad-online-service"></a>Připojení k online službě Azure AD

>[!NOTE]
>Chcete-li použít rutiny modulu MSOnline, je nutné je spustit z prostředí Windows PowerShell. PowerShell Core nepodporuje tyto rutiny.

Pomocí modulu MSOnline se můžete připojit ke službě Azure AD z předplatného Office 365. Připojení používá uživatelské jméno a heslo Office 365 nebo používá vícefaktorové ověřování (MFA). Můžete se připojit pomocí portálu Azure nebo příkazového řádku prostředí Windows PowerShell (nemusí být zvýšená).

Příklad prostředí PowerShell je uveden níže. Rutina [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) zobrazí výzvy pro pověření a `Msolcred` uloží je do proměnné. Pak [rutina Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) používá přihlašovací údaje pro připojení k online službě adresářů Azure. Pokud se chcete připojit ke konkrétnímu `AzureEnvironment` prostředí Azure, použijte parametr.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

Pokud se nezobrazí žádné chyby, úspěšně jste se připojili. Rychlý test je spuštění rutiny Office 365, `Get-MsolUser`například , a zobrazit výsledky. Pokud se zobrazí chyby, všimněte si, že běžný problém je nesprávné heslo.

>[!NOTE]
>Můžete také použít modul AzureRM nebo modul Az pro připojení k Azure AD z předplatného Office 365. Hlavní rutina připojení je [Connect-AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0). Tato rutina podporuje `AzureEnvironmentName` parametr pro konkrétní prostředí Office 365.

## <a name="creating-a-powershell-runbook-from-an-existing-script"></a>Vytvoření runbooku prostředí PowerShell z existujícího skriptu

Přístup k funkcím Office 365 můžete přistupovat ze skriptu PowerShellu. Tady je příklad skriptu pro pověření `Office-Credentials` s názvem `admin@TenantOne.com`s uživatelským jménem . Používá `Get-AutomationPSCredential` se k importu přihlašovacích údajů Office 365.

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

## <a name="running-the-script-in-a-runbook"></a>Spuštění skriptu v runbooku

Skript můžete použít v runbooku Azure Automation. Pro například účely použijeme typ runbooku Prostředí PowerShell.

1. Vytvořte novou runbook prostředí PowerShell. Viz [Vytvoření runbooku Azure Automation](https://docs.microsoft.com/azure/automation/automation-quickstart-create-runbook).
2. Ve svém účtu Automation vyberte **runbooky** v části **Automatizace procesů**.
3. Vyberte novou knihu Runbook a klepněte na **tlačítko Upravit**.
4. Zkopírujte skript a vložte jej do textového editoru pro runbook.
5. Vyberte **DATOVÉ ZDROJE**, **rozbalte položku Pověření** a ověřte, zda je k dispozici pověření Office 365.
6. Klikněte na **Uložit**.
7. Vyberte **Podokno testování**a kliknutím na **Start** začněte testovat runbook. Viz [Správa runbooků v Azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks).
8. Po dokončení testování ukončete testovací podokno.

## <a name="publishing-and-scheduling-the-runbook"></a>Publikování a plánování sady Runbook

Pokud chcete publikovat a pak naplánovat runbook, přečtěte si informace [o správě runbooků v Azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks).

## <a name="next-steps"></a>Další kroky

* Informace o datových prostředkůch pověření automatizace najdete v [datových prostředkůch pověření v Azure Automation](shared-resources/credentials.md).
* Informace o tom, jak pracovat s moduly automatizace, najdete [v tématu Správa modulů v Azure Automation.](shared-resources/modules.md)
* Přehled správy runbooků najdete v tématu [Správa runbooků v Azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks).
* Další informace o metodách, které se dá použít ke spuštění sady Runbook v Azure Automation, najdete v [tématu Spuštění runbooku v Azure Automation](automation-starting-a-runbook.md).
* Další informace o Prostředí PowerShell, včetně jazykových referenčních a výukových modulů, najdete v [tématu Dokumenty prostředí PowerShell](https://docs.microsoft.com/powershell/scripting/overview).