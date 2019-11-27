---
title: K dispozici Poznámkový blok Jupyter jako prezentace v Azure
description: Jak nakonfigurovat buňky pro režim prezentace v poznámkovém bloku Jupyter a pak prezentujte prezentace pomocí rozšíření VZESTUPU.
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: d180eaf571fa57191e3b0856020b02f05d05e344
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277597"
---
# <a name="run-a-notebook-slideshow"></a>Spusťte Poznámkový blok prezentace

Poznámkových bloků Azure je předem nakonfigurovaný pomocí Jupyter/IPython prezentace rozšíření (VZESTUPU), který umožňuje poznámkového bloku přímo jako prezentaci. V prezentaci buňky jsou obvykle zobrazené jeden po druhém použitím velikosti písma, která je vhodná pro prezentace na velkých obrazovkách a můžete se ještě může spustit kód, spíše než přepnutí na ukázku samostatné počítače.

Zobrazení standardního Poznámkový blok, ve kterém uvidíte Markdown a buňky s kódem všechno dohromady na následujícím obrázku:

![Poznámkový blok v standardní zobrazení](media/slideshow/slideshow-notebook-view.png)

Při zahájení prezentace je první buňka zvětšena tak, aby vyplnila prohlížeč, kde **X** v levém horním rohu prezentace opustí **?** v dolním levém zobrazí klávesové zkratky a šipky v pravém dolním přecházet mezi snímky:

![Poznámkový blok v režimu prezentace](media/slideshow/slideshow-slide-view.png)

Příprava poznámkového bloku pro prezentaci zahrnuje dvě primární aktivity:

1. Protože buňky Markdown se vykreslují velká písma, nemusí být zobrazeny v prezentaci nějaký obsah. Proto obvykle omezit množství textu v jakékoli dané buňky; záhlaví řádků čtyř až šesti obvykle je nejvhodnější. Pokud máte více textu, rozdělte tyto informace do více buněk.

2. Konfigurace chování každá buňka v prezentaci pomocí nástrojů buňky prezentace. Typy buňky určují chování navigačních tlačítek.

## <a name="the-anatomy-of-a-slideshow"></a>Anatomie prezentaci

Je-li provést náhodné Poznámkový blok a používat ho pro prezentaci, obvykle pro vás, že všechny buňky jsou neuspořádaná společně a většina obsahu je skrytý spodní část okna prohlížeče. Aby efektivní prezentace, musíte přiřadit typ prezentace jednotlivé buňky pomocí nástrojů buňky prezentace:

1. V nabídce **zobrazení** vyberte **panel nástrojů buňky** > **prezentace**:

    ![Zapnutí nástrojů prezentace buňky](media/slideshow/slideshow-view-cell-toolbar.png)

1. Rozevírací seznam **typ snímku** se zobrazí v pravém horním rohu každé buňky v poznámkovém bloku:

    ![Panel nástrojů prezentace buňky](media/slideshow/slideshow-cell-toolbar.png)

1. Pro každou buňku vyberte jeden z pěti typů:

    ![Typy prezentace buněk](media/slideshow/slideshow-cell-slide-types.png)

    | Typ snímku | Chování |
    | --- | --- |
    | -(Nenastaveno) | Buňka se zobrazí s předcházejí buňky, která není často požadovaného efektu v prezentaci. |
    | Snímek | Buňka je primární snímku, procházet pomocí šipky vlevo a vpravo navigaci ovládacího prvku. |
    | Dílčí snímku | Buňka je "primární snímku, přejde na šipku dolů v ovládacím prvku navigace pomocí below". Na šipku nahoru vrátí primární snímku. Dílčí snímky se používají pro sekundární materiál může přeskočit na hlavní cestě prezentace, ale snadno dostupné v případě potřeby. |
    | Fragment | Při použití na šipku navigace (fragment odebere pomocí kláves Šipka nahoru), zobrazí se v rámci předchozího snímku nebo dílčí snímku obsah buňky. Fragment můžete použít s buňku kódu k Ujistěte se, že kód, který se zobrazí v rámci snímku, nebo můžete použít několik fragmentů aby text, který se zobrazí odrážky jeden po druhém (viz příklad v další části). Protože fragmenty sestavení na aktuálním snímku, nadbytečné fragmenty nebudou viditelné mimo dolní části okna prohlížeče. |
    | Přeskočit | Buňka se nezobrazí v prezentaci. |
    | Poznámky: | V buňce jako poznámky lektora, které se nezobrazují v prezentaci. |

