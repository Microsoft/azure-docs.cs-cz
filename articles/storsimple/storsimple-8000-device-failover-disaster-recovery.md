---
title: StorSimple převzetí služeb při selhání, zotavení po havárii pro zařízení 8000 series | Dokumentace Microsoftu
description: Zjistěte, jak převzít služby při selhání zařízení StorSimple k samotné, jiné fyzické zařízení nebo cloudového zařízení.
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
ms.openlocfilehash: 079a2f153f257040d1899a33c9e255d633e526ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576326"
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-8000-series-device"></a>Převzetí služeb při selhání a zotavení po havárii pro vaše zařízení řady StorSimple 8000

## <a name="overview"></a>Přehled

Tento článek popisuje funkce převzetí služeb při selhání zařízení pro zařízení řady StorSimple 8000 a jak tato funkce slouží k obnovení zařízení StorSimple, pokud dojde k havárii. StorSimple využívá převzetí služeb při selhání zařízení pro migraci dat ze zdrojového zařízení v datovém centru k jiné cílové zařízení. Pokyny v tomto článku se vztahuje na fyzická zařízení řady StorSimple 8000 a cloud Appliance s software verze Update 3 a novější.

StorSimple využívá **zařízení** okno spuštění funkce převzetí služeb při selhání zařízení v případě havárie. V tomto okně se seznamem všech zařízení StorSimple připojené k vaší službě Správce zařízení StorSimple.

![Okno zařízení](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)


## <a name="disaster-recovery-dr-and-device-failover"></a>Zotavení po havárii (DR) a převzetí služeb při selhání zařízení

V případě zotavení po havárii v primárním zařízení přestane fungovat. StorSimple využívá jako primární zařízení _zdroj_ a přesouvá data přidruženého cloudu do jiného _cílové_ zařízení. Tento proces se označuje jako *převzetí služeb při selhání*. Následující obrázek znázorňuje proces převzetí služeb při selhání.

![Co se děje v převzetí služeb při selhání zařízení?](./media/storsimple-8000-device-failover-disaster-recovery/failover-dr-flow.png)

Cílové zařízení převzetí služeb při selhání může být fyzické zařízení nebo dokonce cloudového zařízení. Cílové zařízení mohou být umístěny ve stejné nebo jiné zeměpisné umístění než zdrojové zařízení.

Během převzetí služeb při selhání můžete vybrat kontejnerů svazků pro migraci. StorSimple poté změní vlastnictví tyto kontejnery svazků ze zdrojového zařízení na cílové zařízení. Jakmile kontejnery svazků změnit vlastnictví, odstraní StorSimple tyto kontejnery ze zdrojového zařízení. Po odstranění je dokončeno, můžete službu navrátit cílové zařízení. _Navrácení služeb po obnovení_ převede vlastnictví zpět do původní zdrojové zařízení.

### <a name="cloud-snapshot-used-during-device-failover"></a>Snímek v cloudu použít během převzetí služeb při selhání zařízení

