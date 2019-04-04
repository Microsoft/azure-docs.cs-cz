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
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: b6a859f1104be8650b95ac789f3da606fb1b70f6
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58486678"
---
# <a name="validate-azure-identity"></a>Ověření identit Azure

Použijte nástroj prerequisite Checker připravenosti Azure Stack (**AzsReadinessChecker**) Chcete-li ověřit, že Azure Active Directory (Azure AD) připravený k použití s Azure Stack. Ověřte vaše řešení Azure identity před zahájením nasazení služby Azure Stack.  

Kontrola připravenosti ověří:

- Azure Active Directory (Azure AD) jako zprostředkovatele identity pro službu Azure Stack.
- Účet Azure AD, který chcete použít, můžete přihlásit jako globální správce Azure Active Directory.

Ověření zajišťuje, že je prostředí připravené pro službu Azure Stack k ukládání informací o uživatele, aplikace, skupiny nebo instanční objekty ze služby Azure Stack ve službě Azure AD.

## <a name="get-the-readiness-checker-tool"></a>Získejte nástroj prerequisite checker připravenosti

Stáhněte si nejnovější verzi nástroje Azure Stack připravenosti kontrola (AzsReadinessChecker) z [Galerie prostředí PowerShell](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Požadavky

Vyžadují se následující požadavky:

**Počítač, na kterém je nástroj spuštěn:**

- Windows 10 nebo Windows Server 2016 s připojením k Internetu.
- Prostředí PowerShell 5.1 nebo novější. Zkontrolujte verzi spuštěním následujícího příkazu prostředí PowerShell a pak si projděte **hlavní** verze a **menší** verze:  

  ```powershell
  $PSVersionTable.PSVersion
  ```

- [Prostředí PowerShell nakonfigurovaná pro Azure Stack](azure-stack-powershell-install.md).
- Nejnovější verzi [Microsoft Azure Stack připravenosti kontrola](https://aka.ms/AzsReadinessChecker) nástroj.

**Prostředí Azure Active Directory:**

- Identifikace účtu služby Azure AD pro Azure Stack, a ujistěte se, že je globální správce Azure Active Directory.
- Určete název tenanta Azure AD. Název tenanta musí být název primární doména služby Azure Active Directory; například **contoso.onmicrosoft.com**.
- Určete, které bude používat prostředí Azure. Podporované hodnoty pro parametr name prostředí **AzureCloud**, **AzureChinaCloud**, nebo **AzureUSGovernment**podle toho, jaké předplatné Azure, můžete použít.

## <a name="steps-to-validate-azure-identity"></a>Kroky k ověření identit Azure

1. Na počítači, který splňuje požadavky, otevřete příkazový řádek se zvýšenými oprávněními prostředí PowerShell a spusťte následující příkaz k instalaci **AzsReadinessChecker**:  

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. Z příkazového řádku PowerShell, spusťte následující příkaz pro nastavení **$serviceAdminCredential** jako správce služeb pro vašeho tenanta Azure AD.  Nahraďte **serviceadmin\@contoso.onmicrosoft.com** názvem vašeho účtu a tenanta:

   ```powershell
   $serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter credentials for service administrator of Azure Active Directory tenant"
   ```

3. Z příkazového řádku PowerShell spuštěním následujícího příkazu spusťte ověření služby Azure AD:

   - Zadejte hodnotu názvu prostředí pro **AzureEnvironment**. Podporované hodnoty pro parametr name prostředí **AzureCloud**, **AzureChinaCloud**, nebo **AzureUSGovernment**podle toho, jaké předplatné Azure, můžete použít.
   - Nahraďte **contoso.onmicrosoft.com** názvem vašeho tenanta Azure Active Directory.

   ```powershell
   Invoke-AzsAzureIdentityValidation -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment <environment name> -AADDirectoryTenantName contoso.onmicrosoft.com
   ```

4. Po spuštění nástroje, prohlédněte si výstup. Potvrďte, že stav je **OK** pro požadavky na instalaci. Úspěšné ověření se zobrazí jako na následujícím obrázku:

   ```powershell
   Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
   Starting Azure Identity Validation

   Checking Installation Requirements: OK

   Finished Azure Identity Validation

   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsAzureIdentityValidation Completed
   ```

## <a name="report-and-log-file"></a>Sestavy a soubor protokolu

Každé ověření při spuštění, zaznamená výsledky do **AzsReadinessChecker.log** a **AzsReadinessCheckerReport.json**. Umístění těchto souborů se zobrazí s výsledky ověření v prostředí PowerShell.

Tyto soubory můžete sdílet stav ověření před nasazením služby Azure Stack nebo prozkoumat zaznamenané problémy s ověřením. Oba soubory zachovat výsledky každé následné ověření. Tato sestava poskytuje vaše nasazení team potvrzení konfigurace identity. Soubor protokolu mohou pomoci týmu nasazení nebo odborné pomoci prozkoumat problémy s ověřením.

Ve výchozím nastavení, oba soubory jsou zapsány do **C:\Users\<uživatelské jméno > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json**.  

- Použití **- OutputPath** ***&lt;cesta&gt;*** parametr na konci spuštění příkazového řádku a zadejte umístění různých sestav.
- Použití **- CleanReport** parametr na konci příkazu run se vymazat informace o předchozích spuštění nástroje z **AzsReadinessCheckerReport.json**.

Další informace najdete v tématu [sestavu ověření služby Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Chyby ověřování

Pokud se ověření nezdaří, v okně Powershellu zobrazovat podrobnosti o chybě. Nástroj také protokoluje informace do souboru AzsReadinessChecker.log.

Následující příklady poskytují pokyny o běžných chyb při ověřování.

### <a name="expired-or-temporary-password"></a>Vypršela platnost, nebo dočasné heslo

```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The password for account  has expired or is a temporary password that needs to be reset before continuing. Run Login-AzureRMAccount, login with  credentials and follow the prompts to reset.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Příčina** – účet nemůže přihlásit, protože heslo je buď vypršela platnost, nebo je pouze dočasné.

**Rozlišení** – v prostředí PowerShell, spusťte následující příkaz a postupujte podle pokynů k resetování hesla:

```powershell
Login-AzureRMAccount
```

Případně, přihlaste se k [webu Azure portal](https://portal.azure.com) jako vlastník účtu a uživatel bude muset změnit heslo.

### <a name="unknown-user-type"></a>Typ Neznámý uživatel 
 
```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
Unknown user type detected. Check the account  is valid for AzureChinaCloud
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Příčina** – účet přihlásit k zadané Azure Active Directory (**AADDirectoryTenantName**). V tomto příkladu **AzureChinaCloud** je stanoveno, **AzureEnvironment**.

**Rozlišení** – ověřte, že účet je platný pro zadaný prostředí Azure. V prostředí PowerShell spusťte následující příkaz k ověření, že účet je platný pro konkrétní prostředí:

```powershell
Login-AzureRmAccount –EnvironmentName AzureChinaCloud
```

### <a name="account-is-not-an-administrator"></a>Účet nemá oprávnění správce

```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The Service Admin account you entered 'admin@contoso.onmicrosoft.com' is not an administrator of the Azure Active Directory tenant 'contoso.onmicrosoft.com'.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Příčina** – i když tento účet můžete úspěšně přihlásit, že účet není správce služby Azure Active Directory (**AADDirectoryTenantName**).  

**Řešení** – přihlásit [webu Azure portal](https://portal.azure.com) jako vlastník účtu, přejděte na **Azure Active Directory**, pak **uživatelé**, pak **vyberte Uživatel**, pak **Role adresáře**a potom ověřte je uživatel **globálního správce**. Pokud je účet **uživatele**, přejděte na stránku **Azure Active Directory** > **vlastní názvy domén**a potvrďte, je zadán název pro  **AADDirectoryTenantName** je označen jako primární doména název pro tento adresář. V tomto příkladu, který je **contoso.onmicrosoft.com**.

Azure Stack vyžaduje, aby název domény je název primární doménu.

## <a name="next-steps"></a>Další kroky

[Ověření registrace v Azure](azure-stack-validate-registration.md)  
[Podívejte se na sestavu připravenosti](azure-stack-validation-report.md)  
[Důležité informace o integraci Azure Stack obecné](azure-stack-datacenter-integration.md)  
