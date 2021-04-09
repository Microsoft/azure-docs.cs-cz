---
title: Poznámky k verzi pro Application Insights | Microsoft Docs
description: Přidejte značky nasazení nebo sestavení do grafů Průzkumníka metrik v Application Insights.
ms.topic: conceptual
ms.date: 08/14/2020
ms.openlocfilehash: 776efd56aaa523d1c2621c51cba0446a42bb7411
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103461908"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Poznámky na grafech metrik v Application Insights

Poznámky ukazují, kde jste nasadili nové sestavení nebo jiné významné události. Poznámky usnadňují zjištění, zda vaše změny měly vliv na výkon vaší aplikace. Mohou být automaticky vytvořeny [Azure Pipelinesm](/azure/devops/pipelines/tasks/) systémem sestavení. Můžete také vytvořit poznámky k označení libovolné události, kterou chcete, vytvořením z PowerShellu.

## <a name="release-annotations-with-azure-pipelines-build"></a>Poznámky k verzi s Azure Pipelines Build

Poznámky k verzi jsou součástí cloudové Azure Pipelines služby Azure DevOps.

### <a name="install-the-annotations-extension-one-time"></a>Instalace rozšíření pro poznámky (jednorázově)

Aby bylo možné vytvářet poznámky k verzi, budete muset nainstalovat jednu z mnoha rozšíření Azure DevOps, která jsou k dispozici v Visual Studio Marketplace.

