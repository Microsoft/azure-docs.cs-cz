---
title: Použití editoru Video Indexer k vytváření projektů
titleSuffix: Azure Media Services
description: Toto téma ukazuje, jak použít Editor Video Indexer k vytváření projektů.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: 5eaf17281fef32d682a2dac17b379faf7ec23a53
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90970079"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>Použití editoru Video Indexer k vytváření projektů

Video Indexer web umožňuje používat hloubkové přehledy videí k: vyhledání správného mediálního obsahu, vyhledání částí, které vás zajímají, a použití výsledků k vytvoření zcela nového projektu. Po vytvoření se projekt dá vykreslit a stáhnout z Video Indexer a použije se ve svých vlastních aplikacích pro úpravy nebo v pracovních postupech.

Tato funkce může být užitečná v některých případech: 

* Vytváření zvýraznění filmů pro přípojná místa.
* Použití starých klipů videí v přetypováních zpráv.
* Vytváření kratšího obsahu pro sociální média

Tento článek ukazuje, jak vytvořit projekt od začátku a také jak vytvořit projekt z videa ve vašem účtu.

## <a name="create-new-project-and-manage-videos"></a>Vytvoření nového projektu a Správa videí

1. Přejděte na web [Video Indexer](https://www.videoindexer.ai/) a přihlaste se.
1. Vyberte kartu **projekty** . Pokud jste vytvořili projekty dříve, zobrazí se zde všechny ostatní projekty.
1. Klikněte na **vytvořit nový projekt**.  

    ![Nový projekt](./media/video-indexer-view-edit/new-project.png)
1. Pojmenujte svůj projekt kliknutím na ikonu tužky. Nahraďte text textem "projekt bez názvu" názvem projektu a klikněte na kontrolu.

    ![Nový projekt](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>Přidat videa do projektu

> [!NOTE]
> Projekty v současné době můžou obsahovat jenom videa indexovaná ve stejném jazyce. Jakmile vyberete video v jednom jazyce, nebudete moct videa přidat do svého účtu, který je v jiném jazyce.

1. Výběrem možnosti **přidat videa**přidejte videa, se kterými chcete v tomto projektu pracovat.

    Zobrazí se všechna videa ve vašem účtu a vyhledávací pole s textem "hledání textu, klíčových slov nebo vizuálního obsahu". Chcete-li vyhledat videa, která mají určenou osobu, popisek, značku, klíčové slovo nebo výskyt v přepisu a optickém rozpoznávání znaků.
    
    Například na následujícím obrázku hledáte videa, která zmiňují GitHub.
    
    ![Snímek obrazovky s vyhledáváním videí, která zmiňují GitHub, se dvěma výsledky](./media/video-indexer-view-edit/github.png)

    Výsledky můžete dál filtrovat tak, že vyberete **výsledky filtrování**. Můžete filtrovat tak, aby zobrazovala videa, která mají určitou osobu, nebo aby bylo možné určit, že chcete zobrazit jenom výsledky videa, které jsou v určitém jazyce, nebo jestli mají konkrétního vlastníka. <br/> Můžete také zadat rozsah dotazu. Pokud například chcete v OCR vyhledat "GitHub", vyberte **vizuální text**.

    ![Filtrování](./media/video-indexer-view-edit/visual-text.png)

    Do dotazu můžete převrstvit více filtrů. Pomocí **+** / **-** tlačítek můžete přidat nebo odebrat filtry. Pomocí **Vymazat filtry** odeberte všechny filtry.
1. Pokud chcete přidat videa, vyberte je a pak vyberte **Přidat**.
1. Teď se zobrazí všechna videa, která jste zvolili. Jedná se o videa, ze kterých se chystáte vybrat klipy pro svůj projekt.

    Pořadí videí můžete změnit přetažením nebo vyřazením nebo výběrem tlačítka nabídky seznam a výběrem možnosti **Přesunout dolů** nebo **Přesunout nahoru**. V nabídce seznam bude také možné odebrat video z tohoto projektu. 

    ![Snímek obrazovky ukazuje Video Indexer s místní nabídkou pro jedno z videí, která se mají odebrat, Vymazat výběr nebo přesunout dolů.](./media/video-indexer-view-edit/rearrange.png)
    
    K tomuto projektu máte možnost kdykoli přidat další videa výběrem možnosti **přidat videa**. Do projektu můžete také přidat více výskytů stejného videa. Tuto akci můžete chtít provést, pokud chcete zobrazit klip z jednoho videa a potom klip z druhého a potom z prvního videa vytvořit jiný klip. 

### <a name="select-clips-to-use-in-your-project"></a>Vyberte klipy, které chcete použít v projektu.

Pokud kliknete na šipku dolů na pravé straně každého videa, otevře se přehledy ve videu na základě časových razítek (klipy videa). 

1. Vyberte **Zobrazit přehledy** a upravte, které přehledy chcete zobrazit a které nechcete vidět. 

    ![Zobrazit přehledy](./media/video-indexer-view-edit/insights.png)
1. Pokud chcete vytvářet dotazy pro konkrétní klipy, použijte vyhledávací pole, které říká "hledání v přepisu, vizuální text, lidi a štítky".
1. Kliknutím na **možnost Možnosti filtru**přidejte filtry, které vám pomohou určit, jaké scény hledáte.

    ![Možnosti filtru](./media/video-indexer-view-edit/filter-options.png)

    Například můžete chtít zobrazit klipy, na kterých se zmiňuje GitHub, zatímco na obrazovce Donovan Brown. V takovém případě je třeba přidat filtr "include", který má "lidé" jako typ Insight. Pak musíte do vyhledávacího pole pro filtr zadat text "Donovan Brown".
    
    ![Snímek obrazovky zobrazuje Video Indexer s lidmi vybranými pro filtr zahrnutí.](./media/video-indexer-view-edit/include.png)
    
    Pokud chcete klipy, na kterých se zmiňuje GitHub, zatímco Donovan Brown _není na obrazovce_ , stačí změnit filtr zahrnout do filtru "vyloučit" pomocí rozevíracího seznamu. 

1. Přidejte do projektu klip tak, že vyberete segment, který chcete přidat. Tento klip můžete zrušit kliknutím na segment znovu.
    
    Přidejte všechny segmenty videa kliknutím na možnost rozevírací seznam vedle videa a výběrem možnosti **Vybrat všechny segmenty**. 

    ![Přidat vše](./media/video-indexer-view-edit/add-all.png)

    Výběrem možnosti Vymazat výběr můžete zrušit výběr všech položek.

> [!TIP]
> Při výběru a objednávání klipů můžete zobrazit náhled videa v přehrávači na pravé straně stránky. 

![Snímek obrazovky zobrazuje Video Indexer s náhledem videa na pravé straně okna.](./media/video-indexer-view-edit/preview.png)

Nezapomeňte uložit projekt, když provedete změny, a to tak, že vyberete **Uložit projekt**. 

### <a name="render-and-download-the-project"></a>Vykreslit a stáhnout projekt

> [!NOTE]
> U Video Indexer placených účtů má vykreslování projektu náklady na kódování. Účty Video Indexer zkušební verze jsou omezené na 5 hodin vykreslování.

1. Až budete hotovi, ujistěte se, že je váš projekt uložený. Tento projekt teď můžete vykreslit. Vyberte **vykreslování a stáhnout**. 

    ![Snímek obrazovky ukazuje Video Indexer s možností vykreslování a stažení vašeho projektu.](./media/video-indexer-view-edit/save.png)

    Zobrazí se automaticky otevírané okno s oznámením, že video indexer vygeneruje soubor a pak se odkaz pro stažení pošle na e-mail. Vyberte pokračovat. 
    
    Zobrazí se také oznámení o tom, že se projekt vykresluje nad stránku. Po vykreslení se zobrazí nové oznámení, že projekt byl úspěšně vykreslen. Kliknutím na oznámení Stáhněte projekt. Stáhne projekt ve formátu MP4.

    ![Vykreslování dokončeno](./media/video-indexer-view-edit/rendering-done.png)

1. Na kartě **projekty** lze získat přístup k uloženým projektům. 

    Pokud vyberete tento projekt, zobrazí se všechny přehledy a časová osa tohoto projektu. Pokud vyberete možnost **Editor videa**, můžete pokračovat v provádění úprav tohoto projektu. Úpravy zahrnují přidávání a odebírání videí a klipů nebo přejmenovávání projektu.

    ![Editor videí](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>Vytvoření projektu z videa

Nový projekt můžete vytvořit přímo z videa ve vašem účtu. 

1. Přejít na kartu **Knihovna** na webu video indexer.
1. Otevřete video, které chcete použít k vytvoření projektu. Na stránce přehledy a časová osa vyberte tlačítko **Editor videa** .

    Tím přejdete na stejnou stránku, kterou jste použili k vytvoření nového projektu. Na rozdíl od nového projektu uvidíte segmenty pro přehledy s časovým razítkem videa, které jste dříve začali upravovat.

## <a name="see-also"></a>Viz také

[Přehled Video Indexeru](video-indexer-overview.md)

