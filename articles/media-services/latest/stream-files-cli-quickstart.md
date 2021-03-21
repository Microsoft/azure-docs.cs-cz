---
title: Streamování videosouborů pomocí Azure Media Services CLI
description: Postupujte podle kroků v tomto kurzu a pomocí Azure CLI vytvořte nový účet Azure Media Services, zakódovat soubor a Streamujte ho do Azure Media Player.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: azure media services, stream
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: devx-track-azurecli
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: c78205d7e2b41628de9e8b92c9fa5506e82158cb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954492"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---azure-cli"></a>Kurz: kódování vzdáleného souboru na základě adresy URL a streamování videa – Azure CLI

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

V tomto kurzu se dozvíte, jak pomocí Azure Media Services a Azure CLI snadno kódovat a streamovat videa na nejrůznějších prohlížečích a zařízeních. Vstupní obsah můžete zadat pomocí adres URL protokolu HTTPS nebo SAS nebo cest k souborům v úložišti objektů BLOB v Azure.

Příklad v tomto článku zakóduje obsah, který zpřístupníte přes adresu URL HTTPS. Media Services V3 v současné době nepodporuje kódování bloků přenosu prostřednictvím adres URL protokolu HTTPS.

Na konci tohoto kurzu budete moct streamovat video.  

