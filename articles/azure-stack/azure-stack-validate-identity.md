---
title: Ověření identit Azure pro Azure zásobníku | Microsoft Docs
description: Pomocí kontroly připravenosti zásobník Azure k ověření Azure identity.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: fe5e7281cbe01ad11f667729df344f91ef1327d2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="validate-azure-identity"></a>Ověření identit Azure 
Nástroj Kontrola připravenosti zásobník Azure (AzsReadinessChecker) k ověření, že služby Azure Active Directory (Azure AD) je připravený k použití s Azure zásobníku. Ověření řešení identit Azure před zahájením nasazení služby Azure zásobníku.  

Nástroj pro kontrolu připravenosti ověří:
 - Azure Active Directory (Azure AD) jako zprostředkovatel identity pro Azure zásobníku.
 - Účet Azure AD, kterou chcete použít může přihlásit jako globální správce služby Azure Active Directory. 

Ověření zajistí, že je prostředí připravené pro zásobník Azure k ukládání informací o uživatelích, aplikace, skupiny a objekty služby z zásobník Azure ve službě Azure AD.

## <a name="get-the-readiness-checker-tool"></a>Získat nástroje Kontrola připravenosti
Stáhnout nejnovější verzi nástroje pro kontrolu připravenosti zásobník Azure (AzsReadinessChecker) z [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Požadavky
Na místě musí být splněné následující předpoklady.

**Počítač, ve kterém se nástroj spustí:**
 - Windows 10 nebo Windows Server 2016 s připojením k Internetu.
 - Prostředí PowerShell 5.1 nebo novější. Zkontrolujte vaši verzi, spusťte následující příkaz prostředí PowerShell a poté zkontrolovat *hlavní* verze a *menší* verze:  

   > `$PSVersionTable.PSVersion`
 - Konfigurace [prostředí PowerShell pro Azure zásobníku](azure-stack-powershell-install.md). 
 - Nejnovější verzi [Kontrola připravenosti zásobníku Microsoft Azure](https://aka.ms/AzsReadinessChecker) nástroj.

**Prostředí Azure Active Directory:**
 - Určete účet Azure AD, budete používat pro Azure zásobníku a ujistěte se, že Azure Active Directory globální správce.
 - Určete název vašeho klienta Azure AD. Název klienta musí být *primární* název domény vaší služby Azure Active Directory. Například *contoso.onmicrosoft.com*. 
 - Identifikovat AzureEnvironement budete používat: *AzureCloud*, *AzureGermanCloud*, nebo *AzureChinaCloud*.

## <a name="validate-azure-identity"></a>Ověření identit Azure 
1. Na počítači, který splňuje požadavky otevřete Správce příkazovém řádku prostředí PowerShell a spusťte následující příkaz pro instalaci AzsReadinessChecker:  

   > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. Z řádku Powershellu spusťte následující příkaz pro nastavení *$serviceAdminCredential* jako správce služeb pro vašeho Tenanta Azure AD.  Nahraďte *serviceadmin@contoso.onmicrosoft.com* s váš účet a klienta. 
   > `$serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant"` 

3. Z řádku Powershellu spusťte následující příkaz a spusťte ověření služby Azure AD. 
   - Zadejte hodnotu pro AzureEnvironment jako *AzureCloud*, *AzureGermanCloud*, nebo *AzureChinaCloud*.  
   - Zadejte název Azure Active Directory klienta nahradit *contoso.onmicrosoft.com*. 

   > `Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment AzureCloud -AADDirectoryTenantName contoso.onmicrosoft.com`
4. Po spuštění nástroje, prohlédněte si výstup. Zkontrolujte stav **OK** pro přihlášení a požadavky na instalaci. Úspěšné ověření se zobrazí jako na následujícím obrázku: 
 
![Ověření spuštění](./media/azure-stack-validate-identity/validation.png)


## <a name="report-and-log-file"></a>Sestavy a soubor protokolu
Každé ověření časové platnosti spuštěna, protokoluje výsledky do **AzsReadinessChecker.log** a **AzsReadinessCheckerReport.json**. Umístění těchto souborů se zobrazí ve výsledcích ověření v prostředí PowerShell.

Tyto soubory můžete sdílet stav ověření před nasazením Azure zásobníku nebo prozkoumat problémy ověření.  Oba soubory zachovat výsledků kontroly každý následné ověření. Sestava obsahuje vaše nasazení team potvrzení konfigurace identity. Soubor protokolu může pomoci prozkoumat problémy ověření týmu nasazení nebo podpory. 

Ve výchozím nastavení, oba soubory jsou zapsány do *C:\Users\<uživatelské jméno > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Použití **- OutputPath** ***&lt;cesta&gt;*** parametr na konci spuštění příkazového řádku a zadejte umístění různé sestavy.   
 - Použití **- CleanReport** parametr na konci spusťte příkaz chcete vymazat informace z *AzsReadinessCheckerReport.json*.  o předchozích spuštění nástroje. 

Další informace najdete [sestavu ověření Azure zásobníku](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Selhání ověření
Pokud kontrola ověření selže, podrobnosti o selhání se zobrazí v okně prostředí PowerShell. Nástroj taky zaznamená do protokolu informace na AzsReadinessChecker.log.

Následující příklady poskytovat informace o běžných chyb při ověřování.

### <a name="expired-or-temporary-password"></a>Vypršela platnost, nebo dočasné heslo. 
 
![platnost hesla](./media/azure-stack-validate-identity/expired-password.png)
**Příčina** -účet nelze přihlásit, protože heslo je buď vypršela platnost, nebo dočasné.     

**Řešení** – v prostředí PowerShell spusťte následující příkaz a potom postupujte podle pokynů k resetování hesla.  
> `Login-AzureRMAccount`

Alternativně přihlášení do https://portal.azure.com jako účet a uživatel se vynutí změnu hesla.
### <a name="unknown-user-type"></a>Typ Neznámý uživatele 
 
![Neznámé uživatelské](./media/azure-stack-validate-identity/unknown-user.png)
**Příčina** -účet se nemohou přihlásit ke službě zadaný Azure Active Directory (AADDirectoryTenantName). V tomto příkladu *AzureChinaCloud* je zadán jako *AzureEnvironment*.

**Řešení** -potvrďte, že účet je platný pro zadaný prostředí Azure. V prostředí PowerShell, spusťte následující příkaz k ověření účtu je platný pro jakékoli konkrétní prostředí: Login-AzureRmAccount – EnvironmentName AzureChinaCloud 
### <a name="account-is-not-an-administrator"></a>Účet není správce 
 
![Ne správce](./media/azure-stack-validate-identity/not-admin.png)

**Příčina** – i když se účet může úspěšně přihlásit, účet není správce služby Azure Active Directory (AADDirectoryTenantName).  

**Řešení** -přihlášení do https://portal.azure.com jako účet, přejděte na **Azure Active Directory** > **uživatelé** > *vyberte uživatele,*  >  **Directory Role**a pak se ujistěte, je uživatel **globálního správce**.  Pokud je účet uživatele, přejděte na **Azure Active Directory** > **vlastní domény** názvy a potvrďte, že název, jste zadali pro *AADDirectoryTenantName* je Označit jako název primární domény pro tento adresář.  V tomto příkladu, který je *contoso.onmicrosoft.com*. 

Zásobník Azure vyžaduje, aby název domény název primární domény.

## <a name="next-steps"></a>Další kroky
[Ověření registrace Azure](azure-stack-validate-registration.md)  
[Podívejte se na sestavu připravenosti](azure-stack-validation-report.md)  
[Důležité informace o integraci obecné Azure zásobníku](azure-stack-datacenter-integration.md)  

