---
title: Přizpůsobení modelu osoby pomocí Video Indexer webu
titleSuffix: Azure Media Services
description: Naučte se, jak přizpůsobit model osoby pomocí Video Indexer webu.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/06/2020
ms.author: kumud
ms.openlocfilehash: 9f5a18cc51aa92b53a5391404b1e9d3fc800bb47
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91356617"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Přizpůsobení modelu osoby pomocí Video Indexer webu

Video Indexer podporuje rozpoznávání celebrit pro obsah videa. Funkce rozpoznávání celebrit se zabývá přibližně 1 000 000 obličejemi na základě běžně vyžádaného zdroje dat, jako jsou IMDB, Wikipedii a nejvyšší vlivy na LinkedIn. Podrobný přehled najdete [v tématu Přizpůsobení modelu osoby v video indexer](customize-person-model-overview.md).

Web Video Indexer můžete použít k úpravě ploch zjištěných ve videu, jak je popsáno v tomto tématu. Můžete také použít rozhraní API, jak je popsáno v tématu [Přizpůsobení modelu osoby pomocí rozhraní API](customize-person-model-with-api.md).

## <a name="central-management-of-person-models-in-your-account"></a>Centrální Správa modelů osob v účtu

1. Pokud chcete zobrazit, upravit a odstranit modely osob v účtu, přejděte na Web Video Indexer a přihlaste se.

2. V pravém horním rohu stránky vyberte tlačítko pro přizpůsobení obsahu modelu.

    ![Snímek obrazovky s vybraným tlačítkem přizpůsobení Content model zobrazí Video Indexer.](./media/customize-face-model/content-model-customization.png)

