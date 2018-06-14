---
title: Monitorování indikátory StorSimple | Microsoft Docs
description: Popisuje svítivé diody (LED) a zvukových výstrahy, které se používá k monitorování stavu zařízení StorSimple.
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
ms.openlocfilehash: 9ae0caec211dc1199f0abd2ce9bc0c7ad11c02ec
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2017
ms.locfileid: "24030353"
---
# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>Používat ke správě zařízení StorSimple monitorování ukazatele


## <a name="overview"></a>Přehled
Zařízení StorSimple zahrnuje svítivé diody (LED) a poplašná, můžete použít ke sledování celkového stavu zařízení StorSimple a modulů. Monitorování indikátory naleznete na hardwarové součásti skříň primární zařízení a EBOD skříň. Ukazatele monitorování může být LED nebo zvukové výstrahy.

Existují tři stavy DIODU slouží k určení stavu modulu: zelená, blikající zelená red žlutou nebo žlutou red.  

* Zelená LED představují provozní stav v pořádku.  
* Blikající zelená k red žlutou LED představují přítomnost nekritické podmínky, které mohou vyžadovat zásah uživatele.  
* Red žlutou LED znamenat, že existuje v rámci modulu kritickou chybu.  

Zbývající část tohoto článku popisuje různé monitorování indikátor LED, jejich umístění v zařízení StorSimple, stav zařízení na základě DIODU stavy a všechny přidružené zvukové výstrahy.

## <a name="front-panel-indicator-leds"></a>Přední panel indikátor LED
Před panelu, také známé jako *operations panely* nebo *ops panely*, zobrazuje agregovaný stav všech modulů v systému. Přední panel je stejná na primární zařízení StorSimple a skříň EBOD a je zobrazený dole.  

   ![Zařízení předního panelu][1]

Přední panel obsahuje následující ukazatele:  

