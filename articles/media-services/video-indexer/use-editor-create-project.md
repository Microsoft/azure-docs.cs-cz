---
title: Použití editoru Video Indexer k vytváření projektů a přidávání videoklipů
titleSuffix: Azure Media Services
description: Toto téma ukazuje, jak použít Editor Video Indexer k vytváření projektů a přidávání videoklipů.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/28/2020
ms.author: juliako
ms.openlocfilehash: 3a3ac3f2db4e23f03f83a98bee0aceaddef9f889
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433419"
---
# <a name="add-video-clips-to-your-projects"></a>Přidávání videoklipů do projektů

Video Indexer web umožňuje používat hloubkové přehledy videí k: vyhledání správného mediálního obsahu, vyhledání částí, které vás zajímají, a použití výsledků k vytvoření zcela nového projektu. 

Po vytvoření se projekt dá vykreslit a stáhnout z Video Indexer a použije se ve svých vlastních aplikacích pro úpravy nebo v pracovních postupech.

Tato funkce může být užitečná v některých případech: 

* Vytváření zvýraznění filmů pro přípojná místa.
* Použití starých klipů videí v přetypováních zpráv.
* Vytváření kratšího obsahu pro sociální média

Tento článek ukazuje, jak vytvořit projekt a přidat vybrané klipy z videí do projektu. 

## <a name="create-new-project-and-manage-videos"></a>Vytvoření nového projektu a Správa videí

