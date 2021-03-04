---
title: Skupiny Azure Monitorch sešitů
description: Používání skupin v sešitech Azure Monitor.
services: azure-monitor
author: lgayhardt
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: lagayhar
ms.openlocfilehash: b36dec79e5d60d97c11b9f8c74790527e1bd19fb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731353"
---
# <a name="how-to-use-groups-in-workbooks"></a>Používání skupin v sešitech

Položka skupiny v sešitu umožňuje logicky seskupovat sadu kroků v sešitu.

Skupiny v sešitech jsou užitečné pro několik věcí:

- Layout
  - Ve scénářích, kdy chcete, aby byly položky uspořádány svisle, můžete vytvořit skupinu položek, které budou všechny navrstveny, a nastavit styl skupiny tak, aby byl procentuální hodnotou šířky, místo nastavení procentuální šířky pro všechny jednotlivé položky.
- Viditelnost
  - Ve scénářích, kdy chcete zobrazit nebo zobrazit dohromady mnoho položek, můžete nastavit viditelnost celé skupiny položek místo nastavení nastavení viditelnosti pro každou jednotlivou položku. To může být užitečné v šablonách, které používají karty, protože můžete použít skupinu jako obsah karty a celou skupinu můžete skrýt nebo zobrazit na základě parametru nastaveného na vybrané kartě.
