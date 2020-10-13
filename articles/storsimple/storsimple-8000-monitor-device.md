---
title: Monitorování zařízení řady StorSimple 8000
description: Popisuje, jak používat službu StorSimple Device Manager ke sledování využití, výkonu vstupu a výstupu a využití kapacity.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 10/17/2017
ms.author: alkohli
ms.openlocfilehash: 7ffb13912780edcf3d92093e672d1dae983e50a8
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977404"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>Monitorování zařízení StorSimple pomocí služby StorSimple Device Manager

## <a name="overview"></a>Overview
Službu StorSimple Device Manager můžete použít k monitorování konkrétních zařízení v rámci řešení StorSimple. Můžete vytvářet vlastní grafy založené na výkonu vstupu a výstupu, využití kapacity, propustnost sítě a metriky výkonu zařízení a připnout je na řídicí panel. Další informace najdete na webu [přizpůsobení řídicího panelu portálu](../azure-portal/azure-portal-dashboards.md).

Chcete-li zobrazit informace o monitorování určitého zařízení, vyberte v Azure Portal službu Device Manager StorSimple. V seznamu zařízení vyberte zařízení a pak přejít na **monitor**. Pak můžete zobrazit diagramy **kapacity**, **využití**a **výkonu** pro vybrané zařízení.

## <a name="capacity"></a>Kapacita
**Kapacita** sleduje zřízené místo a zbývající místo na zařízení. Zbývající kapacita se pak zobrazí jako připnuté místně nebo na vrstvenou.

Zajištěná a zbývající kapacita je dále rozdělená na vrstvené a místně připnuté svazky. Pro každý svazek se zobrazí zřízená kapacita a zbývající kapacita zařízení.

