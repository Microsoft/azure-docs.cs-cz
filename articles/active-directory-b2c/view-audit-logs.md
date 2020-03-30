---
title: Přístup k protokolům auditu a jejich kontrola
titleSuffix: Azure AD B2C
description: Jak získat přístup k protokolům auditu Azure AD B2C programově a na webu Azure Portal.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 83086fa2cb96eba423b9111134a0406d7256821f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264216"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Přístup k protokolům auditu Azure AD B2C

Azure Active Directory B2C (Azure AD B2C) vydává protokoly auditu obsahující informace o aktivitách B2C, vydaných tokenech a přístupu správce. Tento článek obsahuje stručný přehled informací dostupných v protokolech auditu a pokyny, jak získat přístup k těmto datům pro vašeho klienta Azure AD B2C.

Události protokolu auditu jsou uchovávány pouze po dobu **sedmi dnů**. Pokud požadujete delší dobu uchovávání, naplánujte si stažení a uložení protokolů pomocí jedné z níže uvedených metod.

> [!NOTE]
> Přihlášení uživatelů pro jednotlivé aplikace Azure AD B2C se nezobrazují v části **Uživatelé** na stránkách **Azure Active Directory** nebo Azure **AD B2C** na webu Azure Portal. Události přihlášení tam zobrazit aktivitu uživatele, ale nemůže být korelován zpět do aplikace B2C, které uživatel přihlášen. Je nutné použít protokoly auditu pro to, jak je vysvětleno dále v tomto článku.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Přehled činností dostupných v kategorii B2C auditních protokolů

Kategorie **B2C** v protokolech auditu obsahuje následující typy činností:

|Typ aktivity |Popis  |
|---------|---------|
|Autorizace |Aktivity týkající se autorizace uživatele pro přístup k prostředkům B2C (například správce, který přistupuje k seznamu zásad B2C).         |
|Adresář |Aktivity související s atributy adresáře načtené, když se správce přihlásí pomocí portálu Azure. |
|Aplikace | Vytvářejte, čtěte, aktualizujte a odstraňujte operace (CRUD) v aplikacích B2C. |
|Klíč |CRUD operace na klíče uložené v kontejneru klíčů B2C. |
|Prostředek |CRUD operace na b2c prostředky. Například zásady a zprostředkovatelé identity.
|Ověřování |Ověření pověření uživatele a vystavování tokenů.|

Informace o aktivitách crud objektu uživatele naleznete v kategorii **Základní adresář.**

## <a name="example-activity"></a>Příklad aktivity

Tento příklad obrázku z portálu Azure zobrazuje data zachycená při přihlášení uživatele pomocí externího poskytovatele identity, v tomto případě Facebook:

![Příklad stránky Podrobnosti o aktivitě protokolu auditu na webu Azure Portal](./media/view-audit-logs/audit-logs-example.png)

Panel podrobností o činnosti obsahuje tyto relevantní informace:

|Sekce|Pole|Popis|
|-------|-----|-----------|
| Aktivita | Name (Název) | Která činnost se uskutečnila. Například *vydat id_token do aplikace*, která uzavírá skutečné přihlášení uživatele. |
| Iniciováno (herec) | ObjectId | **ID objektu** aplikace B2C, ke které se uživatel přihlašuje. Tento identifikátor není viditelný na webu Azure Portal, ale je přístupný prostřednictvím rozhraní Microsoft Graph API. |
| Iniciováno (herec) | Spn | **ID aplikace** aplikace B2C, ke které se uživatel přihlašuje. |
| Cíle | ObjectId | **ID objektu** uživatele, který se přihlašuje. |
| Další podrobnosti | TenantId | **ID klienta** klienta klienta Azure AD B2C. |
| Další podrobnosti | PolicyId | **ID zásad** toku uživatele (zásady) se používá k přihlášení uživatele. |
| Další podrobnosti | ApplicationId | **ID aplikace** aplikace B2C, ke které se uživatel přihlašuje. |

## <a name="view-audit-logs-in-the-azure-portal"></a>Zobrazení protokolů auditu na webu Azure Portal

Portál Azure poskytuje přístup k událostem protokolu auditu ve vašem tenantovi Azure AD B2C.

