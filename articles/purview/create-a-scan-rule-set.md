---
title: Vytvoření sady pravidel prohledávání
description: Vytvořte sadu pravidel skenování ve službě Azure dosah a rychle prohledejte zdroje dat ve vaší organizaci.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: 9662652a6a40285ad382857975ec0dd04b8ba8be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96553048"
---
# <a name="create-a-scan-rule-set"></a>Vytvoření sady pravidel prohledávání

V katalogu Azure dosah můžete vytvořit sady pravidel skenování, které vám umožní rychlou kontrolu zdrojů dat ve vaší organizaci.

Sada pravidel skenování je kontejner pro seskupení sady pravidel skenování, abyste je mohli snadno přidružit k prověřování. Můžete například vytvořit výchozí sadu pravidel skenování pro každý z vašich typů zdrojů dat a pak tyto sady pravidel skenování použít ve výchozím nastavení pro všechny kontroly ve vaší společnosti. Můžete také chtít, aby uživatelé se správnými oprávněními vytvářeli jiné sady pravidel skenování s různými konfiguracemi podle potřeb podniku.

## <a name="steps-to-create-a-scan-rule-set"></a>Postup vytvoření sady pravidel skenování

Vytvoření sady pravidel skenování:

1. V katalogu Azure dosah vyberte **Centrum pro správu**.

1. V levém podokně vyberte možnost **Prohledat sady pravidel** a pak vyberte **Nová**.

1. Na stránce **Nová sada pravidel skenování** vyberte zdroje dat, které skener katalogu podporuje, z rozevíracího seznamu **typ zdroje** . Pro každý typ zdroje dat, který chcete skenovat, můžete vytvořit sadu pravidel skenování.

1. Pojmenujte pravidlo pro kontrolu a nastavte **název**. Maximální délka je 63 znaků bez povolených mezer. Volitelně zadejte **Popis**. Maximální délka je 256 znaků.

   :::image type="content" source="./media/create-a-scan-rule-set/purview-home-page.png" alt-text="Snímek obrazovky zobrazující stránku sady pravidel skenování" border="true":::

1. Vyberte **Pokračovat**.

   Zobrazí se stránka **vybrat typy souborů** . Všimněte si, že možnosti typu souboru na této stránce se liší v závislosti na typu zdroje dat, který jste zvolili na předchozí stránce. Ve výchozím nastavení jsou povolené všechny typy souborů.

      :::image type="content" source="./media/create-a-scan-rule-set/select-file-types-page.png" alt-text="Snímek obrazovky zobrazující stránku vybrat typy souborů":::

   Výběr **typů souborů dokumentů** na této stránce umožňuje zahrnout nebo vyloučit následující typy souborů Office:. doc,. DOCM,. docx,. dot,. odp,. ODS,. odt,. PDF,. pot,. PPS,. ppsx,. ppt,. pptm,. pptx,. xlc,. xlsb,. xlsm,. xlsx a. xlt.

1. Zaškrtnutím nebo zrušením zaškrtnutí políčka povolíte nebo zakážete dlaždici typu souboru. Pokud vyberete zdroj dat typu Data Lake (například Azure Data Lake Storage Gen2 nebo Azure BLOB), povolte typy souborů, pro které chcete, aby bylo schéma extrahováno a klasifikováno.