![Kapacita v/v](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>Využití
**Využití** sleduje metriky související s objemem úložného prostoru dat, který používají svazky, kontejnery svazků nebo zařízení. Můžete vytvářet sestavy založené na využití kapacity vašeho primárního úložiště, cloudového úložiště nebo úložiště zařízení. Využití kapacity se dá měřit na konkrétním svazku, konkrétním kontejneru svazků nebo všech kontejnerech svazků.
Ve výchozím nastavení je hlášeno použití za posledních 24 hodin. Úpravou grafu můžete změnit dobu trvání, po které se bude vykazovat využití, a to výběrem možnosti:
* Posledních 24 hodin
* Posledních 7 dní
* Posledních 30 dní
* Posledních 90 dní
* Minulý rok

Dvě klíčové metriky, růst a rozsah jsou hlášeny pro grafy využití. Rozsah odkazuje na maximální hodnotu a minimální hodnoty využití nahlášené přes vybranou dobu trvání (FO instance, posledních 7 dní).

Nárůst znamená zvýšení využití od prvního dne k poslednímu dni ve vybrané době trvání. 

Růst a rozsah můžou být také reprezentované následujícími rovnicemi:

```
Range = {Usage(minimum), Usage(maximum)}

Growth = Usage(Last day) - Usage(first day)

Growth (%) = [{Usage(last day) - Usage(first day)} X 100]/Usage(first day)
```

Použití primárního, cloudového a místního úložiště lze popsat následujícím způsobem:

### <a name="primary-storage-usage"></a>Využití primárního úložiště
Tyto grafy zobrazují množství dat zapsaných na StorSimple svazky předtím, než se data odstraňují a komprimují. Můžete zobrazit primární úložiště používané všemi svazky v kontejneru svazků nebo pro jeden svazek. Použité primární úložiště je dále rozdělené podle použitého primárního vrstveného úložiště a použitého primárního místně připojeného úložiště.

Následující grafy ukazují primární úložiště použité pro zařízení StorSimple před a po pořízení snímku cloudu. Vzhledem k tomu, že se jedná o data svazku, by snímek v cloudu neměl měnit primární úložiště. Jak vidíte, graf nezobrazuje žádný rozdíl v primárním vrstveném nebo místně připojeném úložišti, které se používá v důsledku pořízení snímku v cloudu. Snímek cloudu začal přibližně 11:50 pm na tomto zařízení.

![Využití primární kapacity po snímku cloudu](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

Pokud teď spouštíte v/v hostitele připojeného k vašemu zařízení StorSimple, uvidíte zvýšení primárního vrstveného úložiště nebo primárního místně připojeného úložiště, které se používá v závislosti na tom, které svazky (vrstvené nebo místně připnuté) zapisujete data do. Tady jsou grafy využití primárního úložiště pro zařízení StorSimple. Na tomto zařízení Hostitel StorSimple zahájil obsluhu zápisů na vrstvených svazcích na zařízení v přibližně 2:30 PM. Můžete si prohlédnout špičku v bajtech zápisu za sekundu odpovídající vstupně-výstupní práci na hostiteli.

![Výkon při spuštění v/v na vrstveném svazku](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

Pokud se podíváte na použité primární vrstvené úložiště, které se zobrazí, zatímco primární místní připnuté použití zůstane beze změny, protože na místně připnuté svazky na zařízení nezpracovávají žádné zápisy.

![Využití primární kapacity při spuštění v/v vrstvených svazcích](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

Pokud používáte aktualizaci 3 nebo vyšší, můžete snížit využití kapacity primárního úložiště jednotlivému svazku, všemi svazky, všemi vrstvenými svazky a všemi místně připnuté svazky, jak je znázorněno níže. Rozdělení na všechny místně připojené svazky vám umožní rychle zjistit, jak velká je místní vrstva.

![Využití primární kapacity pro všechny vrstvené svazky](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![Využití primární kapacity pro všechny místně připojené svazky](./media/storsimple-8000-monitor-device/monitor-usage4.png)

Můžete dále kliknout na jednotlivé svazky v seznamu a zobrazit odpovídající použití.

![Využití primární kapacity na všech místně připojených svazcích 2](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>Využití cloudového úložiště
V těchto grafech se zobrazuje velikost využitého cloudového úložiště. Tato data jsou Odstraněná a komprimovaná. Tato velikost zahrnuje cloudové snímky, které by mohly obsahovat data, která se neprojevují v žádném primárním svazku a jsou zachovaná pro starší verze nebo pro požadované účely uchování. Můžete porovnat údaje o využití primárního a cloudového úložiště, abyste získali představu o míře omezení dat, i když číslo nebude přesné.

Následující grafy ukazují využití cloudového úložiště zařízení StorSimple při pořízení snímku v cloudu.

* Snímek cloudu začal v přibližně 11:50 na tomto zařízení a vidíte ho před snímkem cloudu. nepoužilo se žádné cloudové úložiště. 
* Po dokončení snímku cloudu se využití cloudového úložiště vyhledá 0,89 GB. 
* I když se snímkem v cloudu probíhal, v vstupně-výstupních operacích ze zařízení do cloudu je taky odpovídající špička.

    ![Využití cloudového úložiště před snímkem cloudu](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![Využití cloudového úložiště po snímku cloudu](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![Vstupně-výstupní operace ze zařízení do cloudu během snímku v cloudu](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>Využití místního úložiště
Tyto grafy zobrazují celkové využití pro zařízení, což bude víc než použití primárního úložiště, protože zahrnuje lineární vrstvu SSD. Tato úroveň obsahuje množství dat, která jsou také na dalších úrovních zařízení. Kapacita v lineární vrstvě SSD se cykluje, takže když se nová data objeví v, přesunou se stará data do vrstvy HDD (ve které je doba, která je Odstraněná a komprimovaná) a následně do cloudu.

V průběhu času se použité primární úložiště a použité místní úložiště nejvíce zvyšují dohromady, dokud se data nezačnou vrstvit do cloudu. V tomto okamžiku začne místní úložiště pravděpodobně stabilní úrovně, ale použité primární úložiště se zvýší, protože se zapisují další data.

Následující grafy ukazují primární úložiště použité pro zařízení StorSimple při pořízení snímku v cloudu. Snímek cloudu začal v 11:50 a místní úložiště se v tuto chvíli začala zmenšovat. Využité místní úložiště se snížilo z 1,445 GB na 1,09 GB. To znamená, že pravděpodobně nekomprimovaná data v lineární vrstvě SSD byla odstraněna, komprimována a přesunuta do vrstvy HDD. Všimněte si, že pokud už zařízení obsahuje velké množství dat v vrstvách SSD i HDD, toto zmenšení se nemusí sledovat. V tomto příkladu má zařízení malé množství dat.

![Využití místního úložiště po snímku cloudu](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>Výkon
**Výkon** sleduje metriky související s počtem operací čtení a zápisu mezi rozhraními iniciátoru iSCSI na hostitelském serveru a zařízením nebo zařízením a cloudem. Tento výkon se dá změřit pro konkrétní svazek, konkrétní kontejner svazků nebo všechny kontejnery svazků. Výkon taky zahrnuje využití CPU a propustnost sítě pro různá síťová rozhraní v zařízení.

### <a name="io-performance-for-initiator-to-device"></a>Výkon vstupu a výstupu pro iniciátora na zařízení
Následující graf zobrazuje vstupně-výstupní operace pro iniciátora zařízení pro všechny svazky v produkčním zařízení. Vykreslené metriky jsou přečtené a zapsané bajty za sekundu. Můžete také zobrazit, zapsat a nezpracované vstupně-výstupní operace a latenci čtení a zápisu.

![Výkon v/v pro iniciátora zařízení](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>Výkon v/v pro zařízení do cloudu
U stejného zařízení se vstupně-výstupní operace vykreslí pro data ze zařízení do cloudu pro všechny kontejnery svazků. V tomto zařízení jsou data jenom v lineární vrstvě a nic se nepředalo do cloudu. Neexistují žádné operace čtení a zápisu, které se nacházejí ze zařízení do cloudu. Proto se špičky v grafu nacházejí v intervalu 5 minut, který odpovídá četnosti, s jakou se prezenční signál kontroluje mezi zařízením a službou.

U stejného zařízení byl snímek v cloudu pořízený pro data o svazcích od 11:50. Výsledkem je tok dat ze zařízení do cloudu. V této době byly do cloudu obsluhovány zápisy. V/v grafu se zobrazuje špička v bajtech zápisu za sekundu odpovídající době pořízení snímku.

![Vstupně-výstupní operace ze zařízení do cloudu během snímku v cloudu](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>Propustnost sítě pro síťová rozhraní zařízení
**Propustnost sítě** sleduje metriky související s objemem dat přenesených z síťových rozhraní iniciátoru iSCSI na hostitelském serveru a v zařízení a mezi zařízením a cloudem. Tuto metriku můžete monitorovat pro každé ze síťových rozhraní iSCSI na vašem zařízení.

Následující grafy ukazují propustnost sítě pro síť data 0, 1 1 GbE v zařízení, která byla povolena pro Cloud (výchozí nastavení) i pro iSCSI. Na tomto zařízení 14. června ODP. června se data nacházela do cloudu (v tuto chvíli se v tomto okamžiku neprováděly žádné cloudové snímky, které odkazují na vrstvení, aby přesunuli data do cloudu), což vedlo k provozu do cloudu. V grafu propustnosti sítě je k dispozici odpovídající špička ve stejnou dobu a většina síťových přenosů je odchozí do cloudu.

![Propustnost sítě pro data 0](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

Pokud se podíváme na graf propustnosti rozhraní data 1, další síťové rozhraní, které bylo jenom standardu iSCSI, pak v tuto dobu existoval prakticky žádný síťový provoz.

![Propustnost sítě pro data 1](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>Využití procesoru pro zařízení
**Využití CPU** sleduje metriky související s využitím procesoru na vašem zařízení. Následující graf znázorňuje statistiku využití procesoru pro zařízení v produkčním prostředí.

![Využití procesoru pro zařízení](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>Další kroky
* Naučte se [používat řídicí panel zařízení StorSimple Device Manager Service](storsimple-device-dashboard.md).
* Naučte se [používat službu StorSimple Device Manager ke správě zařízení StorSimple](storsimple-manager-service-administration.md).

