---
title: Výměna PCM na zařízení řady StorSimple 8000 | Microsoft Docs
description: Vysvětluje, jak odebrat a nahradit modul Power and chlazení (PCM) na zařízení StorSimple.
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
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 045cec85174a88d1d608a4adc679461008852768
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85514586"
---
# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Výměna napájecího a chladicího modulu na zařízení StorSimple
## <a name="overview"></a>Přehled
Modul Power and chlazení (PCM) ve vašem zařízení Microsoft Azure StorSimple se skládá z napájení a chladicích ventilátorů, které jsou ovládány prostřednictvím primárních a EBOD skříní. K dispozici je pouze jeden model PCM, který je pro každou skříňku certifikovaný. Primární skříň je certifikována pro 764 W PCM a skříň EBOD je certifikována pro 580 W PCM. I když PCMs pro primární skříň a skříň EBOD se liší, je postup nahrazení identický.

Tento kurz vysvětluje následující postupy:

* Odebrání PCM
* Instalace náhradního PCM

> [!IMPORTANT]
> Před odebráním a nahrazením PCM si Projděte bezpečnostní informace v části [StorSimple hardwarové součásti výměna](storsimple-8000-hardware-component-replacement.md).


## <a name="before-you-replace-a-pcm"></a>Před nahrazením PCM
Před nahrazením PCM si pamatujte na následující důležité problémy:

* Pokud se napájení PCM nezdařilo, ponechte poškozený modul nainstalovaný, ale odeberte napájecí kabel. Ventilátor bude nadále přijímat energii z skříně a bude pokračovat v poskytování řádného chlazení. Pokud se ventilátor nezdařil, je nutné vyměnit PCM okamžitě.
* Než odeberete PCM, odpojte napájení služby PCM vypnutím hlavního přepínače (Pokud je k dispozici) nebo fyzického odebrání napájecí šňůry. Tím se zobrazí upozornění systému na bezprostřední vypnutí napájení.
* Před nahrazením chybného PCM zajistěte, aby byl jiný modul PCM funkční pro pokračování systémové operace. Vadná Služba PCM musí být nahrazena plně funkčním PCM, jakmile to bude možné.
* Nahrazení modulu PCM trvá jenom několik minut, ale je potřeba ho dokončit do 10 minut od odebrání neúspěšného vyhřívání, aby se zabránilo přehřátí.
* Všimněte si, že náhradní moduly PCM 764 W dodávané z továrny neobsahují modul záložní baterie. Před provedením náhrady budete muset odstranit baterii z vadného PCM a pak ji vložit do nahrazujícího modulu. Další informace najdete v tématu Jak [Odebrat a vložit modul záložní baterie](storsimple-8000-battery-replacement.md).

## <a name="remove-a-pcm"></a>Odebrání PCM
Postupujte podle těchto pokynů, pokud jste připraveni odebrat modul Power and chlazení z Microsoft Azure StorSimple zařízení.

> [!NOTE]
> Před odebráním PCM ověřte, že máte správnou náhradu (764 W pro primární skříň nebo 580 W pro skříň EBOD).

#### <a name="to-remove-a-pcm"></a>Odebrání PCM
1. Na portálu Azure Classic klikněte na **nastavení > monitorování > stav hardwaru**. Ověřte stav komponent PCM v části **sdílené součásti** a určete, který PCM se nezdařil:
   
   * Pokud se zdroji napájení v PCM 0 nezdařilo, bude stav zdroje **napájení v PCM 0** červený.
   * Pokud se zdroj napájení v PCM 1 nezdařil, bude stav zdroje **napájení v PCM 1** červený.
   * Pokud se ventilátor v PCM 1 nezdařil, stav buď **chladicího 0 pro PCM** 0 nebo **chlazení 1 pro PCM 0** bude červený.
2. Vyhledejte neúspěšné PCM na zadní straně primární skříně. Pokud používáte model 8600, identifikujte primární skříň tím, že prohlížíte identifikační číslo systémové jednotky zobrazené na předním panelu zobrazení indikátoru LED. Výchozí ID jednotky zobrazené v primární skříni je **00**, zatímco výchozí ID jednotky zobrazené na skříni eBOD je **01**. Následující diagram a tabulka vysvětlují přední panel zobrazení indikátoru LED.
   
    ![ID systému na předním panelu OPS](./media/storsimple-power-cooling-module-replacement/IC740991.png)
   
     **Obrázek 1** Přední panel zařízení  
   
   | Popisek | Popis |
   |:--- |:--- |
   | 1 |Tlačítko pro ztlumení |
   | 2 |Výkon systému |
   | 3 |Chyba modulu |
   | 4 |Logická chyba |
   | 5 |Zobrazení ID jednotky |