3. Vyberte kartu osoby.

    Ve svém účtu se zobrazí výchozí model osoby. Model výchozí osoby obsahuje všechny plošky, které jste si v přehledu vašich videí mohli upravit nebo změnit, pro které jste při indexování neurčili vlastní model osoby.

    Pokud jste vytvořili jiné modely osob, budou také uvedeny na této stránce.

    ![Snímek obrazovky se zobrazí v dialogovém okně Přizpůsobení modelu obsahu.](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>Vytvoření nového modelu osoby

1. Vyberte tlačítko **+ přidat model** .

    ![Snímek obrazovky se zobrazí dialogové okno Přizpůsobení modelu obsahu s vybranou kartou lidé.](./media/customize-face-model/add-new-person.png)

2. Zadejte název modelu a zaškrtněte tlačítko zaškrtnutí vedle názvu.

    ![Snímek obrazovky se zobrazí dialogové okno Přizpůsobení modelu obsahu s názvem zadaným pro model.](./media/customize-face-model/add-new-person2.png)

    Vytvořili jste nový model Person. Nyní můžete přidat plošky do modelu nové osoby.

3. Vyberte tlačítko nabídky seznam a zvolte **+ Přidat osobu**.

    ![Snímek obrazovky se zobrazí v dialogovém okně Přizpůsobení modelu obsahu s místní nabídkou pro nový model s možností přidat osobu.](./media/customize-face-model/add-new-person3.png)

## <a name="add-a-new-person-to-a-person-model"></a>Přidat novou osobu do modelu osoby

> [!NOTE]
> Video Indexer umožňuje přidat více osob se stejným názvem v modelu osoby. Doporučuje se ale každému uživateli v modelu udělit jedinečné názvy pro použitelnost a přehlednost.

1. Pokud chcete přidat novou plochu do modelu osoby, vyberte tlačítko nabídky seznam vedle modelu osoby, ke kterému chcete přidat obličej.

1. V nabídce vyberte **+ Přidat osobu** .

    ![Snímek obrazovky s dialogovým oknem přizpůsobení modelu obsahu, který je připravený k přidání osoby s místní nabídkou](./media/customize-face-model/add-new-face.png)

    Automaticky otevírané okno vás vyzve, abyste vyplnili podrobnosti o této osobě. Zadejte jméno osoby a zaškrtněte tlačítko zaškrtnutí.

    ![Snímek obrazovky se zobrazí podrobnosti o uživateli.](./media/customize-face-model/add-new-face2.png)

    Pak si můžete vybrat z Průzkumníka souborů nebo přetáhnout plošky na plošku. Video Indexer budou přebírat všechny standardní typy obrázkových souborů (např.: JPG, PNG a další).

    Video Indexer může zjistit výskyty této osoby v budoucích videích, která jste indexoval, a aktuální videa, která jste už nastavili, pomocí Personového modelu, ke kterému jste přidali tuto novou plochu. Rozpoznávání osoby v aktuálních videích může trvat delší dobu, protože se jedná o dávkový proces.

## <a name="rename-a-person-model"></a>Přejmenování modelu osoby

Ve svém účtu můžete přejmenovat libovolný model osoby včetně modelu výchozí osoby. I když přejmenujete výchozí pracovní model, bude i nadále sloužit jako výchozí model osoby ve vašem účtu.

1. Vyberte tlačítko nabídky seznam vedle modelu osoby, který chcete přejmenovat.
2. V nabídce vyberte **Přejmenovat** .

    ![Snímek obrazovky s dialogovým oknem přizpůsobení modelu obsahu obsahuje místní nabídku s možností přejmenovat model osoby. ](./media/customize-face-model/rename-person.png)

3. Vyberte aktuální název modelu a zadejte nový název.

    ![Snímek obrazovky se zobrazí v přejmenovaném modelu osoby.](./media/customize-face-model/rename-person2.png)

4. Zaškrtněte tlačítko zaškrtnutí pro váš model, který chcete přejmenovat.

## <a name="delete-a-person-model"></a>Odstranění modelu osoby

Můžete odstranit jakýkoli model osoby, který jste ve svém účtu vytvořili. Nemůžete ale odstranit výchozí pracovní model.

1. V nabídce vyberte **Odstranit** .

    ![Snímek obrazovky s dialogovým oknem přizpůsobení modelu obsahu obsahuje místní nabídku s možností odstranit model osoby.](./media/customize-face-model/delete-person.png)

    Automaticky otevírané okno se zobrazí a upozorní vás, že tato akce odstraní model osoby a všechny osoby a soubory, které obsahuje. Tuto akci nejde vrátit zpátky.

    ![Snímek obrazovky se zobrazí dialogové okno Potvrdit pro odstranění modelu osoby.](./media/customize-face-model/delete-person2.png)

1. Pokud si jste jisti, vyberte odstranit znovu.

> [!NOTE]
> Existující videa, která byla indexována pomocí tohoto (nyní odstraněného) modelu osoby, nepodporují možnost aktualizovat názvy plošek, které se zobrazí ve videu. Názvy plošek v těchto videích budete moct upravovat až po jejich přeindexaci pomocí jiného modelu osoby. Pokud předáte index bez zadání modelu osoby, použije se výchozí model.

## <a name="manage-existing-people-in-a-person-model"></a>Správa stávajících osob v modelu person

Pokud se chcete podívat na obsah některého z vašich osobních modelů, vyberte šipku vedle názvu modelu osoby. V rozevíracím seznamu se zobrazí všichni lidé z tohoto modelu konkrétní osoby. Když vyberete tlačítko nabídky seznam vedle každé osoby, zobrazí se možnosti spravovat, přejmenovat a odstranit.  

![Snímek obrazovky zobrazuje kontextovou nabídku s možnostmi správy, přejmenování a odstranění.](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Přejmenování osoby

1. Pokud chcete uživatele v modelu osoby přejmenovat, vyberte tlačítko nabídky seznam a v nabídce seznam zvolte **Přejmenovat** .
1. Vyberte aktuální jméno osoby a zadejte nový název.
1. Zaškrtněte tlačítko zaškrtnutí a osoba se přejmenuje.

### <a name="delete-a-person"></a>Odstranit osobu

1. Pokud chcete osobu odstranit z modelu osoby, vyberte tlačítko nabídky seznam a v nabídce seznam zvolte **Odstranit** .
1. V automaticky otevíraném okně se dozvíte, že tato akce odstraní osobu a že tuto akci nelze vrátit zpět.
1. Vyberte možnost **Odstranit** znovu a tato akce odebere uživatele z modelu osoby.

### <a name="manage-a-person"></a>Správa osoby

Pokud vyberete možnost **Spravovat**, zobrazí se v okně **Podrobnosti o uživateli** všechny plošky, ze kterých je tento IT model vyškolený. Tyto plošky pocházejí z výskytů této osoby ve videích, které používají tento model osoby, nebo z obrázků, které jste ručně nahráli.

> [!TIP]
> Kliknutím na jméno osoby nebo kliknutím na **Spravovat**, jak vidíte výše, se dostanete do okna **podrobností o osobě** .

#### <a name="add-a-face"></a>Přidat obličej

K osobě můžete přidat další plošky tak, že vyberete **Přidat obrázky**.

#### <a name="delete-a-face"></a>Odstranit obličej

Vyberte bitovou kopii, kterou chcete odstranit, a klikněte na **Odstranit**.

#### <a name="rename-and-delete-the-person"></a>Přejmenovat a odstranit osobu 

K přejmenování osoby můžete použít podokno Správa a odstranit osobu z modelu osoby.

## <a name="use-a-person-model-to-index-a-video"></a>Použití modelu osoby k indexování videa

K indexování nového videa můžete použít pracovní model přiřazením modelu osoby během nahrávání videa.

Pokud chcete použít svůj pracovní model na novém videu, proveďte následující kroky:

1. V horní části stránky vyberte tlačítko **nahrát** .

    ![Snímek obrazovky zobrazující tlačítko pro nahrání](./media/customize-face-model/upload.png)

1. Přetáhněte videosoubor do kruhu nebo ho vyhledejte.
1. Vyberte šipku **Upřesnit možnosti** .

    ![Snímek obrazovky se zobrazí dialogové okno nahrát mediální soubor.](./media/customize-face-model/upload2.png)

1. Vyberte rozevírací seznam a vyberte model osoby, který jste vytvořili.

    ![Snímek obrazovky se zobrazí dialogové okno nahrát mediální soubor s výchozím nastavením pro model osob v pokročilých možnostech.](./media/customize-face-model/upload3.png)

1. V dolní části stránky vyberte možnost **nahrávání** a nové video bude indexováno pomocí modelu vaší osoby.

Pokud během nahrávání nezadáte model osoby, Video Indexer bude video indexovat pomocí výchozího modelu osoby ve vašem účtu.

## <a name="use-a-person-model-to-reindex-a-video"></a>Použití modelu osoby k přeindexaci videa

Pokud chcete k reindexaci videa v kolekci použít pracovní model, přejděte na video o účtu na domovské stránce Video Indexer a najeďte myší na název videa, které chcete znovu indexovat.

Zobrazí se možnosti pro úpravu, odstranění a přeindexování videa.

1. Vyberte možnost pro Reindexování videa.

    ![Snímek obrazovky zobrazuje videa k účtu a možnost Přeindexovat video.](./media/customize-face-model/reindex.png)

    Nyní můžete vybrat model osoby, pomocí kterého se má video znovu indexovat.
1. Vyberte rozevírací seznam a vyberte model osoby, který chcete použít.

    ![Snímek obrazovky zobrazuje možnosti videa, včetně výběru modelu osob.](./media/customize-face-model/reindex2.png)

1. Vyberte tlačítko **Přeindexovat** a vaše video se přeindexuje pomocí modelu vaší osoby.

Všechny nové úpravy, které jste provedli u plošek zjištěných a rozpoznaných ve videu, které jste právě reindexováni, budou uloženy v modelu osoby, který jste použili k přeindexování videa.

## <a name="managing-people-in-your-videos"></a>Správa lidí ve vašich videích

Můžete spravovat plošky, které jsou zjištěny, a lidi, kteří jsou rozpoznáni v videích, které můžete indexovat úpravou a odstraněním ploch.

Odstranění obličeje odstraní konkrétní obličej z přehledů videa.

Úpravy obličeje přejmenují plošku, která je ve videu zjištěná a možná rozpoznaná. Když ve videu upravíte plošku, tento název se uloží jako položka osoby v modelu osoby, která se přiřadila videu během nahrávání a indexování.

Pokud k videu během nahrávání nepřiřadíte model osoby, vaše úprava se uloží do modelu výchozí osoby vašeho účtu.

### <a name="edit-a-face"></a>Úprava plochy

> [!NOTE]
> Pokud má model osoby dva nebo více různých lidí se stejným názvem, nebudete moci označit tento název v videích, které používají tento model osoby. Můžete provádět změny jenom pro lidi, kteří tento název sdílí, na kartě lidé stránky přizpůsobení modelu obsahu v Video Indexer. Z tohoto důvodu doporučujeme, abyste každé osobě v modelu osoby přiřadíte jedinečné názvy.

1. Přejděte na web Video Indexer a přihlaste se.
1. Vyhledejte video, které chcete ve svém účtu zobrazit a upravit.
1. Pokud chcete ve videu upravit plošku, navštivte kartu Přehledy a vyberte ikonu tužky v pravém horním rohu okna.

    ![Snímek obrazovky znázorňující video s neznámou ploškou k výběru.](./media/customize-face-model/edit-face.png)

1. Vyberte libovolné zjištěné plošky a změňte jejich názvy z "Neznámý #X" (nebo název, který byl dříve přiřazený k obličeji).
1. Po zadání nového názvu vyberte ikonu zaškrtnutí vedle nového názvu. Tato akce uloží nový název a rozpoznává a pojmenuje všechny výskyty této plošky v dalších současných videích a v budoucích videích, která nahráváte. Rozpoznávání obličeje v ostatních aktuálních videích může nějakou dobu trvat, protože se jedná o dávkový proces.

Pokud pojmenujte obličej s názvem existující osoby v modelu osoby, kterou video používá, zjištěné obrázky z tohoto videa této osoby se sloučí s tím, co už v modelu existuje. Pokud vytvoříte název obličeje s novým názvem, vytvoří se nová položka osoby v modelu osoby, kterou video používá.

![Snímek obrazovky zobrazuje plošku s názvem, který jste přidali.](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>Odstranit obličej

Pokud chcete ve videu odstranit zjištěnou plošku, v pravém horním rohu podokna vyberte ikonu tužky. Vyberte možnost **Odstranit** pod názvem obličeje. Tato akce odebere z videa zjištěnou plochu. Ploška osoby bude i nadále detekována v dalších videích, ve kterých se zobrazí, ale z videí můžete odstranit i po jejich indexování.

Osoba, pokud byla pojmenována, bude i nadále existovat v modelu osoby, který byl použit k indexování videa, ze kterého jste odstranili plochu, pokud výslovně neodstraníte osobu z modelu osoby.

![Odstranění obličeje ve videu](./media/customize-face-model/delete-face.png)

## <a name="next-steps"></a>Další kroky

[Přizpůsobení modelu osoby pomocí rozhraní API](customize-person-model-with-api.md)
