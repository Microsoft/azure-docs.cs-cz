---
title: 'Kurz: Správa dotazů na webu Azure Portal'
description: V tomto kurzu vytvoříte dotaz na graf prostředků a nový dotaz sdílíte s ostatními na webu Azure Portal.
ms.date: 11/21/2019
ms.topic: tutorial
ms.openlocfilehash: 00cb3f95112804c81beb6bce6fc35891e6197e60
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74303952"
---
# <a name="tutorial-create-and-share-an-azure-resource-graph-query-in-the-azure-portal"></a>Kurz: Vytvoření a sdílení dotazu Azure Resource Graph na webu Azure Portal

Azure Resource Graph Explorer umožňuje ukládat dotazy na graf prostředků přímo na webu Azure Portal. Existují dva typy dotazů: _Soukromé_ a _Sdílené_. Soukromý dotaz se uloží do nastavení portálu Azure. Vzhledem k tomu, sdílený dotaz je prostředek Správce prostředků, který lze spravovat pomocí ovládacích prvků přístupu na základě rolí (RBAC) a chráněné uzamčení prostředků. Oba typy dotazů jsou šifrovány v klidovém stavu.

Uložením dotazů na portál Azure ušetříte čas, který byste jinak mohli strávit hledáním oblíbených nebo běžně používaných dotazů. Když sdílíte dotazy, pomáháte týmu realizovat cíle konzistence a efektivity prostřednictvím opakování.

V tomto kurzu dokončíte následující úkoly:

> [!div class="checklist"]
> - Vytvoření a odstranění soukromého dotazu
> - Vytvoření sdíleného dotazu
> - Zjišťování sdílených dotazů
> - Odstranění sdíleného dotazu

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).

## <a name="create-and-delete-a-private-query"></a>Vytvoření a odstranění soukromého dotazu

Soukromé dotazy jsou přístupné a viditelné pouze pro účet, který je vytváří. Když se ukládají do nastavení portálu Azure, můžou je vytvářet, používat a odstraňovat jenom z portálu Azure. Soukromý dotaz není prostředek Správce prostředků. Chcete-li vytvořit nový soukromý dotaz, postupujte takto:

1. V nabídce portálu vyberte **Všechny služby** nebo použijte vyhledávací pole Azure v horní části všech stránek. Vyhledejte a vyberte **Průzkumník a průzkumník zdrojů**.