1. U určitých typů zdrojů dat můžete také [vytvořit vlastní typ souboru](#create-a-custom-file-type).

1. Vyberte **Pokračovat**.

   Zobrazí se stránka **Vybrat pravidla klasifikace** . Tato stránka zobrazuje vybraná **systémová pravidla** a **vlastní pravidla** a celkový počet vybraných pravidel klasifikace. Ve výchozím nastavení jsou zaškrtnutá políčka všechna **systémová pravidla** .

1. Pro pravidla, která chcete zahrnout nebo vyloučit, můžete zaškrtnutím nebo zrušením zaškrtnutí políček pravidla klasifikace **systémových pravidel** v kategorii použít globálně.

   :::image type="content" source="./media/create-a-scan-rule-set/select-classification-rules.png" alt-text="Snímek obrazovky zobrazující stránku vybrat pravidla klasifikace":::

1. Můžete rozbalit uzel kategorie a zaškrtnout nebo zrušit zaškrtnutí jednotlivých políček. Pokud například pravidlo pro **Argentina. dni** má vysoké falešně pozitivních hodnot, můžete toto konkrétní zaškrtávací políčko zrušit.

   :::image type="content" source="./media/create-a-scan-rule-set/select-system-rules.png" alt-text="Snímek obrazovky ukazující, jak vybrat systémová pravidla":::

1. Výběrem **vytvořit** dokončete vytváření sady pravidel skenování.

### <a name="create-a-custom-file-type"></a>Vytvoření vlastního typu souboru

Azure dosah podporuje Přidání vlastního rozšíření a definování oddělovače vlastních sloupců v sadě pravidel skenování.

Vytvoření vlastního typu souboru:

1. Postupujte podle kroků 1 – 5 v [postupu vytvoření sady pravidel skenování](#steps-to-create-a-scan-rule-set) nebo úprava existující sady pravidel skenování.

1. Na stránce **vybrat typy souborů** vyberte možnost **nový typ souboru** a vytvořte nový vlastní typ souboru.

   :::image type="content" source="./media/create-a-scan-rule-set/select-new-file-type.png" alt-text="Snímek obrazovky ukazující, jak vybrat nový typ souboru na stránce vybrat typy souborů":::

1. Zadejte **příponu souboru** a volitelný **Popis**.

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-page.png" alt-text="Snímek obrazovky s novou stránkou typ vlastního souboru" border="true":::

1. Proveďte jeden z následujících výběrů pro **obsah souborů v rámci** aplikace a zadejte typ obsahu souboru v rámci souboru:

   - Vyberte **vlastní oddělovač** a zadejte vlastní **oddělovač** (pouze jeden znak).

   - Vyberte **typ systémového souboru** a v rozevíracím seznamu **typ systémového souboru** zvolte typ systémového souboru (například XML).

1. Výběrem **vytvořit** uložte vlastní soubor.

   Systém vrátí na stránku **vybrat typy souborů** a vloží nový vlastní typ souboru jako novou dlaždici.

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-tile.png" alt-text="Snímek obrazovky s ikonou nového vlastního typu souboru na stránce vybrat typy souborů":::

1. Pokud chcete změnit nebo odstranit, vyberte možnost **Upravit** v dlaždici typ nového souboru.

1. Vyberte **pokračovat** a dokončete konfiguraci sady pravidel skenování.

## <a name="system-scan-rule-sets"></a>Sady pravidel kontroly systému

Sady pravidel kontroly systému jsou sady pravidel skenování definované společností Microsoft, které se automaticky vytvoří pro každý katalog Azure dosah. Každá sada pravidel kontroly systému je přidružená k určitému typu zdroje dat. Když vytvoříte kontrolu, můžete ji přidružit k sadě pravidel nástroje pro kontrolu systému. Pokaždé, když Microsoft provede aktualizaci těchto sad pravidel systému, můžete je aktualizovat ve vašem katalogu a použít aktualizaci na všechny související kontroly.

1. Chcete-li zobrazit seznam sad pravidel kontroly systému, vyberte možnost **Prohledat sady pravidel** v **centru pro správu** a klikněte na kartu **systém** .

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg" alt-text="Snímek obrazovky zobrazující seznam sad pravidel kontroly systému" lightbox="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg":::

1. Každá sada pravidel kontroly systému má **název**, **typ zdroje** a **verzi**. Pokud vyberete číslo verze sady pravidel skenování ve sloupci **verze** , zobrazí se pravidla přidružená k aktuální verzi a k předchozím verzím (pokud existují).

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg" alt-text="Snímek obrazovky zobrazující stránku sady pravidel kontroly systému" lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg":::

1. Pokud je k dispozici aktualizace pro sadu pravidel kontroly systému, můžete ve sloupci **verze** vybrat možnost **aktualizovat** . Na stránce pravidlo kontroly systému vyberte z rozevíracího seznamu **vybrat novou verzi, kterou chcete aktualizovat** , z verze. Stránka poskytuje seznam pravidel klasifikace systému přidružených k nové verzi a aktuální verzi.

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg" alt-text="Snímek obrazovky ukazující, jak změnit verzi sady pravidel kontroly systému" lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg":::

### <a name="associate-a-scan-with-a-system-scan-rule-set"></a>Přidružení kontroly k sadě pravidel kontroly systému

Při [vytváření prověřování](tutorial-scan-data.md#scan-data-into-the-catalog)se můžete rozhodnout, že ho přidružíte k sadě pravidel nástroje pro kontrolu systému, jak je znázorněno níže:

1. Na stránce **Vyberte sadu pravidel skenování** vyberte sadu pravidel kontroly systému.

   :::image type="content" source="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg" alt-text="Snímek obrazovky ukazující, jak vybrat sadu pravidel kontroly systému pro kontrolu." lightbox="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg":::

1. Vyberte **pokračovat** a pak vyberte **Uložit a spustit**.
