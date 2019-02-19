---
title: Stream video soubory pomocí Azure Media Services – rozhraní příkazového řádku | Dokumentace Microsoftu
description: Pomocí kroků v tomto rychlém startu můžete vytvořit nový účet služby Azure Media Services, zakódovat soubor a streamovat ho do Azure Media Playeru.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: azure media services, stream
ms.service: media-services
ms.workload: media
ms.topic: quickstart
ms.custom: ''
ms.date: 02/15/2019
ms.author: juliako
ms.openlocfilehash: c0b1f3fb854f4ca553d24ed601749cf91c2b5f28
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56339800"
---
# <a name="quickstart-stream-video-files---cli"></a>Rychlý start: Streamování videosouborů – CLI

Toto rychlé zprovoznění předvádí, jak je jednoduché kódovat a začít se streamováním videí pomocí služby Azure Media Services v široké škále prohlížečů a zařízení. Vstupní obsah se dá specifikovat jako HTTPS, URL, SAS nebo cesta k souboru v úložišti objektů Blob Azure.
Ukázka v tomto tématu kóduje obsah, který zpřístupníte prostřednictvím adresy URL protokolu HTTPS. AMS v3 v současné době nepodporuje blokového kódování přenosu prostřednictvím adresy URL HTTPS.

Na konci rychlého startu budete schopni streamovat video.  

