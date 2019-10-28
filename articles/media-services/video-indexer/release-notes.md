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
ms.date: 10/27/2019
ms.author: juliako
ms.openlocfilehash: 0583c0093d240026e3ebcdad7b14494f07986ec2
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968720"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Poznámky k verzi Azure Media Services Video Indexer

V tomto článku najdete informace o tom, jak se chcete zabývat aktuálním vývojem.

* Nejnovější verze
* Známé problémy
* Opravy chyb
* Zastaralé funkce

## <a name="october-2019"></a>Říjen 2019
 
* Hledání animovaných znaků v galerii

    Při indexování animovaných znaků je teď můžete vyhledat v zobrazení videa účtu. Další informace najdete v tématu [rozpoznávání animovaných znaků](animated-characters-recognition.md).

## <a name="september-2019"></a>Září 2019
 
V daty IBC 2019 bylo oznámeno více předběžných.
 
* Animované rozpoznávání znaků (Public Preview)

    Možnost rozpoznávat znaky skupinových reklam v animovaném obsahu prostřednictvím integrace s vlastními vizemi. Další informace naleznete v tématu [animovaná detekce znaků](animated-characters-recognition.md).
* Identifikace ve více jazycích (Public Preview)

    Detekuje segmenty v různých jazycích zvukové stopy a na základě nich vytvoří vícejazyčný přepis. Počáteční podpora: angličtina, španělština, němčina a francouzština. Další informace najdete v tématu [Automatické určení a přepisovat vícejazyčného obsahu](multi-language-identification-transcription.md).
* Extrakce pojmenovaná entita pro lidi a umístění

    Extrahuje značky, umístění a lidi z mluvené řeči a vizuálního textu prostřednictvím zpracování přirozeného jazyka (NLP).
* Klasifikace typu redakčního snímku

    Označování snímků pomocí redakčních typů, jako je uzavření, střední záběr, dva snímky, interiér, venkovní atd. Další informace najdete v tématu [zjištění typu redakčního snímku](scenes-shots-keyframes.md#editorial-shot-type-detection).
* Inferencing vylepšení – teď pokrývání úrovně 2
    
    V tématu Inferencing model teď podporuje hlubší členitost taxonomie IPTC. Přečtěte si úplné podrobnosti [Azure Media Services nové inovace s podporou AI](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/).

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

## <a name="june-2019"></a>Červen 2019

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
