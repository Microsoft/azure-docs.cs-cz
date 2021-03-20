---
title: Přepsání hierarchického stavu
description: Vysvětluje pojem hierarchické komponenty přepisu stavu.
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 851a87885ac765c829e8c2be9fd1205e22906ca9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94445148"
---
# <a name="hierarchical-state-override"></a>Přepsání hierarchického stavu

V mnoha případech je nutné dynamicky měnit vzhled částí [modelu](../../concepts/models.md), například skrývání dílčích grafů nebo přepnutí částí na transparentní vykreslování. Změna materiálů jednotlivých zúčastněných částí není praktická, protože vyžaduje iteraci v celém grafu scény a správu klonování a přiřazování materiálů na jednotlivých uzlech.

K dosažení tohoto případu použití s minimální možnou režií použijte `HierarchicalStateOverrideComponent` . Tato součást implementuje hierarchické aktualizace stavu na libovolných větvích grafu scény. To znamená, že stav může být definován na libovolné úrovni grafu scény a trickles hierarchii, dokud není přepsána novým stavem nebo použit pro objekt typu list.

Můžete například zvážit model automobilu a chcete přepnout celý automobil tak, aby byl průhledný, s výjimkou součásti vnitřního modulu. Tento případ použití zahrnuje jenom dvě instance komponenty:

* První součást je přiřazena ke kořenovému uzlu modelu a zapne transparentní vykreslování pro celý automobil.
* Druhá součást je přiřazena kořenovému uzlu modulu a Přepisuje stav znovu tím, že explicitně vypíná režim zobrazení.

## <a name="features"></a>Funkce

Pevná sada stavů, které lze přepsat, jsou následující:

* **`Hidden`**: Odpovídající sítě v grafu scény jsou skryté nebo zobrazené.
* **`Tint color`**: Vykreslený objekt může mít barevný nádech s jeho individuálním barevným nádechem a váhou odstínu. Následující obrázek ukazuje barevný nádech okraje kolečka.
  
  ![Barevný nádech použitý k zapnutí objektu zeleně](./media/color-tint.png)

* **`See-through`**: Geometrie se vykresluje s poloviční transparentní, například k zobrazení vnitřních částí objektu. Následující obrázek ukazuje, že se celý automobil vykresluje v režimu zobrazení, s výjimkou červené brzdy Caliper:

  ![Režim zobrazení, který slouží k výběru vybraných objektů jako transparentní](./media/see-through.png)

  > [!IMPORTANT]
  > Efekt převádění funguje pouze v případě, že je použit [režim vykreslování](../../concepts/rendering-modes.md) *TileBasedComposition* .

* **`Shell`**: Geometrie se vykresluje jako transparentní a denasycené prostředí. Tento režim umožňuje podobu nedůležitých částí scény a přitom zachovat smysl tvaru a relativního umístění. Chcete-li změnit vzhled pro vykreslování prostředí, použijte stav [ShellRenderingSettings](shell-effect.md) . Podívejte se na následující obrázek pro model auta, který je zcela vykreslený, kromě modrých pružin:

  ![Režim prostředí používaný pro zeslabení specifických objektů](./media/shell.png)

  > [!IMPORTANT]
  > Efekt prostředí funguje pouze v případě, že je použit [režim vykreslování](../../concepts/rendering-modes.md) *TileBasedComposition* .

* **`Selected`**: Geometrie je vykreslena s [obrysem výběru](outlines.md).

  ![Možnost obrysu, která slouží k zvýraznění vybrané části](./media/selection-outline.png)

* **`DisableCollision`**: Geometrie je vyjmuta z [prostorových dotazů](spatial-queries.md). **`Hidden`** Příznak nemá vliv na příznak stavu kolizí, takže tyto dva příznaky jsou často nastaveny dohromady.

