---
title: Přizpůsobení modelu osobě – Azure pomocí webových stránek Video Indexer
titlesuffix: Azure Media Services
description: Tento článek ukazuje, jak upravit model osoba s webem Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 03/19/2019
ms.author: anzaman
ms.openlocfilehash: 8dd535d97e40fe1dd4358d782db60940af1dd95d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "58892830"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Přizpůsobení modelu osoba s webem Video Indexer

Video Indexer podporuje rozpoznávání celebrit v případě video obsahu. Funkce rozpoznávání celebrit pokrývá přibližně jednoho milionu tváří, které jsou založené na zdroji dat běžně požadovaných například IMDB Wikipedia a horní vlivné osoby LinkedIn. Podrobnější přehled najdete v části [přizpůsobení modelu osoby v Video Indexer](customize-person-model-overview.md).

Video Indexer webu můžete použít k úpravě tváří, které byly zjištěny ve videu, jak je popsáno v tomto tématu. Můžete také použít rozhraní API, jak je popsáno v [přizpůsobit osoba model s použitím rozhraní API](customize-person-model-with-api.md).

## <a name="central-management-of-person-models-in-your-account"></a>Centrální správa modelů osoba ve vašem účtu

1. Chcete-li zobrazit, upravit a odstraňovat modely osoba ve vašem účtu, přejděte na web Video Indexer a přihlaste se.
2. Klikněte na tlačítko přizpůsobení modelu obsahu v pravém horním rohu stránky.

    ![Přizpůsobení modelu obsahu](./media/customize-face-model/content-model-customization.png)
