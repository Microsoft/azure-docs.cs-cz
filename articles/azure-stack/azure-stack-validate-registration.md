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
ms.topic: get-started-article
ms.date: 06/08/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: d6835f05666d66cc4f6aa937c4b85047ce3c2e93
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077065"
---
# <a name="validate-azure-registration"></a>Ověření registrace služby Azure 
Použijte nástroj Kontrola připravenosti Azure Stack (AzsReadinessChecker) k ověření, že vaše předplatné Azure je připravený k použití s Azure Stack. Ověření registrace před zahájením nasazení služby Azure Stack. Kontrola připravenosti ověří:
- Předplatné Azure, které používáte je podporovaného typu. Předplatné musí být Cloud Service Provider (CSP) nebo smlouvu Enterprise (EA). 
- Účet, který používáte k registraci předplatného Azure můžete přihlásit k Azure a je vlastníkem předplatného. 

Další informace o registraci Azure Stack, najdete v části [registrace Azure Stack s využitím Azure](azure-stack-registration.md). 

## <a name="get-the-readiness-checker-tool"></a>Získejte nástroj prerequisite checker připravenosti
Stažení nejnovější verze nástroje Azure Stack připravenosti kontrola (AzsReadinessChecker) je k dispozici na [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Požadavky
Následující požadavky musí být splněné.

**Počítač, ve kterém se nástroj spouští:**
 - Windows 10 nebo Windows Server 2016 s připojením k Internetu.
 - Prostředí PowerShell 5.1 nebo novější. Pokud chcete zkontrolovat verzi, spusťte následující příkaz prostředí PowerShell a pak si projděte *hlavní* verze a *menší* verze:  

    >`$PSVersionTable.PSVersion` 
 - Konfigurace [prostředí PowerShell pro Azure Stack](azure-stack-powershell-install.md). 
 - Stáhněte si nejnovější verzi [Microsoft Azure Stack připravenosti kontrola](https://aka.ms/AzsReadinessChecker) nástroj.  

**Prostředí Azure Active Directory:**
 - Určete uživatelské jméno a heslo pro účet, který je vlastníkem předplatného Azure, které budete používat s Azure Stack.  
 - Určete ID předplatného pro předplatné Azure, které budete používat. 
 - Identifikujte AzureEnvironment budete používat: *AzureCloud*, *AzureGermanCloud*, nebo *AzureChinaCloud*.

## <a name="validate-azure-registration"></a>Ověření registrace služby Azure
1. Na počítači, který splňuje požadavky otevřete Správce příkazový řádek Powershellu a spusťte následující příkaz k instalaci AzsReadinessChecker.
    > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. Z příkazového řádku PowerShell, spusťte následující příkaz pro nastavení *$registrationCredential* jako účet, který je vlastníkem předplatného.   Nahraďte *subscriptionowner@contoso.onmicrosoft.com* pomocí vašeho účtu a tenanta. 
    > `$registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"`

3. Z příkazového řádku PowerShell, spusťte následující příkaz pro nastavení *$subscriptionID* jako předplatné Azure, které budete používat. Nahraďte *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx* s vlastním ID předplatného.  
     > `$subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"` 

4. Z příkazového řádku PowerShell spuštěním následujícího příkazu spusťte ověření vašeho předplatného 
   - Zadejte hodnotu pro AzureEnvironment jako *AzureCloud*, *AzureGermanCloud*, nebo *AzureChinaCloud*.  
   - Zadejte správce Azure Active Directory a název vašeho Tenanta Azure Active Directory. 

   > `Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID`

5. Po spuštění nástroje, prohlédněte si výstup. Potvrďte, že stav je v pořádku pro přihlášení a požadavky na registraci. Úspěšné ověření se zobrazí jako na následujícím obrázku:  
![spustit ověření](./media/azure-stack-validate-registration/registration-validation.png)


## <a name="report-and-log-file"></a>Sestavy a soubor protokolu
Každé ověření při spuštění, zaznamená výsledky do **AzsReadinessChecker.log** a **AzsReadinessCheckerReport.json**. Umístění těchto souborů se zobrazí s výsledky ověření v prostředí PowerShell. 

Tyto soubory můžete sdílet stav ověření před nasazením služby Azure Stack nebo prozkoumat zaznamenané problémy s ověřením. Oba soubory zachovat výsledky každé následné ověření. Tato sestava poskytuje vaše nasazení team potvrzení konfigurace identity. Soubor protokolu mohou pomoci týmu nasazení nebo odborné pomoci prozkoumat problémy s ověřením. 

Ve výchozím nastavení, oba soubory jsou zapsány do *C:\Users\<uživatelské jméno > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Použití **- OutputPath** ***&lt;cesta&gt;*** parametr na konci spuštění příkazového řádku a zadejte umístění různých sestav.   
 - Použití **- CleanReport** parametr na konci příkazu run chcete vymazat informace z *AzsReadinessCheckerReport.json*.  informace o předchozích spuštění tohoto nástroje. Další informace najdete [sestavu ověření služby Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Chyby ověřování
Pokud se ověření nezdaří, v okně Powershellu zobrazovat podrobnosti o chybě. Nástroj také protokoluje informace do AzsReadinessChecker.log.

Následující příklady poskytují pokyny o běžných chyb při ověřování.

### <a name="user-must-be-an-owner-of-the-subscription"></a>Uživatel musí být vlastníkem předplatného   
![Vlastník předplatného](./media/azure-stack-validate-registration/subscription-owner.png)
**Příčina** – účet není správcem předplatného Azure.   

**Rozlišení** -použít účet, který je správcem předplatného Azure, který se bude účtovat za použití z nasazení služby Azure Stack.


### <a name="expired-or-temporary-password"></a>Vypršela platnost, nebo dočasné heslo 
![vypršela platnost hesla](./media/azure-stack-validate-registration/expired-password.png)
**Příčina** – účet nemůže přihlásit, protože heslo je buď vypršela platnost, nebo je pouze dočasné.     

**Rozlišení** – v prostředí PowerShell spustit a postupujte podle pokynů k resetování hesla. 
  > `Login-AzureRMAccount` 

Případně, přihlaste se k https://portal.azure.com jako účet a uživatel bude muset změnit heslo.


### <a name="microsoft-accounts-are-not-supported-for-registration"></a>Účty Microsoft se nepodporují pro registraci.  
![Nepodporovaný účet](./media/azure-stack-validate-registration/unsupported-account.png)
**Příčina** – byl zadán účet A Microsoft (jako je Hotmail.com nebo Outlook.com).  Tyto účty nejsou podporovány.

**Rozlišení** -použít účet a předplatné z cloudové služby Provider (CSP) nebo smlouvu Enterprise (EA). 


### <a name="unknown-user-type"></a>Typ Neznámý uživatel  
![Neznámý uživatel](./media/azure-stack-validate-registration/unknown-user.png)
**Příčina** – účet se nemůže připojit k zadané prostředí Azure Active Directory. V tomto příkladu *AzureChinaCloud* je stanoveno, *AzureEnvironment*.  

**Rozlišení** – ověřte, že účet je platný pro zadaný prostředí Azure. V prostředí PowerShell spusťte následující příkaz k ověření, že účet je platný pro konkrétní prostředí.     
  > `Login-AzureRmAccount -EnvironmentName AzureChinaCloud`


## <a name="next-steps"></a>Další kroky
[Ověření Azure identity](azure-stack-validate-identity.md)
[zobrazit sestavu připravenosti](azure-stack-validation-report.md)
[důležité informace o integraci Azure Stack obecné](azure-stack-datacenter-integration.md)

