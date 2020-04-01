---
title: Obnovení služeb při selhání a zotavení po havárii pro zařízení řady StorSimple 8000
description: Zjistěte, jak převzetí služeb při selhání zařízení StorSimple pro sebe, jiné fyzické zařízení nebo cloudzařízení.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: 179bc5cdf982792f41e0dec209341f346959a31a
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397510"
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-8000-series-device"></a>Převzetí služeb při selhání a zotavení po havárii pro zařízení StorSimple řady 8000

## <a name="overview"></a>Přehled

Tento článek popisuje funkci převzetí služeb při selhání zařízení pro zařízení řady StorSimple 8000 a jak lze tuto funkci použít k obnovení zařízení StorSimple, pokud dojde k havárii. StorSimple používá převzetí služeb při selhání zařízení k migraci dat ze zdrojového zařízení v datovém centru do jiného cílového zařízení. Pokyny v tomto článku platí pro storSimple 8000 řady fyzických zařízení a cloudových zařízení se systémem verze softwaru aktualizace 3 a novější.

StorSimple používá okno **Devices** ke spuštění funkce převzetí služeb při selhání zařízení v případě havárie. V tomto okně jsou uvedena všechna zařízení StorSimple připojená ke službě Správce zařízení StorSimple.

![Čepel zařízení](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)


## <a name="disaster-recovery-dr-and-device-failover"></a>Zotavení po havárii (DR) a převzetí služeb při selhání zařízení

Ve scénáři zotavení po havárii (DR) primární zařízení přestane fungovat. StorSimple používá primární zařízení jako _zdroj_ a přesune přidružená cloudová data do jiného _cílového_ zařízení. Tento proces se označuje jako *převzetí služeb při selhání*. Následující obrázek znázorňuje proces převzetí služeb při selhání.

![Co se stane při převzetí služeb při selhání zařízení?](./media/storsimple-8000-device-failover-disaster-recovery/failover-dr-flow.png)

Cílové zařízení pro převzetí služeb při selhání může být fyzické zařízení nebo dokonce cloudové zařízení. Cílové zařízení může být umístěno ve stejném nebo jiném zeměpisném umístění než zdrojové zařízení.

Během převzetí služeb při selhání můžete vybrat kontejnery svazků pro migraci. StorSimple pak změní vlastnictví těchto kontejnerů svazku ze zdrojového zařízení na cílové zařízení. Jakmile kontejnery svazku změnit vlastnictví, StorSimple odstraní tyto kontejnery ze zdrojového zařízení. Po dokončení odstranění můžete cílové zařízení vrátit zpět. _Navrácení služeb po selhání_ převede vlastnictví zpět do původního zdrojového zařízení.

### <a name="cloud-snapshot-used-during-device-failover"></a>Snímek cloudu použitý při převzetí služeb při selhání zařízení