3. Vyberte kartu uživatelé.

    Zobrazí se výchozí osoba modelu ve vašem účtu. Model výchozí osoby obsahuje všechny tváří můžete upravovat nebo změnili v poznatky o vašich videí, pro které jste nezadali vlastního modelu osoba během indexování. 

    Pokud jste vytvořili další modely osoby, budou uvedené na této stránce stejně. 

    ![Přizpůsobení modelu obsahu](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>Vytvořit nový model osoby

1. Klikněte na tlačítko **+ přidat model** tlačítko.

    ![Přidat nové uživatele](./media/customize-face-model/add-new-person.png)
2. Zadejte název modelu a klikněte na tlačítko se symbolem zaškrtnutí vedle názvu.

    ![Přidat nové uživatele](./media/customize-face-model/add-new-person2.png)

    Vytvořili jste nový model osoby. Nyní přidáte tváří na nový model osoby.
3. Klikněte na tlačítko nabídky seznam a zvolte **+ přidat osoba**.

    ![Přidat nové uživatele](./media/customize-face-model/add-new-person3.png)
    
## <a name="add-a-new-person-to-a-person-model"></a>Přidat nové osobě do modelu osoby

> [!NOTE]
> Video Indexer umožňuje přidat více uživatelů se stejným názvem v modelu osoby. Doporučujeme však, abyste udělili unque názvy pro každou osobu v modelu použitelnosti a srozumitelnější.

1. Pokud chcete přidat nové tváře osoby model, klikněte na tlačítko nabídky seznamu vedle osoba model, který chcete přidat typ písma k.
1. Klikněte na tlačítko **+ přidat osoba** z nabídky.

    ![Přidat nové tváře osoby](./media/customize-face-model/add-new-face.png)
 
    Automaticky otevírané okno vás vyzve k vyplnění její podrobnosti. Zadejte jméno osoby a klikněte na tlačítko se symbolem zaškrtnutí.

    ![Přidat nové tváře osoby](./media/customize-face-model/add-new-face2.png)
 
Můžete pak vybrat z Průzkumníka souborů nebo přetáhněte a umístěte obrázků pro rozpoznávání tváře z setkávají. Video Indexer bude trvat image všechny standardní typy souborů (např: JPG, PNG a další).

Video Indexer měli být schopni detekovat výskyty této osoby v budoucích videa indexu je a aktuální videa, která jste měli již indexována, pomocí modelu osoby, ke které jste přidali do této nové tváře. Rozpoznávání osoby ve vaší aktuální videa může trvat nějakou dobu, než se projeví, protože tento dávkový proces.


## <a name="rename-a-person-model"></a>Přejmenovat modelu osoby

Ve vašem účtu, včetně modelu výchozí osoby můžete přejmenovat všechny modely osoby. I v případě, že přejmenujete výchozí model osoby, bude i nadále sloužit jako výchozí model osoba ve vašem účtu.

1. Klikněte na tlačítko nabídky seznamu vedle modelu osoba, kterou chcete přejmenovat.
2. Klikněte na tlačítko **přejmenovat** z nabídky.

    ![Přejmenovat osoby](./media/customize-face-model/rename-person.png)
3. Klikněte na aktuální název modelu a zadejte nový název.

    ![Přejmenovat osoby](./media/customize-face-model/rename-person2.png)
4. Klikněte na tlačítko se symbolem zaškrtnutí pro váš model, který má být přejmenován.

## <a name="delete-a-person-model"></a>Odstranit model osoby

Můžete odstranit všechny osoby modelu, který jste vytvořili ve vašem účtu. Však nelze odstranit výchozí model osoby.

1. Klikněte na tlačítko **odstranit** z nabídky.

    ![Odstranit osoby](./media/customize-face-model/delete-person.png)
    
    Automaticky otevírané okno se zobrazí a oznámit, že tato akce odstraní osoba model a všechny osoby a soubory, které obsahuje. Tato akce je nevratná 

    ![Odstranit osoby](./media/customize-face-model/delete-person2.png)
1. Pokud jste si jistí, klikněte na odstranit znovu.

> [!NOTE]
> Existující videa, které nebyly indexovány pomocí tohoto modelu (nyní odstraněným) osoba nebude podporovat možnost aktualizovat názvy tváří na videu. Budete moci upravit názvy tváří v těchto videích až poté, co je pomocí jiného modelu osoba přeindexování. Pokud jste přeindexování bez zadání modelu osoby, použije se výchozí model. 

## <a name="manage-existing-people-in-a-person-model"></a>Spravovat existující lidí v modelu osoby

Podívat se na obsah některého z vašich modelů, osoby, klikněte na šipku vedle názvu modelu osoby.
Rozevírací seznam se dozvíte, všichni lidé v takovém modelu konkrétní osoby. Pokud kliknete na tlačítko nabídky seznamu vedle každého osob, se zobrazí spravovat, přejmenování a odstranění možnosti.  

![Přidat nové tváře osoby](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Přejmenovat osoby

1. Přejmenování osoby v modelu osoby, klikněte na tlačítko nabídky seznam a zvolte **přejmenovat** nabídce seznamu.
1. Klikněte na název aktuální osoby a zadejte nový název.
1. Klikněte na tlačítko se symbolem zaškrtnutí a osoby se přejmenuje.

### <a name="delete-a-person"></a>Odstranit osoby

1. Pokud chcete odstranit osoby z vašeho modelu osoby, klikněte na tlačítko nabídky seznam a zvolte **odstranit** nabídce seznamu.
1. Automaticky otevírané okno zjistíte, že tato akce odstraní osoby a že tuto akci nejde vrátit zpět.
1. Klikněte na tlačítko Odstranit a tato akce odebere uživatele z modelu osoby.

### <a name="manage-a-person"></a>Správa osoby

Pokud kliknete na **spravovat**, se zobrazí všechny plochy, které probíhajícího cvičení modelu tuto osobu z. Tyto tváří pocházejí z výskytů této osoby ve videích, která používají tento model osoby nebo z bitové kopie, které jste odeslali ručně. 

Můžete přidat další tváří osobě, kliknutím na Přidat obrázky.

Podokno manage osoby přejmenovat a odstranit z modelu osoba osobu.

## <a name="use-a-person-model-to-index-a-video"></a>Použití modelu osoby k indexování videa

Osoba model můžete použít k indexování nové video přiřazením modelu osoba během nahrávání videa.

Použití modelu osoba na nové video, postupujte takto:

1. Klikněte na tlačítko **nahrát** tlačítko v horní části stránky.

    ![Odeslat](./media/customize-face-model/upload.png)
1. Umístění souboru videa v kruhu nebo procházet pro soubor.
1. Klikněte na tlačítko se šipkou pokročilé možnosti.

    ![Odeslat](./media/customize-face-model/upload2.png)
1. Klikněte na rozevírací seznam a vyberte model osoba, kterou jste vytvořili.

    ![Odeslat](./media/customize-face-model/upload3.png)
1. Klikněte na možnost nahrávání v dolní části stránky a nové video bude indexovat pomocí vašeho modelu osoby.

Pokud nezadáte modelu osoba během nahrávání, budou indexovat Video Indexer videa pomocí výchozí osoba modelu ve vašem účtu.

## <a name="use-a-person-model-to-reindex-a-video"></a>Použití modelu osoba přeindexování videa 

Použít model osoba přeindexování videa v kolekci, přejděte na účet videí na domovské stránce Video Indexer a podržte ukazatel myši na název, který chcete přeindexování videa.

Zobrazí možnosti, jak upravit, odstranit a přeindexování vaše video. 

1. Klikněte na možnost přeindexování vaše video.

    ![Reindex](./media/customize-face-model/reindex.png)

    Teď můžete vybrat model osoba přeindexování video.
1. Klikněte na rozevírací seznam a vyberte model osoba, kterou chcete použít. 

    ![Reindex](./media/customize-face-model/reindex2.png)

1. Klikněte na tlačítko **přeindexování** tlačítko a vaše video se bude přeindexovány pomocí vašeho modelu osoby.

Veškeré nové úpravy provedené tváří zjištěna a rozpoznat ve videu, který jste právě přeindexovány se uloží v modelu osoba, která jste použili k přeindexování videa.

## <a name="managing-people-in-your-videos"></a>Správa uživatelů ve vašich videích

Můžete spravovat tváří, které jsou zjištěny a osoby, které nebyly rozpoznány ve videích indexování úpravou a odstraněním tváří.

Odstraňuje se řez, zruší poznatky o videu konkrétní tváře.

Úpravy rozpoznávání tváře, přejmenuje řez, který je zjištěn a pravděpodobně rozpoznat ve videu. Při úpravách rozpoznávání tváře ve videu, tento název je uložen jako osoba položky v modelu osoba, která byla přiřazena videa během nahrávání a indexování.

Pokud nepřiřadíte modelu osoby k videu při nahrávání, úpravy se uloží do vašeho účtu výchozí osoba model.

### <a name="edit-a-face"></a>Upravit rozpoznávání tváře


> [!NOTE]
> Pokud model osoby obsahuje dva nebo více různých lidí se stejným názvem, nebude možné označit tento název ve videích, která používají tento model osoby. Pouze bude možné provést změny lidem, které sdílejí tento název v kartě osoby stránky přizpůsobení modelu obsahu v Video Indexer. Z tohoto důvodu se doporučuje, abyste udělili jedinečné názvy pro každou osobu v modelu osoby.

1. Přejděte na web Video Indexer a přihlaste se.
1. Vyhledejte video, které chcete zobrazit a upravit ve vašem účtu.
1. Rozpoznávání tváře ve videu upravit, přejděte na kartu Insights a klikněte na ikonu tužky v pravém horním rohu okna.

    ![Upravit rozpoznávání tváře ve videu](./media/customize-face-model/edit-face.png)
1. Klepněte na žádný zjištěný tváří a změňte jejich názvy z "Neznámý #X" (nebo název, který byl dříve přiřazen typ písma). 
1. Po zadání nový název, klikněte na ikonu zaškrtnutí vedle názvu nové. To se uloží nový název a rozpozná a názvy všech výskytů dané tento směřovat vaše další aktuální videa a v budoucnu videa, které můžete odeslat. Rozpoznávání tváře ve vaší aktuální videa může trvat nějakou dobu, než se projeví, protože tento dávkový proces.

Pokud zadáte název plochy s názvem existujícího osoby v modelu osoba, která používá videa, tím dojde ke sloučení zjištěných pro rozpoznávání tváře imagí z toto video, uživatel s co v modelu již existuje. Pokud zadáte název plochy s úplně novým názvem, tím se vytvoří nový záznam osoba v modelu osoba, která používá videa. 

    ![Edit a face in your video](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>Odstranit plochy

Pokud chcete odstranit zjištěný rozpoznávání tváře ve videu, přejděte do podokna Insights a klikněte na ikonu tužky v pravém horním rohu podokna. Klikněte na možnost odstranit pod názvem plochy. Tato akce odebere zjištěným tváří z videa. Rozpoznávání tváře osoby bude stále rozpoznán v jiných videí, ve kterých se vyskytuje, ale plochu můžete odstranit z těchto videí a po byla indexované. Osoba, pokud ji má název, také nadále existovat v modelu osoba, která byla použita k indexování video, ze kterého jste odstranili typ písma Pokud konkrétně odstranit z modelu osoba osoby.

![Odstranit rozpoznávání tváře ve videu](./media/customize-face-model/delete-face.png)


## <a name="next-steps"></a>Další postup

[Přizpůsobení modelu osoba s použitím rozhraní API](customize-person-model-with-api.md)
