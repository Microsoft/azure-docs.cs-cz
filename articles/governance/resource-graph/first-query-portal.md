---
title: 'Rychlý Start: váš první dotaz na portálu'
description: V tomto rychlém startu budete postupovat podle pokynů ke spuštění prvního dotazu z Azure Portal pomocí Průzkumníka Azure Resource Graph.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom:
- mode-portal
ms.openlocfilehash: 07e336121c6338b27c018acb5b2332653663e1b4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533088"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-resource-graph-explorer"></a>Rychlý Start: spuštění prvního dotazu na graf prostředku pomocí Průzkumníka Azure Resource graphu

Výkon Azure Resource graphu je k dispozici přímo v Azure Portal prostřednictvím Průzkumníka Azure Resource graphu. Průzkumník prostředků Resource Explorer poskytuje procházetelné informace o Azure Resource Manager typech prostředků a vlastnostech, které se dají dotazovat. Průzkumník diagramů prostředků také poskytuje čisté rozhraní pro práci s více dotazy, vyhodnocení výsledků a dokonce i převod výsledků některých dotazů do grafu, který je možné připnout na řídicí panel Azure.

Na konci tohoto rychlého startu použijete Azure Portal a Průzkumník diagramů prostředků ke spuštění prvního dotazu grafu prostředku a připnuté výsledky na řídicí panel.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="run-your-first-resource-graph-query"></a>Spusťte nejdříve dotaz na Resource Graph použitím Azure CLI

