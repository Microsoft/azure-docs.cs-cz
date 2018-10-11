---
title: Ověření Azure Identity pro Azure Stack | Dokumentace Microsoftu
description: Kontrola připravenosti Azure Stack slouží k ověření identit Azure.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 9c7ac89d1f12e8ec033b201f2c2dd845c11486e2
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077813"
---
# <a name="validate-azure-identity"></a>Ověření identit Azure 
Použijte nástroj Kontrola připravenosti Azure Stack (AzsReadinessChecker) Chcete-li ověřit, že Azure Active Directory (Azure AD) připravený k použití s Azure Stack. Ověřte vaše řešení Azure identity před zahájením nasazení služby Azure Stack.  

Kontrola připravenosti ověří:
 - Azure Active Directory (Azure AD) jako zprostředkovatele identity pro službu Azure Stack.
 - Účet Azure AD, který chcete použít se může přihlásit jako globální správce Azure Active Directory. 

Ověření zajišťuje, že je prostředí připravené pro službu Azure Stack k ukládání informací o uživatele, aplikace, skupiny nebo instanční objekty ze služby Azure Stack ve službě Azure AD.

## <a name="get-the-readiness-checker-tool"></a>Získejte nástroj prerequisite checker připravenosti
Stáhněte si nejnovější verzi nástroje Azure Stack připravenosti kontrola (AzsReadinessChecker) z [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Požadavky
Následující požadavky musí být splněné.

**Počítač, ve kterém se nástroj spouští:**
 - Windows 10 nebo Windows Server 2016 s připojením k Internetu.
 - Prostředí PowerShell 5.1 nebo novější. Pokud chcete zkontrolovat verzi, spusťte následující příkaz prostředí PowerShell a pak si projděte *hlavní* verze a *menší* verze:  

   > `$PSVersionTable.PSVersion`
 - Konfigurace [prostředí PowerShell pro Azure Stack](azure-stack-powershell-install.md). 
 - Nejnovější verzi [Microsoft Azure Stack připravenosti kontrola](https://aka.ms/AzsReadinessChecker) nástroj.

**Prostředí Azure Active Directory:**
 - Identifikace účtu služby Azure AD bude používat pro službu Azure Stack a ujistěte se, že je Azure Active Directory globálního správce.
 - Určete název vašeho Tenanta Azure AD. Název tenanta musí být *primární* názvu domény pro Azure Active Directory. Například *contoso.onmicrosoft.com*. 
 - Identifikujte AzureEnvironement budete používat: *AzureCloud*, *AzureGermanCloud*, nebo *AzureChinaCloud*.

## <a name="validate-azure-identity"></a>Ověření identit Azure 
1. Na počítači, který splňuje požadavky otevřete Správce příkazový řádek Powershellu a spusťte následující příkaz k instalaci AzsReadinessChecker:  

   > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. Z příkazového řádku PowerShell, spusťte následující příkaz pro nastavení *$serviceAdminCredential* jako správce služeb pro vašeho Tenanta Azure AD.  Nahraďte *serviceadmin@contoso.onmicrosoft.com* pomocí vašeho účtu a tenanta. 
   > `$serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant"` 

3. Z příkazového řádku PowerShell spuštěním následujícího příkazu spusťte ověření služby Azure AD. 
   - Zadejte hodnotu pro AzureEnvironment jako *AzureCloud*, *AzureGermanCloud*, nebo *AzureChinaCloud*.  
   - Zadejte Azure Active Directory název Tenanta k nahrazení *contoso.onmicrosoft.com*. 

   > `Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment AzureCloud -AADDirectoryTenantName contoso.onmicrosoft.com`
4. Po spuštění nástroje, prohlédněte si výstup. Potvrďte, že stav je **OK** pro přihlášení a požadavky na instalaci. Úspěšné ověření se zobrazí jako na následujícím obrázku: 
 
![spustit ověření](./media/azure-stack-validate-identity/validation.png)


## <a name="report-and-log-file"></a>Sestavy a soubor protokolu
Každé ověření při spuštění, zaznamená výsledky do **AzsReadinessChecker.log** a **AzsReadinessCheckerReport.json**. Umístění těchto souborů se zobrazí s výsledky ověření v prostředí PowerShell.

Tyto soubory můžete sdílet stav ověření před nasazením služby Azure Stack nebo prozkoumat zaznamenané problémy s ověřením.  Oba soubory zachovat výsledky každé následné ověření. Tato sestava poskytuje vaše nasazení team potvrzení konfigurace identity. Soubor protokolu mohou pomoci týmu nasazení nebo odborné pomoci prozkoumat problémy s ověřením. 

Ve výchozím nastavení, oba soubory jsou zapsány do *C:\Users\<uživatelské jméno > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Použití **- OutputPath** ***&lt;cesta&gt;*** parametr na konci spuštění příkazového řádku a zadejte umístění různých sestav.   
 - Použití **- CleanReport** parametr na konci příkazu run chcete vymazat informace z *AzsReadinessCheckerReport.json*.  informace o předchozích spuštění tohoto nástroje. 

Další informace najdete [sestavu ověření služby Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Chyby ověřování
Pokud se ověření nezdaří, v okně Powershellu zobrazovat podrobnosti o chybě. Nástroj také protokoluje informace do AzsReadinessChecker.log.

Následující příklady poskytují pokyny o běžných chyb při ověřování.

### <a name="expired-or-temporary-password"></a>Vypršela platnost, nebo dočasné heslo 
 
![prošlé heslo](./media/azure-stack-validate-identity/expired-password.png)
**Příčina** – účet nemůže přihlásit, protože heslo je buď vypršela platnost, nebo je pouze dočasné.     

**Rozlišení** – v prostředí PowerShell spusťte následující příkaz a postupujte podle pokynů k resetování hesla.  
> `Login-AzureRMAccount`

Případně, přihlaste se k https://portal.azure.com jako účet a uživatel bude muset změnit heslo.
### <a name="unknown-user-type"></a>Typ Neznámý uživatel 
 
![Neznámý uživatel](./media/azure-stack-validate-identity/unknown-user.png)
**Příčina** – účet nemůže přihlásit do zadaného Azure Active Directory (AADDirectoryTenantName). V tomto příkladu *AzureChinaCloud* je stanoveno, *AzureEnvironment*.

**Rozlišení** – ověřte, že účet je platný pro zadaný prostředí Azure. V prostředí PowerShell, spusťte následující příkaz k ověření účtu je platný pro konkrétní prostředí: Login-AzureRmAccount-EnvironmentName AzureChinaCloud 
### <a name="account-is-not-an-administrator"></a>Účet nemá oprávnění správce 
 
![Ne správce](./media/azure-stack-validate-identity/not-admin.png)

**Příčina** – i když tento účet můžete úspěšně přihlásit, že účet není správce služby Azure Active Directory (AADDirectoryTenantName).  

**Rozlišení** – Přihlaste se k https://portal.azure.com jako účet, přejděte na **Azure Active Directory** > **uživatelé** > *vyberte uživatele*  >  **Role adresáře**a potom ověřte je uživatel **globálního správce**.  Pokud je účet uživatele, přejděte na **Azure Active Directory** > **vlastní domény** názvy a ujistěte se, je zadán název pro *AADDirectoryTenantName* je označen jako primární doména název pro tento adresář.  V tomto příkladu, který je *contoso.onmicrosoft.com*. 

Azure Stack vyžaduje, aby název domény je název primární doménu.

## <a name="next-steps"></a>Další kroky
[Ověření registrace služby Azure](azure-stack-validate-registration.md)  
[Podívejte se na sestavu připravenosti](azure-stack-validation-report.md)  
[Důležité informace o integraci Azure Stack obecné](azure-stack-datacenter-integration.md)  

