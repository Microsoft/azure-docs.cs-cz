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
ms.date: 07/22/2019
ms.author: juliako
ms.openlocfilehash: b627a78edef1c0b0fe6b3ed011678145aea397ae
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845885"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Poznámky k verzi Azure Media Services Video Indexer

Abyste mohli používat aktuální pomocí nejnovější vývoj, tento článek poskytuje informace o:

* Nejnovější vydané verzi
* Známé problémy
* Opravy chyb
* Zastaralé funkce

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

## <a name="next-steps"></a>Další postup

[Přehled](video-indexer-overview.md)