Otevřete [Azure Portal](https://portal.azure.com) pro vyhledání a použití Průzkumníka grafu prostředků podle těchto kroků ke spuštění prvního dotazu na graf prostředku:

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Průzkumník diagramů prostředků**.

1. V části **dotazu 1** v okně zadejte dotaz `Resources | project name, type | limit 5` a vyberte **Spustit dotaz**.

   > [!NOTE]
   > Jelikož tento příklad dotazu neposkytuje modifikátor řazení, jako je `order by` spuštění tohoto dotazu vícekrát, je pravděpodobně výsledkem jiné sady prostředků na žádost.

1. Přečtěte si odpověď na dotaz na kartě **výsledky** . Výběrem karty **zprávy** zobrazíte podrobnosti o dotazu, včetně počtu výsledků a doby trvání dotazu. Případné chyby se zobrazí na této kartě.

1. Aktualizujte dotaz na `order by` vlastnost **Name** : `Resources | project name, type | limit 5 | order by name asc` . Pak vyberte **Spustit dotaz**.

   > [!NOTE]
   > Stejně jako u prvního dotazu opakované spouštění tohoto dotazu pravděpodobně poskytne jinou sadu zdrojů na jednu žádost. Pořadí příkazů dotazů je důležité. V tomto příkladu `order by` přichází po `limit`. Toto pořadí příkazů nejprve omezí výsledky dotazu a pak je vyřadí.

1. Aktualizujte dotaz na první `order by` vlastnost **Name** a pak `limit` na horních pět výsledků: `Resources | project name, type | order by name asc | limit 5` . Pak vyberte **Spustit dotaz**.

Když se konečný dotaz několikrát spustí, předpokládá se, že se nic ve vašem prostředí nemění, vrácené výsledky jsou konzistentní a seřazené podle vlastnosti **Name** , ale pořád se omezí na pět nejlepších výsledků.

### <a name="schema-browser"></a>Prohlížeč schémat

Prohlížeč schématu je umístěný v levém podokně Průzkumníka grafu prostředků. Tento seznam prostředků zobrazuje všechny _typy_ prostředků Azure, které podporuje Azure Resource Graph a které existují v tenantovi, ke kterému máte přístup. Rozbalením typu prostředku nebo podvlastnostmi se zobrazí podřízené vlastnosti, které lze použít k vytvoření dotazu grafu prostředku.

Výběr typu prostředku umístí `where type =="<resource type>"` do pole dotazu. Výběrem jedné z podřízených vlastností přidáte `where <propertyName> == "INSERT_VALUE_HERE"` do pole dotazu.
Prohlížeč schémat je skvělým způsobem, jak zjistit vlastnosti pro použití v dotazech. Nezapomeňte nahradit _vloženou \_ hodnotu \__ vlastní hodnotou, upravit dotaz pomocí podmínek, operátorů a funkcí, abyste dosáhli zamýšlených výsledků.

## <a name="create-a-chart-from-the-resource-graph-query"></a>Vytvoření grafu z dotazu na graf prostředků

Pokud po spuštění předchozího dotazu vyberete kartu **grafy** , zobrazí se zpráva, že sada výsledků dotazu není kompatibilní s vizualizací výsečového grafu. Dotazy, které uvádějí výsledky seznamu, se nedají provést v grafu, ale můžou se zadat i dotazy, které poskytují počty prostředků. Pomocí [ukázkového dotazu – počet virtuálních počítačů podle typu operačního systému](./samples/starter.md#count-os)vytvoříme vizualizaci z dotazu na graf prostředků.

1. V části pro **dotaz 1** v okně zadejte následující dotaz a vyberte **Spustit dotaz**.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Vyberte kartu **výsledky** a Všimněte si, že odpověď pro tento dotaz poskytuje počty.

1. Vyberte kartu **grafy** . Nyní je výsledkem dotazu vizualizace. Změňte typ z volby _typ grafu.._ . na _sloupcový graf_ nebo _prstencový graf_ pro experimentování s dostupnými možnostmi vizualizace.

## <a name="pin-the-query-visualization-to-a-dashboard"></a>Připnutí vizualizace dotazu na řídicí panel

Když máte výsledky z dotazu, který je možné vizuálně vyfiltrovat, můžete tuto vizualizaci dat připnout k některému z vašich řídicích panelů. Po spuštění předchozího dotazu proveďte tyto kroky:

1. Vyberte **Uložit** a zadejte název virtuální počítače podle typu operačního systému. Potom v dolní části pravého podokna vyberte **Save (Uložit** ).

1. Výběrem **Spustit dotaz** spusťte dotaz nyní, který je uložený.

1. Na kartě **grafy** vyberte vizualizaci dat. Pak vyberte **Připnout na řídicí panel**.

1. Buď vyberte oznámení na portálu, které se zobrazí, nebo v levém podokně vyberte **řídicí panel** .

Dotaz je nyní k dispozici na řídicím panelu s názvem dlaždice, která odpovídá názvu dotazu. Pokud byl dotaz při připnutí neuložený, nazývá se místo toho dotaz 1.

Dotaz a výsledná vizualizace dat se spustí a aktualizuje pokaždé, když se řídicí panel načte, a poskytuje v reálném čase a dynamické poznatky k prostředí Azure přímo ve vašem pracovním postupu.

> [!NOTE]
> Dotazy, které jsou výsledkem seznamu, lze také připnout na řídicí panel. Tato funkce není omezena na vizualizace dat dotazů.

## <a name="import-example-resource-graph-explorer-dashboards"></a>Příklady importu řídicích panelů Průzkumníka diagramů prostředků

Chcete-li poskytnout příklady dotazů na grafy prostředků a jak lze použít Průzkumníka grafu prostředků k vylepšení Azure Portalho pracovního postupu, vyzkoušejte tyto ukázkové řídicí panely.

- [Průzkumník grafu prostředků – ukázkový #1 řídicího panelu](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-1/resourcegraphexplorer-sample-1.json)

  :::image type="content" source="./media/arge-sample1-small.png" alt-text="Příklad obrázku pro ukázkový řídicí panel #1" lightbox="./media/arge-sample1-large.png":::

- [Průzkumník grafu prostředků – ukázkový #2 řídicího panelu](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-2/resourcegraphexplorer-sample-2.json)

  :::image type="content" source="./media/arge-sample2-small.png" alt-text="Příklad obrázku pro ukázkový řídicí panel #2" lightbox="./media/arge-sample2-large.png":::

> [!NOTE]
> Počty a grafy ve výše uvedených ukázkových snímcích obrazovky řídicího panelu se liší v závislosti na prostředí Azure.

1. Vyberte a Stáhněte si ukázkový řídicí panel, který chcete vyhodnotit.

1. V Azure Portal v levém podokně vyberte **řídicí panel** .

1. Vyberte **nahrát** a pak vyhledejte a vyberte stažený ukázkový soubor řídicího panelu. Pak vyberte **otevřít**.

Automaticky se zobrazí importovaný řídicí panel. Protože teď existuje ve vašem Azure Portal, můžete podle potřeby prozkoumat a dělat změny nebo vytvořit nové řídicí panely z tohoto příkladu a sdílet je s vašimi týmy. Další informace o práci s řídicími panely najdete v tématu [Vytvoření a sdílení řídicích panelů v Azure Portal](../../azure-portal/azure-portal-dashboards.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat ukázkové řídicí panely grafu prostředků z prostředí Azure Portal, můžete to udělat pomocí následujících kroků:

1. V levém podokně vyberte **řídicí panel** .

1. V rozevíracím seznamu řídicí panel vyberte ukázkový řídicí panel grafu prostředků, který chcete odstranit.

1. V nabídce řídicího panelu v horní části řídicího panelu vyberte **Odstranit** a potvrďte ji kliknutím na **OK** .

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili Azure Resource Graph Explorer ke spuštění prvního dotazu a prohlédli si příklady řídicích panelů, které jsou napájené podle grafu zdrojů. Chcete-li získat další informace o jazyku grafu prostředků, přejděte na stránku podrobností dotazovacího jazyka.

> [!div class="nextstepaction"]
> [Získat další informace o dotazovacím jazyku](./concepts/query-language.md)
