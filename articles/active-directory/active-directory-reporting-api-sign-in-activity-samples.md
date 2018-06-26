---
title: Ukázky rozhraní API pro sestavy aktivit přihlašování do Azure Active Directory | Microsoft Docs
description: Jak začít s rozhraním API pro generování sestav Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: c41c1489-726b-4d3f-81d6-83beb932df9c
ms.service: active-directory
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/31/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 52d49770014a5fb6a5eec644868e702c8a8d9ef3
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36224894"
---
# <a name="azure-active-directory-sign-in-activity-report-api-samples"></a>Ukázky rozhraní API pro sestavy aktivit přihlašování do Azure Active Directory
Tento článek je součástí kolekce článků o rozhraní API pro generování sestav Azure Active Directory.  
Generování sestav Azure AD poskytuje rozhraní API, které umožňuje přistupovat k datům aktivit přihlašování pomocí kódu nebo souvisejících nástrojů.  
Účelem tohoto článku je poskytnout vám vzorový kód pro **rozhraní API pro aktivity přihlašování**.

Přečtěte si:

* Další koncepční informace v tématu věnovaném [protokolům auditu](active-directory-reporting-azure-portal.md#activity-reports).
* Další informace o rozhraní API pro generování sestav v tématu [Začínáme s rozhraním API pro generování sestav Azure Active Directory](active-directory-reporting-api-getting-started.md).


## <a name="prerequisites"></a>Požadavky
Než budete moct použít ukázky v tomto článku, musíte dokončit [požadavky pro přístup k rozhraní API pro generování sestav Azure AD](active-directory-reporting-api-prerequisites.md).  

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




## <a name="executing-the-script"></a>Spuštění skriptu
Jakmile dokončíte úpravy skriptu, spusťte ho a ověřte, že se ze sestavy protokolů přihlašování vracejí očekávaná data.

Skript vrátí výstup ze sestavy přihlašování ve formátu JSON. Vytvoří také soubor `SignIns.json` se stejným výstupem. Můžete experimentovat tím, že skript upravíte, aby vracel data z jiných sestav, a okomentujete formáty výstupu, které nepotřebujete.

## <a name="next-steps"></a>Další kroky
* Chcete si přizpůsobit ukázky v tomto článku? Přečtěte si [referenční informace k rozhraní API pro aktivity přihlašování do Azure Active Directory](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin). 
* Úplný přehled použití rozhraní API pro generování sestav Azure Active Directory najdete v tématu [Začínáme s rozhraním API pro generování sestav Azure Active Directory](active-directory-reporting-api-getting-started.md).
* Další informace o generování sestav Azure Active Directory najdete v tématu [Průvodce generováním sestav Azure Active Directory](active-directory-reporting-guide.md).  

