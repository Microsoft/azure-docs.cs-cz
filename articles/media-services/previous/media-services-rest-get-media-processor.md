---
title: Jak získat procesor médií instanci pomocí REST | Microsoft Docs
description: Naučte se vytvořit komponentu procesoru média zakódovat, převést formát, šifrování nebo dešifrování obsahu médií pro službu Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: f9ff1997-0da6-4528-aaed-792837e5be41
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: juliako
ms.openlocfilehash: 3e0bd654deca9db8ac13f4af9c4732ba42c01e97
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-get-a-media-processor-instance"></a>Jak získat instanci procesor médií
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Přehled
Procesory médií jsou komponenty, která zpracovává konkrétní video nebo zvuk zpracování úloh, třeba kódování, převod formátu, šifrování nebo dešifrování objektu mediální obsah. Všechny úlohy, odeslané ke službě Media Services vyžadují kódování, šifrování nebo převod obsahu video nebo zvuk procesor médií. 

## <a name="azure-media-processors"></a>Procesory médií Azure 

Následující téma obsahuje seznam procesory médií:

* [Procesory médií z kódování](scenarios-and-availability.md#encoding-media-processors)
* [Procesory médií Analytics](scenarios-and-availability.md#analytics-media-processors)

>[!NOTE]
>Při přístupu k entity ve službě Media Services, musíte nastavit specifická pole hlaviček a hodnoty ve své žádosti HTTP. Další informace najdete v tématu [instalační program pro Media Services REST API vývoj](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Připojení ke službě Media Services

Informace o tom, jak připojit k rozhraní API pro AMS najdete v tématu [přístup k Azure Media Services API pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 


## <a name="get-a-media-processor"></a>Získat procesor médií

Toto volání REST ukazuje, jak získat instance procesoru média podle názvu (v tomto případě **Media Encoder Standard**). 

Žádost:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.17
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


## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další kroky
Teď, když víte, jak získat instance procesoru média, přejděte na [postup kódovat Asset](media-services-rest-get-started.md) článek, která demonstruje použití Media Encoder Standard kódování assetu.

