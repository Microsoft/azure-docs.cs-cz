---
title: 'Kurz: vytvoření a import podmínek glosáře v Azure dosah (Preview)'
description: V tomto kurzu se dozvíte, jak vytvořit Glosář pojmů, přidat do assetu výrazy glosáře a importovat terminologie glosáře.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 9443c6fbaca16cf075745972a1655a2b4b2ea43c
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077529"
---
# <a name="tutorial-create-and-import-glossary-terms-in-azure-purview-preview"></a>Kurz: vytvoření a import podmínek glosáře v Azure dosah (Preview)

> [!IMPORTANT]
> Služba Azure dosah je aktuálně ve verzi PREVIEW. [Doplňkové podmínky použití pro](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verze Preview Microsoft Azure zahrnují další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti.

Glosář je důležitý nástroj pro údržbu a organizování katalogu. Svůj Glosář sestavíte tak, že definujete nové podmínky nebo importujete seznam termínů a pak tyto podmínky použijete pro vaše prostředky.

V tomto kurzu je třetí *částí kurzu* , ve kterém se seznámíte se základy správy řízení dat napříč datovou řadou pomocí Azure dosah. Tento kurz sestaví na práci, kterou jste dokončili v [části 4: Prozkoumejte sady prostředků, podrobnosti, schémata a klasifikace v Azure dosah (Preview)](tutorial-schemas-and-classifications.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vytvořte pojem glosáře.
> * Přidejte do assetu výrazy glosáře.
> * Importujte pojem Glosář.

## <a name="prerequisites"></a>Požadavky

* Úplný [kurz: Prozkoumejte sady prostředků, podrobnosti, schémata a klasifikace v Azure dosah (Preview)](tutorial-schemas-and-classifications.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="create-glossary-terms"></a>Vytváření termínů glosáře

V glosáři můžete vytvořit obchodní nebo technické výrazy. Prostředky můžete také opatřit poznámkami, a to použitím podmínek.

Tady je souhrn některých nejběžnějších polí na stránce **pojem Glosář** :

* **Stav**: přiřaďte stav termínu (**koncept**, **schváleno**, **platnost** nebo **Výstraha**).

* **Definice**: zadejte definici podmínky.

* **Akronym**: zadejte zkrácenou verzi termínu s použitím počátečních písmen každého slova.

* **Synonyma**: vyberte jiné výrazy se stejnými nebo podobnými definicemi.

* **Související výrazy**: v glosáři vyberte jiné výrazy, které se vztahují k tomuto, ale mají různé definice. Příklady jsou technické podmínky, které souvisejí s obchodním termínem, názvem kódu nebo jinými podmínkami, které by měly být spojeny s termínem.

Vytvořte Glosář pomocí následujících kroků:

1. V Azure Portal přejděte do svého prostředku Azure dosah a vyberte **otevřít dosah Studio**. Automaticky přejdete na domovskou stránku aplikace dosah Studio.

1. V levém podokně vyberte **Glosář** a otevřete stránku **glosářové výrazy** .

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/glossary-terms-page.png" alt-text="Snímek obrazovky zobrazující stránku pojmů glosáře":::

1. Vyberte možnost **nové**  >  **Výchozí označení systému**  >  .

1. Na kartě **Přehled** na stránce **nový termín** zadejte **název** nového termínu: *finanční*.

1. Zadejte **definici**: *Tento pojem představuje finanční informace pro společnost Contoso Inc.*

1. V rozevíracím seznamu **stav** vyberte **schváleno**.

1. Až budete hotovi, vyberte **vytvořit**.

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/enter-new-glossary-term.png" alt-text="Snímek obrazovky ukazující, jak vytvořit nový termín glosáře.":::

## <a name="add-glossary-terms-to-an-asset"></a>Přidat do assetu výrazy glosáře

1. K vyhledání assetu použijte postup, který jste zjistili v [části 1 této série kurzů](tutorial-scan-data.md) . Například **Contoso_CashFlow_ {N}. csv**.

1. Na stránce Podrobnosti o aktivech vyberte **Upravit**.

1. V rozevíracím seznamu **pojem Glosář** na kartě **Přehled** vyberte **finance** a pak vyberte **Uložit**.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-glossary-term-to-asset.png" alt-text="Snímek obrazovky znázorňující, jak přidat Glosář termínu k assetu.":::

1. V části **podmínky glosáře** na kartě **Přehled** si všimněte, že se u Assetu použil termín *finanční* údaj.

## <a name="import-glossary-terms"></a>Importovat terminologie glosáře

Pokud chcete importovat výrazy nebo aktualizovat existující výrazy hromadně, nahrajte do glosáře předem vyplněnou šablonu.

V tomto postupu importujete pojem glosáře prostřednictvím souboru. CSV:

1. Všimněte si, kam jste uložili soubor s názvem *StarterKitTerms.csv*, který je součástí startovní sady, kterou jste si stáhli v [části 1 této série kurzů](tutorial-scan-data.md).

   Tento soubor obsahuje seznam předem vyplněných podmínek, které jsou relevantní pro vaši datovou nemovitost.

1. Pokud chcete začít s importem, vyberte **Glosář** a pak vyberte **importovat výrazy**.

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/import-glossary-terms-select.png" alt-text="Snímek obrazovky, který ukazuje, jak importovat terminologie glosáře.":::

1. Na stránce **importovat výrazy** vyberte **výchozí systémové nastavení** a pak vyberte **pokračovat**.

1. Vyberte **Procházet**, přejděte do umístění, kam jste stáhli *StarterKitTerms.csv*, a pak vyberte **otevřít**.

1. Výběrem **OK** začněte s importem podmínek.

   Po dokončení importu se na stránce **Glosář** pojmů zobrazí nové terminologické výrazy.

1. Zobrazte si všechny nově importované výrazy, abyste viděli, jak jsou definované.

## <a name="create-custom-term-templates"></a>Vytváření vlastních šablon termínů

V této části se dozvíte, jak vytvořit vlastní šablonu s vlastními atributy a importovat data pomocí souboru CSV šablony.

Existuje několik existujících šablon systémových termínů, které můžete zobrazit výběrem **Glosář**  >  **spravovat systém šablon termínů**  >  .

:::image type="content" source="./media/tutorial-import-create-glossary-terms/system-term-templates.png" alt-text="Šablony systémových termínů.":::

Chcete-li vytvořit novou vlastní šablonu termínu, proveďte následující kroky:

1. V nabídce na levé straně vyberte **Glosář** .
1. Výběr **možnosti Spravovat šablony termínů**  >  **vlastní**  >  **Nová šablona termínů**

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-new-custom-term-template.png" alt-text="screate novou vlastní šablonu term.":::

Na obrazovce **Nová šablona termínu** proveďte následující kroky:

1. Zadejte **název šablony**: `Sensitivity level` .
1. Zadejte požadovaný popis, například `Indicates the level of sensitivity for this data.`
1. Vyberte **+ Nový atribut** a otevřete dialogové okno pro přidání atributů.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/new-term-template-screen-start.png" alt-text="Spustí se nová obrazovka šablony termínu.":::

1. Na obrazovce **Nový atribut** zadejte následující parametry:

   |Nastavení|Navrhovaná hodnota|
   |---------|-----------|
   |Název atributu |jsou citlivé informace|
   |Typ pole | Jedna volba|
   |Označit jako povinné | Zaškrtněte toto políčko.|
   |+ Přidat volbu | Přidejte dvě možnosti. "Ano" a "ne".|

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-new-attribute.png" alt-text="Přidejte nový atribut.":::

1. Zopakováním předchozích kroků přidejte další atribut s názvem `can be shared externally` . Po přidání obou atributů nakonec vyberte **vytvořit**.

## <a name="import-terms-from-a-template"></a>Import podmínek ze šablony

V dalším kroku naimportujete nový termín pomocí šablony **úrovně citlivosti** , kterou jste vytvořili. 

1. Na obrazovce **výrazy glosáře** vyberte **importovat výrazy**.

1. Na obrazovce **Import podmínek** vyberte **úroveň citlivost** . Vyberte **Pokračovat**.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/select-sensitivity-level.png" alt-text="Vyberte úroveň citlivosti.":::

1. Pojem šablona pro **úroveň citlivosti** obsahuje výchozí systémové atributy a také nové atributy, které jste přidali: `can be shared externally` a `is sensitive information` . Vyberte **Stáhnout ukázku. Soubor CSV** .

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/download-sample-csv-file.png" alt-text="Stáhněte si ukázkový soubor CSV.":::

1. Stáhne se soubor šablony, abyste mohli upravit a nahrát nový pojem Glosář s atributy zákazníka. Otevřete soubor CSV, který jste stáhli. Přidejte nové a příslušné hodnoty jako nové řádky do souboru CSV.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-term-in-csv.png" alt-text="Vytvoří termín v souboru CSV.":::

   Jakékoli výrazy uvedené ve sloupci **související výrazy** nebo **synonyma** , které ještě neexistují, budou vytvořeny při odeslání souboru. Budou vytvořeny pomocí **výchozí systémové** šablony.

1. Pokud chcete nahrát soubor, vraťte se na obrazovku **Import podmínek** a vyberte **Procházet** vedle pole **Vybrat dokončeno. Soubor CSV, který se má nahrát** . V dialogovém okně, které se otevře, vyberte soubor a pak vyberte **OK**.

1. Nové výrazy jsou teď uvedené na obrazovce **Glosář pojmů** . Kliknutím na název termínu v seznamu si můžete zobrazit podrobnosti o nových termínech.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
>
> * Vytvořte pojem glosáře.
> * Přidejte do assetu výrazy glosáře.
> * Importujte pojem Glosář.

V dalším článku se dozvíte o různých přehledech katalogu.

> [!div class="nextstepaction"]
> [Principy přehledů v Azure Purview](concept-insights.md)
