---
title: Log Analytics v Azure Monitor nabízí sady ukázkových dotazů, které můžete spustit samostatně nebo použít jako výchozí bod pro vlastní dotazy.
description: Dotazy, které můžete začít zadávat a upravovat podle svých potřeb
ms.subservice: logs
ms.topic: article
author: rboucher
ms.author: robb
ms.date: 06/16/2020
ms.openlocfilehash: e4b264d980eac50525e9d9013bc2841fe68065a3
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94496962"
---
# <a name="example-queries-in-azure-monitor-log-analytics"></a>Příklady dotazů v Azure Monitor Log Analytics
Log Analytics nabízí sady ukázkových dotazů, které můžete spustit samostatně nebo použít jako výchozí bod pro vlastní dotazy. Tento článek popisuje příklady dotazů a jejich použití.

Pokud nejste obeznámeni s Log Analytics nebo dotazovacím jazykem KQL, příklady dotazů představují skvělý způsob, jak začít. Můžou poskytnout okamžitý přehled o zdroji a poskytnout dobrý způsob, jak začít s učením a používáním KQL, takže zkrátí dobu potřebnou k zahájení používání Log Analytics. Shromáždili jsme a nařídili více než 250 ukázkových dotazů navržených tak, aby vám poskytovala okamžitou hodnotu a tento počet ukázkových dotazů se průběžně zvětšuje.

## <a name="in-context-queries"></a>Dotazy v kontextu

Nové filtry prostředí a navrhuje dotazy v kontextu. Jinými slovy systém automaticky zobrazuje jenom dotazy týkající se zvoleného oboru.

- Pro **jeden prostředek** – dotazy se filtrují podle typu prostředku.
- Pro **skupinu prostředků** – dotazy se filtrují podle prostředků v konkrétní skupině prostředků.
- Pro **pracovní prostor** – dotazy se filtrují podle řešení nainstalovaných v pracovním prostoru.

Toto chování je konzistentní pro všechny obory Log Analytics. Pokud nevidíte vzorový dotaz na požadovaný typ prostředku, může to být kvůli filtrům z důvodu kontextu. V další části se dozvíte, jak odebrat obor v kontextu, abyste mohli zobrazit všechny možné dotazy.

> [!TIP]
> Čím více prostředků máte v oboru, tím déle bude vyfiltrovat a zobrazit dialogové okno ukázkový dotaz.

## <a name="example-query-user-interface"></a>Příklad uživatelského rozhraní dotazů

Můžete získat příklady dotazů ze dvou různých umístění.

### <a name="example-query-dialog"></a>Příklad dialogového okna dotazu

Při prvním zadání Log Analyticsho prostředí se zobrazí *dialogové okno Ukázkové dotazy* automaticky.  K němu lze také přistup kliknutím v pravém horním rohu obrazovky na **příkladech dotazů**.

![Poznámky – příklady dotazů](media/saved-queries/sidebar-2.png)

Zobrazí se dialogové okno příklad dotazu, jak je znázorněno níže:  

![Obrazovka příklad dotazů](media/saved-queries/example-query-start.png)

Předchozí snímek obrazovky zobrazuje obrazovku protokolů pro instanci Azure Key Vault. Jak bylo zmíněno dříve v tomto článku, dotazy jsou zobrazeny v kontextu.  V důsledku toho se snímek obrazovky zobrazí pouze Key Vault souvisejících příkladech. Pokud vyberete celé předplatné, zobrazí se dotazy pro všechny typy prostředků v tomto předplatném.  

Každý příklad dotazu je reprezentován kartou. Můžete rychle prohledat dotazy a zjistit, co potřebujete. Dotaz můžete spustit přímo z dialogového okna nebo ho načíst do editoru dotazů pro další vyladění a vylepšení.

### <a name="sidebar-query-experience"></a>Možnosti dotazů na postranní panel

Ke všem funkcím prostředí dialogového okna se dá dostat z podokna dotazy na levém postranním panelu Log Analytics. Popis dotazu a další funkce můžete získat tak, že najedete myší na název dotazu.