![Přehrávání videa](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-media-services-account"></a>Vytvoření účtu Media Services

Pokud chcete začít, šifrování, kódování, analýza, Správa a streamování médií obsahu v Azure, budete muset vytvořit účet Media Services. Účet Media Services musí být přidružený jeden nebo více účtů úložiště.

Účet Media Services a všechny přidružené úložiště účty musí být ve stejném předplatném Azure. Důrazně doporučujeme používat účty úložiště ve stejném umístění jako účet Media Services, aby se zabránilo další latenci a data náklady na odchozí přenosy.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

```azurecli
az group create -n amsResourceGroup -l westus2
```

### <a name="create-an-azure-storage-account-general-purpose-v2-standard-ragrs"></a>Vytvoření účtu služby azure storage, obecné účely v2, standardní RAGRS

```azurecli
az storage account create -n amsstorageaccount --kind StorageV2 --sku Standard_RAGRS -l westus2 -g amsResourceGroup
```

### <a name="create-an-azure-media-service-account"></a>Vytvoření účtu služby azure media

```azurecli
az ams account create --n amsaccount -g amsResourceGroup --storage-account amsstorageaccount -l westus2
```

```
{
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
  "location": "West US 2",
  "mediaServiceId": "8b569c2e-d648-4fcb-9035-c7fcc3aa7ddf",
  "name": "amsaccount",
  "resourceGroup": "amsResourceGroupTest",
  "storageAccounts": [
    {
      "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Storage/storageAccounts/amsstorageaccount",
      "resourceGroup": "amsResourceGroupTest",
      "type": "Primary"
    }
  ],
  "tags": null,
  "type": "Microsoft.Media/mediaservices"
}
```

## <a name="start-streaming-endpoint"></a>Spuštění koncového bodu streamování

Rozhraní příkazového řádku následující spustí výchozí **koncový bod streamování**.

```azurecli
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
```

Jakmile je spuštěno, získáte odpovědi podobně jako tato:

```
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
{
  "accessControl": null,
  "availabilitySetName": null,
  "cdnEnabled": true,
  "cdnProfile": "AzureMediaStreamingPlatformCdnProfile-StandardVerizon",
  "cdnProvider": "StandardVerizon",
  "created": "2019-02-06T21:58:03.604954+00:00",
  "crossSiteAccessPolicies": null,
  "customHostNames": [],
  "description": "",
  "freeTrialEndTime": "2019-02-21T22:05:31.277936+00:00",
  "hostName": "amsaccount-usw22.streaming.media.azure.net",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingendpoints/default",
  "lastModified": "2019-02-06T21:58:03.604954+00:00",
  "location": "West US 2",
  "maxCacheAge": null,
  "name": "default",
  "provisioningState": "Succeeded",
  "resourceGroup": "amsResourceGroup",
  "resourceState": "Running",
  "scaleUnits": 0,
  "tags": {},
  "type": "Microsoft.Media/mediaservices/streamingEndpoints"
}
```

Pokud koncový bod streamování je již spuštěn, můžete získat

```
(InvalidOperation) The server cannot execute the operation in its current state.
```

## <a name="create-a-transform-for-adaptive-bitrate-encoding"></a>Vytvoření transformace pro kódování adaptivní přenosové rychlosti

Vytvoření **transformace** konfigurace běžné úlohy kódování nebo analyzovat videa. V tomto příkladu chceme udělat s adaptivní přenosovou rychlostí kódování. Pak bude odešlete **úlohy** pod transformací, která jste vytvořili. Úloha je skutečnou žádost služby Media Services transformací, která platí pro daného vstupního videa nebo zvukový obsah.

```azurecli
az ams transform create --name testEncodingTransform --preset AdaptiveStreaming --description 'a simple Transform for Adaptive Bitrate Encoding' -g amsResourceGroup -a amsaccount
```

Získejte odpovědi podobně jako tato:

```
{
  "created": "2019-02-15T00:11:18.506019+00:00",
  "description": "a simple Transform for Adaptive Bitrate Encoding",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform",
  "lastModified": "2019-02-15T00:11:18.506019+00:00",
  "name": "testEncodingTransform",
  "outputs": [
    {
      "onError": "StopProcessingJob",
      "preset": {
        "odatatype": "#Microsoft.Media.BuiltInStandardEncoderPreset",
        "presetName": "AdaptiveStreaming"
      },
      "relativePriority": "Normal"
    }
  ],
  "resourceGroup": "amsResourceGroup",
  "type": "Microsoft.Media/mediaservices/transforms"
}
```

## <a name="create-an-output-asset"></a>Vytvoření výstupního prostředku

Vytvoří výstup **Asset** , který se používá jako výstup úlohy kódování.

```azurecli
az ams asset create -n testOutputAssetName -a amsaccount -g amsResourceGroup
```

Získejte odpovědi podobně jako tato:

```
{
  "alternateId": null,
  "assetId": "96427438-bbce-4a74-ba91-e38179b72f36",
  "container": null,
  "created": "2019-02-14T23:58:19.127000+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/assets/testOutputAssetName",
  "lastModified": "2019-02-14T23:58:19.127000+00:00",
  "name": "testOutputAssetName",
  "resourceGroup": "amsResourceGroup",
  "storageAccountName": "amsstorageaccount",
  "storageEncryptionFormat": "None",
  "type": "Microsoft.Media/mediaservices/assets"
}
```

## <a name="start-job-with-https-input"></a>Spuštění úlohy se vstupem HTTPS

V Media Services v3 při odesílání úloh zpracování videa, budete muset zjistit, kde najít vstupního videa Media Services. Jednu z možností je zadat adresu URL HTTPS jako úloha vstup (jak je znázorněno v tomto příkladu). 

```azurecli
az ams job start --name testJob001 --transform-name testEncodingTransform --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' --files 'Ignite-short.mp4' --output-assets testOutputAssetName= -a amsaccount -g amsResourceGroup 
```

> [!TIP]
> Všimněte si, že budete muset přidat "=" k názvu výstupní assety i v případě, že vlastnost popisek nemají nastavený na úloze výstupy.

Získejte odpovědi podobně jako tato:

```
{
  "correlationData": {},
  "created": "2019-02-15T05:08:26.266104+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform/jobs/testJob001",
  "input": {
    "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
    "files": [
      "Ignite-short.mp4"
    ],
    "label": null,
    "odatatype": "#Microsoft.Media.JobInputHttp"
  },
  "lastModified": "2019-02-15T05:08:26.266104+00:00",
  "name": "testJob001",
  "outputs": [
    {
      "assetName": "testOutputAssetName",
      "error": null,
      "label": "",
      "odatatype": "#Microsoft.Media.JobOutputAsset",
      "progress": 0,
      "state": "Queued"
    }
  ],
  "priority": "Normal",
  "resourceGroup": "amsResourceGroup",
  "state": "Queued",
  "type": "Microsoft.Media/mediaservices/transforms/jobs"
}
```

### <a name="check-status"></a>Zkontrolování stavu

V 5 minut zkontrolujte stav úlohy. To by měl být "dokončeno". Není, vrátit se změnami ještě pár minut. Po "Dokončení", přejděte k dalšímu kroku a vytvořit **Lokátor streamování**.

```azurecli
az ams job show -a amsaccount -g amsResourceGroup -t testEncodingTransform -n testJob001
```

## <a name="create-streaming-locator-and-get-path"></a>Vytvořit lokátor streamování a získat cestu

Po dokončení kódování, dalším krokem je video v prostředku výstupu zpřístupnit klientům pro přehrávání. Můžete to provést ve dvou krocích: nejdřív vytvořte **Lokátor streamování**a druhý, sestavení adresy URL pro streamování, které můžou klienti používat.

### <a name="create-a-streaming-locator"></a>Vytvořit lokátor streamování

```azurecli
az ams streaming-locator create -n testStreamingLocator --asset-name testOutputAssetName --streaming-policy-name Predefined_ClearStreamingOnly  -g amsResourceGroup -a amsaccount 
```

Získejte odpovědi podobně jako tato:

```
{
  "alternativeMediaId": null,
  "assetName": "output-3b6d7b1dffe9419fa104b952f7f6ab76",
  "contentKeys": [],
  "created": "2019-02-15T04:35:46.270750+00:00",
  "defaultContentKeyPolicyName": null,
  "endTime": "9999-12-31T23:59:59.999999+00:00",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingLocators/testStreamingLocator",
  "name": "testStreamingLocator",
  "resourceGroup": "amsResourceGroup",
  "startTime": null,
  "streamingLocatorId": "e01b2be1-5ea4-42ca-ae5d-7fe704a5962f",
  "streamingPolicyName": "Predefined_ClearStreamingOnly",
  "type": "Microsoft.Media/mediaservices/streamingLocators"
}
```

### <a name="get-streaming-locator-paths"></a>Získání cesty Lokátor pro streamování

```azurecli
az ams streaming-locator get-paths -a amsaccount -g amsResourceGroup -n testStreamingLocator
```

Získejte odpovědi podobně jako tato:

```
{
  "downloadPaths": [],
  "streamingPaths": [
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)"
      ],
      "streamingProtocol": "Hls"
    },
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=mpd-time-csf)"
      ],
      "streamingProtocol": "Dash"
    },
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest"
      ],
      "streamingProtocol": "SmoothStreaming"
    }
  ]
}
```

Zkopírujte cestu Hls. V tomto případě: `/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)`.

## <a name="build-url"></a>Sestavit adresu URL 

### <a name="get-streaming-endpoint-host-name"></a>Získat název hostitele koncového bodu streamování

```azurecli
az ams streaming-endpoint list -a amsaccount -g amsResourceGroup -n default
```

Kopírovat `hostName` hodnotu. V tomto případě: `amsaccount-usw22.streaming.media.azure.net`.

### <a name="assemble-url"></a>Sestavit adresu URL

"https:// " + &lt;hostName value&gt; + &lt;Hls path value&gt;

#### <a name="example"></a>Příklad:

`https://amsaccount-usw22.streaming.media.azure.net/7f19e783-927b-4e0a-a1c0-8a140c49856c/ignite.ism/manifest(format=m3u8-aapl)`

## <a name="play-back-with-azure-media-player"></a>Přehrávání pomocí Azure Media Playeru

Tento článek používá k otestování streamu přehrávač Azure Media Player. 

> [!NOTE]
> Pokud se přehrávač hostuje na webu HTTPS, nezapomeňte adresu URL aktualizovat tak, aby obsahovala „https“. 

1. Otevřete webový prohlížeč a přejděte na adresu [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. V **adresy URL:** pole, vložte adresu URL, kterou jste vytvořili v předchozí části. 
3. Stiskněte **Update Player** (Aktualizovat přehrávač).

Azure Media Player můžete použít pro účely testování, nesmí se ale používat v produkčním prostředí. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud ze skupiny prostředků už žádné prostředky nepotřebujete, včetně účtu služby Media Services a účtů úložiště, které jste vytvořili v rámci tohoto rychlého startu, odstraňte tuto skupinu prostředků.

Spusťte následující příkaz rozhraní příkazového řádku:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Ukázky rozhraní příkazového řádku](cli-samples.md)