1. Zpočátku je vhodné zvolit **snímek** pro každou buňku. Budete moct zprovoznit službu prezentaci a proveďte příslušné změny.

### <a name="example-fragment-cells-for-bullet-items"></a>Příklad: fragment buněk pro položky odrážky

Aby se odrážky na snímku zobrazovaly po jednom, umístěte záhlaví snímku do buňky Markdownu s typem **snímku** a potom každou odrážku umístěte do samostatné buňky Markdownu s typem **fragmentu** :

![Příklad vytvoření více buněk Markdownu pro položky s odrážkami](media/slideshow/slideshow-fragments.png)

Protože prezentaci vykreslí fragmenty s více svislé mezery než když jsou všechny odrážek v jedné buňce, nebudete moci používat libovolný počet položek odrážky.

## <a name="run-the-slideshow"></a>Spuštění prezentaci

1. Pokud jste upravovali všechny buňky Markdownu, ujistěte se, že je spouštíte pro vykreslování HTML, jinak se v prezentaci zobrazí *jako* Markdownu.

1. Jakmile nakonfigurujete **typ snímku** pro každou buňku, vyberte buňku, se kterou chcete prezentaci spustit, a pak na hlavním panelu nástrojů vyberte tlačítko pro vytvoření **přírůstku pro vložení/ukončení** :

    ![Zapne/ukončí NARŮSTAT prezentace tlačítko na hlavním panelu nástrojů](media/slideshow/slideshow-start.png)

1. Přecházet mezi snímky, jakož i fragmenty, použijte v ovládacím prvku navigační šipky vlevo a vpravo. Text v ovládacím prvku zobrazuje číslo představující *snímek. dílčí snímek*.

    ![Ovládací prvek navigace prezentace](media/slideshow/slideshow-navigation-control.png)

1. Přecházet mezi snímky a dílčí snímky, jakož i fragmenty, použijte nahoru a dolů šipkami, pokud je povoleno:

    ![Ovládací prvky pro navigaci k prezentaci pro dílčí snímky](media/slideshow/slideshow-navigation-control-subslide.png)

1. Na buňky kódu použijte na tlačítko Přehrát akci pro spuštění kódu; na snímku, zobrazí se výstup:

    ![Tlačítko Přehrát akci spustíte buňku kódu](media/slideshow/slideshow-run-code-cell.png)

    ![Zobrazí se výstup buňky kódu v prezentaci](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > Výstup buňky je považováno za součást buněk v prezentaci. Pokud spustíte buňky v poznámkovém bloku nebo zobrazení prezentace, se zobrazí v ostatních zobrazeních i výstup. Pokud chcete výstup vymazat, **použijte > ** **aktuální výstupy** > **Vymazat** příkaz (pro aktuální buňku) nebo **buňku** > **všechny výstupy** > **clear** (pro všechny buňky).

1. Až budete s prezentací hotovi, použijte **X** k návratu do zobrazení Poznámkový blok.

## <a name="next-steps"></a>Další kroky

- [Postupy: konfigurace a správa projektů](configure-manage-azure-notebooks-projects.md)
- [Postupy: Instalace balíčků z poznámkového bloku](install-packages-jupyter-notebook.md)
- [Postupy: práce s datovými soubory](work-with-project-data-files.md)
- [Postupy: přístup k datovým prostředkům](access-data-resources-jupyter-notebooks.md)