Následující zotavení po Havárii se používá poslední zálohování v cloudu pro obnovení dat na cílové zařízení. Další informace o cloudových snímků, najdete v části [proveďte ruční zálohování pomocí služby Správce zařízení StorSimple](storsimple-8000-manage-backup-policies-u2.md#take-a-manual-backup).

Na řadu StorSimple 8000 jsou zásady zálohování přidružené zálohy. Pokud existuje více zásad zálohování pro stejný svazek, StorSimple vybere zásadu zálohování, která s největší počet svazků. StorSimple potom použije nejnovější zálohy z vybraných zásadách zálohování pro obnovení dat na cílovém zařízení.

Předpokládejme, že existují dvě zásady zálohování, *defaultPol* a *customPol*:

* *defaultPol*: Jeden svazek *vol1*, spustí denní od 10:30 odp.
* *customPol*: Čtyři svazky *vol1*, *vol2*, *vol3*, *vol4*, spustí denní od 10:00 PM.

V takovém případě StorSimple upřednostňuje pro konzistence vzhledem k selháním a používá *customPol* má více svazků. Nejnovější zálohy z této zásady umožňuje obnovit data. Další informace o tom, jak vytvářet a spravovat zásady zálohování, přejděte na [Správa zásad zálohování pomocí služby Správce zařízení StorSimple](storsimple-8000-manage-backup-policies-u2.md).

## <a name="common-considerations-for-device-failover"></a>Častá rozhodnutí při převzetí služeb při selhání zařízení

Než převzetí služeb při selhání zařízení, projděte si následující informace:

* Před spuštěním převzetí služeb zařízení, musí být v režimu offline všechny svazky v kontejnery svazků. V neplánované převzetí služeb při selhání StotSimple svazky budou automaticky přejdou do režimu offline. Ale pokud provádíte plánované převzetí služeb při selhání (pro testování zotavení po Havárii), je nutné provést všechny svazky do offline režimu.
* Pouze kontejnery svazků, které mají přiřazený snímek v cloudu jsou uvedeny pro zotavení po Havárii. Musí existovat alespoň jeden kontejner svazků snímkem přidruženého cloudu chcete obnovit data.
* Pokud existují cloudové snímky, které jsou rozmístěny napříč více kontejnery svazků, StorSimple převezme služby při selhání těchto kontejnerů svazků jako sady. Ve výjimečných instanci Pokud nejsou místní snímky, které jsou rozmístěny napříč více kontejnery svazků, ale související cloudové snímky tomu tak není, StorSimple převzetí služeb při selhání místních snímků a po zotavení po Havárii dojde ke ztrátě místních dat.
* Dostupné cílové zařízení pro zotavení po Havárii jsou zařízení, která mají dostatek místa pro toto vybrané kontejnery svazků. Všechna zařízení, které nemají dostatek místa, nejsou uvedeny jako cílová zařízení.
* Po DR (po omezenou dobu) výkon přístupu k data mohou být ovlivněny výrazně, protože zařízení je potřeba přístup k datům z cloudu a uložte ho místně.

#### <a name="device-failover-across-software-versions"></a>Převzetí služeb při selhání zařízení mezi verzemi softwaru

Služba Správce zařízení StorSimple v nasazení může mít více zařízení, jak fyzické, tak cloudových, všechny spuštěné různé verze softwaru.

Pomocí následující tabulky určete, jestli jste převzetí služeb při selhání nebo selhání zpět na jiném zařízení s jinou verzi a chování typů svazků během zotavení po Havárii.

#### <a name="failover-and-failback-across-software-versions"></a>Převzetí služeb při selhání a navrácení služeb po obnovení mezi verzemi softwaru

| Převzetí služeb při selhání nebo obnovení vrátit | Fyzické zařízení | Cloudové zařízení |
| --- | --- | --- |
| Aktualizace 3 a Update 4 |Vrstvené svazky převzetí služeb při selhání vrstvený. <br></br>Místně připojené svazky převzetí služeb při selhání jako místně připojené. <br></br> Po převzetí služeb při pořídíte snímek na zařízení s aktualizací Update 4 [sledování na základě heatmapu](storsimple-update4-release-notes.md#whats-new-in-update-4) aktivuje. |Místně připojené svazky převzetí služeb při selhání jako vrstvený. |
| Aktualizace 4 s aktualizací 3 |Vrstvené svazky převzetí služeb při selhání vrstvený. <br></br>Místně připojené svazky převzetí služeb při selhání jako místně připojené. <br></br> Použít k obnovení zálohy uchovávat heatmapu metadat. <br></br>Na základě Heatmapu sledování není k dispozici ve verzi Update 3 po navrácení služeb po obnovení. |Místně připojené svazky převzetí služeb při selhání jako vrstvený. |


## <a name="device-failover-scenarios"></a>Scénáře převzetí služeb při selhání zařízení

Pokud dojde k havárii, můžete se rozhodnout převzít služby při selhání zařízení StorSimple:

* [Fyzické zařízení](storsimple-8000-device-failover-physical-device.md).
* [Na sebe sama](storsimple-8000-device-failover-same-device.md).
* [Do cloudového zařízení](storsimple-8000-device-failover-cloud-appliance.md).

Předchozí články poskytují podrobné pokyny pro každé z výše uvedených případech převzetí služeb při selhání.


## <a name="failback"></a>Navrácení služeb po obnovení

S aktualizací Update 3 a novější verze StorSimple podporuje také navrácení služeb po obnovení. Navrácení služeb po obnovení je naopak převzetí služeb při selhání, cíl se stane zdroj a původní zdrojové zařízení při selhání teď bude cílové zařízení. 

Během navrácení služeb po obnovení StorSimple znovu synchronizuje data zpět do primárního umístění, zastaví vstupně-výstupní operace a aktivita aplikace a přejde zpět do původního umístění.

Po dokončení převzetí služeb při selhání se StorSimple provede následující akce:

* StorSimple vyčistí kontejnery svazků, které při selhání ze zdrojového zařízení.
* StorSimple iniciuje úlohu na pozadí za kontejner svazků (převzetí služeb při selhání) na zdrojové zařízení. Pokud se pokusíte provést znovu, pokud úloha v průběhu, obdržíte oznámení o tom. Počkejte na dokončení úlohy spusťte navrácení služeb po obnovení.
* Čas potřebný k dokončení odstranění kontejnerů svazků, závisí na různých faktorech, jako je například množství dat, stáří dat, počet záloh a šířku pásma sítě dostupnou pro operaci.

Pokud jsou plánování testovací převzetí služeb při selhání nebo testovací navrácení služeb po obnovení, doporučujeme, abyste otestovali kontejnery svazků s méně dat (GB). Obvykle můžete spustit navrácení služeb po obnovení 24 hodin po dokončení převzetí služeb.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Otázka: **Co se stane, když zotavení po Havárii se nezdaří, nebo má částečný úspěch?**

A. Pokud zotavení po Havárii se nezdaří, doporučujeme vám, opakujte akci. Druhá úloha převzetí služeb při selhání zařízení si je vědoma průběh první úlohy a spustí od tohoto okamžiku a vyšší.

Otázka: **Můžete odstranit zařízení, když probíhá převzetí služeb při selhání zařízení?**

A. Zařízení nemůžete odstranit, když probíhá zotavení po Havárii. Zařízení můžete odstranit pouze po dokončení zotavení po Havárii. Můžete sledovat průběh úlohy převzetí služeb při selhání zařízení v **úlohy** okno.

Otázka: **Při uvolnění paměti začínající na zdrojové zařízení tak, aby místní data na zdrojové zařízení se odstraní?**

A. Uvolňování paměti je povolená na zdrojové zařízení až poté, co je zcela vyčistit zařízení. Čištění zahrnuje čištění objekty, které mají převzetí služeb při selhání ze zdrojového zařízení, jako jsou svazky, záložní objekty (nikoli data), kontejnery svazků a zásady.

Otázka: **Co se stane, když selže tato úloha odstraňte přidružené kontejnery svazků ve zdrojové zařízení?**

A.  Pokud se úloha odstranění nezdaří, můžete ručně odstraňte kontejnery svazků. V **zařízení** okně vyberte zdrojové zařízení a klikněte na tlačítko **kontejnery svazků**. Vyberte kontejnery svazků, neúspěšných i po uložení v dolní části okna, klikněte na **odstranit**. Po odstranění všech se přes kontejnery svazků na zdrojové zařízení, můžete spustit navrácení služeb po obnovení. Další informace najdete v části [odstraňování kontejneru svazků](storsimple-8000-manage-volume-containers.md#delete-a-volume-container).

## <a name="business-continuity-disaster-recovery-bcdr"></a>Obchodní kontinuity podnikových procesů zotavení po havárii (BCDR)

O obchodní kontinuity podnikových procesů zotavení po havárii (BCDR) nastane, pokud celé datové centrum Azure přestane fungovat. Tento scénář může mít vliv na služby Správce zařízení StorSimple a jeho přiřazená zařízení StorSimple.

Zařízení StorSimple byl zaregistrován těsně před došlo k havárii, může toto zařízení musí podstoupit obnovení do výrobního nastavení. Po havárii zařízení StorSimple se zobrazí na webu Azure Portal jako v režimu offline. Toto zařízení se musí odstranit z portálu. Obnoví výchozí tovární nastavení zařízení a znovu ji zaregistrovat ve službě.

## <a name="next-steps"></a>Další postup

Pokud jste připraveni k převzetí služeb zařízení, zvolte jednu z následujících scénářů pro podrobné pokyny:

- [Převzetí služeb při selhání do jiného fyzického zařízení](storsimple-8000-device-failover-physical-device.md)
- [Převzetí služeb při selhání do stejného zařízení](storsimple-8000-device-failover-same-device.md)
- [Převzetí služeb při selhání pro řešení StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md)

Pokud budete mít převzetí služeb při selhání zařízení, vyberte jednu z následujících možností:

* [Deaktivace a odstranění zařízení StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* [Použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

