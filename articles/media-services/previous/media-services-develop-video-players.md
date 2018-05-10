---
title: Vývoj aplikací videopřehrávače
description: Téma obsahuje odkazy na architektury přehrávačů a moduly plug-in, který můžete použít pro vývoj vlastních klientských aplikací, které můžou využívat streamovaná média ze služby Media Services.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 55e419fc-4c39-4902-9c62-f41cfcd86c6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: juliako
ms.openlocfilehash: 71d8b020fe96094c15965fd82615e3182e333990
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="develop-video-player-applications"></a>Vývoj aplikací videopřehrávače
## <a name="overview"></a>Přehled
Služba Azure Media Services nabízí nástroje, které potřebujete k vytvoření dynamických aplikací pro klientské přehrávače pro většinu platforem včetně: zařízení iOS, zařízení Android, Windows, Windows Phone, Xbox a set top boxy. Toto téma obsahuje také odkazy na sady SDK a architektury přehrávačů, můžete použít pro vývoj vlastních klientských aplikací, které můžou využívat streamovaná média ze služby Azure Media Services.

>[!NOTE]
>Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 
 
## <a name="azure-media-player"></a>Přehrávač médií Azure
[Azure Media Player](http://aka.ms/ampinfo) je přehrávání videa webové vytvořená tak, aby přehrání mediálního obsahu z Microsoft Azure Media Services na širokou škálu prohlížečích a zařízeních. Azure Media Player využívá oborové standardy, jako je například HTML5, rozšíření zdrojového média (MSE) a šifrované rozšíření média (EME) vám umožňuje provádět rozšířené adaptivní streamování prostředí. Při těchto standardů nejsou k dispozici v zařízení nebo v prohlížeči, přehrávač médií Azure používá jako záložní technologie Flash a Silverlight. Bez ohledu na to technologie přehrávání používá bude mít vývojáři jednotné rozhraní JavaScript pro přístup k rozhraní API. To umožňuje obsah obsluhuje Azure Media Services má být přehráván napříč celou rozsah zařízení a prohlížeče, které nemají žádné další úsilí.

Microsoft Azure Media Services umožňuje zpracovat s DASH, technologie Smooth Streaming a formátů streamování HLS veškerý obsah k přehrávání obsahu. Azure Media Player bere v úvahu tyto různými formáty a automaticky hraje odkaz doporučené možnostem platformy nebo prohlížeče. Microsoft Azure Media Services také umožňuje dynamické šifrování prostředků s šifrování PlayReady nebo AES-128 bitů obálky šifrování. Azure Media Player umožňuje dešifrování PlayReady a AES-128 bitů šifrovaný obsah, když správně nakonfigurovaný. 

Další informace najdete tady:

* [Přehrávač médií Azure](http://aka.ms/ampinfo)
* [Dokumentace k Azure Media Player](http://aka.ms/ampdocs) 
* [Přehrávač médií Azure získávání spuštění Blog](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
* [Zaregistrujte si nejnovější informace ze přehrávač médií Azure](http://aka.ms/ampsignup)
* [Přidat nové žádosti o funkce, nápady, zpětné vazby](http://aka.ms/ampuservoice) 

## <a name="other-tools-for-creating-player-applications"></a>Další nástroje pro vytváření aplikací pro přehrávání
Také můžete použít některý z následujících sad SDK:

* [Klient funkce Smooth Streaming SDK](http://www.iis.net/downloads/microsoft/smooth-streaming) 
* [Technologie Smooth Streaming aplikace pro Windows Store](media-services-build-smooth-streaming-apps.md)
* [Platforma Microsoft média: Přehrávač Framework](http://playerframework.codeplex.com/) 
* [HTML5 Dokumentaci k rozhraní Framework Player](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
* [Microsoft funkce Smooth Streaming modulu plug-in pro OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
* [Licencování Smooth streamování klienta Microsoft® portování Kit](http://aka.ms/sspk) 
* [Vývoj aplikací Video XBOX](http://xbox.create.msdn.com/) 

## <a name="advertising"></a>Reklama
Azure Media Services poskytuje podporu pro vkládání reklam prostřednictvím platformu Windows Media: architektury přehrávačů. Architektury přehrávačů s podporou ad jsou dostupné pro zařízení s Windows 8, Silverlight, Windows Phone 8 a iOS. Každý player framework obsahuje ukázkový kód, který ukazuje, jak implementovat aplikace přehrávače. Existují tři různé druhy reklamy, které můžete vložit do médií:

Lineární – úplná rámce služby Active Directory, které pozastavit hlavní video

Nelineární – reklamy překrytí, které se zobrazují jako přehrávání videa hlavní, obvykle logo nebo jiné statický obrázek umístěny v rámci prohlížeče

Doprovodná – reklamu, které se zobrazují mimo přehrávač

Služby Active Directory mohou být umístěny v libovolném bodě hlavní video časovou osu. Přehrávač musí zjistit, kdy přehrávání ad a které reklamy přehrávání. To se provádí pomocí sady standardní soubory formátu XML: Video Ad služby šablony (VAST), více Ad seznam stop (VMAP) ve digitální Video, šablony abstraktní sekvencování na média (STOŽÁRŮ) a digitální Video Player Ad rozhraní definice (VPAID). VELKÁ soubory zadejte co zobrazit reklamy. Soubory VMAP určete, kdy k přehrávání různých reklamy a obsahují velká XML. STOŽÁRŮ soubory jsou jiný způsob, jak pořadí služby Active Directory, které také může obsahovat velká XML. Soubory VPAID definovat rozhraní mezi přehrávání videa a ad nebo serveru služby ad. Další informace najdete v tématu [vkládání reklam](https://msdn.microsoft.com/library/dn387398.aspx).

Informace o titulků a podpoře služby Active Directory v živé streamování videa najdete v tématu [podporované titulky a standardy vložení Ad](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Vložení videa adaptivního streamování MPEG-DASH do aplikace HTML5 se souborem DASH.js](media-services-embed-mpeg-dash-in-html5.md)

[Dash.js úložiště GitHub](https://github.com/Dash-Industry-Forum/dash.js)

