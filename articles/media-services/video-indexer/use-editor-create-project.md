---
title: Vytváření projektů pomocí editoru Video Indexer
titlesuffix: Azure Media Services
description: Toto téma ukazuje, jak vytvářet projekty pomocí editoru Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: 725e265bb7f8c98db19971c50e690974cb38a0d2
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2019
ms.locfileid: "65518142"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>Vytváření projektů pomocí editoru Video Indexer

Video Indexer webu, vám umožní použít vaše videa hluboko: najít správné mediálního obsahu, vyhledejte části zajímá a použít výsledky vytvořte zcela nový projekt. Po vytvoření projektu můžete vykreslení a stáhnout z Video Indexer a použít vlastní úpravy aplikací, nebo podřízený pracovní postupy.

Některé scénáře, kde může najít tuto funkci užitečné jsou: 

* Vytváření video pro přípojných zvýrazní.
* Pomocí staré klipy videa v přetypování zpráv.
* Vytváření kratší obsahu pro sociální sítě.

Tento článek popisuje, jak vytvořit projekt od začátku a také jak vytvořit projekt z videa ve vašem účtu.

## <a name="create-new-project-and-manage-videos"></a>Vytvoření nového projektu a spravovat videa

1. Přejděte na web [Video Indexer](https://www.videoindexer.ai/) a přihlaste se.
1. Vyberte **projekty** kartu. Pokud jste vytvořili projekty před, zobrazí se všechny ostatní projekty zde.
1. Klikněte na tlačítko **vytvořit nový projekt**.  

    ![Nový projekt](./media/video-indexer-view-edit/new-project.png)
1. Pojmenujte svůj projekt kliknutím na ikonu tužky. Nahraďte text, který říká "Bez názvu projektu" název vašeho projektu a klikněte na kontrola.

    ![Nový projekt](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>Přidávání videí do projektu

> [!NOTE]
> Projekty v současné době může obsahovat jenom videa indexované ve stejném jazyce. Jakmile vyberete videa v jednom jazyce, nelze přidat videa ve vašem účtu, které jsou v jiném jazyce.

1. Přidávání videí, které chcete pracovat v tomto projektu tak, že vyberete **přidávat videa**.

    Zobrazí se všechna videa v účtu a vyhledávací pole s textem "Vyhledávání textu, klíčová slova nebo vizuální obsah". Pro vyhledávání videí, které mají zadané osoby, popisek, značky, – klíčové slovo nebo výskytu v přepisu a rozpoznávání znaků.
    
    Například na následujícím obrázku Těšíme se na videa, která zmiňovat "Githubu".
    
    ![GitHub](./media/video-indexer-view-edit/github.png)

    Výsledky můžete dále filtrovat tak, že vyberete **filtrování výsledků**. Můžete filtrovat, chcete-li zobrazit videa, které mají určité osoby v nich nebo k určení, že chcete pouze vidět videa výsledky, které jsou v určitém jazyce, nebo mají konkrétní vlastníka. <br/> Můžete také zadat rozsah vašeho dotazu. Například, pokud chcete vyhledávat "Githubu" OCR, vyberte **Visual Text**.

    ![Filtr](./media/video-indexer-view-edit/visual-text.png)

    Několik filtrů vrstvit do dotazu. Použití **+** / **-** tlačítka Přidat nebo odebrat filtry. Použití **zrušit filtry** odebrat všechny filtry.
1. Pokud chcete přidat videa, vyberte je a pak vyberte **přidat**.
1. Nyní zobrazí se všechna videa, kterou jste zvolili. Jedná se o videa, ze kterých chcete vybrat klipy pro váš projekt.

    Můžete změnit pořadí videa přetažením nebo nabídky tlačítka seznamu a výběrem **přesunout dolů** nebo **nahoru**. Z nabídky seznam také bude možné odebrat video z tohoto projektu. 

    ![Změna uspořádání](./media/video-indexer-view-edit/rearrange.png)
    
    Máte možnost přidávat další videa do tohoto projektu v okamžiku tak, že vyberete **přidávat videa**. Vícenásobné výskyty stejného video můžete také přidat do projektu. Můžete to provést, pokud chcete zobrazit klip z jeden video a potom na položku Galerie z jiného a pak další galerie z první video. 

### <a name="select-clips-to-use-in-your-project"></a>Vyberte klipy používat ve vašem projektu

Pokud kliknete na šipku dolů na pravé straně každého videa, se otevře insights ve videu založené na časových razítek (klipy videa). 

1. Vyberte **zobrazit přehledy** přizpůsobení přehledy, které chcete zobrazit a které nechcete zobrazit. 

    ![Zobrazit přehledy](./media/video-indexer-view-edit/insights.png)
1. Pokud chcete vytvořit dotazy na konkrétní klipy, použijte vyhledávací pole s textem "Hledat v přepisu, visual text, lidé a popisky".
1. Přidejte filtry, které dále určit podrobnosti, jaké scén hledáte tak, že vyberete **možnosti filtru**.

    ![Nastavení filtru](./media/video-indexer-view-edit/filter-options.png)

    Můžete například zobrazit klipy, ve kterém je uvedený Githubu Donovan Brown je na obrazovce. V takovém případě budete muset přidat filtr "zahrnutí", který má "Osob" jako typ insight. Pak je třeba zadat v "Donovan Brown" do vyhledávacího pole filtru.
    
    ![Zahrnout](./media/video-indexer-view-edit/include.png)
    
    Pokud chcete, ve kterém je uvedený Githubu Donovan Brown je klipy _není_ na obrazovce by jednoduše změňte filtr "zahrnout" do filtr "vyloučit" pomocí rozevíracího seznamu. 

1. Přidejte klip tak, že vyberete segment, který chcete přidat do projektu. Zrušit výběr tento klip znovu kliknutím na segment.
    
    Kliknutím na možnost nabídky seznamu vedle videa a výběrem možnosti Přidat všechny segmenty videa **vyberte všechny segmenty**. 

    ![Přidat vše](./media/video-indexer-view-edit/add-all.png)

    Výběrem Vymazat výběr můžete vymazat vše vašeho výběru.

> [!TIP]
> Jako jsou výběr a pořadí klipů, můžete zobrazit náhled videa v přehrávači na pravé straně stránky. 

![Náhled](./media/video-indexer-view-edit/preview.png)

Nezapomeňte uložit projekt, pokud provedete změny tak, že vyberete **uložit projekt**. 

### <a name="render-and-download-the-project"></a>Vykreslení a stáhněte si projekt

> [!NOTE]
> Video Indexer placené účty vykreslování váš projekt má náklady na kódování. Video Indexer zkušebními účty jsou omezené na 5 hodin vykreslování.

1. Až budete hotoví, ujistěte se, že váš projekt se uložil. Nyní můžete zobrazit tento projekt. Vyberte **vykreslení a stáhnout**. 

    ![Uložení](./media/video-indexer-view-edit/save.png)

    Bude automaticky otevíraného okna s oznámením, že Video indexer bude vykreslení souboru a odkaz ke stažení bude odeslat e-mailu. Vyberte pokračovat. 
    
    Také se zobrazí oznámení, že projekt je vykreslení na stránce. Po dokončení vykreslení, zobrazí se nové oznámení, že projekt bylo úspěšně vykresleno. Klikněte na oznámení a projekt stáhnout. Jinak se stáhne projekt ve formátu mp4.

    ![Vykreslování Hotovo](./media/video-indexer-view-edit/rendering-done.png)

1. Můžete přistupovat z uložených projektů **projekty** kartu. 

    Pokud vyberete tento projekt, se zobrazí všechny přehledy a časová osa tohoto projektu. Pokud vyberete **Video editor**, můžete pokračovat ve vytváření úpravy pro tento projekt. Úpravy patří přidání nebo odebrání videa a klipy nebo přejmenování projektu.

    ![Editor videa](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>Vytvoření projektu z videa

Nový projekt můžete vytvořit přímo z videa ve vašem účtu. 

1. Přejděte **knihovny** kartu webu Video Indexer.
1. Otevřete na video, které chcete použít k vytvoření projektu. Na stránce Přehled a časová osa, vyberte **Video editor** tlačítko.

    Tím přejdete na stejnou stránku, který jste použili k vytvoření nového projektu. Na rozdíl od nový projekt naleznete v tématu segmenty insights časovým razítkem videa, který byl spuštěn dříve úpravy.

## <a name="see-also"></a>Další informace najdete v tématech

[Přehled Video Indexeru](video-indexer-overview.md)

