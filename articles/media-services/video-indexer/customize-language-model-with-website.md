---
title: Přizpůsobení jazykového modelu pomocí Video Indexer webu
titleSuffix: Azure Media Services
description: Naučte se, jak přizpůsobit jazykový model pomocí Video Indexer webu.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/10/2020
ms.author: kumud
ms.openlocfilehash: dd8b36340deb6c785989107461dd420e7fc0d985
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2020
ms.locfileid: "97722568"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Přizpůsobení jazykového modelu pomocí Video Indexer webu

Video Indexer vám umožní vytvářet vlastní jazykové modely pro přizpůsobení rozpoznávání řeči odesláním přizpůsobeného textu, konkrétně textu z domény, kde chcete, aby se modul přizpůsobil. Jakmile svůj model vytvoříte, budou se nová slova uvedená v textu přizpůsobení rozpoznat.

Podrobný přehled a osvědčené postupy pro vlastní jazykové modely najdete v tématu [přizpůsobení jazykového modelu pomocí video indexer](customize-language-model-overview.md).

Web Video Indexer můžete použít k vytvoření a úpravě vlastních jazykových modelů v účtu, jak je popsáno v tomto tématu. Můžete také použít rozhraní API, jak je popsáno v tématu [přizpůsobení jazykového modelu pomocí rozhraní API](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Vytvoření jazykového modelu

1. Přejít na web [video indexer](https://www.videoindexer.ai/) a přihlásit se.
1. Pokud chcete ve svém účtu přizpůsobit model, vyberte tlačítko **Přizpůsobení modelu obsahu** na levé straně stránky.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-language-model/model-customization.png" alt-text="Přizpůsobení modelu obsahu v Video Indexer":::
1. Vyberte kartu **jazyk** .

    Zobrazí se seznam podporovaných jazyků.
1. V části požadovaný jazyk vyberte **přidat model**.
1. Zadejte název pro jazykový model a stiskněte ENTER.

    Tento krok vytvoří model a nabídne možnost odeslat textové soubory do modelu.
1. Chcete-li přidat textový soubor, vyberte možnost **Přidat soubor**. Otevře se Průzkumník souborů.
1. Přejděte na a vyberte textový soubor. Do modelu jazyka můžete přidat více textových souborů.

    Můžete také přidat textový soubor výběrem tlačítka **...** na pravé straně jazykového modelu a výběrem možnosti **Přidat soubor**.
1. Jakmile budete hotovi s nahráváním textových souborů, vyberte zelenou možnost **vlaku** .

Proces školení může trvat několik minut. Po dokončení školení se zobrazí **školení** vedle modelu. Můžete zobrazit náhled, stáhnout a odstranit soubor z modelu.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/customize-language-model/customize-language-model.png" alt-text="Trénování modelu":::

### <a name="using-a-language-model-on-a-new-video"></a>Použití jazykového modelu na novém videu

Pokud chcete použít svůj jazykový model na novém videu, proveďte jednu z následujících akcí:

* V horní části stránky vyberte tlačítko **nahrát** .

    ![Tlačítko pro odeslání Video Indexer](./media/customize-language-model/upload.png)
* Přetáhněte svůj zvukový soubor nebo videosoubor nebo ho vyhledejte.

Máte možnost vybrat **zdrojový jazyk videa**. Vyberte rozevírací nabídku a vyberte jazykový model, který jste vytvořili ze seznamu. Měl by to být jazyk vašeho jazykového modelu a název, který jste mu přiřadili v závorkách. Například:

![Zvolit zdrojový jazyk videa – přeindexujte video pomocí Video Indexer](./media/customize-language-model/reindex.png)

V dolní části stránky vyberte možnost **nahrávání** a nové video bude indexováno pomocí vašeho jazykového modelu.

### <a name="using-a-language-model-to-reindex"></a>Reindexace pomocí jazykového modelu

Pokud chcete k reindexaci videa v kolekci použít jazykový model, postupujte takto:

1. Přihlaste se na domovskou stránku [video indexer](https://www.videoindexer.ai/) .
1. Klikněte na tlačítko zapnout **...** na videu a vyberte **znovu index**.
1. Máte možnost vybrat **zdrojový jazyk videa** , ve kterém se má video znovu indexovat. Vyberte rozevírací nabídku a vyberte jazykový model, který jste vytvořili ze seznamu. Měl by to být jazyk vašeho jazykového modelu a název, který jste mu přiřadili v závorkách.
1. Vyberte tlačítko **znovu index** a vaše video se přeindexuje pomocí vašeho jazykového modelu.

## <a name="edit-a-language-model"></a>Úprava jazykového modelu

Můžete upravit jazykový model změnou jeho názvu, přidáním souborů do něj a odstraněním souborů z něj.

Pokud přidáváte nebo odstraňujete soubory z jazykového modelu, budete muset znovu vytvořit model a vybrat zelenou možnost **vlaku** .

### <a name="rename-the-language-model"></a>Přejmenování jazykového modelu

Název jazykového modelu můžete změnit tak, že vyberete tlačítko se třemi tečkami (**...**) na pravé straně jazykového modelu a vyberete **Přejmenovat**.

Zadejte nový název a stiskněte klávesu ENTER.

### <a name="add-files"></a>Přidat soubory

Chcete-li přidat textový soubor, vyberte možnost **Přidat soubor**. Otevře se Průzkumník souborů.

Přejděte na a vyberte textový soubor. Do modelu jazyka můžete přidat více textových souborů.

Textový soubor můžete také přidat tak, že vyberete tlačítko se třemi tečkami (**...**) na pravé straně jazykového modelu a vyberete **Přidat soubor**.

### <a name="delete-files"></a>Odstranění souborů

Pokud chcete odstranit soubor z jazykového modelu, vyberte na pravé straně textového souboru tlačítko se třemi tečkami (**...**) a vyberte **Odstranit**. Zobrazí se nové okno s oznámením, že odstranění nelze vrátit zpět. V novém okně vyberte možnost **Odstranit** .

Tato akce odebere soubor úplně z jazykového modelu.

## <a name="delete-a-language-model"></a>Odstranění jazykového modelu

Pokud chcete z účtu odstranit jazykový model, vyberte na pravé straně jazykového modelu tlačítko se třemi tečkami (**...**) a vyberte **Odstranit**.

Zobrazí se nové okno s oznámením, že odstranění nelze vrátit zpět. V novém okně vyberte možnost **Odstranit** .

Tato akce zcela odebere jazykový model z vašeho účtu. Všechna videa, která používala odstraněný jazykový model, budou mít stejný index, dokud video nebudete indexovat. Při opětovném indexování videa můžete videu přiřadit nový jazykový model. V opačném případě Video Indexer použije výchozí model k reindexaci videa.

## <a name="customize-language-models-by-correcting-transcripts"></a>Přizpůsobení jazykových modelů opravou přepisů

Video Indexer podporuje automatické přizpůsobení jazykových modelů na základě skutečného počtu oprav, které uživatelé provedou v přepisu jejich videí.

1. Pokud chcete provést opravy přepisu, otevřete video, které chcete upravit, z videí k účtu. Vyberte kartu **Časová osa** .

    ![Karta pro přizpůsobení časové osy jazykového modelu – Video Indexer](./media/customize-language-model/timeline.png)

1. Vyberte ikonu tužky a upravte přepis svého přepisu.

    ![Přizpůsobení jazykové model upravit přepis – Video Indexer](./media/customize-language-model/edits.png)

    Video Indexer zachycuje všechny řádky, které jsou opraveny v přepisu videa, a automaticky je přidá do textového souboru s názvem "z úprav přepisu". Tyto úpravy slouží k přeučení specifického jazykového modelu, který se použil k indexování tohoto videa. 
    
    K dispozici jsou také úpravy, které byly provedeny v časové ose [widgetu](video-indexer-embed-widgets.md) .
    
    Pokud jste při indexování tohoto videa neurčili jazykový model, všechny úpravy tohoto videa budou uloženy ve výchozím jazykovém modelu s názvem "přizpůsobení účtu" v rámci zjištěného jazyka videa.
    
    V případě, že se na stejném řádku provedlo několik úprav, použije se k aktualizaci jazykového modelu jenom poslední verze opraveného řádku.  
    
    > [!NOTE]
    > Pro vlastní nastavení se používají jenom textové opravy. Nezahrnují se opravy, které neobsahují skutečná slova (například interpunkční znaménka nebo mezery).
    
1. Na kartě jazyk na stránce přizpůsobení modelu obsahu se zobrazí opravy přepisu.

   Pokud se chcete podívat na soubor úpravy přepisu pro každý z jazykových modelů, vyberte ho a otevřete ho.

    ![Od úprav přepisu – Video Indexer](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Další kroky

[Přizpůsobení jazykového modelu pomocí rozhraní API](customize-language-model-with-api.md)
