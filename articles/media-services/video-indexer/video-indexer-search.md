---
title: Hledání ve videích ve videu s využitím Video Indexer
titleSuffix: Azure Media Services
description: Naučte se, jak ve videích vyhledat přesný moment pomocí Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/23/2019
ms.author: juliako
ms.openlocfilehash: a1ca7c677e5f1eb55d74cca45e757676674f303c
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030504"
---
# <a name="search-for-exact-moments-in-videos-with-video-indexer"></a>Hledání ve videích ve videu s využitím Video Indexer

V tomto tématu se dozvíte, jak používat Video Indexer web k hledání přesného momentu ve videích.

1. Přejít na web [video indexer](https://www.videoindexer.ai/) a přihlásit se.
1. Zadejte klíčová slova pro vyhledávání a hledání se bude provádět mezi všemi videi v knihovně vašeho účtu. 

    Hledání můžete filtrovat výběrem **filtrů**. V níže uvedeném příkladu vyhledáme "Microsoft", který se zobrazuje jenom jako text na obrazovce (OCR).

    :::image type="content" source="./media/video-indexer-search/filter.png" alt-text="Filter, Only text":::
1. Kliknutím na tlačítko **Hledat** zobrazíte výsledek.

    :::image type="content" source="./media/video-indexer-search/results.png" alt-text="Výsledek hledání videí":::

    Když vyberete jeden z výsledků, přehrávač vám ve videu zařadí přesně tento okamžik.
1. Zobrazte a Prohlédněte si souhrnné přehledy videa kliknutím na tlačítko **Přehrát** ve videu nebo výběrem některého z původních výsledků hledání. 

    **Přehledy** můžete zobrazit, Hledat a upravovat. Když vyberete jeden z přehledů, přehrávač vám ve videu nabídne přesně tento okamžik.  

    :::image type="content" source="./media/video-indexer-search/insights.png" alt-text="Zobrazit, vyhledat a upravit přehledy videa":::

    Pokud vložíte video prostřednictvím Video Indexer widgety, můžete v aplikaci dosáhnout zobrazení a synchronizace pro Player/Insights. Další informace najdete v tématu [vložení video indexer widgetů do aplikace](video-indexer-embed-widgets.md).
1. Přepisy můžete zobrazit, vyhledávat a upravovat kliknutím na kartu **Časová osa** . 

    :::image type="content" source="./media/video-indexer-search/timeline.png" alt-text="Zobrazit, vyhledat a upravit přepisy videa":::

    Pokud chcete text upravit, v pravém horním rohu vyberte **Upravit** a podle potřeby změňte text. 

    Přepisy můžete také přeložit a stáhnout výběrem vhodné možnosti v pravém horním rohu. 

## <a name="embed-download-create-projects"></a>Vložení, stažení, vytvoření projektů

Můžete vložit své video výběrem **</>vložit** do svého videa. Podrobnosti najdete v tématu věnovaném [vkládání vizuálních pomůcek do aplikace](video-indexer-embed-widgets.md).

Zdrojové video, přehledy videa a přepisů si můžete stáhnout kliknutím na **Stáhnout** pod videem.

Můžete vytvořit klip na základě videa konkrétního řádku a momentu tak, že **v editoru** kliknete na otevřít. Pak upravte video a uložte projekt. Podrobnosti najdete v článku o [používání hloubkových přehledů videí](use-editor-create-project.md).

:::image type="content" source="./media/video-indexer-search/embed-download-create-projects.png" alt-text="Vložení, stažení a vytvoření projektů videa":::

## <a name="next-steps"></a>Další kroky

[Zpracování obsahu pomocí rozhraní REST API služby Video Indexer](video-indexer-use-apis.md)
