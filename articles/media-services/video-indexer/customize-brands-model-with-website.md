---
title: Použití Video Indexer webu k přizpůsobení značek modelů – Azure
titleSuffix: Azure Media Services
description: Tento článek popisuje, jak přizpůsobit model značek pomocí webu Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 956ca7af055768398392045ecf9b383d2eb1060f
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513894"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Přizpůsobení modelu značek pomocí Video Indexer webu

Video Indexer podporuje rozpoznávání značek od mluvené řeči a vizuálního textu během indexování a přeindexování videa a zvukového obsahu. Funkce detekce značky identifikuje zmínky o produktech, službách a společnostech, které navrhla databáze značek Bingu. Například pokud se Microsoft zmiňuje v obrazovém nebo zvukovém obsahu nebo pokud se zobrazuje v vizuálním textu ve videu, Video Indexer ho detekuje jako značku v obsahu. Vlastní model značek umožňuje vybrat, jestli Video Indexer budou rozpoznávat značky z databáze značek Bingu, vyloučit některé značky z detekce (v podstatě vytváření černého seznamu značek) a zahrnout značky, které by měly být součástí vašeho modelu. to nemusí být v databázi značek Bingu (v podstatě vytváření bílého seznamu značek).

Podrobný přehled najdete v tématu [Přehled](customize-brands-model-overview.md).

Web Video Indexer můžete použít k vytváření, používání a úpravám vlastních značek, které byly zjištěny ve videu, jak je popsáno v tomto tématu. Můžete také použít rozhraní API, jak je popsáno v tématu [Přizpůsobení modelu značek pomocí rozhraní API](customize-brands-model-with-api.md).

## <a name="edit-the-settings-of-the-brands-model"></a>Úprava nastavení modelu značek  

Máte možnost nastavit, jestli chcete, aby se zjistily značky z databáze značek Bingu. V takovém případě je nutné upravit nastavení modelu značek.

1. Přejděte na web [Video Indexer](https://www.videoindexer.ai/) a přihlaste se.
2. Pokud chcete ve svém účtu přizpůsobit model, klikněte na tlačítko **Přizpůsobení modelu obsahu** v pravém horním rohu stránky.
 
   ![Přizpůsobení modelu obsahu](./media/content-model-customization/content-model-customization.png) 
3. Chcete-li upravit značky, vyberte kartu **značky** .

    ![Přizpůsobení modelu značek](./media/customize-brand-model/customize-brand-model.png)
4. Pokud chcete, aby Video Indexer zahrnula značky navržené v rámci Bingu, přečtěte si možnost **Zobrazit značky navržené podle Bingu** . Tuto možnost nechte nezaškrtnuté, pokud nechcete, Video Indexer ke zjištění značek navrhovaných bingem v obsahu. 

## <a name="include-brands-in-the-model"></a>Zahrnutí značek do modelu

Oddíl **include** Tags reprezentuje vlastní značky, které chcete video indexer rozpoznat, i když je služba Bing nenavrhuje.  

### <a name="add-a-brand"></a>Přidat značku

1. Klikněte na + Přidat značku.

    ![Přizpůsobení modelu značek](./media/customize-brand-model/add-brand.png)

    Zadejte název (povinné), kategorii (volitelné), popis (nepovinné) a adresu URL odkazu (volitelné).
    Pole kategorie je určeno, aby vám pomohly označit značky. Toto pole se zobrazuje jako *značka* značky při použití video indexer rozhraní API. Například značka "Azure" může být označená nebo Kategorizovaná jako "Cloud".

    Pole Adresa URL odkazu může být libovolný referenční web pro značku, jako je například odkaz na svou Wikipedii stránku.
2. Klikněte na Přidat značku a uvidíte, že se do seznamu **Zahrnutí značek** přidala značka.

### <a name="edit-a-brand"></a>Úprava značky

1. Klikněte na ikonu tužky vedle značky, kterou chcete upravit.

    Můžete aktualizovat kategorii, popis nebo adresu URL odkazu na značku. Nemůžete změnit název značky, protože názvy značek jsou jedinečné. Pokud potřebujete změnit název značky, odstraňte celou značku (viz další část) a vytvořte novou značku s novým názvem.
2. Kliknutím na tlačítko **aktualizovat** aktualizujte značku novými informacemi.

### <a name="delete-a-brand"></a>Odstranění značky

1. Klikněte na ikonu koše vedle značky, kterou chcete odstranit.
2. Klikněte na Odstranit a značka se už nebude zobrazovat v seznamu *zahrnutých značek* .

## <a name="exclude-brands-from-the-model"></a>Vyloučení značek z modelu

Část **vyloučení značek** představuje značky, které chcete, aby se video indexer nerozpoznaly.

### <a name="add-a-brand"></a>Přidat značku

1. Klikněte na + Přidat značku.

    Zadejte název (povinné), kategorii (volitelné).
2. Klikněte na Přidat značku a uvidíte, že se do seznamu *vyloučení značek* přidala značka.

### <a name="edit-a-brand"></a>Úprava značky

1. Klikněte na ikonu tužky vedle značky, kterou chcete upravit.

    Můžete aktualizovat pouze kategorii značky. Nemůžete změnit název značky, protože názvy značek jsou jedinečné. Pokud potřebujete změnit název značky, odstraňte celou značku (viz další část) a vytvořte novou značku s novým názvem.
2. Kliknutím na tlačítko **aktualizovat** aktualizujte značku novými informacemi.

### <a name="delete-a-brand"></a>Odstranění značky

1. Klikněte na ikonu koše vedle značky, kterou chcete odstranit.
2. Klikněte na Odstranit a značka se už nebude zobrazovat v seznamu *vyloučených značek* .

## <a name="next-steps"></a>Další kroky

[Přizpůsobení modelu značek pomocí rozhraní API](customize-brands-model-with-api.md)
