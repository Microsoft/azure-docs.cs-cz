---
title: Reprojekce pozdního jeviště
description: Informace o reprojekci pozdní fáze a o tom, jak ji používat.
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 4aa1148e544ff3451aa1cb956bc4a5fb932b9611
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680983"
---
# <a name="late-stage-reprojection"></a>Reprojekce pozdního jeviště

*Pozdní fáze Reprojekce* (LSR) je hardwarová funkce, která pomáhá stabilizovat hologramy, když se uživatel pohybuje.

Očekává se, že statické modely si při pohybu po nich vizuálně zachovávají svou pozici. Pokud se zdají být nestabilní, toto chování může naznačovat problémy LSR. Mějte na paměti, že toto chování mohou maskovat další dynamické transformace, jako jsou animace nebo zobrazení rozpadu.

Můžete si vybrat mezi dvěma různými režimy LSR, a to **Planární LSR** nebo **Depth LSR**. Který z nich je aktivní, je určen tím, zda klientská aplikace odešle hloubkovou vyrovnávací paměť.

Oba režimy LSR zlepšují stabilitu hologramu, i když mají různá omezení. Začněte tím, že se pokusí hloubka LSR, jak to je pravděpodobně dává lepší výsledky ve většině případů.

## <a name="choose-lsr-mode-in-unity"></a>Volba režimu LSR v unity

V editoru Unity přejděte na *Soubor > nastavení sestavení*. V levém dolním rohu vyberte *Nastavení přehrávače* a zaškrtněte v části *> Nastavení XR přehrávače > sady Virtual Reality > windows smíšené realitě,* zda je zaškrtnuto **políčko Povolit sdílení hloubkové vyrovnávací paměti:**

![Příznak Povoleno sdílení hloubkové vyrovnávací paměti](./media/unity-depth-buffer-sharing-enabled.png)

Pokud ano, vaše aplikace bude používat Hloubka LSR, jinak bude používat Planární LSR.

## <a name="depth-lsr"></a>Hloubka LSR

Aby hloubka LSR fungovala, musí klientská aplikace poskytnout platnou hloubkovou vyrovnávací paměť, která obsahuje všechny příslušné geometrie, které je třeba zvážit během LSR.

Hloubka LSR se pokouší stabilizovat snímek videa na základě obsahu dodané hloubkové vyrovnávací paměti. V důsledku toho obsah, který nebyl vykreslen na něj, jako jsou průhledné objekty, nelze upravit LSR a může zobrazit nestabilitu a reprojection artefakty.

## <a name="planar-lsr"></a>Rovinná LSR

Planární LSR nemá informace o hloubce pro jeden pixel, jako hloubka LSR dělá. Místo toho znovu roztážní veškerý obsah na základě roviny, kterou je nutné zadat každý snímek.

Rovinné LSR přetavit ty objekty nejlepší, které leží v blízkosti zadané roviny. Čím dál je objekt, tím je nestabilní, tím bude vypadat. Zatímco hloubka LSR je lepší při přemýšlení objektů v různých hloubkách, rovinná LSR může lépe fungovat pro obsah, který se dobře vyrovná s rovinou.

### <a name="configure-planar-lsr-in-unity"></a>Konfigurace rovinné lsr v jednotě

Parametry roviny jsou odvozeny z takzvaného *zaostřovacího* `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame`bodu , který musíte poskytnout každému snímku . Podrobnosti najdete v [rozhraní API prioritního bodu unity.](https://docs.microsoft.com/windows/mixed-reality/focus-point-in-unity) Pokud nenastavíte zaostřovací bod, bude pro vás vybrán záložní bod. Tento automatický záložní často však vede k neoptimálním výsledkům.

Zaostřovací bod můžete vypočítat sami, i když může mít smysl založit jej na tom, který vypočítá hostitel vzdáleného vykreslování. Zavolejte, `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` abyste to získali. Budete vyzváni k zadání souřadnicového rámce, ve kterém se vyjádří zaostřovací bod. Ve většině případů budete chtít poskytnout výsledek `UnityEngine.XR.WSA.WorldManager.GetNativeISpatialCoordinateSystemPtr` zde.

Klient i hostitel obvykle vykreslují obsah, o kterém si druhá strana není vědoma, například prvky uživatelského rozhraní v klientovi. Proto může mít smysl kombinovat vzdálený zaostřovací bod s místně vypočteným bodem.

Zaostřovací body vypočítané ve dvou po sobě jdoucích snímcích mohou být zcela odlišné. Jednoduše jejich použití as-je může vést k hologramy se zdají být skákání kolem. Chcete-li tomuto chování zabránit, interpolace mezi předchozím a aktuálním zaostřovacím bodem je vhodné.

## <a name="next-steps"></a>Další kroky

* [Dotazy na výkon na straně serveru](performance-queries.md)
