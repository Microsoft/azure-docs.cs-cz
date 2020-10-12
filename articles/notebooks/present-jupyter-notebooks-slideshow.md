---
title: Prezentovat Jupyter Poznámkový blok jako prezentaci Azure Notebooks Preview
description: Naučte se konfigurovat buňky pro režim prezentace v Jupyter poznámkovém bloku a pak prezentaci prezentovat pomocí rozšíření pro zvýšení počtu.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 2fe337361436ecfc8eabf2855ad633b891db69d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85834042"
---
# <a name="run-a-notebook-slideshow-in-azure-notebooks-preview"></a>Spuštění prezentace poznámkového bloku v Azure Notebooks Preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Azure Notebooks je předem nakonfigurovaný s rozšířením prezentace Jupyter/IPython (VZESTUPně), které umožňuje prezentovat Poznámkový blok přímo jako prezentaci. V prezentaci jsou buňky obvykle zobrazeny po jednom pomocí velikosti písma, která je vhodná pro prezentace na velkých obrazovkách, a stále můžete kód spustit, nikoli přepnout na samostatný ukázkový počítač.

Následující obrázek znázorňuje standardní zobrazení poznámkového bloku, ve kterém můžete zobrazit Markdownu a buňky kódu dohromady:

![Poznámkový blok ve standardním zobrazení](media/slideshow/slideshow-notebook-view.png)

Při zahájení prezentace je první buňka zvětšena tak, aby vyplnila prohlížeč, kde **X** v levém horním rohu prezentace opustí **?** v levém dolním rohu se zobrazí klávesové zkratky a šipky v pravém dolním rohu mezi snímky:

![Poznámkový blok v režimu prezentace](media/slideshow/slideshow-slide-view.png)

Příprava poznámkového bloku pro prezentaci zahrnuje dvě primární aktivity:

1. Vzhledem k tomu, že se buňky Markdownu vykreslují s velkými písmy, nemusí být v prezentaci vidět nějaký obsah. Tím se obvykle omezí množství textu v určité buňce; obvykle funguje záhlaví, které má čtyři až šest řádků. Pokud máte více textu, rozdělte tyto informace do více buněk.

2. Nakonfigurujte chování jednotlivých buněk v prezentaci pomocí panelu nástrojů buňky prezentace. Typy buněk určují chování navigačních tlačítek.

## <a name="the-anatomy-of-a-slideshow"></a>Anatomie prezentace

Pokud vezmete náhodný Poznámkový blok a použijete ho pro prezentaci, obvykle zjistíte, že jsou všechny buňky jumbled společně, a většina obsahu je v dolní části okna prohlížeče skrytá. Chcete-li vytvořit efektivní prezentaci, je třeba přiřadit typ prezentace ke každé buňce pomocí panelu nástrojů buňky prezentace:

1. V nabídce **zobrazení** vyberte položku Prezentace **panelu nástrojů buňky**  >  **Slideshow**:

    ![Zapnutí panelu nástrojů prezentace buněk](media/slideshow/slideshow-view-cell-toolbar.png)

1. Rozevírací seznam **typ snímku** se zobrazí v pravém horním rohu každé buňky v poznámkovém bloku:

    ![Panel nástrojů prezentace buňky](media/slideshow/slideshow-cell-toolbar.png)

1. Pro každou buňku vyberte jeden z pěti typů:

    ![Typy prezentace buněk](media/slideshow/slideshow-cell-slide-types.png)

    | Typ snímku | Chování |
    | --- | --- |
    | -(Nenastaveno) | Buňka se zobrazí s předchozí buňkou, která se často nejedná o požadovaný efekt v prezentaci. |
    | Snímek | Buňka je primární snímek, který se přechází pomocí levé a pravé šipky ovládacího prvku navigace. |
    | Dílčí snímek | Buňka je "pod" primárním snímkem, přechodem na použití šipky dolů ovládacího prvku navigace. Šipka nahoru se vrátí k primárnímu snímku. Dílčí snímky se používají pro sekundární materiál, který můžete přeskočit v hlavní cestě prezentace, ale v případě potřeby je možné snadno zpřístupnit. |
    | Fragment | Obsah buňky se zobrazí v kontextu předchozího snímku nebo dílčího snímku při použití šipky dolů (při použití šipky nahoru se odebere fragment). Pomocí fragmentu s buňkou kódu můžete nastavit, aby se kód zobrazoval v rámci snímku, nebo můžete použít více fragmentů, aby se odrážky textu zobrazovaly po jednom (viz příklad v další části). Vzhledem k tomu, že fragmenty jsou sestaveny na aktuálním snímku, nadbytečné fragmenty nebudou zobrazeny v dolní části okna prohlížeče. |
    | Přeskočit | V prezentaci není zobrazená buňka. |
    | Poznámky | Buňka obsahuje poznámky mluvčího, které se nezobrazují v prezentaci. |

