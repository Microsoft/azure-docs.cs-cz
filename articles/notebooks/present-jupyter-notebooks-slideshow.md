---
title: Prezentace poznámkového bloku Jupyter jako prezentace v náhledu poznámkových bloků Azure
description: Přečtěte si, jak nakonfigurovat buňky pro režim prezentace v poznámkovém bloku Jupyter a potom prezentaci prezentovat pomocí rozšíření RISE.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 05dd3d9c5580e208ecf6f9e6d762476b0b493a6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647114"
---
# <a name="run-a-notebook-slideshow-in-azure-notebooks-preview"></a>Spuštění prezentace poznámkového bloku v náhledu poznámkových bloků Azure

Poznámkové bloky Azure je předem nakonfigurované s Jupyter/IPython Slideshow Extension (RISE), který umožňuje prezentovat poznámkový blok přímo jako prezentaci. V prezentaci jsou buňky obvykle zobrazeny jeden po druhém pomocí velikosti písma, která je vhodná pro prezentaci na velkých obrazovkách, a stále můžete kód spustit, nikoli přepnout na samostatný demo počítač.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Následující obrázek znázorňuje standardní zobrazení poznámkového bloku, ve kterém můžete vidět Markdown a buňky kódu dohromady:

![Poznámkový blok ve standardním zobrazení](media/slideshow/slideshow-notebook-view.png)

Když spustíte prezentaci, první buňka se zvětší, aby vyplnila prohlížeč, kde **X** v levém horním rohu opustí prezentaci, **?** v levém dolním rohu se zobrazují klávesové zkratky a šipky vpravo dole přecházejí mezi snímky:

![Poznámkový blok v režimu prezentace](media/slideshow/slideshow-slide-view.png)

Příprava poznámkového bloku pro prezentaci zahrnuje dvě primární aktivity:

1. Vzhledem k tomu, že markdownové buňky jsou vykreslovány velkými písmy, nemusí být některý obsah v prezentaci viditelný. Proto obvykle omezujete množství textu v dané buňce; záhlaví se čtyřmi až šesti řádky obvykle funguje nejlépe. Pokud máte více textu, rozdělte tyto informace do více buněk.

2. Nakonfigurujte chování jednotlivých buněk v prezentaci pomocí panelu nástrojů buňky prezentace. Typy buněk určují chování navigačních tlačítek.

## <a name="the-anatomy-of-a-slideshow"></a>Anatomie prezentace

Pokud vezmete náhodný poznámkový blok a použijete ho pro prezentaci, obvykle zjistíte, že všechny buňky jsou neuspořádané dohromady a velká část obsahu je skryta v dolní části okna prohlížeče. Chcete-li vytvořit efektivní prezentaci, musíte každé buňce přiřadit typ prezentace pomocí panelu nástrojů buňky Prezentace:

1. V nabídce **Zobrazení** vyberte Prezentace **panelu nástrojů** > **buňky**:

    ![Zapnutí panelu nástrojů prezentace buněk](media/slideshow/slideshow-view-cell-toolbar.png)

1. V pravém horním části každé buňky poznámkového bloku se zobrazí rozevírací nabídka **Typ snímku:**

    ![Panel nástrojů prezentace buněk](media/slideshow/slideshow-cell-toolbar.png)

1. Pro každou buňku vyberte jeden z pěti typů:

    ![Typy prezentací buněk](media/slideshow/slideshow-cell-slide-types.png)

    | Typ snímku | Chování |
    | --- | --- |
    | - (není nastaveno) | Buňka je zobrazena s předchozí buňkou, což často není požadovaný efekt v prezentaci. |
    | Snímek | Buňka je primární snímek, navigovaný pomocí levé a pravé šipky navigačního ovládacího prvku. |
    | Dílčí snímek | Buňka je "pod" primární snímek, navigoval pomocí šipky dolů navigačního ovládacího prvku. Šipka nahoru se vrátí na primární snímek. Dílčí snímky se používají pro sekundární materiál, který můžete přeskočit v hlavní cestě prezentace, ale je v případě potřeby snadno dostupný. |
    | Fragment | Obsah buňky se zobrazí v kontextu předchozího snímku nebo dílčího snímku při použití navigační šipky dolů (fragment se při použití šipky nahoru odstraní). Můžete použít fragment s buňkou kódu, aby se tento kód objevil v rámci snímku, nebo můžete použít více fragmentů, aby se textové odrážky zobrazovaly jeden po druhém (viz příklad v další části). Vzhledem k tomu, že fragmenty se staví na aktuálním snímku, přebytečné fragmenty nebudou viditelné z dolní části okna prohlížeče. |
    | Skip | Buňka není v prezentaci zobrazena. |
    | Poznámky | Buňka obsahuje poznámky lektora, které nejsou zobrazeny v prezentaci. |

