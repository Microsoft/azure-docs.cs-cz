---
title: 'Azure Active Directory B2C: Využití sestav ukázky rozhraní API a definice | Microsoft Docs'
description: Průvodce a ukázky na získávání sestavy na klienta Azure AD B2C, uživatelé, ověřování a ověřování službou Multi-Factor Authentication
services: active-directory-b2c
documentationcenter: dev-center-name
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.topic: article
ms.workload: identity
ms.date: 08/04/2017
ms.author: davidmu
ms.openlocfilehash: 3470f45683ea508dfe338c313a5307eb6c1164d8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Přístup k použití sestav v Azure AD B2C prostřednictvím rozhraní API pro generování sestav

Azure Active Directory B2C (Azure AD B2C) zajišťuje ověřování na základě přihlášení uživatele a ověřování Azure Multi-Factor Authentication. Ověřování se poskytuje koncovým uživatelům vaší aplikace rodiny napříč poskytovatelů identit. Když víte, počet uživatelů, registrované v klientovi, zprostředkovatele, který se používá k registraci a počet ověřování podle typu, je zodpovědět otázky jako:
* Kolik uživatelů z každého typu zprostředkovatele identity (například účet Microsoft nebo LinkedIn) registrovali v posledních 10 dnů?
* Kolik ověřování pomocí služby Multi-Factor Authentication byly úspěšně dokončeny za minulý měsíc?
* Kolik ověřování přihlášení v základě byly dokončeny tohoto měsíce? Za den? Na aplikaci?
* Jak můžete odhadnout očekávané měsíční náklady na Moje aktivity klienta Azure AD B2C?

Tento článek se zaměřuje na sestavy, které jsou svázané s fakturační aktivity, která je založena na počtu uživatelů, fakturovatelný sign v based ověřování a ověřování službou Multi-Factor Authentication.


## <a name="prerequisites"></a>Požadavky
Než začnete, musíte dokončit kroky v [požadavky pro přístup k rozhraní API pro vytváření sestav Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/). Vytvořit aplikaci, získat tajný klíč a jí udělit přístup práva k sestavy klienta Azure AD B2C. *Bash skriptu* a *skript v jazyce Python* příklady jsou také uvedeny zde. 

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


## <a name="usage-report-definitions"></a>Definice sestavy využití
* **tenantUserCount**: počet uživatelů v klientovi typem zprostředkovatele identity, za den za posledních 30 dní. (Volitelně `TimeStamp` filtr poskytuje počty uživatelů ze zadaného data na aktuální datum). Sestava obsahuje:
  * **TotalUserCount**: počet všechny uživatelské objekty.
  * **OtherUserCount**: počet uživatelů Azure Active Directory (nikoli uživatelům Azure AD B2C).
  * **LocalUserCount**: počet uživatelských účtů Azure AD B2C vytvořit s přihlašovacími údaji místního klienta Azure AD B2C.

* **AlternateIdUserCount**: počet uživatelů, Azure AD B2C zaregistrována zprostředkovatelů externí identity (například Facebook, účet Microsoft nebo jiného klienta Azure Active Directory, také označuje jako `OrgId`).

* **b2cAuthenticationCountSummary**: Souhrn denní počet fakturovatelný ověření za posledních 30 dní, podle dne a typu toku ověřování.

* **b2cAuthenticationCount**: počet ověření v časovém období. Výchozí hodnota je za posledních 30 dní.  (Volitelné: na začátek a konec `TimeStamp` parametry definovat za určité časové období.) Výstup obsahuje `StartTimeStamp` (nejdřívější datum aktivity pro tohoto klienta) a `EndTimeStamp` (nejnovější aktualizace).

* **b2cMfaRequestCountSummary**: Souhrn denní počet ověřování službou Multi-Factor Authentication podle dne a typu (SMS nebo hlasové).


## <a name="limitations"></a>Omezení
Uživatel počet data se aktualizují každých 24 až 48 hodin. Ověření se aktualizují několikrát za den. Při použití `ApplicationId` odpověď prázdná sestavy filtr, může být kvůli jednomu z následujících podmínek:
  * ID aplikace v klientovi neexistuje. Zkontrolujte, zda že je správný.
  * ID aplikace existuje, ale v období vytváření sestav nebyla nalezena žádná data. Zkontrolujte parametry data a času.


## <a name="next-steps"></a>Další postup
### <a name="monthly-bill-estimates-for-azure-ad"></a>Odhadne měsíčních nákladů pro Azure AD
V kombinaci s [nejaktuálnější Azure AD B2C cenách dostupné](https://azure.microsoft.com/pricing/details/active-directory-b2c/), můžete odhadnout denní, týdenní a měsíční využití platformy Azure.  Odhad je obzvláště užitečná při plánování změny v chování klienta, který může mít vliv na celkové náklady. Můžete zkontrolovat skutečné náklady v vaše [přidružené předplatné Azure](active-directory-b2c-how-to-enable-billing.md).

### <a name="options-for-other-output-formats"></a>Možnosti pro ostatní formáty výstupu
Následující kód ukazuje příklady odeslání výstupu do formátu JSON, seznam hodnot název a XML:
```powershell
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath b2cUserJourneySummaryEvents.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```
