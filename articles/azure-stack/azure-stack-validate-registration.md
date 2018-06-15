---
title: Ověření Azure registrace pro Azure zásobníku | Microsoft Docs
description: Pomocí kontroly připravenosti zásobník Azure k ověření registrace Azure.
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
ms.openlocfilehash: 84eb1c08cc3f9ef104e2eb0b96ed397315c3f374
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937837"
---
# <a name="validate-azure-registration"></a>Ověření registrace Azure 
Nástroj Kontrola připravenosti zásobník Azure (AzsReadinessChecker) k ověření, že vaše předplatné Azure je připravené k použití s Azure zásobníku. Ověření registrace před zahájením nasazení služby Azure zásobníku. Nástroj pro kontrolu připravenosti ověří:
- Předplatné Azure, které používáte je podporovaného typu. Odběry musí být poskytovatele cloudové služby (CSP) nebo Enterprise Agreement (EA). 
- Účet, který použijete k registraci předplatného Azure můžete přihlášení k Azure a je vlastník předplatného. 

Další informace o registraci zásobník Azure najdete v tématu [zaregistrovat zásobník Azure s Azure](azure-stack-registration.md). 

## <a name="get-the-readiness-checker-tool"></a>Získat nástroje Kontrola připravenosti
Stažení nejnovější verze nástroje pro kontrolu připravenosti zásobník Azure (AzsReadinessChecker) je k dispozici na [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Požadavky
Na místě musí být splněné následující předpoklady.

**Počítač, ve kterém se nástroj spustí:**
 - Windows 10 nebo Windows Server 2016 s připojením k Internetu.
 - Prostředí PowerShell 5.1 nebo novější. Zkontrolujte vaši verzi, spusťte následující příkaz prostředí PowerShell a poté zkontrolovat *hlavní* verze a *menší* verze:  

    >`$PSVersionTable.PSVersion` 
 - Konfigurace [prostředí PowerShell pro Azure zásobníku](azure-stack-powershell-install.md). 
 - Stáhněte si nejnovější verzi [Kontrola připravenosti zásobníku Microsoft Azure](https://aka.ms/AzsReadinessChecker) nástroj.  

**Prostředí Azure Active Directory:**
 - Určete uživatelské jméno a heslo pro účet, který je vlastníkem předplatného Azure, které budete používat s Azure zásobníku.  
 - Určete ID předplatného pro předplatné Azure, které chcete použít. 
 - Identifikovat AzureEnvironment budete používat: *AzureCloud*, *AzureGermanCloud*, nebo *AzureChinaCloud*.

## <a name="validate-azure-registration"></a>Ověření registrace Azure
1. Na počítači, který splňuje požadavky otevřete Správce příkazovém řádku prostředí PowerShell a spusťte následující příkaz pro instalaci AzsReadinessChecker.
    > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. Z řádku Powershellu spusťte následující příkaz pro nastavení *$registrationCredential* jako účet, který je vlastníkem předplatného.   Nahraďte *subscriptionowner@contoso.onmicrosoft.com* s váš účet a klienta. 
    > `$registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"`

3. Z řádku Powershellu spusťte následující příkaz pro nastavení *$subscriptionID* jako předplatné Azure, které budete používat. Nahraďte *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx* s vlastní ID předplatného.  
     > `$subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"` 

4. Na řádku prostředí PowerShell spusťte následující příkaz spusťte ověření předplatného 
   - Zadejte hodnotu pro AzureEnvironment jako *AzureCloud*, *AzureGermanCloud*, nebo *AzureChinaCloud*.  
   - Zadejte správce Azure Active Directory a název klienta Azure Active Directory. 

   > `Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID`

5. Po spuštění nástroje, prohlédněte si výstup. Potvrďte, že stav je OK pro přihlášení a požadavky na registraci. Úspěšné ověření se zobrazí jako na následujícím obrázku:  
![Ověření spuštění](./media/azure-stack-validate-registration/registration-validation.png)


## <a name="report-and-log-file"></a>Sestavy a soubor protokolu
Každé ověření časové platnosti spuštěna, protokoluje výsledky do **AzsReadinessChecker.log** a **AzsReadinessCheckerReport.json**. Umístění těchto souborů se zobrazí ve výsledcích ověření v prostředí PowerShell. 

Tyto soubory můžete sdílet stav ověření před nasazením Azure zásobníku nebo prozkoumat problémy ověření. Oba soubory zachovat výsledků kontroly každý následné ověření. Sestava obsahuje vaše nasazení team potvrzení konfigurace identity. Soubor protokolu může pomoci prozkoumat problémy ověření týmu nasazení nebo podpory. 

Ve výchozím nastavení, oba soubory jsou zapsány do *C:\Users\<uživatelské jméno > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Použití **- OutputPath** ***&lt;cesta&gt;*** parametr na konci spuštění příkazového řádku a zadejte umístění různé sestavy.   
 - Použití **- CleanReport** parametr na konci spusťte příkaz chcete vymazat informace z *AzsReadinessCheckerReport.json*.  o předchozích spuštění nástroje. Další informace najdete [sestavu ověření Azure zásobníku](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Selhání ověření
Pokud kontrola ověření selže, podrobnosti o selhání se zobrazí v okně prostředí PowerShell. Nástroj taky zaznamená do protokolu informace na AzsReadinessChecker.log.

Následující příklady poskytovat informace o běžných chyb při ověřování.

### <a name="user-must-be-an-owner-of-the-subscription"></a>Uživatel musí být vlastníkem předplatného   
![Vlastník předplatného](./media/azure-stack-validate-registration/subscription-owner.png)
**Příčina** – účet není správce předplatného Azure.   

**Řešení** -použít účet, který je správcem předplatného Azure, které bude platit pro využití z nasazení služby Azure zásobníku.


### <a name="expired-or-temporary-password"></a>Vypršela platnost, nebo dočasné heslo. 
![platnost hesla](./media/azure-stack-validate-registration/expired-password.png)
**Příčina** -účet nelze přihlásit, protože heslo je buď vypršela platnost, nebo dočasné.     

**Řešení** – v prostředí PowerShell spustit a postupujte podle pokynů k resetování hesla. 
  > `Login-AzureRMAccount` 

Alternativně přihlášení do https://portal.azure.com jako účet a uživatel se vynutí změnu hesla.


### <a name="microsoft-accounts-are-not-supported-for-registration"></a>Účty Microsoft nejsou podporovány pro registraci  
![nepodporované účet](./media/azure-stack-validate-registration/unsupported-account.png)
**Příčina** -byl zadán účet A Microsoft (jako je Outlook.com nebo Hotmail.com).  Tyto účty nejsou podporovány.

**Řešení** -používat účet a předplatné z poskytovatele cloudové služby (CSP) nebo Enterprise Agreement (EA). 


### <a name="unknown-user-type"></a>Typ Neznámý uživatele  
![Neznámý uživatel](./media/azure-stack-validate-registration/unknown-user.png)
**Příčina** -účet se nemůže přihlásit do zadaného prostředí Azure Active Directory. V tomto příkladu *AzureChinaCloud* je zadán jako *AzureEnvironment*.  

**Řešení** -potvrďte, že účet je platný pro zadaný prostředí Azure. V prostředí PowerShell spusťte následující příkaz a ověřte, že účet je platný pro jakékoli konkrétní prostředí.     
  > `Login-AzureRmAccount -EnvironmentName AzureChinaCloud`


## <a name="next-steps"></a>Další kroky
[Ověření identit Azure](azure-stack-validate-identity.md)
[zobrazit sestavu připravenosti](azure-stack-validation-report.md)
[zásobník Azure Obecné aspekty integrace](azure-stack-datacenter-integration.md)

