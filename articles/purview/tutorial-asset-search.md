---
title: 'Kurz: navigace na domovské stránce Azure dosah a vyhledání assetu'
description: V tomto kurzu se dozvíte, jak používat funkce na domovské stránce Azure dosah a hledat v katalogu.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 02/22/2020
ms.openlocfilehash: 5285d5b2c7cbe6845fb6043fa1096f94254602e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677829"
---
# <a name="tutorial-navigate-the-azure-purview-preview-home-page-and-search-for-an-asset"></a>Kurz: navigace na domovské stránce Azure dosah (Preview) a hledání prostředku

> [!IMPORTANT]
> Služba Azure dosah je aktuálně ve verzi PREVIEW. [Doplňkové podmínky použití pro](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verze Preview Microsoft Azure zahrnují další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti.

V tomto kurzu se seznámíte s Azure dosah tak, že přejdete na funkce domovské stránky a vyhledáte Asset v katalogu.

Tento článek je *částí 2 série kurzů* , ve které se seznámíte se základy správy dat v rámci datové části pomocí Azure dosah. Tento kurz sestaví na práci, kterou jste dokončili v [části 1: Kontrola dat pomocí Azure dosah](tutorial-scan-data.md)

V tomto kurzu:

> [!div class="checklist"]
>
> * Přejděte na domovskou stránku Azure dosah.
> * Vyhledejte Asset.
> * Přidejte vlastníka majetku.

## <a name="prerequisites"></a>Předpoklady

* Úplný [kurz: Kontrola dat pomocí Azure dosah](tutorial-scan-data.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="navigate-the-azure-purview-home-page"></a>Navigace na domovské stránce Azure dosah

Následující kroky vás provedou na domovské stránce Azure dosah.

1. V Azure Portal přejděte do svého prostředku Azure dosah a vyberte **otevřít dosah Studio**. Automaticky přejdete na domovskou stránku aplikace dosah Studio.

   V horní části domovské stránky se zobrazuje název vašeho katalogu a sada katalogových analýz. Zahrnuje počet zdrojů, datových assetů a pojmů. V souhrnu vidíte, že v části Total a 113 glosáře se nachází více než 200 prostředků. Toto číslo se aktualizuje v rámci vaší organizace a přidává další výrazy do Azure dosah.

   :::image type="content" source="./media/tutorial-asset-search/purview-home-page.png" alt-text="Snímek obrazovky, na které se zobrazuje Domovská stránka Azure dosah":::

1. Vyberte **Procházet assety** a zobrazte všechny prostředky.

## <a name="search-for-an-asset"></a>Hledání assetu

Než začnete, tady je rychlý aktualizační program pro určitou základní terminologii:

* **Asset**: jeden objekt v katalogu, který je stručný a obsahuje definici jakékoli entity v nemovitosti. Mezi příklady entit patří tabulka SQL, sestava Power BI a aktivita vaší datové továrny.
  
* **Schéma**: označuje se také jako sloupec nebo atribut, schéma představuje strukturu assetu nebo sady prostředků.

* **Sada prostředků**: jeden objekt v katalogu, který představuje mnoho fyzických objektů v úložišti. Tyto objekty obvykle sdílejí společné schéma a ve většině případů konvence pojmenování nebo struktura složek. Například formát data je *RRRR/MM/DD*. Další informace najdete v tématu [porozumění sadám prostředků](concept-resource-sets.md).

* **Typ assetu**: seskupení prostředků a sad prostředků pod logickým názvem, které se obvykle mapuje na název datové platformy.

Pomocí těchto kroků můžete vyhledat Asset a označit ho jako vlastníka:

1. V poli **katalog hledání** na domovské stránce zadejte název skupiny prostředků, která obsahuje vaši datovou sadu (skupinu prostředků, kterou jste vytvořili v předchozím kurzu). Zobrazí se seznam návrhů.

1. Vyberte **Zobrazit výsledky hledání**. Vzhledem k tomu, že všechny vaše prostředky začínají názvem vaší skupiny prostředků, všechny se zobrazí ve výsledcích hledání. Mimo tento kurz byste hledali konkrétní názvy assetů, nikoli skupiny prostředků.

    :::image type="content" source="./media/tutorial-asset-search/search-suggestions.png" alt-text="Snímek obrazovky zobrazující seznam návrhů vyhledávání v katalogu":::

1. Filtry v levém navigačním panelu můžete použít k filtrování podle typu assetu, klasifikace, kontaktu, popisku a glosáře.

1. Pokud chcete vyhledat sadu prostředků, začněte psát název sady. Zobrazí se seznam návrhů vyhledávání se správnými klíčovými slovy. Můžete také vyhledat absolutní názvy jejich vložením do uvozovek.

   :::image type="content" source="media/tutorial-asset-search/keyword-search.png" alt-text="Hledání prostředků klíčových slov Azure dosah":::

## <a name="edit-an-asset"></a>Úprava prostředku

1. Vyberte jeden z assetů z výsledků hledání. Pak vyberte **Upravit** .

1. Na kartě **Přehled** můžete přidat popis.

    :::image type="content" source="./media/tutorial-asset-search/overview-tab.png" alt-text="Snímek obrazovky s polem Popis na kartě Přehled":::

1. Vyberte kartu **Kontakty** . V poli **vlastníci** vyberte v nabídce **Vybrat uživatele nebo skupinu** zadání firemní e-mailové adresy.

    :::image type="content" source="./media/tutorial-asset-search/contacts-tab.png" alt-text="Snímek obrazovky zobrazující vyplněná pole":::

    Automaticky se zobrazí návrh názvu.

1. Vedle **expertů** v poli **Vybrat uživatele nebo skupinu** zadejte název (například název vašeho nadřízeného) a pak vyberte **Uložit**.

    Pole Popis, jméno vlastníka a název odborníka jsou nyní vyplněna.

## <a name="next-steps"></a>Další kroky

Než přejdete k dalšímu kurzu v této sérii, proveďte další čas a prozkoumejte si domovskou stránku Azure dosah sami. V tomto kurzu jste se naučili:

> [!div class="checklist"]
>
> * Přejděte na domovskou stránku Azure dosah.
> * Vyhledejte Asset.
> * Přidejte vlastníka majetku.

Přejděte k dalšímu kurzu, kde se dozvíte, jak vyhledat assety ve službě Azure dosah a zjistit vydanou řádkovou položku.

> [!div class="nextstepaction"]
> [Procházet assety a zobrazovat jejich čárové prvky](tutorial-browse-and-view-lineage.md)