1. Přihlášení k [portálu Azure](https://portal.azure.com)
1. Přepněte do adresáře, který obsahuje vašeho klienta Azure AD B2C, a pak přejděte na **Azure AD B2C**.
1. V části **Aktivity** v levé nabídce vyberte **protokoly auditování**.

Zobrazí se seznam událostí aktivit zaznamenaných za posledních sedm dní.

![Ukázkový filtr se dvěma událostmi aktivit y na Webu Azure Portal](./media/view-audit-logs/audit-logs-example-filter.png)

K dispozici je několik možností filtrování, například:

* **Typ prostředku aktivity** – filtrujte podle typů aktivit zobrazených v tabulce v části [Přehled dostupných aktivit.](#overview-of-activities-available-in-the-b2c-category-of-audit-logs)
* **Datum** - Filtrovat rozsah dat zobrazených aktivit.

Pokud v seznamu vyberete řádek, zobrazí se podrobnosti o aktivitě události.

Chcete-li stáhnout seznam událostí aktivity v souboru hodnot oddělených čárkami (CSV), vyberte **možnost Stáhnout**.

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>Získání protokolů auditování pomocí rozhraní API pro vytváření sestav Azure AD

Protokoly auditu se publikují do stejného kanálu jako ostatní aktivity pro Službu Azure Active Directory, takže k nim lze přistupovat prostřednictvím [rozhraní Azure Active Directory reporting API](https://docs.microsoft.com/graph/api/directoryaudit-list). Další informace najdete [v tématu Začínáme s rozhraním API pro vytváření sestav služby Azure Active Directory](../active-directory/reports-monitoring/concept-reporting-api.md).

### <a name="enable-reporting-api-access"></a>Povolení přístupu k rozhraní API pro vytváření sestav

Chcete-li povolit přístup k rozhraní API pro vytváření sestav Azure AD nebo na základě aplikací, potřebujete aplikaci registrovanou ve vašem tenantovi Azure AD B2C s následujícími oprávněními rozhraní API. Tato oprávnění můžete povolit pro existující registraci aplikace v rámci klienta B2C nebo vytvořit novou, která se používá speciálně pro použití s automatizací protokolu auditování.

* Oprávnění aplikací > microsoft graph u > AuditLog > AuditLog.Read.All

Podle pokynů v následujícím článku zaregistrujte aplikaci s požadovanými oprávněními:

[Správa Azure AD B2C pomocí Microsoft Graphu](microsoft-graph-get-started.md)

Po registraci aplikace s příslušnými oprávněními najdete v části skript prostředí PowerShell dále v tomto článku příklad, jak můžete získat události aktivity se skriptem.

### <a name="access-the-api"></a>Přístup k rozhraní API

Chcete-li stáhnout události protokolu auditu Azure AD B2C `B2C` prostřednictvím rozhraní API, filtrujte protokoly v kategorii. Chcete-li filtrovat podle `filter` kategorie, použijte parametr řetězce dotazu při volání koncového bodu rozhraní API pro vytváření sestav Azure AD.

```HTTP
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>Skript PowerShellu

Následující skript Prostředí PowerShell ukazuje příklad, jak se dotazovat rozhraní API pro vytváření sestav Azure AD. Po dotazování rozhraní API vytiskne zaznamenané události na standardní výstup a pak zapíše výstup JSON do souboru.

Tento skript můžete vyzkoušet v [prostředí Azure Cloud Shell](overview.md). Nezapomeňte ji aktualizovat pomocí ID aplikace, tajného klíče klienta a názvu klienta Azure AD B2C.

```powershell
# This script requires an application registration that's granted Microsoft Graph API permission
# https://docs.microsoft.com/azure/active-directory-b2c/microsoft-graph-get-started

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's client ID, a GUID
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's client secret
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # Insert your Azure AD B2C tenant domain name

$loginURL       = "https://login.microsoftonline.com"
$resource       = "https://graph.microsoft.com"           # Microsoft Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 through n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = "https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?`$filter=loggedByService eq 'B2C' and activityDateTime gt  " + $7daysago

    # loop through each query page (1 through n)
    Do {
        # display each event on the console window
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output ($event | ConvertTo-Json)
        }

        # save the query page to an output file
        Write-Output "Save the output to a file audit$i.json"
        $myReport.Content | Out-File -FilePath audit$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)
} else {
    Write-Host "ERROR: No Access Token"
}
```

Zde je json reprezentace ukázkové události aktivity uvedené dříve v článku:

```JSON
{
    "id": "B2C_DQO3J_4984536",
    "category": "Authentication",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "result": "success",
    "resultReason": "N/A",
    "activityDisplayName": "Issue an id_token to the application",
    "activityDateTime": "2019-09-14T18:13:17.0618117Z",
    "loggedByService": "B2C",
    "operationType": "",
    "initiatedBy": {
        "user": null,
        "app": {
            "appId": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "servicePrincipalId": null,
            "servicePrincipalName": "00000000-0000-0000-0000-000000000000"
        }
    },
    "targetResources": [
        {
            "id": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "type": "User",
            "userPrincipalName": null,
            "groupType": null,
            "modifiedProperties": []
        }
    ],
    "additionalDetails": [
        {
            "key": "TenantId",
            "value": "test.onmicrosoft.com"
        },
        {
            "key": "PolicyId",
            "value": "B2C_1A_signup_signin"
        },
        {
            "key": "ApplicationId",
            "value": "00000000-0000-0000-0000-000000000000"
        },
        {
            "key": "Client",
            "value": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36"
        },
        {
            "key": "IdentityProviderName",
            "value": "facebook"
        },
        {
            "key": "IdentityProviderApplicationId",
            "value": "0000000000000000"
        },
        {
            "key": "ClientIpAddress",
            "value": "127.0.0.1"
        }
    ]
}
```

## <a name="next-steps"></a>Další kroky

Můžete automatizovat další úlohy správy, například [spravovat uživatelské účty Azure AD B2C pomocí Microsoft Graphu](manage-user-accounts-graph-api.md).
