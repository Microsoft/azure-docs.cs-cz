---
title: Ukázky rozhraní API pro audit generování sestav Azure Active Directory | Microsoft Docs
description: Jak začít s rozhraním API pro generování sestav Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: de8b8ec3-49b3-4aa8-93fb-e38f52c99743
ms.service: active-directory
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/30/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 240095b64f4b7b37a71f84ce8d7a3521e0ab7c03
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36223881"
---
# <a name="azure-active-directory-reporting-audit-api-samples"></a>Ukázky rozhraní API pro audit generování sestav Azure Active Directory
Tento článek je součástí kolekce článků o rozhraní API pro generování sestav Azure Active Directory.  
Generování sestav Azure AD poskytuje rozhraní API, které umožňuje přistupovat k datům auditu pomocí kódu nebo souvisejících nástrojů.
Účelem tohoto článku je poskytnout vám vzorový kód pro **rozhraní API pro audit**.

Přečtěte si:

* Další koncepční informace v tématu věnovaném [protokolům auditu](active-directory-reporting-azure-portal.md#activity-reports).
* Další informace o rozhraní API pro generování sestav v tématu [Začínáme s rozhraním API pro generování sestav Azure Active Directory](active-directory-reporting-api-getting-started.md).

Pokud máte otázky, problémy nebo připomínky, kontaktujte [podporu ke generování sestav AAD](mailto:aadreportinghelp@microsoft.com).


## <a name="prerequisites"></a>Požadavky
Než budete moct použít ukázky v tomto článku, musíte dokončit [požadavky pro přístup k rozhraní API pro generování sestav Azure AD](active-directory-reporting-api-prerequisites.md).  

## <a name="known-issue"></a>Známý problém
Ověřování aplikace nebude fungovat, pokud je váš tenant v oblasti Evropa. Dokud tento problém nevyřešíme, jako alternativní řešení použijte pro přístup k rozhraní API pro audit ověřování uživatelů. 

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

### <a name="executing-the-powershell-script"></a>Spuštění skriptu PowerShellu
Jakmile dokončíte úpravy skriptu, spusťte ho a ověřte, že se ze sestavy protokolů auditu vracejí očekávaná data.

Skript vrátí výstup ze sestavy auditu ve formátu JSON. Vytvoří také soubor `Audits.json` se stejným výstupem. Můžete experimentovat tím, že skript upravíte, aby vracel data z jiných sestav, a okomentujete formáty výstupu, které nepotřebujete.





## <a name="next-steps"></a>Další kroky
* Chcete si přizpůsobit ukázky v tomto článku? Přečtěte si [referenční informace k rozhraní API pro audit Azure Active Directory](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit). 
* Úplný přehled použití rozhraní API pro generování sestav Azure Active Directory najdete v tématu [Začínáme s rozhraním API pro generování sestav Azure Active Directory](active-directory-reporting-api-getting-started.md).
* Další informace o generování sestav Azure Active Directory najdete v tématu [Průvodce generováním sestav Azure Active Directory](active-directory-reporting-guide.md).  

