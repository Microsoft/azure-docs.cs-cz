---
title: Monitorování vašeho zařízení StorSimple 8000 series | Dokumentace Microsoftu
description: Popisuje způsob použití služby Správce zařízení StorSimple ke sledování využití, výkonu vstupně-výstupní operace a využití kapacity.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/17/2017
ms.author: alkohli
ms.openlocfilehash: 602514df69977891167f341db0ab20913bcacc9f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60634467"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>Použití služby Správce zařízení StorSimple k monitorování zařízení StorSimple

## <a name="overview"></a>Přehled
Služba Správce zařízení StorSimple můžete použít ke sledování konkrétních zařízení v rámci vašeho řešení StorSimple. Můžete vytvářet vlastní grafy na základě výkonu vstupně-výstupních operací, využití kapacity, propustnost sítě a zařízení metriky výkonu a připnout na řídicí panel. Další informace najdete v části [přizpůsobit řídicí panel portálu](../azure-portal/azure-portal-dashboards.md).

Chcete-li zobrazit informacím o monitorování pro určité zařízení, na webu Azure Portal, vyberte ve službě Správce zařízení StorSimple. Ze seznamu zařízení vyberte zařízení a potom přejděte ke **monitorování**. Můžete se podívat **kapacity**, **využití**, a **výkonu** grafy pro vybrané zařízení.

## <a name="capacity"></a>Kapacita
**Kapacita** sleduje zřízeného místa a místa zůstává v zařízení. Zbývající kapacita se následně zobrazí místně připnuté a vrstvené.

Zřízená a zbývající kapacity se dále člení místně připnuté a vrstvené svazky. Pro každý svazek zřízená kapacita a zbývající kapacity v zařízení se zobrazí.

