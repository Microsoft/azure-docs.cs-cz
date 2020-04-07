---
title: Přepsání hierarchického stavu
description: Vysvětluje koncept hierarchického stavu přepsat součásti.
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: f3be073857cc8583669ab26f306760478479e2ae
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680788"
---
# <a name="hierarchical-state-override"></a>Přepsání hierarchického stavu

V mnoha případech je nutné dynamicky měnit vzhled částí [modelu](../../concepts/models.md), například skrývat podgrafy nebo přepínat součásti na průhledné vykreslování. Změna materiálů jednotlivých zapojených částí není praktická, protože vyžaduje iteřovat celý graf scény a spravovat klonování materiálu a přiřazení na každém uzlu.

Chcete-li provést tento případ použití s `HierarchicalStateOverrideComponent`co nejmenší režií, použijte . Tato komponenta implementuje aktualizace hierarchického stavu na libovolných větvích grafu scény. To znamená, že stav může být definován na libovolné úrovni v grafu scény a stéká dolů hierarchie, dokud je buď přepsán a nový stav nebo použita na objekt listu.

Jako příklad zvažte model vozu a chcete přepnout celé auto tak, aby bylo průhledné, s výjimkou vnitřní části motoru. Tento případ použití zahrnuje pouze dvě instance komponenty:

* První komponenta je přiřazena kořenovému uzlu modelu a zapne transparentní vykreslování pro celé auto.
* Druhá součást je přiřazena kořenovému uzlu motoru a znovu přepíše stav explicitním vypnutím režimu průhledného režimu.

## <a name="features"></a>Funkce

Pevná sada stavů, které mohou být přepsány, jsou:

* **Skryté**: Příslušná ok v grafu scény jsou skrytá nebo zobrazená.
* **Barva tónu**: Vykreslený objekt může být barevně tónovaný s jeho individuální barvou tónu a tloušťkou tónu. Na obrázku níže je zobrazenbarevný tónování ráfku kola.
  
  ![Barevný barevný tón](./media/color-tint.png)

* **Průhledné**: Geometrie je vykreslena poloprůhledně, například pro zobrazení vnitřních částí objektu. Následující obrázek znázorňuje celé auto vykreslené v režimu průhledných, s výjimkou červeného brzdového třmenu:

  ![Průsvitný](./media/see-through.png)

  > [!IMPORTANT]
  > Efekt průhledný funguje pouze v případě, že je použit [režim vykreslování](../../concepts/rendering-modes.md) *TileBasedComposition.*

* **Vybraná**: Geometrie se vykreslí s [obrysem výběru](outlines.md).

  ![Obrys výběru](./media/selection-outline.png)

* **DisableCollision**: Geometrie je osvobozena od [prostorových dotazů](spatial-queries.md). **Příznak Skrytý** nevypne kolize, takže tyto dvě vlajky jsou často nastaveny společně.

## <a name="hierarchical-overrides"></a>Hierarchická přepsání

Lze `HierarchicalStateOverrideComponent` připojit na více úrovních hierarchie objektů. Vzhledem k tomu, že v entitě `HierarchicalStateOverrideComponent` může být pouze jedna součást každého typu, každý spravuje stavy pro skryté, průhledné, vybrané, barevné tóny a kolize.

Proto každý stav lze nastavit na jeden z:

* `ForceOn`- stav je povolen pro všechny sítě na a pod tímto uzlem
* `ForceOff`- stav je zakázán pro všechna oky na tomto uzlu a pod ním
* `InheritFromParent`- stav není touto komponentou přepsání ovlivněn.

Stavy můžete změnit přímo `SetState` nebo prostřednictvím funkce:

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

### <a name="tint-color"></a>Barva tónu

Přepsání barvy tónu je mírně zvláštní v tom, že je jak on / off / inherit stavu a tón barva vlastnost. Alfa část barvy tónu definuje hmotnost tónovacího efektu: Pokud je nastavena na 0,0, není viditelná žádná barva tónu a pokud je nastavena na 1,0, objekt bude vykreslen s čistou barvou tónu. U mezihodnot bude konečná barva smíchána s barvou tónu. Barvu tónu lze změnit na základě počtu snímků, aby se dosáhlo barevné animace.

## <a name="performance-considerations"></a>Otázky výkonu

Instance sama `HierarchicalStateOverrideComponent` o sobě nepřidává mnoho režie runtime. Je však vždy vhodné udržovat nízký počet aktivních součástí. Například při implementaci systému výběru, který zvýrazní vybraný objekt, se doporučuje odstranit komponentu při odebrání zvýraznění. Udržování komponent y neutrálních prvků může rychle přidat.

Transparentní vykreslování klade více zatížení na gpu serveru než standardní vykreslování. Pokud velké části grafu scény jsou přepnuty do *průhledných*, s mnoha vrstvami geometrie jsou viditelné, může se stát kritickým bodem výkonu. Totéž platí pro objekty s [obrysy výběru](../../overview/features/outlines.md#performance).

## <a name="next-steps"></a>Další kroky

* [Obrysy](../../overview/features/outlines.md)
* [Režimy vykresl](../../concepts/rendering-modes.md)
* [Prostorové dotazy](../../overview/features/spatial-queries.md)
