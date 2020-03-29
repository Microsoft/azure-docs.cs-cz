---
title: Jak získat instanci mediálního procesoru pomocí REST | Dokumenty společnosti Microsoft
description: Přečtěte si, jak vytvořit komponentu mediálního procesoru pro kódování, převod formátu, šifrování nebo dešifrování mediálního obsahu pro Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: f9ff1997-0da6-4528-aaed-792837e5be41
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 3d7b3922c9bb7eb79cd6436ba1b265714678fcc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774911"
---
# <a name="how-to-get-a-media-processor-instance"></a>Jak získat instanci mediálního procesoru
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [Odpočinku](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Přehled
Mediální procesory jsou součástí, která zpracovává určitou úlohu zpracování videa nebo zvuku, jako je kódování, převod formátu, šifrování nebo dešifrování mediálního obsahu. Všechny úkoly odeslané do služby Media Services vyžadují k kódování, šifrování nebo převodu obsahu videa nebo zvuku mediální procesor. 

## <a name="azure-media-processors"></a>Mediální procesory Azure 

Následující téma obsahuje seznam mediálních procesorů:

* [Kódovací procesory médií](scenarios-and-availability.md#encoding-media-processors)
* [Analytické procesory médií](scenarios-and-availability.md#analytics-media-processors)

>[!NOTE]
>Při přístupu k entitám ve službě Media Services je nutné nastavit konkrétní pole záhlaví a hodnoty v požadavcích HTTP. Další informace naleznete [v tématu Setup for Media Services REST API Development](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Připojení ke službě Media Services

Informace o tom, jak se připojit k rozhraní AMS API, najdete [v tématu Přístup k rozhraní API Azure Media Services pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 


## <a name="get-a-media-processor"></a>Získání mediálního procesoru

Následující volání REST ukazuje, jak získat instanci mediálního procesoru podle názvu (v tomto případě **Standard kodéru médií).** 

Požadavek:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.19
    Host: media.windows.net

Odpověď:

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }


## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další kroky
Teď, když víte, jak získat instanci mediálního procesoru, přejděte do článku [Jak zakódovat datový zdroj,](media-services-rest-get-started.md) který ukazuje, jak použít standard kodéru médií ke kódování datového zdroje.

