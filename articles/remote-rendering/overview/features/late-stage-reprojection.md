---
title: Reprojekce pozdní fáze
description: Informace o reprojekci a způsobu použití v pozdní fázi
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 8d42087008f1812bc3713456025ed3be351d0917
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84022176"
---
# <a name="late-stage-reprojection"></a>Reprojekce pozdní fáze

*Reprojekce za pozdní fázi* (LSR) je hardwarová funkce, která pomáhá stabilizovat hologramy, když se uživatel přesune.

Očekává se, že statické modely vizuálně udržují jejich polohu při přesunu kolem nich. Pokud se jeví jako nestabilní, může toto chování pomoci při LSR potížích. Pamatujte na to, že další dynamické transformace, jako jsou animace nebo zobrazení rozbalení, můžou toto chování maskovat.

Můžete si vybrat mezi dvěma různými LSR režimy, zejména **PLANÁRNÍ LSR** nebo **Depth LSR**. Který z nich je aktivní, určuje, jestli klientská aplikace odesílá vyrovnávací paměť hloubky.

Oba režimy LSR zlepšují stabilitu hologramů, i když mají odlišná omezení. Začněte tím, že zkusíte LSRnou hloubku, protože pravděpodobně poskytuje lepší výsledky ve většině případů.

## <a name="choose-lsr-mode-in-unity"></a>Výběr režimu LSR v Unity

V editoru Unity pokračujte na *:::no-loc text="File > Build Settings":::* . *:::no-loc text="Player Settings":::* V levém dolním rohu vyberte, *:::no-loc text="Player > XR Settings > Virtual Reality SDKs > Windows Mixed Reality":::* jestli **:::no-loc text="Enable Depth Buffer Sharing":::** je zaškrtnuté políčko:

![Příznak povoleného sdílení vyrovnávací paměti hloubky](./media/unity-depth-buffer-sharing-enabled.png)

Pokud je, vaše aplikace bude používat LSR hloubku, jinak bude používat planární LSR.

## <a name="depth-lsr"></a>LSR hloubky

Aby LSRa hloubka fungovala, musí klientská aplikace dodat platnou hloubkovou vyrovnávací paměť, která obsahuje všechny relevantní geometrie, které by měly být během LSR zváženy.

LSR hloubky se pokusí stabilizovat snímek videa na základě obsahu poskytnuté vyrovnávací paměti s hloubkou. Vzhledem k tomu, že obsah, který se na něj nevykresluje, jako jsou průhledné objekty, nejde upravit pomocí LSR a může zobrazovat artefakty nestability a reprojekce.

## <a name="planar-lsr"></a>Planární LSR

Planární LSR nemá informace o hloubkě pro pixel, jako má hloubka LSR. Místo toho bude znovu promítnout veškerý obsah na základě roviny, kterou je nutné poskytnout každému snímku.

Planární LSR reprojektuje tyto objekty nejlépe, které leží blízko dodané roviny. Dalším objektem je, tím větší nestabilita bude vypadat. I když je hloubka LSRa lepší při reprojektech objektů v různých hloubkách, může planární LSR fungovat lépe, aby se obsah dobře zarovnal s rovinou.

### <a name="configure-planar-lsr-in-unity"></a>Konfigurace planární LSR v Unity

Parametry roviny jsou odvozeny od třídy, která se označuje jako vstupní *bod*, který je nutné zadat každý rámec `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame` . Podrobnosti najdete v tématu [rozhraní API pro zaměření služby Unity](https://docs.microsoft.com/windows/mixed-reality/focus-point-in-unity) . Pokud nenastavíte fokus, bude pro vás zvolena možnost záložního bodu. Automatické nouzové řešení často vede k neoptimálním výsledkům.

Fokus můžete vypočítat sami, ale může to mít smysl na základu vypočítaného hostitelem vzdáleného vykreslování. Zavolejte `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` k získání. Zobrazí se výzva k zadání souřadnicového rámce, ve kterém se bude soustředit pozice kurzoru. Ve většině případů budete chtít jenom zadat výsledek z `UnityEngine.XR.WSA.WorldManager.GetNativeISpatialCoordinateSystemPtr` tohoto místa.

Obvykle klient i hostitel vykreslí obsah, na který druhá strana neví, například prvky uživatelského rozhraní klienta. Proto může být vhodné zkombinovat bod vzdáleného zaměření s místně vypočítaným objektem.

Body výběru vypočítané ve dvou po sobě jdoucích snímcích můžou být poměrně odlišné. Stačí je jednoduše používat tak, jak je možné, že se k nim můžete pohybovat na hologramech. Aby se zabránilo tomuto chování, je vhodné provést interpolaci mezi předchozími a aktuálními body výběru.

## <a name="next-steps"></a>Další kroky

* [Dotazy na výkon na straně serveru](performance-queries.md)
