---
title: K dispozici Poznámkový blok Jupyter jako prezentace v Azure
description: Jak nakonfigurovat buňky pro režim prezentace v poznámkovém bloku Jupyter a pak prezentujte prezentace pomocí rozšíření VZESTUPU.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: c372175b-beb5-4b45-b2f8-34cb06990117
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: d29aac52948cddf9d7e274d28735e2c12143b5ca
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53081417"
---
# <a name="run-a-notebook-slideshow"></a>Spusťte Poznámkový blok prezentace

Poznámkových bloků Azure je předem nakonfigurovaný pomocí Jupyter/IPython prezentace rozšíření (VZESTUPU), který umožňuje poznámkového bloku přímo jako prezentaci. V prezentaci buňky jsou obvykle zobrazené jeden po druhém použitím velikosti písma, která je vhodná pro prezentace na velkých obrazovkách a můžete se ještě může spustit kód, spíše než přepnutí na ukázku samostatné počítače.

Zobrazení standardního Poznámkový blok, ve kterém uvidíte Markdown a buňky s kódem všechno dohromady na následujícím obrázku:

![Poznámkový blok v standardní zobrazení](media/slideshow/slideshow-notebook-view.png)

Při spuštění prezentaci první buňky se zvětší tak, aby vyplnil prohlížeče, kde **X** v levém horním rohu se ukončí prezentací, **?** v dolním levém zobrazí klávesové zkratky a šipky v pravém dolním přecházet mezi snímky:

![Poznámkový blok v režimu prezentace](media/slideshow/slideshow-slide-view.png)

Příprava poznámkového bloku pro prezentaci zahrnuje dvě primární aktivity:

1. Protože buňky Markdown se vykreslují velká písma, nemusí být zobrazeny v prezentaci nějaký obsah. Proto obvykle omezit množství textu v jakékoli dané buňky; záhlaví řádků čtyř až šesti obvykle je nejvhodnější. Pokud máte více textu, rozdělte tyto informace do více buněk.

2. Konfigurace chování každá buňka v prezentaci pomocí nástrojů buňky prezentace. Typy buňky určují chování navigačních tlačítek.

## <a name="the-anatomy-of-a-slideshow"></a>Anatomie prezentaci

Je-li provést náhodné Poznámkový blok a používat ho pro prezentaci, obvykle pro vás, že všechny buňky jsou neuspořádaná společně a většina obsahu je skrytý spodní část okna prohlížeče. Aby efektivní prezentace, musíte přiřadit typ prezentace jednotlivé buňky pomocí nástrojů buňky prezentace:

1. Na **zobrazení** nabídce vyberte možnost **buňky nástrojů** > **prezentace**:

    ![Zapnutí nástrojů prezentace buňky](media/slideshow/slideshow-view-cell-toolbar.png)

1. A **typ snímku** rozevíracího seznamu se zobrazí v pravém horním rohu buňky v poznámkovém bloku:

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
    | Poznámky | V buňce jako poznámky lektora, které se nezobrazují v prezentaci. |

1. Standardně je užitečné, chcete-li zvolit **snímků** pro každou buňku. Budete moct zprovoznit službu prezentaci a proveďte příslušné změny.

### <a name="example-fragment-cells-for-bullet-items"></a>Příklad: fragment buněk pro položky odrážky

Aby odrážky na snímku snímku hlavička v Markdownu buňku s se zobrazí po jednom, místě **snímku** zadejte pak umístit každý odrážek v buňce samostatné Markdown s **Fragment** typ:

![Příklad vytvoření více buněk Markdownu pro položky s odrážkami](media/slideshow/slideshow-fragments.png)

Protože prezentaci vykreslí fragmenty s více svislé mezery než když jsou všechny odrážek v jedné buňce, nebudete moci používat libovolný počet položek odrážky.

## <a name="run-the-slideshow"></a>Spuštění prezentaci

1. Pokud jste upravili všechny buňky Markdownu, ujistěte se, že je k vykreslení jejich HTML, jinak se zobrazují spustit *jako* Markdownu v prezentaci.

1. Po dokončení konfigurace **typ snímku** pro každou buňku, vyberte buňku, pomocí kterého se má spustit prezentaci a pak vyberte **Enter a její opuštění NARŮSTAT prezentace** tlačítko na hlavním panelu nástrojů:

    ![Zapne/ukončí NARŮSTAT prezentace tlačítko na hlavním panelu nástrojů](media/slideshow/slideshow-start.png)

1. Přecházet mezi snímky, jakož i fragmenty, použijte v ovládacím prvku navigační šipky vlevo a vpravo. Text v ovládacím prvku zobrazuje číslo představující *slide.sub snímku*.

    ![Ovládací prvek navigace prezentace](media/slideshow/slideshow-navigation-control.png)

1. Přecházet mezi snímky a dílčí snímky, jakož i fragmenty, použijte nahoru a dolů šipkami, pokud je povoleno:

    ![Ovládací prvky pro navigaci k prezentaci pro dílčí snímky](media/slideshow/slideshow-navigation-control-subslide.png)

1. Na buňky kódu použijte na tlačítko Přehrát akci pro spuštění kódu; na snímku, zobrazí se výstup:

    ![Tlačítko Přehrát akci spustíte buňku kódu](media/slideshow/slideshow-run-code-cell.png)

    ![Zobrazí se výstup buňky kódu v prezentaci](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > Výstup buňky je považováno za součást buněk v prezentaci. Pokud spustíte buňky v poznámkovém bloku nebo zobrazení prezentace, se zobrazí v ostatních zobrazeních i výstup. Pokud chcete vymazat výstup, použijte **buňky** > **aktuální výstupy** > **vymazat** příkazu (pro aktuální buňka) nebo **buňky**  >  **Všechny výstupy** > **vymazat** (pro všechny buňky).

1. Jakmile budete hotovi s prezentaci, použijte **X** se vrátíte do zobrazení poznámkového bloku.

## <a name="next-steps"></a>Další postup

- [Postupy: Konfigurace a správa projektů](configure-manage-azure-notebooks-projects.md)
- [Postupy: instalace balíčků z v rámci poznámkového bloku](install-packages-jupyter-notebook.md)
- [Postupy: práce s datovými soubory](work-with-project-data-files.md)
- [Postupy: přístup k datovým prostředkům](access-data-resources-jupyter-notebooks.md)
