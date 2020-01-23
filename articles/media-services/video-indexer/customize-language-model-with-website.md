---
title: Přizpůsobení jazykových modelů pomocí Video Indexer webu – Azure
titleSuffix: Azure Media Services
description: Tento článek popisuje, jak přizpůsobit jazykový model pomocí Video Indexer webu.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 329da39914ef957d3a5376ba59e0c7103ad6a5dd
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513911"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Přizpůsobení jazykového modelu pomocí Video Indexer webu

Video Indexer vám umožní vytvářet vlastní jazykové modely pro přizpůsobení rozpoznávání řeči odesláním přizpůsobeného textu, konkrétně textu z domény, kde chcete, aby se modul přizpůsobil. Jakmile svůj model vytvoříte, budou se nová slova uvedená v textu přizpůsobení rozpoznat. 

Podrobný přehled a osvědčené postupy pro vlastní jazykové modely najdete v tématu [přizpůsobení jazykového modelu pomocí video indexer](customize-language-model-overview.md).

Web Video Indexer můžete použít k vytvoření a úpravě vlastních jazykových modelů v účtu, jak je popsáno v tomto tématu. Můžete také použít rozhraní API, jak je popsáno v tématu [přizpůsobení jazykového modelu pomocí rozhraní API](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Vytvoření jazykového modelu

1. Přejděte na web [Video Indexer](https://www.videoindexer.ai/) a přihlaste se.
2. Pokud chcete ve svém účtu přizpůsobit model, klikněte na tlačítko **Přizpůsobení modelu obsahu** v pravém horním rohu stránky.

   ![Přizpůsobení modelu obsahu](./media/content-model-customization/content-model-customization.png)

3. Vyberte kartu **jazyk** .

    Zobrazí se seznam podporovaných jazyků. 

    ![Přizpůsobení jazykového modelu](./media/customize-language-model/customize-language-model.png)

4. V části požadovaný jazyk klikněte na **přidat model**.
5. Zadejte název pro jazykový model a stiskněte ENTER.

    Tím se vytvoří model a nabídne možnost nahrání textových souborů do modelu.
6. Chcete-li přidat textový soubor, klikněte na tlačítko **Přidat soubor**. Tím otevřete Průzkumníka souborů.

7. Přejděte na a vyberte textový soubor. Do modelu jazyka můžete přidat více textových souborů.

    Můžete také přidat textový soubor kliknutím na tlačítko **...** na pravé straně jazykového modelu a výběrem možnosti **Přidat soubor**.
8. Po dokončení nahrávání textových souborů klikněte na zelenou možnost **vlaku** .

    ![Jazykový model výuky](./media/customize-language-model/train-model.png)

Proces školení může trvat několik minut. Po dokončení školení se zobrazí **školení** vedle modelu. Můžete zobrazit náhled, stáhnout a odstranit soubor z modelu.

![Vyškolený jazykový model](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Použití jazykového modelu na novém videu

Chcete-li použít svůj jazykový model na novém videu, proveďte jednu z následujících akcí:

* Klikněte na tlačítko **nahrát** v horní části stránky. 

    ![Nahrávání](./media/customize-language-model/upload.png)
* Zařaďte svůj zvukový nebo videosoubor do kruhu nebo ho vyhledejte.

    ![Nahrávání](./media/customize-language-model/upload2.png)

Tím získáte možnost vybrat **zdrojový jazyk videa**. Klikněte na rozevírací seznam a vyberte jazykový model, který jste vytvořili ze seznamu. Měl by to být jazyk vašeho jazykového modelu a název, který jste mu přiřadili v závorkách.

V dolní části stránky klikněte na možnost **nahrát** a nové video bude indexováno pomocí vašeho jazykového modelu.

### <a name="using-a-language-model-to-reindex"></a>Reindexace pomocí jazykového modelu

Pokud chcete pomocí vašeho jazykového modelu znovu indexovat video v kolekci, přejděte na video o **účtu** na domovské stránce [video indexer](https://www.videoindexer.ai/) a umístěte ukazatel myši na název videa, které chcete znovu indexovat.

Uvidíte možnosti, jak upravit vaše video, odstranit video a znovu indexovat video. Pokud chcete video znovu indexovat, klikněte na možnost.

![Přeindexování](./media/customize-language-model/reindex1.png)

Díky tomu máte možnost vybrat **zdrojový jazyk videa** , pomocí kterého se má video znovu indexovat. Klikněte na rozevírací seznam a vyberte jazykový model, který jste vytvořili ze seznamu. Měl by to být jazyk vašeho jazykového modelu a název, který jste mu přiřadili v závorkách.

![Přeindexování](./media/customize-language-model/reindex.png)

Klikněte na tlačítko **znovu index** a vaše video se přeindexuje pomocí jazykového modelu.

## <a name="edit-a-language-model"></a>Úprava jazykového modelu

Můžete upravit jazykový model změnou jeho názvu, přidáním souborů do něj a odstraněním souborů z něj.

Pokud přidáváte nebo odstraňujete soubory z jazykového modelu, budete si muset model znovu vytvořit kliknutím na zelenou možnost **vlaku** .

### <a name="rename-the-language-model"></a>Přejmenování jazykového modelu

Název jazykového modelu můžete změnit kliknutím na **...** na pravé straně jazykového modelu a výběrem možnosti **Přejmenovat**. 

Zadejte nový název a stiskněte klávesu ENTER.

### <a name="add-files"></a>Přidat soubory

Chcete-li přidat textový soubor, klikněte na tlačítko **Přidat soubor**. Tím otevřete Průzkumníka souborů.

Přejděte na a vyberte textový soubor. Do modelu jazyka můžete přidat více textových souborů.

Můžete také přidat textový soubor kliknutím na tlačítko **...** na pravé straně jazykového modelu a výběrem možnosti **Přidat soubor**.

### <a name="delete-files"></a>Odstranění souborů

Pokud chcete odstranit soubor z jazykového modelu, klikněte na pravé straně textového souboru na tlačítko **...** a vyberte **Odstranit**. Tím se zobrazí nové okno s oznámením, že odstranění nelze vrátit zpět. V novém okně klikněte na možnost **Odstranit** .

Tato akce odebere soubor úplně z jazykového modelu.

## <a name="delete-a-language-model"></a>Odstranění jazykového modelu

Pokud chcete z účtu odstranit jazykový model, klikněte na pravé straně jazykového modelu na tlačítko **...** a vyberte **Odstranit**.

Tím se zobrazí nové okno s oznámením, že odstranění nelze vrátit zpět. V novém okně klikněte na možnost **Odstranit** .

Tato akce zcela odebere jazykový model z vašeho účtu. Všechna videa, která používala odstraněný jazykový model, budou mít stejný index, dokud video znovu neindexujte. Pokud video znovu indexovat, můžete video přiřadit nový jazykový model. V opačném případě Video Indexer použije výchozí model k opakovanému indexování videa. 

## <a name="customize-language-models-by-correcting-transcripts"></a>Přizpůsobení jazykových modelů opravou přepisů

Video Indexer podporuje automatické přizpůsobení jazykových modelů na základě skutečného počtu oprav, které uživatelé provedou v přepisu jejich videí.

1. Pokud chcete provést opravy přepisu, otevřete video, které chcete upravit, z videí k účtu. Vyberte kartu **Časová osa** .

    ![Přizpůsobení jazykového modelu](./media/customize-language-model/timeline.png)
1. Kliknutím na ikonu tužky upravte přepis svého přepisu. 

    ![Přizpůsobení jazykového modelu](./media/customize-language-model/edits.png)

    Video Indexer zachycuje všechny řádky, které jsou opraveny v přepisu videa, a automaticky je přidá do textového souboru s názvem "z úprav přepisu". Tyto úpravy slouží k opětovnému vytvoření výukového modelu konkrétního jazyka, který se použil k indexování tohoto videa. 
    
    Pokud jste při indexování tohoto videa neurčili jazykový model, všechny úpravy tohoto videa budou uloženy ve výchozím jazykovém modelu nazývaném přizpůsobení účtu v rámci zjištěného jazyka videa. 
    
    V případě, že se na stejném řádku provedlo několik úprav, použije se k aktualizaci jazykového modelu jenom poslední verze opraveného řádku.  
    
    > [!NOTE]
    > Pro vlastní nastavení se používají jenom textové opravy. To znamená, že nejsou zahrnuté opravy, které nezahrnují skutečná slova (například interpunkční znaménka nebo mezery). 
    
1. Na kartě jazyk na stránce přizpůsobení modelu obsahu se zobrazí opravy přepisu.

    ![Přizpůsobení jazykového modelu](./media/customize-language-model/customize.png)

   Pokud se chcete podívat na soubor "od úprav přepisu" pro každý jazykový model, klikněte na něj a otevřete ho. 

    ![Od úprav přepisu](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Další kroky

[Přizpůsobení jazykového modelu pomocí rozhraní API](customize-language-model-with-api.md)
