---
title: Vytváření přehledů videí z existujících videí – Azure
titlesuffix: Azure Media Services
description: Toto téma ukazuje, jak vytvářet a publikovat přehledy videí na základě existujících video souborů.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 4a65e88e3f94f64a56bde882b535030968ae354d
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55991272"
---
# <a name="create-highlights-from-existing-videos"></a>Vytvoření vybraná vystoupení z existujících videí

Toto téma ukazuje, jak vytvářet a publikovat přehledy videí na základě jiného videa.

1. Přejděte na web [Video Indexer](https://www.videoindexer.ai/) a přihlaste se.
2. Najděte video, ze kterého chcete vytvořit přehled videa.
3. Stiskněte **Play** (Přehrát).

    Na této stránce se zobrazí souhrnný přehled videa. 

    ![Insights](./media/video-indexer-create-new/video-indexer-summarized-insights.png)
3. Stiskněte tlačítko **Edit** (Upravit).

    Tato stránka zobrazuje úplný rozpis videa. Rozpis je rozdělený do bloků. Bloky usnadňují procházení dat. Blok může být rozdělený třeba podle toho, jak se mění přednášející, nebo když nastane dlouhá pauza. Můžete vytvořit vlastní seznam stop obsahující jenom řádky, které chcete. Pokud chcete zobrazit jenom konkrétní části zdrojového videa, můžete filtrovat podle témat / klíčových slov, pocitového zabarvení, lidí nebo mluvčích. Můžete zobrazit jenom přepis videa nebo optické rozpoznávání znaků (OCR) ve videu.    

    ![Insights](./media/video-indexer-create-new/video-indexer-create-new-playlist.png)
4. Vytvořte si seznam stop.

    Pokud chcete na seznam přidat nebo z něho odebrat řádky, stiskněte **+**/**-**.
5. Zobrazte náhled seznamu stop.

    Po dokončení vytváření seznamu stop stiskněte **Preview** (Náhled).
6. Publikujte seznam stop.

    Po zobrazení náhledu seznamu stop ho můžete publikovat.

    Když seznam stop publikujete, přidá se do seznamu vašich přehledů videí.


## <a name="next-steps"></a>Další postup 

Když vytvoříte nový seznam stop, můžete pokračovat v jeho zpracování podle popisu v některém z těchto témat: 

- [Zpracování obsahu pomocí rozhraní REST API služby Video Indexer](video-indexer-use-apis.md)
- [Vložení vizuálních widgetů do vaší aplikace](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Další informace najdete v tématech

[Přehled Video Indexeru](video-indexer-overview.md) 
