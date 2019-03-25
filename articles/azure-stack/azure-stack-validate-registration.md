---
title: Ověření registrace služby Azure pro službu Azure Stack | Dokumentace Microsoftu
description: Kontrola připravenosti Azure Stack slouží k ověření registrace služby Azure.
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
ms.openlocfilehash: a777fc1d9052eb58bbebd319fe6cc7f42a09cb9a
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403608"
---
# <a name="validate-azure-registration"></a>Ověření registrace služby Azure

Použijte nástroj prerequisite Checker připravenosti Azure Stack (**AzsReadinessChecker**) k ověření, že vaše předplatné Azure je připravený k použití s Azure Stack. Ověření registrace před zahájením nasazení služby Azure Stack. Kontrola připravenosti ověří, jestli:

- Předplatné Azure, které používáte je podporovaného typu. Předplatné musí být Cloud Service Provider (CSP) nebo smlouvu Enterprise (EA).
- Účet, který používáte k registraci předplatného Azure můžete přihlásit k Azure a je vlastníkem předplatného.

Další informace o registraci Azure Stack, najdete v části [registrace Azure Stack s využitím Azure](azure-stack-registration.md).

## <a name="get-the-readiness-checker-tool"></a>Získejte nástroj prerequisite checker připravenosti

Stáhněte si nejnovější verzi **AzsReadinessChecker** z [Galerie prostředí PowerShell](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Požadavky

Vyžadují se následující požadavky:

**Počítač, na kterém je nástroj spuštěn:**

- Windows 10 nebo Windows Server 2016 s připojením k Internetu.
- Prostředí PowerShell 5.1 nebo novější. K ověření verze, spusťte následující rutinu prostředí PowerShell a pak si projděte **hlavní** a **menší** verze:  

  ```powershell
  $PSVersionTable.PSVersion
  ```

- [Prostředí PowerShell nakonfigurovaná pro Azure Stack](azure-stack-powershell-install.md).
- Nejnovější verzi [Microsoft Azure Stack připravenosti kontrola](https://aka.ms/AzsReadinessChecker).  

**Prostředí Azure Active Directory:**

- Určete uživatelské jméno a heslo pro účet, který je vlastníkem předplatného Azure, které budete používat s Azure Stack.  
- Určete ID předplatného pro předplatné Azure, které budete používat.
- Identifikujte **AzureEnvironment** budete používat. Podporované hodnoty pro parametr name prostředí **AzureCloud**, **AzureChinaCloud**, nebo **AzureUSGovernment**podle toho, jaké předplatné Azure se použití.

## <a name="steps-to-validate-azure-registration"></a>Kroky k ověření registrace služby Azure

1. Na počítači, který splňuje požadavky, otevřete řádku Powershellu se zvýšenými oprávněními a spusťte následující příkaz k instalaci **AzsReadinessChecker**:

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. Z příkazového řádku PowerShell, spusťte následující příkaz pro nastavení `$registrationCredential` jako účet, který je vlastníkem předplatného. Nahraďte `subscriptionowner@contoso.onmicrosoft.com` názvem vašeho účtu a tenanta:

   ```powershell
   $registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"
   ```

   > [!NOTE]
   > Jako zprostředkovatel kryptografických služeb, při použití sdílené služby nebo IUR předplatného je třeba zadat pověření uživatele z tohoto příslušných AAD. Obvykle to bude vypadat podobně jako `subscriptionowner@iurcontoso.onmicrosoft.com`. Tento uživatel musí mít příslušné přihlašovací údaje, jak je popsáno v předchozím kroku.

3. Z příkazového řádku PowerShell, spusťte následující příkaz pro nastavení `$subscriptionID` jako předplatné Azure se má použít. Nahraďte `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` s vlastním ID předplatného:

   ```powershell
   $subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
   ```

4. Z příkazového řádku PowerShell spusťte následující příkaz pro spuštění ověření vašeho předplatného:

   - Zadejte hodnotu pro `AzureEnvironment` jako **AzureCloud**, **AzureGermanCloud**, nebo **AzureChinaCloud**.  
   - Zadejte správce Azure Active Directory a název tenanta Azure Active Directory.

   ```powershell
   Invoke-AzsRegistrationValidation -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID
   ```

5. Po spuštění nástroje, prohlédněte si výstup. Potvrďte, že stav je správný pro přihlášení a požadavky na registraci. Podobně jako v následujícím příkladu se zobrazí výstup úspěšné ověření:

   ```shell
   Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
   Checking Registration Requirements: OK
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsRegistrationValidation Completed
   ```

## <a name="report-and-log-file"></a>Sestavy a soubor protokolu

Každé ověření při spuštění, zaznamená výsledky do **AzsReadinessChecker.log** a **AzsReadinessCheckerReport.json**. Umístění těchto souborů se zobrazí spolu s výsledky ověření v prostředí PowerShell.

Tyto soubory můžete sdílet stav ověření před nasazením služby Azure Stack nebo prozkoumat zaznamenané problémy s ověřením. Oba soubory zachovat výsledky každé následné ověření. Tato sestava poskytuje vaše nasazení team potvrzení konfigurace identity. Soubor protokolu mohou pomoci týmu nasazení nebo odborné pomoci prozkoumat problémy s ověřením.

Ve výchozím nastavení, oba soubory jsou zapsány do **C:\Users\<uživatelské jméno > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json**.  

- Použití **- OutputPath** ***&lt;cesta&gt;*** parametr na konci spuštění příkazového řádku a zadejte umístění různých sestav.
- Použití **- CleanReport** parametr na konci příkazu run se vymazat informace o předchozích spuštění nástroje z **AzsReadinessCheckerReport.json**.

Další informace najdete v tématu [sestavu ověření služby Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Chyby ověřování

Pokud se ověření nezdaří, v okně Powershellu zobrazovat podrobnosti o chybě. Nástroj také zaznamenává informace, které **AzsReadinessChecker.log** souboru.

Následující příklady poskytnout Rady ohledně běžných chyb při ověřování:

### <a name="user-must-be-an-owner-of-the-subscription"></a>Uživatel musí být vlastníkem předplatného

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
The user admin@contoso.onmicrosoft.com is role(s) Reader for subscription 3f961d1c-d1fb-40c3-99ba-44524b56df2d. User must be an owner of the subscription to be used for registration.
Additional help URL https://aka.ms/AzsRemediateRegistration

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Příčina** – účet není správcem předplatného Azure.

**Rozlišení** -použít účet, který je správcem předplatného Azure, který se bude účtovat za použití z nasazení služby Azure Stack.

### <a name="expired-or-temporary-password"></a>Vypršela platnost, nebo dočasné heslo

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription [subscription ID] using account admin@contoso.onmicrosoft.com failed with AADSTS50055: Force Change Password.
Trace ID: [Trace ID]
Correlation ID: [Correlation ID]
Timestamp: 2018-10-22 11:16:56Z: The remote server returned an error: (401) Unauthorized.

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Příčina** – účet přihlásit, protože heslo je buď vypršela platnost, nebo je pouze dočasné.

**Rozlišení** – v prostředí PowerShell, spusťte následující příkaz a postupujte podle pokynů k resetování hesla.

```powershell
Login-AzureRMAccount
```

Případně, přihlaste se k [webu Azure portal](https://portal.azure.com) jako vlastník účtu a uživatel bude muset změnit heslo.

### <a name="unknown-user-type"></a>Typ Neznámý uživatel  

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription <subscription ID> using <account> failed with unknown_user_type: Unknown User Type

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Příčina** – účet přihlásit k zadané prostředí Azure Active Directory. V tomto příkladu **AzureChinaCloud** je stanoveno, **AzureEnvironment**.  

**Rozlišení** – ověřte, že účet je platný pro zadaný prostředí Azure. V prostředí PowerShell spusťte následující příkaz k ověření, že účet je platný pro konkrétní prostředí:

```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

## <a name="next-steps"></a>Další kroky

- [Ověření identit Azure](azure-stack-validate-identity.md)
- [Podívejte se na sestavu připravenosti](azure-stack-validation-report.md)
- [Důležité informace o integraci Azure Stack obecné](azure-stack-datacenter-integration.md)
