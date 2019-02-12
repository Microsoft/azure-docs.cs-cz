---
title: Jak získat instanci procesor médií pomocí REST | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit komponentu procesor médií pro kódování, převod formátu, šifrování nebo dešifrování obsahu médií pro Azure Media Services.
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
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: cc9f65caabe7acaf99952692389838b808af0750
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55996334"
---
# <a name="how-to-get-a-media-processor-instance"></a>Jak získat instanci procesoru médií
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Přehled
Procesory médií jsou komponenty, která zpracovává konkrétní videa nebo zpracování zvuku úloh, jako je například kódování, převod formátů, šifrování nebo dešifrování mediálního obsahu. Všechny úlohy, odeslané do Media Services vyžadují procesor médií kódování, šifrování nebo převést videa nebo zvukový obsah. 

## <a name="azure-media-processors"></a>Procesory médií Azure 

V následujícím tématu najdete seznam procesory médií:

* [Kódovací procesory médií](scenarios-and-availability.md#encoding-media-processors)
* [Analytické procesory médií](scenarios-and-availability.md#analytics-media-processors)

>[!NOTE]
>Při přístupu k entity ve službě Media Services, musíte nastavit specifická pole hlaviček a hodnoty v požadavcích HTTP. Další informace najdete v tématu [instalace pro vývoj pro Media Services REST API](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Připojení ke službě Media Services

Informace o tom, jak se připojit k rozhraní API pro AMS, naleznete v tématu [přístup k rozhraní API Azure Media Services pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 


## <a name="get-a-media-processor"></a>Získat procesor médií

Následující volání REST ukazuje, jak získat instanci procesoru media podle názvu (v tomto případě **kodéru Media Encoder Standard**). 

Požadavek:

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
Teď, když víte, jak získat instanci procesor médií, přejděte [postupy kódování prostředku](media-services-rest-get-started.md) článek, který ukazuje, jak kódování prostředku pomocí Media Encoderu Standard.

