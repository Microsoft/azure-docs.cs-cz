---
title: Ukázky a definice protokolů auditu v Azure Active Directory B2C
description: Průvodce a ukázky pro přístup k protokolům auditu Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 09/14/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: bf9b6a3ad40d46b628bfcdb3fa3e32b2419360c9
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802115"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Přístup k protokolům auditu Azure AD B2C

Azure Active Directory B2C (Azure AD B2C) emituje protokoly auditu obsahující informace o aktivitách o prostředcích B2C, vydaných tokenech a přístupu správce. Tento článek poskytuje stručný přehled informací, které jsou k dispozici v protokolech auditu, a pokyny k přístupu k těmto datům pro vašeho tenanta Azure AD B2C.

Události protokolu auditu se uchovávají jenom po dobu **sedmi dnů**. Naplánujte stažení a uložení protokolů pomocí jedné z níže uvedených metod, pokud požadujete delší dobu uchování.

> [!NOTE]
> V části **uživatelé** **Azure Active Directory** nebo **Azure AD B2C** stránky v Azure Portal neuvidíte přihlášení uživatelů pro jednotlivé Azure AD B2C aplikace. Události přihlašování se zobrazují v aktivitě uživatele, ale nelze je korelovat zpět do aplikace B2C, ke které se uživatel přihlásil. K tomu je nutné použít protokoly auditu, jak je vysvětleno dále v tomto článku.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Přehled aktivit dostupných v kategorii B2C protokolů auditu

Kategorie **B2C** v protokolech auditu obsahuje následující typy aktivit:

|Typ aktivity |Popis  |
|---------|---------|
|Autorizace |Aktivity týkající se autorizace uživatele pro přístup k prostředkům B2C (například správce, který přistupuje k seznamu zásad B2C).         |
|Adresář |Aktivity týkající se atributů adresáře načtené, když se správce přihlásí pomocí Azure Portal. |
|Aplikace | Operace vytvoření, čtení, aktualizace a odstranění (CRUD) v aplikacích B2C. |
|Klíč |Operace CRUD na klíčích uložených v kontejneru klíčů B2C |
|Prostředek |Operace CRUD u prostředků B2C Například zásady a zprostředkovatelé identity.
|Ověření |Ověření přihlašovacích údajů uživatele a vystavení tokenu|

Pro aktivity objektů CRUD uživatele se podívejte do kategorie **základní adresář** .

## <a name="example-activity"></a>Ukázková aktivita

Tento příklad obrázku z Azure Portal zobrazuje data zachycená, když se uživatel přihlásí pomocí externího zprostředkovatele identity, v tomto případě Facebook:

![Příklad stránky s podrobnostmi o aktivitě protokolu auditu v Azure Portal](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

Panel Podrobnosti o aktivitě obsahuje následující relevantní informace:

|Sekce|Pole|Popis|
|-------|-----|-----------|
| Aktivita | Name (Název) | Která aktivita trvala. Například *vydejte id_token aplikaci*, která uzavře vlastní přihlášení uživatele. |
| Iniciované uživatelem (actor) | Objektu | **ID objektu** aplikace B2C, ke které se uživatel přihlašuje Tento identifikátor není viditelný v Azure Portal, ale je přístupný prostřednictvím rozhraní Microsoft Graph API. |
| Iniciované uživatelem (actor) | SPN | **ID aplikace** B2C, ke které se uživatel přihlašuje |
| Cíl (y) | Objektu | **ID objektu** uživatele, který se přihlašuje. |
| Další podrobnosti | TenantId | **ID tenanta** klienta Azure AD B2C. |
| Další podrobnosti | PolicyId | **ID zásady** toku uživatele (zásady), která se používá k podepsání uživatele v. |
| Další podrobnosti | ApplicationId | **ID aplikace** B2C, ke které se uživatel přihlašuje |

## <a name="view-audit-logs-in-the-azure-portal"></a>Zobrazit protokoly auditu v Azure Portal

Azure Portal poskytuje přístup k událostem protokolu auditu ve vašem tenantovi Azure AD B2C.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. Přejděte do adresáře, který obsahuje vašeho tenanta Azure AD B2C, a pak přejděte do **Azure AD B2C**.
1. V části **aktivity** v nabídce vlevo vyberte **protokoly auditu**.

Zobrazí se seznam událostí aktivit zaznamenaných za posledních sedm dní.

![Příklad filtru se dvěma událostmi aktivit v Azure Portal](media/active-directory-b2c-reference-audit-logs/audit-logs-example-filter.png)

K dispozici je několik možností filtrování, včetně:

* **Typ prostředku aktivity** – filtr podle typů aktivit zobrazených v tabulce v části [Přehled dostupných aktivit](#overview-of-activities-available-in-the-b2c-category-of-audit-logs)
* **Datum** – filtruje rozsah kalendářních dat zobrazených aktivit.

Pokud vyberete řádek v seznamu, zobrazí se podrobnosti o aktivitě události.

Chcete-li stáhnout seznam událostí aktivit v souboru hodnot oddělených čárkami (CSV), vyberte možnost **Stáhnout**.

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>Získání protokolů auditu pomocí rozhraní API pro vytváření sestav Azure AD

Protokoly auditu se publikují do stejného kanálu jako jiné aktivity pro Azure Active Directory, takže se dají dostat prostřednictvím [rozhraní API pro vytváření sestav Azure Active Directory](https://docs.microsoft.com/graph/api/directoryaudit-list). Další informace najdete v tématu [Začínáme s rozhraním API pro vytváření sestav Azure Active Directory](../active-directory/reports-monitoring/concept-reporting-api.md).

### <a name="enable-reporting-api-access"></a>Povolit přístup k rozhraní API pro vytváření sestav

Abyste povolili přístup k rozhraní API pro vytváření sestav Azure AD pomocí skriptu nebo aplikace, budete potřebovat aplikaci Azure Active Directory registrovanou v Azure AD B2C tenantovi s následujícími oprávněními API:

* Microsoft Graph
  * Aplikace: číst všechna data protokolu auditu

Tato oprávnění můžete povolit pro existující registraci aplikace Azure Active Directory v rámci tenanta B2C nebo vytvořit novou specifickou pro použití s automatizací protokolu auditu.

Postupujte podle těchto kroků, zaregistrujte aplikaci, udělte jí požadovaná oprávnění Microsoft Graph API a pak vytvořte tajný klíč klienta.

### <a name="register-application-in-azure-active-directory"></a>Registrovat aplikaci v Azure Active Directory

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="assign-api-access-permissions"></a>Přiřazení přístupových oprávnění k rozhraní API

1. Na stránce Přehled **zaregistrovaných aplikací** vyberte **Nastavení**.
1. V části **přístup přes rozhraní API**vyberte **požadovaná oprávnění**.
1. Vyberte **Přidat**a pak **Vyberte rozhraní API**.
1. Vyberte **Microsoft Graph**a pak **Vyberte**.
1. V části **oprávnění aplikace**vyberte **číst všechna data protokolu auditu**.
1. Vyberte tlačítko **Vybrat** a potom vyberte **Hotovo**.
1. Vyberte **udělit oprávnění**a pak vyberte **Ano**.

### <a name="create-client-secret"></a>Vytvořit tajný klíč klienta

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Nyní máte aplikaci s požadovaným přístupem k rozhraní API, ID aplikace a klíč, který můžete použít ve svých skriptech automatizace. Příklad toho, jak můžete získat události aktivity pomocí skriptu, najdete v části PowerShellový skript níže v tomto článku.

### <a name="access-the-api"></a>Přístup k rozhraní API

Pokud chcete stáhnout Azure AD B2C události protokolu auditu přes rozhraní API, vyfiltrujte protokoly v kategorii `B2C`. Pokud chcete filtrovat podle kategorie, použijte parametr řetězce dotazu `filter` při volání koncového bodu rozhraní API pro vytváření sestav Azure AD.

```HTTP
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>Skript PowerShellu

Následující skript prostředí PowerShell ukazuje příklad postupu dotazování rozhraní API pro vytváření sestav Azure AD. Po dotazování rozhraní API se události zaznamenávají do standardního výstupu a pak se zapíší výstup JSON do souboru.

Tento skript můžete vyzkoušet v [Azure Cloud Shell](../cloud-shell/overview.md). Nezapomeňte ho aktualizovat s ID aplikace, klíčem a názvem vašeho tenanta Azure AD B2C.

```powershell
# This script requires the registration of a Web Application in Azure Active Directory:
# https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-reporting-api

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a GUID (registered by Global Admin)
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client secret/key
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # Insert your Azure AD B2C tenant; for example, contoso.onmicrosoft.com
$loginURL       = "https://login.microsoftonline.com"
$resource       = "https://graph.microsoft.com"           # Microsoft Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 thru n for number of nextLink pages
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

Tady je reprezentace JSON ukázkové události aktivity uvedené dříve v článku:

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

Můžete automatizovat další úlohy správy, například [Spravovat uživatele pomocí .NET](active-directory-b2c-devquickstarts-graph-dotnet.md).