1. Zpočátku je užitečné zvolit **snímek** pro každou buňku. Potom můžete spustit prezentaci a provést příslušné úpravy.

### <a name="example-fragment-cells-for-bullet-items"></a>Příklad: buňky fragmentu pro položky odrážek

Chcete-li, aby se odrážky na snímku zobrazovaly jeden po druhém, umístěte záhlaví snímku do buňky Markdown s typem **Snímek** a umístěte každou odrážku do samostatné buňky Markdown s typem **Fragmentu:**

![Příklad vytvoření více buněk Markdown pro položky odrážek](media/slideshow/slideshow-fragments.png)

Vzhledem k tomu, že prezentace vykresluje fragmenty s více svislými mezerami, než když jsou všechny odrážky ve stejné buňce, nemusí být možné použít tolik položek odrážek.

## <a name="run-the-slideshow"></a>Spuštění prezentace

1. Pokud jste upravili všechny Markdownovy buňky, nezapomeňte je spustit, aby se jejich HTML vykreslilo, jinak se v prezentaci zobrazí *jako* Markdown.

1. Po konfiguraci **typu snímku** pro každou buňku vyberte buňku, se kterou chcete spustit prezentaci, a pak vyberte tlačítko **Enter/Exit RISE Slideshow** na hlavním panelu nástrojů:

    ![Tlačítko Enter/Exit RISE Slideshow na hlavním panelu nástrojů](media/slideshow/slideshow-start.png)

1. Chcete-li procházet mezi snímky i fragmenty, použijte v navigačním ovládacím prvku šipku vlevo a vpravo. Text v ovládacím prvku zobrazuje číslo představující *snímek slide.sub-slide*.

    ![Ovládací prvek navigace prezentace](media/slideshow/slideshow-navigation-control.png)

1. Chcete-li procházet mezi snímky a dílčími snímky a fragmenty, použijte šipky nahoru a dolů, pokud je povoleno:

    ![Ovládací prvky navigace prezentace pro dílčí snímky](media/slideshow/slideshow-navigation-control-subslide.png)

1. Na buňku kódu použijte tlačítko play ke spuštění kódu; na snímku se zobrazí:

    ![Tlačítko Přehrát pro spuštění buňky kódu](media/slideshow/slideshow-run-code-cell.png)

    ![Výstup buňky kódu se zobrazí v prezentaci](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > Výstup buňky je považován za součást buňky v prezentaci. Pokud spustíte buňku v zobrazení poznámkového bloku nebo prezentace, zobrazí se výstup také v druhém zobrazení. Chcete-li vymazat výstup, použijte příkaz**Vymazat** **výstupy proudu** >  **buněk** > (pro aktuální buňku) nebo **Buňka** > **Všechny výstupy** > **Vymazat** (pro všechny buňky).

1. Až budete s prezentací hotovi, vraťte se do zobrazení poznámkového bloku pomocí **x.**

## <a name="next-steps"></a>Další kroky

- [Postup: Konfigurace a správa projektů](configure-manage-azure-notebooks-projects.md)
- [Postup: Instalace balíčků z poznámkového bloku](install-packages-jupyter-notebook.md)
- [Postup: Práce s datovými soubory](work-with-project-data-files.md)
- [Postup: Přístup k datovým prostředkům](access-data-resources-jupyter-notebooks.md)
