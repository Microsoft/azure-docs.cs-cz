---
title: 'Kurz: prozkoumání sad prostředků, podrobností, schémat a klasifikací v Azure dosah (Preview)'
description: V tomto kurzu se dozvíte, jak používat sady prostředků, podrobnosti o prostředcích, schémata a klasifikace.
author: animukherjee
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: c74324ebeeefeed361c0557c45a280a411effa22
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2020
ms.locfileid: "97693323"
---
# <a name="tutorial-explore-resource-sets-details-schemas-and-classifications-in-azure-purview-preview"></a>Kurz: prozkoumání sad prostředků, podrobností, schémat a klasifikací v Azure dosah (Preview)

> [!IMPORTANT]
> Služba Azure dosah je aktuálně ve verzi PREVIEW. [Doplňkové podmínky použití pro](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verze Preview Microsoft Azure zahrnují další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti.

V tomto kurzu prozkoumáte klíčové komponenty katalogu: sady prostředků, podrobnosti o prostředcích, schématech a klasifikací.

Tento kurz je *čtvrtou částí série kurzů* , ve které se seznámíte se základy správy dat v rámci datové části pomocí Azure dosah. Tento kurz sestaví na práci, kterou jste dokončili v [části 3: procházení prostředků v Azure dosah (Preview) a zobrazení jejich vydaných řádků](tutorial-browse-and-view-lineage.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Zobrazit sady prostředků.
> * Zobrazit podrobnosti o prostředku
> * Upravte schéma a přidejte klasifikace.

## <a name="prerequisites"></a>Požadavky

* Úplný [kurz: procházení assetů ve službě Azure dosah (Preview) a zobrazení jejich vydaných řádků](tutorial-browse-and-view-lineage.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="view-resource-sets"></a>Zobrazit sady prostředků

Sada prostředků je jeden objekt v katalogu, který představuje mnoho fyzických objektů v úložišti. Objekty obvykle sdílejí společné schéma a ve většině případů konvence pojmenování nebo struktura složek. Například formát data je *RRRR/MM/DD*. Další informace o sadách prostředků najdete v tématu [Principy sad prostředků](concept-resource-sets.md).

1. V Azure Portal přejděte do svého prostředku Azure dosah a vyberte **otevřít dosah Studio**. Automaticky přejdete na domovskou stránku aplikace dosah Studio.

2. Do pole **Prohledat prostředky** zadejte **contoso_staging_positivecashflow_ n** a potom ve výsledcích hledání vyberte **Contoso_Staging_PositiveCashFlow_ {n}. csv** .

## <a name="view-asset-details"></a>Zobrazit podrobnosti prostředku

1. Karta **Přehled** zobrazuje popis, **výrazy glosáře** a **vlastnosti**, jako je například **kvalifikovaný** **název**.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/overview-tab.png" alt-text="Snímek obrazovky s kartou s přehledem na stránce prostředku sady prostředků":::

1. V části **vlastnosti** si všimněte následujících dvou polí:

   * **partitionCount**: Určuje počet fyzických souborů, které jsou přidruženy k této sadě prostředků.
   * **schemaCount**: Určuje počet variant schématu, které byly nalezeny v rámci této sady prostředků.

   Azure dosah tyto vlastnosti naplní během 24 hodin po dokončení kontroly v [části 1 této série kurzů](tutorial-scan-data.md).

1. Vyberte kartu **Kontakty** a zkontrolujte hodnoty **experti** a **vlastníci** .

## <a name="edit-the-schema-and-add-classifications"></a>Úprava schématu a přidání klasifikací

1. Vyberte kartu **schéma** . Sledujte názvy sloupců a klasifikace, které jsou k nim přidružené. Všimněte si, že se automaticky naplní vlastnosti.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/schema-tab.png" alt-text="Snímek obrazovky znázorňující kartu schématu":::

1. Pokud chcete prostředek upravit, vyberte v levém horním rohu tlačítko **Upravit** . Pak vyberte kartu **schéma** .

1. Přidejte **Popis** sloupce nebo sloupec přejmenujte na výstižnější název.

1. Přidáním klasifikace na úrovni assetu vyberte rozevírací seznam **klasifikace na úrovni sloupce** pro název sloupce, který nemá klasifikaci sady.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/edit-schema.png" alt-text="Stránka pro úpravy schématu":::

1. Až skončíte s vašimi změnami, vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
>
> * Zobrazit sady prostředků.
> * Zobrazit podrobnosti o prostředku
> * Upravte schéma a přidejte klasifikace.

Přejděte k dalšímu kurzu, kde se dozvíte o glosáři a o definování a importu nových podmínek pro prostředky.

> [!div class="nextstepaction"]
> [Vytváření a import pojmů glosáře](tutorial-import-create-glossary-terms.md)