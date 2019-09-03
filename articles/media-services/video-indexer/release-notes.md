---
title: Poznámky k verzi Azure Media Services Video Indexer | Microsoft Docs
description: Pokud chcete mít přehled o nejnovějším vývoji, najdete v tomto článku nejnovější aktualizace Azure Media Services Video Indexer.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 08/29/2019
ms.author: juliako
ms.openlocfilehash: a7d178972a8c8b413e3c6336c403348f1eb78abb
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232599"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Poznámky k verzi Azure Media Services Video Indexer

Abyste mohli používat aktuální pomocí nejnovější vývoj, tento článek poskytuje informace o:

* Nejnovější vydané verzi
* Známé problémy
* Opravy chyb
* Zastaralé funkce

## <a name="august-2019"></a>Srpen 2019
 
### <a name="video-indexer-deployed-in-uk-south"></a>Video Indexer nasazena v Velká Británie – jih

Nyní můžete vytvořit placený účet Video Indexer v oblasti Velká Británie – jih.

### <a name="new-editorial-shot-type-insights-available"></a>Nové informace typu redakčního snímku přehled dostupných

Nové značky přidané do snímků videa poskytují redakční "typy snímků" a identifikují je pomocí běžných redakčních frází, které se používají v pracovním postupu pro vytváření obsahu, jako například: extrémní Closeup, Closeup, roztažitelné, střední, dva snímky, venkovní, interiér, levý obličej a pravý obličej (k dispozici v JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>K dispozici je extrakce nových entit pro lidi a umístění

Video Indexer identifikuje pojmenovaná umístění a lidi prostřednictvím zpracování přirozeného jazyka (NLP) z optického rozpoznávání a přepisu videa. Video Indexer používá algoritmus strojového učení k rozpoznání, kdy konkrétní umístění (třeba věž Eiffel Tower) nebo lidi (například Jan Novák) jsou ve videu volána.

### <a name="keyframes-extraction-in-native-resolution"></a>Extrakce klíčových snímků v nativním rozlišení

Klíčové snímky extrahované Video Indexer jsou k dispozici v původním rozlišení videa.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>GA pro školení vlastních modelů tváře z imagí

Školení ploch z imagí přesunutých z režimu náhledu do GA (k dispozici prostřednictvím rozhraní API a na portálu).

> [!NOTE]
> Neexistuje žádný dopad na ceny související s přechodem "Preview na GA".

### <a name="hide-gallery-toggle-option"></a>Skrýt přepínač galerie

Uživatel může zvolit skrytí karty galerie z portálu (podobně jako skrytí karty ukázky).
 
### <a name="maximum-url-size-increased"></a>Zvýšila se maximální velikost adresy URL.

Podpora řetězce dotazu URL 4096 (místo 2048) při indexování videa
 
### <a name="support-for-multi-lingual-projects"></a>Podpora vícejazyčných projektů

Projekty se teď dají vytvářet na základě videí indexovaných v různých jazycích (jenom rozhraní API).

## <a name="july-2019"></a>Červenec 2019

### <a name="editor-as-a-widget"></a>Editor jako widget

Editor AI Video Indexer je teď k dispozici jako widget, který se má vložit do zákaznických aplikací.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Aktualizace vlastního jazykového modelu z zavřeného souboru titulků z portálu

Zákazníci můžou jako vstup pro jazykové modely na stránce vlastní nastavení portálu zadat formáty souborů VTT, SRT aplikace a TTML.

## <a name="june-2019"></a>Červeně 2019

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer nasazené do Japonska – východ

Nyní můžete vytvořit placený účet Video Indexer v oblasti Japonsko – východ.

### <a name="create-and-repair-account-api-preview"></a>Vytvoření a oprava rozhraní API pro účty (Preview)

Přidalo se nové rozhraní API, které umožňuje [aktualizovat koncový bod nebo klíč připojení Azure Media Service](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag).

### <a name="improve-error-handling-on-upload"></a>Zlepšení zpracování chyb při nahrávání 

V případě nestandardního nastavení Azure Media Services účtu se vrátí Popisná zpráva.

### <a name="player-timeline-keyframes-preview"></a>Klíčové snímky časové osy přehrávače v Preview 

V časové ose přehrávače teď můžete zobrazit náhled obrazu pro každou časovou osu.

### <a name="editor-semi-select"></a>Editor – poloviční výběr

Teď si můžete zobrazit náhled všech přehledů, které jsou vybrané jako výsledek výběru konkrétního období přehledu v editoru.

## <a name="may-2019"></a>Květen 2019

### <a name="update-custom-language-model-from-closed-caption-file"></a>Aktualizace vlastního jazykového modelu z zavřeného souboru titulků

[Vytvoření vlastního jazykového modelu](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) a aktualizace rozhraní API pro [vlastní jazykové modely](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) teď podporují formáty souborů vtt, SRT aplikace a TTML jako vstup pro jazykové modely.

Při volání [rozhraní API pro přepis aktualizace videa](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)se přepis přidá automaticky. Školicí model přidružený k videu se také aktualizuje automaticky. Informace o tom, jak přizpůsobit a naučit jazykové modely, najdete v tématu [přizpůsobení jazykového modelu pomocí video indexer](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Nové formáty přepisů ke stažení – TXT a CSV

Kromě skrytého formátu titulků, který už je podporovaný (SRT aplikace, VTT a TTML), teď Video Indexer podporuje stahování přepisu ve formátech TXT a CSV.

## <a name="next-steps"></a>Další kroky

[Přehled](video-indexer-overview.md)
