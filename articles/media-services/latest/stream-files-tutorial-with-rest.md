---
title: Kódování na vzdálený soubor, na základě adresy URL a datového proudu pomocí Azure Media Services - REST | Dokumentace Microsoftu
description: Postupujte podle kroků v tomto kurzu kódování souboru podle adresy URL a Streamovat obsah pomocí služby Azure Media Services pomocí rozhraní REST.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 12/17/2018
ms.author: juliako
ms.openlocfilehash: a26d3c8d6582c07c9724c2872d035e0d4fc01f5e
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53581337"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---rest"></a>Kurz: Vzdálený soubor na základě adresy URL kódování a streamování videa – REST

Azure Media Services umožňuje kódování souborů médií do formátů, které můžete přehrát na širokou škálu prohlížečů a zařízení. Například můžete chtít svůj obsah streamovat ve formátu Apple HLS nebo MPEG DASH. Před streamováním je vhodné soubor digitálního média ve vysoké kvalitě zakódovat. Pokyny ke kódování najdete v tématu [Principy kódování](encoding-concept.md).

V tomto kurzu se dozvíte postupy kódování souboru podle adresy URL a Streamovat videa pomocí služby Azure Media Services pomocí rozhraní REST. 

![Přehrávání videa](./media/stream-files-tutorial-with-api/final-video.png)

V tomto kurzu získáte informace o následujících postupech:    

> [!div class="checklist"]
> * Vytvoření účtu Media Services
> * Přístup k rozhraní API služby Media Services
> * Stažení souborů nástroje Postman
> * Konfigurace nástroje Postman
> * Odesílání požadavků pomocí nástroje Postman
> * Testování adresy URL pro streamování
> * Vyčištění prostředků

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

- Nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

    V současné době všechny [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) příkazy fungují ve službě Azure Cloud Shell. Doporučujeme používat rozhraní příkazového řádku místně.

- [Vytvoření účtu Media Services](create-account-cli-how-to.md).

    Ujistěte se, že hodnoty, které jste použili pro název skupiny prostředků a název účtu Media Services mějte na paměti

