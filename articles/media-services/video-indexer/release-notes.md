---
title: Poznámky k verzi videoindexeru Azure Media Services | Dokumenty společnosti Microsoft
description: Chcete-li zůstat aktuální s nejnovější vývoj, tento článek poskytuje nejnovější aktualizace na Azure Media Services Video Indexer.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 04/20/2020
ms.author: juliako
ms.openlocfilehash: f7ce5f5086bcf2d577fb998df307ee684d536c15
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870101"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Poznámky k verzi videoindexeru Azure Media Services

>Získejte upozornění, kdy se má tato stránka znovu aktualizovat zkopírováním a vložením této adresy URL: `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` do čtečky informačních kanálů RSS.

Chcete-li mít aktuální informace o nejnovějším vývoji, tento článek obsahuje informace o:

* Nejnovější verze
* Známé problémy
* Opravy chyb
* Zastaralé funkce

## <a name="april-2020"></a>Duben 2020

### <a name="new-widget-parameters-capabilities"></a>Nové možnosti parametrů widgetu

Widget **Přehledy** obsahuje nové `language` `control`parametry: a .

Widget **Přehrávač** má `locale` nový parametr. Jazyk `locale` `language` hráče i parametry ovládají.

Další informace naleznete v části [typy widgetů.](video-indexer-embed-widgets.md#widget-types) 

### <a name="new-player-skin"></a>Nový hráč kůže

Nový hráč kůže zahájena s aktualizovaným designem.

## <a name="january-2020"></a>Leden 2020
 
### <a name="custom-language-support-for-additional-languages"></a>Vlastní jazyková podpora pro další jazyky

Video Indexer nyní podporuje `ar-SY` vlastní `en-UK`jazykové `en-AU` modely pro , a (pouze rozhraní API).
 
### <a name="delete-account-timeframe-action-update"></a>Odstranit aktualizaci akce časového rámce účtu

Akce Odstranit účet nyní odstraní účet do 90 dnů namísto 48 hodin.
 
### <a name="new-video-indexer-github-repository"></a>Nové úložiště GitHub pro indexování videa

Nový Video Indexer GitHub s různými projekty, začíná průvodce a ukázky kódu je nyní k dispozici:https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Swagger aktualizace

Video Indexer jednotné **ověřování** a **operace** do jednoho [Video Indexer OpenAPI specifikace (swagger)](https://api-portal.videoindexer.ai/docs/services/Operations/export?DocumentFormat=OpenApiJson). Vývojáři mohou najít rozhraní API na [portálu pro vývojáře video indexeru](https://api-portal.videoindexer.ai/).

## <a name="december-2019"></a>Prosinec 2019

### <a name="update-transcript-with-the-new-api"></a>Aktualizace přepisu pomocí nového rozhraní API

Aktualizujte konkrétní část v přepisu pomocí [rozhraní Update-Video-Index](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?&pattern=update) API.

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>Oprava konfigurace účtu z portálu Video Indexer

Nyní můžete aktualizovat konfiguraci připojení služby Media Services, abyste mohli svépomocně s problémy, jako jsou: 

* nesprávný prostředek Azure Media Services
* změny hesla
* Prostředky mediálních služeb byly přesunuty mezi předplatnými.  

Chcete-li opravit konfiguraci účtu, přejděte na portálu Video Indexer na kartu Nastavení > účet (jako vlastník).

### <a name="configure-the-custom-vision-account"></a>Konfigurace vlastního účtu vize

Nakonfigurujte vlastní účet vize na placených účtech pomocí portálu Video Indexer (dříve to bylo podporováno pouze rozhraním API). Chcete-li to provést, přihlaste se k portálu Video Indexer, zvolte Přizpůsobení modelu > animované postavy > Konfigurovat. 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>Scény, snímky a klíčové snímky – nyní v jednom podokně přehledů

Scény, snímky a klíčové snímky jsou nyní sloučeny do jednoho přehledu pro snadnější spotřebu a navigaci. Když vyberete požadovanou scénu, můžete vidět, z čeho se skládá. 

### <a name="notification-about-a-long-video-name"></a>Oznámení o dlouhém názvu videa

Pokud je název videa delší než 80 znaků, video indexer zobrazí při nahrávání popisnou chybu.

### <a name="streaming-endpoint-is-disabled-notification"></a>Koncový bod streamování je zakázáno oznámení

Pokud je koncový bod streamování zakázán, video indexer zobrazí na stránce přehrávače popisnou chybu.

### <a name="error-handling-improvement"></a>Zlepšení při zpracování chyb

Stavový kód 409 bude nyní vrácen z [re-index video](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?) a [aktualizovat video index](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?) API v případě, že video je aktivně indexovány, aby se zabránilo přepsání aktuální změny přeindexování náhodou.

## <a name="november-2019"></a>Listopad 2019
 
* Podpora korejských vlastních jazykových modelů

    Indexer videa nyní podporuje vlastní`ko-KR`jazykové modely v korejštině ( ) v rozhraní API i na portálu. 
* Nové jazyky podporované pro převod řeči na text (STT)

    Video Indexer API nyní podporují STT v arabském Levantine (ar-SY), anglický britský dialekt (en-GB) a anglický australský dialekt (en-AU).
    
    Pro nahrávání videa jsme nahradili zh-HANS na zh-CN, oba jsou podporovány, ale zh-CN je doporučeno a přesnější.
    
## <a name="october-2019"></a>Říjen 2019
 
* Hledání animovaných postav v galerii

    Při indexování animovaných postav je nyní můžete vyhledat ve videokuchyni účtu. Další informace naleznete v [tématu Rozpoznávání animovaných postav](animated-characters-recognition.md).

## <a name="september-2019"></a>Září 2019
 
Několik vylepšení oznámených na IBC 2019:
 
* Rozpoznávání animovaných postav (public preview)

    Schopnost rozpoznat skupinové reklamy rozpoznat postavy v animovaném obsahu prostřednictvím integrace s vlastní vizí. Další informace naleznete v [tématu Detekce animovaných znaků](animated-characters-recognition.md).
* Identifikace ve více jazycích (public preview)

    Detekujte segmenty ve více jazycích ve zvukové stopě a vytvořte na nich vícejazyčný přepis. Počáteční podpora: angličtina, španělština, němčina a francouzština. Další informace naleznete v tématu [Automaticky identifikovat a přepsat vícejazyčný obsah](multi-language-identification-transcription.md).
* Extrakce pojmenované entity pro osoby a umístění

    Extrahuje značky, místa a osoby z řeči a vizuálního textu prostřednictvím zpracování přirozeného jazyka (NLP).
* Klasifikace typu redakčního záběru

    Označování záběrů s redakčními typy, jako je zblízka, střední výstřel, dva výstřely, vnitřní, venkovní atd. Další informace naleznete [v tématu Redakční detekce typů záběrů](scenes-shots-keyframes.md#editorial-shot-type-detection).
* Vylepšení odvození tématu – nyní pokrývá úroveň 2
    
    Model odvození tématu nyní podporuje hlubší rozlišovací schopnost taxonomie IPTC. Přečtěte si podrobné informace o [nové inovaci založené na umělou atomové technologii Azure Media Services](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/).

## <a name="august-2019"></a>Srpen 2019
 
### <a name="video-indexer-deployed-in-uk-south"></a>Video Indexer nasazený na jihu Spojeného království

Nyní můžete vytvořit placený účet Video Indexer v oblasti Jižní Británie.

### <a name="new-editorial-shot-type-insights-available"></a>K dispozici jsou nové přehledy typu Redakční shot

Nové značky přidané do videosnímků poskytují redakční "typy záběrů", které je identifikují běžnými redakčními frázemi používanými v pracovním postupu tvorby obsahu, jako jsou: extrémní detailní záběr, detailní záběr, široký, střední, dva výstřely, venkovní, vnitřní, levá a pravá tvář (k dispozici v JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>K dispozici je extrakce entit Nové osoby a umístění

Video Indexer identifikuje pojmenované lokality a osoby prostřednictvím zpracování přirozeného jazyka (NLP) z rozpoznávání OCR a přepisu videa. Video Indexer používá algoritmus strojového učení k rozpoznání, kdy jsou konkrétní místa (například Eiffelova věž) nebo lidé (například John Doe) voláni ve videu.

### <a name="keyframes-extraction-in-native-resolution"></a>Extrakce klíčových snímků v nativním rozlišení

Klíčové snímky extrahované video indexerem jsou k dispozici v původním rozlišení videa.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>GA pro školení vlastní modely obličeje z obrázků

Trénovací tváře z obrázků přesunuté z režimu náhledu do GA (k dispozici prostřednictvím rozhraní API a na portálu).

> [!NOTE]
> Neexistuje žádný cenový dopad související s přechodem "Náhled na GA".

### <a name="hide-gallery-toggle-option"></a>Možnost skrytí galerie

Uživatel může skrýt kartu galerie z portálu (podobně jako skrytí karty ukázky).
 
### <a name="maximum-url-size-increased"></a>Maximální velikost adresy URL byla zvýšena.

Podpora pro řetězec dotazu URL 4096 (místo 2048) na indexování videa.
 
### <a name="support-for-multi-lingual-projects"></a>Podpora vícejazyčných projektů

Projekty lze nyní vytvářet na základě videí indexovaných v různých jazycích (pouze rozhraní API).

## <a name="july-2019"></a>Červenec 2019

### <a name="editor-as-a-widget"></a>Editor jako widget

Video Indexer AI-editor je nyní k dispozici jako widget, který má být vložen do zákaznických aplikací.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Aktualizace vlastního jazykového modelu ze souboru skrytých titulků z portálu

Zákazníci mohou poskytnout formáty souborů VTT, SRT a TTML jako vstup pro jazykové modely na stránce přizpůsobení portálu.

## <a name="june-2019"></a>Červen 2019

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer nasazený na východ ě Japonska

Nyní můžete vytvořit placený účet Video Indexer v oblasti Japonsko – východ.

### <a name="create-and-repair-account-api-preview"></a>Vytvoření a oprava rozhraní API účtu (preview)

Přidáno nové rozhraní API, které umožňuje [aktualizovat koncový bod nebo klíč připojení služby Azure Media Service](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag).

### <a name="improve-error-handling-on-upload"></a>Zlepšit zpracování chyb při nahrávání 

V případě chybné konfigurace základního účtu Azure Media Services se vrátí popisná zpráva.

### <a name="player-timeline-keyframes-preview"></a>Náhled klíčových snímků přehrávače 

Nyní můžete zobrazit náhled obrázku pro každý časnatou dobu přehrávače.

### <a name="editor-semi-select"></a>Editor semi-select

Nyní můžete zobrazit náhled všech přehledů, které jsou vybrány v důsledku výběru konkrétního časového rámce přehledu v editoru.

## <a name="may-2019"></a>Květen 2019

### <a name="update-custom-language-model-from-closed-caption-file"></a>Aktualizace vlastního jazykového modelu ze souboru skrytých titulků

[Vytvořte vlastní jazykový model](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) a [aktualizovat vlastní jazykové modely](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) rozhraní API nyní podporují formáty souborů VTT, SRT a TTML jako vstup pro jazykové modely.

Při volání [aktualizace video přepis API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript), přepis je přidán automaticky. Tréninkový model přidružený k videu se také aktualizuje automaticky. Informace o přizpůsobení a trénování jazykových modelů naleznete v [tématu Přizpůsobení jazykového modelu pomocí videoindexeru](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Nové formáty přepisu stahování – TXT a CSV

Kromě již podporovaného formátu skrytých titulků (SRT, VTT a TTML) video indexer nyní podporuje stahování přepisu ve formátech TXT a CSV.

## <a name="next-steps"></a>Další kroky

[Přehled](video-indexer-overview.md)
