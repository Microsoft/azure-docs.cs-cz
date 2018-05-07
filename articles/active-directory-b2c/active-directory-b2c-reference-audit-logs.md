---
title: 'Azure Active Directory B2C: Ukázky a definice protokoly auditu'
description: Průvodce a ukázky v přístupu k protokolů auditu Azure AD B2C
services: active-directory-b2c
author: sromeroz
manager: sasubram
ms.author: sezambra
ms.service: active-directory-b2c
ms.topic: article
ms.workload: identity
ms.date: 08/04/2017
ms.openlocfilehash: 8cc48853b0677230c4e19df4f0ecd93ce88b119d
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Přístup k Azure AD B2C protokoly auditu

Azure Active Directory B2C (Azure AD B2C) vysílá protokoly auditu obsahující aktivitu informací o prostředcích B2C, vystavené tokeny a přístup správce. Tento článek poskytuje stručný přehled informací dostupných prostřednictvím protokolů auditu a pokyny o tom, jak přístup k těmto datům pro vašeho tenanta Azure AD B2C.

> [!IMPORTANT]
> Protokoly auditu jsou uchovány pouze sedm dní. Naplánujte ke stažení a uložení protokolů pomocí jedné z metod vidíte níže, pokud požadujete delší dobu uchování. 

##<a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Přehled aktivit, které jsou k dispozici v kategorii B2C protokoly auditu
**B2C** kategorie v protokolech auditu obsahuje následující typy aktivit:
|Typ aktivity |Popis  |
|---------|---------|
|Autorizace |Činnosti týkající se povolení uživatele pro přístup k B2C prostředkům (například správce přístupu k zobrazení seznamu zásad B2C)         |
|Adresář |Aktivity související s atributy adresáře načítají, když se správce přihlásí pomocí portálu Azure |
|Aplikace | Operace CRUD v aplikacích B2C |
|Klíč |Operace CRUD v klíče uložené v kontejneru klíčů B2C |
|Prostředek |Operace CRUD v B2C prostředky (například zásady a zprostředkovatelů identity)
|Authentication |Ověření pověření uživatele a vystavování tokenů|

> [!NOTE]
> Aktivity CRUD objekt uživatele, najdete v části **základní Directory** kategorie.

##<a name="example-activity"></a>Ukázkové aktivity
Následující příklad ukazuje data zaznamenaná Pokud se uživatel přihlásí pomocí zprostředkovatele identity externí: ![protokoly auditu – příklad](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

##<a name="accessing-audit-logs-through-the-azure-portal"></a>Přístup k protokoly auditu prostřednictvím portálu Azure
1. Přejděte na [portál Azure](https://portal.azure.com). Ujistěte se, že jste ve svém adresáři B2C.
2. Klikněte na **Azure Active Directory** na panelu oblíbených položek na levé straně 
    
    ![Protokoly auditu – tlačítko AAD](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-aad.png)

1. V části **aktivity**, klikněte na **protokoly auditu**

    ![Protokoly auditu - části protokoly](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-section.png)

2. V **kategorie** dropbox, vyberte **B2C**
3. Klikněte na **použít**

    ![Protokoly auditu - kategorie](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-category.png)

Zobrazí se seznam aktivity zaprotokolovány za posledních 7 dní. 
- Použití **typ prostředku aktivity** rozevírací filtrovat podle typy aktivit uvedených výše
- Použití **rozsah** rozevírací vyfiltrujete rozsah kalendářních dat aktivity zobrazit
- Pokud kliknete na konkrétní řádek v seznamu, pole kontextové na pravé straně vám ukáže další atributy, které jsou přidružené k aktivitě
- Klikněte na **Stáhnout** ke stažení aktivity do souboru csv

##<a name="accessing-audit-logs-through-the-azure-ad-reporting-api"></a>Přístup k protokoly auditu prostřednictvím generování sestav rozhraní API Azure AD
Protokoly auditu jsou publikovány do stejné kanálu jako ostatní aktivity pro Azure Active Directory, takže je přístupná prostřednictvím [Azure Active Directory, vytváření sestav rozhraní API](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-api-audit-reference). 

###<a name="prerequisites"></a>Požadavky
K ověření do služby Azure AD reporting rozhraní API musíte nejprve zaregistrovat aplikaci. Ujistěte se, postupujte podle kroků v [požadavky pro přístup k rozhraní API pro vytváření sestav Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/).

###<a name="accesing-the-api"></a>Accesing rozhraní API
Ke stažení protokolů auditu Azure AD B2C prostřednictvím rozhraní API, budete chtít filtrování protokolů **B2C** kategorie. Filtrovat podle kategorie, použijte parametr řetězce dotazu při volání metody generování sestav koncový bod rozhraní API, Azure AD, jak je uvedeno níže:

`https://graph.windows.net/your-b2c-tentant.onmicrosoft.com/activities/audit?api-version=beta&$filter=category eq 'B2C'`

###<a name="powershell-script"></a>Skript PowerShellu
Následující skript představuje příklad použití prostředí PowerShell pro dotaz na generování sestav rozhraní API Azure AD a výsledek uložit jako soubor JSON:

```powershell
# This script will require registration of a Web Application in Azure Active Directory (see https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a Globally Unique ID (registered by Global Admin)
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client Key/Secret string
$loginURL       = "https://login.microsoftonline.com"     
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # AAD B2C Tenant; for example, contoso.onmicrosoft.com
$resource       = "https://graph.windows.net"             # Azure AD Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 thru n for number of nextLink pages
if ($oauth.access_token -ne $null) {   
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = 'https://graph.windows.net/' + $tenantdomain + '/activities/audit?api-version=beta&$filter=category eq ''B2C''and activityDate gt ' + $7daysago 

    # loop through each query page (1 through n)
    Do{
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

