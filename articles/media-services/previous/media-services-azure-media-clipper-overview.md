---
title: "Vytvoření klipů pomocí Azure Media výřez | Microsoft Docs"
description: "Přehled Azure Media výřez, nástroj pro vytváření média klipů z prostředků"
services: media-services
keywords: "klip; subclip; kódování; média"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: f3822386d0d16b1feaf16853424329558a18f910
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2017
---
# <a name="create-clips-with-azure-media-clipper"></a>Vytvoření klipů pomocí Azure Media výřez
Azure Media výřez je volné knihovna JavaScript, která umožňuje vývojářům webů jejich uživatelům poskytnout rozhraní pro vytváření klipů média. Tento nástroj lze integrovat do žádné webové stránce a poskytuje rozhraní API pro načítání prostředků a odesílání úloh výstřižek.

Azure Media výřez umožňuje:
- Trim projektem před a po slate ze za provozu archivy 
- Napište video označuje z živé události, za provozu archivy nebo fMP4 VOD soubory AMS 
- Řetězení videa z více zdrojů 
- Vytvoření souhrnné klipů z vaše prostředky média AMS 
- Oříznutí videa s rámečkem přesnost 
- Generování manifestu dynamické filtry přes existující za provozu a VOD prostředky s přesnost skupiny z obrázků (GOP) 
- Vytvořit kódování úlohy s prostředky v účtu media services

Chcete-li požádat o nové funkce, zadejte nápady nebo připomínky, odeslání [UserVoice pro službu Azure Media Services](http://aka.ms/amsvoice/). Pokud máte a konkrétní problémy, dotazy nebo najít žádné chyby, vyřaďte Media Services team řádek v amcinfo@microsoft.com.

Následující obrázek ukazuje rozhraní výřez: ![výřez médií Azure](media/media-services-azure-media-clipper-overview/media-services-azure-media-clipper-interface.PNG)

## <a name="release-notes"></a>Poznámky k verzi
Najdete v příspěvku blogu výřez různé známé problémy a protokol změn pro nejnovější verzi výřez v následujícím seznamu:
- [Příspěvek blogu](https://azure.microsoft.com/blog/azure-media-clipper/)
- [Seznam známých problémů](https://amp.azure.net/libs/amc/latest/docs/known_issues.html)
- [Protokol změn](https://amp.azure.net/libs/amc/latest/docs/changelog.html)

## <a name="browser-support"></a>Podpora prohlížeče
Azure Media výřez je sestaven pomocí moderní technologie HTML5 a podporuje následující prohlížeče:

- Microsoft Edge 13 +
- Internet Explorer 11 +
- Chrome 54 +
- Safari 10 +
- Firefox 50 +

> [!NOTE]
> Aktuálně je podporována pouze HTML5 přehrávání datové proudy ze služby Azure Media Services.

## <a name="language-support"></a>Podpora jazyků
Pomůcka výřez je k dispozici v těchto jazycích 18:
- Čínština (zjednodušená)
- Čínština (tradiční)
- Čeština
- Holandština Vlamska verze
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
Chcete-li začít používat Azure Media výřez, přečtěte si [Začínáme](media-services-azure-media-clipper-getting-started.md) článku podrobnosti o tom, jak nasadit widgetu.
