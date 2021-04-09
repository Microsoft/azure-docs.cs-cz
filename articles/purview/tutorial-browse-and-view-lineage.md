---
title: 'Kurz: procházení prostředků v Azure dosah a zobrazení jejich vydaných řádků'
description: V tomto kurzu se dozvíte, jak vyhledat prostředky v katalogu a zobrazit datové řádky.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 7ffbe2ded44ded4f580655f6ae9e98391490f94a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97696102"
---
# <a name="tutorial-browse-assets-in-azure-purview-preview-and-view-their-lineage"></a>Kurz: procházení assetů ve službě Azure dosah (Preview) a zobrazení jejich vydaných řádků

> [!IMPORTANT]
> Služba Azure dosah je aktuálně ve verzi PREVIEW. [Doplňkové podmínky použití pro](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verze Preview Microsoft Azure zahrnují další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti.

V tomto kurzu se dozvíte, jak procházet assety v Azure dosah a zobrazit jejich důležité podrobnosti, jako je například řádek.

V tomto kurzu je *třetí částí kurzu* , ve kterém se seznámíte se základy správy řízení dat napříč datovou řadou pomocí Azure dosah. Tento kurz sestaví na práci, kterou jste dokončili v [části 2: navigace na domovské stránce Azure dosah (Preview) a hledání prostředku](tutorial-asset-search.md).

V tomto kurzu:

> [!div class="checklist"]
>
> * Vyhledejte prostředky v katalogu.
> * Zobrazení počtu assetů.

## <a name="prerequisites"></a>Požadavky

* Úplný [kurz: přejděte na domovskou stránku Azure dosah (Preview) a vyhledejte Asset](tutorial-asset-search.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="browse-for-assets-in-the-catalog"></a>Vyhledat prostředky v katalogu

V předchozím kurzu jste zjistili, jak vám hledání může pomáhat při zjišťování prostředků v katalogu Azure dosah. Dalším způsobem, jak zjistit prostředky v katalogu, je použití prostředí pro procházení katalogu.

V této části se dozvíte, jak procházet katalog Azure dosah.

1. V Azure Portal přejděte do svého prostředku Azure dosah a vyberte **otevřít dosah Studio**. Automaticky přejdete na domovskou stránku aplikace dosah Studio.

1. Na **domovské** stránce vyberte **Procházet assety**.

   Zobrazí se stránka **Procházet prostředky** , která zobrazuje souhrn všech typů assetů ve vašem katalogu.

1. Pokud chcete prozkoumat různé Azure Data Lake Gen2 typů, které jsou k dispozici ve vašem katalogu, vyberte dlaždici **Azure Data Lake Gen2** .

   :::image type="content" source="./media/tutorial-browse-and-view-lineage/browse-by-asset-type.png" alt-text="Snímek obrazovky se stránkou pro procházení podle typu assetu s vybraným Azure Data Lake Gen2":::

1. Pokud existuje více prostředků, můžete vybrat záhlaví sloupce **název** k seřazení prostředků abecedně. V tomto kurzu je k dispozici pouze jeden Azure Data Lake Storage Gen2 Asset.

1. Vyberte název assetu.

1. Vyberte Contoso_GrossProfit_ sadu prostředků **{N}. SSV** . Pokud tento prostředek v katalogu neexistuje, vyberte jiný.

   :::image type="content" source="media/tutorial-browse-and-view-lineage/view-adls-assets.png" alt-text="Seznam prostředků Azure Data Lake Storage Gen2":::

## <a name="view-the-lineage-of-assets"></a>Zobrazit počet assetů

Na stránce Podrobnosti o aktivech prozkoumejte zdroj dat.

1. Vyberte kartu  Contoso_GrossProfit_ sady prostředků **{N}. SSV** .

   Zobrazí se Asset, který jste vybrali. Informace o zobrazených řádcích ukazují, že se tato sada prostředků zkopírovala z účtu služby Azure Blob Storage do Azure Data Lake Storage Gen2.

   :::image type="content" source="./media/tutorial-browse-and-view-lineage/view-lineage.png" alt-text="Snímek obrazovky znázorňující zobrazení řádku pro daný prostředek":::

1. Na této stránce vyberte Asset objektů BLOB a vyberte odkaz **Přejít na prostředek** .

   Všimněte si, že okno přešlo do sady prostředků Azure Blob, což byl zdroj původní Azure Data Lake Storage Gen2.

1. Vyberte kartu **Přehled** a prozkoumejte Asset a potvrďte jeho podrobnosti.

Informace o tom, jak vytvořit aktivitu Azure Data Factory toku dat mezi objektem blob Azure a sadou prostředků Azure Data Lake Storage Gen2 a sledovat, najdete v tématu [Azure Data Factory aktivity toku dat](../data-factory/concepts-data-flow-overview.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
>
> * Vyhledejte prostředky v katalogu.
> * Zobrazení počtu assetů.

Přejděte k dalšímu kurzu, kde se dozvíte o sadách prostředků, podrobnostech o prostředcích, schématech a klasifikacích.

> [!div class="nextstepaction"]
> [Sady prostředků, podrobnosti o prostředcích, schématech a klasifikací](tutorial-schemas-and-classifications.md)
