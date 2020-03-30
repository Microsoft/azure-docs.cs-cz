---
title: Přizpůsobení modelu značek pomocí webu Video Indexer
titleSuffix: Azure Media Services
description: Přečtěte si, jak přizpůsobit model značky pomocí webu Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 81df3897dff13823e4b97e10bc91d3a22b0e1b0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128042"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Přizpůsobení modelu značek pomocí webu Video Indexer

Video Indexer podporuje detekci značky z řeči a vizuálního textu během indexování a reindexingu video a audio obsahu. Funkce detekce značek identifikuje zmínky o produktech, službách a společnostech navržených databází značek Bingu. Pokud je například společnost Microsoft zmíněna ve videonebo zvukovém obsahu nebo pokud se ve videu zobrazuje ve vizuálním textu, video indexer ji v obsahu rozpozná jako značku.

Vlastní značky model umožňuje:

- Vyberte, pokud chcete, aby video indexer detekoval značky z databáze značek Bing.
- Vyberte, pokud chcete, aby video indexer vyloučil zjištění určitých značek (v podstatě vytvoření seznamu odepřít značky).
- Vyberte, pokud chcete, aby video indexer zahrnoval značky, které by měly být součástí vašeho modelu, které nemusí být v databázi značek Bingu (v podstatě vytvoření seznamu značek pro přijetí).

Podrobný přehled naleznete v tomto [přehledu](customize-brands-model-overview.md).

Pomocí webu Video Indexer můžete vytvářet, používat a upravovat vlastní modely značek zjištěné ve videu, jak je popsáno v tomto tématu. Rozhraní API můžete také použít, jak je popsáno v [modelu Přizpůsobit značky pomocí rozhraní API](customize-brands-model-with-api.md).

## <a name="edit-brands-model-settings"></a>Upravit nastavení modelu značek

Máte možnost nastavit, zda chcete zjistit značky z databáze značek Bing. Chcete-li tuto možnost nastavit, je třeba upravit nastavení modelu značek. Postupujte následovně:

1. Přejděte na web [Video Indexer](https://www.videoindexer.ai/) a přihlaste se.
2. Chcete-li přizpůsobit model v účtu, vyberte tlačítko **Přizpůsobení modelu obsahu** v pravém horním rohu stránky.

   ![Přizpůsobení modelu obsahu v videoindexeru](./media/content-model-customization/content-model-customization.png)

3. Chcete-li značky upravit, vyberte kartu **Značky.**

    ![Přizpůsobení modelu značek v videoindexeru](./media/customize-brand-model/customize-brand-model.png)

4. Pokud chcete, aby videoindexer detekoval značky navržené bingem, zaškrtněte **políčko Zobrazit značky navržené službou Bing** – pokud nechcete, ponechte tuto možnost nezaškrtnutou.

## <a name="include-brands-in-the-model"></a>Zahrnout značky do modelu

Část **Zahrnout značky** představuje vlastní značky, které má indexer videa zjistit, i když je bing nenavrhuje.  

### <a name="add-a-brand-to-include-list"></a>Přidání značky do seznamu

1. Vyberte **+ Přidat značku**.

    ![Přizpůsobení modelu značek v videoindexeru](./media/customize-brand-model/add-brand.png)

    Zadejte název (povinné), kategorii (volitelné), popis (volitelné) a referenční adresu URL (volitelné).
    Pole kategorie vám má pomoci označit vaše značky. Toto pole se při použití polí Video Indexer API zobrazí jako *značky značky.* Například značka "Azure" může být označena nebo kategorizována jako "Cloud".

    Referenční pole URL může být jakákoli referenční webová stránka pro značku (jako odkaz na stránku Wikipedie).

2. Vyberte **Přidat značku** a uvidíte, že značka byla přidána do seznamu **Zahrnout značky.**

### <a name="edit-a-brand-on-the-include-list"></a>Úprava značky v seznamu zahrnutí

1. Vyberte ikonu tužky vedle značky, kterou chcete upravit.

    Můžete aktualizovat kategorii, popis nebo referenční adresu URL značky. Název značky nemůžete změnit, protože názvy značek jsou jedinečné. Pokud potřebujete změnit značku, odstraňte celou značku (viz další část) a vytvořte novou značku s novým názvem.

2. Chcete-li značku aktualizovat novými informacemi, vyberte tlačítko **Aktualizovat.**

### <a name="delete-a-brand-on-the-include-list"></a>Odstranění značky v seznamu zahrnutí

1. Vyberte ikonu koše vedle značky, kterou chcete odstranit.
2. Vyberte **Odstranit** a značka se už nebude zobrazovat v seznamu *Zahrnout značky.*

## <a name="exclude-brands-from-the-model"></a>Vyloučit značky z modelu

Část **Vyloučit značky** představuje značky, které nechcete, aby indexer videa zjišťoval.

### <a name="add-a-brand-to-exclude-list"></a>Přidání značky do seznamu vyloučení

1. Vyberte **+ Přidat značku.**

    Uveďte název (povinné), kategorie (volitelné).

2. Vyberte **Přidat značku** a uvidíte, že značka byla přidána do seznamu *Vyloučit značky.*

### <a name="edit-a-brand-on-the-exclude-list"></a>Úprava značky v seznamu vyloučení

1. Vyberte ikonu tužky vedle značky, kterou chcete upravit.

    Můžete aktualizovat pouze kategorii značky. Název značky nemůžete změnit, protože názvy značek jsou jedinečné. Pokud potřebujete změnit značku, odstraňte celou značku (viz další část) a vytvořte novou značku s novým názvem.

2. Chcete-li značku aktualizovat novými informacemi, vyberte tlačítko **Aktualizovat.**

### <a name="delete-a-brand-on-the-exclude-list"></a>Odstranění značky v seznamu vyloučení

1. Vyberte ikonu koše vedle značky, kterou chcete odstranit.
2. Vyberte **Odstranit** a značka se už nebude zobrazovat v seznamu *Vyloučit značky.*

## <a name="next-steps"></a>Další kroky

[Přizpůsobení modelu značek pomocí api](customize-brands-model-with-api.md)
