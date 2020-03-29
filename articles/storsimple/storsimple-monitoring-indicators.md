---
title: StorJednoduché monitorovací ukazatele | Dokumenty společnosti Microsoft
description: Popisuje diody vyzařující světlo (LED) a zvukové alarmy používané ke sledování stavu zařízení StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 59dee7b9-ca6d-4fd9-96e6-a0071e8d248e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: ef8acf1c3c9211168ebacc8d62647f6789c745a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60630560"
---
# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>Správa zařízení pomocí indikátorů monitorování StorSimple


## <a name="overview"></a>Přehled
Zařízení StorSimple obsahuje diody vyzařující světlo (LED) a alarmy, které můžete použít ke sledování modulů a celkového stavu zařízení StorSimple. Indikátory monitorování naleznete na hardwarových komponentách primárnískříně zařízení a skříni EBOD. Indikátory monitorování mohou být buď LED diody nebo zvukové alarmy.

K označení stavu modulu se používají tři stavy LED diod: zelená, blikající zelená až červená oranžová nebo červená oranžová.  

* Zelené LED diody představují zdravý provozní stav.  
* Blikající zelené až červeně jantarové LED diody představují přítomnost nekritických stavů, které mohou vyžadovat zásah uživatele.  
* Červeně jantarové LED diody indikují, že v modulu je přítomna kritická závada.  

Zbývající část tohoto článku popisuje různé indikátory monitorování LED, jejich umístění na zařízení StorSimple, stav zařízení na základě stavů LED diod a všechny přidružené zvukové alarmy.

## <a name="front-panel-indicator-leds"></a>LED diody indikátoru předního panelu
Přední panel, známý také jako *ovládací panel* nebo *operační panel*, zobrazuje souhrnný stav všech modulů v systému. Přední panel je identický na Primární StorSimple a EBOD skříň, a je znázorněno níže.  

   ![Přední panel zařízení][1]

Přední panel obsahuje následující indikátory:  

