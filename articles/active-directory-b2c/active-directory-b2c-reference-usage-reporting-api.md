---
title: Ukázky a definice rozhraní API pro vytváření sestav využití
titleSuffix: Azure AD B2C
description: Průvodce a ukázky, které vám pomůžou získat sestavy Azure AD B2C uživatelů klientů, ověřování a Multi-Factor Authentication.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f81acf28b502965f896cd8b38767e7c2e925156c
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949334"
---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Přístup k sestavám využití v Azure AD B2C prostřednictvím rozhraní API pro vytváření sestav

Azure Active Directory B2C (Azure AD B2C) poskytuje ověřování na základě přihlášení uživatele a služby Azure Multi-Factor Authentication. Ověřování se poskytuje pro koncové uživatele vaší rodiny aplikací napříč poskytovateli identity. Pokud znáte počet uživatelů registrovaných v tenantovi, poskytovatelé, kteří použili k registraci, a počet ověřování podle typu, můžete zodpovědět otázky jako:
* Kolik uživatelů od každého typu zprostředkovatele identity (například účet Microsoft nebo LinkedIn) bylo za posledních 10 dní zaregistrováno?
* Kolik ověřování pomocí Multi-Factor Authentication bylo úspěšně dokončeno za poslední měsíc?
* Kolik ověření na základě přihlášení bylo dokončeno v tomto měsíci? Za den? Na aplikaci?
* Jak můžu odhadnout očekávané měsíční náklady aktivity Azure AD B2C tenanta?

Tento článek se zaměřuje na sestavy vázané na fakturační aktivity, které vycházejí z počtu uživatelů, fakturovatelných ověřování na základě přihlášení a vícefaktorového ověřování.


## <a name="prerequisites"></a>Předpoklady
Než začnete, musíte dokončit kroky v části [požadavky pro přístup k rozhraním API pro generování sestav Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/). Vytvořte aplikaci, Získejte pro ni tajný klíč a udělte jim přístupová práva k sestavám vašeho tenanta Azure AD B2C. Příklady *skriptů bash* a *skriptu Pythonu* jsou zde také uvedeny.

## <a name="powershell-script"></a>Skript PowerShellu
Tento skript ukazuje vytvoření čtyř sestav použití pomocí parametru `TimeStamp` a filtru `ApplicationId`.

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
* **tenantUserCount**: počet uživatelů v tenantovi podle typu zprostředkovatele identity za den za posledních 30 dní. (Volitelně `TimeStamp` filtr poskytuje počet uživatelů od zadaného data po aktuální datum). Tato sestava poskytuje:
  * **TotalUserCount**: počet všech uživatelských objektů.
  * **OtherUserCount**: počet uživatelů Azure Active Directory (kteří nejsou Azure AD B2C uživatelé).
  * **LocalUserCount**: počet Azure AD B2C uživatelských účtů vytvořených s přihlašovacími údaji, které jsou místní pro Azure AD B2C tenanta.

* **AlternateIdUserCount**: počet Azure AD B2C uživatelů registrovaných u externích zprostředkovatelů identity (například Facebook, účet Microsoft nebo jiný Azure Active Directory tenant, označovaný také jako `OrgId`).

* **b2cAuthenticationCountSummary**: Shrnutí denního počtu fakturovatelných ověřování za posledních 30 dní, podle dne a typu toku ověřování.

* **b2cAuthenticationCount**: počet ověření v rámci určitého časového období. Výchozí hodnota je posledních 30 dní.  (Volitelné: parametry počáteční a koncové `TimeStamp` definují konkrétní časové období.) Výstup zahrnuje `StartTimeStamp` (nejstarší datum aktivity pro tohoto tenanta) a `EndTimeStamp` (nejnovější aktualizace).

* **b2cMfaRequestCountSummary**: Shrnutí denního počtu vícefaktorového ověřování, podle dne a typu (SMS nebo Voice).


## <a name="limitations"></a>Omezení
Data počtu uživatelů se aktualizují každých 24 až 48 hodin. Ověřování se aktualizují několikrát denně. Při použití filtru `ApplicationId` může být prázdná odpověď sestavy způsobena jednou z následujících podmínek:
  * ID aplikace v tenantovi neexistuje. Ujistěte se, že je správný.
  * ID aplikace existuje, ale v období generování sestav se nenašla žádná data. Zkontrolujte parametry data a času.


## <a name="next-steps"></a>Další kroky
### <a name="monthly-bill-estimates-for-azure-ad"></a>Odhady měsíčních faktur pro Azure AD
V kombinaci s [nejaktuálnějšími dostupnými Azure AD B2C cenami](https://azure.microsoft.com/pricing/details/active-directory-b2c/)můžete odhadnout denní, týdenní a měsíční spotřebu Azure.  Odhad je zvláště užitečný, pokud plánujete změny v chování tenanta, které by mohly mít dopad na celkové náklady. V [propojených předplatných Azure](active-directory-b2c-how-to-enable-billing.md)si můžete projít skutečné náklady.

### <a name="options-for-other-output-formats"></a>Možnosti pro jiné formáty výstupu
Následující kód ukazuje příklady odeslání výstupu do formátu JSON, seznamu hodnot názvu a XML:
```powershell
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath name-your-file.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```
