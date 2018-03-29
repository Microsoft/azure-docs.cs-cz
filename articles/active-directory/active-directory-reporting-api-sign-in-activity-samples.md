---
title: Ukázek Azure sestavy API přihlašovací aktivita služby Active Directory | Microsoft Docs
description: Jak začít pracovat s Azure Active Directory Reporting API
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: c41c1489-726b-4d3f-81d6-83beb932df9c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: c76ac75acdb1645ee6cc5b496aadccbd1daf2d79
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="azure-active-directory-sign-in-activity-report-api-samples"></a>Ukázek Azure sestavy API přihlašovací aktivita služby Active Directory
Toto téma je součástí kolekce témat o službě Azure Active Directory, vytváření sestav rozhraní API.  
Generování sestav služby Azure AD poskytuje rozhraní API, která umožňuje přístup k datům přihlašovací aktivita pomocí kódu nebo související nástroje.  
Obor tohoto tématu je poskytnout ukázkový kód pro **aktivity API přihlášení**.

Přejděte na téma:

* [Protokoly auditu](active-directory-reporting-azure-portal.md#activity-reports) další koncepční informace
* [Začínáme s Azure Active Directory Reporting API](active-directory-reporting-api-getting-started.md) Další informace o rozhraní API pro generování sestav.


## <a name="prerequisites"></a>Požadavky
Před použitím ukázky v tomto tématu, které potřebujete k dokončení [požadavky pro přístup k Azure AD reporting rozhraní API](active-directory-reporting-api-prerequisites.md).  

## <a name="powershell-script"></a>Skript PowerShellu
    # This script will require the Web Application and permissions setup in Azure Active Directory
    $ClientID       = "<clientId>"             # Should be a ~35 character string insert your info here
    $ClientSecret   = "<clientSecret>"         # Should be a ~44 character string insert your info here
    $loginURL       = "https://login.microsoftonline.com/"
    $tenantdomain   = "<tenantDomain>"
    $daterange            # For example, contoso.onmicrosoft.com

    $7daysago = "{0:s}" -f (get-date).AddDays(-7) + "Z"
    # or, AddMinutes(-5)

    Write-Output $7daysago

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}

    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    $url = "https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&`$filter=signinDateTime ge $7daysago"

    $i=0

    Do{
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        Write-Output "Save the output to a file SigninActivities$i.json"
        Write-Output "---------------------------------------------"
        $myReport.Content | Out-File -FilePath SigninActivities$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)

    } else {

        Write-Host "ERROR: No Access Token"
    }




## <a name="executing-the-script"></a>Provádění skriptu
Se vrátí po ukončení úprav skript, spouštět a ověřte, že očekávaná data z auditu protokoluje sestavy.

Skript vrátí výstupní ze sestavy přihlášení ve formátu JSON. Vytvoří také `SigninActivities.json` soubor s stejný výstup. Můžete experimentovat změnou skript, který chcete vrátit data z jiných sestavy a komentář výstupní formáty, které nepotřebujete.

## <a name="next-steps"></a>Další kroky
* Chcete přizpůsobit ukázky v tomto tématu? Podívejte se [referenční dokumentace rozhraní API služby Azure Active Directory přihlašovací aktivita](active-directory-reporting-api-sign-in-activity-reference.md). 
* Pokud chcete zobrazit úplný přehled pomocí Azure Active Directory, vytváření sestav rozhraní API, najdete v části [Začínáme s Azure Active Directory, vytváření sestav rozhraní API](active-directory-reporting-api-getting-started.md).
* Pokud chcete získat další informace o vytváření sestav Azure Active Directory, přečtěte si téma [Azure Active Directory průvodce vytvářením sestav](active-directory-reporting-guide.md).  

