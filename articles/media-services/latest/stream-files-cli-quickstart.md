---
title: Stream video soubory pomocí Azure Media Services a Azure CLI | Dokumentace Microsoftu
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
ms.date: 02/19/2019
ms.author: juliako
ms.openlocfilehash: a323cbe4188207fa77525648297b366c9c57121b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61466897"
---
# <a name="quickstart-stream-video-files---cli"></a>Rychlý start: Streamování videosouborů – CLI

Tento rychlý start ukazuje, jak snadno kódování a streamování videa na různých prohlížečích a zařízeních s použitím rozhraní příkazového řádku Azure a Azure Media Services. Můžete určit vstupní obsah pomocí protokolu HTTPS nebo adresy URL SAS nebo cesty k souborům ve službě Azure Blob storage.

V příkladu v tomto článku kóduje obsah, který můžete zpřístupnit přes adresu URL HTTPS. Media Services v3 v současné době nepodporuje blokového kódování prostřednictvím adresy URL HTTPS přenosu.

Na konci tohoto rychlého startu budete Streamovat videa.  

![Přehrávání videa](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-media-services-account"></a>Vytvoření účtu Media Services

Předtím, než můžete šifrovat, kódovat, analyzovat, spravovat a Streamovat multimediální obsah v Azure, budete muset vytvořit účet Media Services. Tento účet musí být přidružený jeden nebo více účtů úložiště.

Váš účet Media Services a všechny přidružené úložiště účty musí být ve stejném předplatném Azure. Doporučujeme použít účty úložiště, které jsou na stejném místě jako účet Media Services a omezit náklady na odchozí přenosy latenci a data.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

```azurecli
az group create -n amsResourceGroup -l westus2
```

### <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure

V tomto příkladu vytvoříme pro obecné účely v2 účet Standard LRS.

Pokud chcete experimentovat s účty úložiště, použijte `--sku Standard_LRS`. Když vybíráte SKU pro produkční prostředí, zvažte použití `--sku Standard_RAGRS`, která poskytuje geografické replikace zajišťuje nepřetržitý chod podniků. Další informace najdete v tématu [účty úložiště](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest).
 
```azurecli
az storage account create -n amsstorageaccount --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```

### <a name="create-an-azure-media-services-account"></a>Vytvoření účtu Azure Media Services

```azurecli
az ams account create --n amsaccount -g amsResourceGroup --storage-account amsstorageaccount -l westus2
```

Můžete získat následujícím způsobem:

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

Následující příkaz rozhraní příkazového řádku Azure spustí výchozí **koncový bod Sstreaming**.

```azurecli
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
```

Můžete získat následujícím způsobem:

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

Pokud koncový bod streamování je již spuštěn, se zobrazí tato zpráva:

```
(InvalidOperation) The server cannot execute the operation in its current state.
```

## <a name="create-a-transform-for-adaptive-bitrate-encoding"></a>Vytvoření transformace pro kódování s adaptivní přenosovou rychlostí

Vytvoření **transformace** konfigurace běžné úlohy kódování nebo analyzovat videa. V tomto příkladu děláme kódování s adaptivní přenosovou rychlostí. Potom odešleme úlohu v rámci transformace, kterou jsme vytvořili. Úloha je žádost o služby Media Services použít transformace daného videa nebo zvukový obsah vstup.

```azurecli
az ams transform create --name testEncodingTransform --preset AdaptiveStreaming --description 'a simple Transform for Adaptive Bitrate Encoding' -g amsResourceGroup -a amsaccount
```

Můžete získat následujícím způsobem:

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

Vytvoření výstupní **Asset** používat jako výstup úlohy kódování.

```azurecli
az ams asset create -n testOutputAssetName -a amsaccount -g amsResourceGroup
```

Můžete získat následujícím způsobem:

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

## <a name="start-a-job-by-using-https-input"></a>Spustit úlohu s použitím protokolu HTTPS vstup

Při odesílání úloh zpracování videa, budete muset zjistit, kde najít vstupního videa Media Services. Jednou z možností je zadat adresu URL HTTPS jako vstup úlohy, jak je znázorněno v tomto příkladu.

Při spuštění `az ams job start`, můžete nastavit popisek na výstupu úlohy. Popisek můžete pak použít k identifikaci prostředku výstupu je pro.

- Pokud přiřadíte hodnotu popisku, nastavte "– výstupní assety k" assetname = label ".
- Pokud nechcete přiřadit hodnotu popisku, nastavte "--výstupní assety k" assetname = ".

  Všimněte si, že se nám přidat "=" k `output-assets`.

```azurecli
az ams job start --name testJob001 --transform-name testEncodingTransform --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' --files 'Ignite-short.mp4' --output-assets testOutputAssetName= -a amsaccount -g amsResourceGroup 
```

Můžete získat následujícím způsobem:

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

Během pěti minut zkontrolujte stav úlohy. To by měl být "dokončeno." To není dokončeno, kontrola znovu za pár minut. Když se dokončí, přejděte k dalšímu kroku a vytvořit **Lokátor streamování**.

```azurecli
az ams job show -a amsaccount -g amsResourceGroup -t testEncodingTransform -n testJob001
```

## <a name="create-a-streaming-locator-and-get-a-path"></a>Vytvořit lokátor streamování a získat cestu

Po dokončení kódování, dalším krokem je video v prostředku výstupu zpřístupnit klientům pro přehrávání. Provedete to tak, nejdřív vytvořte Lokátor streamování. Potom vytvářejte adresy URL, které můžou klienti používat pro streamování.

### <a name="create-a-streaming-locator"></a>Vytvoření lokátoru streamování

```azurecli
az ams streaming-locator create -n testStreamingLocator --asset-name testOutputAssetName --streaming-policy-name Predefined_ClearStreamingOnly  -g amsResourceGroup -a amsaccount 
```

Můžete získat následujícím způsobem:

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

### <a name="get-streaming-locator-paths"></a>Získání datových proudů lokátoru cesty

```azurecli
az ams streaming-locator get-paths -a amsaccount -g amsResourceGroup -n testStreamingLocator
```

Můžete získat následujícím způsobem:

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

Kopírování HTTP live streaming (HLS) cestu. V tomto případě má `/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)`.

## <a name="build-the-url"></a>Vytvoření adresy URL 

### <a name="get-the-streaming-endpoint-host-name"></a>Získat název hostitele koncového bodu streamování

```azurecli
az ams streaming-endpoint list -a amsaccount -g amsResourceGroup -n default
```
Kopírovat `hostName` hodnotu. V tomto případě má `amsaccount-usw22.streaming.media.azure.net`.

### <a name="assemble-the-url"></a>Sestavit adresu URL

"https:// " + &lt;hostName value&gt; + &lt;Hls path value&gt;

Tady je příklad:

`https://amsaccount-usw22.streaming.media.azure.net/7f19e783-927b-4e0a-a1c0-8a140c49856c/ignite.ism/manifest(format=m3u8-aapl)`

## <a name="test-playback-by-using-azure-media-player"></a>Přehrávání testů pomocí Azure Media Playeru

> [!NOTE]
> Pokud hráč je hostovaný na serveru HTTPS, ujistěte se, že počáteční adresa URL s "https".

1. Otevřete webový prohlížeč a přejděte na [ https://aka.ms/azuremediaplayer/ ](https://aka.ms/azuremediaplayer/).
2. V **URL** pole, vložte adresu URL, kterou jste vytvořili v předchozí části. Vložte adresu URL ve formátu technologie Smooth, Dash nebo HLS. Azure Media Player automaticky použije příslušný protokol streamování pro přehrávání na vašem zařízení.
3. Vyberte **aktualizovat Player**.

>[!NOTE]
>Azure Media Player můžete použít pro účely testování, nesmí se ale používat v produkčním prostředí.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete žádné prostředky ve vaší skupině prostředků, včetně Media Services a účty úložiště, které jste vytvořili pro účely tohoto rychlého startu Odstraňte skupinu prostředků.

Spuštěním následujícího příkazu rozhraní příkazového řádku:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="see-also"></a>Další informace najdete v tématech

Zobrazit [úlohy kódy chyb](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Další postup

> [Ukázky rozhraní příkazového řádku](cli-samples.md)