1. Na kartě **Dotaz 1** na stránce Průzkumník u prostředků Azure zadejte následující dotaz:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

   Vyberte **Spustit dotaz,** chcete-li zobrazit výsledky dotazu v dolním podokně.

   Další informace o tomto dotazu naleznete v [tématu Ukázky – Počet virtuálních počítačů podle typu operačního systému](../samples/starter.md#count-virtual-machines-by-os-type).


1. Vyberte **Uložit** nebo **Uložit jako**, zadejte jako název **virtuální chod počítání podle operačního serveru,** ponechte typ jako **soukromý dotaz**a v dolní části podokna **Uložit dotaz** vyberte **Uložit.** Název karty se změní z **dotazu 1** na **počet virtuálních ms podle operačního.**

1. Přesuňte se od Průzkumníka prostředků Azure na webu Azure Portal a pak se k němu vraťte. Všimněte si, že uložený dotaz se již nezobrazuje a karta **Dotaz 1** byla vrácena.

1. Vyberte **Otevřít dotaz**. Ujistěte se, že typ je **soukromý dotaz**. Uložené **názvy Počet virtuálních počítače podle operačního serveru** se nyní zobrazí v seznamu **Název dotazu.** Když vyberete odkaz na název uloženého dotazu, načte se na novou kartu s názvem tohoto dotazu.

   > [!NOTE] 
   > Když je otevřený uložený dotaz a na kartě se zobrazí jeho název, výběrem tlačítka **Uložit** jej aktualizujete se všemi provedenými změnami. Chcete-li vytvořit nový uložený dotaz z tohoto otevřeného dotazu, vyberte **Uložit jako** a postupujte tak, jako byste ukládejte zcela nový dotaz.

1. Chcete-li uložený dotaz odstranit, vyberte znovu **otevřít dotaz** a ověřte, zda je pole **Typ** nastaveno na **soukromý dotaz**. Na řádku uloženého `Count VMs by OS` dotazu vyberte **Odstranit** (ikona koše). V potvrzovacím dialogovém okně vyberte **Ano,** chcete-li dokončit odstranění dotazu.
   Potom zavřete **podokno Otevřít dotaz.**

## <a name="create-a-shared-query"></a>Vytvoření sdíleného dotazu

Na rozdíl od soukromého dotazu je sdílený dotaz prostředek Správce prostředků. Tato skutečnost znamená, že dotaz získá uloženy do skupiny prostředků, lze spravovat a řídit pomocí RBAC a lze dokonce chránit pomocí uzamčení prostředků. Jako prostředek může zobrazit a použít každý, kdo má příslušná oprávnění.
Chcete-li vytvořit nový sdílený dotaz, postupujte takto:

1. V nabídce portálu vyberte **Všechny služby**nebo použijte vyhledávací pole Azure v horní části všech stránek k vyhledání a výběru **Průzkumníka grafů prostředků**.

1. Na kartě **Dotaz 1** na stránce Průzkumník u prostředků Azure zadejte následující dotaz:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```
    
   Vyberte **Spustit dotaz,** chcete-li zobrazit výsledky dotazu v dolním podokně.

   Další informace o tomto dotazu naleznete v [tématu Ukázky – Počet virtuálních počítačů podle typu operačního systému](../samples/starter.md#count-virtual-machines-by-os-type).

1. Vyberte **Uložit** nebo **Uložit jako**.

   
   ![Uložení nového dotazu pomocí tlačítka Uložit](../media/create-share-query/save-shared-query-buttons.png)

1. V podokně **Uložit dotaz** zadejte pro název **virtuální chod y count podle operačního serveru.**

1. Změňte typ na **Sdílený dotaz**, nastavte popis na **Počet virtuálních počítačů podle typu operačního systému**a nastavte **možnost Odběr** určující, kde se prostředek dotazu vytvoří.

1. Ponechte zaškrtnuté políčko **Publikovat na skupině prostředků a** umístění **skupiny prostředků** na **(US) –střed USA**.

1. V dolní části podokna **Uložit dotaz** vyberte **Uložit.** Název karty se změní z **dotazu 1** na **počet virtuálních ms podle operačního.** Při prvním použití skupiny prostředků **graf dotazů** je použita, uložení trvá déle, než bylo očekáváno, jak se vytvoří skupina prostředků.
   
   ![Uložení nového dotazu jako sdíleného dotazu](../media/create-share-query/save-shared-query-window.png)

   > [!NOTE] 
   > Pokud chcete zadat název existující skupiny prostředků pro uložení sdíleného dotazu, můžete zrušit **zaškrtnutí políčka Publikovat do skupiny prostředků a dotazů na dotazy** k prostředkům. Použití výchozí skupiny pojmenovaných prostředků pro dotazy usnadňuje zjišťování sdílených dotazů. Také zviditisňuje účel této skupiny prostředků. Můžete se však rozhodnout vybrat existující skupinu prostředků z bezpečnostních důvodů na základě existujících oprávnění.

1. Přesuňte se od Průzkumníka prostředků Azure na webu Azure Portal a pak se k němu vraťte. Všimněte si, že uložený dotaz se již nezobrazuje a karta **Dotaz 1** byla vrácena.

1. Vyberte **Otevřít dotaz**. Ověřte, zda je typ nastaven na **sdílený dotaz** a kombinace **nabídky Odběr** a Skupina **prostředků** odpovídá uložení dotazu. Uložené **virtuální počítače s počtem dat podle položky operačního serveru** se nyní zobrazí v seznamu **Název dotazu.** Vyberte odkaz na název uloženého dotazu, chcete-li jej načíst na novou kartu s názvem tohoto dotazu. Jako sdílený dotaz zobrazí na kartě vedle názvu ikonu, která ji označuje jako sdílenou.

   ![Zobrazit ikonu Sdílený dotaz s ikonou](../media/create-share-query/show-saved-shared-query.png)

   > [!NOTE] 
   > Když je otevřený uložený dotaz a na kartě se zobrazí jeho název, tlačítko **Uložit** jej aktualizuje o všechny provedené změny. Pokud chcete vytvořit nový uložený dotaz, vyberte **Uložit jako** a pokračujte, jako byste ukládávali zcela nový dotaz.

## <a name="discover-shared-queries"></a>Zjišťování sdílených dotazů

Vzhledem k tomu, že sdílený dotaz je prostředek Správce prostředků, existuje několik způsobů, jak ho najít:

- V Průzkumníku grafů prostředků vyberte **Otevřít dotaz** a nastavte typ na **Sdílený dotaz**.
- Na stránce portálu dotazů na graf prostředků.
- Ze skupiny prostředků, do které byl uložen sdílený dotaz.
- Prostřednictvím dotazu na resource graph.

### <a name="view-resource-graph-queries"></a>Zobrazit dotazy na graf prostředků

Na webu Azure Portal se na stránce Dotazy na graf prostředků zobrazí sdílené dotazy, ke kterým má přístup protokolovaný účet. Tato stránka umožňuje filtrování podle názvu, odběr, skupina prostředků a další vlastnosti dotazu grafu prostředků. Pomocí tohoto rozhraní můžete také označit, exportovat a odstranit dotazy na graf prostředků.

Výběrem jednoho z dotazů se otevře stránka dotazu na graf prostředků. Stejně jako ostatní prostředky Správce prostředků nabízí tato stránka interaktivní přehled spolu s protokolem aktivit, řízením přístupu a značkami. Zámek prostředků můžete také použít přímo z této stránky.

Přechylujte stránku s dotazy na graf prostředků z nabídky portálu výběrem **možnosti Všechny služby** nebo pomocí vyhledávacího pole Azure v horní části všech stránek. Vyhledejte a vyberte **Průzkumník a průzkumník zdrojů**.

### <a name="list-resource-groups-resources"></a>Seznam zdrojů skupin zdrojů

Dotaz grafu prostředků je uveden vedle jiných prostředků, které jsou součástí skupiny prostředků.
Když vyberete dotaz Na grafu prostředků, otevře se stránka pro tento dotaz. Možnosti tři tečky a místní nabídky (aktivované kliknutím pravým tlačítkem myši) fungují stejně jako na stránce dotazu grafu prostředků.

### <a name="query-resource-graph"></a>Graf prostředků dotazu

Dotazy na graf prostředků můžete najít prostřednictvím dotazu na graf prostředků. Následující dotaz resource graph `Microsoft.ResourceGraph/queries`omezuje podle `project` typu a potom používá k zobrazení pouze názvu, upraveného času a samotného dotazu:

```kusto
Resources
| where type == "microsoft.resourcegraph/queries"
| project name, properties.timeModified, properties.query
```

## <a name="delete-a-shared-query"></a>Odstranění sdíleného dotazu

Pokud sdílený dotaz již není potřeba, odstraňte jej. Odstraněním sdíleného dotazu odeberete odpovídající prostředek Správce prostředků. Všechny řídicí panely, ke kterým byl graf výsledků připnut, se nyní zobrazují chybová zpráva. Když se tato chybová zpráva zobrazí, vyčistěte řídicí panel pomocí tlačítka **Odebrat z řídicího panelu.**

Sdílený dotaz můžete odstranit prostřednictvím následujících rozhraní:
- Stránka Dotazů na graf prostředků
- Stránka dotazu grafu prostředků
- Stránka Otevřít dotaz v **Průzkumníkovi** grafu prostředků
- Stránka Skupiny prostředků

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto kurzu odstraňte soukromé a sdílené dotazy, které jste vytvořili, pokud je již nechcete.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili soukromé a sdílené dotazy. Další informace o jazyce grafu prostředků najdete na stránce podrobností o dotazovacím jazyce.

> [!div class="nextstepaction"]
> [Získání dalších informací o dotazovacím jazyce](../concepts/query-language.md)