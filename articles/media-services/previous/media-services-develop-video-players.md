---
title: Vývoj aplikací videopřehrávače
description: Téma obsahuje odkazy na rozhraní přehrávače a moduly plug-in, které můžete použít k vývoji vlastních klientských aplikací, které mohou využívat mediální datové proudy z Media Services.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 55e419fc-4c39-4902-9c62-f41cfcd86c6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 39459267919747ed49e9fa3f05746294eaf741dc
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906823"
---
# <a name="develop-video-player-applications"></a>Vývoj aplikací videopřehrávače
## <a name="overview"></a>Přehled
Služba Azure Media Services nabízí nástroje, které potřebujete k vytvoření dynamických aplikací pro klientské přehrávače pro většinu platforem včetně: zařízení iOS, zařízení Android, Windows, Windows Phone, Xbox a set top boxy. Toto téma obsahuje také odkazy na sady SDK a architektury přehrávačů, které můžete použít k vývoji vlastních klientských aplikací, které mohou využívat mediální datové proudy z Azure Media Services.

>[!NOTE]
>Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 
 
## <a name="azure-media-player"></a>Azure Media Player
[Azure Media Player](https://aka.ms/ampinfo) je webový přehrávač, který je založený na přehrávání mediálního obsahu z Microsoft Azure Media Services na nejrůznějších prohlížečích a zařízeních. Azure Media Player využívá oborové standardy, jako je HTML5, Media source Extensions (MSE) a rozšíření EME (Encrypted Media Extensions), aby poskytovala obohacené možnosti adaptivního streamování. Pokud tyto standardy nejsou k dispozici v zařízení nebo v prohlížeči, Azure Media Player používá jako záložní technologii Flash a Silverlight. Bez ohledu na použitou technologii přehrávání budou mít vývojáři sjednocené rozhraní JavaScript pro přístup k rozhraním API. To umožňuje, aby se obsah sloužil Azure Media Services, aby se hrál v široké škále zařízení a prohlížečů bez dalšího úsilí.

Microsoft Azure Media Services umožňuje, aby se obsah nahrál pomocí POMLČEK, Smooth Streaming a formátů HLS streamování, aby se mohl přehrávat obsah. Azure Media Player brát v úvahu tyto různé formáty a automaticky hraje nejlepší odkaz na základě schopností platforem a prohlížečů. Microsoft Azure Media Services taky umožňuje dynamické šifrování assetů pomocí šifrování PlayReady nebo šifrování obálek s kódováním AES-128. Azure Media Player umožňuje dešifrování šifrovaného obsahu PlayReady a AES-128, když je správně nakonfigurovaný. 

Další informace:

* [Přehrávač médií Azure](https://aka.ms/ampinfo)
* [Dokumentace k Azure Media Player](https://aka.ms/ampdocs) 
* [Blog Azure Media Player Začínáme](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
* [Zaregistrujte se, abyste měli přehled o nejnovější verzi z Azure Media Player](https://aka.ms/ampsignup)
* [Přidávání nových žádostí o funkce, nápadů, zpětné vazby](https://aka.ms/ampuservoice) 

## <a name="other-tools-for-creating-player-applications"></a>Další nástroje pro vytváření aplikací pro Player
Můžete také použít kteroukoli z následujících sad SDK:

* [Sada SDK pro Smooth Streaming klienta](https://www.iis.net/downloads/microsoft/smooth-streaming) 
* [Smooth Streaming aplikace pro Windows Store](media-services-build-smooth-streaming-apps.md)
* [Platforma Microsoft Media: rozhraní přehrávače](https://playerframework.codeplex.com/) 
* [Dokumentace k rozhraní přehrávače HTML5](https://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
* [Modul plug-in Microsoft Smooth Streaming pro OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
* [Licencování sady Microsoft® Smooth Streaming klientských portů](https://aka.ms/sspk) 
* [Vývoj aplikací pro video XBOX](https://www.xbox.com/en-US/developers) 

## <a name="advertising"></a>Reklama
Azure Media Services poskytuje podporu pro vkládání reklam prostřednictvím platformy Windows Media: rozhraní přehrávače. Pro zařízení se systémem Windows 8, Silverlight, Windows Phone 8 a iOS jsou k dispozici rozhraní přehrávače, která podporují službu AD. Každé rozhraní přehrávače obsahuje vzorový kód, který ukazuje, jak implementovat aplikaci přehrávače. Existují tři různé druhy reklam, které můžete vložit do svého média:

Lineární – fulltextové reklamy, které pozastaví hlavní video

Nelineární – překryvné reklamy zobrazené při přehrávání hlavního videa, obvykle logo nebo jiný statický obrázek umístěný v přehrávači

Doprovodné – reklamy, které se zobrazují mimo přehrávač

Reklamy lze umístit do libovolného místa v časovém øádku hlavního videa. Je nutné sdělit přehrávači, kdy má prohrát reklamu a které reklamy se mají přehrát. K tomu je potřeba použít sadu standardních souborů XML: šablona služby video AD (Velká část), digitální video (VMAP), šablony abstraktního sekvencování médií (MAST) a definice rozhraní Digital Video Player (VPAID). OBROVSKÉ soubory určují, jaké reklamy se mají zobrazit. Soubory VMAP určují, kdy se mají přehrávat různé reklamy, a obsahují velké množství XML. Soubory MAST představují jiný způsob, jak sesekvencit reklamy, které mohou obsahovat také obrovské XML. Soubory VPAID definují rozhraní mezi přehrávačem videa a serverem AD nebo AD. Další informace najdete v tématu [vkládání reklam](https://msdn.microsoft.com/library/dn387398.aspx).

Informace o uzavřeném titulkování a reklamách podpory živého streamování najdete v článku [podporované uzavřené titulky a standardy pro vkládání reklam](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnout zpětnou vazbu
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Vložení videa adaptivního streamování MPEG-DASH do aplikace HTML5 se souborem DASH.js](media-services-embed-mpeg-dash-in-html5.md)

[Úložiště GitHub – pomlčka. js](https://github.com/Dash-Industry-Forum/dash.js)

