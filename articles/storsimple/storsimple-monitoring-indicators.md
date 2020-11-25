---
title: Indikátory monitorování StorSimple | Microsoft Docs
description: Popisuje světelné diody (diody) a zvukové poplachy používané ke sledování stavu zařízení StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 59dee7b9-ca6d-4fd9-96e6-a0071e8d248e
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 97209dca7d30de037dbd21f5cc145b2941060e70
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015396"
---
# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>Použití indikátorů monitorování StorSimple ke správě zařízení


## <a name="overview"></a>Přehled
Zařízení StorSimple zahrnuje diody světla (diody LED) a budíky, které můžete použít k monitorování modulů a celkového stavu zařízení StorSimple. Indikátory monitorování najdete na hardwarových součástech primární skříně zařízení a skříni EBOD. Indikátory monitorování můžou být diody LED nebo Akustické alarmy.

Existují tři stavy indikátoru LED, které označují stav modulu: zeleně, blikající zeleně a červenou žlutou nebo červenou žlutou žlutou.  

* Zelené diody LED reprezentují dobrý provozní stav.  
* Blikání zelených a červených diod LED představuje přítomnost nekritických podmínek, které mohou vyžadovat zásah uživatele.  
* Červené žluté indikátory LED označují, že v modulu je nějaká kritická chyba.  

Zbývající část tohoto článku popisuje různé indikátory LED monitorování, jejich umístění na zařízení StorSimple, stav zařízení na základě stavů LED a všechny související zvukové poplachy.

## <a name="front-panel-indicator-leds"></a>Indikátory LED pro přední panel
V předním panelu, který se označuje také jako *panel operací* nebo *panel OPS*, se zobrazuje celkový stav všech modulů v systému. Přední panel je identický na primárním StorSimple a skříni EBOD a je zobrazený níže.  

   ![Přední panel zařízení][1]

Přední panel obsahuje následující indikátory:  

