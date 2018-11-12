---
title: Vývoj aplikací videopřehrávače
description: Téma obsahuje odkazy na přehrávačů a moduly plug-in, které můžete použít pro vývoj vlastních klientských aplikací, které můžou využívat streamovaná média ze služby Media Services.
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
ms.openlocfilehash: d7f55022dc526f44251814ac953787d6652e6f90
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51237039"
---
# <a name="develop-video-player-applications"></a>Vývoj aplikací videopřehrávače
## <a name="overview"></a>Přehled
Služba Azure Media Services nabízí nástroje, které potřebujete k vytvoření dynamických aplikací pro klientské přehrávače pro většinu platforem včetně: zařízení iOS, zařízení Android, Windows, Windows Phone, Xbox a set top boxy. Toto téma obsahuje také odkazy na sady SDK a rozhraní přehrávačů, můžete použít pro vývoj vlastních klientských aplikací, které můžou využívat streamovaná média ze služby Azure Media Services.

>[!NOTE]
>Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 
 
## <a name="azure-media-player"></a>Přehrávač médií Azure
[Azure Media Player](https://aka.ms/ampinfo) je web přehrávač videa navržená tak, aby přehrávání mediálního obsahu z Microsoft Azure Media Services na širokou škálu prohlížečů a zařízení. Azure Media Player využívá oborové standardy, jako je HTML5, rozšíření zdroj média (MSE) a rozšíření eme (Encrypted Media Extensions), k poskytování bohatších možností adaptivního streamování. Pokud tyto standardy nejsou k dispozici na zařízení nebo v prohlížeči, Azure Media Player používá jako záložní technologie Flash a Silverlight. Bez ohledu na technologii přehrávání použity budou mít vývojáři jednotné rozhraní JavaScript pro přístup k rozhraní API. To umožňuje obsah poskytovaný Azure Media Services přehrávat napříč celou řadu zařízení a přehrávačů, aniž by vyžadovalo zvláštní úsilí.

Microsoft Azure Media Services umožňuje obsah tak, aby se poskytovat, než se DASH, Smooth Streaming a HLS streaming formáty přehrávání obsahu. Azure Media Player bere v úvahu tyto různé formáty a automaticky hraje nejlepší odkaz založené na možnostech platformy a prohlížeče. Microsoft Azure Media Services také umožňuje dynamické šifrování prostředků pomocí šifrování PlayReady nebo 128bitového šifrování AES envelope šifrování. Azure Media Player umožňuje k dešifrování PlayReady a AES-128 bitů šifrovaný obsah, když správně nakonfigurovaný. 

Další informace najdete tady:

* [Přehrávač médií Azure](https://aka.ms/ampinfo)
* [Dokumentace ke službě Azure Media Player](https://aka.ms/ampdocs) 
* [Azure Media Player získávání spuštění blogu](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
* [Zaregistrujte a dostávat nejnovější informace od Azure Media Playeru](https://aka.ms/ampsignup)
* [Přidat nové žádosti o funkce, nápady, zpětnou vazbu](https://aka.ms/ampuservoice) 

## <a name="other-tools-for-creating-player-applications"></a>Další nástroje pro vytváření aplikací pro přehrávání
Můžete také použít některý z následujících sad SDK:

* [SDK klienta Smooth Streaming](http://www.iis.net/downloads/microsoft/smooth-streaming) 
* [Technologie Smooth Streaming aplikace Windows Store](media-services-build-smooth-streaming-apps.md)
* [Médií platforma: Architekturu přehrávače](http://playerframework.codeplex.com/) 
* [HTML5 Dokumentace k rozhraní Framework přehrávač](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
* [Microsoft Smooth Streaming pro OSMF modulu plug-in](https://www.microsoft.com/download/details.aspx?id=36057) 
* [Licencování Microsoft® technologie Smooth Streaming portování klienta](https://aka.ms/sspk) 
* [Vývoj aplikací videa XBOX](https://xbox.create.msdn.com/) 

## <a name="advertising"></a>Inzerce a reklama
Azure Media Services poskytuje podporu pro vkládání reklam prostřednictvím platformy Windows Media: přehrávačů. Přehrávačů s podporou ad jsou k dispozici pro zařízení s Windows 8, Silverlight, Windows Phone 8 a iOS. Každý player framework obsahuje ukázkový kód, který ukazuje, jak implementovat aplikace přehrávače. Existují tři různé druhy reklamy, které lze vložit do médií:

Lineární – úplné rámce reklamy, které pozastavit video hlavní

Nelineárních – překrytí reklamy, které jsou zobrazeny, protože přehrávání videa hlavní, obvykle logo nebo jiný statický obrázek umístěny v přehrávači

Doprovodná – reklamy, které se zobrazují mimo přehrávači

Služby Active Directory je možné použít v libovolném bodě hlavní video časové ose. Hráč musí zjistit, kdy se má přehrát ad a které reklamy přehrávání. To se provádí pomocí sady standardních souborů založený na formátu XML: Video Ad Service šablony (VAST), několik Ad seznamu testů (VMAP) ve digitální Video, šablona abstraktní pořadí úloh na média (STOŽÁRŮ) a digitální Video přehrávač Ad rozhraní definice (VPAID). ROZSÁHLÉ soubory určují jaké služby Active Directory k zobrazení. Soubory VMAP určují, kdy přehrát různých reklamy a obsahovat OBROVSKÉ XML. Dalším způsobem, jak pořadí reklamy, které také mohou obsahovat OBROVSKÉ XML jsou STOŽÁRŮ soubory. Soubory VPAID definují rozhraní mezi přehrávače videa a ad nebo serveru služby ad. Další informace najdete v tématu [vkládání reklam](https://msdn.microsoft.com/library/dn387398.aspx).

Informace o skryté titulky a podpora služby Active Directory v živé streamování videa najdete v tématu [podporované titulky a standardy vložení Ad](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Vložení videa adaptivního streamování MPEG-DASH do aplikace HTML5 se souborem DASH.js](media-services-embed-mpeg-dash-in-html5.md)

[Úložiště GitHub souborem dash.js](https://github.com/Dash-Industry-Forum/dash.js)

