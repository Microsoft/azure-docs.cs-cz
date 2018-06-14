---
title: Monitorování zařízení řady StorSimple 8000 | Microsoft Docs
description: Popisuje, jak používat službu StorSimple Manager zařízení sledovat využití, vstupně-výstupní výkon a využití kapacity.
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
ms.openlocfilehash: 679c1fc8775ad4481bc99c9aea79fe16e9bcac8f
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2017
ms.locfileid: "23933141"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>Použít službu StorSimple Manager zařízení k monitorování zařízení StorSimple

## <a name="overview"></a>Přehled
Monitorování konkrétní zařízení v rámci vašeho řešení StorSimple můžete použít službu StorSimple Manager zařízení. Můžete vytvořit vlastní grafy na základě vstupně-výstupní výkon, využití kapacity, propustnost sítě a metriky výkonu zařízení a připnout do řídicího panelu. Další informace, přejděte na [přizpůsobit řídicího panelu portálu](../azure-portal/azure-portal-dashboards.md).

K zobrazení informací o monitorování pro určité zařízení, na portálu Azure, vyberte službu StorSimple Manager zařízení. V seznamu zařízení vyberte zařízení a potom přejděte na **monitorování**. Můžete se podívat **kapacity**, **využití**, a **výkonu** grafy pro vybrané zařízení.

## <a name="capacity"></a>Kapacita
**Kapacita** sleduje zřízené místa a místa zůstává v zařízení. Zbývající kapacita se následně zobrazí jako místně vázaný nebo vrstvené.

Kapacita zřízený a zbývajících je další členěné podle vrstvené a místně vázaných svazků. Pro každý svazek se zobrazí zřízená kapacita a zbývající kapacita v zařízení.

