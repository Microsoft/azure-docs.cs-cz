---
title: Jak vytvořit překryv pomocí Media Encoder Standard
description: Přečtěte si, jak vytvořit překryv pomocí Media Encoder Standard.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 6c93408bce8da9f8cd0e4a0d0bab615e2bd362dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89267322"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>Jak vytvořit překryv pomocí Media Encoder Standard

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Encoder Standard umožňuje překrýt obrázek do existujícího videa. V současné době jsou podporovány následující formáty: PNG, jpg, GIF a BMP.

## <a name="prerequisites"></a>Požadavky

* Shromážděte informace o účtu, které potřebujete ke konfiguraci *appsettings.js* v souboru v ukázce. Pokud si nejste jisti, jak to provést, přečtěte si téma [rychlý Start: registrace aplikace s platformou Microsoft Identity](../../active-directory/develop/quickstart-register-app.md). V *appsettings.js* souboru jsou očekávány následující hodnoty.

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
* Viz [referenční dokument transformací](/rest/api/media/transforms).

Jakmile se seznámíte s transformacemi, Stáhněte si ukázku překryvů.

## <a name="overlays-preset-sample"></a>Překrývá přednastavený vzorek.

Stáhněte si [ukázku Media-Services-překryv](https://github.com/Azure-Samples/media-services-overlays) , abyste mohli začít s překryvy.

## <a name="next-steps"></a>Další kroky

* [Rozříznout video při kódování pomocí Media Services-.NET](subclip-video-dotnet-howto.md)