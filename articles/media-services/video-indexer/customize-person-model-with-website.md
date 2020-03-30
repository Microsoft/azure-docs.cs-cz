---
title: Přizpůsobení modelu osoby pomocí webu Video Indexer
titleSuffix: Azure Media Services
description: Přečtěte si, jak přizpůsobit model osoby pomocí webu Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: f29e651f5c8542722f0dc2c9878184ac0d2a6a1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499974"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Přizpůsobení modelu osoby pomocí webu Video Indexer

Video Indexer podporuje rozpoznávání celebrit pro video obsah. Funkce rozpoznávání celebrit pokrývá přibližně jeden milion tváří založených na běžně požadovaném zdroji dat, jako je IMDB, Wikipedia a top LinkedIn influencers. Podrobný přehled najdete [v tématu Přizpůsobení modelu osoby v videoindexeru](customize-person-model-overview.md).

Pomocí webu Video Indexer můžete upravit tváře, které byly zjištěny ve videu, jak je popsáno v tomto tématu. Rozhraní API můžete také použít, jak je popsáno v [části Přizpůsobení modelu osoby pomocí rozhraní API](customize-person-model-with-api.md).

## <a name="central-management-of-person-models-in-your-account"></a>Centrální správa modelů osob ve vašem účtu

1. Chcete-li zobrazit, upravit a odstranit modely osob ve vašem účtu, přejděte na web Video Indexer a přihlaste se.

2. V pravém horním rohu stránky vyberte tlačítko přizpůsobení modelu obsahu.

    ![Přizpůsobení modelu obsahu](./media/customize-face-model/content-model-customization.png)