* **`UseCutPlaneFilterMask`**: Použijte bitovou masku pro jednotlivé filtry k řízení vyjmuté roviny výběru. Tento příznak určuje, zda má být použita nebo zděděna jednotlivá maska filtru ze své nadřazené položky. Bitová maska bitového filtru je nastavena prostřednictvím `CutPlaneFilterMask` Vlastnosti. Podrobné informace o tom, jak filtrování funguje, najdete v [odstavci selektivních vyjmutých ploch](cut-planes.md#selective-cut-planes). Podívejte se na následující příklad, kde pouze Tire a ráfk jsou vyjmuty, zatímco zbytek scény zůstává neovlivněný.
![Selektivní vyjmuté plochy](./media/selective-cut-planes-hierarchical-override.png)


> [!TIP]
> Jako alternativu k vypnutí viditelnosti a prostorových dotazů pro úplný dílčí graf `enabled` lze stav herního objektu přepnout. Pokud je hierarchie zakázaná, má přednost před jakýmkoli `HierarchicalStateOverrideComponent` .

## <a name="hierarchical-overrides"></a>Hierarchická přepsání

`HierarchicalStateOverrideComponent`Lze připojit na více úrovních hierarchie objektů. Vzhledem k tomu, že v entitě může existovat pouze jedna součást každého typu, každá `HierarchicalStateOverrideComponent` spravuje stavy pro skryté, předané, vybrané, barevný nádech a kolizí.

Proto je možné každý stav nastavit na jednu z těchto:

* `ForceOn` – stav je povolený pro všechny sítě v tomto uzlu a pod ním.
* `ForceOff` – stav je zakázán pro všechny sítě v tomto uzlu a pod ním.
* `InheritFromParent` – stav nemá vliv na tuto součást přepsání.

Stavy můžete změnit přímo nebo prostřednictvím `SetState` funkce:

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

```cpp
ApiHandle<HierarchicalStateOverrideComponent> component = ...;

// set one state directly
component->SetHiddenState(HierarchicalEnableState::ForceOn);

// or: set a state with the SetState function
component->SetState(HierarchicalStates::SeeThrough, HierarchicalEnableState::InheritFromParent);

// set multiple states at once with the SetState function
component->SetState(
    (HierarchicalStates)((int32_t)HierarchicalStates::Hidden | (int32_t)HierarchicalStates::DisableCollision), HierarchicalEnableState::ForceOff);

```

### <a name="tint-color"></a>Barevný nádech

`tint color`Přepsání je mírně zvláštní v tom, že je stav zapnuto/vypnuto/zděděno a vlastnost barva nádechu. Část alfa barvy odstínu definuje váhu efektu odstínování: Pokud je nastavená na 0,0, není viditelná žádná barva nádechu a pokud je nastavená na 1,0, objekt se vykreslí pomocí čisté barvy nádechu. U hodnot mezi hodnotami se výsledná barva smíchá s barvou odstínu. Barvu barevného nádechu lze změnit podle jednotlivých snímků, aby bylo možné dosáhnout barevné animace.

## <a name="performance-considerations"></a>Otázky výkonu

Instance `HierarchicalStateOverrideComponent` sama sebe nepřidává spoustu zatížení za běhu. Je ale vždy dobrým zvykem udržet počet aktivních komponent na nízké úrovni. Například při implementaci systému výběru, který zvýrazní vydaný objekt, se doporučuje odstranit komponentu při odebrání zvýraznění. Udržování komponent kolem neutrálních funkcí se může rychle přidat.

Transparentní vykreslování přináší více úloh na GPU serveru než standardní vykreslování. Pokud jsou velké části grafu scény přepnuty, aby se *zobrazilo* více vrstev geometrie, může se stát, že se zobrazí problém s výkonem. Totéž platí pro objekty s [obrysy výběru](../../overview/features/outlines.md#performance) a pro [vykreslování prostředí](../../overview/features/shell-effect.md#performance) . 

## <a name="api-documentation"></a>Dokumentace k rozhraní API

* [Třída C# HierarchicalStateOverrideComponent](/dotnet/api/microsoft.azure.remoterendering.hierarchicalstateoverridecomponent)
* [Třída C++ HierarchicalStateOverrideComponent](/cpp/api/remote-rendering/hierarchicalstateoverridecomponent)

## <a name="next-steps"></a>Další kroky

* [Psán](../../overview/features/outlines.md)
* [Režimy vykreslování](../../concepts/rendering-modes.md)
* [Prostorové dotazy](../../overview/features/spatial-queries.md)