Po zotavení po havárii se poslední záloha v cloudu používá k obnovení dat do cílového zařízení. Další informace o cloudových snímcích najdete [v tématu Použití služby Správce zařízení StorSimple k ručnímu zálohování](storsimple-8000-manage-backup-policies-u2.md#take-a-manual-backup).

V řadě StorSimple 8000 jsou zásady zálohování přidruženy k zálohování. Pokud existuje více zásad zálohování pro stejný svazek, pak StorSimple vybere zásady zálohování s největším počtem svazků. StorSimple pak používá nejnovější zálohu z vybrané zásady zálohování k obnovení dat v cílovém zařízení.

Předpokládejme, že existují dvě zásady zálohování, *defaultPol* a *customPol*:

* *defaultPol*: Jeden *svazek, vol1*, běží denně od 22:30.
* *customPol:* Čtyři svazky, *vol1*, *vol2*, *vol3*, *vol4*, běží denně od 22:00.

V tomto případě StorSimple upřednostňuje konzistenci selhání a používá *customPol,* protože má více svazků. Nejnovější záloha z této zásady se používá k obnovení dat. Další informace o vytváření a správě zásad zálohování naleznete v části [Správa zásad zálohování pomocí služby StorSimple Device Manager](storsimple-8000-manage-backup-policies-u2.md).

## <a name="common-considerations-for-device-failover"></a>Běžné důležité informace o převzetí služeb při selhání zařízení

Před převzetím služeb při selhání zařízení zkontrolujte následující informace:

* Před spuštěním převzetí služeb při selhání zařízení musí být všechny svazky v kontejnerech svazků offline. V neplánované převzetí služeb při selhání svazky StotSimple automaticky přejdou do offline. Pokud však provádíte plánované převzetí služeb při selhání (chcete-li otestovat zotavení po havárii), je nutné převést všechny svazky do úplaty.
* Pro zotavení po havárii jsou uvedeny pouze kontejnery svazků, které mají přidružený snímek cloudu. Musí existovat alespoň jeden kontejner svazku s přidruženým cloudovým snímkem, aby bylo možné obnovit data.
* Pokud existují cloudové snímky, které se rozprostírají navíce kontejnerů svazků, StorSimple převezme služby při selhání těchto kontejnerů svazků jako sadu. Ve výjimečných případech pokud existují místní snímky, které se rozprostírají napříč více kontejnery svazků, ale přidružené cloudové snímky ne, StorSimple provede převzetí služeb při selhání místní snímky a místní data se ztratí po zotavení po havárii.
* Dostupná cílová zařízení pro zotavení po havárii jsou zařízení, která mají dostatek místa pro vybrané kontejnery svazků. Všechna zařízení, která nemají dostatek místa, nejsou uvedena jako cílová zařízení.
* Po zotavení po havárii (po omezenou dobu) může být výrazně ovlivněn výkon přístupu k datům, protože zařízení potřebuje přístup k datům z cloudu a jejich místní uložení.

#### <a name="device-failover-across-software-versions"></a>Převzetí služeb při selhání zařízení ve verzích softwaru

Služba StorSimple Device Manager v nasazení může mít více zařízení, fyzických i cloudových, všechna s různými verzemi softwaru.

V následující tabulce můžete určit, zda můžete přepojit služby při selhání nebo zpět na jiné zařízení s jinou verzí softwaru a jak se typy svazků chovají během zotavení po havárii.

#### <a name="failover-and-failback-across-software-versions"></a>Převzetí služeb při selhání a navrácení služeb po selhání ve verzích softwaru

| Převzetí služeb při selhání / vrácení služeb při selhání z | Fyzické zařízení | Cloudové zařízení |
| --- | --- | --- |
| Aktualizace 3 na aktualizaci 4 |Vrstvené svazky převzetí služeb při selhání jako vrstvené. <br></br>Místně vázaných svazků převzetí služeb při selhání jako místně připnutý. <br></br> Po převzetí služeb při selhání, když pořídíte snímek na zařízení Aktualizace 4, [spustí se sledování založené na heatmap.](storsimple-update4-release-notes.md#whats-new-in-update-4) |Místně vázaných svazků převzetí služeb při selhání jako vrstvené. |
| Aktualizace 4 na aktualizaci 3 |Vrstvené svazky převzetí služeb při selhání jako vrstvené. <br></br>Místně vázaných svazků převzetí služeb při selhání jako místně připnutý. <br></br> Zálohy používané k obnovení zachovávají metadata heatmap. <br></br>Sledování založené na heatmap není k dispozici v aktualizaci 3 po navrácení služeb po selhání. |Místně vázaných svazků převzetí služeb při selhání jako vrstvené. |


## <a name="device-failover-scenarios"></a>Scénáře převzetí služeb při selhání zařízení

Pokud dojde k havárii, můžete se rozhodnout převzetí služeb při selhání zařízení StorSimple:

* [K fyzickému zařízení](storsimple-8000-device-failover-physical-device.md).
* [Pro sebe](storsimple-8000-device-failover-same-device.md).
* [Do cloudového zařízení](storsimple-8000-device-failover-cloud-appliance.md).

Předchozí články poskytují podrobné kroky pro každý z výše uvedených případů převzetí služeb při selhání.


## <a name="failback"></a>Navrácení služeb po obnovení

Pro aktualizaci 3 a novější verze StorSimple také podporuje navrácení služeb po selhání. Navrácení služeb po selhání je právě opačná převzetí služeb při selhání, cíl se stane zdrojem a původní zdrojové zařízení během převzetí služeb při selhání se nyní stane cílovým zařízením. 

Během navrácení služeb po obnovení StorSimple znovu synchronizuje data zpět do primárního umístění, zastaví vstupně-v a aplikační aktivity a přechody zpět do původního umístění.

Po dokončení převzetí služeb při selhání, StorSimple provede následující akce:

* StorSimple čistí kontejnery svazku, které jsou převzetí selhání přes ze zdrojového zařízení.
* StorSimple iniciuje úlohu na pozadí na kontejner svazku (převzetí selhání) na zdrojovém zařízení. Pokud se pokusíte o vrácení se selháním během probíhající úlohy, obdržíte oznámení v tomto smyslu. Počkejte, až bude úloha dokončena, a spusťte navrácení služeb po selhání.
* Doba potřebná k dokončení odstranění kontejnerů svazků závisí na různých faktorech, jako je množství dat, stáří dat, počet záloh a šířka pásma sítě dostupná pro operaci.

Pokud plánujete test převzetí služeb při selhání nebo testování navrácení služeb po selhání, doporučujeme otestovat kontejnery svazku s menším množstvím dat (Gbs). Obvykle můžete spustit navrácení služeb po selhání 24 hodin po dokončení převzetí služeb při selhání.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Otázka: **Co se stane, pokud zotavení po havárii selže nebo bude mít částečný úspěch?**

A. Pokud zotavení po havárii selže, doporučujeme to zkusit znovu. Druhá úloha převzetí služeb při selhání zařízení si je vědoma průběhu první úlohy a začíná od tohoto okamžiku dále.

Otázka: **Mohu odstranit zařízení, zatímco probíhá převzetí služeb při selhání zařízení?**

A. Zařízení nelze odstranit v době, kdy probíhá zotavení po havárii. Zařízení můžete odstranit až po dokončení zotavení po havárii. Můžete sledovat průběh úlohy převzetí služeb při selhání zařízení v okně **Úlohy.**

Otázka: **Kdy se spustí uvolňování paměti na zdrojovém zařízení tak, aby byla odstraněna místní data na zdrojovém zařízení?**

A. Uvolňování paměti je povolena na zdrojovém zařízení pouze po úplném vyčištění zařízení. Vyčištění zahrnuje vyčištění objektů, které převzaly převzetí ze zdrojového zařízení, jako jsou svazky, záložní objekty (nikoli data), kontejnery svazků a zásady.

Otázka: **Co se stane, pokud úloha odstranění přidružená ke kontejneru svazků ve zdrojovém zařízení selže?**

A.  Pokud se úloha odstranění nezdaří, můžete kontejnery svazků odstranit ručně. V okně **Zařízení** vyberte zdrojové zařízení a klepněte na **položku Kontejnery svazků**. Vyberte kontejnery svazků, které jste provedli převzetí mne, a v dolní části okna klepněte na tlačítko **Odstranit**. Po odstranění všech kontejnerů s podporou převzetí služeb při selhání nad kontejnery svazků na zdrojovém zařízení můžete spustit navrácení služeb po selhání. Další informace naleznete v řešení [Odstranění kontejneru svazku](storsimple-8000-manage-volume-containers.md#delete-a-volume-container).

## <a name="business-continuity-disaster-recovery-bcdr"></a>Zotavení po havárii kontinuity provozu (BCDR)

Scénář zotavení po havárii kontinuity podnikání (BCDR) nastane, když přestane fungovat celé datové centrum Azure. Tento scénář může ovlivnit službu StorSimple Device Manager a přidružená zařízení StorSimple.

Pokud zařízení StorSimple bylo zaregistrováno těsně před došlo k havárii, pak toto zařízení může být nutné podstoupit obnovení továrního nastavení. Po havárii se zařízení StorSimple zobrazí na webu Azure Portal jako offline. Toto zařízení musí být odstraněno z portálu. Obnovte výchozí hodnotu zařízení z výroby a zaregistrujte jej znovu u služby.

## <a name="next-steps"></a>Další kroky

Pokud jste připraveni provést převzetí služeb při selhání zařízení, zvolte jeden z následujících scénářů pro podrobné pokyny:

- [Převzetí služeb při selhání do jiného fyzického zařízení](storsimple-8000-device-failover-physical-device.md)
- [Převzetí služeb při selhání na stejné zařízení](storsimple-8000-device-failover-same-device.md)
- [Převzetí služeb při selhání do cloudového zařízení StorSimple](storsimple-8000-device-failover-cloud-appliance.md)

Pokud jste zařízení provedli selháním, vyberte jednu z následujících možností:

* [Deaktivujte nebo odstraňte zařízení StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* [Ke správě zařízení StorSimple použijte službu StorSimple](storsimple-8000-manager-service-administration.md)Device Manager .

