---
title: Převzetí služeb při selhání a zotavení po havárii pro zařízení řady StorSimple 8000
description: Přečtěte si, jak převzít služby při selhání zařízení StorSimple na sebe, na jiné fyzické zařízení nebo na cloudové zařízení.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: dffa059b18e159d04b5e3bb8555dabf801ede692
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017113"
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-8000-series-device"></a>Převzetí služeb při selhání a zotavení po havárii pro zařízení StorSimple řady 8000

## <a name="overview"></a>Přehled

Tento článek popisuje funkci převzetí služeb při selhání zařízení pro zařízení řady StorSimple 8000 a způsob, jak se tato funkce dá použít k obnovení zařízení StorSimple, pokud dojde k havárii. StorSimple používá převzetí služeb při selhání zařízení k migraci dat ze zdrojového zařízení v datacentru do jiného cílového zařízení. Pokyny v tomto článku se vztahují na fyzická zařízení řady StorSimple 8000 a cloudová zařízení s verzemi softwaru Update 3 a novější.

StorSimple pomocí okna **zařízení** spustí funkci převzetí služeb při selhání zařízení v případě havárie. Toto okno obsahuje seznam všech zařízení StorSimple připojených ke službě StorSimple Správce zařízení.

![Okno zařízení](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)


## <a name="disaster-recovery-dr-and-device-failover"></a>Zotavení po havárii (DR) a převzetí služeb při selhání zařízení

Ve scénáři zotavení po havárii primární zařízení přestane fungovat. StorSimple používá primární zařízení jako _zdroj_ a přesouvá přidružená cloudová data do jiného _cílového_ zařízení. Tento proces se označuje jako *převzetí služeb při selhání*. Postup převzetí služeb při selhání znázorňuje následující obrázek.

![Co se stane při převzetí služeb zařízení při selhání?](./media/storsimple-8000-device-failover-disaster-recovery/failover-dr-flow.png)

Cílovým zařízením pro převzetí služeb při selhání může být fyzické zařízení nebo i cloudové zařízení. Cílové zařízení se může nacházet ve stejném nebo jiném geografickém umístění než zdrojové zařízení.

Během převzetí služeb při selhání můžete vybrat kontejnery svazků pro migraci. StorSimple pak změní vlastnictví těchto kontejnerů svazků ze zdrojového zařízení na cílové zařízení. Jakmile kontejnery svazků změní vlastnictví, StorSimple tyto kontejnery ze zdrojového zařízení odstraní. Po dokončení odstraňování můžete cílové zařízení navrátit do pozadí. _Navrácení služeb po obnovení_ převede vlastnictví zpátky na původní zdrojové zařízení.

### <a name="cloud-snapshot-used-during-device-failover"></a>Snímek cloudu použitý při převzetí služeb při selhání zařízení

