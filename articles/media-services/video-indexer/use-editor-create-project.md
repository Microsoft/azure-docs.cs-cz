---
title: Použití editoru Video Indexer k vytváření projektů
titleSuffix: Azure Media Services
description: Toto téma ukazuje, jak pomocí editoru Video Indexer k vytvoření projektů.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: 9f16ab34dc9b37806f9c58b22a3f02afe839632e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73839172"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>Použití editoru Video Indexer k vytváření projektů

Video Indexer webové stránky, umožňuje používat vaše videa 'hluboké přehledy: najít správný mediální obsah, najít části, které vás zajímají, a použít výsledky k vytvoření zcela nového projektu. Po vytvoření může být projekt vykreslen a stažen z video indexeru a použit ve vlastních editačních aplikacích nebo podřízené pracovní postupy.

Některé scénáře, kde může být tato funkce užitečná, jsou: 

* Vytváření filmových vrcholů pro upoutávky.
* Použití starých klipů videa v novinky obsazení.
* Vytváření kratšího obsahu pro sociální média.

Tento článek ukazuje, jak vytvořit projekt od začátku a také jak vytvořit projekt z videa ve vašem účtu.

## <a name="create-new-project-and-manage-videos"></a>Vytvoření nového projektu a správa videí

1. Přejděte na web [Video Indexer](https://www.videoindexer.ai/) a přihlaste se.
1. Vyberte kartu **Projekty.** Pokud jste projekty vytvořili již dříve, uvidíte zde všechny ostatní projekty.
1. Klepněte na **tlačítko Vytvořit nový projekt**.  

    ![Nový projekt](./media/video-indexer-view-edit/new-project.png)
1. Pojmenujte projekt kliknutím na ikonu tužky. Nahraďte text s nápisem "Projekt bez názvu" názvem projektu a klikněte na šek.

    ![Nový projekt](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>Přidání videí do projektu

> [!NOTE]
> V současné době mohou projekty obsahovat pouze videa indexovaná ve stejném jazyce. Jakmile vyberete video v jednom jazyce, nemůžete přidat videa do účtu, která jsou v jiném jazyce.

1. Přidejte videa, se kterými chcete v tomto projektu pracovat, výběrem **možnosti Přidat videa**.

    Zobrazí se všechna videa ve vašem účtu a vyhledávací pole s nápisem "Hledat text, klíčová slova nebo vizuální obsah". Chcete-li vyhledat videa, která mají v přepisu a rozpoznávání OCR určenou osobu, štítek, značku, klíčové slovo nebo výskyt.
    
    Například na obrázku níže hledáme videa, která zmiňují "GitHub".
    
    ![GitHub](./media/video-indexer-view-edit/github.png)

    Výsledky můžete dále filtrovat výběrem **možnosti Filtrovat výsledky**. Můžete filtrovat a zobrazit videa, která mají určitou osobu, nebo určit, že chcete zobrazit pouze výsledky videa, které jsou v určitém jazyce nebo mají konkrétního vlastníka. <br/> Můžete také určit rozsah dotazu. Pokud například chcete v rozpoznávání OCR hledat "GitHub", vyberte **vizuální text**.

    ![Filtr](./media/video-indexer-view-edit/visual-text.png)

    Do dotazu můžete vrstvit více filtrů. / **-** Pomocí tlačítek můžete přidávat nebo odebírat **+** filtry. K odebrání všech filtrů použijte **vymazat filtry.**
1. Pokud chcete přidat videa, vyberte je a pak vyberte **Přidat**.
1. Nyní uvidíte všechna videa, která jste si vybrali. Jedná se o videa, ze kterých budete vybírat klipy pro váš projekt.

    Pořadí videí můžete změnit přetažením nebo výběrem tlačítka nabídky seznamu a výběrem **možnosti Přesunout dolů** nebo **Přesunout nahoru**. Z nabídky seznamu budete také moci odstranit video z tohoto projektu. 

    ![Uspořádání](./media/video-indexer-view-edit/rearrange.png)
    
    Máte možnost přidat další videa do tohoto projektu kdykoliv výběrem **Přidat videa**. Do projektu můžete také přidat více výskytů stejného videa. Můžete to udělat, pokud chcete zobrazit klip z jednoho videa a pak klip z jiného a pak další klip z prvního videa. 

### <a name="select-clips-to-use-in-your-project"></a>Výběr klipů, které chcete použít v projektu

Pokud kliknete na šipku dolů na pravé straně každého videa, otevřete přehledy ve videu na základě časových razítek (klipů videa). 

1. Vyberte **Zobrazit přehledy** a přizpůsobte si, které přehledy chcete zobrazit a které nechcete vidět. 

    ![Zobrazit přehledy](./media/video-indexer-view-edit/insights.png)
1. Chcete-li vytvořit dotazy pro konkrétní klipy, použijte vyhledávací pole s textem "Hledat v přepisu, vizuálním textu, lidech a popiscích".
1. Výběrem **možností filtrů**můžete přidat filtry, které chcete dále specifikovat podrobnosti o tom, jaké scény hledáte .

    ![Možnosti filtru](./media/video-indexer-view-edit/filter-options.png)

    Můžete například chtít zobrazit klipy, kde je na obrazovce zmíněn GitHub, zatímco donovan brown je na obrazovce. K tomu je třeba přidat filtr "zahrnout", který má jako typ přehledu "Lidé". Poté musíte do vyhledávacího pole pro filtr zadat "Donovan Brown".
    
    ![Zařadit členy](./media/video-indexer-view-edit/include.png)
    
    Pokud chcete klipy, kde je uveden GitHub, zatímco Donovan Brown _není_ na obrazovce, jednoduše byste změnili filtr "zahrnout" na filtr "vyloučit" pomocí rozbalovacího seznamu. 

1. Přidejte klip do projektu výběrem segmentu, který chcete přidat. Tento klip můžete zrušit opětovným kliknutím na segment.
    
    Přidejte všechny segmenty videa kliknutím na možnost nabídky seznamu vedle videa a výběrem **možnosti Vybrat všechny segmenty**. 

    ![Přidat vše](./media/video-indexer-view-edit/add-all.png)

    Vymažete celý výběr výběrem možnosti Vymazat výběr.

> [!TIP]
> Při výběru a objednávání klipů můžete zobrazit náhled videa v přehrávači na pravé straně stránky. 

![Preview](./media/video-indexer-view-edit/preview.png)

Nezapomeňte projekt uložit při provádění změn výběrem **možnosti Uložit projekt**. 

### <a name="render-and-download-the-project"></a>Vykreslení a stažení projektu

> [!NOTE]
> U placených účtů videoindexermá vykreslování projektu náklady na kódování. Video Indexer zkušební účty jsou omezeny na 5 hodin vykreslování.

1. Po dokončení se ujistěte, že byl projekt uložen. Nyní můžete vykreslit tento projekt. Vyberte **Vykreslení a stažení**. 

    ![Uložit](./media/video-indexer-view-edit/save.png)

    K dispozici bude vyskakovací okno, které vám řekne, že Video indexer vykreslí soubor a pak odkaz ke stažení bude odeslán na váš e-mail. Vyberte Pokračovat. 
    
    Zobrazí se také oznámení, že projekt je vykreslen v horní části stránky. Po dokončení vykreslení se zobrazí nové oznámení, že projekt byl úspěšně vykreslen. Kliknutím na oznámení projekt stáhnete. To bude stahovat projekt ve formátu mp4.

    ![Vykreslování provedeno](./media/video-indexer-view-edit/rendering-done.png)

1. K uloženým projektům můžete přistupovat na kartě **Projekty.** 

    Pokud vyberete tento projekt, zobrazí se všechny přehledy a časová osa tohoto projektu. Pokud vyberete **Editor videa**, můžete pokračovat v úpravě tohoto projektu. Úpravy zahrnují přidání nebo odebrání videí a klipů nebo přejmenování projektu.

    ![Video editor](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>Vytvoření projektu z videa

Nový projekt můžete vytvořit přímo z videa ve vašem účtu. 

1. Přejděte na kartě **Knihovna** na webu Video Indexer.
1. Otevřete video, které chcete použít k vytvoření projektu. Na stránce Přehledy a časové osy vyberte tlačítko **Editor videa.**

    Tím přejdete na stejnou stránku, kterou jste použili k vytvoření nového projektu. Na rozdíl od nového projektu se zobrazí segmenty přehledů s časovým razítkem videa, které jste dříve začali upravovat.

## <a name="see-also"></a>Viz také

[Přehled Video Indexeru](video-indexer-overview.md)

