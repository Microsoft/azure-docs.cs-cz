---
title: Ukázek Azure sestavy API přihlašovací aktivita služby Active Directory | Microsoft Docs
description: Jak začít pracovat s Azure Active Directory Reporting API
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: c41c1489-726b-4d3f-81d6-83beb932df9c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/31/2018
ms.author: dhanyahk;rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: 466755d7d1cc7fbf4006826ac849b74ba306bae9
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34698573"
---
# <a name="azure-active-directory-sign-in-activity-report-api-samples"></a>Ukázek Azure sestavy API přihlašovací aktivita služby Active Directory
Tento článek je součástí kolekce článků o službě Azure Active Directory, vytváření sestav rozhraní API.  
Generování sestav služby Azure AD poskytuje rozhraní API, která umožňuje přístup k datům přihlašovací aktivita pomocí kódu nebo související nástroje.  
Rámec tohoto článku je poskytnout ukázkový kód pro **aktivity API přihlášení**.

Přečtěte si:

* [Protokoly auditu](active-directory-reporting-azure-portal.md#activity-reports) další koncepční informace
* [Začínáme s Azure Active Directory Reporting API](active-directory-reporting-api-getting-started.md) Další informace o rozhraní API pro generování sestav.


## <a name="prerequisites"></a>Požadavky
Před použitím ukázky v tomto článku, je potřeba provést [požadavky pro přístup k Azure AD reporting rozhraní API](active-directory-reporting-api-prerequisites.md).  

## <a name="powershell-script"></a>Skript PowerShellu

```powershell

# This script will require the Web Application and permissions setup in Azure Active Directory
$clientID       = "<appid>"             # ApplicationId
$clientSecret   = "<key>"         # Should be a ~44 character string insert your info here
$loginURL       = "https://login.windows.net/"
$tenantdomain   = "<domain>"            # For example, contoso.onmicrosoft.com
$msgraphEndpoint = "https://graph.microsoft.com"
$countOfSignInDocsToBeSavedInAFile = 2000
    
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$msgraphEndpoint;client_id=$clientID;client_secret=$clientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
    
if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    
    $url = "$msgraphEndpoint/beta/auditLogs/signIns"
    Write-Output "Fetching data using Uri: $url"
    $i=0
    $docCount=0
    Do{
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        $jsonReport = ($myReport.Content | ConvertFrom-Json).value
        $fetchedRecordCount = $jsonReport.Count
        $docCount = $docCount + $fetchedRecordCount
        $totalFetchedRecordCount = $totalFetchedRecordCount + $fetchedRecordCount
        Write-Output "Fetched $fetchedRecordCount records and saved into SignIns$i.json"
        if($docCount -le $countOfSignInDocsToBeSavedInAFile)
        {
            $myReport.Content | Out-File -FilePath SignIns$i.json -append  -Force       
        }
        else
        {           
            $docCount=0
            $i = $i+1
        }
            
        #Get url from next link
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'         
    }while($url -ne $null)
    Write-Output "Total Fetched record count is : $totalFetchedRecordCount"
                
} else {
    Write-Host "ERROR: No Access Token"
}

```




## <a name="executing-the-script"></a>Provádění skriptu
Se vrátí po ukončení úprav skript, spouštět a ověřte, že očekávaná data z přihlášení protokoluje sestavy.

Skript vrátí výstupní ze sestavy přihlášení ve formátu JSON. Vytvoří také `SignIns.json` soubor s stejný výstup. Můžete experimentovat změnou skript, který chcete vrátit data z jiných sestavy a komentář výstupní formáty, které nepotřebujete.

## <a name="next-steps"></a>Další kroky
* Chcete přizpůsobit ukázky v tomto článku? Podívejte se [referenční dokumentace rozhraní API služby Azure Active Directory přihlašovací aktivita](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin). 
* Pokud chcete zobrazit úplný přehled pomocí Azure Active Directory, vytváření sestav rozhraní API, najdete v části [Začínáme s Azure Active Directory, vytváření sestav rozhraní API](active-directory-reporting-api-getting-started.md).
* Pokud chcete získat další informace o vytváření sestav Azure Active Directory, přečtěte si téma [Azure Active Directory průvodce vytvářením sestav](active-directory-reporting-guide.md).  

