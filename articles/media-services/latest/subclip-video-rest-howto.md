---
title: Při kódování pomocí Azure Media Services REST můžete video podříznout
description: Toto téma popisuje, jak při kódování pomocí služby Azure Media Services pomocí služby REST podklipovat video.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/10/2019
ms.author: juliako
ms.openlocfilehash: c39aded55fe36cb130459a4f6f119f872b1adbc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514319"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Subclip video při kódování s Media Services - REST

Video můžete oříznout nebo podklipu při kódování pomocí [úlohy](https://docs.microsoft.com/rest/api/media/jobs). Tato funkce funguje s libovolnou [transformací,](https://docs.microsoft.com/rest/api/media/transforms) která je vytvořena pomocí [přednastavení BuiltInStandardEncoder Preset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) nebo [Přednastavení StandardEncoder.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) 

Příklad REST v tomto tématu vytvoří úlohu, která ořízne video při odeslání úlohy kódování. 

## <a name="prerequisites"></a>Požadavky

Chcete-li provést kroky popsané v tomto tématu, musíte:

- [Vytvořte účet Mediálních služeb Azure](create-account-cli-how-to.md).
- [Konfigurace postman pro Azure Media Services REST API volání](media-rest-apis-with-postman.md).
    
    Ujistěte se, že postupujte podle posledního kroku v tématu [Získat Azure AD Token](media-rest-apis-with-postman.md#get-azure-ad-token). 
- Vytvořte transformace a výstupní prostředky. Můžete vidět, jak vytvořit transformace a výstupní prostředky v [kódovat vzdálený soubor na základě adresy URL a streamovat video - REST](stream-files-tutorial-with-rest.md) tutorial.
- Projděte si téma [konceptu kódování.](encoding-concept.md)

## <a name="create-a-subclipping-job"></a>Vytvoření dílčí úlohy

1. V kolekci Postman, kterou jste stáhli, vyberte **Transformovat a úlohy** -> **Vytvořit úlohu s dílčím oříznutím**.
    
    Požadavek **PUT** vypadá takto:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Aktualizujte hodnotu proměnné prostředí "transformName" s názvem transformace. 
1. Vyberte kartu **Body** a aktualizujte "myOutputAsset" s názvem výstupního datového zdroje.

    ```
    {
      "properties": {
        "description": "A Job with transform cb9599fb-03b3-40eb-a2ff-7ea909f53735 and single clip.",
       
        "input": {
          "@odata.type": "#Microsoft.Media.JobInputHttp",
          "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
          "files": [
                "Ignite-short.mp4"
            ],
          "start": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT10S"
          },
          "end": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT40S"
          }
        },
      
        "outputs": [
          {
            "@odata.type": "#Microsoft.Media.JobOutputAsset",
            "assetName": "myOutputAsset"
          }
        ],
        "priority": "Normal"
      }
    }
    ```
1. Stiskněte **Odeslat**.

    Zobrazí **se odpověď** s informacemi o úloze, která byla vytvořena a odeslána a stav úlohy. 

## <a name="next-steps"></a>Další kroky

[Kódování pomocí vlastní transformace](custom-preset-rest-howto.md) 
