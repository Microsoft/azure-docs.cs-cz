---
title: 'Kurz: Správa dotazů v Azure Portal'
description: V tomto kurzu vytvoříte dotaz na diagram prostředku a nasdílíte nový dotaz s ostatními uživateli v Azure Portal.
ms.date: 01/27/2021
ms.topic: tutorial
ms.openlocfilehash: 7240a843cfa041137b28284e396c8a4b62a81926
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916675"
---
# <a name="tutorial-create-and-share-an-azure-resource-graph-query-in-the-azure-portal"></a>Kurz: vytvoření a sdílení dotazu na graf Azure Resource v Azure Portal

Průzkumník prostředků Azure vám umožňuje uložit dotazy na zdrojové grafy přímo do Azure Portal. Existují dva typy dotazů: _Private_ a _Shared_. Privátní dotaz je uložený v nastaveních Azure Portal. Vzhledem k tomu, že sdílený dotaz je prostředek Azure Resource Manager, který se dá spravovat pomocí řízení přístupu na základě role Azure (Azure RBAC) a je chráněný pomocí zámků prostředků. Oba typy dotazů jsou v klidovém stavu šifrované.

Uložením dotazů v Azure Portal ušetříte čas, který byste jinak strávili hledáním oblíbených nebo běžně používaných dotazů. Když sdílíte dotazy, pomůžete týmu realizovat cíle konzistence a efektivity prostřednictvím opakování.

V tomto kurzu provedete následující úlohy:

> [!div class="checklist"]
> - Vytvoření a odstranění privátního dotazu
> - Vytvořit sdílený dotaz
> - Zjistit sdílené dotazy
> - Odstranit sdílený dotaz

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).

## <a name="create-and-delete-a-private-query"></a>Vytvoření a odstranění privátního dotazu

Soukromé dotazy jsou přístupné a viditelné pouze pro účet, který je vytvoří. Protože jsou uložené v nastavení Azure Portal účtu, dají se vytvořit, používat a odstranit pouze v rámci Azure Portal. Privátní dotaz není Správce prostředků prostředek. Chcete-li vytvořit nový privátní dotaz, použijte následující postup:

1. V nabídce portálu vyberte **všechny služby** nebo použijte pole Azure Search v horní části všech stránek. Vyhledejte a vyberte **Průzkumník diagramů prostředků**.