![Kapacita v/v](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>Využití
**Využití** sleduje metriky týkající se množství prostoru úložiště dat, který je používán svazky, kontejnery svazků nebo zařízení. Můžete vytvořit sestavy na základě využití kapacity primárního úložiště, cloudového úložiště nebo úložiště zařízení. Využití kapacity lze měřit na konkrétním svazku, kontejner svazků konkrétní nebo všechny kontejnery svazků.
Ve výchozím nastavení je uvedená využití za posledních 24 hodin. Můžete upravit graf a změňte trvání, přes který je hlášen výběr z použití:
* Za posledních 24 hodin
* Počet uplynulých 7 dní
* Posledních 30 dnů
* Posledních 90 dnů
* Za poslední rok

Dva klíče metrices, růstu a rozsah jsou hlášené pro grafy využití. Rozsah odkazuje na maximální hodnotu a minimální hodnoty využití hlášené přes vybrané doby trvání (fo instance, posledních 7 dní).

Růst popisuje nárůst využití od prvního dne na posledního dne v vybrané doby trvání. 

Růst a rozsah mohou být také reprezentována pomocí následujícího vzorce:

```
Range = {Usage(minimum), Usage(maximum)}

Growth = Usage(Last day) - Usage(first day)

Growth (%) = [{Usage(last day) - Usage(first day)} X 100]/Usage(first day)
```

Primární, cloud a místní úložiště používá lze popsat následujícím způsobem:

### <a name="primary-storage-usage"></a>Využití primárního úložiště
Tyto grafy zobrazují množství dat zapsaných na svazky zařízení StorSimple, než je s odstraněním duplicitních dat a komprimované data. Můžete zobrazit primárního úložiště používané všechny svazky v kontejneru svazků nebo pro samostatný svazek. Primárního úložiště používá další členěné podle primární vrstveného úložiště používá a primární místně připnutý úložiště použít.

Následující graf zobrazuje primárního úložiště používá pro zařízení StorSimple před a po pořízení snímku cloudu. Protože se právě objem dat, cloudový snímek neměli měnit primárního úložiště. Jak vidíte, graf zobrazuje žádné rozdíly v primární vrstvené nebo místně vázaný úložiště používá v důsledku pořizování snímku cloudu. Cloudový snímek zahájená přibližně 23:50:00 na daném zařízení.

![Využití kapacity primární po cloudový snímek](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

Pokud teď spustit vstupně-výstupních operací na hostiteli připojen k zařízení StorSimple, zobrazí se o zvýšení v primární vrstveného úložiště nebo primární místně připnutý úložiště používané v závislosti na svazků, které (víceúrovňová nebo místně vázaný) můžete zapsat data do. Tady jsou grafy využití primárního úložiště pro zařízení StorSimple. Na tomto zařízení StorSimple hostiteli spuštěn obsluhující zapíše na přibližně 2:30 pm na vrstvený svazek v zařízení. Můžete zobrazit ve špičce v zápisu odpovídající vstupně-výstupních operací na hostiteli spuštěna bajtů/s.

![Výkon, pokud je spuštěn vstupně-výstupní operace zřízeny vrstvené svazky](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

Pokud se podíváte na primární vrstveného úložiště používá, který přešel zatímco primární místně připnutý, že využití zůstává beze změny, protože neexistují žádné zápisy dodávat do místně vázaných svazků v zařízení.

![Využití kapacity primární při vstupně-výstupní operace jsou spuštěny na vrstvené svazky](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

Pokud používáte aktualizací 3 nebo vyšší, lze rozčlenit využití kapacity primárního úložiště ve svazku, všechny svazky, všechny vrstvené svazky a všechny místně vázaných svazků jak je uvedeno níže. Rozdělení podle všechny místně vázaných svazků vám umožní rychle zjistit, kolik místní vrstvy se používá.

![Využití primární kapacity pro všechny vrstvené svazky](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![Využití primární kapacity pro všechny místně vázaných svazků](./media/storsimple-8000-monitor-device/monitor-usage4.png)

Můžete dál klikněte na každý ze svazků v seznamu a zobrazit odpovídající využití.

![Využití primární kapacity pro všechny místně vázaných svazků](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>Využití úložiště cloudu
Tyto grafy zobrazují množství používá úložiště v cloudu. Tato data se odstraňují a komprimované. Toto množství zahrnuje cloudových snímků, které může obsahovat data, která se nereflektují v jakýkoli svazek, primární a je uložen pro účely starší verze nebo požadované uchování. Můžete porovnat primární a cloudu údajů spotřeba úložiště, kde získáte představu snížení míry data, i když počet nesmí být přesná.

Následující graf zobrazuje využití úložiště cloudu zařízení StorSimple při pořízení snímku cloudu.

* Cloudový snímek zahájená přibližně 11:50 am na daném zařízení a uvidíte, že před cloudu snímku, byl žádné úložiště v cloudu používat. 
* Jednou cloudový snímek dokončit, využití úložiště cloudu snímek až rovný 0,89 GB. 
* Během provádění cloudový snímek, je zde i odpovídající ve špičce vstupně-výstupní operace ze zařízení do cloudu.

    ![Využití úložiště cloudu před cloudový snímek](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![Využití úložiště cloudu po cloudový snímek](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![Vstupně-výstupní operace ze zařízení do cloudu během cloudový snímek](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>Využití místní úložiště
Tyto grafy zobrazují celkové využití pro zařízení, která bude použití více než primárního úložiště, protože obsahuje lineární vrstvy SSD. Tato úroveň obsahuje množství dat, která také existuje na zařízení je jiné úrovně. Kapacity ve vrstvě SSD lineární je procházení tak, aby nová data se dodává, stará data bude přesunuta do vrstvy HDD (kdy je s odstraněním duplicitních dat a komprimované) a následně do cloudu.

Nad úložištěm doby, primární použité a místní úložiště používá pravděpodobně zvýší společně dokud data začne být rozvrstvena do cloudu. Od tohoto okamžiku použít místní úložiště zahájíte pravděpodobně stagnovat, ale primárního úložiště používá zvýší při další data jsou zapsána.

Následující graf zobrazuje primárního úložiště používá se pro zařízení StorSimple při pořízení snímku cloudu. Cloudový snímek zahájená 11:50 am a místní úložiště spustit snížení v daném čase. Místní úložiště používá snížil z 1.445 GB na 1,09 GB. To označuje, že s největší pravděpodobností nekomprimovaných dat ve vrstvě SSD lineární bylo odstranění duplicit, komprimované a přesunout do vrstvy HDD. Všimněte si, že pokud zařízení už má velké množství dat SSD a HDD vrstev, nemusíte vidět tento pokles. V tomto příkladu zařízení má malou část data.

![Využití místní úložiště po cloudový snímek](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>Výkon
**Výkon** sleduje metriky týkající se počet čtení a zápisu operace mezi serverem buď rozhraní iniciátor iSCSI na hostitelském serveru a zařízení nebo zařízení a cloudem. Tento výkon lze měřit pro konkrétní svazku, kontejner svazků konkrétní nebo všechny kontejnery svazků. Výkon zahrnuje taky využití procesoru a propustnost sítě pro rozhraní různé sítě na vašem zařízení.

### <a name="io-performance-for-initiator-to-device"></a>Vstupně-výstupní výkon iniciátorů zařízení
Následující graf zobrazuje vstupně-výstupních operací pro iniciátory pro vaše zařízení pro všechny svazky pro produkční zařízení. Metriky vykreslí jsou čtení a zápisu bajtů za sekundu. Můžete také grafu pro čtení, zápisu a nezpracovaných vstupně-výstupní operace, nebo čtení a zápisu latenci.

![Výkon vstupně-výstupní operace pro iniciátor zařízení](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>Vstupně-výstupní výkon pro zařízení do cloudu
Pro stejného zařízení jsou zobrazeny vstupně-výstupních operací pro data ze zařízení do cloudu pro všechny kontejnery svazků. Na tomto zařízení data se pouze ve vrstvě lineární a nic se uniknout do cloudu. Neprobíhají žádné operace čtení a zápis, ke kterým dochází ze zařízení do cloudu. Proto jsou vrcholů v grafu v intervalu 5 minut, který odpovídá frekvenci, kdy prezenční signál je zaškrtnuta možnost mezi zařízením a služby.

Pro stejné zařízení pořízení snímku cloudu pro svazek data začínající na 11:50 am. Výsledkem dat odesílaných ze zařízení do cloudu. Zápisy byly zpracovat do cloudu v této hodnotě duration. Vstupně-výstupní operace graf zobrazuje ve špičce zápisu bajtů/s odpovídající čas při pořízení snímku.

![Vstupně-výstupní operace ze zařízení do cloudu během cloudový snímek](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>Propustnost sítě pro zařízení Síťová rozhraní
**Propustnost sítě** sleduje metriky týkající se množství dat přenesených z rozhraní sítě iniciátor iSCSI na hostitelském serveru a zařízení a mezi zařízením a cloudem. Tato metrika pro každou síťová rozhraní iSCSI můžete sledovat na vašem zařízení.

Následující graf zobrazuje propustnost sítě pro Data 0, 1 1 GbE sítě na vašem zařízení, která byla obou povolenou podporu cloudu (výchozí) a podporou iSCSI. Na tomto zařízení na června 14 v přibližně 21: 00, byl vrstvené data do cloudu (žádné cloudové snímky byly provedeny v daném čase, který odkazuje na vrstvení se tento mechanismus pro přesun dat do cloudu) což vedlo ke vstupně-výstupní operace přístup do cloudu. Neexistuje odpovídající ve špičce v grafu propustnost sítě pro stejnou dobu a většina síťového provozu je odchozí do cloudu.

![Propustnost sítě pro Data 0](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

Pokud podíváme na graf propustnost rozhraní Data 1, jiné 1gbe síťové rozhraní, která byla pouze iSCSI povolený, pak se prakticky žádné síťové přenosy v této hodnotě duration.

![Propustnost sítě pro Data 1](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>Využití procesoru pro zařízení
**Využití procesoru** sleduje metriky související s procesoru použity na zařízení. Následující graf zobrazuje statistik využití procesoru pro zařízení v provozním prostředí.

![Využití procesoru pro zařízení](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>Další kroky
* Zjistěte, jak [pomocí řídicího panelu zařízení služby StorSimple Manager zařízení](storsimple-device-dashboard.md).
* Zjistěte, jak [použít službu StorSimple Manager zařízení ke správě zařízení StorSimple](storsimple-manager-service-administration.md).

