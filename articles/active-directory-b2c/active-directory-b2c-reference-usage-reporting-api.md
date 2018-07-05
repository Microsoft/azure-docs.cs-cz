---
title: Ukázky rozhraní API vytváření sestav využití a definice v Azure Active Directory B2C | Dokumentace Microsoftu
description: Příručka a ukázky na získání sestavy v tenantovi Azure AD B2C, uživatelů, ověřování a ověřování službou Multi-Factor Authentication.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 544b0618f9135b684846c42bb7edeb37cf599883
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445530"
---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Přístup k použití sestav v Azure AD B2C prostřednictvím rozhraní API pro generování sestav

Azure Active Directory B2C (Azure AD B2C) zajišťuje ověřování na základě přihlášení uživatele a ověřování Azure Multi-Factor Authentication. Ověřování je k dispozici pro koncové uživatele řady aplikace zprostředkovatele identity. Když víte, počet uživatelů v tenantovi, zprostředkovatele, který se použije k registraci a počet ověření registrovaných typů, můžete zodpovědět dotazy jako:
* Kolik uživatelů z každého typu zprostředkovatele identity (třeba účtu Microsoft nebo LinkedIn) jste se zaregistrovali v posledních 10 dnů?
* Kolik ověření pomocí služby Multi-Factor Authentication byly úspěšně dokončeny během posledního měsíce?
* Kolik ověřování přihlašování v podle dokončili jste tento měsíc? Za den? Na aplikaci?
* Jak lze odhadněte očekávané měsíční náklady pro moje aktivity tenanta Azure AD B2C?

Tento článek se zaměřuje na sestavy, které jsou vázány na fakturační aktivitu, která je založena na počet uživatelů, fakturovatelný přihlašování v podle ověřování a ověřování službou Multi-Factor Authentication.


## <a name="prerequisites"></a>Požadavky
Než začnete, potřebujete k dokončení kroků v [požadavky pro přístup k rozhraní API pro generování sestav Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/). Vytvoření aplikace, získání tajného kódu pro něj a jí udělit přístup práva k sestavy vašeho tenanta Azure AD B2C. *Skriptu bash* a *skript Pythonu* příklady jsou také uvedeny zde. 

## <a name="powershell-script"></a>Skript PowerShellu
Tento skript ukazuje vytvoření čtyři sestavy využití pomocí `TimeStamp` parametr a `ApplicationId` filtru.

```powershell
# This script will require the Web Application and permissions setup in Azure Active Directory

# Constants
$ClientID      = "your-client-application-id-here"  
$ClientSecret  = "your-client-application-secret-here"
$loginURL      = "https://login.microsoftonline.com"
$tenantdomain  = "your-b2c-tenant-domain.onmicrosoft.com"  
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body          = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth         = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
if ($oauth.access_token -ne $null) {
    $headerParams  = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    Write-host Data from the tenantUserCount report
    Write-host ====================================================
     # Returns a JSON document for the report
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the tenantUserCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?%24filter=TimeStamp+gt+2016-10-15&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCountSummary report
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=TimeStamp+gt+2016-09-20+and+TimeStamp+lt+2016-10-03&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with ApplicationId filter
    Write-host ====================================================
    # Returns a JSON document for the " " report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCountSummary
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCount?%24filter=TimeStamp+gt+2016-09-10+and+TimeStamp+lt+2016-10-04&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with ApplicationId filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
     Write-host $myReport.Content

} else {
    Write-Host "ERROR: No Access Token"
    }
```


## <a name="usage-report-definitions"></a>Definice sestav využití
* **tenantUserCount**: počet uživatelů v tenantovi typem zprostředkovatele identity, každý den za posledních 30 dní. (Volitelně můžete `TimeStamp` filtr poskytuje počty uživatelů od zadaného data na aktuální datum). Tato sestava poskytuje:
  * **TotalUserCount**: počet všech objektů uživatelů.
  * **OtherUserCount**: počet uživatelů Azure Active Directory (nikoli uživatele Azure AD B2C).
  * **LocalUserCount**: počet uživatelských účtů Azure AD B2C vytvořené pomocí přihlašovacích údajů místního do tenanta Azure AD B2C.

* **AlternateIdUserCount**: počet uživatelů Azure AD B2C zaregistrovaného externích zprostředkovatelů identity (například Facebook, účet Microsoft nebo jiného tenanta Azure Active Directory, také nazývané `OrgId`).

* **b2cAuthenticationCountSummary**: Souhrn denní počet účtovaných ověření za posledních 30 dní, den a typu tok ověřování.

* **b2cAuthenticationCount**: počet ověření v časovém období. Výchozí hodnota je v posledních 30 dnech.  (Volitelné: na začátek a konec `TimeStamp` definovat parametry za určité časové období.) Výstup obsahuje `StartTimeStamp` (nejstarší data aktivity pro tohoto tenanta) a `EndTimeStamp` (nejnovější aktualizace).

* **b2cMfaRequestCountSummary**: Souhrn dennímu počtu ověřování službou Multi-Factor Authentication, den a typ (SMS nebo hlasových).


## <a name="limitations"></a>Omezení
Údaje o počtu uživatelů se aktualizují každých 24 až 48 hodin. Ověření jsou aktualizuje několikrát denně. Při použití `ApplicationId` filtr, odpověď prázdnou sestavou může být způsobené jedním z následujících podmínek:
  * ID aplikace neexistuje v tenantovi. Ujistěte se, že je správný.
  * ID aplikace existuje, ale v období vytváření sestav se nenašla žádná data. Zkontrolujte parametry data a času.


## <a name="next-steps"></a>Další postup
### <a name="monthly-bill-estimates-for-azure-ad"></a>Měsíční náklady odhady pro službu Azure AD
V kombinaci s [nejaktuálnější Azure AD B2C ceny dostupné](https://azure.microsoft.com/pricing/details/active-directory-b2c/), chcete-li odhadnout denní, týdenní a měsíční využití Azure.  Odhad je obzvláště užitečná při plánování změny v chování tenanta, které může mít vliv na celkové náklady. Můžete zkontrolovat skutečné náklady v vaše [propojené předplatné Azure](active-directory-b2c-how-to-enable-billing.md).

### <a name="options-for-other-output-formats"></a>Možnosti pro ostatní formáty výstupu
Následující kód ukazuje příklady odesílání výstupu XML, JSON a seznam hodnot název:
```powershell
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath name-your-file.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```
