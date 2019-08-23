---
title: Ukázky a definice protokolů auditu v Azure Active Directory B2C | Microsoft Docs
description: Průvodce a ukázky pro přístup k protokolům auditu Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: d8cc67b8e243fb2b97cd1522a850adc63c84428e
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69969634"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Přístup k protokolům auditu Azure AD B2C

Azure Active Directory B2C (Azure AD B2C) emituje protokoly auditu obsahující informace o aktivitách prostředků B2C, vydaných tokenů a přístupu správce. Tento článek poskytuje stručný přehled informací, které jsou k dispozici prostřednictvím protokolů auditu a pokyny k přístupu k těmto datům pro vašeho tenanta Azure AD B2C.

> [!IMPORTANT]
> Protokoly auditu se uchovávají jenom po dobu sedmi dnů. Naplánujte stažení a uložení protokolů pomocí jedné z níže uvedených metod, pokud požadujete delší dobu uchování.

> [!NOTE]
> V části **Uživatelé** okna **Azure Active Directory** nebo **Azure AD B2C** nelze zobrazit přihlášení uživatelů pro jednotlivé Azure AD B2C aplikace. V přihlašování se zobrazí aktivita uživatele, ale nedá se korelace vrátit k aplikaci B2C, ke které se uživatel přihlásil. K tomu je nutné použít protokoly auditu, jak je vysvětleno dále v tomto článku.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Přehled aktivit dostupných v kategorii B2C protokolů auditu
Kategorie **B2C** v protokolech auditu obsahuje následující typy aktivit:

|Typ aktivity |Popis  |
|---------|---------|
|Authorization |Aktivity týkající se autorizace uživatele pro přístup k prostředkům B2C (například správce, který přistupuje k seznamu zásad B2C)         |
|Adresář |Aktivity týkající se atributů adresáře načtené, když se správce přihlásí pomocí Azure Portal |
|Aplikace | Operace CRUD v aplikacích B2C |
|Klíč |Operace CRUD na klíčích uložených v kontejneru klíčů B2C |
|Resource |Operace CRUD na B2Cch prostředcích (například zásady a zprostředkovatelé identity)
|Ověřování |Ověření přihlašovacích údajů uživatele a vystavení tokenu|

> [!NOTE]
> Pro aktivity objektů CRUD uživatele se podívejte do kategorie **základní adresář** .

## <a name="example-activity"></a>Ukázková aktivita
Následující příklad zobrazuje data zachycená, když se uživatel přihlásí pomocí externího zprostředkovatele identity:  
    ![Příklad stránky s podrobnostmi o aktivitě protokolu auditu v Azure Portal](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

Panel Podrobnosti o aktivitě obsahuje následující relevantní informace:

|Section|Pole|Popis|
|-------|-----|-----------|
| Aktivita | Name | Která aktivita trvala. Například "vydejte do aplikace id_token" (který uzavře vlastní přihlášení uživatele). |
| Iniciované uživatelem (actor) | ObjectId | **ID objektu** aplikace B2C, ke které se uživatel přihlašuje (Tento identifikátor není viditelný v Azure Portal, ale je přístupný prostřednictvím Graph API například). |
| Iniciované uživatelem (actor) | SPN | **ID aplikace** B2C, ke které se uživatel přihlašuje |
| Cíl (y) | ObjectId | **ID objektu** uživatele, který se přihlašuje. |
| Další podrobnosti | TenantId | **ID tenanta** klienta Azure AD B2C. |
| Další podrobnosti | `PolicyId` | **ID zásady** toku uživatele (zásady), která se používá k podepsání uživatele v. |
| Další podrobnosti | ApplicationId | **ID aplikace** B2C, ke které se uživatel přihlašuje |

## <a name="accessing-audit-logs-through-the-azure-portal"></a>Přístup k protokolům auditu prostřednictvím Azure Portal
1. Přejděte na [Azure Portal](https://portal.azure.com). Ujistěte se, že jste v adresáři B2C.
2. Na panelu Oblíbené položky vlevo klikněte na **Azure Active Directory** .

    ![Zvýrazněné tlačítko Azure Active Directory v nabídce na levé straně portálu](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-aad.png)

1. V části **aktivita**klikněte na **protokoly auditu** .

    ![Tlačítko protokoly auditu zvýrazněné v části aktivita v nabídce](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-section.png)

2. V části Dropbox **kategorie** vyberte **B2C**
3. Klikněte na **použít** .

    ![Tlačítko kategorie a použití zvýrazněné ve filtru protokolu auditu](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-category.png)

Zobrazí se seznam aktivit zaznamenaných za posledních sedm dnů.
- Pomocí rozevíracího seznamu **typ prostředku aktivity** můžete filtrovat podle typů aktivit uvedených výše.
- Pomocí rozevíracího seznamu **Rozsah kalendářních** dat můžete filtrovat rozsah kalendářních dat zobrazených aktivit.
- Pokud kliknete na konkrétní řádek v seznamu, zobrazí se v pravém kontextovém poli Další atributy přidružené k aktivitě.
- Kliknutím na **Stáhnout** Stáhněte aktivity jako soubor CSV.

> [!NOTE]
> Protokoly auditu můžete zobrazit také tak, že přejdete na **Azure AD B2C** místo **Azure Active Directory** na panelu Oblíbené položky na levé straně. V části **aktivity**klikněte na **protokoly auditu**, kde se nacházejí stejné protokoly s podobnými možnostmi filtrování.

## <a name="accessing-audit-logs-through-the-azure-ad-reporting-api"></a>Přístup k protokolům auditu prostřednictvím rozhraní API pro vytváření sestav Azure AD
Protokoly auditu se publikují do stejného kanálu jako jiné aktivity pro Azure Active Directory, takže se dají dostat prostřednictvím [rozhraní API pro vytváření sestav Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference).

### <a name="prerequisites"></a>Požadavky
Abyste mohli ověřit rozhraní API pro vytváření sestav Azure AD, musíte nejdřív zaregistrovat aplikaci. Nezapomeňte postupovat podle kroků v části [požadavky pro přístup k rozhraním API pro generování sestav Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/).

### <a name="accessing-the-api"></a>Přístup k rozhraní API
Chcete-li stáhnout protokoly auditu Azure AD B2C přes rozhraní API, budete chtít protokoly filtrovat do kategorie **B2C** . Pokud chcete filtrovat podle kategorie, použijte parametr řetězce dotazu při volání koncového bodu rozhraní API pro vytváření sestav Azure AD, jak je znázorněno níže:

`https://graph.windows.net/your-b2c-tentant.onmicrosoft.com/activities/audit?api-version=beta&$filter=category eq 'B2C'`

### <a name="powershell-script"></a>Skript PowerShellu
Následující skript nabízí příklad použití PowerShellu k dotazování rozhraní API pro vytváření sestav Azure AD a uložení výsledků jako souboru JSON:

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