1. Na kartě **dotaz 1** na stránce Průzkumník Azure Resource Graph zadejte následující dotaz:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

   Výběrem **Spustit dotaz** zobrazíte výsledky dotazu v dolním podokně.

   Další informace o tomto dotazu najdete v tématu [ukázky – počet virtuálních počítačů podle typu operačního systému](../samples/starter.md#count-os).

1. Vyberte **Uložit** nebo **Uložit jako**, jako název zadejte **počet virtuálních počítačů podle operačního systému** , ponechte typ jako **soukromý dotaz** a potom v dolní části podokna **Uložit dotaz** vyberte **Uložit** . Název karty se změní z **dotazu 1** na **počet virtuálních počítačů podle operačního systému**.

1. Přesuňte se z Průzkumníka grafu prostředků Azure do Azure Portal a vraťte se k němu. Všimněte si, že uložený dotaz již není zobrazen a karta **dotazu 1** vrátila hodnotu.

1. Vyberte **Otevřít dotaz**. Ujistěte se, že typ je **privátní dotaz**. **Počet uložených virtuálních počítačů podle operačního systému** se nyní zobrazí v seznamu **název dotazu** . Když vyberete odkaz na název uloženého dotazu, načte se na novou kartu s názvem tohoto dotazu.

   > [!NOTE] 
   > Když je uložený dotaz otevřený a na kartě se zobrazuje jeho název, výběr tlačítka **Uložit** se aktualizuje změnami, které byly provedeny. Pokud chcete vytvořit nový uložený dotaz z tohoto otevřeného dotazu, vyberte **Uložit jako** a pokračujte, jako kdybyste ušetřili nový dotaz značky.

1. Chcete-li odstranit uložený dotaz, vyberte znovu **Otevřít dotaz** a ověřte, zda je pole **typ** nastaveno na možnost **privátní dotaz**. Na řádku uloženého `Count VMs by OS` dotazu vyberte **Odstranit** (ikona odpadkového koše). Kliknutím na **tlačítko Ano** v potvrzovacím dialogovém okně dokončete odstranění dotazu.
   Pak zavřete podokno **dotazu** .

## <a name="create-a-shared-query"></a>Vytvořit sdílený dotaz

Na rozdíl od privátního dotazu je sdílený dotaz Správce prostředkůým prostředkem. Tato skutečnost znamená, že dotaz se uloží do skupiny prostředků, dá se spravovat a řídit pomocí Azure RBAC a může být i chráněný pomocí zámků prostředků. Jako prostředek může zobrazit a používat kdokoli, kdo má příslušná oprávnění. Chcete-li vytvořit nový sdílený dotaz, postupujte podle následujících kroků:

1. V nabídce portálu vyberte **všechny služby**, nebo pomocí pole Azure Search v horní části všech stránek vyhledejte a vyberte **Průzkumník diagramů prostředků**.

1. Na kartě **dotaz 1** na stránce Průzkumník Azure Resource Graph zadejte následující dotaz:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```
    
   Výběrem **Spustit dotaz** zobrazíte výsledky dotazu v dolním podokně.

   Další informace o tomto dotazu najdete v tématu [ukázky – počet virtuálních počítačů podle typu operačního systému](../samples/starter.md#count-os).

1. Vyberte **Uložit** nebo **Uložit jako**.

   :::image type="content" source="../media/create-share-query/save-shared-query-buttons.png" alt-text="Uložte nový dotaz pomocí tlačítka Uložit." border="false":::

1. V podokně **Uložit dotaz** zadejte **počet virtuálních počítačů podle operačního systému** pro název.

1. Změňte typ na **Shared Query**, nastavte popis na **počet virtuálních počítačů podle typu operačního systému** a nastavte **předplatné** tak, aby určovalo vytvoření prostředku dotazu.

1. Nechejte zaškrtnuté políčko **publikovat do skupiny prostředků dotazy Resource-Graph-** a **umístění skupiny prostředků** nastavené na **(US) středozápadní USA**.

1. V dolní části podokna **Uložit dotaz** vyberte **Save (Uložit** ). Název karty se změní z **dotazu 1** na **počet virtuálních počítačů podle operačního systému**. Při prvním použití skupiny prostředků **dotazování prostředků Resource-Graph** trvá uložení déle, než se očekávalo, protože se vytvořila skupina prostředků.
   
   :::image type="content" source="../media/create-share-query/save-shared-query-window.png" alt-text="Uložit nový dotaz jako sdílený dotaz" border="false":::

   > [!NOTE] 
   > Pokud chcete zadat název existující skupiny prostředků, do které se uloží sdílený dotaz, můžete zrušit zaškrtnutí políčka **publikovat do skupiny prostředků dotazy Resource-Query-Query-** Query. Použití výchozí pojmenované skupiny prostředků pro dotazy usnadňuje zjišťování sdílených dotazů. Tím se také poukáže účel této skupiny prostředků. Můžete se ale rozhodnout vybrat existující skupinu prostředků z důvodů zabezpečení na základě stávajících oprávnění.

1. Přesuňte se z Průzkumníka grafu prostředků Azure do Azure Portal a vraťte se k němu. Všimněte si, že uložený dotaz již není zobrazen a karta **dotazu 1** vrátila hodnotu.

1. Vyberte **Otevřít dotaz**. Ověřte, že typ je nastavený na **Shared Query** a že kombinace **předplatného** a **skupiny prostředků** odpovídá umístění, kam jste dotaz uložili. Položka s uloženým **počtem virtuálních počítačů podle operačního systému** se teď zobrazí v seznamu **název dotazu** . Vyberte odkaz na název uloženého dotazu, který chcete načíst na novou kartu s názvem tohoto dotazu. Jako sdílený dotaz zobrazuje na kartě vedle názvu ikonu, která označuje, že je sdílená.

   :::image type="content" source="../media/create-share-query/show-saved-shared-query.png" alt-text="Zobrazit sdílený dotaz s ikonou" border="false":::

   > [!NOTE] 
   > Když je uložený dotaz otevřený a na kartě se zobrazuje jeho název, tlačítko **Uložit** ho aktualizuje o všechny změny, které byly provedeny. Pokud chcete vytvořit nový uložený dotaz, vyberte **Uložit jako** a pokračujte, jako kdybyste ušetřili úplně nový dotaz.

## <a name="discover-shared-queries"></a>Zjistit sdílené dotazy

Vzhledem k tomu, že sdílený dotaz je prostředek Správce prostředků, existuje několik způsobů, jak ho najít:

- Z Průzkumníka grafu prostředků. Vyberte **Otevřít dotaz** a nastavte typ na **Shared Query**.
- Na stránce portálu dotazy grafu prostředků.
- Ze skupiny prostředků, do které byl uložen sdílený dotaz.
- Pomocí dotazu do grafu prostředků.

### <a name="view-resource-graph-queries"></a>Zobrazit dotazy na grafy prostředků

Na stránce Azure Portal dotazy na diagram prostředků se zobrazí sdílené dotazy, ke kterým má přihlášený účet přístup. Tato stránka umožňuje filtrování podle názvu, předplatného, skupiny prostředků a dalších vlastností dotazu na graf prostředků. Pomocí tohoto rozhraní můžete také označovat, exportovat a odstranit dotazy na grafy prostředků.

Výběrem jednoho z dotazů otevřete stránku dotaz na graf prostředků. Podobně jako u jiných Správce prostředkůch prostředků nabízí tato stránka interaktivní přehled spolu s protokolem aktivit, řízením přístupu a značkami. Zámek prostředku můžete použít také přímo z této stránky.

Přejděte na stránku dotazů grafu prostředků z nabídky portál, a to výběrem možnosti **všechny služby** nebo pomocí pole Azure Search v horní části všech stránek. Vyhledejte a vyberte **Průzkumník diagramů prostředků**.

### <a name="list-resource-groups-resources"></a>Vypsat prostředky skupin prostředků

Dotaz na graf prostředků je uvedený vedle dalších prostředků, které jsou součástí skupiny prostředků.
Výběr dotazu grafu prostředku otevře stránku pro daný dotaz. Tři tečky a možnosti místní nabídky (aktivované kliknutím pravým tlačítkem myši) fungují stejně jako na stránce dotazu na graf prostředků.

### <a name="query-resource-graph"></a>Graf prostředku dotazu

Dotazy na grafy prostředků můžete najít prostřednictvím dotazu do grafu prostředků. Následující dotaz na diagram prostředku je omezen podle typu `Microsoft.ResourceGraph/queries` a poté používá `project` k vypsání pouze názvu, času změny a samotného dotazu:

```kusto
Resources
| where type == "microsoft.resourcegraph/queries"
| project name, properties.timeModified, properties.query
```

## <a name="run-a-shared-query"></a>Spustit sdílený dotaz

Sdílený dotaz grafu prostředků lze spustit pomocí `{{shared-query-uri}}` syntaxe (Preview). Další informace najdete v tématu [syntaxe sdíleného dotazu](../concepts/query-language.md#shared-query-syntax).

## <a name="delete-a-shared-query"></a>Odstranit sdílený dotaz

Pokud už sdílený dotaz nepotřebujete, odstraňte ho. Odstraněním sdíleného dotazu odeberete odpovídající prostředek Správce prostředků. Všechny řídicí panely, se kterými se graf výsledků připnul, se teď zobrazí v chybové zprávě. Po zobrazení této chybové zprávy použijte tlačítko **Odebrat z řídicího panelu** k vyčištění řídicího panelu.

Sdílený dotaz můžete odstranit pomocí následujících rozhraní:
- Stránka dotazů na grafy prostředků
- Stránka dotazu na graf prostředků
- **Otevření stránky dotazu** v Průzkumníku grafu prostředků
- Stránka skupiny prostředků

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s tímto kurzem hotovi, odstraňte soukromé a sdílené dotazy, které jste vytvořili, pokud je už nechcete.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili soukromé a sdílené dotazy. Chcete-li získat další informace o jazyku grafu prostředků, přejděte na stránku podrobností dotazovacího jazyka.

> [!div class="nextstepaction"]
> [Získat další informace o dotazovacím jazyku](../concepts/query-language.md)