![Kapacita v/v](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>Využití
**Využití** sleduje metriky související s množství dat úložný prostor, který používá svazky, kontejnery svazků nebo zařízení. Můžete vytvořit sestavy na základě využití kapacity primární úložiště, cloudového úložiště nebo úložiště vašeho zařízení. Využití kapacity se měří na konkrétním svazku, kontejner určitý svazek nebo všechny kontejnery svazků.
Ve výchozím nastavení se použije v hlášení využití za posledních 24 hodin. Můžete upravit graf a změňte, doba trvání nad tím, které se využití hlášené tak, že vyberete:
* Posledních 24 hodin
* Posledních 7 dní
* Posledních 30 dní
* Posledních 90 dní
* Poslední rok

Dva klíče metrices, růst a rozsah označené pro grafy využití. Rozsah odkazuje na hodnotu maximální a minimální hodnoty využití hlášené přes vybranou dobu trvání (fo instance, za posledních 7 dnů).

Růst odkazuje na zvýšení využití v od prvního dne na poslední den za vybrané období. 

Růst a rozsah můžou být vyjádřeny i rovnic následující:

```
Range = {Usage(minimum), Usage(maximum)}

Growth = Usage(Last day) - Usage(first day)

Growth (%) = [{Usage(last day) - Usage(first day)} X 100]/Usage(first day)
```

Primární, cloudové a místní úložiště lze popsat následujícím způsobem:

### <a name="primary-storage-usage"></a>Primární úložiště využití
Tyto grafy zobrazují množství dat zapsaných na svazky zařízení StorSimple, než se s odstraněním duplicitních dat a komprimovaná data. Můžete zobrazit primární úložiště používat všechny svazky v kontejneru svazků nebo jednoho svazku. Využité primární úložiště se dále člení podle použito primární vrstvené úložiště a primární místně připojené úložiště.

Následující grafy ukazují primární úložiště používat pro zařízení StorSimple před a po pořízení snímek v cloudu. Toto je pouze objem dat, by neměly měnit snímek v cloudu z primárního úložiště. Jak je vidět tento graf zobrazuje žádný rozdíl v primární vrstvené nebo místně připojené úložiště použít jako výsledek pořízení snímku cloud. Cloudový snímek začalo přibližně 23:50:00 na tomto zařízení.

![Využití kapacity primární po snímek v cloudu](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

Pokud nyní spustíte vstupně-výstupních operací na hostiteli připojen k zařízení StorSimple, uvidíte zvýšení primární vrstvené úložiště nebo primární místně připojené úložiště v závislosti na svazky, které (vrstvené nebo místně připnuté) zapíšete data do. Tady jsou grafy využití primárního úložiště pro zařízení StorSimple. Na tomto zařízení StorSimple hostiteli spuštěna obsluhující zápis na přibližně 2:30 odp. na vrstvený svazek v zařízení. Zobrazí se ve špičce v zápisu bajtů/s odpovídající vstupně-výstupních operací běží na hostiteli.

![Výkon při vstupně-výstupních operací běží na vrstvené svazky](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

Když se podíváte na použito primární vrstvené úložiště, který přešel zatímco primární místně připojené využití zůstane beze změny, protože neexistují žádné zápisy obsluhovat místně připojené svazky na zařízení.

![Využití kapacity primární vstupně-výstupní operace jsou spuštěny na vrstvené svazky](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

Pokud používáte s aktualizací 3 nebo vyšší, můžete přerušit snížit využití kapacity primární úložiště ve svazku, všechny svazky, všechny vrstvené svazky a všechny místně připojené svazky jak je znázorněno níže. Rozdělení všechny místně připojené svazky vám umožní rychle zjistit, jak velká část u místní vrstvy slouží.

![Využití primární kapacity pro všechny vrstvené svazky](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![Primární využití kapacity pro všechny místně připojené svazky](./media/storsimple-8000-monitor-device/monitor-usage4.png)

Dále můžete kliknutím na jednotlivé svazky v seznamu a zobrazit odpovídající využití.

![Primární využití kapacity pro všechny místně připojené svazky](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>Využití úložiště cloudu
Tyto grafy zobrazují množství využité cloudové úložiště. Tato data se odstraňují duplicity a komprimovaná. Toto množství zahrnuje cloudové snímky, které mohou obsahovat data, která se neprojeví v libovolné primární svazek a uchovávají po dobu uchování starší verze nebo požadované účely. Můžete porovnat primární a cloudu obrázky spotřebu úložiště, kde získáte představu snížení frekvence data, i když číslo nesmí být přesné.

Následující grafy ukazují využití úložiště cloudové zařízení StorSimple při pořízení snímek v cloudu.

* Cloudový snímek začalo asi 11:50 upgradujete na tomto zařízení a vy vidíte, že před cloudových snímků, byl využité cloudové úložiště. 
* Jednou cloudový snímek dokončeno, využití cloudové úložiště snímku si rovný 0,89 GB. 
* Když probíhala snímku v cloudu, je zde i odpovídající maximální vstupně-výstupních operací ze zařízení do cloudu.

    ![Využití úložiště cloudu před snímek v cloudu](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![Využití úložiště po cloudový snímek v cloudu](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![Vstupně-výstupní operace od zařízení ke cloudu během snímek v cloudu](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>Využití místního úložiště
Tyto grafy zobrazují celkové využití pro zařízení, která bude využití více než primární úložiště, protože obsahuje lineární vrstvy SSD. Tato úroveň obsahuje množství dat, která existuje také na zařízení uživatele jiných vrstev. Kapacitu ve vrstvě SSD lineární je uvedená, takže když nová data k dispozici ve, původní data se přesunou do vrstvy HDD (tentokrát ho se odstraňují duplicity a komprimované) a následně do cloudu.

Za čas, primární úložiště používaných a místní úložiště pravděpodobně zvýší společně dokud nezačne data Vrstvená do cloudu. V tu chvíli se začíná stagnovat pravděpodobně začne využité místní úložiště, ale zvýší využité primární úložiště, jako další data se zapisují.

Následující grafy ukazují primární úložiště používat pro zařízení StorSimple při pořízení snímek v cloudu. Cloudový snímek začali v 11:50 a místní úložiště spuštěna, snižují v daném čase. Využité místní úložiště snížil z 1.445 GB 1,09 GB. To označuje, že pravděpodobně nekomprimovaných dat v lineární vrstvy SSD se s odstraněním duplicitních dat, komprimované a přesunout do vrstvy HDD. Všimněte si, že pokud je zařízení už velké množství dat na úrovni SSD a HDD, nemusí zobrazit toto snížení. V tomto příkladu má malé množství dat zařízení.

![Využití místního úložiště po snímek v cloudu](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>Výkon
**Výkon** sleduje metriky související s počet čtení a zápisu operace mezi buď rozhraní iniciátor iSCSI na hostitelském serveru a zařízení nebo zařízení a cloudu. Tato výkonu lze změřit pro určitý svazek, kontejner určitý svazek nebo všechny kontejnery svazků. Výkon také zahrnuje využití procesoru a propustnost sítě pro různé síťových rozhraní na zařízení.

### <a name="io-performance-for-initiator-to-device"></a>Vstupně-výstupní výkon směru od iniciátoru do zařízení
Následující graf zobrazuje vstupy/výstupy směru od iniciátoru do zařízení pro všechny svazky, které jsou pro zařízení s produkčním prostředí. Metriky promítají jsou čtení a zápis bajtů za sekundu. Můžete také grafu pro čtení, zápisu a nezpracovaných vstupně-výstupní operace, nebo čtení a zápisu latenci.

![VSTUPNĚ-výstupním výkonem směru od iniciátoru do zařízení](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>Vstupně-výstupní výkon zařízení do cloudu
Pro stejné zařízení se promítají vstupně-výstupních operací pro data ze zařízení do cloudu pro všechny kontejnery svazků. Na tomto zařízení data pouze v rámci lineární úrovně a má hodnotu nothing přesahovat do cloudu. Neexistují žádné operace čtení i zápis, ke kterým dochází ze zařízení do cloudu. Proto jsou vrcholů v grafu v intervalu 5 minut, který odpovídá frekvenci, s jakou se kontroluje prezenčního signálu mezi zařízením a službou.

Pro stejné zařízení pořízení snímek v cloudu pro spuštění v 11:50 dat svazku. Výsledkem dat odesílaných ze zařízení do cloudu. Zápisy se zpracuje do cloudu v této hodnotě duration. Vstupně-výstupní operace graf zobrazuje ve špičce zápis bajtů/s odpovídající čas pořizování snímku.

![Vstupně-výstupní operace od zařízení ke cloudu během snímek v cloudu](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>Propustnost sítě pro síťová rozhraní zařízení
**Propustnost sítě** sleduje metriky související s množství data přenesená z síťová rozhraní iniciátor iSCSI na hostitelském serveru a zařízení a mezi zařízením a cloudem. Tato metrika všech síťových rozhraní iSCSI můžete monitorovat na vašem zařízení.

Následující grafy ukazují propustnost sítě pro Data 0, 1 1 GbE sítě na vašem zařízení, která byla obou povolenou podporu cloudu (výchozí) a podporu iSCSI. Na tomto zařízení na 14. června na přibližně 21: 00, byla data Vrstvená do cloudu (cloud nevytvořily žádné snímky v daném čase, která odkazuje na ovládání datových vrstev na právě mechanismus pro přesun dat do cloudu) což vedlo ke vstupně-výstupních operací, které jsou používané pro služby do cloudu. Neexistuje odpovídající špičce v grafu propustnost sítě pro stejnou dobu a většina síťového provozu je odchozí do cloudu.

![Propustnost sítě pro Data 0](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

Když se podíváme na graf propustnosti rozhraní Data 1, jiné 1gbe síťové rozhraní, která byla pouze povolené iSCSI a potom se prakticky žádná síťový provoz v této hodnotě duration.

![Propustnost sítě pro Data 1](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>Využití výkonu procesoru zařízení
**Využití procesoru** sleduje metriky související s procesoru využívá na vašem zařízení. Následující graf zobrazuje statistiku využití procesoru pro zařízení v produkčním prostředí.

![Využití výkonu procesoru zařízení](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>Další postup
* Zjistěte, jak [řídicí panel zařízení služby StorSimple Device Manager](storsimple-device-dashboard.md).
* Zjistěte, jak [použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-manager-service-administration.md).