![Snímek obrazovky, který zobrazuje podokno dotazy](media/saved-queries/sidebar-3.png)

## <a name="finding-and-filtering-queries"></a>Hledání a filtrování dotazů

Možnosti v této části jsou k dispozici jak v dialogovém okně, tak v možnosti dotazování v postranním panelu, ale s mírně odlišným uživatelským rozhraním.  

### <a name="use-favorites"></a>Použití oblíbených položek

Můžete oblíbené často používané dotazy využít k poskytnutí rychlejšího přístupu.

> [!TIP]
> Shromažďování a zobrazování dotazů později je dobrým způsobem, jak začít. Najděte potřebné dotazy a kliknutím na hvězdičku vedle dotazu ji přidejte do oblíbených položek. Pokud budete později chtít dotaz najít, můžete ho zrušit jeho oblíbeným.  

### <a name="filtering-and-group-by"></a>Filtrování a seskupení podle

I když se dialogové okno dotazu filers a zobrazí jenom dotazy se správným kontextem, můžete zvolit odebrání filtru a zobrazit všechny dotazy.

### <a name="group-by"></a>Seskupit podle

Chcete-li změnit seskupení dotazů, klikněte na rozevírací seznam *Seskupit podle* :

![Příklady dotazů – zobrazení GroupBy](media/saved-queries/example-query-groupby.png)

Dialog podporuje seskupení podle:

- **Typ prostředku** – prostředek definovaný v Azure, jako je třeba virtuální počítač. Úplné mapování Azure Monitor protokolů/Log Analytics tabulek na typ prostředku najdete v [referenčních informacích k Azure monitor tabulce](/azure/azure-monitor/reference/tables/tables-resourcetype) .  
- **Kategorie** – typ informací, jako je *zabezpečení* nebo *audit*. Kategorie jsou stejné jako kategorie definované v podokně na straně tabulky. Úplný seznam kategorií najdete v [referenčních informacích k Azure monitor tabulce](/azure/azure-monitor/reference/tables/tables-category) .  
- **Řešení** – Azure monitor řešení přidružené k dotazům
- **Téma** – téma ukázkového dotazu, jako jsou *protokoly aktivit* nebo *protokoly aplikací*. Vlastnost tématu je jedinečná pro příklad dotazů a může se lišit podle konkrétního typu prostředku.

Hodnoty seskupení také fungují jako aktivní obsah. Kliknutí na jednu z hodnot na levé straně obrazovky posouvá zobrazení dotazů doprava na položku, na kterou jste klikli.

### <a name="filter"></a>Filtrovat

Můžete také filtrovat dotazy podle výše zmíněných hodnot **Seskupit** . V dialogovém okně příklad dotazu jsou filtry nalezeny v horní části.

![Příklad filtru obrazovky dotazů](media/saved-queries/example-query-filter.png)

### <a name="combining-group-by-and-filter"></a>Kombinování skupin podle a filtrování

Funkce Filter a Group by jsou navržené tak, aby fungovaly společně. Poskytují flexibilitu při uspořádání dotazů. Pokud například používáte skupinu prostředků s více prostředky, budete možná chtít filtrovat dolů na konkrétní typ prostředku a seřadit výsledné dotazy podle tématu.

## <a name="sample-query-dialog-appearance-behavior"></a>Ukázka chování vzhledu dialogu dotazu

Pokud jste KQL pro a dáváte přednost přímému navýšení editoru dotazů, můžete přepnout dialogové okno dotazu na off (vypnuto). V případě vypnutí se dialogové okno ukázkový dotaz při načítání Log Analytics obrazovky nenačte.

![Příklady zapnuto](media/saved-queries/examples-on-off.png)

Na tlačítko *příklady dotazů* na horním panelu Log Analytics můžete mít vždycky přístup k nabídce ukázka dotazu.

## <a name="query-explorer"></a>Průzkumník dotazů

Prostředí Průzkumníka dotazů pro ukládání a sdílení uživatelem generovaných dotazů zůstává nezměněno.

## <a name="next-steps"></a>Další kroky

[Začínáme s dotazy KQL](get-started-queries.md)

