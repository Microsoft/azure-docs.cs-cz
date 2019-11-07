---
title: Vytváření klipů pomocí Azure Media Clipperu | Microsoft Docs
description: Přehled Azure Media Clipperu, nástroj pro vytváření mediálních klipů z assetů
services: media-services
keywords: Clip; dílčí klip; Encoding; Media
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 51f85dffd48e451b477018ef20491f8619a30f25
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685009"
---
# <a name="create-clips-with-azure-media-clipper"></a>Vytváření klipů pomocí Azure Media Clipperu 

Azure Media Clipper je bezplatná knihovna JavaScriptu, která umožňuje webovým vývojářům poskytovat svým uživatelům rozhraní pro vytváření mediálních klipů. Tento nástroj lze integrovat do libovolné webové stránky a poskytuje rozhraní API pro načítání assetů a odesílání ořezových úloh.

Azure Media Clipper vám umožní:
- Vystřihování předslat a post-SLAT z živých archivů 
- Sestavování videí od živých událostí AMS, živých archivů nebo souborů fMP4 VOD 
- Zřetězení videí z více zdrojů 
- Vytváření souhrnných klipů z prostředků médií AMS 
- Vystřihnout videa s přesností snímků 
- Generování dynamických filtrů manifestu pomocí stávajících živých a VOD prostředků s přesností skupinu GOP (Group-of-refotografie) 
- Vytváření úloh kódování pro prostředky v účtu Media Services

Pokud chcete požádat o nové funkce, poskytněte nápady nebo zpětnou vazbu, odešlete do [UserVoice pro Azure Media Services](https://aka.ms/amsvoice/). Pokud máte nějaké a specifické problémy, otázky nebo zjistíte případné chyby, vyřaďte Media Services týmu řádek na amcinfo@microsoft.com.

Následující obrázek ukazuje rozhraní Clipperu: ![Azure Media Clipper](media/media-services-azure-media-clipper-overview/media-services-azure-media-clipper-interface.PNG)

## <a name="release-notes"></a>Poznámky k verzi
Podívejte se na následující seznam pro Blogový příspěvek v Clipperu, různé známé problémy a protokol změn pro nejnovější verzi Clipperu:
- [Příspěvek na blogu](https://azure.microsoft.com/blog/azure-media-clipper/)
- [Seznam známých problémů](https://amp.azure.net/libs/amc/latest/docs/known_issues.html)
- [Změn](https://amp.azure.net/libs/amc/latest/docs/changelog.html)

## <a name="browser-support"></a>Podpora prohlížeče
Azure Media Clipper je sestavený s využitím moderních technologií HTML5 a podporuje následující prohlížeče:

- Microsoft Edge 13 +
- Internet Explorer 11 +
- Chrome 54 +
- Safari 10 +
- Firefox 50 +

> [!NOTE]
> V tuto chvíli se podporuje jenom přehrávání datových proudů HTML5 z Azure Media Services.

## <a name="language-support"></a>Podpora jazyků
Widget v Clipperu je k dispozici v následujících 18 jazycích:
- Čínština (zjednodušená)
- Čínština (tradiční)
- Čeština
- Holandština, Flemish
- Angličtina
- Francouzština
- Němčina
- Maďarština
- italština
- Japonština
- Korejština
- polština
- Portugalština (Brazílie)
- Portugalština (Portugalsko)
- ruština
- Španělština
- švédština
- turečtina

## <a name="next-steps"></a>Další kroky
Pokud chcete začít používat Azure Media Clipper, přečtěte si článek [Začínáme](media-services-azure-media-clipper-getting-started.md) , kde najdete podrobnosti o tom, jak tento widget nasadit.