1. Přejděte na web [Video Indexer](https://www.videoindexer.ai/) a přihlaste se.
1. Vyberte kartu **projekty** . Pokud jste vytvořili projekty dříve, zobrazí se zde všechny ostatní projekty.
1. Klikněte na **vytvořit nový projekt**.  

    :::image type="content" source="./media/video-indexer-view-edit/new-project.png" alt-text="Vytvoření nového projektu":::
1. Pojmenujte svůj projekt kliknutím na ikonu tužky. Nahraďte text textem "projekt bez názvu" názvem projektu a klikněte na kontrolu.

    :::image type="content" source="./media/video-indexer-view-edit/new-project-edit-name.png" alt-text="Nový projekt":::
    
### <a name="add-videos-to-the-project"></a>Přidat videa do projektu

> [!NOTE]
> Projekty v současné době můžou obsahovat jenom videa indexovaná ve stejném jazyce. </br>Když vyberete video v jednom jazyce, nebudete moct videa přidat do svého účtu, který je v jiném jazyce. videa s jinými jazyky se budou šedě vybarvit/zakázat.

1. Výběrem možnosti **přidat videa** přidejte videa, se kterými chcete v tomto projektu pracovat.

    Zobrazí se všechna videa ve vašem účtu a vyhledávací pole s textem "hledání textu, klíčových slov nebo vizuálního obsahu". Můžete vyhledat videa, která mají určenou osobu, popisek, značku, klíčové slovo nebo výskyt v přepisu a optickém rozpoznávání znaků.
    
    Například na následujícím obrázku jsme hledali videa, která zmiňují "vlastní vize" jenom v přepisu (Pokud chcete výsledky hledání filtrovat, použijte **Filtr** ).
    
    :::image type="content" source="./media/video-indexer-view-edit/custom-vision.png" alt-text="Snímek obrazovky s vyhledáváním videí, která zmiňují vlastní vize":::
1. Kliknutím na **Přidat** přidejte videa do projektu.
1. Teď se zobrazí všechna videa, která jste zvolili. Jedná se o videa, ze kterých se chystáte vybrat klipy pro svůj projekt.

    Pořadí videí můžete změnit přetažením nebo vyřazením nebo výběrem tlačítka nabídky seznam a výběrem možnosti **Přesunout dolů** nebo **Přesunout nahoru**. V nabídce seznam bude také možné odebrat video z tohoto projektu. 
    
    Do tohoto projektu můžete kdykoli přidat další videa výběrem možnosti **přidat videa**. Do projektu můžete také přidat více výskytů stejného videa. Tuto akci můžete chtít provést, pokud chcete zobrazit klip z jednoho videa a potom klip z druhého a potom z prvního videa vytvořit jiný klip. 

### <a name="select-clips-to-use-in-your-project"></a>Vyberte klipy, které chcete použít v projektu.

Pokud kliknete na šipku dolů na pravé straně každého videa, otevře se přehledy ve videu na základě časových razítek (klipy videa). 

1. Pokud chcete vytvářet dotazy pro konkrétní klipy, použijte vyhledávací pole, které říká "hledání v přepisu, vizuální text, lidi a štítky".
1. Vyberte **Zobrazit přehledy** a upravte, které přehledy chcete zobrazit a které nechcete vidět. 

    :::image type="content" source="./media/video-indexer-view-edit/search-try-cognitive-services.png" alt-text="Snímek obrazovky znázorňující hledání videí, která říká vyzkoušet službu rozpoznávání":::
1. Kliknutím na **možnost Možnosti filtru** přidejte filtry, které vám pomohou určit, jaké scény hledáte.

    Můžete přidat více filtrů. 
1. Až budete s výsledky spokojeni, vyberte klipy, které chcete přidat do tohoto projektu, a vyberte segment, který chcete přidat. Tento klip můžete zrušit kliknutím na segment znovu.
    
    Přidejte všechny segmenty videa (nebo všechny, které byly vráceny po hledání), kliknutím na položku nabídky seznam vedle videa a vybráním možnosti **Vybrat vše**. 

Při výběru a objednávání klipů můžete zobrazit náhled videa v přehrávači na pravé straně stránky. 

> [!IMPORTANT]
> Nezapomeňte uložit projekt, když provedete změny, a to tak, že v horní části stránky vyberete **Uložit projekt** . 

### <a name="render-and-download-the-project"></a>Vykreslit a stáhnout projekt

> [!NOTE]
> U Video Indexer placených účtů má vykreslování projektu náklady na kódování. Účty Video Indexer zkušební verze jsou omezené na 5 hodin vykreslování.

1. Až budete hotovi, ujistěte se, že je váš projekt uložený. Tento projekt teď můžete vykreslit. Klikněte na **vykreslení**. zobrazí se automaticky otevírané okno s oznámením, že video indexer vykreslí soubor a pak se odkaz na stažení pošle e-mailem. Vyberte pokračovat. 

    :::image type="content" source="./media/video-indexer-view-edit/render-download.png" alt-text="Snímek obrazovky ukazuje Video Indexer s možností vykreslování a stažení vašeho projektu.":::
    
    Zobrazí se také oznámení o tom, že se projekt vykresluje nad stránku. Po vykreslení se zobrazí nové oznámení, že projekt byl úspěšně vykreslen. Kliknutím na oznámení Stáhněte projekt. Stáhne projekt ve formátu MP4.
1. Na kartě **projekty** lze získat přístup k uloženým projektům. 

    Pokud vyberete tento projekt, zobrazí se všechny přehledy a časová osa tohoto projektu. Pokud vyberete možnost **Editor videa**, můžete pokračovat v provádění úprav tohoto projektu. Úpravy zahrnují přidávání a odebírání videí a klipů nebo přejmenovávání projektu.
    
## <a name="create-a-project-from-your-video"></a>Vytvoření projektu z videa

Nový projekt můžete vytvořit přímo z videa ve vašem účtu. 

1. Přejít na kartu **Knihovna** na webu video indexer.
1. Otevřete video, které chcete použít k vytvoření projektu. Na stránce přehledy a časová osa vyberte tlačítko **Editor videa** .

    Tím přejdete na stejnou stránku, kterou jste použili k vytvoření nového projektu. Na rozdíl od nového projektu uvidíte segmenty pro přehledy s časovým razítkem videa, které jste dříve začali upravovat.

## <a name="see-also"></a>Viz také

[Přehled Video Indexeru](video-indexer-overview.md)

