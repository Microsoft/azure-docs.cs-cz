---
title: Přepsání hierarchického stavu
description: Vysvětluje pojem hierarchické komponenty přepisu stavu.
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: f3be073857cc8583669ab26f306760478479e2ae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680788"
---
# <a name="hierarchical-state-override"></a>Přepsání hierarchického stavu

V mnoha případech je nutné dynamicky měnit vzhled částí [modelu](../../concepts/models.md), například skrýt Podgrafy nebo přepínat části na transparentní vykreslování. Změna materiálů jednotlivých zúčastněných částí není praktická, protože vyžaduje iteraci v celém grafu scény a správu klonování a přiřazování materiálů na jednotlivých uzlech.

K dosažení tohoto případu použití s minimální možnou režií použijte `HierarchicalStateOverrideComponent`. Tato součást implementuje hierarchické aktualizace stavu na libovolných větvích grafu scény. To znamená, že stav může být definován na libovolné úrovni grafu scény a trickles hierarchii, dokud není přepsána novým stavem nebo použit pro objekt typu list.

Můžete například zvážit model automobilu a chcete přepnout celý automobil tak, aby byl průhledný, s výjimkou součásti vnitřního modulu. Tento případ použití zahrnuje jenom dvě instance komponenty:

* První součást je přiřazena ke kořenovému uzlu modelu a zapne transparentní vykreslování pro celý automobil.
* Druhá součást je přiřazena kořenovému uzlu modulu a Přepisuje stav znovu tím, že explicitně vypíná režim zobrazení.

## <a name="features"></a>Funkce

Pevná sada stavů, které lze přepsat, jsou následující:

* **Skryté**: odpovídající sítě v grafu scény jsou skryté nebo zobrazené.
* Barevný **nádech**: vykreslený objekt může mít barevný nádech s jeho individuální barvou a váhou odstínu. Následující obrázek ukazuje barevný nádech okraje kolečka.
  
  ![Barevný nádech](./media/color-tint.png)

* **Přečtěte si téma**: geometrie se vykresluje jako příklad, například k odhalení vnitřních částí objektu. Následující obrázek ukazuje, že se celý automobil vykresluje v režimu zobrazení, s výjimkou červené brzdy Caliper:

  ![Viz-through](./media/see-through.png)

  > [!IMPORTANT]
  > Efekt převádění funguje pouze v případě, že je použit [režim vykreslování](../../concepts/rendering-modes.md) *TileBasedComposition* .

* **Vybráno**: geometrie je vykreslena s [obrysem výběru](outlines.md).

  ![Obrys výběru](./media/selection-outline.png)

* **DisableCollision**: geometrie je vyjmuta z [prostorových dotazů](spatial-queries.md). **Skrytý** příznak nevypne kolize, takže tyto dva příznaky jsou často nastaveny dohromady.

## <a name="hierarchical-overrides"></a>Hierarchická přepsání

`HierarchicalStateOverrideComponent` Lze připojit na více úrovních hierarchie objektů. Vzhledem k tomu, že v entitě může existovat pouze jedna součást každého typu `HierarchicalStateOverrideComponent` , každá spravuje stavy pro skryté, předané, vybrané, barevný nádech a kolizí.

Proto je možné každý stav nastavit na jednu z těchto:

* `ForceOn`– stav je povolený pro všechny sítě v tomto uzlu a pod ním.
* `ForceOff`– stav je zakázán pro všechny sítě v tomto uzlu a pod ním.
* `InheritFromParent`– stav nemá vliv na tuto součást přepsání.

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

### <a name="tint-color"></a>Barevný nádech

Přepsání barvy odstínu je mírně zvláštní v tom, že existuje stav zapnuto/vypnuto/zděděno a barevný nádech. Část alfa barvy odstínu definuje váhu efektu odstínování: Pokud je nastavená na 0,0, není viditelná žádná barva nádechu a pokud je nastavená na 1,0, objekt se vykreslí pomocí čisté barvy nádechu. U hodnot mezi hodnotami se výsledná barva smíchá s barvou odstínu. Barvu barevného nádechu lze změnit podle jednotlivých snímků, aby bylo možné dosáhnout barevné animace.

## <a name="performance-considerations"></a>Otázky výkonu

Instance `HierarchicalStateOverrideComponent` sama sebe nepřidává spoustu zatížení za běhu. Je ale vždy dobrým zvykem udržet počet aktivních komponent na nízké úrovni. Například při implementaci systému výběru, který zvýrazní vydaný objekt, se doporučuje odstranit komponentu při odebrání zvýraznění. Udržování komponent kolem neutrálních funkcí se může rychle přidat.

Transparentní vykreslování přináší více úloh na GPU serveru než standardní vykreslování. Pokud jsou velké části grafu scény přepnuty, aby se *zobrazilo*více vrstev geometrie, může se stát, že se zobrazí problém s výkonem. Totéž platí pro objekty s [obrysy výběru](../../overview/features/outlines.md#performance).

## <a name="next-steps"></a>Další kroky

* [Psán](../../overview/features/outlines.md)
* [Režimy vykreslování](../../concepts/rendering-modes.md)
* [Prostorové dotazy](../../overview/features/spatial-queries.md)
