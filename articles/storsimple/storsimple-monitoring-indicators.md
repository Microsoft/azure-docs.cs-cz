---
title: StorSimple ukazatele monitorování | Dokumentace Microsoftu
description: Popisuje světla – vysílání diody (LED) a zvukových alarmy používat s monitorováním stavu zařízení StorSimple.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60630560"
---
# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>Pomocí monitorování indikátorů StorSimple ke správě vašeho zařízení


## <a name="overview"></a>Přehled
Zařízení StorSimple zahrnuje světla – vysílání diody (LED) a alarmů, můžete použít k monitorování modulů a celkového stavu zařízení StorSimple. Ukazatele monitorování můžete najít na primární zařízení zařízení a skříně EBOD hardwarové součásti. Monitorování ukazatele může být LED nebo zvukové alarmy.

Existují tři stavy LED slouží k označení stavu modulu: zelená, začne blikat od zelené po červenou žlutou nebo žlutou red.  

* Zelená LED představují dobrý stav.  
* Blikající od zelené po red žlutou LED představují přítomnost z nekritické podmínek, které mohou vyžadovat zásah uživatele.  
* Červená žlutou LED označení, že existuje kritické selhání k dispozici v rámci modulu.  

Zbývající část tohoto článku popisuje různé monitorování indikátorů LED, jejich umístění na zařízení StorSimple, stav zařízení na základě Indikátor stavu a všechny přidružené zvukové alarmy.

## <a name="front-panel-indicator-leds"></a>Přední panel indikátorů LED
Přední panel, označované také jako *operace panel* nebo *ops panel*, zobrazuje agregovaný stav celé všechny moduly v systému. Přední panel je stejný jako primární StorSimple a skříně EBOD a je znázorněno níže.  

   ![Předního panelu zařízení][1]

Přední panel obsahuje následujících ukazatelů:  

