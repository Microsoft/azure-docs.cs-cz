---
title: Přizpůsobení jazykového modelu pomocí webu Video Indexer
titleSuffix: Azure Media Services
description: Přečtěte si, jak přizpůsobit jazykový model pomocí webu Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 8917a3ac302d18337d79bffce69bad108667b4d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128078"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Přizpůsobení jazykového modelu pomocí webu Video Indexer

Video Indexer umožňuje vytvářet vlastní jazykové modely pro přizpůsobení rozpoznávání řeči nahráním textu adaptace, konkrétně textu z domény, jehož slovní zásobu chcete, aby se motor přizpůsobil. Jakmile táhejte model, budou rozpoznána nová slova uvedená v textu přizpůsobení.

Podrobný přehled a doporučené postupy pro vlastní jazykové modely naleznete v [tématu Přizpůsobení jazykového modelu pomocí nástroje Video Indexer](customize-language-model-overview.md).

Pomocí webu Video Indexer můžete ve svém účtu vytvářet a upravovat vlastní jazykové modely, jak je popsáno v tomto tématu. Rozhraní API můžete také použít, jak je popsáno v [části Přizpůsobit jazykový model pomocí rozhraní API](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Vytvoření jazykového modelu

1. Přejděte na web [Video Indexer](https://www.videoindexer.ai/) a přihlaste se.
2. Chcete-li přizpůsobit model v účtu, vyberte tlačítko **Přizpůsobení modelu obsahu** v pravém horním rohu stránky.

   ![Přizpůsobení modelu obsahu v videoindexeru](./media/content-model-customization/content-model-customization.png)

3. Vyberte kartu **Jazyk.**

    Zobrazí se seznam podporovaných jazyků.

    ![Seznam modelů jazyků v video indexeru](./media/customize-language-model/customize-language-model.png)

4. V požadovaném jazyce vyberte **Přidat model**.
5. Zadejte název jazykového modelu a stiskněte klávesu ENTER.

    Tento krok vytvoří model a dává možnost nahrát textové soubory do modelu.

6. Chcete-li přidat textový soubor, vyberte **Přidat soubor**. Otevře se průzkumník souborů.

7. Přejděte do textového souboru a vyberte ho. Do jazykového modelu můžete přidat více textových souborů.

    Textový soubor můžete přidat také tak, že vyberete tlačítko **...** na pravé straně jazykového modelu a vyberete **přidat soubor**.

8. Po dokončení nahrávání textových souborů vyberte zelenou možnost **Vlak.**

    ![Model jazyka Trénování v videoindexeru](./media/customize-language-model/train-model.png)

Tréninkový proces může trvat několik minut. Po dokončení školení se zobrazí **trénovaný** vedle modelu. Soubor z modelu můžete zobrazit náhled, stáhnout a odstranit.

![Trénovaný jazykový model v video indexeru](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Použití jazykového modelu v novém videu

Chcete-li použít jazykový model v novém videu, proveďte jednu z následujících akcí:

* V horní části stránky vyberte tlačítko **Nahrát.**

    ![Tlačítko Nahrát Video Indexer](./media/customize-language-model/upload.png)

* Přetáhněte zvukový soubor nebo video soubor do kruhu nebo vyhledejte soubor.

    ![Nahrát mediální soubor Video Indexer](./media/customize-language-model/upload2.png)

Máte možnost vybrat **zdrojový jazyk videa**. Vyberte rozevírací seznam a vyberte jazykový model, který jste vytvořili ze seznamu. Měl by vněm být vyslovuje se jazyk emaského modelu a název, který jste mu dali v závorce.

V dolní části stránky vyberte možnost **Nahrát** a nové video bude indexováno podle jazykového modelu.

### <a name="using-a-language-model-to-reindex"></a>Použití jazykového modelu k přeindexování

Pokud chcete použít jazykový model k přeindexování videa v kolekci, přejděte na domovskou stránku Video Indexer na **domovské** stránce [Video Indexer](https://www.videoindexer.ai/) a najeďte na název videa, které chcete přeindexovat.

Zobrazí se možnosti úprav videa, odstranění videa a přeindexování videa. Vyberte možnost přeindexování videa.

![Přeindexování pomocí videoindexeru](./media/customize-language-model/reindex1.png)

Budete mít možnost vybrat **zdrojový jazyk videa,** ve kterých chcete video přeindexovat. Vyberte rozevírací seznam a vyberte jazykový model, který jste vytvořili ze seznamu. Měl by vněm být vyslovuje se v jazyce vašeho jazykového modelu a název, který jste mu dali v závorce.

![Volba jazyka zdroje videa – Přeindexování videa pomocí videoindexeru](./media/customize-language-model/reindex.png)

Vyberte tlačítko **Přeindexovat** a vaše video bude přeindexováno pomocí jazykového modelu.

## <a name="edit-a-language-model"></a>Úprava jazykového modelu

Jazykový model můžete upravit změnou jeho názvu, přidáním souborů a odstraněním souborů z něj.

Pokud přidáte nebo odstraníte soubory z jazykového modelu, budete muset model znovu trénovat výběrem zelené **možnosti Vlak.**

### <a name="rename-the-language-model"></a>Přejmenování jazykového modelu

Název jazykového modelu můžete změnit tak, že na pravé straně jazykového modelu vyberete tlačítko Tři tečky (**...**) a **vyberete přejmenovat**.

Zadejte nový název a stiskněte klávesu ENTER.

### <a name="add-files"></a>Přidání souborů

Chcete-li přidat textový soubor, vyberte **Přidat soubor**. Otevře se průzkumník souborů.

Přejděte do textového souboru a vyberte ho. Do jazykového modelu můžete přidat více textových souborů.

Textový soubor můžete přidat také tak, že na pravé straně jazykového modelu vyberete tlačítko Tři tečky (**...**) a vyberete **přidat soubor**.

### <a name="delete-files"></a>Odstranění souborů

Chcete-li odstranit soubor z jazykového modelu, vyberte tlačítko tři tečky (**...**) na pravé straně textového souboru a vyberte **Odstranit**. Objeví se nové okno s tím, že odstranění nelze vrátit zpět. V novém okně vyberte možnost **Odstranit.**

Tato akce odebere soubor zcela z jazykového modelu.

## <a name="delete-a-language-model"></a>Odstranění jazykového modelu

Chcete-li odstranit jazykový model ze svého účtu, vyberte tlačítko tři tečky (**...**) na pravé straně jazykového modelu a vyberte **odstranit**.

Objeví se nové okno s tím, že odstranění nelze vrátit zpět. V novém okně vyberte možnost **Odstranit.**

Tato akce zcela odebere jazykový model z vašeho účtu. Jakékoli video, které používalo odstraněný jazykový model, bude mít stejný index, dokud video nepřeindexujete. Pokud video přeindexujete, můžete k videu přiřadit nový jazykový model. V opačném případě video Indexer použije svůj výchozí model k přeindexování videa.

## <a name="customize-language-models-by-correcting-transcripts"></a>Přizpůsobení jazykových modelů opravou přepisů

Video Indexer podporuje automatické přizpůsobení jazykových modelů na základě skutečných oprav, které uživatelé provází v přepisech svých videí.

1. Chcete-li provést opravy přepisu, otevřete video, které chcete upravit z videí účtu. Vyberte kartu **Časová osa.**

    ![Přizpůsobit kartu časové osy jazykového modelu – Video Indexer](./media/customize-language-model/timeline.png)

1. Vyberte ikonu tužky a upravte přepis přepisu.

    ![Přizpůsobení přepisu úprav jazykového modelu – Video Indexer](./media/customize-language-model/edits.png)

    Video Indexer zachytí všechny řádky, které jste opravili v přepisu videa, a automaticky je přidá do textového souboru s názvem "Z úprav přepisu". Tyto úpravy se používají k přeškolení konkrétního jazykového modelu, který byl použit k indexování tohoto videa.
    
    Pokud jste při indexování tohoto videa nezadali jazykový model, budou všechny úpravy tohoto videa uloženy ve výchozím jazykovém modelu nazvaném "Úpravy účtu" v detekovaném jazyce videa.
    
    V případě, že bylo provedeno více úprav na stejném řádku, bude pro aktualizaci jazykového modelu použita pouze poslední verze opraveného řádku.  
    
    > [!NOTE]
    > Pro vlastní nastavení se používají pouze textové opravy. Opravy, které nezahrnují skutečná slova (například interpunkční znaménka nebo mezery), nejsou zahrnuty.
    
1. Opravy přepisu se zobrazí na kartě Jazyk na stránce Přizpůsobení modelu obsahu.

    ![Přizpůsobení jazykového modelu – Video Indexer](./media/customize-language-model/customize.png)

   Chcete-li se podívat na soubor "Z úprav přepisů" pro každý z vašich jazykových modelů, vyberte jej, abyste jej otevřeli.

    ![Z úprav přepisu – Video Indexer](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Další kroky

[Přizpůsobení jazykového modelu pomocí rozhraní API](customize-language-model-with-api.md)