3. Vyberte kartu Lidé.

    Ve svém účtu se zobrazí model Výchozí osoba. Model Výchozí osoba obsahuje všechny plochy, které jste upravili nebo změnili v přehledech videí, u kterých jste během indexování nezadali vlastní model osoby.

    Pokud jste vytvořili jiné modely osob, budou také uvedeny na této stránce.

    ![Přizpůsobení modelu obsahu](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>Vytvoření nového modelu osoby

1. Vyberte tlačítko **+ Přidat model.**

    ![Přidání nového modelu osoby](./media/customize-face-model/add-new-person.png)

2. Zadejte název modelu a zaškrtněte tlačítko vedle názvu.

    ![Přidání nového modelu osoby](./media/customize-face-model/add-new-person2.png)

    Vytvořili jste nový model osoby. Nyní můžete přidat tváře do nového modelu Osoba.

3. Vyberte tlačítko nabídky seznamu a zvolte **+ Přidat osobu**.

    ![Přidání nového modelu osoby](./media/customize-face-model/add-new-person3.png)

## <a name="add-a-new-person-to-a-person-model"></a>Přidání nové osoby do modelu osoby

> [!NOTE]
> Video Indexer umožňuje přidat více lidí se stejným názvem v modelu osoba. Doporučujeme však, abyste každému člověku v modelu poskytli jedinečná jména pro použitelnost a srozumitelnost.

1. Chcete-li do modelu osoby přidat novou plochu, vyberte tlačítko nabídky seznamu vedle modelu Osoba, ke kterému chcete obličej přidat.

1. V yberte **+ Přidat osobu** z nabídky.

    ![Přidání nové tváře osobě](./media/customize-face-model/add-new-face.png)

    Vyskakovací okno vás vyzve k vyplnění údajů osoby. Zadejte jméno osoby a zaškrtněte políčko .

    ![Přidání nové tváře osobě](./media/customize-face-model/add-new-face2.png)

    Potom můžete vybrat z průzkumníka souborů nebo přetáhnout obrázky ploch. Video Indexer bude trvat všechny standardní typy souborů obrázku (např.: JPG, PNG a další).

    Video Indexer dokáže zjistit výskyty této osoby v budoucích videích, která indexujete, a aktuálních videí, která jste již indexovali, pomocí modelu Osoba, do kterého jste přidali tuto novou tvář. Uznání osoby ve vašich aktuálních videích může nějakou dobu trvat, než se projeví, protože se jedná o dávkový proces.

## <a name="rename-a-person-model"></a>Přejmenování modelu osoby

Můžete přejmenovat libovolný model osoby ve vašem účtu, včetně modelu výchozí osoby. I když svůj výchozí model osob přejmenujete, bude ve vašem účtu stále sloužit jako výchozí model osoby.

1. Vyberte tlačítko nabídky seznamu vedle modelu Osoba, který chcete přejmenovat.
2. V nabídce vyberte **Přejmenovat.**

    ![Přejmenování modelu osoby](./media/customize-face-model/rename-person.png)

3. Vyberte aktuální název modelu a zadejte nový název.

    ![Přejmenování modelu osoby](./media/customize-face-model/rename-person2.png)

4. Vyberte tlačítko kontroly, aby se model přejmenoval.

## <a name="delete-a-person-model"></a>Odstranění modelu osoby

Můžete odstranit libovolný model osoby, který jste vytvořili ve svém účtu. Výchozí model osoby však nelze odstranit.

1. V nabídce vyberte **Odstranit.**

    ![Odstranění modelu osoby](./media/customize-face-model/delete-person.png)

    Zobrazí se vyskakovací okno s upozorněním, že tato akce odstraní model osoby a všechny osoby a soubory, které obsahuje. Tuto akci nelze vrátit zpět.

    ![Odstranění modelu osoby](./media/customize-face-model/delete-person2.png)

1. Pokud jste si jisti, vyberte odstranit znovu.

> [!NOTE]
> Existující videa, která byla indexována pomocí tohoto (nyní odstraněného) modelu osoby, nepodporují možnost aktualizovat názvy tváří, které se ve videu zobrazují. Názvy tváří v těchto videích budete moci upravovat až poté, co je přeindexujete pomocí jiného modelu osoby. Pokud přeindexujete bez zadání modelu osoby, použije se výchozí model.

## <a name="manage-existing-people-in-a-person-model"></a>Správa stávajících lidí v modelu osoby

Pokud chcete zobrazit obsah některého z modelů osob, vyberte šipku vedle názvu modelu Osoba. Rozevírací seznam zobrazuje všechny osoby v daném modelu osoby. Pokud vyberete tlačítko nabídky seznamu vedle jednotlivých osob, zobrazí se možnosti správy, přejmenování a odstranění.  

![Přidání nové tváře osobě](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Přejmenování osoby

1. Chcete-li přejmenovat osobu v modelu osoby, vyberte tlačítko nabídky seznamu a z nabídky seznamu zvolte **Přejmenovat.**
1. Vyberte aktuální jméno osoby a zadejte nové jméno.
1. Vyberte tlačítko zaškrtnutí a osoba bude přejmenována.

### <a name="delete-a-person"></a>Odstranění osoby

1. Chcete-li odstranit osobu z modelu osoby, vyberte tlačítko nabídky seznamu a z nabídky seznamu zvolte **Odstranit.**
1. Vyskakovací okno informuje o tom, že tato akce osobu odstraní a že tuto akci nelze vrátit zpět.
1. Znovu vyberte **Odstranit** a odeberete osobu z modelu Osoba.

### <a name="manage-a-person"></a>Správa osoby

Pokud vyberete **Spravovat**, zobrazí se všechny plochy, ze kterých se tento model osoby trénovaný provádí. Tyto tváře pocházejí z výskytů této osoby ve videích, které používají tento model osoby, nebo z obrázků, které jste nahráli ručně.

Výběrem možnosti **Přidat obrázky**můžete k osobě přidat další plochy .

Podokno správy můžete použít k přejmenování osoby a k odstranění osoby z modelu osoby.

## <a name="use-a-person-model-to-index-a-video"></a>Indexování videa pomocí modelu osoby

Model osoby můžete použít k indexování nového videa přiřazením modelu Osoba během nahrávání videa.

Pokud chcete model osoby použít u nového videa, postupujte takto:

1. V horní části stránky vyberte tlačítko **Nahrát.**

    ![Nahrání modelu osoby](./media/customize-face-model/upload.png)

1. Upusťte videosoubor do kruhu nebo soubor vyhledejte.
1. Vyberte šipku **Upřesnit možnosti.**

    ![Nahrání modelu osoby](./media/customize-face-model/upload2.png)

1. Vyberte rozevírací seznam a vyberte model osoby, který jste vytvořili.

    ![Nahrání modelu osoby](./media/customize-face-model/upload3.png)

1. V dolní části stránky vyberte možnost **Nahrát** a nové video bude indexováno podle modelu osoby.

Pokud model osoby během nahrávání nezadáte, video indexer video indexuje pomocí modelu Výchozí osoba ve vašem účtu.

## <a name="use-a-person-model-to-reindex-a-video"></a>Přeindexování videa pomocí modelu osoby

Pokud chcete k přeindexování videa v kolekci použít model osoby, přejděte na domovskou stránku videoindexeru na videa svého účtu a najeďte na název videa, které chcete přeindexovat.

Zobrazí se možnosti úprav, odstranění a přeindexování videa.

1. Vyberte možnost přeindexování videa.

    ![Přeindexování videa pomocí modelu osoby](./media/customize-face-model/reindex.png)

    Nyní můžete vybrat model osoby, pomocí kterého chcete video přeindexovat.
1. Vyberte rozevírací seznam a vyberte model osoby, který chcete použít.

    ![Přeindexování videa pomocí modelu osoby](./media/customize-face-model/reindex2.png)

1. Vyberte tlačítko **Přeindexovat** a vaše video bude přeindexováno pomocí modelu Osoby.

Všechny nové úpravy, které provedete na tvářích zjištěných a rozpoznaných ve videu, které jste právě přeindexovali, budou uloženy v modelu Osoba, který jste použili k přeindexování videa.

## <a name="managing-people-in-your-videos"></a>Správa lidí ve vašich videích

Zjištěné plochy a osoby, které jsou rozpoznány ve videích, která indexujete, můžete spravovat úpravou a odstraněním ploch.

Odstraněním plochy odstraníte určitou plochu z přehledů videa.

Úprava obličeje přejmenuje plochu, která je ve videu rozpoznána a případně rozpoznána. Když ve videu upravíte obličej, toto jméno se uloží jako položka osoby v modelu osoba, která byla k videu přiřazena během nahrávání a indexování.

Pokud při nahrávání k videu nepřiřadíte model osoby, vaše úpravy se uloží do výchozího modelu vašeho účtu.

### <a name="edit-a-face"></a>Úprava plochy

> [!NOTE]
> Pokud má model Osoba dva nebo více různých lidí se stejným názvem, nebudete moci toto jméno označit ve videích, která používají tento model osoby. Změny budete moci provádět pouze u lidí, kteří toto jméno sdílejí, na kartě Lidé na stránce přizpůsobení modelu obsahu v indexeru videa. Z tohoto důvodu doporučujeme, abyste každé osobě v modelu osoby přidali jedinečná jména.

1. Přejděte na web Video Indexer a přihlaste se.
1. Vyhledejte video, které chcete zobrazit a upravit ve svém účtu.
1. Pokud chcete ve videu upravit obličej, přejděte na kartu Přehledy a vyberte ikonu tužky v pravém horním rohu okna.

    ![Úprava obličeje ve videu](./media/customize-face-model/edit-face.png)

1. Vyberte některou z detekovaných tváří a změňte jejich názvy z "Neznámý #X" (nebo název, který byl dříve přiřazen k obličeji).
1. Po zadání nového názvu vyberte ikonu kontroly vedle nového názvu. Tato akce uloží nový název a rozpozná a pojmenuje všechny výskyty této tváře v ostatních aktuálních videích a v budoucích videích, která nahrajete. Rozpoznání obličeje v ostatních aktuálních videích může nějakou dobu trvat, než se projeví, protože se jedná o dávkový proces.

Pokud pojmenujete obličej se jménem existující osoby v modelu Osoba, kterou video používá, zjištěné obrázky obličeje z tohoto videa této osoby se sloučí s tím, co již v modelu existuje. Pokud pojmenujete tvář s novým názvem, vytvoří se nová položka Osoba v modelu Osoba, kterou video používá.

![Úprava obličeje ve videu](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>Odstranění plochy

Pokud chcete odstranit zjištěnou tvář ve videu, přejděte do podokna Přehledy a vyberte ikonu tužky v pravém horním rohu podokna. Vyberte volbu **Odstranit** pod názvem plochy. Tato akce odstraní detekovaný obličej z videa. Tvář osoby bude stále rozpoznána v ostatních videích, ve kterých se zobrazuje, ale po jejich indexování můžete obličej z těchto videí odstranit i v těchto videích.

Osoba, pokud byla pojmenována, bude také nadále existovat v modelu Osoba, který byl použit k indexování videa, ze kterého jste odstranili obličej, pokud konkrétně neodstraníte osobu z modelu Osoba.

![Odstranění obličeje ve videu](./media/customize-face-model/delete-face.png)

## <a name="next-steps"></a>Další kroky

[Přizpůsobení modelu osoby pomocí api](customize-person-model-with-api.md)
