---
title: Vytvoření a sdílení dotazu v Azure Portal
description: V tomto kurzu se naučíte vytvořit dotaz na diagram prostředku a sdílet ho s ostatními v Azure Portal.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: resource-graph
ms.openlocfilehash: 10f93f34923fb2399a4b2053167576ba004ae467
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821680"
---
# <a name="tutorial-create-and-share-an-azure-resource-graph-query-in-azure-portal"></a>Kurz: vytvoření a sdílení dotazu na graf Azure Resource v Azure Portal

Průzkumník prostředků Azure umožňuje ukládat dotazy na prostředky grafu přímo do Azure Portal. Existují dva typy dotazů, _Private_ a _Shared_. _Privátní_ dotaz je uložený v nastaveních Azure Portal, ale _sdílený_ dotaz je prostředek správce prostředků, který se dá spravovat pomocí řízení přístupu na základě role (RBAC) a chráněný pomocí zámků prostředků.

Ukládání dotazů v Azure Portal šetří čas strávený hledáním vašich oblíbených nebo běžně používaných dotazů. Při sdílení dotazů povolíte, aby byl váš tým konzistentní a možné ho opakovat. V tomto kurzu provedete tyto kroky:

> [!div class="checklist"]
> - Vytvoření a odstranění _privátního_ dotazu
> - Vytvořit _sdílený_ dotaz
> - Zjistit _sdílené_ dotazy
> - Odstranit _sdílený_ dotaz

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-and-delete-a-private-query"></a>Vytvoření a odstranění privátního dotazu

_Soukromé_ dotazy jsou přístupné nebo viditelné pouze účtu, který je vytváří. Protože se ukládají do nastavení Azure Portal účtu, dají se vytvářet, používat a odstraňovat jenom v rámci Azure Portal. _Privátní_ dotaz není správce prostředků prostředek. Vytvořte nový _privátní_ dotaz pomocí následujících kroků:

1. V nabídce portálu vyberte všechny služby nebo použijte pole Azure Search v horní části všech stránek.
   Vyhledejte a vyberte "Průzkumník grafů prostředků".

