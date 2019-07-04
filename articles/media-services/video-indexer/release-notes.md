---
title: Poznámky k verzi Azure Media Services Video Indexer | Dokumentace Microsoftu
description: Abyste mohli používat aktuální pomocí nejnovější vývoj, tento článek poskytuje nejnovější informace o Azure Media Services Video Indexer.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 06/25/2019
ms.author: juliako
ms.openlocfilehash: f1c5f43316292f17547b84d970a0f03a1a2c366f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454019"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Poznámky k verzi Azure Media Services Video Indexer

Abyste mohli používat aktuální pomocí nejnovější vývoj, tento článek poskytuje informace o:

* Nejnovější vydané verzi
* Známé problémy
* Opravy chyb
* Zastaralé funkce

## <a name="june-2019"></a>2019 dne

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer nasazené do oblasti Japonsko – východ

Nyní můžete vytvořit Video Indexer placený účet v oblasti Japonsko – východ.

### <a name="create-and-repair-account-api-preview"></a>Vytvoření a opravte účet rozhraní API (Preview)

Přidání nového rozhraní API, která vám umožní [aktualizovat koncový bod připojení služby Azure Media Service nebo klíč](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag).

### <a name="improve-error-handling-on-upload"></a>Zlepšení zpracování na odesílání chyb 

Popisná zpráva se vrátí v případě nesprávné konfiguraci podkladového účtu Azure Media Services.

### <a name="player-timeline-keyframes-preview"></a>Přehrávač časová osa klíčových snímků ve verzi preview 

Nyní uvidíte náhled obrázku pokaždé, když na časové ose hráče.

### <a name="editor-semi-select"></a>Editor částečného výběru

Nyní je vidět ve verzi preview všechny přehledy, které jsou vybrány v důsledku výběru časový rámec určité insight v editoru.

## <a name="may-2019"></a>. Května 2019.

### <a name="update-custom-language-model-from-closed-caption-file"></a>Aktualizace vlastního jazykového modelu ze souboru titulků

[Vytvoření vlastního jazykového modelu](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) a [aktualizovat vlastní jazykové modely](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) rozhraní API teď podporují VTT SRT aplikace, a formáty souborů TTML jako vstup pro jazykových modelů.

Při volání [přepisu videa aktualizace rozhraní API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript), je automaticky přidán přepisu. Školení model přidružený k videu se také automaticky aktualizuje. Informace o tom, jak přizpůsobit a trénování modelů jazyka najdete v tématu [upravit jazykový model s Video Indexer](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Nové formáty přepisu stahování – TXT a sdíleného svazku clusteru

Kromě uzavřené titulků formát již podporovalo (SRT VTT a TTML) funkce Video Indexer teď podporuje stahování přepisu ve formátech, TXT a sdíleného svazku clusteru.

## <a name="next-steps"></a>Další postup

[Přehled](video-indexer-overview.md)
