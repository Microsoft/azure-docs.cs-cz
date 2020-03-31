---
title: Sledování zařízení řady StorSimple 8000
description: Popisuje, jak pomocí služby StorSimple Device Manager sledovat využití, vstupně-v., výkon a využití kapacity.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 10/17/2017
ms.author: alkohli
ms.openlocfilehash: b3b77024606c5cdb02ff7bdd357c2d14a2415efa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277060"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>Použití služby StorSimple Device Manager ke sledování zařízení StorSimple

## <a name="overview"></a>Přehled
Službu StorSimple Device Manager můžete použít ke sledování konkrétních zařízení v rámci řešení StorSimple. Můžete vytvořit vlastní grafy na základě výkonu vstupně-va/V, využití kapacity, propustnosti sítě a metrik výkonu zařízení a připnout je na řídicí panel. Další informace naleznete v [přizpůsobení řídicího panelu portálu](../azure-portal/azure-portal-dashboards.md).

Chcete-li zobrazit informace o monitorování pro konkrétní zařízení, vyberte na webu Azure Portal službu StorSimple Device Manager. Ze seznamu zařízení vyberte zařízení a přejděte na **Monitor**. Potom u vybraného zařízení zobrazígrafy **Kapacita**, **Využití**a **Výkon.**

## <a name="capacity"></a>Kapacita
**Kapacita** sleduje zřízený prostor a prostor zbývající na zařízení. Zbývající kapacita se pak zobrazí jako místně připnutá nebo vrstvená.

Zřízená a zbývající kapacita se dále člení podle vrstvených a místně vázaných svazků. Pro každý svazek se zobrazí zřízená kapacita a zbývající kapacita na zařízení.

