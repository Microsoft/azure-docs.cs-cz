---
title: 'Úvodní příručka: První portálový dotaz'
description: V tomto rychlém startu postupujte podle pokynů ke spuštění prvního dotazu z webu Azure Portal pomocí Průzkumníka prostředků Azure.
ms.date: 11/21/2019
ms.topic: quickstart
ms.openlocfilehash: 5cf355e78ad51e06d7ba27d48dd352f35b4c0740
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "74406796"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-resource-graph-explorer"></a>Úvodní příručka: Spuštění prvního dotazu na graf prostředků pomocí Průzkumníka prostředků Azure

Výkon Azure Resource Graph je k dispozici přímo na portálu Azure prostřednictvím Azure Resource Graph Explorer. Průzkumník grafů prostředků poskytuje informace o typech prostředků a vlastnostech Azure Resource Manager, které můžete dotazovat. Průzkumník grafů prostředků také poskytuje čisté rozhraní pro práci s více dotazy, vyhodnocení výsledků a dokonce i převod výsledků některých dotazů do grafu, který lze připnout na řídicí panel Azure.

Na konci tohoto rychlého startu budete používat portál Azure a Průzkumník uprostředků ke spuštění prvního dotazu na Graf prostředků a připnou výsledky na řídicí panel.

## <a name="prerequisites"></a>Požadavky

Pokud nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet, než začnete.

## <a name="run-your-first-resource-graph-query"></a>Spusťte nejdříve dotaz na Resource Graph použitím Azure CLI

