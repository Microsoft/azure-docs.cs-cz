---
title: Ukázkový skript Azure CLI – Vytvoření a spuštění úlohy | Microsoft Docs
description: Skript Azure CLI v tomto tématu znázorňuje, jak odeslat úlohu k transformaci s jednoduchým kódováním pomocí adresy URL s protokolem HTTPs.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: 08caf4b78b1042d5753dba2bc73e93fa3a250a17
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "71122380"
---
# <a name="cli-example-create-and-submit-a-job"></a>Ukázka CLI: Vytvoření a odeslání úlohy

Pokud ve službě Media Services v3 odešlete úlohy pro zpracování videí, musíte službu Media Services informovat, kde najde vstupní video. Jednou z možností je zadat adresu URL HTTPS jako vstup úlohy (jak je znázorněno v tomto článku). 

## <a name="prerequisites"></a>Požadavky 

[Vytvořte účet mediálních služeb](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Ukázkový skript

Při spuštění `az ams job start`můžete nastavit popisek na výstupu úlohy. Popisek lze později použít k identifikaci, k čemu je tento výstupní datový zdroj určen. 

- Pokud popisku přiřadíte hodnotu, nastavte příkaz --output-assets na "assetname=label"
- Pokud popisku nepřiřadíte hodnotu, nastavte hodnotu --output-assets na "assetname=".
  Všimněte si, že přidáte `output-assets`"=" do . 

```azurecli
az ams job start \
  --name testJob001 \
  --transform-name testEncodingTransform \
  --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' \
  --files 'Ignite-short.mp4' \
  --output-assets testOutputAssetName= \
  -a amsaccount \
  -g amsResourceGroup 
```

Dostanete odpověď podobnou této:

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

## <a name="next-steps"></a>Další kroky

[az ams práce (CLI)](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