3. Pro identifikaci vadného PCM se dá použít i indikátor LED na zadní straně primární skříně. V následujícím diagramu a tabulce najdete informace o tom, jak používat diody LED k vyhledání vadného PCM. Například pokud je indikátor LED odpovídající **selhání ventilátoru** osvětlen, ventilátor se nezdařil. Podobně, pokud je indikátor LED odpovídající **výpadku** napájení osvětlen, napájení se nezdařilo. 
   
    ![Naplánování indikátorů LED sledování zařízení PCM](./media/storsimple-power-cooling-module-replacement/IC740992.png)
   
     **Obrázek 2** Zpět PCM s indikátorem LED diod
   
   | Popisek | Popis |
   |:--- |:--- |
   | 1 |Výpadek napájení elektrické sítě |
   | 2 |Selhání ventilátoru |
   | 3 |Chyba baterie |
   | 4 |PCM – OK |
   | 5 |Selhání napájení řadiče domény |
   | 6 |Stav baterie v pořádku |
4. Podívejte se na následující diagram back-StorSimple zařízení a vyhledejte modul PCM, který selhal. PCM 0 je vlevo a PCM 1 je napravo. Následující tabulka vysvětluje moduly.
   
     ![Naplánování modulů primární skříně zařízení](./media/storsimple-power-cooling-module-replacement/IC740994.png)
   
     **Obrázek 3** Vrácení zařízení s modulem plug-in 
   
   | Popisek | Popis |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Kontroler 0 |
   | 4 |Kontroler 1 |
5. Vypněte vadný PCM a odpojte kabel napájecího zdroje. Nyní můžete odebrat PCM.
6. Přiuchopit západku a stranu popisovače PCM mezi vašimi poforefingermi a jejich přetažením a otevřete popisovač.
   
    ![Otevírá se popisovač PCM.](./media/storsimple-power-cooling-module-replacement/IC740995.png)
   
    **Obrázek 4** Otevření popisovače PCM
7. Úchyt a odstraňte PCM.
   
    ![Odebírá se zařízení PCM.](./media/storsimple-power-cooling-module-replacement/IC740996.png)
   
    **Obrázek 5** Odebírá se PCM.

## <a name="install-a-replacement-pcm"></a>Instalace náhradního PCM
Podle těchto pokynů nainstalujte do zařízení StorSimple PCM. Před instalací náhradního PCM (platí jenom pro 764 W PCMs) se ujistěte, že jste vložili modul pro zálohování na záložní baterii. Další informace najdete v tématu Jak [Odebrat a vložit modul záložní baterie](storsimple-8000-battery-replacement.md).

#### <a name="to-install-a-pcm"></a>Instalace PCM
1. Ověřte, že máte pro tuto skříň správnou náhradu PCM. Primární skříň potřebuje 764 W PCM a skříň EBOD potřebuje 580 W PCM. Neměli byste se pokoušet použít 580 W PCM v primární skříni ani 764 W PCM v EBOD skříni. Následující obrázek ukazuje, kde identifikovat tyto informace na popisku připojeném k PCM.
   
    ![Jmenovka PCM zařízení](./media/storsimple-power-cooling-module-replacement/IC740973.png)
   
    **Obrázek 6** Popisek PCM
2. Podívejte se na poškození skříně a věnujte zvláštní pozornost konektorům. 
   
   > [!NOTE]
   > **Neinstalujte modul, pokud jsou nějaké kolíky konektoru ohnuté.**
   > 
   > 
3. S popisovačem PCM v otevřené pozici posuňte modul do skříně.
   
    ![Instalace PCM zařízení](./media/storsimple-power-cooling-module-replacement/IC740975.png)
   
    **Obrázek 7** Instalace PCM
4. Ručně zavřete popisovač PCM. Měli byste slyšet kliknutí jako západ táhla.
   
   > [!NOTE]
   > Aby se zajistilo, že PIN kódy konektorů jsou zavedené, můžete na popisovač zvolna Tug bez uvolnění západky. Pokud se snímky PCM vydělí, znamená to, že západka byla před započetím konektorů uzavřená.
   
5. Připojte napájecí kabely ke zdroji napájení a k PCM.
6. Zabezpečte Bales omezení.
7. Zapněte PCM.
8. Ověřte, že nahrazení bylo úspěšné: v Azure Portal služby Správce zařízení StorSimple přejděte do svého zařízení a pak na **nastavení > monitorování > stav hardwaru**. Ve **sdílených součástech** by měl být stav PCM zelený.
   
   > [!NOTE]
   > Aby se náhradní modul PCM mohl úplně inicializovat, může trvat několik minut.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [nahrazení StorSimple hardwarové součásti](storsimple-8000-hardware-component-replacement.md).