Otevřete [portál Azure](https://portal.azure.com) a vyhledejte a použijte Průzkumník a průzkumník prostředků podle následujících kroků ke spuštění prvního dotazu na Graf prostředků:

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Průzkumník a průzkumník zdrojů**.

1. V části **okna Dotaz 1** zadejte `Resources | project name, type | limit 5` dotaz a vyberte **Spustit dotaz**.

   > [!NOTE]
   > Vzhledem k tomu, že tento příklad `order by`dotazu neposkytuje modifikátor řazení, například , spuštění tohoto dotazu vícekrát pravděpodobně přinese jinou sadu prostředků na požadavek.

1. Zkontrolujte odpověď na dotaz na **Messages** kartě **Výsledky.** Případné chyby jsou zobrazeny pod touto záložkou.

1. Aktualizujte dotaz `order by` na vlastnost `Resources | project name, type | limit 5 | order by name asc` **Name:** . Potom vyberte **Spustit dotaz**.

   > [!NOTE]
   > Stejně jako u prvního dotazu opakované spouštění tohoto dotazu pravděpodobně poskytne jinou sadu zdrojů na jednu žádost. Pořadí příkazů dotazů je důležité. V tomto příkladu `order by` přichází po `limit`. Tak se nejdřív omezí rozsah výsledků dotazu a ty se pak seřadí.

1. Aktualizujte dotaz `order by` nejprve **Name** `limit` vlastnost a potom `Resources | project name, type | order by name asc | limit 5`na prvních pět výsledků: . Potom vyberte **Spustit dotaz**.

Při spuštění konečného dotazu několikrát, za předpokladu, že nic ve vašem prostředí se mění, vrácené výsledky jsou konzistentní a podle očekávání – seřazené **Name** vlastnost, ale stále omezena na prvních pět výsledků.

### <a name="schema-browser"></a>Prohlížeč schématu

Prohlížeč schématu je umístěn v levém podokně Průzkumníka grafů prostředků. Tento seznam prostředků zobrazuje všechny _typy prostředků_ Azure prostředků, které jsou podporovány Azure Resource Graph a které existují v tenantovi, ke kterému máte přístup. Rozbalení typu prostředku nebo podvlastností zobrazit podřízené vlastnosti, které lze použít k vytvoření dotazu grafu prostředků.

Výběr typu prostředku `where type =="<resource type>"` se umístí do pole dotazu. Výběrem jedné z podřízených vlastností přidáte `where <propertyName> == "INSERT_VALUE_HERE"` do pole dotazu.
Prohlížeč schématu je skvělý způsob, jak zjistit vlastnosti pro použití v dotazech. Nezapomeňte nahradit _INSERT\_\_VALUE HERE_ vlastní hodnotou, upravit dotaz s podmínkami, operátory a funkcemi pro dosažení zamýšlených výsledků.

## <a name="create-a-chart-from-the-resource-graph-query"></a>Vytvoření grafu z dotazu Graf zdrojů

Po spuštění posledního výše uvedeného dotazu, pokud vyberete kartu **Grafy,** zobrazí se zpráva, že "sada výsledků není kompatibilní s vizualizací výsečového grafu". Dotazy, které uvádějí výsledky, nelze provést do grafu, ale dotazy, které poskytují počty prostředků, mohou. Pomocí [ukázkového dotazu – spočítejte virtuální počítače podle typu operačního režimu](./samples/starter.md#count-virtual-machines-by-os-type), pojďme vytvořit vizualizaci z dotazu Graf prostředků.

1. V části **okna Dotaz 1** zadejte následující dotaz a vyberte Spustit **dotaz**.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Vyberte kartu **Výsledky** a všimněte si, že odpověď na tento dotaz obsahuje počty.

1. Vyberte kartu **Grafy.** Nyní má dotaz za následek vizualizace. Chcete-li experimentovat s dostupnými možnostmi vizualizace, změňte typ z _možnosti Vybrat typ grafu..._ na _pruhový_ nebo _domatickový graf._

## <a name="pin-the-query-visualization-to-a-dashboard"></a>Připnutí vizualizace dotazu na řídicí panel

Pokud máte výsledky z dotazu, který lze vizualizovat, lze tuto vizualizaci dat připnout k jednomu z řídicích panelů. Po spuštění výše uvedeného dotazu postupujte takto:

1. Vyberte **Uložit** a zadejte název "Virtuální ho podle typu operačního serveru". Pak vdolníčásti pravého podokna vyberte **Uložit.**

1. Výběrem **možnosti Spustit dotaz** znovu spusťte dotaz, když byl uložen.

1. Na kartě **Grafy** vyberte vizualizaci dat. Pak vyberte **Připnout na řídicí panel**.

1. Vyberte oznámení portálu, které se zobrazí, nebo v levém podokně vyberte **řídicí panel.**

Dotaz je nyní k dispozici na řídicím panelu s názvem dlaždice odpovídající názvu dotazu. Pokud dotaz nebyl uložen, když byl připnutý, místo toho se nazývá Dotaz 1.

Dotaz a výsledná vizualizace dat se spouštějí a aktualizují při každém načtení řídicího panelu a poskytují přehledy v reálném čase do prostředí Azure přímo ve vašem pracovním postupu.

> [!NOTE]
> Dotazy, které mají za následek seznam lze také připnout na řídicí panel. Tato funkce není omezena na vizualizace dat dotazů.

## <a name="import-example-resource-graph-explorer-dashboards"></a>Import příkladů řídicích panelů Průzkumníka grafů prostředků

Chcete-li poskytnout příklady dotazů na graf prostředků a jak lze použít Aplikaci pro graf prostředků k vylepšení pracovního postupu portálu Azure, vyzkoušejte tyto ukázkové řídicí panely.

- [Průzkumník grafů prostředků – ukázkový řídicí panel #1](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-1/resourcegraphexplorer-sample-1.json)

  [![Ukázkový obrázek ukázkového #1 řídicího panelu](./media/arge-sample1-small.png)](./media/arge-sample1-large.png#lightbox)

- [Průzkumník grafů prostředků – ukázkový řídicí panel #2](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-2/resourcegraphexplorer-sample-2.json)

  [![Ukázkový obrázek ukázkového #2 řídicího panelu](./media/arge-sample2-small.png)](./media/arge-sample2-large.png#lightbox)

> [!NOTE]
> Počty a grafy na výše uvedených ukázkových snímcích obrazovky řídicího panelu se budou lišit v závislosti na vašem prostředí Azure.

1. Vyberte a stáhněte ukázkový řídicí panel, který chcete vyhodnotit.

1. Na webu Azure Portal vyberte **řídicí panel** v levém podokně.

1. Vyberte **Nahrát**, pak vyhledejte a vyberte stažený ukázkový soubor řídicího panelu. Pak vyberte **Otevřít**.

Importovaný řídicí panel se zobrazí automaticky. Vzhledem k tomu, že teď existuje na vašem portálu Azure, můžete prozkoumat a provést změny podle potřeby nebo vytvořit nové řídicí panely z příkladu pro sdílení s týmy. Další informace o práci s řídicími panely najdete [v tématu Vytváření a sdílení řídicích panelů na webu Azure Portal](../../azure-portal/azure-portal-dashboards.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat ukázkové řídicí panely grafu prostředků z prostředí portálu Azure, můžete tak učinit pomocí následujících kroků:

1. V levém podokně vyberte **Řídicí panel.**

1. V rozevíracím seznamu řídicího panelu vyberte ukázkový řídicí panel Grafu prostředků, který chcete odstranit.

1. Z nabídky řídicího panelu v horní části řídicího panelu vyberte **Odstranit** a kliknutím na ok vyberte **Ok.**

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili Azure Resource Graph Explorer ke spuštění prvního dotazu a podíval se na příklady řídicího panelu založené na Resource Graph. Další informace o jazyce grafu prostředků najdete na stránce podrobností o dotazovacím jazyce.

> [!div class="nextstepaction"]
> [Získání dalších informací o dotazovacím jazyce](./concepts/query-language.md)