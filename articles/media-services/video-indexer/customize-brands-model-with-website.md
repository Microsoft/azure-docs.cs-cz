---
title: Přizpůsobení modelu značky – Azure pomocí webových stránek Video Indexer
titlesuffix: Azure Media Services
description: Tento článek ukazuje, jak upravit značky model s webem Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/03/2018
ms.author: anzaman
ms.openlocfilehash: 2522ede85c290fa238c0d5a5604d2dfbab984cdc
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285288"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Přizpůsobení modelu značky s webem Video Indexer

Video Indexer podporuje značky zjišťování z pro zpracování řeči a vizuální textu během indexování a přeindexování z obrazový a zvukový obsah. Funkce detekce značka identifikuje zmínky produktů, služeb, a společnosti navrhl Bingu značky databáze. Například pokud Microsoft je uveden v videa nebo zvukový obsah nebo pokud se zobrazí ve visual textu ve videu, Video Indexer rozpozná jako značku v obsahu. Vlastního modelu značky vám umožní vybrat, jestli je Video Indexer bude zjišťovat značky z Bingu nebo ne značky databáze, vyloučit určité značky nebudou zjištěny (v podstatě vytváření černého seznam značek) a obsahovat značky, které by měly být součástí modelu který nemusí být v databázi značky Bingu (v podstatě vytvoří prázdný seznam značek).

Podrobnější přehled najdete v části [přehled](customize-brands-model-overview.md).

Video Indexer webu můžete vytvářet, používat a upravovat vlastní modely značky zjistil ve videu, jak je popsáno v tomto tématu. Můžete také použít rozhraní API, jak je popsáno v [značky přizpůsobení modelu pomocí rozhraní API](customize-brands-model-with-api.md).

## <a name="edit-the-settings-of-the-brands-model"></a>Upravit nastavení tohoto modelu značky  

Máte možnost nastavit, jestli chcete, aby značky z databáze značky Bingu detekovanou. V takovém případě musíte upravit nastavení tohoto modelu značky.

1. Přejděte na web [Video Indexer](https://www.videoindexer.ai/) a přihlaste se.
2. Přizpůsobení modelu ve vašem účtu, klikněte na **obsahu přizpůsobení modelu** tlačítko v pravém horním rohu stránky.
 
   ![Přizpůsobení modelu obsahu](./media/content-model-customization/content-model-customization.png) 
3. Chcete-li upravit značky, vyberte **značky** kartu.

    ![Přizpůsobení modelu značky](./media/customize-brand-model/customize-brand-model.png)
4. Zkontrolujte **zobrazit značky navrhl Bingu** možnost, pokud chcete použít pro Video Indexer zahrnout značky navrhl Bingu. Tuto možnost nechte možnost nezaškrtnutou Pokud nechcete, aby Video Indexer ke zjištění značky navrhl Bingu ve vašem obsahu. 

## <a name="include-brands-in-the-model"></a>Zahrnout značky v modelu

**Obsahovat značky** oddíl představuje vlastní značky, které chcete použít pro Video Indexer ke zjišťování i v případě, že nejsou navrhl Bingu.  

### <a name="add-a-brand"></a>Přidání značek

1. Klikněte na tlačítko "+ Přidat značky".

    ![Přizpůsobení modelu značky](./media/customize-brand-model/add-brand.png)

    Zadejte název (povinné), kategorie (volitelné), popis (nepovinné) a odkazovat na adresu URL (nepovinné).
    Pole kategorie je určena při označení vašich značek. Toto pole ukazovat značku *značky* při použití rozhraní Video Indexer API. Například značku "Azure" můžete příznakem nebo klasifikovány jako "Cloud".

    Do pole Adresa URL odkazu může být jakékoli webu odkaz pro značku jít například o odkaz na stránku jeho encyklopedii Wikipedia.
2. Klikněte na tlačítko "Přidat značky" a uvidíte, že značka se přidala **obsahovat značky** seznamu.

### <a name="edit-a-brand"></a>Úprava značek

1. Klikněte na ikonu tužky vedle značku, kterou chcete upravit.

    Můžete aktualizovat kategorii, popis a adresa URL odkazu značek. Název značky nemůže změnit, protože názvy značek musí být jedinečné. Pokud potřebujete změnit název značky, odstranit celý značky (viz další část) a vytvořte novou značku s novým názvem.
2. Klikněte na tlačítko **aktualizovat** tlačítko Aktualizovat značku novými informacemi.

### <a name="delete-a-brand"></a>Odstranění značek

1. Klikněte na ikonu koše vedle značku, kterou chcete odstranit.
2. Klikněte na možnost "Odstranit" a značka se nebude zobrazovat ve vašich *obsahovat značky* seznamu.

## <a name="exclude-brands-from-the-model"></a>Vyloučení značek z modelu

**Vyloučení značek** oddíl představuje značky, které chcete použít pro Video Indexer nechcete zjišťovat.

### <a name="add-a-brand"></a>Přidání značek

1. Klikněte na tlačítko "+ Přidat značky".

    Zadejte název (povinné), kategorie (volitelné).
2. Klikněte na tlačítko "Přidat značky" a uvidíte, že značka se přidala *vyloučení značek* seznamu.

### <a name="edit-a-brand"></a>Úprava značek

1. Klikněte na ikonu tužky vedle značku, kterou chcete upravit.

    Lze aktualizovat pouze kategorii značek. Název značky nemůže změnit, protože názvy značek musí být jedinečné. Pokud potřebujete změnit název značky, odstranit celý značky (viz další část) a vytvořte novou značku s novým názvem.
2. Klikněte na tlačítko **aktualizovat** tlačítko Aktualizovat značku novými informacemi.

### <a name="delete-a-brand"></a>Odstranění značek

1. Klikněte na ikonu koše vedle značku, kterou chcete odstranit.
2. Klikněte na možnost "Odstranit" a značka se nebude zobrazovat ve vašich *vyloučení značek* seznamu.

## <a name="next-steps"></a>Další postup

[Upravit značky modelu s použitím rozhraní API](customize-brands-model-with-api.md)
