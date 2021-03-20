---
title: Použití klasifikací u prostředků (Preview)
description: Tento dokument popisuje, jak použít klasifikace u assetů.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: d12a7d52562fe32126e12a844c2d36c14cf01431
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96553111"
---
# <a name="apply-classifications-on-assets-in-azure-purview"></a>Použití klasifikací u prostředků v Azure dosah

Tento článek popisuje, jak v prostředcích použít klasifikace.

## <a name="introduction"></a>Úvod

Klasifikace můžou být systémové nebo vlastní typy. Klasifikace systému jsou ve výchozím nastavení přítomny ve dosah. Vlastní klasifikace lze vytvořit na základě vzoru regulárního výrazu. Klasifikace lze u prostředků použít buď automaticky, nebo ručně.

Tento dokument vysvětluje, jak použít klasifikace pro vaše data.

## <a name="prerequisites"></a>Předpoklady

- Vytvářejte vlastní klasifikace podle potřeb.
- Nastavte kontrolu zdrojů dat.

## <a name="apply-classifications"></a>Využití klasifikací
Ve službě Azure dosah můžete použít systémové nebo vlastní klasifikace pro soubor, tabulku nebo prostředek sloupce. Tento článek popisuje postup ručního použití klasifikací na svých prostředcích.

### <a name="apply-classification-to-a-file-asset"></a>Použití klasifikace na prostředek souboru
Azure dosah dokáže kontrolovat a automaticky klasifikovat soubory dokumentace. Pokud máte například soubor s názvem *multiple.docx* a v jeho obsahu je číslo národního ID, Azure dosah přidá do stránky s podrobnostmi o prostředku klasifikační **národní identifikační číslo EU** .

V některých scénářích můžete chtít ručně přidat klasifikace do assetu souborů. Pokud máte několik souborů, které jsou seskupené do sady prostředků, přidejte klasifikace na úrovni sady prostředků.

Pomocí těchto kroků přidejte do sady prostředků oddílu vlastní nebo systémovou klasifikaci:

1. Vyhledejte nebo přejděte na oddíl a přejděte na stránku s podrobnostmi o assetu.

    :::image type="content" source="./media/apply-classifications/asset-detail-page.png" alt-text="Snímek obrazovky zobrazující stránku s podrobnostmi o assetu":::

1. Na kartě **Přehled** můžete zobrazit část **klasifikace** a zjistit, jestli existují nějaké existující klasifikace. Vyberte **Upravit**.

1. V rozevíracím seznamu **klasifikace** vyberte konkrétní klasifikace, které vás zajímají. Například **číslo platební karty**, což je systémová klasifikace a **CustomerAccountID**, což je vlastní klasifikace.

    :::image type="content" source="./media/apply-classifications/select-classifications.png" alt-text="Snímek obrazovky ukazující, jak vybrat klasifikace, které se mají přidat do assetu.":::

1. Vyberte **Uložit**.

1. Na kartě **Přehled** potvrďte, že vybrané klasifikace se zobrazí v části **klasifikace** .

    :::image type="content" source="./media/apply-classifications/confirm-classifications.png" alt-text="Snímek obrazovky ukazující, jak potvrdit klasifikace byly přidány do assetu.":::

### <a name="apply-classification-to-a-table-asset"></a>Použití klasifikace na prostředek tabulky

Když Azure dosah prohledává vaše zdroje dat, automaticky nepřiřazuje klasifikace k assetům v tabulce. Pokud chcete, aby měl váš Asset tabulky klasifikaci, je nutné jej přidat ručně.

Přidání klasifikace do assetu tabulky:

1. Najděte Asset tabulky, které vás zajímá. Například tabulka **zákazníků** .

1. Potvrďte, že tabulce nejsou přiřazeny žádné klasifikace. Vybrat **Upravit**

    :::image type="content" source="./media/apply-classifications/select-edit-from-table-asset.png" alt-text="Snímek obrazovky znázorňující, jak zobrazit a upravit klasifikace assetu tabulky":::

1. V rozevíracím seznamu **klasifikace** vyberte jednu nebo více klasifikací. V tomto příkladu se používá vlastní klasifikace s názvem **CustomerInfo**, ale můžete vybrat libovolné klasifikace pro tento krok.

    :::image type="content" source="./media/apply-classifications/select-classifications-in-table.png" alt-text="Snímek obrazovky ukazující, jak vybrat klasifikace, které se mají přidat do assetu tabulky":::

1. Vyberte **Uložit** a uložte klasifikace.

1. Na stránce **Přehled** ověřte, že Azure dosah přidal vaše nové klasifikace.

    :::image type="content" source="./media/apply-classifications/verify-classifications-added-to-table.png" alt-text="Snímek obrazovky ukazující, jak ověřit, že se klasifikace přidaly do assetu tabulky":::

### <a name="add-classification-to-a-column-asset"></a>Přidání klasifikace na prostředek sloupce

Azure dosah automaticky vyhledává a přidává klasifikace do všech prostředků sloupců. Pokud však chcete změnit klasifikaci, můžete tak učinit na úrovni sloupce.

Přidání klasifikace do sloupce:

1. Vyhledejte a vyberte prostředek sloupce a pak na kartě **Přehled** vyberte **Upravit** .

1. Vyberte kartu **schéma** .

    :::image type="content" source="./media/apply-classifications/edit-column-schema.png" alt-text="Snímek obrazovky znázorňující úpravu schématu sloupce":::

1. Identifikujte sloupce, které vás zajímají, a vyberte **Přidat klasifikaci**. Tento příklad přidá do sloupce **PasswordHash** **společná klasifikace hesel** .

    :::image type="content" source="./media/apply-classifications/add-classification-to-column.png" alt-text="Snímek obrazovky znázorňující způsob přidání klasifikace do sloupce":::

1. Vyberte **Uložit**.

1. Vyberte kartu **schéma** a potvrďte, že byla klasifikace přidána do sloupce.

    :::image type="content" source="./media/apply-classifications/confirm-classification-added.png" alt-text="Snímek obrazovky ukazující, jak ověřit, že byla klasifikace přidána do schématu sloupce":::

## <a name="impact-of-rescanning-on-existing-classifications"></a>Dopad opakované kontroly na existující klasifikace

Klasifikace se aplikují poprvé na základě kontroly sady vzorků na vašich datech a jejich porovnáním s nastavením vzoru regulárního výrazu. Pokud se v době opětovného prohledání použijí nové klasifikace, sloupec získá další klasifikace. Existující klasifikace zůstávají ve sloupci a je nutné je odebrat ručně.

## <a name="next-steps"></a>Další kroky
Informace o tom, jak vytvořit vlastní klasifikaci, najdete v tématu [Vytvoření vlastní klasifikace](create-a-custom-classification-and-classification-rule.md).