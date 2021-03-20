---
title: Výměna řadiče zařízení řady StorSimple 8000 | Microsoft Docs
description: Vysvětluje, jak odebrat a nahradit jeden nebo oba moduly řadiče na zařízení řady StorSimple 8000.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 9d8b75c48da2bb13d843258ead378d3e849da951
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85514082"
---
# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Výměna modulu Controller na zařízení StorSimple
## <a name="overview"></a>Přehled
V tomto kurzu se dozvíte, jak odebrat a nahradit jeden nebo oba moduly řadiče na zařízení StorSimple. Popisuje také základní logiku pro scénáře nahrazení jednoho a dvou řadičů.

> [!NOTE]
> Před nahrazením kontroléru doporučujeme, abyste firmware řadiče vždy aktualizovali na nejnovější verzi.
> 
> Aby nedošlo k poškození zařízení StorSimple, vysuňte kontroler, dokud se indikátory LED nezobrazuje jako jedna z následujících možností:
> 
> * Všechna světla jsou VYPNUTa.
> * Dioda LED 3, ![ zelená ikona kontroly ](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png) a ![ červená křížová ikona ](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) jsou blikající a indikátor LED 0 a dioda 7 je **zapnutá**.


V následující tabulce jsou uvedeny podporované scénáře nahrazení řadiče.