1. Tlačítko pro ztlumení
2. Indikátor indikátoru napájení (zelená/červená – oranžová)
3. Indikátor chyby modulu (na červeně, oranžová/VYPNUTý)
4. Indikátor logické závady indikátoru (u červené – oranžová/VYPNUTé
5. Zobrazení ID jednotky  

Hlavním rozdílem mezi indikátory LED v předním panelu pro zařízení a zařízeními EBOD je **identifikační číslo systémové jednotky** zobrazené na displeji LED. Výchozí ID jednotky zobrazené v zařízení je **00**, zatímco výchozí ID jednotky zobrazené na skříni eBOD je **01**. Díky tomu můžete rychle rozlišovat mezi zařízením a skříňkou EBOD, když je zařízení zapnuté. Pokud je zařízení vypnuté, použijte informace uvedené v [nabídce zapnout nové zařízení](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) a rozlišit zařízení od skříně eBOD.  

## <a name="front-panel-led-status"></a>Stav LED předního panelu
Pomocí následující tabulky Identifikujte stav vyznačený diodami LED na předním panelu pro zařízení nebo skříň EBOD.  

| Výkon systému | Chyba modulu | Logická chyba | Požární | Status |
| --- | --- | --- | --- | --- |
| Červená – oranžová |OFF |OFF |Není k dispozici |Napájení ze sítě se ztratilo, pracuje na napájení ze zálohy nebo napájení a moduly kontroleru se odebraly. |
| Green |ON |ON |Není k dispozici |Stav testu na panelu Ops (5) |
| Green |OFF |OFF |Není k dispozici |Zapnutí, všechny funkce dobré |
| Green |ON |Není k dispozici |Diody chyb PCM, diody chyb ventilátoru |Jakákoli chyba PCM, Chyba ventilátoru, nad nebo pod teplotou |
| Green |ON |Není k dispozici |Vstupně-výstupní diody v modulu |Jakákoli chyba modulu Controller |
| Green |ON |N/A |N/A |Chyba logiky skříně |
| Green |Blikající |Není k dispozici |Indikátor stavu modulu v modulu kontroleru. Diody chyb PCM, diody chyb ventilátoru |Je nainstalovaný neznámý typ modulu kontroleru, chyba sběrnice I2C, chyba konfigurace dat modulu kontroleru |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Indikátory LED pro modul Power chlazení (PCM)
Indikátory LED na zadní straně primární skříně nebo skříně EBOD se v každém modulu PCM dají najít. Toto téma popisuje, jak pomocí následujících diod LED monitorovat stav zařízení StorSimple.  

* Diody PCM pro primární skříň
* Diody PCM pro skříň EBOD

## <a name="pcm-leds-for-the-primary-enclosure"></a>Diody PCM pro primární skříň
Zařízení StorSimple má modul 764W PCM s dodatečnou baterií. Na následujícím obrázku je znázorněn panel INDIKÁTORu pro zařízení.  

   ![Diody PCM v primární skříni][2]

Legenda INDIKÁTORu:

1. Výpadek napájení elektrické sítě
2. Selhání ventilátoru
3. Chyba baterie
4. PCM – OK
5. Selhání řadiče domény
6. Dobrá baterie  

Stav PCM je uveden na panelu indikátoru LED. Panel INDIKÁTORu PCM pro zařízení má šest diod LED. Čtyři z těchto diod LED zobrazují stav zdroje napájení a ventilátoru. Zbývající dva LED diody označují stav modulu záložní baterie v PCM. Pomocí následujících tabulek můžete určit stav PCM.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>Indikátory LED indikátorů PCM pro napájení a ventilátor
| Status | PCM – OK (zelená) | Chyba AC (oranžová) | Selhání ventilátoru (oranžová) | Selhání řadiče domény (oranžová) |
| --- | --- | --- | --- | --- |
| Bez napájení z AC (do skříně) |OFF |OFF |OFF |OFF |
| Žádný výkon střídavého napětí (jenom tento PCM) |OFF |ON |OFF |ON |
| Vypněte modul PCM v systému – OK |ON |OFF |OFF |OFF |
| Chyba PCM (ventilátor selže) |OFF |OFF |ON |Není k dispozici |
| Chyba PCM (přes amp, nad napětím, nad aktuální) |OFF |ON |ON |ON |
| PCM (ventilátor je mimo toleranci) |ON |OFF |OFF |ON |
| Pohotovostní režim |Blikající |OFF |OFF |OFF |
| Stažení firmwaru PCM |OFF |Blikající |Blikající |Blikající |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>Indikátory LED indikátorů PCM pro baterii zálohování
| Status | Dobrá baterie (zelená) | Chyba baterie (oranžová) |
| --- | --- | --- |
| Baterie není k dispozici. |OFF |OFF |
| K dispozici baterie a účtuje se. |ON |OFF |
| Vybíjení baterie nebo vybíjení údržby |Blikající |OFF |
| Chyba "měkkého" baterie (obnovitelné) |OFF |Blikající |
| "Tvrdý" chybový stav baterie (neobnovitelná) |OFF |ON |
| Nepřipravená baterie |Blikající |OFF |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>Diody PCM pro skříň EBOD
EBOD skříň má 580W PCM a žádnou další baterii. Panel PCM pro EBOD skříň má indikátor LED pro napájení a ventilátor. Následující ilustrace znázorňuje tyto indikátory LED.

   ![Diody PCM na skříni EBOD][3] 

Pomocí následující tabulky můžete určit stav PCM.  

| Status | PCM – OK (zelená) | Chyba AC (oranžová) | Selhání ventilátoru (oranžová) | Selhání řadiče domény (oranžová) |
| --- | --- | --- | --- | --- |
| Bez napájení z AC (do skříně) |OFF |OFF |OFF |OFF |
| Žádný výkon střídavého napětí (jenom tento PCM) |OFF |ON |OFF |ON |
| Zapnout modul PCM pro střídavý proud – OK |ON |OFF |OFF |OFF |
| Chyba PCM (ventilátor selže) |OFF |OFF |ON |X |
| Chyba PCM (přes amp, nad napětím, nad aktuálním |OFF |ON |ON |ON |
| PCM (ventilátor je mimo toleranci) |ON |OFF |OFF |ON |
| Pohotovostní model |Blikající |OFF |OFF |OFF |
| Stažení firmwaru PCM |OFF |Blikající |Blikající |Blikající |

## <a name="controller-module-indicator-leds"></a>Indikátory LED modulu řadiče
Zařízení StorSimple obsahuje diody LED pro primární řadič a moduly EBOD Controller.   

### <a name="monitoring-leds-for-the-primary-controller"></a>Monitorování diody LED pro primární kontroler
Následující obrázek vám pomůže identifikovat diody LED na primárním kontroleru. (Všechny součásti jsou uvedeny jako pomůcka při orientaci.)  

   ![Monitorování diod LED – primární kontroler][4]

Pomocí následující tabulky určete, zda modul řadiče pracuje správně.  

### <a name="controller-indicator-leds"></a>Indikátory LED řadiče
| POD | Description |
| --- | --- |
| ID – DIODa (modrá) |Indikuje, že se modul identifikuje. Pokud se indikátor Blue LED na spuštěném kontroleru zabliká, je řadič aktivní a druhý kontroler je pohotovostní. Další informace najdete v tématu [určení aktivního řadiče na zařízení](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| INDIKÁTOR chyby (žlutý) |Označuje chybu v kontroleru. |
| INDIKÁTOR pro OK (zelený) |Ustálená Zelená znamená, že kontroler je OK. Blikající zelená ukazuje chybu konfigurace VPD řadiče. |
| Indikátory LED aktivity SAS (zelená) |Ustálená zelená označuje připojení bez aktuální aktivity. Bliknutí zeleně znamená, že připojení má probíhající aktivitu. |
| Stavová dioda stavu sítě Ethernet |Pravá strana indikuje odkaz nebo síťovou aktivitu: (stálý zelený) odkaz aktivní, (blikající zelená) síťová aktivita. Levá strana indikuje rychlost sítě: (žlutá) 1000 MB/s, (zelená) 100 MB/s a (vypnuto) 10 MB/s. V závislosti na modelu komponenty může být toto světlo zablikat i v případě, že není síťové rozhraní povoleno. |
| POST – diody |Určuje průběh spouštění, když je kontroler zapnutý. Pokud se zařízení StorSimple nepodaří spustit, indikátor LED vám pomůže podpora Microsoftu identifikovat bod v procesu spouštění, ve kterém došlo k chybě. |

> [!IMPORTANT]
> Pokud je indikátor chyby osvětlený, nastaly potíže s modulem kontroleru, který může být vyřešen restartováním kontroleru. Pokud restartování řadiče nevyřešíte, kontaktujte prosím podpora Microsoftu.  
> 
> 

### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>Monitorování diody LED pro EBOD (skříň EBOD)
Každý z řadičů SAS EBOD 6 GB/s má indikátory LED, které označují jeho stav, jak je znázorněno na následujícím obrázku.  

  ![Monitorování diod LED – skříň EBOD][5]

Pomocí následující tabulky zjistíte, zda modul EBOD Controller pracuje normálně.  

### <a name="ebod-controller-module-indicator-leds"></a>Indikátory LED modulu EBOD Controller
| Status | I/O modul je v pořádku (zelený) | Chyba vstupně-výstupního modulu (oranžová) | Aktivita portu hostitele (zelená) |
| --- | --- | --- | --- |
| Modul kontroleru – OK |ON |OFF |- |
| Chyba modulu Controller |OFF |ON |- |
| Žádné připojení k portu externího hostitele |- |- |OFF |
| Připojení k portu externího hostitele – žádná aktivita |- |- |ON |
| Připojení portu externího hostitele – aktivita |- |- |Blikající |
| Chyba metadat modulu kontroleru |Blikající |- |- |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>Indikátory LED na diskové jednotce pro primární skříň a skříň EBOD
Zařízení StorSimple má diskové jednotky umístěné v primární skříni i v skříni EBOD. Každá disková jednotka obsahuje indikátory LED monitorování, jak je popsáno v této části. 

U diskových jednotek je stav jednotky označen zeleným indikátorem LED a červeným indikátorem LED připojeným před každým modulem nosnéch jednotek. Následující ilustrace znázorňuje tyto indikátory LED.

  ![Diody na diskové jednotce][6]

Pomocí následující tabulky můžete určit stav jednotlivých diskových jednotek, které mají vliv na celkový stav LED na předním panelu.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>Indikátory LED na diskové jednotce pro skříň EBOD
| Status | Indikátor aktivity v pořádku (zelený) | INDIKÁTOR chyby (červená – oranžová) | Indikátor LED přidruženého panelu OPS |
| --- | --- | --- | --- |
| Není nainstalovaná žádná jednotka. |OFF |OFF |Žádné |
| Jednotka je nainstalovaná a funkční. |Zapnutí nebo vypnutí blikání aktivity |X |Žádné |
| Sada identit zařízení služby skříně SCSI (SES) |ON |Blikání 1 sekundy na/1 sekundu vypnuto |Žádné |
| Sada bitů SES-bit pro selhání zařízení |ON |ON |Logická chyba (červená) |
| Selhání okruhu řízení spotřeby |OFF |ON |Chyba modulu (červená) |

## <a name="audible-alarms"></a>Akustické poplachy
Zařízení StorSimple obsahuje zvukové poplachy spojené s primární skříňkou a skříní EBOD. Akustický poplach se nachází na předním panelu (označuje se také jako panel OPS) obou skříní. Zvukové znamení signalizuje, že je přítomný chybový stav. Alarm se aktivují v následujících podmínkách:  

* Chyba ventilátoru nebo selhání
* Napětí mimo rozsah
* Nad nebo pod stavem teploty
* Tepelné přetečení
* Systémová chyba
* Logická chyba
* Chyba zdroje napájení
* Odebrání modulu Power chlazení (PCM)  

V následující tabulce jsou popsány různé stavy alarmů.  

### <a name="alarm-states"></a>Stavy alarmu
| Stav alarmu | Akce | Akce s stisknutým tlačítkem ztlumení |
| --- | --- | --- |
| S0 |Normální režim: tiché |ZvukovýSignál dvakrát |
| S1 |Chybový režim: 1 sekunda při/1 sekundách vypnuto |Přechod na S2 nebo S3 (viz poznámky) |
| S2 |Režim připomenutí: přerušované pípnutí |Žádné |
| S3 |Ztlumený režim: tiché |Žádné |
| S4 |Kritický režim selhání: nepřetržitý poplach |Není k dispozici: ztlumení není aktivní. |

> [!NOTE]
> * Pokud v alarmovém stavu S1 nestisknete ztlumení během 2 minut, stav se automaticky převede na S2 nebo S3.  
> * Alarmy stavů S1 až S4 se vrátí do S0 po vymazání stavu chyby.  
> * Kritický stav selhání S4 lze zadat z libovolného jiného stavu.  


Zvukový poplach můžete ztlumit stisknutím tlačítka Ztlumit na panelu OPS. Automatické ztlumení bude provedeno po dvou minutách, pokud není přepínač ztlumení ovládán ručně. Když je alarm ztlumený, bude pokračovat ve zvuku s krátkými přerušovanými signály, aby označoval, že problém stále existuje. Alarm bude při zrušení všech problémů tichý.

V následující tabulce jsou popsány různé podmínky alarmu.

### <a name="alarm-conditions"></a>Podmínky alarmu
| Status | Závažnost | Požární | INDIKÁTOR na panelu OPS |
| --- | --- | --- | --- |
| Výstraha PCM – ztráta napájení řadiče domény z jediného PCM |Chyba – bez ztráty redundance |S1 |Chyba modulu |
| Výstraha PCM – ztráta napájení řadiče domény z jediného PCM |Chyba – ztráta redundance |S1 |Chyba modulu |
| Ventilátor PCM selhal |Chyba – ztráta redundance |S1 |Chyba modulu |
| Modul SBB zjistil chybu PCM. |Indikován |S1 |Chyba modulu |
| PCM byl odebrán |Chyba konfigurace |Žádné |Chyba modulu |
| Chyba konfigurace skříně |Chyba – kritické |S1 |Chyba modulu |
| Výstraha o nízké teplotě upozornění |Upozornění |S1 |Chyba modulu |
| Výstraha vysoké teploty upozornění |Upozornění |S1 |Chyba modulu |
| Alarm přes teplotu |Chyba – kritické |S1 |Chyba modulu |
| Chyba sběrnice I2C |Chyba – ztráta redundance |S1 |Chyba modulu |
| Chyba komunikace na panelu Ops (I2C) |Chyba – kritické |S1 |Chyba modulu |
| Chyba kontroleru |Chyba – kritické |S1 |Chyba modulu |
| Chyba modulu SBB Interface |Chyba – kritické |S1 |Chyba modulu |
| Chyba modulu rozhraní SBB – nezbývá žádné funkční moduly. |Chyba – kritické |S4 |Chyba modulu |
| Modul rozhraní SBB se odebral. |Upozornění |Žádné |Chyba modulu |
| Ovládání chyby řízení spotřeby |Upozornění – žádná ztráta napájení z jednotky |S1 |Chyba modulu |
| Ovládání chyby řízení spotřeby |Chyba – kritické; Ztráta napájení jednotky |S1 |Chyba modulu |
| Jednotka odebrána |Upozornění |Žádné |Chyba modulu |
| Nedostatek dostupného napájení |Upozornění |žádné |Chyba modulu |

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [StorSimple hardwarových komponentách a stavu](storsimple-8000-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png