1. Tlačítko Ztlumit
2. Indikátor napájení LED (zelená/červená-oranžová)
3. Indikátor poruchy modulu LED (SVÍTÍ červeně oranžově/vypnuto)
4. Indikátor logické poruchy LED (SVÍTÍ červeně oranžově/OFF
5. Zobrazení ID jednotky  

Hlavní rozdíl mezi LED diodami předního panelu pro zařízení a led pro skříň EBOD je **identifikační číslo systémové jednotky** zobrazené na LED displeji. Výchozí ID jednotky zobrazené na zařízení je **00**, zatímco výchozí ID jednotky zobrazené ve skříni EBOD je **01**. To vám umožní rychle rozlišovat mezi zařízením a skříní EBOD, když je zařízení zapnuto. Pokud je vaše zařízení vypnuté, použijte informace uvedené v [Zapnuto nové zařízení](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) k odlišení zařízení od skříně EBOD.  

## <a name="front-panel-led-status"></a>Stav LED na předním panelu
V následující tabulce můžete identifikovat stav označený LED diodami na předním panelu zařízení nebo skříně EBOD.  

| Napájení systému | Porucha modulu | Logická chyba | Alarm | Status |
| --- | --- | --- | --- | --- |
| Červená oranžová |OFF |OFF |Není dostupné. |Ztráta napájení ze střídavého proudu, provoz na záložní napájení nebo zapnutí střídavého proudu a moduly řadiče byly odstraněny. |
| Green |ON |ON |Není dostupné. |Zapnutí zkušebního stavu panelu Ops (5s) |
| Green |OFF |OFF |Není dostupné. |Zapnutí, všechny funkce dobré |
| Green |ON |Není dostupné. |PCM poruchy LED, ventilátor poruchy LED |Jakákoli porucha PCM, porucha ventilátoru, nad nebo za teplotě |
| Green |ON |Není dostupné. |Led diody i/o modulu |Jakákoli porucha modulu regulátoru |
| Green |ON |Není dostupné. |Není dostupné. |Chyba logiky skříně |
| Green |Flash |Není dostupné. |Stavová LED dioda modulu modulu modulu modulu modulu. PCM poruchy LED, ventilátor poruchy LED |Neznámý typ modulu řadiče nainstalován, selhání sběrnice I2C, chyba konfigurace konfiguračních dat v systému 2000, chyba konfigurace v systému 2000 –VPD |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Indikátorové LED diody modulu chlazení (PCM)
Indikátorové LED diody napájecího chladicího modulu (PCM) naleznete na zadní straně primární skříně nebo skříně EBOD na každém pcm modulu. Toto téma popisuje, jak pomocí následujících LED diod sledovat stav zařízení StorSimple.  

* LED diody PCM pro primární skříň
* LED diody PCM pro skříň EBOD

## <a name="pcm-leds-for-the-primary-enclosure"></a>LED diody PCM pro primární skříň
Zařízení StorSimple má 764W PCM modul s další baterií. Následující obrázek znázorňuje LED panel zařízení.  

   ![LED diody PCM na primární skříni][2]

LED legenda:

1. Výpadku napájení střídavého proudu
2. Selhání ventilátoru
3. Porucha baterie
4. PCM je v pořádku
5. Selhání řadiče domény
6. Baterie je v pořádku  

Stav PCM je indikován na LED panelu. Panel PCM LED zařízení má šest LED diod. Čtyři z těchto LED diod zobrazují stav napájecího zdroje a ventilátoru. Zbývající dvě LED diody indikují stav záložního bateriového modulu v PCM. Stav PCM můžete použít v následujících tabulkách.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>LED LED diody INDIKÁTOR PCM pro napájení a ventilátor
| Status | PCM OK (zelená) | Selhání střídavého aktu (oranžová) | Selhání ventilátoru (oranžová) | Selhání řadiče domény (oranžová) |
| --- | --- | --- | --- | --- |
| Žádný střídavý proud (do skříně) |OFF |OFF |OFF |OFF |
| Bez střídavého proudu (pouze tento PCM) |OFF |ON |OFF |ON |
| AC současné PCM ON - OK |ON |OFF |OFF |OFF |
| PCM selhání (ventilátor selhání) |OFF |OFF |ON |Není dostupné. |
| PcM porucha (přes zesilovač, přepětí, přes proud) |OFF |ON |ON |ON |
| PCM (ventilátor mimo toleranci) |ON |OFF |OFF |ON |
| Pohotovostní režim |Blikající |OFF |OFF |OFF |
| Pcm firmware ke stažení |OFF |Blikající |Blikající |Blikající |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>LED led diody INDIKÁTOR PCM pro záložní baterii
| Status | Baterie dobrá (zelená) | Porucha baterie (oranžová) |
| --- | --- | --- |
| Baterie není k dispozici |OFF |OFF |
| Baterie přítomna a nabitá |ON |OFF |
| Nabíjení nebo údržba baterie |Blikající |OFF |
| Baterie "soft" porucha (obnovitelné) |OFF |Blikající |
| Porucha baterie "tvrdá" (neobnovitelná) |OFF |ON |
| Baterie byla odjištěna |Blikající |OFF |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>LED diody PCM pro skříň EBOD
Skříň EBOD má 580W PCM a žádnou další baterii. Panel PCM pro skříň EBOD má indikátorové LED diody pouze pro napájecí zdroje a ventilátor. Následující obrázek znázorňuje tyto LED diody.

   ![LED diody PCM na skříni EBOD][3] 

Stav PCM můžete použít v následující tabulce.  

| Status | PCM OK (zelená) | Selhání střídavého aktu (oranžová) | Selhání ventilátoru (oranžová) | Selhání řadiče domény (oranžová) |
| --- | --- | --- | --- | --- |
| Žádný střídavý proud (do skříně) |OFF |OFF |OFF |OFF |
| Bez střídavého proudu (pouze tento PCM) |OFF |ON |OFF |ON |
| AC současné PCM ON - OK |ON |OFF |OFF |OFF |
| PCM selhání (ventilátor selhání) |OFF |OFF |ON |× |
| PcM porucha (přes zesilovač, přepětí, přes proud |OFF |ON |ON |ON |
| PCM (ventilátor mimo toleranci) |ON |OFF |OFF |ON |
| Pohotovostní model |Blikající |OFF |OFF |OFF |
| Pcm firmware ke stažení |OFF |Blikající |Blikající |Blikající |

## <a name="controller-module-indicator-leds"></a>LED diody indikátoru modulu regulátoru
Zařízení StorSimple obsahuje LED diody pro primární regulátor a moduly regulátoru EBOD.   

### <a name="monitoring-leds-for-the-primary-controller"></a>Monitorování LED diod pro primární regulátor
Následující obrázek vám pomůže identifikovat LED diody na primárním řadiči. (Všechny součásti jsou uvedeny na podporu orientace.)  

   ![Monitorování LED diod - primární regulátor][4]

V následující tabulce můžete určit, zda modul řadiče funguje správně.  

### <a name="controller-indicator-leds"></a>LED diody indikátoru regulátoru
| Led | Popis |
| --- | --- |
| ID LED (modrá) |Označuje, že modul je identifikován. Pokud na běžícím ovladači bliká modrá LED dioda, je regulátor aktivním ovladačem a druhým je pohotovostní ovladač. Další informace naleznete [v tématu Identifikace aktivního ovladače v zařízení](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| Led dioda poruchy (oranžová) |Označuje chybu v ovladači. |
| OK LED (zelená) |Svítí zeleně označuje, že ovladač je v pořádku. Bliká zeleně označuje chybu konfigurace řadiče VPD. |
| Led diody aktivity SAS (zelené) |Svítí zeleně označuje připojení bez aktuální aktivity. Bliká zeleně označuje, že připojení má probíhající aktivitu. |
| Ethernetové stavové LED diody |Pravá strana označuje aktivitu spojení/sítě: (stabilní zelená) aktivní linka, (blikající zelená) síťová aktivita. Levá strana označuje rychlost sítě: (žlutá) 1000 Mb/s, (zelená) 100 Mb/s a (OFF) 10 Mb/s. V závislosti na modelu komponenty může toto kontrolka blikat i v případě, že síťové rozhraní není povoleno. |
| POST LED diody |Označuje průběh spuštění při zapnutí řadiče. Pokud se zařízení StorSimple nepodaří spustit, tato LED dioda pomůže podpoře společnosti Microsoft identifikovat bod spouštěcího procesu, při kterém došlo k chybě. |

> [!IMPORTANT]
> Pokud kontrolka KONTROLKA poruchy svítí, došlo k potížím s modulem řadiče, který by mohl být vyřešen restartováním ovladače. Pokud restartování řadiče nevyřeší tento problém, obraťte se na podporu společnosti Microsoft.  
> 
> 

### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>Monitorování LED diod pro EBOD (skříň EBOD)
Každý z 6 Gb/s Řadiče SAS EBOD má LED diody, které označují jeho stav, jak je znázorněno na následujícím obrázku.  

  ![Monitorování LED diod - skříň EBOD][5]

V následující tabulce můžete určit, zda modul řadiče EBOD pracuje normálně.  

### <a name="ebod-controller-module-indicator-leds"></a>Led diody indikátoru řídicího modulu EBOD
| Status | I/O modul OK (zelená) | Porucha i/o modulu (oranžová) | Aktivita hostitelského portu (zelená) |
| --- | --- | --- | --- |
| Modul řadiče je v pořádku |ON |OFF |- |
| Porucha modulu regulátoru |OFF |ON |- |
| Žádné připojení externího portu hostitele |- |- |OFF |
| Připojení externího portu hostitele – žádná aktivita |- |- |ON |
| Připojení externího portu hostitele – aktivita |- |- |Blikající |
| Chyba metadat modulu řadiče |Blikající |- |- |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>Led diody indikátoru diskového pohonu pro primární skříň a skříň EBOD
Zařízení StorSimple má diskové jednotky umístěné v primární skříni i ve skříni EBOD. Každá disková jednotka obsahuje indikátory LED, jak je popsáno v této části. 

U diskových jednotek je stav jednotky indikován zelenou LED diodou a červeno-oranžovou LED diodou namontovanou na přední straně každého modulu nosiče pohonu. Následující obrázek znázorňuje tyto LED diody.

  ![Led diody diskové jednotky][6]

Následující tabulka slouží k určení stavu každé diskové jednotky, což zase ovlivňuje celkový stav LED panelu.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>Led diody indikátoru diskového pohonu pro skříň EBOD
| Status | Aktivita OK LED (zelená) | Porucha LED (červená-oranžová) | Associated ops panel LED |
| --- | --- | --- | --- |
| Nebyla nainstalována žádná jednotka. |OFF |OFF |Žádný |
| Jednotka nainstalována a funkční |Bliká zapnutí/ vypnutí s aktivitou |× |Žádný |
| Sada identit zařízení služby SCSI Enclosure Services (SES) |ON |Bliká 1 sekunda na / 1 sekunda off |Žádný |
| Sada chybových bitů zařízení SES |ON |ON |Logická porucha (červená) |
| Porucha řídicího obvodu výkonu |OFF |ON |Porucha modulu (červená) |

## <a name="audible-alarms"></a>Akustionkteří
Zařízení StorSimple obsahuje zvukové alarmy spojené jak s primární skříní, tak s skříní EBOD. Na předním panelu (známém také jako panel ops) obou skříní je umístěn zvukový alarm. Zvukový alarm signalizuje, kdy je přítomen poruchový stav. Alarm se aktivuje následujícími podmínkami:  

* Porucha nebo porucha ventilátoru
* Napětí mimo dosah
* Nad nebo za teplotních podmínek
* Tepelný přeteče
* Závada systému
* Logická chyba
* Porucha napájení
* Odstranění modulu napájecího chlazení (PCM)  

Následující tabulka popisuje různé stavy alarmu.  

### <a name="alarm-states"></a>Stavy alarmů
| Stav alarmu | Akce | Akce s stisknutým tlačítkem ztlumení |
| --- | --- | --- |
| S0 |Normální režim: tichý |Pípnutí dvakrát |
| S1 |Režim poruchy: 1 sekunda zapnuto/ 1 sekunda vypnuto |Přechod na S2 nebo S3 (viz poznámky) |
| S2 |Režim Připomenutí: přerušované pípnutí |Žádný |
| S3 |Ztlumený režim: tichý |Žádný |
| S4 |Režim kritické poruchy: nepřetržitý poplach |Není k dispozici: ztlumení není aktivní |

> [!NOTE]
> * Ve stavu alarmu S1, pokud nestisknete ztlumení do 2 minut, stav se automaticky přepne na S2 nebo S3.  
> * Alarm uvádí S1 až S4 vrátit do S0 po poruchový stav je vymazán.  
> * Kritický stav poruchy S4 lze zadat z libovolného jiného stavu.  


Zvukový alarm můžete ztlumit stisknutím tlačítka ztlumení na panelu ops. Automatické ztlumení dojde po dvou minutách, pokud přepínač ztlumení není ovládán ručně. Po ztlumení alarmu bude nadále znít krátkým přerušovaným pípnutím, které signalizuje, že problém stále existuje. Alarm bude tichý, když jsou všechny problémy vymazány.

Následující tabulka popisuje různé podmínky alarmu.

### <a name="alarm-conditions"></a>Podmínky alarmu
| Status | Severity | Alarm | Led dioda panelu Ops |
| --- | --- | --- | --- |
| PCM alert – ztráta stejnosměrného proudu z jednoho PCM |Závada – bez ztráty redundance |S1 |Porucha modulu |
| PCM alert – ztráta stejnosměrného proudu z jednoho PCM |Závada – ztráta redundance |S1 |Porucha modulu |
| PCM ventilátor selhání |Závada – ztráta redundance |S1 |Porucha modulu |
| Modul SBB zjistil chybu PCM |Chyba |S1 |Porucha modulu |
| PCM odstraněno |Chyba konfigurace |Žádný |Porucha modulu |
| Chyba konfigurace skříně |Porucha – kritická |S1 |Porucha modulu |
| Upozornění na nízkou výstražnou teplotu |Upozornění |S1 |Porucha modulu |
| Výstraha vysoké výstražné teploty |Upozornění |S1 |Porucha modulu |
| Alarm nad teplotou |Porucha – kritická |S1 |Porucha modulu |
| Selhání sběrnice I2C |Závada – ztráta redundance |S1 |Porucha modulu |
| Chyba komunikace panelu Ops (I2C) |Porucha – kritická |S1 |Porucha modulu |
| Chyba řadiče |Porucha – kritická |S1 |Porucha modulu |
| Porucha modulu rozhraní SBB |Porucha – kritická |S1 |Porucha modulu |
| Chyba modulu rozhraní SBB – nezbývají žádné funkční moduly |Porucha – kritická |S4 |Porucha modulu |
| Modul rozhraní SBB byl odstraněn |Upozornění |Žádný |Porucha modulu |
| Porucha řízení výkonu pohonu |Varování – bez ztráty výkonu pohonu |S1 |Porucha modulu |
| Porucha řízení výkonu pohonu |Porucha – kritická; ztráta výkonu pohonu |S1 |Porucha modulu |
| Jednotka byla odebrána. |Upozornění |Žádný |Porucha modulu |
| Nedostatek energie k dispozici |Upozornění |Žádná |Porucha modulu |

## <a name="next-steps"></a>Další kroky
Další informace o [hardwarových součástech A stavu StorSimple](storsimple-8000-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png


