---
title: Použít Azure Video Indexer vytvářet přehledy, videa z existující videa | Microsoft Docs
description: Toto téma ukazuje postup vytvoření a publikování videa přehledy založené na některé další video.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 05/30/2018
ms.author: juliako
ms.openlocfilehash: 1834fa9f1dd7db618850ab897a85a1bcfdaa89ac
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "35343864"
---
# <a name="create-video-insights-from-existing-videos"></a>Vytvářet přehledy, videa z existující videa

Toto téma ukazuje postup vytvoření a publikování videa přehledy založené na některé další video.

1. Přihlaste se k vaší [Video Indexer](https://api-portal.videoindexer.ai/) účtu.
2. Najít video, ze kterého chcete vytvořit video přehledy.
3. Stiskněte klávesu **přehrání**.

    Stránce se zobrazují na video Souhrnná statistika. 

    ![Insights](./media/video-indexer-create-new/video-indexer-summarized-insights.png)

3. Stiskněte **upravit** tlačítko.

    Tato stránka zobrazuje úplný rozpis video. Rozdělení je rozděleno do bloků. Aby bylo snazší projít data jsou zde bloky. Například bloku může rozdělit na základě při mluvčí změnit nebo je dlouhá pozastavení. Můžete vytvořit vlastní seznam skladeb, který obsahuje pouze řádky, které chcete. Chcete-li zobrazit pouze konkrétní části zdroj videa, můžete filtrovat podle témata nebo klíčová slova, chráněny, osoby, mluvčí. Můžete zobrazit jenom na video přepis nebo rozpoznávání znaků.    

    ![Insights](./media/video-indexer-create-new/video-indexer-create-new-playlist.png)

4. Vytvořením seznamu.

    Chcete-li přidat nebo odebrat řádky do nebo ze seznamu stop, stiskněte **+** / **-**.

5. Zobrazte náhled seznamu stop.

    Po dokončení vytváření seznamu stop, stiskněte klávesu **Preview**.
6. Publikujte seznam stop.

    Po zobrazení náhledu seznam stop, můžete ho publikovat.

    Jakmile publikujete seznam stop, se přidá do seznamu video přehledy.


## <a name="next-steps"></a>Další postup 

Jakmile vytvoříte nový seznam stop, můžete dál, zpracování, jak je popsáno v těchto tématech: 

- [Zpracování obsahu s Video Indexer REST API](video-indexer-use-apis.md)
- [Vložení v aplikaci visual pomůcky](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Další informace najdete v tématech

[Video s přehledem indexeru](video-indexer-overview.md) 