- Výkon
  - V případech, kdy máte velmi velkou šablonu s mnoha oddíly nebo kartami, můžete převést jednotlivé oddíly na vlastní podšablonu a pomocí skupin načíst všechny dílčí šablony v rámci šablony nejvyšší úrovně. Obsah podšablon se nebude načítat ani spouštět, dokud uživatel nenastaví tyto skupiny jako viditelné. Přečtěte si další informace o [tom, jak rozdělit velkou šablonu do mnoha šablon](#how-to-split-a-large-template-into-many-templates).

## <a name="using-groups"></a>Používání skupin

Chcete-li přidat skupinu do sešitu, vyberte možnost *Přidat* a *Přidat skupinu*.

![Vyberte Přidat a potom skupina.](./media/workbooks-groups/add-group.png)

Níže je snímek obrazovky skupiny v režimu čtení se dvěma položkami uvnitř: textovou položkou a položkou dotazu.  

![Skupina v režimu čtení.](./media/workbooks-groups/groups-view.png)

Při úpravách sešitu vidíte, že tyto dvě položky jsou ve skutečnosti v rámci položky skupiny:

![Skupina v režimu úprav. ](./media/workbooks-groups/groups-edit.png)

Na snímku obrazovky výše je skupina v režimu úprav, zobrazení obsahuje dvě položky (uvnitř přerušované oblasti). Každý krok může být v režimu úprav nebo čtení, nezávisle na sobě navzájem. Například textový krok je v režimu úprav, zatímco je krok dotazu v režimu čtení.

## <a name="scoping"></a>Rozsah

V současné době je skupina považována za nový obor v sešitu. Všechny parametry vytvořené ve skupině jsou viditelné pouze uvnitř skupiny. To platí také pro sloučení, data se zobrazí pouze v rámci skupiny nebo na nadřazené úrovni.

## <a name="group-types"></a>Typy skupin

Položka skupiny sešitu umožňuje přidat skupinu položek do sešitu. Jako autor sešitu určíte, který typ skupiny bude. Existují dva typy skupin:

- Možno
  - Skupina v sešitu umožňuje přidat, odebrat nebo upravit obsah položek ve skupině. Tato operace se nejčastěji používá pro účely rozložení a viditelnosti.
- Ze šablony
  - Skupina v sešitu se načte z obsahu jiné šablony podle jejího ID. Obsah této šablony se načte a sloučí do sešitu za běhu. V režimu úprav nelze upravovat žádné obsahy skupiny, protože se při dalším načtení položky znovu načtou ze šablony.  

## <a name="load-types"></a>Typy zatížení

Existuje několik různých způsobů, jak lze načíst obsah skupiny. Jako autor sešitu se dostanete, když určíte, kdy a jak se bude obsah skupiny načítat.

### <a name="lazy-the-default"></a>Opožděné (výchozí)

Skupina bude načtena pouze v případě, že je položka viditelná. To umožňuje, aby se skupina použila položkami karet. Pokud není karta nikdy vybraná, nebude se tato skupina zobrazovat, takže se obsah nenačte.

Pro skupiny vytvořené ze šablony se nenačte obsah šablony a položky ve skupině se nevytvoří, dokud se skupina nebude zobrazovat. Uživateli se při načtení obsahu zobrazí číselníky průběhu pro celou skupinu.

### <a name="explicit"></a>Explicitní

V tomto režimu se zobrazí tlačítko, kde by byla skupina, a nebude načten ani vytvořen žádný obsah, dokud uživatel neklikne na tlačítko, aby načetl obsah. To je užitečné ve scénářích, kdy může být obsah nákladný pro výpočetní výkon nebo pro vzácná použití. Autor může zadat text, který se zobrazí na tlačítku.

Níže je snímek obrazovky s explicitním nastavením načítání zobrazující nakonfigurované tlačítko načíst další.

![Nastavení explicitního načtení](./media/workbooks-groups/groups-explicitly-loaded.png)

Skupina před načtením do sešitu:

![Explicitní Skupina před načtením do sešitu](./media/workbooks-groups/groups-explicitly-loaded-before.png)

Skupina po načtení do sešitu:

![Explicitní Skupina po načtení do sešitu](./media/workbooks-groups/groups-explicitly-loaded-after.png)

### <a name="always"></a>Vždy

V tomto režimu se obsah skupiny vždy načte a vytvoří, jakmile se sešit načte. Nejčastěji se používá při použití skupiny pouze pro účely rozložení, kde bude obsah vždy viditelný.

## <a name="using-templates-inside-a-group"></a>Používání šablon v rámci skupiny

Když je skupina nakonfigurovaná tak, aby se načetla ze šablony, bude se tento obsah ve výchozím nastavení načítat opožděně, načte se jenom v případě, že je skupina zobrazená.

Když je šablona načtena do skupiny, sešit se pokusí sloučit všechny parametry deklarované v šabloně, která je načtena s parametry, které již ve skupině existují. Všechny parametry, které již existují v sešitu se stejnými názvy, budou sloučeny ze načtené šablony. Pokud jsou všechny parametry v kroku parametru sloučeny, celý krok parametrů zmizí.

### <a name="example-1-all-parameters-have-identical-names"></a>Příklad 1: všechny parametry mají stejné názvy

Zvažte šablonu, která má dva parametry v horní části.

- `TimeRange` – Parametr časového rozsahu.
- `Filter` – textový parametr.

![Úprava položky parametrů: "parametry nejvyšší úrovně"](./media/workbooks-groups/groups-top-level-params.png)

Pak položka skupiny načte druhou šablonu, která má své vlastní dva parametry a textový krok, kde jsou parametry pojmenované stejné:

![Úprava položky parametru pro druhou šablonu](./media/workbooks-groups/groups-merged-away.png)

Při načtení druhé šablony do skupiny budou duplicitní parametry sloučeny. Vzhledem k tomu, že všechny parametry jsou sloučeny, krok vnitřních parametrů je také sloučen, což vede ke skupině obsahující pouze krok textu.

### <a name="example-2-one-parameter-has-an-identical-name"></a>Příklad 2: jeden parametr má stejný název

Zvažte šablonu skupiny, která má dva parametry v horní části.

- `TimeRange` – Parametr časového rozsahu.
- `FilterB` – textový parametr, Všimněte si, že se `Filter` nelíbí horní šablona.

![Úprava položky skupiny s výsledkem sloučení parametrů](./media/workbooks-groups/groups-wont-merge-away.png)

Po načtení šablony item's skupiny se `TimeRange` parametr sloučí ze skupiny. Sešit bude mít krok počátečních parametrů s a a `TimeRange` `Filter` parametr skupiny bude obsahovat jenom tyto kroky: `FilterB`

![výsledek parametrů, které se pryč nesloučí](./media/workbooks-groups/groups-wont-merge-away-result.png)

Pokud načtená šablona obsahovala `TimeRange` a `Filter` (namísto `FilterB` ), pak výsledný sešit by měl mít krok Parameters a skupinu s pouze zbývajícím krokem textu.

## <a name="how-to-split-a-large-template-into-many-templates"></a>Jak rozdělit velkou šablonu do mnoha šablon

Pro zvýšení výkonu je vhodné rozdělit velkou šablonu do několika menších šablon, které uživatel načte nějaký obsah opožděně nebo na vyžádání. Díky tomu je počáteční načtení rychlejší, protože šablona nejvyšší úrovně může být mnohem menší.

Při rozdělení šablony na části budete efektivně potřebovat rozdělit šablonu na mnoho šablon (dílčí šablony), které všechny pracují samostatně. Takže pokud má šablona nejvyšší úrovně `TimeRange` parametr, který používá jiný postup, bude mít dílčí šablona také krok Parameters, který definuje parametr s přesným názvem. To umožňuje, aby dílčí šablony fungovaly nezávisle a mohly je načíst uvnitř větších šablon ve skupinách.

Chcete-li převést větší šablonu na více dílčích šablon:

1.  Po sdílených parametrech vytvořte v horní části sešitu novou prázdnou skupinu. Tato nová skupina se nakonec stane dílčí šablonou.
2. Vytvořte kopii kroku sdílených parametrů a pak pomocí *přesunout do skupiny* přesuňte kopii do skupiny vytvořené v kroku 1. Tento krok parametrů umožní dílčí šabloně pracovat nezávisle na vnější šabloně a při načtení do vnější šablony se sloučí.
    > [!NOTE]
    > Podšablona nepotřebuje mít tyto parametry, které by se daly sloučit, pokud nikdy neplánujete dílčí šablony, které jsou viditelné sami. Pokud to budete potřebovat později, budete je velmi těžké upravovat nebo ladit.

3. Přesuňte každou položku v sešitu, který chcete mít v dílčí šabloně, do skupiny vytvořené v kroku 1.
4. Pokud se jednotlivé kroky přesunuté v kroku 3 dostaly do podmíněného viditelnosti, to se stane viditelností vnější skupiny (například používané na kartách). Odeberte je z položek uvnitř skupiny a přidejte toto nastavení viditelnosti do samotné skupiny. Uložte sem, abyste zabránili ztrátě změn nebo exportu a uložení kopie obsahu JSON.
5. Pokud chcete, aby se tato skupina načetla ze šablony, můžete použít tlačítko *Upravit* na panelu nástrojů ve skupině. Tím se otevře pouze obsah této skupiny jako sešit v novém okně. Pak ji můžete uložit podle potřeby a zavřít toto zobrazení sešitu (nezavírejte prohlížeč, pouze toto zobrazení pro návrat k předchozímu sešitu, který jste upravovali).
6. Pak můžete změnit krok skupiny pro načtení ze šablony a nastavit pole ID šablony na sešit nebo šablonu, kterou jste vytvořili v kroku 5. Aby bylo možné pracovat s ID sešitů, musí být zdrojem ID prostředku sdíleného sešitu. Stiskněte *načíst* a obsah této skupiny se teď načte z této dílčí šablony místo uložení do tohoto vnějšího sešitu.

## <a name="next-steps"></a>Další kroky
- [Přehled sešitů](./workbooks-overview.md)
- [Použití JSONPath se sešity](workbooks-jsonpath.md)