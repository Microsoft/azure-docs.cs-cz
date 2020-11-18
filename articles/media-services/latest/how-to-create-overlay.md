---
title: Jak vytvořit překryv pomocí Media Encoder Standard
description: Přečtěte si, jak vytvořit překryv pomocí Media Encoder Standard.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 743fe146042c7b52394cc4ee8ced49a0f540e79c
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844280"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>Jak vytvořit překryv pomocí Media Encoder Standard

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Encoder Standard umožňuje překrytí obrázku, zvukového souboru nebo jiného videa na jiné video. Vstup musí určovat přesně jeden soubor. Můžete určit soubor obrázku ve formátu JPG, PNG, GIF nebo BMP nebo zvukový soubor (například WAV, MP3, WMA nebo M4A) nebo videosoubor.


## <a name="prerequisites"></a>Předpoklady

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
    "Location": "",
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