1. Ztlumit tlačítko
2. Napájení indikátor LED (zelená/červená žlutá)
3. Indikátor selhání modulu VEDL (červená žlutou/OFF)
4. Indikátor logické chyby VEDL (červená žlutou VYPÍNÁNÍ
5. Zobrazení ID jednotky  

Hlavní rozdíl mezi přední panel LED pro zařízení a pro skříň EBOD je **systémové jednotky identifikační číslo** zobrazený na displeji Indikátor. Výchozí jednotka je na zařízení zobrazí ID **00**, že je výchozí ID jednotka zobrazuje na skříň EBOD **01**. To vám umožní rychle rozlišovat mezi zařízením a skříně EBOD po zapnutí zařízení. Pokud vaše zařízení je vypnuté, použijte informace uvedené v [zapnout nové zařízení](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) odlišili od EBOD skříň zařízení.  

## <a name="front-panel-led-status"></a>Přední panel Indikátor stavu
Následující tabulku použijte k určení stavu indikován LED na přední panel EBOD skříň nebo zařízení.  

| Napájení systému | Chyby modulu | Logické chyby | Alarm | Status |
| --- | --- | --- | --- | --- |
| Červená oranžová |OFF |OFF |neuvedeno |Napájení ze sítě ztrátě provozující na zálohování napájení nebo napájení ze sítě a zařízení, které moduly byly odebrány. |
| Zelená |ON |ON |neuvedeno |Panel OPS pro zapnutí 5 (s) testu stavu |
| Zelená |OFF |OFF |neuvedeno |Výkon na dobré všechny funkce |
| Zelená |ON |neuvedeno |Selhání PCM LED selhání ventilátor indikátorů LED |Žádné chyby PCM ventilátor selhání, nad nebo pod teploty |
| Zelená |ON |neuvedeno |Vstupně-výstupních operací modulu indikátorů LED |Modul závady kontroleru |
| Zelená |ON |neuvedeno |neuvedeno |Chyby logiky skříň |
| Zelená |Animace Flash |neuvedeno |Stav modulu VEDLA na kontroleru modulu. Selhání PCM LED selhání ventilátor indikátorů LED |Typ modulu Neznámý kontroleru nainstalované, I2C sběrnice selhání, Chyba konfigurace řadiče modulu důležité produktu data (VPD) |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Napájení, chlazení modulu (PCM) indikátorů LED
Napájení chladicí modulu (PCM) indikátorů LED můžete najít na zadní primární skříň nebo EBOD skříň na každý modul PCM. Toto téma popisuje, jak používat následující LED s monitorováním stavu zařízení StorSimple.  

* PCM LED pro primární zařízení
* PCM LED pro skříň EBOD

## <a name="pcm-leds-for-the-primary-enclosure"></a>PCM LED pro primární zařízení
Zařízení StorSimple nemá modul 764W PCM s další baterie. Následující obrázek znázorňuje panelu LED pro zařízení.  

   ![PCM LED na primární zařízení][2]

Indikátor legendy:

1. Výpadku napájení AC.
2. Chyba větráku
3. Selhání baterie
4. PCM OK
5. Řadič domény selhání
6. Dobrý stav baterie  

Stav PCM je uveden na panelu LED. Indikátor PCM panelu zařízení má šest LED. Čtyři z těchto LED zobrazí stav napájení a ventilátor. Zbývající dvě LED informací o stavu modulu záložní baterie v PCM. Následující tabulky můžete použít k určení stavu PCM.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>PCM indikátorů LED pro zdroj napájení a ventilátoru
| Status | PCM OK (zelený) | Selhání AC (oranžové) | Ventilátor selhání (oranžové) | Řadič domény selhání (oranžové) |
| --- | --- | --- | --- | --- |
| Žádné napájení ze sítě (do skříně) |OFF |OFF |OFF |OFF |
| Žádné napájení ze sítě (jenom tento PCM) |OFF |ON |OFF |ON |
| AC prezentovat PCM ON - OK |ON |OFF |OFF |OFF |
| Selhání PCM (větráku) |OFF |OFF |ON |neuvedeno |
| Selhání PCM (přes amp přes napětí nad aktuální) |OFF |ON |ON |ON |
| PCM (ventilátor mimo toleranci) |ON |OFF |OFF |ON |
| Pohotovostní režim |Blikání |OFF |OFF |OFF |
| Stahování firmwaru PCM |OFF |Blikání |Blikání |Blikání |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>PCM indikátorů LED pro záložní baterie
| Status | Baterie dobrý (zelený) | Baterie selhání (oranžová) |
| --- | --- | --- |
| Baterie není k dispozici. |OFF |OFF |
| K dispozici a účtované baterie |ON |OFF |
| Uvolnění účtování nebo údržby baterie |Blikání |OFF |
| (Obnovit) "text soft" selhání baterie |OFF |Blikání |
| "Pevné" selhání baterie (neobnovitelná) |OFF |ON |
| S odstraněnými baterie |Blikání |OFF |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>PCM LED pro skříň EBOD
Skříň EBOD má 580W PCM a žádná další baterie. Na panelu PCM skříň EBOD má indikátorů LED pouze pro dodávku napájení a ventilátor. Následující obrázek znázorňuje tyto LED.

   ![PCM LED na skříň EBOD][3] 

V následující tabulce můžete použít k určení stavu PCM.  

| Status | PCM OK (zelený) | Selhání AC (oranžové) | Ventilátor selhání (oranžové) | Řadič domény selhání (oranžové) |
| --- | --- | --- | --- | --- |
| Žádné napájení ze sítě (do skříně) |OFF |OFF |OFF |OFF |
| Žádné napájení ze sítě (jenom tento PCM) |OFF |ON |OFF |ON |
| AC prezentovat PCM ON – OK |ON |OFF |OFF |OFF |
| Selhání PCM (větráku) |OFF |OFF |ON |X |
| Selhání PCM (přes amp přes napětí nad aktuální |OFF |ON |ON |ON |
| PCM (ventilátor mimo toleranci) |ON |OFF |OFF |ON |
| Pohotovostní modelu |Blikání |OFF |OFF |OFF |
| Stahování firmwaru PCM |OFF |Blikání |Blikání |Blikání |

## <a name="controller-module-indicator-leds"></a>Kontroler modulu indikátorů LED
Zařízení StorSimple obsahuje LED pro primární kontroler a moduly řadiče EBOD.   

### <a name="monitoring-leds-for-the-primary-controller"></a>Monitorovacích indikátorů LED pro primární kontroler
Na následujícím obrázku vám pomůže určit LED na primární kontroler. (Všechny komponenty jsou uvedeny na podporu orientovaný).  

   ![Monitorovacích indikátorů LED – primární kontroler][4]

Použijte následující tabulku k určení, zda modul kontroler pracuje správně.  

### <a name="controller-indicator-leds"></a>Kontroler indikátorů LED
| INDIKÁTOR LED | Popis |
| --- | --- |
| Indikátor ID (modrá) |Označuje, že se modul rozpoznán. Pokud modrou Indikátor bliká na spuštěný řadič, kontroleru je aktivní kontroler a druhý je kontroler v pohotovostním režimu. Další informace najdete v tématu [identifikace aktivního kontroleru na vašem zařízení](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| Selhání LED (oranžová) |Označuje chybu v kontroleru. |
| Indikátor LED OK (zelený) |Konstantní zelená značí, že kontroler je v pořádku. Blikající zelená označuje kontroleru VPD Chyba konfigurace. |
| Aktivita SAS LED (zelený) |Konstantní zelená značí připojení se žádná aktuální aktivity. Blikající zelená označuje, že připojení má probíhající činnosti. |
| Stav sítě Ethernet indikátorů LED |Na pravé straně indikuje aktivitu odkaz/network: odkaz (konstantní zelená) aktivní, (začne blikat zelená) síťové aktivity. Na levé straně označuje rychlost sítě: 1000 Mb/s (žlutý), (zelená) 100 Mb/s a (vypnuto) 10 Mb/s. V závislosti na modelu může tento světle blikání i v případě, že síťové rozhraní není povolená. |
| PŘÍSPĚVEK indikátorů LED |Označuje průběh spuštění po zapnutí kontroleru. Pokud zařízení StorSimple se nepodaří spustit, vám pomůže tento Indikátor Microsoft Support identifikovat bod v procesu spouštění, ve kterém došlo k chybě. |

> [!IMPORTANT]
> Pokud selhání Indikátor svítí, dojde k problému s modulem kontroler, který může vyřešit restartováním kontroleru. Pokud restartování kontroleru se tento problém nevyřeší, obraťte se na Microsoft Support.  
> 
> 

### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>Monitorovacích indikátorů LED pro EBOD (EBOD skříně)
Každého 6 řadiče EBOD SAS Gb/s je LED, který indikuje její stav, jak je znázorněno na následujícím obrázku.  

  ![Monitorovacích indikátorů LED – EBOD skříň][5]

V následující tabulce použijte k určení, zda modul řadiče EBOD normálně.  

### <a name="ebod-controller-module-indicator-leds"></a>EBOD řadič modulu indikátorů LED
| Status | Vstupně-výstupních operací modulu OK (zelený) | Chyby modulu vstupně-výstupních operací (oranžová) | Aktivita port hostitele (zelený) |
| --- | --- | --- | --- |
| Modul řadiče OK |ON |OFF |- |
| Chyby modulu kontroleru |OFF |ON |- |
| Port připojení externího hostitele |- |- |OFF |
| Port připojení externího hostitele – žádná aktivita |- |- |ON |
| Připojení k portu externího hostitele - aktivita |- |- |Blikání |
| Chyba modulu metadat řadiče |Blikání |- |- |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>Disková jednotka indikátorů LED pro primární skříň a skříně EBOD
Zařízení StorSimple je umístěný ve primárního skříň a skříně EBOD diskové jednotky. Každé diskové jednotce obsahuje monitorování indikátorů LED, jak je popsáno v této části. 

Pro diskové jednotky, je stav jednotky indikován zelený LED a červená žlutou Indikátor připojené na začátku každého modulu dopravce jednotky. Následující obrázek znázorňuje tyto LED.

  ![Disková jednotka indikátorů LED][6]

Pomocí následující tabulky můžete určit stav každého disku, který zase ovlivňuje celkový předního panelu Indikátor stavu.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>Disková jednotka indikátorů LED pro skříň EBOD
| Status | Indikátor činnosti OK (zelený) | Selhání LED (červená žlutá) | Související ops panel LED |
| --- | --- | --- | --- |
| Žádná jednotka nainstalovány |OFF |OFF |Žádný |
| Jednotka nainstalovaný a funkční |Blikající zapnout nebo vypnout pomocí aktivity |X |Žádný |
| Sada identit zařízení služeb skříně rozhraní SCSI (SES) |ON |Blikající 1 sekunda na/1 sekundu vypnuto |Žádný |
| SES zařízení proti bitová sada |ON |ON |Logické selhání (červená) |
| Ovládací prvek okruh výpadku |OFF |ON |Modul selhání (červená) |

## <a name="audible-alarms"></a>Zvukové upozornění
Zařízení StorSimple obsahuje zvukové alarmy spojené s primární skříň a EBOD skříň. Akustický signál se nachází na přední panel (označované také jako panelu ops) i přílohy. Akustický signál označuje, kdy je přítomna podmínka selhání. Následující podmínky se budou aktivovat alarm:  

* Ventilátor chyb nebo selhání
* Snímač napětí mimo rozsah.
* Nad nebo pod teploty podmínku
* Teplotní přetečení
* Poruchy systému
* Logické chyby
* Dodávku napájení
* Odebrání napájení, chlazení modulu (PCM)  

Následující tabulka popisuje různé stavy alarm.  

### <a name="alarm-states"></a>Stavy upozornění
| Stav varování | Akce | Akce s Ztlumit stisknutí tlačítka |
| --- | --- | --- |
| S0 |Normální režim: tiché |Zvukový signál dvakrát |
| S1 |Režim selhání: 1 sekunda/1 sekundu vypnuté. |Přechod na S2 nebo S3 (viz poznámky) |
| S2 |Připomenout režimu: přerušované zvukový signál |Žádný |
| S3 |Tlumených režimu: tiché |Žádný |
| S4 |Režim kritické selhání: průběžné upozornění |Není k dispozici: ztlumení není aktivní |

> [!NOTE]
> * Ve stavu varování S1 Pokud jste nemačkejte klávesu Ztlumit během 2 minut, stav automaticky přejde do S2 nebo S3.  
> * Stavy alarmů S1 na S4 vrátit S0 po vymazání stavu selhání.  
> * Kritické chyby stavu S4 lze zadat z jakéhokoli jiného stavu.  


Akustický signál můžete Ztlumit stisknutím klávesy Ztlumit tlačítko na panelu ops. Automatické ztlumení se vrátí po dvou minutách Ztlumit přepínač není provozované ručně. Když je ztlumen alarm, bude nadále zvuk s krátkou přerušované signálů k označení, že problém přetrvává. Zrušte zaškrtnutí všech problémů budou alarm silent.

Následující tabulka popisuje různé podmínky upozornění.

### <a name="alarm-conditions"></a>Podmínky upozornění
| Status | Severity | Alarm | OPS panelu LED |
| --- | --- | --- | --- |
| Upozornění PCM – výpadku napájení řadiče domény z jedné PCM |Selhání – bez ztráty redundance |S1 |Chyby modulu |
| Upozornění PCM – výpadku napájení řadiče domény z jedné PCM |Selhání – ztrátu redundance |S1 |Chyby modulu |
| Větráku PCM |Selhání – ztrátu redundance |S1 |Chyby modulu |
| Propojení sběrnice SBB modulu bylo zjištěno selhání PCM |Selhání |S1 |Chyby modulu |
| Odebrat PCM |Chyba konfigurace |Žádný |Chyby modulu |
| Chyba konfigurace skříň |Selhání – kritické |S1 |Chyby modulu |
| Nízká výstraha teploty upozornění |Upozornění |S1 |Chyby modulu |
| Vysoká upozornění teploty |Upozornění |S1 |Chyby modulu |
| Přes teplotní alarm |Selhání – kritické |S1 |Chyby modulu |
| Selhání I2C Service bus |Selhání – ztrátu redundance |S1 |Chyby modulu |
| OPS panelu došlo k chybě komunikace (I2C) |Selhání – kritické |S1 |Chyby modulu |
| Chyba řadiče |Selhání – kritické |S1 |Chyby modulu |
| Propojení sběrnice SBB rozhraní modulu selhání |Selhání – kritické |S1 |Chyby modulu |
| Propojení sběrnice SBB rozhraní modulu selhání – žádný funkční moduly zbývající |Selhání – kritické |S4 |Chyby modulu |
| Propojení sběrnice SBB rozhraní modulu odebrat |Upozornění |Žádný |Chyby modulu |
| Jednotka řízení napájení |Upozornění – bez výpadku napájení jednotky |S1 |Chyby modulu |
| Jednotka řízení napájení |Selhání – kritická; výpadku napájení jednotky |S1 |Chyby modulu |
| Jednotka byla odebrána. |Upozornění |Žádný |Chyby modulu |
| Nedostatek energie k dispozici |Upozornění |None |Chyby modulu |

## <a name="next-steps"></a>Další postup
Další informace o [StorSimple hardwarové součásti a stav](storsimple-8000-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png


