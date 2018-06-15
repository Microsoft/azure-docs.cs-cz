---
title: StorSimple převzetí služeb při selhání, zotavení po havárii pro řady 8000 zařízení | Microsoft Docs
description: Naučte se převzít zařízení StorSimple sám sebe, jiné fyzické zařízení nebo zařízení cloudu.
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
ms.openlocfilehash: 5dc4a98bf889d38c62c76364289c2d58c14d771e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874980"
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-8000-series-device"></a>Převzetí služeb při selhání a zotavení po havárii pro vaše zařízení řady StorSimple 8000

## <a name="overview"></a>Přehled

Tento článek popisuje funkce převzetí služeb při selhání zařízení pro řadu zařízení StorSimple 8000 a jak tato funkce slouží k obnovení zařízení StorSimple, pokud dojde k havárii. StorSimple používá zařízení převzetí služeb při selhání k migraci dat ze zdrojového zařízení v datovém centru jiné cílové zařízení. Pokyny v tomto článku se vztahují na řadu fyzického zařízení StorSimple 8000 a cloudu zařízení se systémem verze softwaru Update 3 nebo novější.

Používá StorSimple **zařízení** okna spustit funkci převzetí služeb při selhání zařízení v případě havárie. Toto okno obsahuje seznamy všech zařízení StorSimple připojené k vaší službě StorSimple Manager zařízení.

![Okno zařízení](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)


## <a name="disaster-recovery-dr-and-device-failover"></a>Zotavení po havárii (DR) a převzetí služeb při selhání zařízení

Ve scénáři zotavení po havárii primární zařízení přestane fungovat. Používá jako primární zařízení StorSimple _zdroj_ a přesouvá data přidruženého cloudu do jiného _cíl_ zařízení. Tento proces se označuje jako *převzetí služeb při selhání*. Následující obrázek znázorňuje proces převzetí služeb při selhání.

![Co se stane v zařízení převzetí služeb při selhání?](./media/storsimple-8000-device-failover-disaster-recovery/failover-dr-flow.png)

Cílové zařízení převzetí služeb při selhání může být fyzické zařízení nebo i o zařízení cloudu. Cílové zařízení může nacházet ve stejné nebo jiné zeměpisné umístění než zdrojového zařízení.

Během převzetí služeb při selhání můžete vybrat kontejnery svazků pro migraci. StorSimple poté změní vlastnictví tyto kontejnery svazků ze zdrojového zařízení cílového zařízení. Jakmile kontejnery svazků změnit vlastnictví, odstraní StorSimple těchto kontejnerů ze zdrojového zařízení. Po dokončení odstranění může zpět selhat cílové zařízení. _Navrácení služeb po obnovení_ přenos vlastnictví zpět do původního zdrojového zařízení.

### <a name="cloud-snapshot-used-during-device-failover"></a>Cloudový snímek používá během převzetí služeb při selhání zařízení