1. Tlačítko pro ztlumení
2. Indikátor Power DIODU (zelený/red žlutou)
3. Indikátor selhání modulu VEDLA (na red oranžová/OFF)
4. Logické chyby indikátor VEDLA (na red oranžová/OFF
5. Zobrazení ID jednotky  

Hlavní rozdíl mezi přední panel LED pro zařízení a pro skříni EBOD je **systému jednotky identifikační číslo** zobrazený na displeji Indikátor. Výchozí jednotku ID zobrazí na zařízení je **00**, kdežto ID jednotky výchozí zobrazené na skříni EBOD **01**. To umožňuje rychle rozlišit mezi zařízením a skříň EBOD po zapnutí zařízení. Pokud vaše zařízení je vypnutý, použijte informace uvedené v [zapnout nové zařízení](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) od skříně EBOD odlišit od zařízení.  

## <a name="front-panel-led-status"></a>Přední panel Indikátor stavu
Následující tabulku použijte k určení stavu indikován LED na přední panel pro zařízení nebo EBOD skříň.  

| Napájení systému | Selhání modulu | Logické chyby | Výstrahy | Status |
| --- | --- | --- | --- | --- |
| Red oranžová |OFF |OFF |Není k dispozici |Napájení ze sítě ztratili, pracující na zálohování napájení nebo napájení ze sítě na a na zařízení, které byly odebrány moduly. |
| Zelená |ON |ON |Není k dispozici |Test stavu OPS panely zapnutí (5s) |
| Zelená |OFF |OFF |Není k dispozici |Zapnutí všechny funkce funkční |
| Zelená |ON |Není k dispozici |Selhání PCM LED selhání ventilátor LED |Všechny chyby PCM ventilátor odolnost, nad nebo pod teploty |
| Zelená |ON |Není k dispozici |Vstupně-výstupních operací modulu LED |Všechny řadiče selhání modulu |
| Zelená |ON |Není k dispozici |Není k dispozici |Skříň logiku selhání |
| Zelená |Flash |Není k dispozici |V modulu řadiče VEDLA stav modulu. Selhání PCM LED selhání ventilátor LED |Typ modulu neznámé řadiče nainstalovaná, I2C sběrnice selhání, Chyba konfigurace řadiče modulu produktu důležitých dat (VPD) |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Napájení chladicí modulu (PCM) indikátor LED
Power chladicí modulu (PCM) indikátor LED naleznete na zadní straně primární skříň nebo EBOD skříň v každé PCM modulu. Toto téma popisuje, jak používat následující LED monitorovat stav zařízení StorSimple.  

* PCM LED pro primární skříň
* PCM LED pro EBOD skříň

## <a name="pcm-leds-for-the-primary-enclosure"></a>PCM LED pro primární skříň
Zařízení StorSimple má modul 764W PCM s další stav baterie. Následující obrázek znázorňuje DIODU panel pro zařízení.  

   ![PCM LED na primární skříň][2]

Indikátor legendy:

1. Výpadku napájení ze sítě
2. Ventilátor selhání
3. Selhání stav baterie.
4. PCM OK
5. Řadič domény selhání
6. Dobrý stav baterie  

Stav PCM uvedené na panelu Indikátor. Panel PCM DIODU zařízení má šest LED. Čtyři tyto LED zobrazit stav napájení a ventilátoru. Zbývající dvě LED označují stav modulu zálohování baterie ve PCM. Následující tabulky můžete použít k určení stavu PCM.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>Indikátor PCM LED pro napájení a ventilátor
| Status | PCM OK (zelený) | Selhání AC (oranžové) | Ventilátor služeb při selhání (oranžové) | Řadič domény selhání (oranžové) |
| --- | --- | --- | --- | --- |
| Žádné AC napájení (do skříně) |OFF |OFF |OFF |OFF |
| Napájení (pouze tento PCM) žádné ze sítě |OFF |ON |OFF |ON |
| AC prezentovat PCM ON - OK |ON |OFF |OFF |OFF |
| Selhání PCM (ventilátor selhání) |OFF |OFF |ON |Není k dispozici |
| Selhání PCM (přes amp přes napětí přes aktuální) |OFF |ON |ON |ON |
| PCM (ventilátor mimo toleranci) |ON |OFF |OFF |ON |
| Pohotovostní režim |Blikání |OFF |OFF |OFF |
| Stažení firmwaru PCM |OFF |Blikání |Blikání |Blikání |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>Indikátor PCM LED pro zálohování baterie
| Status | Baterie dobrý (zelený) | Stav baterie. chyby (oranžová) |
| --- | --- | --- |
| Nejsou k dispozici baterie |OFF |OFF |
| Baterie přítomen a účtovat |ON |OFF |
| Baterie poplatků nebo Údržba na vyřízení |Blikání |OFF |
| Baterie "soft" selhání (obnovitelné) |OFF |Blikání |
| "Pevné" selhání baterie (neobnovitelná) |OFF |ON |
| S odstraněnými stav baterie. |Blikání |OFF |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>PCM LED pro EBOD skříň
Skříň EBOD má 580W PCM a žádná další baterie. Panel PCM pro skříni EBOD má kláves pouze pro zdroje napájení a ventilátoru. Následující obrázek znázorňuje tyto LED.

   ![PCM LED na skříni EBOD][3] 

V následující tabulce slouží k určení stavu PCM.  

| Status | PCM OK (zelený) | Selhání AC (oranžové) | Ventilátor služeb při selhání (oranžové) | Řadič domény selhání (oranžové) |
| --- | --- | --- | --- | --- |
| Žádné AC napájení (do skříně) |OFF |OFF |OFF |OFF |
| Napájení (pouze tento PCM) žádné ze sítě |OFF |ON |OFF |ON |
| AC prezentovat PCM ON – OK |ON |OFF |OFF |OFF |
| Selhání PCM (ventilátor selhání) |OFF |OFF |ON |X |
| Selhání PCM (přes amp přes napětí přes aktuální |OFF |ON |ON |ON |
| PCM (ventilátor mimo toleranci) |ON |OFF |OFF |ON |
| Pohotovostní modelu |Blikání |OFF |OFF |OFF |
| Stažení firmwaru PCM |OFF |Blikání |Blikání |Blikání |

## <a name="controller-module-indicator-leds"></a>Řadič modulu indikátor LED
Zařízení StorSimple obsahuje LED pro primární řadič a moduly EBOD řadiče.   

### <a name="monitoring-leds-for-the-primary-controller"></a>Monitorování LED pro primární řadič
Na následujícím obrázku pomáhá identifikovat LED na primárním řadiči. (Všechny součásti jsou uvedeny na podporu v orientaci).  

   ![Monitorování LED - primární řadič][4]

Následující tabulku použijte k určení, zda modul řadiče pracuje správně.  

### <a name="controller-indicator-leds"></a>Řadič indikátor LED
| INDIKÁTOR | Popis |
| --- | --- |
| ID DIODU (modrá) |Označuje, že modul je jsou označené. Pokud blue DIODU bliká na běžícího řadiče, řadič je aktivní a druhý je pohotovostní řadiče. Další informace najdete v tématu [identifikovat řadič active na vašem zařízení](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| Selhání DIODU (oranžová) |Označuje chybu v kontroleru. |
| Indikátor LED OK (zelený) |Konstantní zelená značí, zda je řadič OK. Blikající zelená značí řadič VPD Chyba konfigurace. |
| Aktivita SAS LED (zelený) |Konstantní zelená značí, připojení se žádné aktuální aktivity. Blikající zelená značí, že připojení má probíhající aktivity. |
| Stav Ethernet LED |Pravé straně označuje odkaz nebo síťové aktivity: active (blikající zelená) (konstantní zelený) odkaz síťové aktivity. Levé straně určuje rychlost sítě: (žlutý) 1000 Mb/s (zelený) 100 Mb/s a (OFF) 10 Mb/s. V závislosti na modelu součásti může tento lehký blink i v případě, že síťové rozhraní není povoleno. |
| LED POST |Určuje spouštěcí průběh po zapnutí kontroleru. Pokud zařízení StorSimple se nepodaří spustit, vám pomůže tento DIODU Microsoft Support identifikovat bod v procesu spouštění, kdy došlo k selhání. |

> [!IMPORTANT]
> Pokud je lit LED selhání, došlo k potížím s modulem řadiče, která může vyřešit restartováním kontroleru. Pokud restartování řadičem tento problém nevyřeší, obraťte se na Microsoft Support.  
> 
> 

### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>Monitorování LED EBOD (EBOD skříň)
Každý 6 Gb/s SAS EBOD řadičů má LED, který indikuje její stav, jak je znázorněno na následujícím obrázku.  

  ![Monitorování LED - EBOD skříň][5]

Následující tabulku použijte k určení, zda modul řadiče EBOD pracuje normálně.  

### <a name="ebod-controller-module-indicator-leds"></a>EBOD řadiče modulu indikátor LED
| Status | Vstupně-výstupních operací modulu OK (zelený) | Selhání modulu vstupně-výstupní operace (oranžová) | Aktivitu port hostitele (zelený) |
| --- | --- | --- | --- |
| Modul řadiče OK |ON |OFF |- |
| Selhání modulu řadiče |OFF |ON |- |
| Port připojení externího hostitele |- |- |OFF |
| Port připojení externího hostitele – žádná aktivita |- |- |ON |
| Připojení k externím hostiteli port - aktivity |- |- |Blikání |
| Chyba modulu metadat řadiče |Blikání |- |- |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>Disková jednotka indikátor LED pro primární skříně a EBOD skříň
Zařízení StorSimple je umístěn ve skříni primární a skříň EBOD diskové jednotky. Každé diskové jednotce obsahuje monitorování kláves, jak je popsáno v této části. 

Pro diskové jednotky, disku stav je indikován zelená DIODU a červená oranžová DIODU připojena před každou jednotku poskytovatel modulu. Následující obrázek znázorňuje tyto LED.

  ![LED diskovou jednotku][6]

Pomocí následující tabulky můžete určit stav každého disku, který naopak ovlivňuje celkový předního panelu Indikátor stavu.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>Disková jednotka indikátor LED pro EBOD skříň
| Status | Aktivita DIODU OK (zelený) | Selhání DIODU (červená oranžová) | Související ops panely DIODU |
| --- | --- | --- | --- |
| Žádné jednotky nainstalována |OFF |OFF |Žádný |
| Jednotka nainstalovaný a funkční |Blikající zapnout nebo vypnout s aktivitou |X |Žádný |
| Sada identity zařízení SCSI skříň služby (SES) |ON |Blikající 1 sekunda na/1 sekundu vypnuto |Žádný |
| SES zařízení selhání bitová sada |ON |ON |Logické chyby (červený) |
| Selhání okruh řízení spotřeby |OFF |ON |Selhání modulu (červený) |

## <a name="audible-alarms"></a>Zvukové výstrahy
Zařízení StorSimple obsahuje zvukové výstrahy související s skříni primární a EBOD skříň. Zvukové poplašné se nachází na přední panel (také označované jako panely ops) i skříně. Zvukové poplašné označuje, pokud je přítomen podmínku chyby. Tyto podmínky se aktivují upozornění:  

* Ventilátor chyb nebo selhání
* Napětí mimo rozsah.
* Nad nebo pod teploty podmínky
* Teplotní přetečení
* Selhání systému
* Logické chyby
* Selhání napájení napájení
* Odebrání napájení chlazení modulu (PCM)  

Následující tabulka popisuje různé stavy výstrahy.  

### <a name="alarm-states"></a>Stavy výstrahy
| Stav výstrahy | Akce | Akce s ztlumení stisknutí tlačítka |
| --- | --- | --- |
| S0 |Normálním režimu: tichou |Zvukový signál dvakrát |
| S1 |Režim selhání: 1 sekunda na/1 sekundu vypnuto |Přechod do S2 nebo S3 (viz poznámky) |
| S2 |Připomenout režim: přerušované zvukový signál |Žádný |
| S3 |Muted režim: tichou |Žádný |
| S4 |Kritické chyby režimu: průběžné výstrahy |Není k dispozici: ztlumení není aktivní |

> [!NOTE]
> * Ve stavu výstrahy S1 Pokud není stisknete ztlumení během 2 minut, stav automaticky přejde na S2 nebo S3.  
> * Po je zrušen stav chyby, vraťte alarmů stavy S1 k S4 k S0.  
> * Kritické chyby stavu S4 lze zadat z jiných stavu.  


Zvukové poplašné můžete ztlumení stisknutím ztlumení tlačítka v panelech ops. Automatické ztlumení bude po dvou minutách dojít, pokud není vypínač zvuku provozovat ručně. Pokud je ztlumen na upozornění, bude pokračovat zvuk s krátkou přerušované signálů k označení, že problém stále existuje. Upozornění bude tichou, když všechny problémy jsou odstraněny.

Následující tabulka popisuje různé podmínky výstrahy.

### <a name="alarm-conditions"></a>Podmínky výstrahy
| Status | Závažnost | Výstrahy | OPS panelu DIODU |
| --- | --- | --- | --- |
| Výstraha PCM – výpadku napájení řadiče domény z jedné PCM |Selhání – bez ztráty redundance |S1 |Selhání modulu |
| Výstraha PCM – výpadku napájení řadiče domény z jedné PCM |Selhání – ztráta redundance |S1 |Selhání modulu |
| Ventilátor selhání PCM |Selhání – ztráta redundance |S1 |Selhání modulu |
| SBB modulu bylo zjištěno selhání PCM |Selhání |S1 |Selhání modulu |
| Odebrat PCM |Chyba konfigurace. |Žádný |Selhání modulu |
| Chyba konfigurace skříň |Selhání – kritická |S1 |Selhání modulu |
| Teplotní nízkou upozornění |Upozornění |S1 |Selhání modulu |
| Vysoké teploty upozornění |Upozornění |S1 |Selhání modulu |
| Přes teplotní alarm |Selhání – kritická |S1 |Selhání modulu |
| Selhání sběrnice I2C |Selhání – ztráta redundance |S1 |Selhání modulu |
| OPS panelu došlo k chybě komunikace (I2C) |Selhání – kritická |S1 |Selhání modulu |
| Chyba řadiče |Selhání – kritická |S1 |Selhání modulu |
| Selhání modulu SBB rozhraní |Selhání – kritická |S1 |Selhání modulu |
| Selhání modulu SBB rozhraní – bez funkční moduly zbývající |Selhání – kritická |S4 |Selhání modulu |
| Odebere modul SBB rozhraní |Upozornění |Žádný |Selhání modulu |
| Jednotka selhání řízení spotřeby |Upozornění – bez výpadku napájení jednotky |S1 |Selhání modulu |
| Jednotka selhání řízení spotřeby |Selhání – kritická; výpadku napájení jednotky |S1 |Selhání modulu |
| Jednotka byla odebrána. |Upozornění |Žádný |Selhání modulu |
| Nedostatečné napájení, které jsou k dispozici |Upozornění |None |Selhání modulu |

## <a name="next-steps"></a>Další kroky
Další informace o [StorSimple hardwarové součásti a stav](storsimple-8000-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png