1. Přihlaste se ke svému projektu [Azure DevOps](https://azure.microsoft.com/services/devops/) .
   
1. Na stránce [rozšíření pro poznámky k verzi](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) Visual Studio Marketplace vyberte vaši organizaci Azure DevOps a pak výběrem **nainstalovat** přidejte rozšíření do vaší organizace Azure DevOps.
   
   ![Vyberte organizaci Azure DevOps a pak vyberte nainstalovat.](./media/annotations/1-install.png)
   
Pro vaši organizaci Azure DevOps stačí rozšíření nainstalovat jenom jednou. Nyní můžete nakonfigurovat poznámky k verzi pro libovolný projekt ve vaší organizaci.

### <a name="configure-release-annotations"></a>Konfigurovat poznámky k verzi

Vytvořte samostatný klíč rozhraní API pro každou ze šablon verze Azure Pipelines.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a otevřete prostředek Application Insights, který monitoruje vaši aplikaci. Pokud ho ještě nemáte, [vytvořte nový prostředek Application Insights](./app-insights-overview.md).
   
1. Otevřete kartu **přístup přes rozhraní API** a zkopírujte **ID Application Insights**.
   
   ![V části přístup k rozhraní API zkopírujte ID aplikace.](./media/annotations/2-app-id.png)

1. V samostatném okně prohlížeče otevřete nebo vytvořte šablonu vydané verze, která spravuje nasazení Azure Pipelines.
   
1. Vyberte **Přidat úlohu** a potom z nabídky vyberte úlohu **poznámky k verzi Application Insights** .
   
   ![Vyberte přidat úlohu a vyberte Application Insights poznámky k verzi.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > Úloha poznámky k verzi aktuálně podporuje jenom agenty založené na Windows. neběží na Linux, macOS nebo jiných typech agentů.
   
1. V části **ID aplikace** vložte Application Insights ID, které jste zkopírovali z karty **přístup k rozhraní API** .
   
   ![Vložit ID Application Insights](./media/annotations/4-paste-app-id.png)
   
1. Zpátky v okně Application Insights **přístup k rozhraní API** vyberte **vytvořit klíč rozhraní API**. 
   
   ![Na kartě přístup k rozhraní API vyberte vytvořit klíč rozhraní API.](./media/annotations/5-create-api-key.png)
   
1. V okně **vytvořit klíč rozhraní API** zadejte popis, vyberte **zápis poznámek** a pak vyberte **vygenerovat klíč**. Zkopírujte nový klíč.
   
   ![V okně vytvořit klíč rozhraní API zadejte popis, vyberte zápis poznámek a pak vyberte vygenerovat klíč.](./media/annotations/6-create-api-key.png)
   
1. V okně Šablona verze vyberte na kartě **proměnné** možnost **Přidat** a vytvořte definici proměnné pro nový klíč rozhraní API.

1. Do pole **název** zadejte `ApiKey` a v části **hodnota** vložte klíč rozhraní API, který jste zkopírovali z karty **přístup přes rozhraní API** .
   
   ![Na kartě proměnné DevOps Azure vyberte Přidat, pojmenujte proměnnou ApiKey a vložte klíč rozhraní API pod hodnotu.](./media/annotations/7-paste-api-key.png)
   
1. Vyberte **Uložit** v hlavním okně šablony vydané verze a uložte šablonu.


   > [!NOTE]
   > Omezení pro klíče rozhraní API jsou popsána v [dokumentaci omezení rychlosti REST API](https://dev.applicationinsights.io/documentation/Authorization/Rate-limits).

## <a name="view-annotations"></a>Zobrazit poznámky


   > [!NOTE]
   > Poznámky k verzi nejsou aktuálně k dispozici v podokně metriky Application Insights

Když teď k nasazení nové verze použijete šablonu verze, pošle se Application Insights Poznámka. Poznámky lze zobrazit v následujících umístěních:

Podokno **použití** , kde máte také možnost ručně vytvořit poznámky k verzi:

![Snímek obrazovky pruhového grafu s počtem návštěv uživatele zobrazených v průběhu několika hodin Poznámky k verzi se zobrazí jako zelené zaškrtnutí nad grafem, který indikuje moment v čase, kdy k vydání došlo.](./media/annotations/usage-pane.png)

V jakémkoli dotazu na sešit založený na protokolu, kde vizualizace zobrazuje čas podél osy x.

![Snímek obrazovky podokna sešitů s dotazem na základě protokolu Time Series se zobrazenými poznámkami](./media/annotations/workbooks-annotations.png)

Pokud chcete povolit poznámky v sešitu, přejít na **Upřesnit nastavení** a vyberte **Zobrazit poznámky**.

![Snímek obrazovky s nabídkou pokročilých nastavení s slovy zobrazit poznámky zvýrazněné vedle nastavení, které se má povolit](./media/annotations/workbook-show-annotations.png)

Vyberte libovolnou značku poznámky a otevřete tak podrobnosti o vydané verzi, včetně žadatele, větve správy zdrojového kódu, kanálu vydání a prostředí.

## <a name="create-custom-annotations-from-powershell"></a>Vytváření vlastních poznámek z PowerShellu
Pomocí skriptu prostředí PowerShell pro CreateReleaseAnnotation z GitHubu můžete vytvářet poznámky z libovolného procesu, který chcete, bez použití Azure DevOps.

1. Vytvořte místní kopii CreateReleaseAnnotation.ps1:

    ```powershell
    
    # Copyright (c) Microsoft Corporation. All rights reserved. 
    # Licensed under the MIT License. See License.txt in the project root for license information. 
    
    # Sample usage .\CreateReleaseAnnotation.ps1 -applicationId "<appId>" -apiKey "<apiKey>" -releaseFilePath "<path to .exe with file version>" -releaseProperties @{"ReleaseDescription"="Release with annotation";"TriggerBy"="John Doe"}
    param(
        [parameter(Mandatory = $true)][string]$applicationId,
        [parameter(Mandatory = $true)][string]$apiKey,
        [parameter(Mandatory = $true)][string]$releaseFilePath,
        [parameter(Mandatory = $false)]$releaseProperties
    )
    
    $releaseName = (Get-Item $releaseFilePath).VersionInfo.FileVersion
    Write-Host "Creating release annotation $releaseName in ApplicationInsights" -ForegroundColor Cyan
    
    # background info on how fwlink works: After you submit a web request, many sites redirect through a series of intermediate pages before you finally land on the destination page.
    # So when calling Invoke-WebRequest, the result it returns comes from the final page in any redirect sequence. Hence, I set MaximumRedirection to 0, as this prevents the call to 
    # be redirected. By doing this, we get a response with status code 302, which indicates that there is a redirection link from the response body. We grab this redirection link and 
    # construct the url to make a release annotation.
    # Here's how this logic is going to works
    # 1. Client send http request, such as:  http://go.microsoft.com/fwlink/?LinkId=625115
    # 2. FWLink get the request and find out the destination URL for it, such as:  http://www.bing.com
    # 3. FWLink generate a new http response with status code “302” and with destination URL “http://www.bing.com”. Send it back to Client.
    # 4. Client, such as a powershell script, knows that status code “302” means redirection to new a location, and the target location is “http://www.bing.com”
    function GetRequestUrlFromFwLink($fwLink)
    {
        $request = Invoke-WebRequest -Uri $fwLink -MaximumRedirection 0 -UseBasicParsing -ErrorAction Ignore
        if ($request.StatusCode -eq "302") {
            return $request.Headers.Location
        }
        
        return $null
    }
    
    function CreateAnnotation($grpEnv)
    {
        $retries = 1
        $success = $false
        while (!$success -and $retries -lt 6) {
            $location = "$grpEnv/applications/$applicationId/Annotations?api-version=2015-11"
                
            Write-Host "Invoke a web request for $location to create a new release annotation. Attempting $retries"
            set-variable -Name createResultStatus -Force -Scope Local -Value $null
            set-variable -Name createResultStatusDescription -Force -Scope Local -Value $null
            set-variable -Name result -Force -Scope Local
    
            try {
                $result = Invoke-WebRequest -Uri $location -Method Put -Body $bodyJson -Headers $headers -ContentType "application/json; charset=utf-8" -UseBasicParsing
            } catch {
                if ($_.Exception){
                    if($_.Exception.Response) {
                        $createResultStatus = $_.Exception.Response.StatusCode.value__
                        $createResultStatusDescription = $_.Exception.Response.StatusDescription
                    }
                    else {
                        $createResultStatus = "Exception"
                        $createResultStatusDescription = $_.Exception.Message
                    }
                }
            }
    
            if ($result -eq $null) {
                if ($createResultStatus -eq $null) {
                    $createResultStatus = "Unknown"
                }
                if ($createResultStatusDescription -eq $null) {
                    $createResultStatusDescription = "Unknown"
                }
            }
            else {
                    $success = $true                     
            }
    
            if ($createResultStatus -eq 409 -or $createResultStatus -eq 404 -or $createResultStatus -eq 401) # no retry when conflict or unauthorized or not found
            {
                break
            }
    
            $retries = $retries + 1
            sleep 1
        }
    
        $createResultStatus
        $createResultStatusDescription
        return
    }
    
    # Need powershell version 3 or greater for script to run
    $minimumPowershellMajorVersion = 3
    if ($PSVersionTable.PSVersion.Major -lt $minimumPowershellMajorVersion) {
       Write-Host "Need powershell version $minimumPowershellMajorVersion or greater to create release annotation"
       return
    }
    
    $currentTime = (Get-Date).ToUniversalTime()
    $annotationDate = $currentTime.ToString("MMddyyyy_HHmmss")
    set-variable -Name requestBody -Force -Scope Script
    $requestBody = @{}
    $requestBody.Id = [GUID]::NewGuid()
    $requestBody.AnnotationName = $releaseName
    $requestBody.EventTime = $currentTime.GetDateTimeFormats("s")[0] # GetDateTimeFormats returns an array
    $requestBody.Category = "Deployment"
    
    if ($releaseProperties -eq $null) {
        $properties = @{}
    } else {
        $properties = $releaseProperties    
    }
    $properties.Add("ReleaseName", $releaseName)
    
    $requestBody.Properties = ConvertTo-Json($properties) -Compress
    
    $bodyJson = [System.Text.Encoding]::UTF8.GetBytes(($requestBody | ConvertTo-Json))
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("X-AIAPIKEY", $apiKey)
    
    set-variable -Name createAnnotationResult1 -Force -Scope Local -Value $null
    set-variable -Name createAnnotationResultDescription -Force -Scope Local -Value ""
    
    # get redirect link from fwlink
    $requestUrl = GetRequestUrlFromFwLink("http://go.microsoft.com/fwlink/?prd=11901&pver=1.0&sbp=Application%20Insights&plcid=0x409&clcid=0x409&ar=Annotations&sar=Create%20Annotation")
    if ($requestUrl -eq $null) {
        $output = "Failed to find the redirect link to create a release annotation"
        throw $output
    }
    
    $createAnnotationResult1, $createAnnotationResultDescription = CreateAnnotation($requestUrl)
    if ($createAnnotationResult1) 
    {
         $output = "Failed to create an annotation with Id: {0}. Error {1}, Description: {2}." -f $requestBody.Id, $createAnnotationResult1, $createAnnotationResultDescription
         throw $output
    }
    
    $str = "Release annotation created. Id: {0}." -f $requestBody.Id
    Write-Host $str -ForegroundColor Green
    
    ```
   
1. Pomocí kroků v předchozím postupu můžete získat ID Application Insights a na kartě **přístup k rozhraní api** Application Insights vytvořit klíč rozhraní API.
   
1. Zavolejte skript prostředí PowerShell s následujícím kódem a nahraďte zástupné symboly v závorkách hodnotami. Rozhraní `-releaseProperties` jsou volitelná. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

Skript můžete upravit například tak, aby bylo možné vytvořit poznámky za minulosti.

## <a name="next-steps"></a>Další kroky

* [vytvářet pracovní položky,](./diagnostic-search.md#create-work-item)
* [Automatizace v prostředí PowerShell](./powershell.md)