1. Zpočátku je vhodné zvolit **snímek** pro každou buňku. Pak můžete prezentaci spustit a provést příslušné úpravy.

### <a name="example-fragment-cells-for-bullet-items"></a>Příklad: fragment buněk pro položky odrážek

Aby se odrážky na snímku zobrazovaly po jednom, umístěte záhlaví snímku do buňky Markdownu s typem **snímku** a potom každou odrážku umístěte do samostatné buňky Markdownu s typem **fragmentu** :

![Příklad vytvoření více Markdownu buněk pro položky odrážek](media/slideshow/slideshow-fragments.png)

Vzhledem k tomu, že prezentace vykresluje fragmenty s více svislými mezerami, než jsou všechny odrážky ve stejné buňce, pravděpodobně nebudete moci použít tolik položek odrážek.

## <a name="run-the-slideshow"></a>Spuštění prezentace

1. Pokud jste upravovali všechny buňky Markdownu, ujistěte se, že je spouštíte pro vykreslování HTML, jinak se v prezentaci zobrazí *jako* Markdownu.

1. Jakmile nakonfigurujete **typ snímku** pro každou buňku, vyberte buňku, se kterou chcete prezentaci spustit, a pak na hlavním panelu nástrojů vyberte tlačítko pro vytvoření **přírůstku pro vložení/ukončení** :

    ![Tlačítko pro vložení nebo ukončení zvýšení prezentace na hlavním panelu nástrojů](media/slideshow/slideshow-start.png)

1. Chcete-li procházet mezi snímky a fragmenty, použijte levou a pravou šipku v ovládacím prvku navigace. Text v ovládacím prvku zobrazuje číslo představující *snímek. dílčí snímek*.

    ![Navigační ovládací prvek prezentace](media/slideshow/slideshow-navigation-control.png)

1. Chcete-li procházet snímky a dílčí snímky i fragmenty, použijte šipky nahoru a dolů, pokud je tato možnost povolena:

    ![Ovládací prvky pro navigaci prezentace pro dílčí snímky](media/slideshow/slideshow-navigation-control-subslide.png)

1. V buňce kódu použijte tlačítko Přehrát ke spuštění kódu; výstup se zobrazí na snímku:

    ![Tlačítko Přehrát pro spuštění buňky kódu](media/slideshow/slideshow-run-code-cell.png)

    ![Výstup buňky kódu se zobrazí v zobrazení prezentace.](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > Výstup buňky se považuje za součást buňky v prezentaci. Pokud jste v zobrazení poznámkového bloku nebo zobrazení prezentace spustili buňku, výstup se zobrazí také v jiném zobrazení. Výstup vymažete tak, že **Cell**použijete možnost  >  **Vymazat aktuální výstupy**  >  **Clear** pro buňku (pro aktuální buňku) nebo **Cell**  >  **všechny výstupy**buňky  >  **Vymazat** (pro všechny buňky).

1. Až budete s prezentací hotovi, použijte **X** k návratu do zobrazení Poznámkový blok.

## <a name="next-steps"></a>Další kroky

- [Postupy: konfigurace a správa projektů](configure-manage-azure-notebooks-projects.md)
- [Postupy: Instalace balíčků z poznámkového bloku](install-packages-jupyter-notebook.md)
- [Postupy: práce s datovými soubory](work-with-project-data-files.md)
- [Postupy: přístup k datovým prostředkům](access-data-resources-jupyter-notebooks.md)
