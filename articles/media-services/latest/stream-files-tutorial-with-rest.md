---
title: Kódování vzdáleného souboru a datového proudu pomocí Media Services
description: Použijte postup v tomto kurzu ke kódování souboru na základě adresy URL a streamování vašeho obsahu pomocí Azure Media Services s využitím REST.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/17/2021
ms.author: inhenkel
ms.openlocfilehash: 92f89bafe1b96b575d12e8a09289a05c2379d837
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280016"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---rest"></a>Kurz: Kódování vzdáleného souboru na základě adresy URL a streamování videa – REST

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services umožňuje kódování mediálních souborů ve formátech, které se dají přehrávat na nejrůznějších prohlížečích a zařízeních. Například můžete chtít svůj obsah streamovat ve formátu Apple HLS nebo MPEG DASH. Před streamováním je vhodné soubor digitálního média ve vysoké kvalitě zakódovat. Pokyny ke kódování najdete v tématu [Principy kódování](encode-concept.md).

V tomto kurzu se dozvíte, jak zakódovat soubor na základě adresy URL a streamu videa s Azure Media Services pomocí REST. 

![Přehrávání videa](./media/stream-files-tutorial-with-api/final-video.png)

V tomto kurzu získáte informace o následujících postupech:    

> [!div class="checklist"]
> * Vytvoření účtu Media Services
> * Přístup k rozhraní Media Services API
> * Stažení souborů nástroje Postman
> * Konfigurace nástroje Postman
> * Odesílání požadavků pomocí nástroje Postman
> * Testování adresy URL pro streamování
> * Vyčištění prostředků

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

- [Vytvořte účet Media Services](./account-create-how-to.md).

    Nezapomeňte si pamatovat hodnoty, které jste použili pro název skupiny prostředků a název účtu Media Services

