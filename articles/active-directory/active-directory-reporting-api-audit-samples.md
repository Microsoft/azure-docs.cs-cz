---
title: Generování sestav Azure Active Directory audit ukázky rozhraní API | Microsoft Docs
description: Jak začít pracovat s Azure Active Directory Reporting API
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: de8b8ec3-49b3-4aa8-93fb-e38f52c99743
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/30/2018
ms.author: dhanyahk;rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: 77ecb1f0c4b3614c9692715edae21a09f261b277
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34588160"
---
# <a name="azure-active-directory-reporting-audit-api-samples"></a>Azure Active Directory, vytváření sestav ukázky auditu rozhraní API
Tento článek je součástí kolekce článků o službě Azure Active Directory, vytváření sestav rozhraní API.  
Generování sestav služby Azure AD poskytuje rozhraní API, která umožňuje přístup k datům auditování pomocí kódu nebo související nástroje.
Rámec tohoto článku je poskytnout ukázkový kód pro **audit rozhraní API**.

Přečtěte si:

* [Protokoly auditu](active-directory-reporting-azure-portal.md#activity-reports) další koncepční informace
* [Začínáme s Azure Active Directory Reporting API](active-directory-reporting-api-getting-started.md) Další informace o rozhraní API pro generování sestav.

Pro dotazy, problémy nebo připomínky, obraťte se na [AAD Reporting pomoci](mailto:aadreportinghelp@microsoft.com).


## <a name="prerequisites"></a>Požadavky
Před použitím ukázky v tomto článku, je potřeba provést [požadavky pro přístup k Azure AD reporting rozhraní API](active-directory-reporting-api-prerequisites.md).  

## <a name="known-issue"></a>Známý problém
Pokud váš klient je v oblasti, Evropa, ověřování aplikace nebude fungovat. Použijte ověřování uživatele pro přístup k rozhraní API auditu jako alternativní řešení, dokud jsme problém opravte. 

## <a name="powershell-script"></a>Skript PowerShellu


```powershell

# This script will require the Web Application and permissions setup in Azure Active Directory
$clientID       = "<appid>"             # ApplicationId
$clientSecret   = "<key>"         # Should be a ~44 character string insert your info here
$loginURL       = "https://login.windows.net/"
$tenantdomain   = "<domain>"            # For example, contoso.onmicrosoft.com
$msgraphEndpoint = "https://graph.microsoft.com"
$countOfAuditDocsToBeSavedInAFile = 2000
    
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$msgraphEndpoint;client_id=$clientID;client_secret=$clientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
    
if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    
    $url = "$msgraphEndpoint/beta/auditLogs/directoryAudits"
    Write-Output "Fetching data using Uri: $url"
    $i=0
    $docCount=0
    Do{
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        $jsonReport = ($myReport.Content | ConvertFrom-Json).value
        $fetchedRecordCount = $jsonReport.Count
        $docCount = $docCount + $fetchedRecordCount
        $totalFetchedRecordCount = $totalFetchedRecordCount + $fetchedRecordCount
        Write-Output "Fetched $fetchedRecordCount records and saved into Audits$i.json"
        if($docCount -le $countOfAuditDocsToBeSavedInAFile)
        {
            $myReport.Content | Out-File -FilePath Audits$i.json -append  -Force        
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

### <a name="executing-the-powershell-script"></a>Provádění skriptu prostředí PowerShell
Se vrátí po ukončení úprav skript, spouštět a ověřte, že očekávaná data z auditu protokoluje sestavy.

Skript vrátí výstupní ze sestavy auditu ve formátu JSON. Vytvoří také `Audits.json` soubor s stejný výstup. Můžete experimentovat změnou skript, který chcete vrátit data z jiných sestavy a komentář výstupní formáty, které nepotřebujete.





## <a name="next-steps"></a>Další postup
* Chcete přizpůsobit ukázky v tomto článku? Podívejte se [auditování Azure Active Directory referenční dokumentace rozhraní API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit). 
* Pokud chcete zobrazit úplný přehled pomocí Azure Active Directory, vytváření sestav rozhraní API, najdete v části [Začínáme s Azure Active Directory, vytváření sestav rozhraní API](active-directory-reporting-api-getting-started.md).
* Pokud chcete získat další informace o vytváření sestav Azure Active Directory, přečtěte si téma [Azure Active Directory průvodce vytvářením sestav](active-directory-reporting-guide.md).  