![Přehrávání videa](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-media-services-account"></a>Vytvoření účtu Media Services

Než budete moct šifrovat, kódovat, analyzovat, spravovat a streamovat mediální obsah v Azure, musíte vytvořit účet Media Services. Tento účet musí být přidružený k jednomu nebo více účtům úložiště.

Váš účet Media Services a všechny přidružené účty úložiště musí být ve stejném předplatném Azure. Doporučujeme používat účty úložiště, které jsou na stejném místě jako účet Media Services, abyste omezili náklady na latenci a výstup dat.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

```azurecli-interactive
az group create -n amsResourceGroup -l westus2
```

### <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure

V tomto příkladu vytvoříme účet General-Purpose v2 úrovně Standard LRS.

Pokud chcete experimentovat s účty úložiště, použijte `--sku Standard_LRS` . Když vybíráte SKU pro produkci, zvažte použití nástroje `--sku Standard_RAGRS` , který zajišťuje geografickou replikaci pro kontinuitu podnikových prostředí. Další informace najdete v tématu [účty úložiště](/cli/azure/storage/account).

```azurecli-interactive
az storage account create -n amsstorageaccount --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```

### <a name="create-an-azure-media-services-account"></a>Vytvoření účtu Azure Media Services

```azurecli-interactive
az ams account create --n amsaccount -g amsResourceGroup --storage-account amsstorageaccount -l westus2
```

Dostanete odpověď takto:

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

## <a name="start-the-streaming-endpoint"></a>Spuštění koncového bodu streamování

Následující příkaz rozhraní příkazového řádku Azure spustí výchozí **koncový bod streamování**.

```azurecli-interactive
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
```

Dostanete odpověď takto:

```
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

Pokud je koncový bod streamování už spuštěný, zobrazí se tato zpráva:

```
(InvalidOperation) The server cannot execute the operation in its current state.
```

## <a name="create-a-transform-for-adaptive-bitrate-encoding"></a>Vytvořit transformaci pro kódování s adaptivní přenosovou rychlostí

Vytvořte **transformaci** , která konfiguruje běžné úlohy pro kódování a analýzu videí. V tomto příkladu máme kódování s adaptivní přenosovou rychlostí. Pak odešleme úlohu s transformací, kterou jsme vytvořili. Úloha je požadavek, aby se Media Services použít transformaci na daný vstup videa nebo zvukového obsahu.

```azurecli-interactive
az ams transform create --name testEncodingTransform --preset AdaptiveStreaming --description 'a simple Transform for Adaptive Bitrate Encoding' -g amsResourceGroup -a amsaccount
```

Dostanete odpověď takto:

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

Vytvořte výstupní **prostředek** , který se použije jako výstup úlohy kódování.

```azurecli-interactive
az ams asset create -n testOutputAssetName -a amsaccount -g amsResourceGroup
```

Dostanete odpověď takto:

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

## <a name="start-a-job-by-using-https-input"></a>Spuštění úlohy pomocí vstupu HTTPS

Když odesíláte úlohy pro zpracování videí, je nutné sdělit Media Services, kde najít vstupní video. Jednou z možností je zadat adresu URL HTTPS jako vstup úlohy, jak je znázorněno v tomto příkladu.

Když spustíte `az ams job start` , můžete pro výstup úlohy nastavit popisek. Pak můžete pomocí popisku identifikovat, k čemu je výstupní Asset.

- Pokud k popisku přiřadíte hodnotu, nastavte '--Output-assets ' na "Asset = Label".
- Pokud k popisku nepřiřadíte hodnotu, nastavte '--Output-assets ' na "Asset =".

  Všimněte si, že přidáme "=" do `output-assets` .

```azurecli-interactive
az ams job start --name testJob001 --transform-name testEncodingTransform --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' --files 'Ignite-short.mp4' --output-assets testOutputAssetName= -a amsaccount -g amsResourceGroup
```

Dostanete odpověď takto:

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

V pěti minutách ověřte stav úlohy. Měla by být "dokončená". Ještě není hotové, zkuste to znovu za několik minut. Až se dokončí, pokračujte na další krok a vytvořte **Lokátor streamování**.

```azurecli-interactive
az ams job show -a amsaccount -g amsResourceGroup -t testEncodingTransform -n testJob001
```

## <a name="create-a-streaming-locator-and-get-a-path"></a>Vytvořit Lokátor streamování a získat cestu

Po dokončení kódování je dalším krokem vytvoření videa ve výstupním prostředku k dispozici pro klienty pro přehrávání. K tomu je třeba nejprve vytvořit Lokátor streamování. Pak Sestavte adresy URL streamování, které můžou klienti používat.

### <a name="create-a-streaming-locator"></a>Vytvoření lokátoru streamování

```azurecli-interactive
az ams streaming-locator create -n testStreamingLocator --asset-name testOutputAssetName --streaming-policy-name Predefined_ClearStreamingOnly  -g amsResourceGroup -a amsaccount 
```

Dostanete odpověď takto:

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

### <a name="get-streaming-locator-paths"></a>Získat cesty lokátoru streamování

```azurecli-interactive
az ams streaming-locator get-paths -a amsaccount -g amsResourceGroup -n testStreamingLocator
```

Dostanete odpověď takto:

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

Zkopírujte cestu k HTTP Live Streaming (HLS). V tomto případě je to `/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)` .

## <a name="build-the-url"></a>Sestavení adresy URL

### <a name="get-the-streaming-endpoint-host-name"></a>Získat název hostitele koncového bodu streamování

```azurecli-interactive
az ams streaming-endpoint list -a amsaccount -g amsResourceGroup -n default
```

Zkopírujte `hostName` hodnotu. V tomto případě je to `amsaccount-usw22.streaming.media.azure.net` .

### <a name="assemble-the-url"></a>Sestavení adresy URL

"https://" + hodnota &lt; cesty pro název hostitele &gt;  +  &lt; HLS&gt;

Tady je příklad:

`https://amsaccount-usw22.streaming.media.azure.net/7f19e783-927b-4e0a-a1c0-8a140c49856c/ignite.ism/manifest(format=m3u8-aapl)`

## <a name="test-playback-by-using-azure-media-player"></a>Přehrávání testů pomocí Azure Media Player

> [!NOTE]
> Pokud je přehrávač hostovaný na serveru HTTPS, ujistěte se, že jste spustili adresu URL s protokolem HTTPS.

1. Otevřete webový prohlížeč a pokračujte na [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. Do pole **Adresa URL** vložte adresu URL, kterou jste vytvořili v předchozí části. Adresu URL můžete vložit ve formátu HLS, pomlčka nebo vyhlazení. Azure Media Player bude automaticky používat vhodný protokol pro streamování pro přehrávání na vašem zařízení.
3. Vyberte **aktualizovat přehrávač**.

>[!NOTE]
>Azure Media Player můžete použít pro účely testování, nesmí se ale používat v produkčním prostředí.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už v rámci skupiny prostředků nepotřebujete žádné prostředky, včetně Media Services a účtů úložiště, které jste pro tento kurz vytvořili, odstraňte skupinu prostředků.

Spusťte tento příkaz rozhraní příkazového řádku Azure:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="next-steps"></a>Další kroky

[Přehled Media Services](media-services-overview.md)