1. Na kartě dotaz 1 na stránce Průzkumník Azure Resource Graph zadejte následující dotaz. Informace o tomto dotazu najdete v tématu [virtuální počítače s počtem vzorků podle typu operačního systému](../samples/starter.md#count-virtual-machines-by-os-type).
   Pokud chcete zobrazit výsledky dotazu v dolním podokně, vyberte **Spustit dotaz** .

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Vyberte **Uložit** nebo **Uložit jako**, zadejte _název_ jako počet virtuálních počítačů podle OS, ponechte _typ_ jako privátní dotaz a potom v dolní části podokna _Uložit dotaz_ vyberte **Uložit** . Název karty se změní z hodnoty Query 1 na počet virtuálních počítačů podle operačního systému.

1. Procházejte z Průzkumníka Azure Resource graphu v Azure Portal a pak se do něj vraťte. Uložený dotaz již není zobrazen a karta dotaz 1 byla vrácena.

1. Vyberte **Otevřít dotaz**. Ověřte, zda je _typ_ "privátní dotaz". Uložený počet virtuálních počítačů podle OS se nyní zobrazí v seznamu _název dotazu_ . Vyberte odkaz na název uloženého dotazu, který se načte na novou kartu s názvem dotazu.

   > [!NOTE]
   > Když je uložený dotaz otevřený a na kartě se zobrazí jeho _název_, tlačítko **Uložit** ho aktualizuje pomocí všech provedených změn. Chcete-li vytvořit nový uložený dotaz, použijte příkaz **Uložit jako** a postupujte podle kroků, jako by se jednalo o nově uložený dotaz typu Brand.

1. Chcete-li odstranit uložený dotaz, vyberte znovu **Otevřít dotaz** a ověřte, zda je _typ_ "privátní dotaz". Na řádku dotazu počet uložených virtuálních počítačů podle OS vyberte ikonu odpadkový koš. Kliknutím na **tlačítko Ano** v potvrzovacím dialogovém okně dokončete odstranění dotazu. Pak zavřete podokno _dotazu_ .

## <a name="create-a-shared-query"></a>Vytvořit sdílený dotaz

Na rozdíl od _privátního_ dotazu je _sdílený_ dotaz správce prostředkůým prostředkem. Tato skutečnost znamená, že se dotaz uložil do skupiny prostředků, dá se spravovat a řídit pomocí RBAC a dokonce i chráněný pomocí zámků prostředků. Jako prostředek může zobrazit a používat kdokoli s příslušnými oprávněními. Pomocí následujících kroků vytvořte nový _sdílený_ dotaz:

1. V nabídce portálu vyberte všechny služby nebo použijte pole Azure Search v horní části všech stránek.
   Vyhledejte a vyberte "Průzkumník grafů prostředků".

1. Na kartě dotaz 1 na stránce Průzkumník Azure Resource Graph zadejte následující dotaz. Informace o tomto dotazu najdete v tématu [virtuální počítače s počtem vzorků podle typu operačního systému](../samples/starter.md#count-virtual-machines-by-os-type).
   Výběrem **Spustit dotaz** zobrazíte výsledky dotazu v dolním podokně.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Vyberte **Uložit** nebo **Uložit jako**.

   ![Uložte nový dotaz pomocí tlačítka Uložit.](../media/create-share-query/save-shared-query-buttons.png)

1. V podokně _Uložit dotaz_ zadejte _název_ jako počet virtuálních počítačů podle OS, změňte _typ_ na Shared Query, nastavte _Popis_ na počet virtuálních počítačů podle typu operačního systému a vyberte _předplatné_ , ve kterém se prostředek dotazu vytvoří. Ponechte zaškrtnuté políčko Publikovat do skupiny prostředků – dotazy na skupinu prostředků a _umístění skupiny prostředků_ nastavte na (US) středozápadní USA. V dolní části podokna _Uložit dotaz_ vyberte **Save (Uložit** ). Název karty se změní z hodnoty Query 1 na počet virtuálních počítačů podle operačního systému. Při prvním použití skupiny prostředků "Resource-Graph-dotazů" bude uložení trvat déle, než se vytvoří skupina prostředků.

   ![Uložit nový dotaz jako sdílený dotaz](../media/create-share-query/save-shared-query-window.png)

   > [!NOTE]
   > V případě potřeby odeberte kontrolu a zadejte název existující skupiny prostředků, do které se uloží sdílený dotaz. Použití výchozí pojmenované skupiny prostředků pro dotazy usnadňuje zjišťování _sdílených_ dotazů. Tím se také zřetelně vylepší účel této skupiny prostředků. Výběr existující skupiny prostředků se ale může provést z důvodů zabezpečení na základě stávajících oprávnění.

1. Procházejte z Průzkumníka Azure Resource graphu v Azure Portal a pak se do něj vraťte. Uložený dotaz již není zobrazen a karta dotaz 1 byla vrácena.

1. Vyberte **Otevřít dotaz**. Ověřte, že _typ_ je "Shared Query" a kombinace _předplatného_ a _skupiny prostředků_ odpovídá umístění, kam jste dotaz uložili. Uložený počet virtuálních počítačů podle OS se nyní zobrazí v seznamu _název dotazu_ . Vyberte odkaz na název uloženého dotazu, který se načte na novou kartu s názvem dotazu. Jako _sdílený_ dotaz zobrazuje ikonu na kartě vedle názvu, který označuje jako sdílený.

   ![Zobrazit sdílený dotaz s ikonou](../media/create-share-query/show-saved-shared-query.png)

   > [!NOTE]
   > Když je uložený dotaz otevřený a na kartě se zobrazí jeho _název_, tlačítko **Uložit** ho aktualizuje pomocí všech provedených změn. Chcete-li vytvořit nový uložený dotaz, použijte příkaz **Uložit jako** a postupujte podle kroků, jako by se jednalo o nově uložený dotaz typu Brand.

## <a name="discover-shared-queries"></a>Zjistit sdílené dotazy

Jako _sdílený_ dotaz je správce prostředků prostředek, existuje několik způsobů, jak je najít:

- V Průzkumníku grafu prostředků vyberte **Otevřít dotaz** a nastavte _typ_ na Shared Query.
- Stránka portálu dotazy na diagram prostředků
- Skupina prostředků, ve které se uložila
- S dotazem do grafu prostředků

### <a name="view-resource-graph-queries"></a>Zobrazit dotazy na grafy prostředků

V Azure Portal stránka dotazy na diagram prostředků zobrazuje _sdílené_ dotazy, ke kterým má přihlášený účet přístup. Tato stránka umožňuje filtrování podle názvu, předplatného, skupiny prostředků a dalších vlastností dotazu na graf prostředků. Dotazy na grafy prostředků mohou být také označeny, exportovány a odstraněny pomocí tohoto rozhraní.

Výběrem jednoho z dotazů otevřete stránku dotaz na graf prostředků. Podobně jako u jiných Správce prostředkůch prostředků nabízí tato stránka interaktivní přehled spolu s protokolem aktivit, řízením přístupu a značkami. Zámek prostředků lze také použít přímo z této stránky.

Přejděte na stránku dotazů grafu prostředků z nabídky portál, a to tak, že vyberete všechny služby, nebo použijete pole Azure Search v horní části všech stránek. Vyhledejte a vyberte "Průzkumník grafů prostředků".

### <a name="list-resource-groups-resources"></a>Vypsat prostředky skupin prostředků

Dotaz na graf prostředků je uvedený vedle dalších prostředků, které jsou součástí skupiny prostředků.
Výběr dotazu grafu prostředku otevře stránku pro daný dotaz. Tlačítko se třemi tečkami nebo kliknutím pravým tlačítkem fungují stejně jako na stránce dotaz na graf prostředků.

### <a name="query-resource-graph"></a>Graf prostředku dotazu

Jako prostředek Správce prostředků se dotazy na grafy prostředků dají najít pomocí dotazu do grafu prostředků.
Následující omezení dotazu na diagram prostředku podle typu `Microsoft.ResourceGraph/queries`a poté používá `project` k vypsání pouze názvu, času změny a samotného dotazu:

```kusto
Resources
| where type == "microsoft.resourcegraph/queries"
| project name, properties.timeModified, properties.query
```

## <a name="delete-a-shared-query"></a>Odstranit sdílený dotaz

Pokud už _sdílený_ dotaz nepotřebujete, odstraňte ho. Odstraněním _sdíleného_ dotazu dojde k odebrání skutečného prostředku správce prostředků. Všechny řídicí panely, se kterými se graf výsledků připnul, se teď zobrazí chybová zpráva. Po zobrazení této chybové zprávy použijte tlačítko **Odebrat z řídicího panelu** k vyčištění řídicího panelu.

_Sdílený_ dotaz lze odstranit z následujících rozhraní:
- Stránka dotazů na grafy prostředků
- Stránka dotazu na graf prostředků
- Průzkumník grafu prostředků – otevře stránku dotazu
- Stránka skupiny prostředků

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s tímto kurzem hotovi, odstraňte _soukromé_ a _sdílené_ dotazy, které jste vytvořili, pokud je už nechcete.

## <a name="next-steps"></a>Další kroky

- Spusťte první dotaz pomocí [Azure Portal](../first-query-portal.md)
- Získejte další informace o [dotazovacím jazyce](../concepts/query-language.md)
- Naučte se [prozkoumat prostředky](../concepts/explore-resources.md)
- Zobrazit ukázky [Starter dotazy](../samples/starter.md)
- Zobrazit ukázky [Pokročilé dotazy](../samples/advanced.md)
- Váš názor na [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)