| Obchodní případ | Scénář nahrazení | Příslušný postup |
|:--- |:--- |:--- |
| 1 |Jeden kontroler je ve stavu selhání, druhý kontroler je v pořádku a aktivní. |[Nahrazení jedním řadičem](#replace-a-single-controller), které popisuje [logiku za jedním nahrazením řadičem](#single-controller-replacement-logic), stejně jako [náhradní kroky](#single-controller-replacement-steps). |
| 2 |Oba řadiče selhaly a vyžadovaly nahrazení. Skříň, disky a skříň disku jsou v pořádku. |[Nahrazení dvojím řadičem](#replace-both-controllers), které popisuje [logiku za nahrazením pomocí dvojího kontroleru](#dual-controller-replacement-logic), jakož i [postup nahrazení](#dual-controller-replacement-steps). |
| 3 |Řadiče ze stejného zařízení nebo z různých zařízení se přemění. Skříň, disky a skříně disku jsou v pořádku. |Zobrazí se zpráva s upozorněním o neshodu slotu. |
| 4 |Jeden kontroler chybí a druhý kontroler se nezdařil. |[Nahrazení dvojím řadičem](#replace-both-controllers), které popisuje [logiku za nahrazením pomocí dvojího kontroleru](#dual-controller-replacement-logic), jakož i [postup nahrazení](#dual-controller-replacement-steps). |
| 5 |Jeden nebo oba řadiče selhaly. Nemůžete získat přístup k zařízení prostřednictvím konzoly sériového prostředí nebo vzdálené komunikace Windows PowerShellu. |[Kontaktujte podpora Microsoftu](storsimple-8000-contact-microsoft-support.md) pro ruční postup nahrazení kontroleru. |
| 6 |Řadiče mají jinou verzi buildu, která může být způsobena:<ul><li>Řadiče mají jinou verzi softwaru.</li><li>Řadiče mají jinou verzi firmwaru.</li></ul> |Pokud se liší verze softwaru kontroléru, logika nahrazení ji detekuje a aktualizuje verzi softwaru na řadiči pro nahrazení.<br><br>Pokud se liší verze firmwaru řadiče a stará verze firmwaru není **automaticky** aktualizovatelná, zobrazí se ve Azure Portal zpráva s upozorněním. Měli byste vyhledat aktualizace a nainstalovat aktualizace firmwaru.</br></br>Pokud se liší verze firmwaru kontroléru a stará verze firmwaru je automaticky aktualizovatelná, logika nahrazení řadičem ji detekuje a po spuštění kontroleru se firmware automaticky aktualizuje. |

Pokud se nezdařila, je nutné odebrat modul kontroleru. Jeden nebo oba moduly řadiče můžou selhat, což může vést k nahrazení jednoho řadiče nebo nahrazení dvou řadičů. Postup nahrazení a logika za nimi najdete v následujících tématech:

* [Výměna jednoho kontroleru](#replace-a-single-controller)
* [Výměna obou řadičů](#replace-both-controllers)
* [Odebrání kontroleru](#remove-a-controller)
* [Vložit kontroler](#insert-a-controller)
* [Identifikujte aktivní kontroler v zařízení](#identify-the-active-controller-on-your-device)

> [!IMPORTANT]
> Než odeberete a nahradíte kontroler, Projděte si bezpečnostní informace v části [StorSimple hardwarové součásti výměna](storsimple-8000-hardware-component-replacement.md).
> 
> 

## <a name="replace-a-single-controller"></a>Výměna jednoho kontroleru
Pokud se jeden ze dvou řadičů v zařízení Microsoft Azure StorSimple nezdařil, nepracuje správně nebo chybí, je nutné nahradit jeden kontroler.

### <a name="single-controller-replacement-logic"></a>Logika nahrazení jednoho řadiče
V případě nahrazení jedním řadičem byste měli nejdřív odebrat kontroler, který selhal. (Zbývající kontroler v zařízení je aktivním řadičem.) Při vložení kontroleru pro nahrazení dojde k následujícím akcím:

1. Náhradní kontroler hned spustí komunikaci se zařízením StorSimple.
2. Snímek virtuálního pevného disku (VHD) pro aktivní kontroler se zkopíruje na řadiči pro nahrazení.
3. Snímek se upraví tak, aby při spuštění řadiče pro nahrazení z tohoto virtuálního pevného disku byl rozpoznán jako pohotovostní kontroler.
4. Po dokončení úprav se řadič pro nahrazení spustí jako pohotovostní kontroler.
5. V případě, že oba řadiče jsou spuštěné, cluster se dostane do online režimu.

### <a name="single-controller-replacement-steps"></a>Postup nahrazení jednoho řadiče
Pokud jeden z řadičů v zařízení Microsoft Azure StorSimple neprojde, proveďte následující kroky. (Druhý kontroler musí být aktivní a spuštěný. Pokud selže nebo dojde k selhání obou řadičů, přečtěte si [postup nahrazení dvou](#dual-controller-replacement-steps)řadičů.)

> [!NOTE]
> Může trvat 30 až 45 minut, než se kontroler restartuje a úplně se obnoví z jediného postupu výměny řadiče. Celková doba pro celý postup, včetně připojení kabelů, je přibližně 2 hodiny.


#### <a name="to-remove-a-single-failed-controller-module"></a>Odebrání jednoho modulu neúspěšného kontroleru
1. V Azure Portal přejděte na službu StorSimple Správce zařízení, klikněte na **zařízení** a potom klikněte na název zařízení, které chcete monitorovat.
2. Přejít na **Monitor > stav hardwaru**. Stav obou řadičů Controller 0 nebo Controller 1 by měl být červený, což znamená selhání.
   
   > [!NOTE]
   > Neúspěšný kontroler v nahrazení jednoho řadiče je vždy pohotovostním řadičem.
   
3. Pomocí obrázku 1 a následující tabulky Najděte modul neúspěšného kontroleru.
   
    ![Naplánování modulů primární skříně zařízení](./media/storsimple-controller-replacement/IC740994.png)
   
    **Obrázek 1** Back-StorSimple zařízení
   
   | Popisek | Description |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Kontroler 0 |
   | 4 |Kontroler 1 |
4. Na řadiči, který selhal, odeberte všechny připojené síťové kabely z datových portů. Pokud používáte model 8600, odeberte také kabely SAS, které připojují kontroler k řadiči EBOD.
5. Postupujte podle kroků v části [Odebrání řadiče](#remove-a-controller) pro odebrání neúspěšného kontroleru.
6. Nainstalujte nahrazení továrny do stejného slotu, ze kterého se odstranil neúspěšný kontroler. Tím se aktivuje logika nahrazení jedním řadičem. Další informace najdete v tématu [logika nahrazení jednoho řadiče](#single-controller-replacement-logic).
7. I když jeden řadič nahradil logiku na pozadí, znovu připojí kabely. Připojte všechny kabely přesně stejným způsobem, jako by byly připojeny před nahrazením.
8. Po restartování kontroleru zkontrolujte **stav kontroléru** a **stav clusteru** v Azure Portal, abyste ověřili, že se kontroler vrátí do stavu v pořádku a je v pohotovostním režimu.

> [!NOTE]
> Pokud zařízení sledujete prostřednictvím konzoly sériového portu, může se při obnovování z náhradní procedury Zobrazit více restartování. Po zadání nabídky konzoly sériového portu se zobrazí informace o tom, že byla náhrada dokončena. Pokud se nabídka neobjeví během dvou hodin od spuštění nahrazení řadičem, kontaktujte prosím [Podpora Microsoftu](storsimple-8000-contact-microsoft-support.md).
>
> Od aktualizace Update 4 můžete `Get-HCSControllerReplacementStatus` k monitorování stavu procesu nahrazení řadiče použít také rutinu v rozhraní Windows PowerShell zařízení.
> 

## <a name="replace-both-controllers"></a>Výměna obou řadičů
Pokud se oba řadiče v zařízení Microsoft Azure StorSimple nezdařily, nefungují správně nebo chybí, je nutné nahradit oba řadiče. 

### <a name="dual-controller-replacement-logic"></a>Logika nahrazení dvou řadičů
V případě nahrazení dvojím řadičem nejprve odeberte oba neúspěšné řadiče a pak vložte náhrady. Při vložení dvou náhradních řadičů dojde k následujícím akcím:

1. Řadič pro nahrazení ve slotu 0 zkontroluje následující:
   
   1. Používá aktuální verze firmwaru a softwaru?
   2. Je součástí clusteru?
   3. Je partnerský řadič spuštěný a je clusterovaný?
      
      Pokud žádná z těchto podmínek není pravdivá, kontroler vyhledá poslední denní zálohu (umístěnou v **nonDOMstorage** na jednotce S). Kontroler kopíruje poslední snímek VHD ze zálohy.
2. Kontroler ve slotu 0 používá snímek k samotné imagi.
3. Mezitím kontroler ve slotu 1 čeká na kontrolér 0, aby dokončil vytváření a spouštění.
4. Po spuštění kontroléru 0 kontroler 1 detekuje cluster vytvořený řadičem 0, který aktivuje logiku nahrazení jedním řadičem. Další informace najdete v tématu [logika nahrazení jednoho řadiče](#single-controller-replacement-logic).
5. Následně budou oba řadiče spuštěny a cluster přejde do režimu online.

> [!IMPORTANT]
> Po nakonfigurovaném nastavení s dvojím řadičem je po konfiguraci zařízení StorSimple nutné provést ruční zálohování zařízení. Zálohování na denní konfiguraci zařízení se neaktivuje, dokud neuplyne 24 hodin. Pokud chcete provést ruční zálohování zařízení, pracujte s [Podpora Microsoftu](storsimple-8000-contact-microsoft-support.md) .


### <a name="dual-controller-replacement-steps"></a>Postup nahrazení dvou řadičů
Tento pracovní postup se vyžaduje v případě, že se oba řadiče v zařízení Microsoft Azure StorSimple nezdařily. K tomu může dojít v datacentru, ve kterém systém přestane reagovat, a v důsledku toho dojde v krátké době k selhání obou řadičů. V závislosti na tom, jestli je zařízení StorSimple vypnuté nebo zapnuté a jestli používáte model 8600 nebo 8100, je potřeba použít jinou sadu kroků.

> [!IMPORTANT]
> Může trvat 45 minut až 1 hodinu, než se kontroler restartuje a zcela obnoví z postupu pro nahrazení dvou řadičů. Celková doba pro celý postup, včetně připojení kabelů, je přibližně 2,5 hodin.

#### <a name="to-replace-both-controller-modules"></a>Postup nahrazení obou modulů Controller
1. Pokud je zařízení vypnuté, přeskočte tento krok a přejděte k dalšímu kroku. Pokud je zařízení zapnuté, vypněte zařízení.
   
   1. Pokud používáte model 8600, vypněte nejdřív primární skříň a pak vypněte EBOD skříň.
   2. Počkejte, než se zařízení úplně vypne. Všechny diody LED na zadní straně zařízení budou vypnuté.
2. Odeberte všechny síťové kabely, které jsou připojené k datovým portům. Pokud používáte model 8600, odeberte taky kabely SAS, které spojují primární skříň k EBOD skříni.
3. Odeberte oba řadiče ze zařízení StorSimple. Další informace najdete v tématu [Odebrání kontroleru](#remove-a-controller).
4. Nejprve vložte náhradní továrnu pro Controller 0 a pak vložte řadič 1. Další informace najdete v tématu [vložení kontroleru](#insert-a-controller). Tím se aktivuje logika nahrazení dvojím řadičem. Další informace najdete v tématu [logika nahrazení dvou řadičů](#dual-controller-replacement-logic).
5. I když se logika nahrazení řadičem na pozadí pokračuje, znovu připojte kabely. Připojte všechny kabely přesně stejným způsobem, jako by byly připojeny před nahrazením. Projděte si podrobné pokyny k modelu v části zařízení [instalace zařízení StorSimple 8100](storsimple-8100-hardware-installation.md) nebo [instalaci zařízení StorSimple 8600](storsimple-8600-hardware-installation.md).
6. Zapněte zařízení StorSimple. Pokud používáte model 8600:
   
   1. Ujistěte se, že je EBOD skříň na začátku zapnutá.
   2. Počkejte, dokud nebude spuštěná skříň EBOD.
   3. Zapněte primární skříň.
   4. Po restartování prvního kontroleru a jeho v dobrém stavu bude systém běžet.
      
      > [!NOTE]
      > Pokud zařízení sledujete prostřednictvím konzoly sériového portu, může se při obnovování z náhradní procedury Zobrazit více restartování. Po zobrazení nabídky konzoly sériového portu se dozvíte, že byla náhrada dokončena. Pokud se nabídka neobjeví během 2,5 hodin od nahrazení řadiče, [kontaktujte prosím podpora Microsoftu](storsimple-8000-contact-microsoft-support.md).
     
## <a name="remove-a-controller"></a>Odebrání kontroleru
Pomocí následujícího postupu odeberte chybný modul kontroleru ze zařízení StorSimple.

> [!NOTE]
> Následující obrázky jsou pro Controller 0. U kontroléru 1 by se vrátila zpět.


#### <a name="to-remove-a-controller-module"></a>Odebrání modulu kontroleru
1. Pouchopit západku modulu mezi jezdcem a forefinger.
2. Pozvolna a forefinger se jemně rozpustíte, abyste uvolnili západku řadiče.
   
    ![Uvolňování zámků kontroléru](./media/storsimple-controller-replacement/IC741047.png)
   
    **Obrázek 2** Uvolňování zámků kontroléru
3. Použijte západku jako popisovač k posunutí kontroleru mimo skříň.
   
    ![Posuvný řadič mimo skříň](./media/storsimple-controller-replacement/IC741048.png)
   
    **Obrázek 3** Odsuňte řadič mimo skříň.

## <a name="insert-a-controller"></a>Vložit kontroler
Pomocí následujícího postupu nainstalujete modul kontroleru dodaný výrobcem po odebrání vadného modulu ze zařízení StorSimple.

#### <a name="to-install-a-controller-module"></a>Instalace modulu kontroleru
1. Zkontrolujte, jestli nedošlo k poškození konektorů rozhraní. Neinstalujte modul, pokud jsou některé PIN kódy konektoru poškozené nebo ohnuté.
2. Posuňte modul Controller do skříně, zatímco západka je plně uvolněná.
   
    ![Posuvný kontroler do skříně](./media/storsimple-controller-replacement/IC741053.png)
   
    **Obrázek 4** Posuvný kontroler do skříně
3. V případě, že je vložen modul kontroleru, začněte s tím, že budete pokračovat v zapínání modulu Controller do skříně. Západka se bude řídit kontroler na místě.
   
    ![Uzavírací západka kontroleru](./media/storsimple-controller-replacement/IC741054.png)
   
    **Obrázek 5** Zavírá se západka kontroleru.
4. Jste hotovi, když se Západ přitahuje na místo. Teď by měl být zapnutý indikátor **OK** .
   
   > [!NOTE]
   > Může trvat až 5 minut, než se kontroler a indikátor LED aktivuje.
  
5. Pokud chcete ověřit, jestli je nahrazení úspěšné, přejděte v Azure Portal do svého zařízení a pak přejděte na **monitor**  >  **stav hardwaru** a ujistěte se, že je řadič 0 i řadič 1 v pořádku (stav je zelený).

## <a name="identify-the-active-controller-on-your-device"></a>Identifikujte aktivní kontroler v zařízení
Existuje mnoho situací, jako je například registrace zařízení za provozu nebo náhrada řadiče, které vyžadují, abyste našli aktivní kontroler na zařízení StorSimple. Aktivní kontroler zpracovává všechny síťové firmware a síťové operace. Aktivní kontroler můžete identifikovat pomocí kterékoli z následujících metod:

* [K identifikaci aktivního kontroleru použijte Azure Portal.](#use-the-azure-portal-to-identify-the-active-controller)
* [K identifikaci aktivního kontroleru použijte Windows PowerShell pro StorSimple.](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)
* [Ověřte, že fyzické zařízení identifikuje aktivní kontroler.](#check-the-physical-device-to-identify-the-active-controller)

Jednotlivé postupy jsou popsány dále.

### <a name="use-the-azure-portal-to-identify-the-active-controller"></a>K identifikaci aktivního kontroleru použijte Azure Portal.
V Azure Portal přejděte do zařízení a pak **Sledujte**  >  **stav hardwaru** a přejděte do části **řadiče** . Tady můžete ověřit, který kontroler je aktivní.

![Identifikujte aktivní kontroler v Azure Portal](./media/storsimple-controller-replacement/IC752072.png)

**Obrázek 6** Azure Portal zobrazení aktivního kontroleru

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>K identifikaci aktivního kontroleru použijte Windows PowerShell pro StorSimple.
Při přístupu k zařízení prostřednictvím konzoly sériového portu se zobrazí zpráva banner. Zpráva banneru obsahuje základní informace o zařízení, jako je model, název, verze nainstalovaného softwaru a stav řadiče, ke kterému přistupujete. Následující obrázek ukazuje příklad zprávy banner:

![Zpráva sériového proužku](./media/storsimple-controller-replacement/IC741098.png)

**Obrázek 7** Bannerová zpráva ukazující, že Controller 0 jako aktivní

Pomocí zprávy banner můžete zjistit, jestli je řadič, ke kterému jste připojení, aktivní nebo pasivní.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Ověřte, že fyzické zařízení identifikuje aktivní kontroler.
Pokud chcete na svém zařízení identifikovat aktivní kontroler, Najděte modrý indikátor LED nad portem DATA 5 na zadní straně primární skříně.

Pokud je tento indikátor LED blikat, je kontroler aktivní a druhý kontroler je v pohotovostním režimu. Použijte následující diagram a tabulku jako pomůcku.

![Primární skříň zařízení – naplánování na dataportech](./media/storsimple-controller-replacement/IC741055.png)

**Obrázek 8** Back of the Primary skříň s datovými porty a monitorováním diody LED

| Popisek | Description |
|:--- |:--- |
| 1-6 |DATA 0 – 5 síťových portů |
| 7 |Indikátor Blue |

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [nahrazení StorSimple hardwarové součásti](storsimple-8000-hardware-component-replacement.md).

