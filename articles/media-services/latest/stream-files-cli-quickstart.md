---
title: Streamování video souborů pomocí Azure Media Services a azure cli
description: Podle pokynů tohoto kurzu vytvořte nový účet Azure Media Services, zakódujte soubor a streamujte ho do Programu Azure Media Player.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: azure media services, stream
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: ''
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: 91259e10966173cb701b867f5b3ed362112beef3
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382779"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---azure-cli"></a>Kurz: Kódování vzdáleného souboru na základě adresy URL a streamování videa – Azure CLI

Tento kurz ukazuje, jak snadno kódovat a streamovat videa na různých prohlížečích a zařízeních pomocí Azure Media Services a Azure CLI. Vstupní obsah můžete určit pomocí adres URL HTTPS nebo SAS nebo cest k souborům v úložišti objektů Blob Azure.

Příklad v tomto článku kóduje obsah, který zpřístupníte prostřednictvím adresy URL HTTPS. Media Services v3 v současné době nepodporuje kódování blokových přenosů přes adresy URL HTTPS.

Na konci tohoto kurzu budete moci streamovat video.  

![Přehrávání videa](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-media-services-account"></a>Vytvoření účtu Media Services

Než budete moci šifrovat, kódovat, analyzovat, spravovat a streamovat mediální obsah v Azure, musíte vytvořit účet Mediálních služeb. Tento účet musí být přidružen k jednomu nebo více účtům úložiště.

Váš účet Mediální chslužeb a všechny přidružené účty úložiště musí být ve stejném předplatném Azure. Doporučujeme použít účty úložiště, které jsou na stejném místě jako účet Mediální služby k omezení latence a nákladů na odchozí přenos dat.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

```azurecli-interactive
az group create -n amsResourceGroup -l westus2
```

### <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure

V tomto příkladu vytvoříme účet LRS standardu pro obecné účely v2.

Pokud chcete experimentovat s účty `--sku Standard_LRS`úložiště, použijte . Při výběru skladové položky pro `--sku Standard_RAGRS`produkční prostředí zvažte použití aplikace , která poskytuje geografickou replikaci pro kontinuitu provozu. Další informace naleznete v [tématu účty úložiště](/cli/azure/storage/account).

```azurecli-interactive
az storage account create -n amsstorageaccount --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```

### <a name="create-an-azure-media-services-account"></a>Vytvoření účtu Azure Media Services

```azurecli-interactive
az ams account create --n amsaccount -g amsResourceGroup --storage-account amsstorageaccount -l westus2
```

Dostanete odpověď, jako je tento:

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

Následující příkaz Azure CLI spustí výchozí **koncový bod streamování**.

```azurecli-interactive
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
```

Dostanete odpověď, jako je tento:

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

Pokud koncový bod streamování je již spuštěn, zobrazí se tato zpráva:

```
(InvalidOperation) The server cannot execute the operation in its current state.
```

## <a name="create-a-transform-for-adaptive-bitrate-encoding"></a>Vytvoření transformace pro adaptivní kódování přenosové rychlosti

Vytvořte **transformaci pro** konfiguraci běžných úloh pro kódování nebo analýzu videí. V tomto příkladu děláme adaptivní kódování datových toku. Poté odešleme práci v rámci transformace, kterou jsme vytvořili. Úloha je požadavek na media services použít transformaci na daný vstup video nebo audio obsahu.

```azurecli-interactive
az ams transform create --name testEncodingTransform --preset AdaptiveStreaming --description 'a simple Transform for Adaptive Bitrate Encoding' -g amsResourceGroup -a amsaccount
```

Dostanete odpověď, jako je tento:

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

Vytvořte výstupní **datový zdroj,** který se použije jako výstup úlohy kódování.

```azurecli-interactive
az ams asset create -n testOutputAssetName -a amsaccount -g amsResourceGroup
```

Dostanete odpověď, jako je tento:

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

## <a name="start-a-job-by-using-https-input"></a>Zahájení úlohy pomocí vstupu HTTPS

Při odesílání úloh ke zpracování videí je třeba sdělit službě Media Services, kde vstupní video najít. Jednou z možností je zadat adresu URL HTTPS jako vstup úlohy, jak je znázorněno v tomto příkladu.

Při spuštění `az ams job start`můžete nastavit popisek na výstupu úlohy. Potom můžete použít popisek k identifikaci, k čemu je výstupní datový zdroj určen.

- Pokud popisku přiřadíte hodnotu, nastavte příkaz --output-assets na "assetname=label".
- Pokud popisku nepřiřadíte hodnotu, nastavte hodnotu --output-assets na "assetname=".

  Všimněte si, že přidáme "=" do . `output-assets`

```azurecli-interactive
az ams job start --name testJob001 --transform-name testEncodingTransform --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' --files 'Ignite-short.mp4' --output-assets testOutputAssetName= -a amsaccount -g amsResourceGroup
```

Dostanete odpověď, jako je tento:

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

Za pět minut zkontrolujte stav úlohy. Mělo by to být "Hotovo". Ještě to není hotové, za pár minut to zkontrolujte znovu. Po dokončení přejděte k dalšímu kroku a vytvořte **lokátor streamování**.

```azurecli-interactive
az ams job show -a amsaccount -g amsResourceGroup -t testEncodingTransform -n testJob001
```

## <a name="create-a-streaming-locator-and-get-a-path"></a>Vytvoření lokátoru streamování a získání cesty

Po dokončení kódování je dalším krokem zpřístupnění videa ve výstupním datovém zdroji klientům pro přehrávání. Chcete-li to provést, nejprve vytvořte lokátor streamování. Potom sestavte adresy URL streamování, které mohou klienti používat.

### <a name="create-a-streaming-locator"></a>Vytvoření lokátoru streamování

```azurecli-interactive
az ams streaming-locator create -n testStreamingLocator --asset-name testOutputAssetName --streaming-policy-name Predefined_ClearStreamingOnly  -g amsResourceGroup -a amsaccount 
```

Dostanete odpověď, jako je tento:

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

### <a name="get-streaming-locator-paths"></a>Získání cest lokátoru streamování

```azurecli-interactive
az ams streaming-locator get-paths -a amsaccount -g amsResourceGroup -n testStreamingLocator
```

Dostanete odpověď, jako je tento:

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

Zkopírujte cestu http živého streamování (HLS). V tomto případě je `/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)`to .

## <a name="build-the-url"></a>Vytvoření adresy URL

### <a name="get-the-streaming-endpoint-host-name"></a>Získání názvu hostitele koncového bodu streamování

```azurecli-interactive
az ams streaming-endpoint list -a amsaccount -g amsResourceGroup -n default
```

Zkopírujte `hostName` hodnotu. V tomto případě je `amsaccount-usw22.streaming.media.azure.net`to .

### <a name="assemble-the-url"></a>Sestavení adresy URL

Hodnota cesty &lt;"https://&gt; + &lt;" + hostName hodnota Hls&gt;

Tady je příklad:

`https://amsaccount-usw22.streaming.media.azure.net/7f19e783-927b-4e0a-a1c0-8a140c49856c/ignite.ism/manifest(format=m3u8-aapl)`

## <a name="test-playback-by-using-azure-media-player"></a>Testování přehrávání pomocí programu Azure Media Player

> [!NOTE]
> Pokud je hráč hostován na webu HTTPS, nezapomeňte adresu URL spustit pomocí "https".

1. Otevřete webový prohlížeč [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)a přejděte na stránku .
2. Do pole **ADRESA URL** vložte adresu URL, kterou jste vytvořili v předchozí části. Adresu URL můžete vložit ve formátu HLS, Pomlčka nebo Hladká. Azure Media Player bude automaticky používat příslušný protokol streamování pro přehrávání na vašem zařízení.
3. Vyberte **možnost Aktualizovat přehrávač**.

>[!NOTE]
>Azure Media Player můžete použít pro účely testování, nesmí se ale používat v produkčním prostředí.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete žádné prostředky ve skupině prostředků, včetně mediálních služeb a účtů úložiště, které jste vytvořili pro tento kurz, odstraňte skupinu prostředků.

Spusťte tento příkaz příkazového příkazu k příkazu Azure:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="next-steps"></a>Další kroky

[Přehled mediálních služeb](media-services-overview.md)