- Nainstalujte klienta [Postman](https://www.getpostman.com/) rozhraní REST, ve kterém můžete spouštět rozhraní REST API používaná v některých kurzech ke službě AMS REST. 

    V příkladech používáme **Postman**, můžete ale zvolit jakýkoli nástroj REST. Další alternativy: **Visual Studio Code** pomocí modulu plug-in REST nebo **Telerik Fiddler**. 

## <a name="download-postman-files"></a>Stažení souborů nástroje Postman

Naklonujte úložiště GitHub, který obsahuje soubory kolekce a prostředí nástroje Postman.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="access-api"></a>Přístup k rozhraní API

Podrobné informace najdete v tématu [získání přihlašovacích údajů pro přístup k rozhraní Media Services API](access-api-howto.md) .

## <a name="configure-postman"></a>Konfigurace nástroje Postman

### <a name="configure-the-environment"></a>Konfigurace prostředí 

1. Otevřete aplikaci pro **vyúčtování** .
2. Na pravé straně obrazovky vyberte možnost **Manage environment** (Spravovat prostředí).

    ![Správa prostředí](./media/develop-with-postman/postman-import-env.png)
4. V dialogovém okně **Manage environment** (Spravovat prostředí) klikněte na **Import**.
2. Vyhledejte soubor `Azure Media Service v3 Environment.postman_environment.json`, který se stáhl při klonování úložiště `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
6. Přidá se prostředí služby **Azure Media Service v3 Environment**.

    > [!Note]
    > Aktualizujte přístupové proměnné pomocí hodnot, které jste získali výše v části **Přístup k rozhraní API služby Media Services**.

7. Poklikejte na vybraný soubor a zadejte hodnoty, které jste získali v postupu pro [přístup k rozhraní API](#access-api).
8. Zavřete dialogové okno.
9. Z rozevíracího seznamu vyberte prostředí **Azure Media Service v3 Environment**.

    ![Výběr prostředí](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>Konfigurace kolekce

1. Kliknutím na **Import** importujte soubor kolekce.
1. Vyhledejte soubor `Media Services v3.postman_collection.json`, který se stáhl při klonování úložiště `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
3. Vyberte soubor **Media Services v3.postman_collection.json**.

    ![Importovat soubor](./media/develop-with-postman/postman-import-collection.png)

## <a name="send-requests-using-postman"></a>Odesílání požadavků pomocí nástroje Postman

V této části odešleme požadavky relevantní pro kódování a vytvoření adres URL, abyste mohli soubor streamovat. Konkrétně se odesílají následující požadavky:

1. Získání tokenu služby Azure AD pro ověření instančního objektu
1. Spuštění koncového bodu streamování
2. Vytvoření výstupního prostředku
3. Vytvořit transformaci
4. Vytvoření úlohy
5. Vytvoření lokátoru streamování
6. Seznam cest lokátoru streamování

> [!Note]
>  V tomto kurzu předpokládáme, že všechny prostředky vytváříte s jedinečnými názvy.  

### <a name="get-azure-ad-token"></a>Získání tokenu služby Azure AD 

1. V levém okně aplikace po výběru vyberte "krok 1: získání ověřovacího tokenu AAD".
2. Potom vyberte „Get Azure AD Token for Service Principal Authentication“ (Získat token služby Azure AD pro ověření instančního objektu).
3. Stiskněte **Odeslat**.

    Odešle se následující operace **POST**.

    ```
    https://login.microsoftonline.com/:aadTenantDomain/oauth2/token
    ```

4. Vrátí se odpověď s tokenem, která nastaví proměnnou prostředí „AccessToken“ na hodnotu tokenu. Kód, který nastavuje proměnnou „AccessToken“, zobrazíte na kartě **Tests** (Testy). 

    ![Získání tokenu AAD](./media/develop-with-postman/postman-get-aad-auth-token.png)


### <a name="start-a-streaming-endpoint"></a>Spuštění koncového bodu streamování

Pokud chcete streamování povolit, musíte nejdřív spustit [koncový bod streamování](./stream-streaming-endpoint-concept.md) , ze kterého chcete streamovat video.

> [!NOTE]
> Fakturuje se vám jenom v případě, že je koncový bod streamování ve stavu spuštěno.

1. V levém okně aplikace po výběru vyberte streamování a živé.
2. Pak vyberte "spustit StreamingEndpoint".
3. Stiskněte **Odeslat**.

    * Odesílá se následující operace **post** :

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaservices/:accountName/streamingEndpoints/:streamingEndpointName/start?api-version={{api-version}}
        ```
    * Pokud je žádost úspěšná, `Status: 202 Accepted` vrátí se.

        Tento stav znamená, že žádost byla přijata ke zpracování; zpracování však nebylo dokončeno. Můžete zadat dotaz na stav operace na základě hodnoty v `Azure-AsyncOperation` hlavičce Response.

        Například následující operace GET vrátí stav vaší operace:
        
        `https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<resourceGroupName>/providers/Microsoft.Media/mediaservices/<accountName>/streamingendpointoperations/1be71957-4edc-4f3c-a29d-5c2777136a2e?api-version=2018-07-01`

        Článek [sledování asynchronních operací Azure](../../azure-resource-manager/management/async-operations.md) podrobněji vysvětluje, jak sledovat stav asynchronních operací Azure prostřednictvím hodnot vrácených v odpovědi.

### <a name="create-an-output-asset"></a>Vytvoření výstupního prostředku

Výstupní [Asset](/rest/api/media/assets) ukládá výsledek vaší úlohy kódování. 

1. V levém okně aplikace po výběru vyberte prostředky.
2. Pak vyberte „Create or update an Asset“ (Vytvořit nebo aktualizovat prostředek).
3. Stiskněte **Odeslat**.

    * Odešle se následující operace **PUT**:

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/assets/:assetName?api-version={{api-version}}
        ```
    * Operace obsahuje následující text:

        ```json
        {
        "properties": {
            "description": "My Asset",
            "alternateId" : "some GUID",
            "storageAccountName": "<replace from environment file>",
            "container": "<supply any valid container name of your choosing>"
         }
        }
        ```

> [!NOTE]
> Nezapomeňte nahradit účet úložiště a názvy kontejnerů buď pomocí těch ze souboru prostředí, nebo zadejte vlastní.
>
> Po dokončení kroků popsaných ve zbývající části tohoto článku se ujistěte, že v textu žádosti zadáte platné parametry.

### <a name="create-a-transform"></a>Vytvoření transformace

Když kódujete nebo zpracováváte obsah v Media Services, kódování se obvykle nastaví jako předpis. Potom stačí odeslat **Úlohu**, která tento předpis použije pro video. Odesláním nových úloh pro každé nové video použijete tento recept na všechna videa v knihovně. V Media Services se pro předpis používá označení **transformace**. Další informace najdete v tématu [Transformace a úlohy](./transform-jobs-concept.md). Ukázka popsaná v tomto kurzu definuje předpis, který zakóduje video tak, aby se dalo streamovat na nejrůznějších zařízeních s iOSem a Androidem. 

Když vytváříte novou instanci [Transformace](/rest/api/media/transforms), musíte určit, co má být jejím výstupem. Objekt **TransformOutput** je povinný parametr. Každý objekt **TransformOutput** obsahuje **Předvolbu**. **Předvolba** popisuje podrobné pokyny operací zpracování videa nebo zvuku, které se používají ke generování požadovaného objektu **TransformOutput**. Ukázka popsaná v tomto článku používá předdefinovanou předvolbu s názvem **AdaptiveStreaming**. Tato předvolba zakóduje vstupní video na základě vstupního rozlišení a přenosové rychlosti do automaticky generované dvojice přenosová rychlost / rozlišení (tzv. bitrate ladder) a vytvoří soubory ISO MP4 s videem H.264 a zvukem AAC odpovídající jednotlivým dvojicím přenosová rychlost / rozlišení. Informace o této předvolbě najdete v tématu o [automatickém generování dvojic bitrate ladder](encode-autogen-bitrate-ladder.md).

Můžete použít předdefinovanou předvolbu EncoderNamedPreset, nebo si vytvořit vlastní. 

> [!Note]
> Než začnete vytvářet [transformaci](/rest/api/media/transforms), ověřte si nejdřív pomocí metody **Get**, jestli už neexistuje. V tomto kurzu předpokládáme, že vytváříte transformaci s jedinečným názvem.

1. V levém okně aplikace po vybírání kódování a analýzy.
2. Potom vyberte „Create Transform“ (Vytvořit transformaci).
3. Stiskněte **Odeslat**.

    * Následující operace **Put** je odeslána.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
        ```
    * Operace obsahuje následující text:

        ```json
        {
            "properties": {
                "description": "Standard Transform using an Adaptive Streaming encoding preset from the library of built-in Standard Encoder presets",
                "outputs": [
                    {
                    "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                    "preset": {
                        "@odata.type": "#Microsoft.Media.BuiltInStandardEncoderPreset",
                        "presetName": "AdaptiveStreaming"
                    }
                    }
                ]
            }
        }
        ```

### <a name="create-a-job"></a>Vytvoření úlohy

[Úloha](/rest/api/media/jobs) je vlastní požadavek na službu Media Services, aby vytvořenou **transformaci** použila na daný vstupní videoobsah nebo zvukový obsah. **Úloha** určuje informace, jako je umístění vstupního videa a umístění pro výstup.

V tomto příkladu je vstup úlohy založen na adrese URL HTTPS ("https: \/ /nimbuscdn-nimbuspm.Streaming.MediaServices.Windows.NET/2b533311-b215-4409-80af-529c3e853622/").

1. V levém okně aplikace po vybírání kódování a analýzy.
2. Pak vyberte „Create or Update Job“ (Vytvořit nebo aktualizovat úlohu).
3. Stiskněte **Odeslat**.

    * Následující operace **Put** je odeslána.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
        ```
    * Operace obsahuje následující text:

        ```json
        {
        "properties": {
            "input": {
            "@odata.type": "#Microsoft.Media.JobInputHttp",
            "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
            "files": [
                    "Ignite-short.mp4"
                ]
            },
            "outputs": [
            {
                "@odata.type": "#Microsoft.Media.JobOutputAsset",
                "assetName": "testAsset1"
            }
            ]
        }
        }
        ```