![Kapacita vi](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>Využití
**Použití** sleduje metriky související s množstvím místa úložiště dat, které používají svazky, kontejnery svazků nebo zařízení. Sestavy můžete vytvářet na základě využití kapacity primárního úložiště, cloudového úložiště nebo úložiště zařízení. Využití kapacity lze měřit na určitém svazku, konkrétní kontejner svazku nebo všechny kontejnery svazku.
Ve výchozím nastavení je hlášeno využití za posledních 24 hodin. Graf můžete upravit a změnit dobu trvání, po kterou je využití hlášeno, výběrem z následujících možností:
* Posledních 24 hodin
* Posledních 7 dní
* Posledních 30 dní
* Posledních 90 dní
* Minulý rok

Pro grafy využití jsou vykazovány dvě klíčové metriky, růst a rozsah. Rozsah odkazuje na maximální hodnotu a minimální hodnoty využití hlášené za vybranou dobu trvání (fo instance, Past 7 days).

Růst se týká zvýšení využití od prvního dne do posledního dne během vybrané doby trvání. 

Růst a rozsah mohou být také reprezentovány následujícími rovnicemi:

```
Range = {Usage(minimum), Usage(maximum)}

Growth = Usage(Last day) - Usage(first day)

Growth (%) = [{Usage(last day) - Usage(first day)} X 100]/Usage(first day)
```

Primární, cloudové a místní úložiště lze popsat takto:

### <a name="primary-storage-usage"></a>Využití primárního úložiště
Tyto grafy zobrazují množství dat zapsaných do svazků StorSimple před odstraněním duplicit a komprimací dat. Můžete zobrazit primární úložiště používané všemi svazky v kontejneru svazků nebo pro jeden svazek. Primární použité úložiště se dále člení podle primárnívrstvé úložiště použité a primární místně připnuté úložiště používá.

Následující grafy ukazují primární úložiště používané pro zařízení StorSimple před a po pořízení snímku cloudu. Protože se jedná pouze o data svazku, snímek cloudu by neměl změnit primární úložiště. Jak můžete vidět, graf nezobrazuje žádný rozdíl v primární vrstvené nebo místně připnuté úložiště používané v důsledku pořízení snímku cloudu. Snímek oblačnosti začal na tomto zařízení kolem 23:50.

![Primární využití kapacity po snímku cloudu](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

Pokud teď spustíte iO na hostiteli připojeném k vašemu zařízení StorSimple, zobrazí se zvýšení primárního vrstveného úložiště nebo primárního místně připojeného úložiště používaného v závislosti na svazcích (vrstvených nebo místně vázaných) zapisujete data. Zde jsou primární tabulky využití úložiště pro zařízení StorSimple. Na tomto zařízení hostitel StorSimple začal obsluhovat zápisy kolem 14:30 na vrstvené svazku v zařízení. Můžete vidět vrchol v bajtů zápisu /s odpovídající VO spuštěné na hostiteli.

![Výkon při spuštění vstupně-in na vrstvených svazcích](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

Pokud se podíváte na primární vrstvené úložiště používá, který se zvýšil vzhledem k tomu, že primární místně připnuté využití zůstane beze změny, protože neexistují žádné zápisy obsluhované místně vázaných svazků v zařízení.

![Primární využití kapacity, když jsou vstupně-místy spuštěny na vrstvených svazcích](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

Pokud používáte aktualizaci 3 nebo vyšší, můžete rozdělit využití primární kapacity úložiště podle jednotlivého svazku, všech svazků, všech vrstvených svazků a všech místně vázaných svazků, jak je znázorněno níže. Rozdělení podle všech místně vázaných svazků vám umožní rychle zjistit, kolik místní vrstvy je vyděláno.

![Primární využití kapacity pro všechny vrstvené svazky](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![Primární využití kapacity pro všechny místně vázaných svazků](./media/storsimple-8000-monitor-device/monitor-usage4.png)

Můžete dále kliknout na každý ze svazků v seznamu a zobrazit odpovídající využití.

![Primární využití kapacity pro všechny místně vázaných svazků](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>Využití cloudového úložiště
Tyto grafy ukazují velikost použitého cloudového úložiště. Tato data jsou deduplikována a komprimována. Tato částka zahrnuje snímky cloudu, které mohou obsahovat data, která se neprojeví v žádném primárním svazku a jsou uchovávány pro starší nebo požadované účely uchovávání informací. Můžete porovnat údaje o spotřebě primárního a cloudového úložiště, abyste získali představu o rychlosti snížení dat, i když číslo nebude přesné.

Následující grafy ukazují využití cloudového úložiště zařízení StorSimple při pořízení snímku cloudu.

* Snímek cloudu začal na tomto zařízení kolem 11:50 a můžete vidět, že před snímekcloudu cloudu nebylo použito žádné cloudové úložiště. 
* Po dokončení snímku cloudu se využití cloudového úložiště zvýšilo o 0,89 GB. 
* Zatímco snímek cloudu probíhal, je také odpovídající vrchol v iO ze zařízení do cloudu.

    ![Využití cloudového úložiště před cloudovým snímkem](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![Využití cloudového úložiště po snímku cloudu](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![IO ze zařízení do cloudu během snímku cloudu](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>Využití místního úložiště
Tyto grafy zobrazují celkové využití zařízení, což bude více než využití primárního úložiště, protože zahrnuje lineární vrstvu SSD. Tato úroveň obsahuje množství dat, která také existuje na ostatních vrstvách zařízení. Kapacita v lineární vrstvě SSD je cyklitován tak, že když nová data přijde, stará data se přesune do vrstvy HDD (v té době je deduplikována a komprimována) a následně do cloudu.

V průběhu času se primární úložiště a místní úložiště bude s největší pravděpodobností zvyšovat společně, dokud se data nezačnou vrstvené do cloudu. V tomto okamžiku se místní úložiště pravděpodobně začne platová plocha, ale primární úložiště použité zvýší jako další data je zapsána.

Následující grafy ukazují primární úložiště používané pro zařízení StorSimple při pořízení snímku cloudu. Snímek cloudu začal v 11:50 a místní úložiště začalo klesat v té době. Použité místní úložiště se snížilo z 1.445 GB na 1.09 GB. To znamená, že s největší pravděpodobností nekomprimovaná data v lineární vrstvě SSD byla deduplikována, komprimována a přesunuta do vrstvy HDD. Všimněte si, že pokud zařízení již má velké množství dat v obou vrstev SSD a HDD, nemusí vidět tento pokles. V tomto příkladu má zařízení malé množství dat.

![Využití místního úložiště po snímku cloudu](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>Výkon
**Výkon** sleduje metriky související s počtem operací čtení a zápisu mezi rozhraními iniciátoru iSCSI na hostitelském serveru a zařízení nebo zařízení a cloudu. Tento výkon lze měřit pro určitý svazek, kontejner určitého svazku nebo všechny kontejnery svazků. Výkon zahrnuje také využití procesoru a propustnost sítě pro různá síťová rozhraní v zařízení.

### <a name="io-performance-for-initiator-to-device"></a>Vstupně-no výkon pro iniciátor zařízení
Níže uvedená tabulka ukazuje vstupně-iniciatele do zařízení pro všechny svazky pro produkční zařízení. Vykreslené metriky jsou čtení a zápis bajtů za sekundu. Můžete také graf číst, psát a nevyřízené vi, nebo číst a zapisovat latence.

![Výkon iniciátoru k zařízení](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>Vstupně-výkon pro zařízení do cloudu
Pro stejné zařízení jsou vstupně-nosné operace vykresleny pro data ze zařízení do cloudu pro všechny kontejnery svazků. Na tomto zařízení jsou data pouze v lineární vrstvě a nic se nepřelilo do cloudu. Neexistují žádné operace čtení a zápisu dochází ze zařízení do cloudu. Proto jsou vrcholy v grafu v intervalu 5 minut, který odpovídá frekvenci, při které je prezenční signál kontrolován mezi zařízením a službou.

Pro stejné zařízení byl pořízen snímek cloudu pro data svazku od 11:50. Výsledkem jsou data proudící ze zařízení do cloudu. Zápisy byly doručeny do cloudu v této době trvání. Graf vstupně-amp; Graf zobrazuje vrchol v zápisu bajtů/s odpovídající době, kdy byl pořízen snímek.

![IO ze zařízení do cloudu během snímku cloudu](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>Síťová propustnost pro síťová rozhraní zařízení
**Propustnost sítě** sleduje metriky související s množstvím dat přenesených ze síťových rozhraní iniciátoru iSCSI na hostitelském serveru a zařízení a mezi zařízením a cloudem. Tuto metriku můžete sledovat pro každé síťové rozhraní iSCSI v zařízení.

Následující grafy zobrazují propustnost sítě Data 0, 1 1 GbE v zařízení, která byla povolena v cloudu (výchozí) i iSCSI. Na tomto zařízení v červnu 14 kolem 21:00 byla data vrstvena do cloudu (v té době nebyly pořízeny žádné snímky cloudu, což ukazuje na vrstvení, což je mechanismus pro přesunutí dat do cloudu), což vedlo k tomu, že vstupně-vysílání se obsluhovalo do cloudu. V grafu propustnosti sítě je odpovídající špička pro stejnou dobu a většina síťového provozu je odchozí do cloudu.

![Síťová propustnost pro data 0](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

Podíváme-li se na propustnost rozhraní Data 1 graf, další 1 GbE síťové rozhraní, které bylo povoleno pouze iSCSI, pak tam byl prakticky žádný síťový provoz v této době trvání.

![Síťová propustnost pro data 1](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>Využití procesoru pro zařízení
**Využití procesoru** sleduje metriky související s procesorem používaným ve vašem zařízení. Následující graf ukazuje statistiky využití procesoru pro zařízení v produkčním prostředí.

![Využití procesoru pro zařízení](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>Další kroky
* Přečtěte si, jak [používat řídicí panel služby StorSimple Device Manager](storsimple-device-dashboard.md).
* Přečtěte si, jak [používat službu StorSimple Device Manager ke správě zařízení StorSimple](storsimple-manager-service-administration.md).

