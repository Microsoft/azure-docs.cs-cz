---
title: Použití Video Indexer webu k přizpůsobení modelu osoby – Azure
titleSuffix: Azure Media Services
description: Tento článek popisuje, jak přizpůsobit model osoby pomocí Video Indexer webu.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 060d94d6181e894c18d268845b48eb802c52730c
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838275"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Přizpůsobení modelu osoby pomocí Video Indexer webu

Video Indexer podporuje rozpoznávání celebrit pro obsah videa. Funkce rozpoznávání celebrit se zabývá přibližně 1 000 000 obličejemi na základě běžně vyžádaného zdroje dat, jako jsou IMDB, Wikipedii a nejvyšší vlivy na LinkedIn. Podrobný přehled najdete [v tématu Přizpůsobení modelu osoby v video indexer](customize-person-model-overview.md).

Web Video Indexer můžete použít k úpravě ploch zjištěných ve videu, jak je popsáno v tomto tématu. Můžete také použít rozhraní API, jak je popsáno v tématu [Přizpůsobení modelu osoby pomocí rozhraní API](customize-person-model-with-api.md).

## <a name="central-management-of-person-models-in-your-account"></a>Centrální Správa modelů osob v účtu

1. Pokud chcete zobrazit, upravit a odstranit modely osob v účtu, přejděte na Web Video Indexer a přihlaste se.
2. Klikněte na tlačítko Přizpůsobení modelu obsahu v pravém horním rohu stránky.

    ![Přizpůsobení modelu obsahu](./media/customize-face-model/content-model-customization.png)