Úloze chvíli trvá, než se dokončí, a když k tomu dojde, budete na to pravděpodobně chtít upozornit. K zobrazení průběhu úlohy doporučujeme použít službu Event Grid. Ta je navržená s ohledem na vysokou dostupnost, stabilní výkon a dynamické škálování. Díky službě Event Grid můžou vaše aplikace naslouchat událostem a reagovat na ně, ať už pocházejí z kterékoli služby Azure. Události můžou pocházet i z vlastních zdrojů. Jednoduché, reaktivní zpracování událostí založené na protokolu HTTP pomáhá sestavovat efektivní řešení prostřednictvím inteligentního filtrování a směrování událostí.  Další informace najdete v článku [Směrování událostí na vlastní webový koncový bod](monitoring/job-state-events-cli-how-to.md).

**Úloha** obvykle prochází následujícími stavy: **Naplánováno**, **Ve frontě**, **Zpracovávání** a **Dokončeno** (konečný stav). Pokud během provádění úlohy dojde k chybě, přejde úloha do stavu **Chyba**. Když úlohu zrušíte, změní se její stav na **Rušení** a potom na **Zrušeno**.

#### <a name="job-error-codes"></a>Kódy chyb úlohy

Viz [kódy chyb](/rest/api/media/jobs/get#joberrorcode).

### <a name="create-a-streaming-locator"></a>Vytvoření lokátoru streamování

Po dokončení úlohy kódování je dalším krokem vytvoření videa ve výstupním **prostředku** pro klienty k přehrávání. Video můžete zpřístupnit ve dvou krocích: nejdřív vytvořte streamovací lokátor ([StreamingLocator](/rest/api/media/streaminglocators)) a pak adresy URL pro streamování, které budou klienti používat. 

Proces vytvoření lokátoru streamování se nazývá publikování. Ve výchozím nastavení je Lokátor streamování platný hned po volání rozhraní API a trvá až do odstranění, pokud nenastavíte volitelné počáteční a koncové časy. 

Když vytváříte streamovací lokátor [StreamingLocator](/rest/api/media/streaminglocators), je potřeba zadat požadovaný název zásady streamování **StreamingPolicyName**. V tomto příkladu budete zasílat streamování (nebo nešifrovaných) obsahu, takže se použije předdefinovaná zásada pro zrušení streamování Predefined_ClearStreamingOnly.

> [!IMPORTANT]
> Pokud chcete definovat vlastní [zásady streamování](/rest/api/media/streamingpolicies), doporučujeme navrhnout pro účet služby Media Service omezený počet takovýchto zásad a používat je opakovaně pro streamovací lokátory, kdykoli potřebujete stejné protokoly a možnosti šifrování. 

Váš účet Media Service má kvótu pro počet položek **zásad streamování** . Pro každý Lokátor streamování byste neměli vytvářet nové **zásady streamování** .

1. V levém okně aplikace po tomto výběru vyberte zásady streamování a lokátory.
2. Pak vyberte vytvořit Lokátor streamování (Vymazat).
3. Stiskněte **Odeslat**.

    * Následující operace **Put** je odeslána.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingLocators/:streamingLocatorName?api-version={{api-version}}
        ```
    * Operace obsahuje následující text:

        ```json
        {
          "properties": {
            "streamingPolicyName": "Predefined_ClearStreamingOnly",
            "assetName": "testAsset1",
            "contentKeys": [],
            "filters": []
         }
        }
        ```

### <a name="list-paths-and-build-streaming-urls"></a>Seznam cest a vytvoření adres URL pro streamování

#### <a name="list-paths"></a>Seznam cest

Teď, když se vytvořil [Lokátor streamování](/rest/api/media/streaminglocators) , můžete získat adresy URL streamování.

1. V levém okně aplikace pro odesílání vyberte možnost Zásady streamování.
2. Potom vyberte „List Paths“ (Seznam cest).
3. Stiskněte **Odeslat**.

    * Odešle se následující operace **POST**.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingLocators/:streamingLocatorName/listPaths?api-version={{api-version}}
        ```
        
    * Operace neobsahuje žádný text.
        
4. Poznamenejte si jednu z cest, které chcete použít pro streamování, budete ji potřebovat v další části. V tomto případě se vrátily následující cesty:
    
    ```
    "streamingPaths": [
        {
            "streamingProtocol": "Hls",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=m3u8-aapl)"
            ]
        },
        {
            "streamingProtocol": "Dash",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=mpd-time-csf)"
            ]
        },
        {
            "streamingProtocol": "SmoothStreaming",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest"
            ]
        }
    ]
    ```

#### <a name="build-the-streaming-urls"></a>Vytvoření adres URL pro streamování

V této části vytvoříme adresu URL pro streamování HLS. Adresy URL se skládají z následujících hodnot:

1. Protokol, přes který se data odesílají. V tomto případě je to protokol HTTPS.

    > [!NOTE]
    > Pokud se přehrávač hostuje na webu HTTPS, nezapomeňte adresu URL aktualizovat tak, aby obsahovala „https“. 

2. Název hostitele koncového bodu streamování. V tomto případě je to „amsaccount-usw22.streaming.media.azure.net“.

    K získání názvu hostitele můžete použít následující operaci GET:
    
    ```
    https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaservices/:accountName/streamingEndpoints/default?api-version={{api-version}}
    ```
    a ujistěte se, že jste nastavili `resourceGroupName` `accountName` parametry a tak, aby odpovídaly souboru prostředí. 
    
3. Cesta, kterou jste získali v předchozí části (Seznam cest).  

Výsledkem pak je následující adresa URL pro streamování HLS

```
https://amsaccount-usw22.streaming.media.azure.net/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=m3u8-aapl)
```

## <a name="test-the-streaming-url"></a>Testování adresy URL pro streamování


> [!NOTE]
> Ujistěte se, že **koncový bod streamování** , ze kterého chcete streamovat, je spuštěný.

Tento článek používá k otestování streamu přehrávač Azure Media Player. 

1. Otevřete webový prohlížeč a přejděte na [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. Do pole pro **adresu URL** vložte adresu URL, kterou jste vytvořili. 
3. Stiskněte **Update Player** (Aktualizovat přehrávač).

Azure Media Player můžete použít pro účely testování, nesmí se ale používat v produkčním prostředí. 

## <a name="clean-up-resources-in-your-media-services-account"></a>Vyčištění prostředků v účtu služby Media Services

Obecně platí, že byste měli vyčistit všechno kromě objektů, které plánujete znovu použít (obvykle budete znovu používat **transformace** a budete uchovávat **Lokátory streamování** atd.). Pokud chcete účet po experimentování vyčistit, měli byste odstranit prostředky, které nemáte v plánu znovu použít.  

Provedete to tak, že u prostředku, který chcete odstranit, vyberete operaci „Odstranit…“.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud ze skupiny prostředků už žádné prostředky nepotřebujete, včetně účtu služby Media Services a účtu úložiště, které jste vytvořili v tomto kurzu, pak tuto dříve vytvořenou skupinu prostředků odstraňte.  

Spusťte následující příkaz rozhraní příkazového řádku:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Položte otázky, sdělte nám svůj názor, Získejte aktualizace.

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="next-steps"></a>Další kroky

Teď, když už víte, jak nahrávat, kódovat a streamovat videa, podívejte se na následující článek: 

> [!div class="nextstepaction"]
> [Analýza videí](analyze-videos-tutorial.md)
