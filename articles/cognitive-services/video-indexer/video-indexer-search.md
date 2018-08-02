---
title: Vyhledání určitých momentů ve videích pomocí Azure Video Indexer | Dokumentace Microsoftu
description: Toto téma popisuje způsob vyhledání určitých momentů ve videích.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/31/2018
ms.author: juliako
ms.openlocfilehash: 1cffa067d8028adab4dbcc82c529f77d980ce6be
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397539"
---
# <a name="find-exact-moments-within-videos"></a>Vyhledání určitých momentů ve videích

Toto téma ukazuje možnosti hledání, které vám umožní vyhledání určitých momentů ve videích.

1. Přihlaste se k vaší [Video Indexer](https://api-portal.videoindexer.ai/) účtu.
2. Prohledávat všechna videa ve vašem účtu.

    V následujícím příkladu jsme hledali všechna videa, které jsou vytvořené na webu Channel 9 se Scottem Hanselmanem.

    ![Search](./media/video-indexer-search/video-indexer-search01.png)
    
3. Hledejte souhrnný poznatky o videu.

    Můžete vyhledávat ve videu kliknutím **Přehrát** na video. Potom můžete hledat ve videu výběrem **hledání** kartu. Například jsme hledali všechna místa, kde se používá text "identity protection". 

    ![Search](./media/video-indexer-search/video-indexer-search02.png)

    Pokud kliknete na některý z výsledků, hráč přináší je na daném okamžiku videa. Ve vaší aplikaci můžete dosáhnout zobrazení player a přehledy a synchronizace. Další informace najdete v tématu [vložit Video Indexer widgetů do aplikace](video-indexer-embed-widgets.md). 

    
4. Vyhledejte podrobný přehled videa.

    Pokud chcete vytvořit vlastní rozpis podle video, které jste našli, stiskněte **upravit** tlačítko. Tato stránka zobrazuje úplný přehled videa. Můžete vyhledávat v rozpis k zobrazení pouze řádky, které vás zajímají. Další informace najdete v tématu [zobrazení a úprava přehledů funkce Video Indexer](video-indexer-view-edit.md).

    V tomto příkladu budeme prohledávat text "identity protection". Můžeme také použít další filtry, jak je znázorněno na následující obrazovce.

    ![Search](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>Další postup 

Po nalezení videa, kterou chcete pracovat, můžete pokračovat, zpracování videa, jak je popsáno v některém z těchto témat: 

- [Vytvořit nové poznatky z videí podle existujícího videa](video-indexer-create-new.md)
- [Zpracování obsahu pomocí rozhraní REST API služby Video Indexer](video-indexer-use-apis.md)
- [Vložení widgetů visual ve vaší aplikaci](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Další informace najdete v tématech

[Video Indexer s přehledem](video-indexer-overview.md)
