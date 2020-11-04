---
title: Jak získat instanci procesoru médií pomocí REST | Microsoft Docs
description: Naučte se vytvořit komponentu procesoru médií ke kódování, převedení formátu, šifrování nebo dešifrování mediálního obsahu pro Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: f9ff1997-0da6-4528-aaed-792837e5be41
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2020
ms.author: inhenkel
ms.openlocfilehash: e1ac2a2408e16c3f8057c49c69c354ebb1e377c7
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348487"
---
# <a name="how-to-get-a-media-processor-instance"></a>Jak získat instanci procesoru multimédií

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)


## <a name="overview"></a>Přehled

Procesory médií jsou součástí, která zpracovává konkrétní úlohu zpracování videa nebo zvuku, jako je například kódování, převod formátu, šifrování nebo dešifrování mediálního obsahu. Všechny úlohy odeslané do Media Services vyžadují, aby procesor médií mohl kódování, šifrování nebo převod zvukového nebo zvukového obsahu.

## <a name="azure-media-processors"></a>Procesory médií Azure

V následujícím tématu najdete seznam procesorů médií:

* [Kódovací procesory médií](scenarios-and-availability.md)
* [Analytické procesory médií](scenarios-and-availability.md)

>[!NOTE]
>Při přístupu k entitám v Media Services musíte nastavit konkrétní pole a hodnoty hlaviček v požadavcích HTTP. Další informace najdete v tématu [instalace Media Services REST APIm vývoji](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Připojení ke službě Media Services

Informace o tom, jak se připojit k rozhraní API AMS, najdete v tématu [přístup k rozhraní Azure Media Services API pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 


## <a name="get-a-media-processor"></a>Získat procesor médií

Následující volání REST ukazuje, jak získat instance procesoru médií podle názvu (v tomto případě **Media Encoder Standard** ). 

Požadavek:

```console
GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <token>
x-ms-version: 2.19
Host: media.windows.net
```

Odpověď:

```console
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
```


## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další kroky
Když teď víte, jak získat instanci procesoru médií, přečtěte si článek [Postup kódování assetu](media-services-rest-get-started.md) , který ukazuje, jak použít Media Encoder Standard ke kódování assetu.