Následující zotavení po Havárii se používá nejnovější zálohy cloudu pro obnovení dat do cílové zařízení. Další informace o cloudových snímků, najdete v části [používat službu StorSimple Manager zařízení k provedení ruční zálohy](storsimple-8000-manage-backup-policies-u2.md#take-a-manual-backup).

Zásady zálohování na řadu zařízení StorSimple 8000 nejsou přidružené zálohy. Pokud existují více zásady zálohování pro stejný svazek, StorSimple vybere zásady zálohování s největší počet svazků. StorSimple pak použije nejnovější zálohy z vybrané zásady zálohování pro obnovení dat na cílovém zařízení.

Předpokládejme, že existují dvě zásady zálohování, *defaultPol* a *customPol*:

* *defaultPol*: jeden svazek, *vol1*, spustí denní od 22:30:00.
* *customPol*: čtyři svazky, *vol1*, *vol2*, *vol3*, *vol4*, spustí denní od 22:00:00.

V takovém případě StorSimple upřednostňuje pro konzistenci s havárií a používá *customPol* protože má více svazků. Nejnovější zálohy z této zásady se používá k obnovení dat. Další informace o tom, jak vytvořit a spravovat zásady zálohování, přejděte na [použít službu StorSimple Manager zařízení ke správě zásady zálohování](storsimple-8000-manage-backup-policies-u2.md).

## <a name="common-considerations-for-device-failover"></a>Častá rozhodnutí při převzetí služeb při selhání zařízení

Než budete selhání zařízení, projděte si následující informace:

* Před zahájením selhání zařízení, musí být všechny svazky v rámci kontejnery svazků offline. V neplánované převzetí služeb při selhání svazky StotSimple automaticky přejde do režimu offline. Pokud provádíte plánované převzetí služeb při selhání (Pokud k testování zotavení po Havárii), je však třeba provést všechny svazky do offline režimu.
* Pouze kontejnery svazků, které mají přiřazený cloudový snímek jsou uvedené pro zotavení po Havárii. Musí existovat alespoň jeden kontejner svazků s k přidružené cloudový snímek chcete obnovit data.
* Pokud existují cloudové snímky, které jsou rozmístěny napříč více kontejnery svazků, StorSimple převezme tyto kontejnery svazků jako sada. Ve výjimečných instance Pokud existují místní snímky, které jsou rozmístěny napříč více kontejnery svazků, ale nechcete snímky přidružený cloud, StorSimple převzetí služeb při selhání místní snímky a místní data dojde ke ztrátě po zotavení po Havárii.
* Dostupných cílových zařízení pro zotavení po Havárii jsou zařízení, která mají dostatek místa pro uložení kontejnery vybraný svazek. Všechna zařízení, které nemají dostatek místa, nejsou uvedené jako cílová zařízení.
* Po zotavení po Havárii (pro omezené trvání) data přístup může mít vliv na výkon výrazně, jako zařízení potřebuje přístup k datům z cloudu a uložte ho místně.

#### <a name="device-failover-across-software-versions"></a>Zařízení převzetí služeb při selhání mezi verzemi softwaru

Service Manager zařízení StorSimple v nasazení může mít více zařízení, i fyzického a cloudu, všechny verze spuštění různých softwaru.

Pomocí následující tabulky určete, jestli jste převzetí služeb při selhání nebo navrácení služeb po obnovení jiná zařízení se systémem verzi jiný software a chování typy svazku během zotavení po Havárii.

#### <a name="failover-and-failback-across-software-versions"></a>Převzetí služeb při selhání a navrácení služeb po obnovení mezi verzemi softwaru

| Převzetí služeb při selhání / po obnovení vrátit | Fyzické zařízení | Cloudové zařízení |
| --- | --- | --- |
| Aktualizace 3 pro aktualizací 4 |Vrstvené svazky přes vrstvené nezdaří. <br></br>Místně vázaných svazků převzetí služeb při selhání jako místně vázaný. <br></br> Po převzetí služeb při pořízení snímku na zařízení s aktualizacemi Update 4 [na základě heatmap sledování](storsimple-update4-release-notes.md#whats-new-in-update-4) dojde k. |Místně vázaný svazky převzetí služeb při selhání jako vrstvené. |
| Aktualizace 4 pro aktualizací 3 |Vrstvené svazky přes vrstvené nezdaří. <br></br>Místně vázaných svazků převzetí služeb při selhání jako místně vázaný. <br></br> Použít k obnovení zálohy zachovat heatmap metadat. <br></br>Na základě Heatmap sledování není k dispozici ve verzi Update 3 následující navrácení služeb po obnovení. |Místně vázaný svazky převzetí služeb při selhání jako vrstvené. |


## <a name="device-failover-scenarios"></a>Scénáře převzetí služeb při selhání zařízení

Pokud dojde k havárii, můžete převzít zařízení StorSimple:

* [Fyzické zařízení](storsimple-8000-device-failover-physical-device.md).
* [Na sebe sama](storsimple-8000-device-failover-same-device.md).
* [Do cloudu zařízení](storsimple-8000-device-failover-cloud-appliance.md).

Předchozí články poskytují podrobné pokyny pro každou z výše uvedených případech převzetí služeb při selhání.


## <a name="failback"></a>Navrácení služeb po obnovení

Pro Update 3 a novějších verzích podporuje StorSimple také navrácení služeb po obnovení. Navrácení služeb po obnovení je právě zpětného převzetí služeb při selhání, cíl se změní na zdroji a původního zdrojového zařízení během převzetí služeb při selhání teď se změní na cílovém zařízení. 

Během navrácení služeb po obnovení StorSimple znovu synchronizuje data zpět do primárního umístění, zdržovalo vstupy/výstupy a aktivitě aplikace a přejde zpět do původního umístění.

Po dokončení převzetí služeb StorSimple provede následující akce:

* StorSimple vyčistí kontejnery svazků, které jsou při selhání ze zdrojového zařízení.
* StorSimple spustí úlohu na pozadí na kontejneru svazků (převzetí služeb při selhání) na zdrojového zařízení. Pokud se pokusíte selžou zpět probíhá úloha, dostanete oznámení, k tomuto účelu. Počkejte na dokončení úlohy spusťte navrácení služeb po obnovení.
* Čas potřebný k dokončení odstranění kontejnery svazků závisí na různých faktorech, jako je množství dat, stáří dat, počet záloh a šířku pásma sítě dostupnou pro operaci.

Pokud jsou plánování testovací převzetí služeb při selhání nebo testovací navrácení služeb po obnovení, doporučujeme, abyste otestovali kontejnery svazků s méně dat (GB). Obvykle můžete spustit navrácení služeb po obnovení 24 hodin po dokončení převzetí služeb při selhání.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

OTÁZKY. **Co se stane, když zotavení po Havárii se nezdaří, nebo má částečný úspěch?**

A. Pokud zotavení po Havárii se nezdaří, doporučujeme vám, opakujte akci. Druhý převzetí služeb při selhání úlohy zařízení si je vědoma průběh první úlohy a spustí z tohoto bodu a vyšší.

OTÁZKY. **Můžete odstranit zařízení, když probíhá převzetí služeb při selhání zařízení?**

A. Zařízení nelze odstranit, když probíhá zotavení po Havárii. Zařízení může odstranit pouze po dokončení zotavení po Havárii. Můžete sledovat průběh úlohy převzetí služeb při selhání zařízení v **úlohy** okno.

OTÁZKY. **Pokud uvolnění paměti spustit na zdrojového zařízení, tak, aby místní data na zdrojového zařízení se odstraní?**

A. Uvolňování paměti je zapnuta zdrojového zařízení až po zařízení je zcela vyčištěna. Vyčištění zahrnuje čištění objektů, které mají převzetí služeb při selhání ze zdrojového zařízení, jako jsou svazky, zálohování objekty (ne data), kontejnery svazků a zásady.

OTÁZKY. **Co se stane, pokud se nezdaří úlohy odstranění spojené s kontejnery svazků v zdrojového zařízení?**

A.  Pokud úloha odstranění selže, můžete ručně odstranit kontejnery svazků. V **zařízení** okně vyberte vaše zdrojové zařízení a klikněte na **kontejnery svazků**. Vyberte kontejnery svazků, které při selhání přes a v dolní části okna, klikněte na **odstranit**. Po odstranění všech neúspěšný přes kontejnery svazků na zdrojového zařízení, můžete spustit navrácení služeb po obnovení. Další informace, přejděte na [odstranit kontejner svazků](storsimple-8000-manage-volume-containers.md#delete-a-volume-container).

## <a name="business-continuity-disaster-recovery-bcdr"></a>Obchodní kontinuity zotavení po havárii (BCDR)

Obchodní kontinuity po havárii (BCDR) obnovení scénář nastane, když celého datového centra Azure přestane fungovat. Tento scénář může ovlivnit služby StorSimple Manager zařízení a jeho přiřazená zařízení StorSimple.

Pokud zařízení StorSimple byl zaregistrován těsně před došlo k havárii, toto zařízení možná muset podstoupit obnovení továrního nastavení. Po havárii zařízení StorSimple se objeví na portálu Azure jako v režimu offline. Toto zařízení musí být odstraněna z portálu. V zařízení resetovat výchozí tovární nastavení a zaregistrujte ho znovu se službou.

## <a name="next-steps"></a>Další kroky

Pokud jste připravení na provedení selhání zařízení, vyberte jednu z následujících scénářů pro podrobné pokyny:

- [Převzetí služeb při selhání do jiného fyzického zařízení](storsimple-8000-device-failover-physical-device.md)
- [Převzetí služeb při selhání do stejného zařízení](storsimple-8000-device-failover-same-device.md)
- [Převzetí služeb při selhání zařízení StorSimple cloudu](storsimple-8000-device-failover-cloud-appliance.md)

Pokud jste při selhání zařízení, vyberte jednu z následujících možností:

* [Deaktivovat nebo odstranit zařízení StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* [Použít službu StorSimple Manager zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