- Nainstalujte klienta [Postman](https://www.getpostman.com/) rozhraní REST, ve kterém můžete spouštět rozhraní REST API používaná v některých kurzech ke službě AMS REST. 

    V příkladech používáme **Postman**, můžete ale zvolit jakýkoli nástroj REST. Další možnosti jsou: **Visual Studio Code** pomocí modulu plug-in REST nebo **Telerik Fiddler**. 

## <a name="download-postman-files"></a>Stažení souborů nástroje Postman

Naklonujte úložiště GitHub, který obsahuje soubory kolekce a prostředí nástroje Postman.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="configure-postman"></a>Konfigurace nástroje Postman

Tato část popisuje konfiguraci nástroje Postman.

### <a name="configure-the-environment"></a>Konfigurace prostředí 

1. Spusťte nástroj **Postman**.
2. Na pravé straně obrazovky vyberte možnost **Manage environment** (Spravovat prostředí).

    ![Správa prostředí](./media/develop-with-postman/postman-import-env.png)
4. V dialogovém okně **Manage environment** (Spravovat prostředí) klikněte na **Import**.
2. Vyhledejte soubor `Azure Media Service v3 Environment.postman_environment.json`, který se stáhl při klonování úložiště `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
6. Přidá se prostředí služby **Azure Media Service v3 Environment**.

    > [!Note]
    > Aktualizujte přístupové proměnné pomocí hodnot, které jste získali výše v části **Přístup k rozhraní API služby Media Services**.

7. Poklikejte na vybraný soubor a zadejte hodnoty, které jste získali v postupu pro [přístup k rozhraní API](#access-the-media-services-api).
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
2. Vytvoření výstupního prostředku
3. Vytvoření transformace
4. Vytvoření úlohy 
5. Vytvoření lokátoru streamování
6. Seznam cest lokátoru streamování

> [!Note]
>  V tomto kurzu předpokládáme, že všechny prostředky vytváříte s jedinečnými názvy.  

### <a name="get-azure-ad-token"></a>Získání tokenu služby Azure AD 

1. V levém okně Postman, vyberte "krok 1: Získání ověřování AAD tokenu".
2. Potom vyberte „Get Azure AD Token for Service Principal Authentication“ (Získat token služby Azure AD pro ověření instančního objektu).
3. Stiskněte **Odeslat**.

    Odešle se následující operace **POST**.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. Vrátí se odpověď s tokenem, která nastaví proměnnou prostředí „AccessToken“ na hodnotu tokenu. Kód, který nastavuje proměnnou „AccessToken“, zobrazíte na kartě **Tests** (Testy). 

    ![Získání tokenu AAD](./media/develop-with-postman/postman-get-aad-auth-token.png)

### <a name="create-an-output-asset"></a>Vytvoření výstupního prostředku

Výstupní [prostředek](https://docs.microsoft.com/rest/api/media/assets) uloží výsledek vaší úlohy kódování. 

1. V levém okně nástroje Postman vyberte „Assets“ (Prostředky).
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
            "alternateId" : "some GUID"
         }
        }
        ```

### <a name="create-a-transform"></a>Vytvoření transformace

Když kódujete nebo zpracováváte obsah v Media Services, kódování se obvykle nastaví jako předpis. Potom stačí odeslat **Úlohu**, která tento předpis použije pro video. Když odešlete novou Úlohu pro každé nové video, použije se tento předpis pro všechna videa ve vaší knihovně. V Media Services se pro předpis používá označení **transformace**. Další informace najdete v tématu [Transformace a úlohy](transform-concept.md). Ukázka popsaná v tomto kurzu definuje předpis, který zakóduje video tak, aby se dalo streamovat na nejrůznějších zařízeních s iOSem a Androidem. 

Když vytváříte novou instanci [Transformace](https://docs.microsoft.com/rest/api/media/transforms), musíte určit, co má být jejím výstupem. Objekt **TransformOutput** je povinný parametr. Každý objekt **TransformOutput** obsahuje **Předvolbu**. **Předvolba** popisuje podrobné pokyny operací zpracování videa nebo zvuku, které se používají ke generování požadovaného objektu **TransformOutput**. Ukázka popsaná v tomto článku používá předdefinovanou předvolbu s názvem **AdaptiveStreaming**. Tato předvolba zakóduje vstupní video na základě vstupního rozlišení a přenosové rychlosti do automaticky generované dvojice přenosová rychlost / rozlišení (tzv. bitrate ladder) a vytvoří soubory ISO MP4 s videem H.264 a zvukem AAC odpovídající jednotlivým dvojicím přenosová rychlost / rozlišení. Informace o této předvolbě najdete v tématu o [automatickém generování dvojic bitrate ladder](autogen-bitrate-ladder.md).

Můžete použít předdefinovanou předvolbu EncoderNamedPreset, nebo si vytvořit vlastní. 

> [!Note]
> Než začnete vytvářet [transformaci](https://docs.microsoft.com/rest/api/media/transforms), ověřte si nejdřív pomocí metody **Get**, jestli už neexistuje. V tomto kurzu předpokládáme, že vytváříte transformaci s jedinečným názvem.

1. V levém okně nástroje Postman vyberte „Encoding and Analysis“ (Kódování a analýza).
2. Potom vyberte „Create Transform“ (Vytvořit transformaci).
3. Stiskněte **Odeslat**.

    * Odešle se následující operace **PUT**.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
        ```
    * Operace obsahuje následující text:

        ```json
        {
            "properties": {
                "description": "Basic Transform using an Adaptive Streaming encoding preset from the libray of built-in Standard Encoder presets",
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

[Úloha](https://docs.microsoft.com/rest/api/media/jobs) je vlastní požadavek na službu Media Services, aby vytvořenou **transformaci** použila na daný vstupní videoobsah nebo zvukový obsah. **Úloha** určuje informace, jako je umístění vstupního videa a umístění pro výstup.

V tomto příkladu se vstup úlohy vytvoří na základě adresy URL protokolu HTTPS (https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/).

1. V levém okně nástroje Postman vyberte „Encoding and Analysis“ (Kódování a analýza).
2. Pak vyberte „Create or Update Job“ (Vytvořit nebo aktualizovat úlohu).
3. Stiskněte **Odeslat**.

    * Odešle se následující operace **PUT**.

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

Úloze chvíli trvá, než se dokončí, a když k tomu dojde, budete na to pravděpodobně chtít upozornit. K zobrazení průběhu úlohy doporučujeme použít službu Event Grid. Ta je navržená s ohledem na vysokou dostupnost, stabilní výkon a dynamické škálování. Díky službě Event Grid můžou vaše aplikace naslouchat událostem a reagovat na ně, ať už pocházejí z kterékoli služby Azure. Události můžou pocházet i z vlastních zdrojů. Jednoduché, reaktivní zpracování událostí založené na protokolu HTTP pomáhá sestavovat efektivní řešení prostřednictvím inteligentního filtrování a směrování událostí.  Další informace najdete v článku [Směrování událostí na vlastní webový koncový bod](job-state-events-cli-how-to.md).

**Úlohy** obvykle prochází následujících stavů: **Naplánované**, **ve frontě**, **zpracování**, **dokončeno** (konečný stav). Pokud během provádění úlohy dojde k chybě, přejde úloha do stavu **Chyba**. Když úlohu zrušíte, změní se její stav na **Rušení** a potom na **Zrušeno**.

### <a name="create-a-streaming-locator"></a>Vytvoření lokátoru streamování

Po dokončení úlohy kódování následuje zpřístupnění videa ve výstupním prostředku, kde bude k dispozici klientům pro přehrávání. Video můžete zpřístupnit ve dvou krocích: nejdřív vytvořte streamovací lokátor ([StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators)) a pak adresy URL pro streamování, které budou klienti používat. 

Proces vytváření **streamovacího lokátoru** označujeme jako publikování. Pokud nenakonfigurujete volitelný počáteční a koncový čas, je **streamovací lokátor** ve výchozím nastavení platný hned po zavolání rozhraní API a jeho platnost zrušíte až jeho odstraněním. 

Když vytváříte [streamovací lokátor](https://docs.microsoft.com/rest/api/media/streaminglocators), je potřeba zadat požadovaný název zásad streamování (**StreamingPolicyName**). V tomto příkladu budete streamovat čistý nebo také nešifrovaný obsah, takže použijete předdefinovanou zásadu čistého streamování **PredefinedStreamingPolicy.ClearStreamingOnly**.

> [!IMPORTANT]
> Pokud chcete definovat vlastní [zásady streamování](https://docs.microsoft.com/rest/api/media/streamingpolicies), doporučujeme navrhnout pro účet služby Media Service omezený počet takovýchto zásad a používat je opakovaně pro streamovací lokátory, kdykoli potřebujete stejné protokoly a možnosti šifrování. 

Počet záznamů StreamingPolicy je pro účty služby Media Service omezený kvótou. Neměli byste vytvářet samostatnou zásadu streamování pro každý streamovací lokátor.

1. V levém okně nástroje Postman vyberte „Streaming Policies“ (Zásady streamování).
2. Potom vyberte možnost „Create a Streaming Locator“ (Vytvořit lokátor streamování).
3. Stiskněte **Odeslat**.

    * Odešle se následující operace **PUT**.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingPolicies/:streamingPolicyName?api-version={{api-version}}
        ```
    * Operace obsahuje následující text:

        ```json
        {
            "properties":{
            "assetName": "{{assetName}}",
            "streamingPolicyName": "{{streamingPolicyName}}"
            }
        }
        ```

### <a name="list-paths-and-build-streaming-urls"></a>Seznam cest a vytvoření adres URL pro streamování

#### <a name="list-paths"></a>Seznam cest

Teď, když máte vytvořený [streamovací lokátor](https://docs.microsoft.com/rest/api/media/streaminglocators), můžete vytvořit adresy URL pro streamování.

1. V levém okně nástroje Postman vyberte „Streaming Policies“ (Zásady streamování).
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

    Pokud chcete získat název hostitele, můžete provádět následující operace GET:
    
    ```
    https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingEndpoints/default?api-version={{api-version}}
    ```
    
3. Cesta, kterou jste získali v předchozí části (Seznam cest).  

Výsledkem pak je následující adresa URL pro streamování HLS

```
https://amsaccount-usw22.streaming.media.azure.net/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=m3u8-aapl)
```

## <a name="test-the-streaming-url"></a>Testování adresy URL pro streamování


> [!NOTE]
> Zkontrolujte, že je spuštěný koncový bod streamování, ze kterého chcete streamovat.

Tento článek používá k otestování streamu přehrávač Azure Media Player. 

1. Otevřete webový prohlížeč a přejděte na adresu [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Do pole pro **adresu URL** vložte adresu URL, kterou jste vytvořili. 
3. Stiskněte **Update Player** (Aktualizovat přehrávač).

Azure Media Player můžete použít pro účely testování, nesmí se ale používat v produkčním prostředí. 

## <a name="clean-up-resources-in-your-media-services-account"></a>Vyčištění prostředků v účtu služby Media Services

Obecně platí, že byste měli vyčistit všechno kromě objektů, které máte v plánu použít znovu, (obvykle jsou to transformace, streamovací lokátory apod.). Pokud chcete účet po experimentování vyčistit, měli byste odstranit prostředky, které nemáte v plánu znovu použít.  

Provedete to tak, že u prostředku, který chcete odstranit, vyberete operaci „Odstranit…“.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud ze skupiny prostředků už žádné prostředky nepotřebujete, včetně účtu služby Media Services a účtu úložiště, které jste vytvořili v tomto kurzu, pak tuto dříve vytvořenou skupinu prostředků odstraňte.  

Spusťte následující příkaz rozhraní příkazového řádku:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="next-steps"></a>Další postup

Teď, když už víte, jak nahrávat, kódovat a streamovat videa, podívejte se na následující článek: 

> [!div class="nextstepaction"]
> [Analýza videí](analyze-videos-tutorial-with-api.md)
