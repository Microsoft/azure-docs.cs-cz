---
title: Dílčí klip videa při kódování pomocí REST API služby Azure Media Services
description: Toto téma popisuje, jak dílčího klipu videa při kódování pomocí Azure Media Services pomocí rozhraní REST
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
ms.openlocfilehash: df8c8a4040b4aae4379b4bfe0e9a16337588dd1b
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305003"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Dílčí klip videa při kódování pomocí Media Services – REST

Můžete oříznout nebo dílčího klipu videa, když ho pomocí kódování [úlohy](https://docs.microsoft.com/rest/api/media/jobs). Tato funkce funguje s jakoukoli [transformace](https://docs.microsoft.com/rest/api/media/transforms) , který je sestaven buď pomocí [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) přednastavení, nebo [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) předvolby. 

V příkladu REST v tomto tématu se vytvoří úlohu, která ořízne videa podle odešle úlohu kódování. 

## <a name="prerequisites"></a>Požadavky

K dokončení kroků popsaných v tomto tématu, budete muset:

- [Vytvoření účtu Azure Media Services](create-account-cli-how-to.md).
- [Konfigurace nástroje Postman pro volání REST API služby Azure Media Services](media-rest-apis-with-postman.md).
    
    Ujistěte se, že chcete postupovat podle poslední krok v tomto tématu [získat Azure AD na Token](media-rest-apis-with-postman.md#get-azure-ad-token). 
- Vytvoření transformace a výstupní Assety. Můžete zjistit, jak vytvořit transformace a výstupní Assety [na vzdálený soubor, na základě adresy URL kódování a streamování videa – REST](stream-files-tutorial-with-rest.md) kurzu.
- Zkontrolujte [kódování koncept](encoding-concept.md) tématu.

## <a name="create-a-subclipping-job"></a>Vytvořit úlohu oříznutím

1. V kolekci Postman, kterou jste si stáhli, vyberte **transformuje a úlohy** -> **vytvoření úlohy s Sub oříznutí**.
    
    **UMÍSTIT** žádost vypadá takto:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Aktualizujte hodnotu proměnné prostředí "transformName" s vaším jménem transformace. 
1. Vyberte **tělo** kartě a aktualizovat výstupu "myOutputAsset" název Assetu.

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

    Zobrazí **odpovědi** s informací o úlohu, která byla vytvořena a odeslání a stav úlohy. 

## <a name="next-steps"></a>Další postup

[Kódování s vlastní transformace](custom-preset-rest-howto.md) 