3. Vyberte kartu osoby.

    Ve vašem účtu se zobrazí výchozí model osoby. Model výchozí osoby obsahuje všechny plošky, které jste si v přehledu vašich videí mohli upravit nebo změnit, pro které jste při indexování neurčili vlastní model osoby. 

    Pokud jste vytvořili jiné modely osob, budou uvedeny i na této stránce. 

    ![Přizpůsobení modelu obsahu](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>Vytvoření nového modelu osoby

1. Klikněte na tlačítko **+ přidat model** .

    ![Přidat novou osobu](./media/customize-face-model/add-new-person.png)
2. Zadejte název modelu a klikněte na tlačítko zaškrtnutí vedle názvu.

    ![Přidat novou osobu](./media/customize-face-model/add-new-person2.png)

    Vytvořili jste nový model osoby. Nyní můžete přidat plošky do modelu nové osoby.
3. Klikněte na tlačítko nabídky se seznamem a vyberte **+ Přidat osobu**.

    ![Přidat novou osobu](./media/customize-face-model/add-new-person3.png)
    
## <a name="add-a-new-person-to-a-person-model"></a>Přidat novou osobu do modelu osoby

> [!NOTE]
> Video Indexer umožňuje přidat více osob se stejným názvem v modelu osoby. Doporučuje se ale každému uživateli v modelu udělit unque jména pro použitelnost a přehlednost.

1. Chcete-li přidat novou plochu do modelu osoby, klikněte na tlačítko nabídky seznam vedle modelu osoby, ke kterému chcete přidat obličej.
1. V nabídce klikněte na **+ Přidat osobu** .

    ![Přidat k osobě novou plochu](./media/customize-face-model/add-new-face.png)
 
    Automaticky otevírané okno vás vyzve, abyste vyplnili podrobnosti o této osobě. Zadejte jméno osoby a klikněte na tlačítko zaškrtávací políčko.

    ![Přidat k osobě novou plochu](./media/customize-face-model/add-new-face2.png)
 
Pak si můžete vybrat z Průzkumníka souborů nebo přetáhnout plošky na plošku. Video Indexer budou přebírat všechny standardní typy obrázkových souborů (např.: JPG, PNG a další).

Video Indexer by měl být schopný detekovat výskyty této osoby v budoucích videích, která jste indexoval, a aktuální videa, která jste už nastavili jako indexovanou, pomocí modelu osoby, ke kterému jste tuto novou plochu přidali. Rozpoznávání osoby v aktuálních videích může trvat delší dobu, protože se jedná o dávkový proces.


## <a name="rename-a-person-model"></a>Přejmenování modelu osoby

Ve svém účtu můžete přejmenovat libovolný model osoby včetně modelu výchozí osoby. I když přejmenujete výchozí pracovní model, bude i nadále sloužit jako výchozí model osoby ve vašem účtu.

1. Klikněte na tlačítko nabídky seznam vedle modelu osoby, který chcete přejmenovat.
2. V nabídce klikněte na **Přejmenovat** .

    ![Přejmenování osoby](./media/customize-face-model/rename-person.png)
3. Klikněte na aktuální název modelu a zadejte nový název.

    ![Přejmenování osoby](./media/customize-face-model/rename-person2.png)
4. Klikněte na tlačítko pro kontrolu pro váš model, který chcete přejmenovat.

## <a name="delete-a-person-model"></a>Odstranění modelu osoby

Můžete odstranit jakýkoli model osoby, který jste ve svém účtu vytvořili. Nemůžete ale odstranit výchozí pracovní model.

1. V nabídce klikněte na **Odstranit** .

    ![Odstranit osobu](./media/customize-face-model/delete-person.png)
    
    Automaticky otevírané okno se zobrazí a upozorní vás, že tato akce odstraní model osoby a všechny osoby a soubory, které obsahuje. Tuto akci nelze vrátit zpět. 

    ![Odstranit osobu](./media/customize-face-model/delete-person2.png)
1. Pokud jste si jisti, klikněte znovu na Odstranit.

> [!NOTE]
> Existující videa, která byla indexována pomocí tohoto (nyní odstraněného) modelu osoby, nebudou podporovat možnost aktualizovat názvy plošek, které se zobrazí ve videu. Názvy plošek v těchto videích budete moct upravovat až po jejich přeindexaci pomocí jiného modelu osoby. Pokud předáte index bez zadání modelu osoby, použije se výchozí model. 

## <a name="manage-existing-people-in-a-person-model"></a>Správa stávajících osob v modelu person

Pokud se chcete podívat na obsah libovolného z vašich osobních modelů, klikněte na šipku vedle názvu modelu osoby.
V rozevíracím seznamu se zobrazí všichni lidé z tohoto modelu konkrétní osoby. Pokud kliknete na tlačítko nabídky seznam vedle každé osoby, zobrazí se možnosti spravovat, přejmenovat a odstranit.  

![Přidat k osobě novou plochu](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Přejmenování osoby

1. Pokud chcete uživatele v modelu osoby přejmenovat, klikněte na tlačítko nabídky seznam a v nabídce seznam vyberte **Přejmenovat** .
1. Klikněte na aktuální jméno osoby a zadejte nový název.
1. Klikněte na tlačítko pro kontrolu a osoba se přejmenuje.

### <a name="delete-a-person"></a>Odstranit osobu

1. Pokud chcete osobu odstranit z modelu osoby, klikněte na tlačítko nabídky seznam a v nabídce seznam vyberte **Odstranit** .
1. V automaticky otevíraném okně se dozvíte, že tato akce odstraní osobu a že tuto akci nelze vrátit zpět.
1. Znovu klikněte na tlačítko Odstranit a tato akce odebere osobu z modelu osoby.

### <a name="manage-a-person"></a>Správa osoby

Pokud kliknete na možnost **Spravovat**, zobrazí se všechny plošky, ze kterých je tento IT model vyškolený. Tyto plošky pocházejí z výskytů této osoby ve videích, které používají tento model osoby, nebo z obrázků, které jste ručně nahráli. 

Kliknutím na Přidat obrázky můžete této osobě přidat další plošky.

K přejmenování osoby můžete použít podokno Správa a odstranit osobu z modelu osoby.

## <a name="use-a-person-model-to-index-a-video"></a>Použití modelu osoby k indexování videa

K indexování nového videa můžete použít pracovní model přiřazením modelu osoby během nahrávání videa.

Pokud chcete použít svůj pracovní model na novém videu, udělejte toto:

1. Klikněte na tlačítko **nahrát** v horní části stránky.

    ![Odeslat](./media/customize-face-model/upload.png)
1. Přetáhněte videosoubor do kruhu nebo ho vyhledejte.
1. Klikněte na šipku Upřesnit možnosti.

    ![Odeslat](./media/customize-face-model/upload2.png)
1. Klikněte na rozevírací seznam a vyberte model osoby, který jste vytvořili.

    ![Odeslat](./media/customize-face-model/upload3.png)
1. V dolní části stránky klikněte na možnost nahrát a nové video bude indexováno pomocí modelu vaší osoby.

Pokud během nahrávání nezadáte model osoby, Video Indexer bude video indexovat pomocí výchozího modelu osoby ve vašem účtu.

## <a name="use-a-person-model-to-reindex-a-video"></a>Použití modelu osoby k přeindexaci videa 

Pokud chcete k reindexaci videa v kolekci použít pracovní model, přejděte na video o účtu na domovské stránce Video Indexer a najeďte myší na název videa, které chcete znovu indexovat.

Zobrazí se možnosti pro úpravu, odstranění a přeindexování videa. 

1. Klikněte na možnost pro Reindexování videa.

    ![Přeindexování](./media/customize-face-model/reindex.png)

    Nyní můžete vybrat model osoby, pomocí kterého se má video znovu indexovat.
1. Klikněte na rozevírací seznam a vyberte model osoby, který chcete použít. 

    ![Přeindexování](./media/customize-face-model/reindex2.png)

1. Klikněte na tlačítko **Přeindexovat** a vaše video se přeindexuje pomocí modelu osoby.

Všechny nové úpravy, které jste provedli u plošek zjištěných a rozpoznaných ve videu, které jste právě reindexováni, budou uloženy v modelu osoby, který jste použili k přeindexování videa.

## <a name="managing-people-in-your-videos"></a>Správa lidí ve vašich videích

Můžete spravovat plošky, které jsou zjištěny, a lidi, kteří byli rozpoznáni v videích, které můžete indexovat úpravou a odstraněním ploch.

Odstranění obličeje odstraní konkrétní obličej z přehledu videa.

Úprava plochy, přejmenuje plošku, která je ve videu zjištěná a možná rozpoznaná. Když ve videu upravíte plošku, tento název se uloží jako položka osoby v modelu osoby, která se přiřadila videu během nahrávání a indexování.

Pokud se k videu během nahrávání nepřiřadíte model osoby, vaše úprava se uloží do modelu výchozí osoby vašeho účtu.

### <a name="edit-a-face"></a>Úprava plochy


> [!NOTE]
> Pokud má model osoby dva nebo více různých lidí se stejným názvem, nebudete moci označit tento název v videích, které používají daný pracovní model. Na kartě osoby na stránce přizpůsobení modelu obsahu v Video Indexer budete moct provádět změny jenom pro lidi, kteří tento název sdílí. Z tohoto důvodu doporučujeme, abyste každému uživateli přiřadíte jedinečné názvy v modelu vaší osoby.

1. Přejděte na Web Video Indexer a přihlaste se.
1. Vyhledejte video, které chcete ve svém účtu zobrazit a upravit.
1. Pokud chcete ve videu upravit plošku, přejděte na kartu Přehledy a klikněte na ikonu tužky v pravém horním rohu okna.

    ![Úprava obličeje ve videu](./media/customize-face-model/edit-face.png)
1. Klikněte na kteroukoli z zjištěných plošek a změňte jejich názvy z "Neznámý #X" (nebo název, který byl dříve přiřazený k obličeji). 
1. Po zadání nového názvu klikněte na ikonu zaškrtnutí vedle nového názvu. Tím se uloží nový název a rozpoznává a pojmenuje všechny výskyty této plošky na vašich dalších současných videích a v budoucích videích, která nahráváte. Rozpoznávání obličeje v ostatních aktuálních videích může nějakou dobu trvat, protože se jedná o dávkový proces.

Pokud se vám pojmenuje obličej s názvem existující osoby v modelu osoby, který video používá, sloučí se zjištěné obrázky z tohoto videa o této osobě s tím, co už v modelu existuje. Pokud vytvoříte tvář s úplně novým názvem, vytvoří se nová položka osoby v modelu osoby, kterou video používá. 

    ![Edit a face in your video](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>Odstranit obličej

Pokud chcete ve videu odstranit zjištěnou plošku, přejděte do podokna přehled a klikněte na ikonu tužky v pravém horním rohu podokna. Klikněte na možnost Odstranit pod názvem obličeje. Tato akce odebere z videa zjištěnou plochu. Ploška osoby bude i nadále detekována v dalších videích, ve kterých se zobrazí, ale z videí můžete odstranit i po jejich indexování. Osoba, pokud byla pojmenována, bude i nadále existovat v modelu osoby, který byl použit k indexování videa, ze kterého jste odstranili plochu, pokud výslovně neodstraníte osobu z modelu osoby.

![Odstranění obličeje ve videu](./media/customize-face-model/delete-face.png)


## <a name="next-steps"></a>Další kroky

[Přizpůsobení modelu osoby pomocí rozhraní API](customize-person-model-with-api.md)
