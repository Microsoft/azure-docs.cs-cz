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
ms.date: 03/01/2019
ms.author: juliako
ms.openlocfilehash: 63d036ea4faaf7e24f337fa3956986d165c84854
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2019
ms.locfileid: "57244331"
---
# <a name="cli-example-create-and-submit-a-job"></a>Příklad rozhraní příkazového řádku: Vytvoření a odeslání úlohy

V Media Services v3 při odesílání úloh zpracování videa, budete muset zjistit, kde najít vstupního videa Media Services. Jednu z možností je zadat adresu URL HTTPS jako úloha vstup (jak je znázorněno v tomto článku). 

## <a name="prerequisites"></a>Požadavky 

[Vytvoření účtu Media Services](../create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Ukázkový skript

Při spuštění `az ams job start`, můžete nastavit popisek na výstupu úlohy. Popisek můžete později použít k identifikaci tohoto prostředku výstupu je pro. 

- Pokud přiřadíte hodnotu popisku, nastavte "– výstupní assety k" assetname = label "
- Pokud není hodnota přiřadit popisek, nastavte "– výstupní assety k" assetname = ".
  Všimněte si, že přidáte "=" k `output-assets`. 

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

## <a name="next-steps"></a>Další postup

Další příklady najdete v tématu [Ukázky Azure CLI](../cli-samples.md).
