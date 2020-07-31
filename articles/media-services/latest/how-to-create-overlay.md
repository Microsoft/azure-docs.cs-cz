---
title: Jak vytvořit překryv pomocí Media Encoder Standard
description: Přečtěte si, jak vytvořit překryv pomocí Media Encoder Standard.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 0d1a6d5626e081ff50f65b3a4396e223b33f920d
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2020
ms.locfileid: "87433587"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>Jak vytvořit překryv pomocí Media Encoder Standard

Media Encoder Standard umožňuje překrýt obrázek do existujícího videa. V současné době jsou podporovány následující formáty: PNG, jpg, GIF a BMP.

## <a name="prerequisites"></a>Předpoklady

* Shromážděte informace o účtu, které potřebujete ke konfiguraci *appsettings.js* v souboru v ukázce. Pokud si nejste jisti, jak to provést, přečtěte si téma [rychlý Start: registrace aplikace s platformou Microsoft Identity](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). V *appsettings.js* souboru jsou očekávány následující hodnoty.

    ```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Region": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
    ```

Pokud jste s transformací ještě neseznámili, doporučujeme, abyste dokončili následující činnosti:

* Číst [video a zvuk v kódování pomocí Media Services](encoding-concept.md)
* Přečtěte si, [jak se zakódovat pomocí vlastní transformace – .NET](customize-encoder-presets-how-to.md). Postupujte podle kroků v tomto článku a nastavte rozhraní .NET potřebné pro práci s transformacemi a pak se vraťte sem, abyste si vyzkoušeli ukázku přednastavených překryvů.
* Viz [referenční dokument transformací](https://docs.microsoft.com/rest/api/media/transforms).

Jakmile se seznámíte s transformacemi, Stáhněte si ukázku překryvů.

## <a name="overlays-preset-sample"></a>Překrývá přednastavený vzorek.

Stáhněte si [ukázku Media-Services-překryv](https://github.com/Azure-Samples/media-services-overlays) , abyste mohli začít s překryvy.

## <a name="next-steps"></a>Další kroky

* [Rozříznout video při kódování pomocí Media Services-.NET](subclip-video-dotnet-howto.md)