---
title: Přizpůsobení modelu značek pomocí Video Indexer webu
titleSuffix: Azure Media Services
description: Naučte se, jak přizpůsobit model značek pomocí Video Indexer webu.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/15/2019
ms.author: kumud
ms.openlocfilehash: a2de9dbb479f43d6b646cd9f6cf604d6a08c8b6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586094"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Přizpůsobení modelu značek pomocí Video Indexer webu

Video Indexer podporuje rozpoznávání značek od mluvené řeči a vizuálního textu během indexování a přeindexování videa a zvukového obsahu. Funkce detekce značky identifikuje zmínky o produktech, službách a společnostech, které navrhla databáze značek Bingu. Pokud je například Microsoft zmíněný ve videu nebo zvukovém obsahu nebo pokud se zobrazuje v obrazovém textu ve videu, Video Indexer ho detekuje jako značku v obsahu.

Vlastní model značek vám umožní:

- tuto možnost vyberte, pokud chcete, aby Video Indexer zjišťoval značky z databáze značek Bingu.
- Vyberte, pokud chcete, aby se zjistilo, že se mají vyVideo Indexer vyloučit některé značky (v podstatě vytváření seznamu zakázaných značek).
- tuto možnost vyberte, pokud chcete, aby Video Indexer zahrnuly značky, které by měly být součástí modelu, který nemusí být v databázi značek Bingu (v podstatě vytvoření seznamu pro příjem značek).

Podrobný přehled najdete v tomto [přehledu](customize-brands-model-overview.md).

Web Video Indexer můžete použít k vytváření, používání a úpravám vlastních značek, které byly zjištěny ve videu, jak je popsáno v tomto tématu. Můžete také použít rozhraní API, jak je popsáno v tématu [Přizpůsobení modelu značek pomocí rozhraní API](customize-brands-model-with-api.md).

> [!NOTE]
> Pokud bylo video indexováno před přidáním značky, je nutné ho znovu indexovat. V rozevírací nabídce přidružené k videu najdete položku **opětovného indexování** . Vyberte **Pokročilé možnosti**  ->  **Kategorie značky** a zaškrtněte **všechny značky**.

## <a name="edit-brands-model-settings"></a>Upravit nastavení modelu značek

Máte možnost nastavit, jestli chcete, aby se zjistily značky z databáze značek Bingu. Chcete-li nastavit tuto možnost, je nutné upravit nastavení modelu značek. Postupujte takto:

1. Přejít na web [video indexer](https://www.videoindexer.ai/) a přihlásit se.
1. Pokud chcete ve svém účtu přizpůsobit model, vyberte tlačítko **Přizpůsobení modelu obsahu** na levé straně stránky.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="Přizpůsobení modelu obsahu v Video Indexer":::
1. Chcete-li upravit značky, vyberte kartu **značky** .

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-brand-model/customize-brand-model.png" alt-text="Snímek obrazovky se zobrazí v dialogovém okně Přizpůsobení modelu obsahu – karta značky":::
1. Pokud chcete, aby Video Indexer zjistila značky navržené v rámci Bingu, přečtěte si možnost **Zobrazit značky navržené podle Bingu** . Pokud to neuděláte, nechte možnost nezaškrtnutou.

## <a name="include-brands-in-the-model"></a>Zahrnutí značek do modelu

Oddíl **include** Tags reprezentuje vlastní značky, které chcete video indexer rozpoznat, i když je služba Bing nenavrhuje.  

### <a name="add-a-brand-to-include-list"></a>Přidat značku k seznamu zahrnutí

1. Vyberte **+ vytvořit novou značku**.

    Zadejte název (povinné), kategorii (volitelné), popis (nepovinné) a adresu URL odkazu (volitelné).
    Pole kategorie je určeno, aby vám pomohly označit značky. Toto pole se zobrazuje jako *značka* značky při použití video indexer rozhraní API. Například značka "Azure" může být označená nebo Kategorizovaná jako "Cloud".

    Pole Adresa URL odkazu může být libovolný referenční web pro značku (například odkaz na jeho stránku Wikipedii).

2. Vyberte **Uložit** a uvidíte, že byla značka přidána do seznamu **vložených značek** .

### <a name="edit-a-brand-on-the-include-list"></a>Úprava značky v seznamu zahrnutí

1. Vyberte ikonu tužky vedle značky, kterou chcete upravit.

    Můžete aktualizovat kategorii, popis nebo adresu URL odkazu na značku. Nemůžete změnit název značky, protože názvy značek jsou jedinečné. Pokud potřebujete změnit název značky, odstraňte celou značku (viz další část) a vytvořte novou značku s novým názvem.

2. Kliknutím na tlačítko **aktualizovat** aktualizujte značku novými informacemi.

### <a name="delete-a-brand-on-the-include-list"></a>Odstranění značky v seznamu zahrnutí

1. Vyberte ikonu odpadkového koše vedle značky, kterou chcete odstranit.
2. Vyberte **Odstranit** a značka se už nebude zobrazovat v seznamu *vložených značek* .

## <a name="exclude-brands-from-the-model"></a>Vyloučení značek z modelu

Část **vyloučení značek** představuje značky, které nechcete video indexer rozpoznat.

### <a name="add-a-brand-to-exclude-list"></a>Přidat značku k seznamu vyloučení

1. Vyberte **+ vytvořit novou značku.**

    Zadejte název (povinné), kategorii (volitelné).

2. Vyberte **Uložit** a uvidíte, že byla značka přidána do seznamu *vyloučených značek* .

### <a name="edit-a-brand-on-the-exclude-list"></a>Úprava značky na seznamu vyloučení

1. Vyberte ikonu tužky vedle značky, kterou chcete upravit.

    Můžete aktualizovat pouze kategorii značky. Nemůžete změnit název značky, protože názvy značek jsou jedinečné. Pokud potřebujete změnit název značky, odstraňte celou značku (viz další část) a vytvořte novou značku s novým názvem.

2. Kliknutím na tlačítko **aktualizovat** aktualizujte značku novými informacemi.

### <a name="delete-a-brand-on-the-exclude-list"></a>Odstranění značky na seznamu vyloučení

1. Vyberte ikonu odpadkového koše vedle značky, kterou chcete odstranit.
2. Vyberte **Odstranit** a značka se už nebude zobrazovat v seznamu *vyloučení značek* .

## <a name="next-steps"></a>Další kroky

[Přizpůsobení modelu značek pomocí rozhraní API](customize-brands-model-with-api.md)