Po zotavení po havárii se nejnovější cloudové zálohování používá k obnovení dat na cílovém zařízení. Další informace o cloudových snímcích najdete v tématu [použití služby StorSimple Správce zařízení k provedení ručního zálohování](storsimple-8000-manage-backup-policies-u2.md#take-a-manual-backup).

V řadě StorSimple 8000 jsou zásady zálohování přidružené k zálohování. Pokud je pro stejný svazek k dispozici více zásad zálohování, pak StorSimple vybere zásadu zálohování s největším počtem svazků. StorSimple pak pomocí nejaktuálnějšího zálohování z vybraných zásad zálohování obnoví data na cílovém zařízení.

Předpokládejme, že existují dvě zásady zálohování, *defaultPol* a *customPol*:

* *defaultPol*: jeden svazek, *vol1*, běží denně od 10:30. odp.
* *customPol*: čtyři svazky, *vol1*, *VOL2*, *vol3*, *VOL4*, běží denně od 10:00./odp..

V takovém případě StorSimple priorit pro konzistenci havárií a používá *customPol* , protože má více svazků. Poslední záloha z těchto zásad slouží k obnovení dat. Další informace o tom, jak vytvářet a spravovat zásady zálohování, najdete v tématu [použití služby StorSimple Správce zařízení ke správě zásad zálohování](storsimple-8000-manage-backup-policies-u2.md).

## <a name="common-considerations-for-device-failover"></a>Běžné důležité informace o převzetí služeb při selhání zařízení

Před převzetím služeb při selhání zařízení si přečtěte následující informace:

* Před spuštěním převzetí služeb zařízení musí být všechny svazky v kontejnerech svazků offline. V neplánovaném převzetí služeb při selhání budou svazky StotSimple automaticky přejít do offline režimu. Pokud ale provádíte plánované převzetí služeb při selhání (k otestování DR), musíte všechny svazky převést do režimu offline.
* Pro zotavení po havárii jsou uvedené pouze kontejnery svazků, které mají přidružený cloudový snímek. Aby bylo možné obnovit data, musí existovat alespoň jeden kontejner svazků s přidruženým snímkem cloudu.
* Pokud jsou cloudové snímky, které se nacházejí v rámci více kontejnerů svazků, StorSimple převezme tyto kontejnery svazků jako sadu. Pokud existují místní snímky, které jsou rozloženy na více kontejnerech svazků, ale přidružené cloudové snímky neStorSimple, převezmou při selhání místní snímky a po zotavení po havárii dojde ke ztrátě místních dat.
* Dostupná cílová zařízení pro DR jsou zařízení, která mají dostatek místa k umístění vybraných kontejnerů svazků. Všechna zařízení, která nemají dostatek místa, nejsou uvedena jako cílová zařízení.
* Po zotavení po havárii (po omezené době trvání) může být výkon přístupu k datům významně ovlivněný, protože zařízení potřebuje přístup k datům z cloudu a ukládá je místně.

#### <a name="device-failover-across-software-versions"></a>Převzetí služeb při selhání u zařízení v různých verzích softwaru

Služba StorSimple Správce zařízení v nasazení může mít více zařízení (fyzických i cloudových), ve kterých běží různé verze softwaru.

Pomocí následující tabulky zjistíte, jestli můžete převzít služby při selhání nebo navrácení služeb po obnovení do jiného zařízení s jinou verzí softwaru a jak se typy svazků chovají během zotavení po havárii.

#### <a name="failover-and-failback-across-software-versions"></a>Převzetí služeb při selhání a navrácení služeb po obnovení v

| Převzetí služeb při selhání/navrácení služeb po obnovení z | Fyzické zařízení | Cloudové zařízení |
| --- | --- | --- |
| Aktualizace 3 na aktualizaci 4 |Vrstvené svazky se převezmou jako vrstvené. <br></br>Místně připojené svazky převezmou služby při selhání místně. <br></br> Po převzetí služeb při selhání při pořizování snímku na zařízení s aktualizací Update 4 se [sledování založené na heatmapu](storsimple-update4-release-notes.md#whats-new-in-update-4) zahájí. |Místně připnuté svazky převezmou služby v podobě vrstev. |
| Aktualizace 4 na aktualizaci 3 |Vrstvené svazky se převezmou jako vrstvené. <br></br>Místně připojené svazky převezmou služby při selhání místně. <br></br> Zálohy používané k obnovení uchovávajících metadat heatmapu. <br></br>Sledování založené na heatmapu není k dispozici v Update 3 po navrácení služeb po obnovení. |Místně připnuté svazky převezmou služby v podobě vrstev. |


## <a name="device-failover-scenarios"></a>Scénáře převzetí služeb při selhání zařízení

Pokud dojde k havárii, můžete se rozhodnout pro převzetí služeb při selhání zařízení StorSimple:

* [Na fyzické zařízení](storsimple-8000-device-failover-physical-device.md).
* [Do sebe samé](storsimple-8000-device-failover-same-device.md).
* [Do cloudového zařízení](storsimple-8000-device-failover-cloud-appliance.md).

Předchozí články obsahují podrobné kroky pro každý z výše uvedených případů převzetí služeb při selhání.


## <a name="failback"></a>Navrácení služeb po obnovení

U aktualizací 3 a novějších verzí StorSimple podporuje také navrácení služeb po obnovení. Navrácení služeb po obnovení je jenom zpětným převzetím služeb při selhání, cíl se změní na zdroj a původní zdrojové zařízení v průběhu převzetí služeb při selhání teď bude cílové zařízení. 

Během navrácení služeb po obnovení StorSimple znovu synchronizuje data zpět do primárního umístění, zastaví vstupně-výstupní operace a aktivitu aplikace a přejde zpět do původního umístění.

Po dokončení převzetí služeb při selhání provede StorSimple následující akce:

* StorSimple vyčistí kontejnery svazků, u kterých došlo k převzetí služeb při selhání ze zdrojového zařízení.
* StorSimple inicializuje na zdrojovém zařízení úlohu na pozadí na kontejner svazků (převzetí služeb při selhání). Pokud se v průběhu úlohy pokusíte o navrácení služeb po obnovení, obdržíte k tomuto účelu oznámení. Počkejte, než se úloha dokončí, aby se spustila navrácení služeb po obnovení.
* Doba potřebná k odstranění kontejnerů svazků závisí na různých faktorech, například na množství dat, stáří dat, počtu záloh a šířce pásma sítě, která je pro danou operaci k dispozici.

Pokud plánujete testovací převzetí služeb při selhání nebo testovací failbacks, doporučujeme, abyste otestovali kontejnery svazků s méně daty (GB). Obvykle můžete zahájit navrácení služeb po obnovení po dobu 24 hodin po dokončení převzetí služeb při selhání.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Otázka: **Co se stane, když dojde k chybě DR nebo k částečnému úspěchu?**

A. Pokud dojde k chybě DR, doporučujeme to zkusit znovu. Druhá úloha převzetí služeb při selhání zařízení si uvědomuje průběh první úlohy a začíná od tohoto bodu výše.

Otázka: **Můžu zařízení odstranit, když probíhá převzetí služeb při selhání zařízení?**

A. Nemůžete odstranit zařízení, když probíhá DR. Po dokončení programu DR můžete zařízení odstranit. Průběh úlohy převzetí služeb při selhání zařízení můžete sledovat v okně **úlohy** .

Otázka: **Kdy se uvolňování paměti spustí na zdrojovém zařízení, aby se odstranila místní data na zdrojovém zařízení?**

A. Uvolňování paměti se na zdrojovém zařízení povoluje až po úplném vyčištění zařízení. Vyčištění zahrnuje čištění objektů, u kterých došlo k převzetí služeb při selhání ze zdrojového zařízení, jako jsou svazky, záložní objekty (ne data), kontejnery svazků a zásady.

Otázka: **Co se stane, když úloha odstranění přidružená k kontejnerům svazků na zdrojovém zařízení selhává?**

A.  Pokud úloha odstranění selhává, můžete kontejnery svazků odstranit ručně. V okně **zařízení** vyberte zdrojové zařízení a klikněte na **kontejnery svazků**. Vyberte kontejnery svazků, u kterých došlo k převzetí služeb při selhání, a v dolní části okna klikněte na **Odstranit**. Po odstranění kontejnerů svazků, u kterých došlo k převzetí služeb při selhání na zdrojovém zařízení, můžete spustit navrácení služeb po obnovení. Další informace najdete v pro [odstranění kontejneru svazků](storsimple-8000-manage-volume-containers.md#delete-a-volume-container).

## <a name="business-continuity-disaster-recovery-bcdr"></a>Zotavení po havárii pro provozní kontinuitu (BCDR)

Když celé datacentrum Azure přestane fungovat, nastane Scénář zotavení po havárii (BCDR) pro provozní kontinuitu. Tento scénář může ovlivnit službu StorSimple Správce zařízení a přidružená zařízení StorSimple.

Pokud se zařízení StorSimple zaregistrovalo těsně před haváriem, může být potřeba, aby se toto zařízení nacházelo s resetováním továrního nastavení. Po havárii se zařízení StorSimple zobrazí v Azure Portal jako offline. Toto zařízení se musí odstranit z portálu. Resetujte zařízení do výchozího továrního nastavení a zaregistrujte ho u služby.

## <a name="next-steps"></a>Další kroky

Pokud jste připraveni provést převzetí služeb při selhání, pro podrobné pokyny vyberte jeden z následujících scénářů:

- [Převzetí služeb při selhání jiným fyzickým zařízením](storsimple-8000-device-failover-physical-device.md)
- [Převzetí služeb při selhání u stejného zařízení](storsimple-8000-device-failover-same-device.md)
- [Převzetí služeb při selhání StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md)

Pokud jste se zařízením převzali služby při selhání, vyberte jednu z následujících možností:

* [Deaktivujte nebo odstraňte zařízení StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* [Ke správě zařízení StorSimple použijte službu StorSimple Správce zařízení](storsimple-8000-manager-service-administration.md).

