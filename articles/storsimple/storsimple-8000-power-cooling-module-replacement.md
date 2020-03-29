---
title: Výměna PCM na zařízení řady StorSimple 8000 | Dokumenty společnosti Microsoft
description: Vysvětluje, jak odstranit a vyměnit napájecí a chladicí modul (PCM) na zařízení StorSimple
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 42561570e24aec5edd33248ef1738e53175e480e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60632416"
---
# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Výměna napájecího a chladicího modulu na zařízení StorSimple
## <a name="overview"></a>Přehled
Modul napájení a chlazení (PCM) ve vašem zařízení Microsoft Azure StorSimple se skládá z napájecích a chladicích ventilátorů, které jsou řízeny prostřednictvím primárních skříní a skříní EBOD. Existuje pouze jeden model PCM, který je certifikován pro každou skříň. Primární skříň je certifikována pro PCM o 764 W a skříň EBOD je certifikována pro PCM o 580 W. Přestože pcms pro primární skříň a skříň EBOD se liší, postup nahrazení je totožný.

Tento kurz vysvětluje následující postupy:

* Odebrání pcm
* Instalace náhradního pcm

> [!IMPORTANT]
> Před odebráním a výměnou pcm zkontrolujte bezpečnostní informace v [nahrazení hardwarové součásti StorSimple](storsimple-8000-hardware-component-replacement.md).


## <a name="before-you-replace-a-pcm"></a>Před výměnou PCM
Než počítač PCM nahradíte, mějte na paměti následující důležité problémy:

* Pokud dojde k výpadku napájení pcm, nechte vadný modul nainstalovaný, ale vyjměte napájecí kabel. Ventilátor bude i nadále přijímat energii z krytu a nadále zajišťovat správné chlazení. Pokud ventilátor selže, pcm je třeba okamžitě vyměnit.
* Před vyjmutím PCM odpojte napájení od PCM vypnutím hlavního vypínače (pokud je k dispozici) nebo fyzickým vyjmutím napájecího kabelu. To poskytuje upozornění pro váš systém, že vypnutí napájení je bezprostřední.
* Před výměnou vadného pcm se ujistěte, že je druhý pcm funkční pro pokračování v provozu systému. Vadný PCM musí být co nejdříve nahrazen plně funkčním PCM.
* Výměna modulu PCM trvá jen několik minut, ale musí být dokončena do 10 minut od odstranění neúspěšného PCM, aby se zabránilo přehřátí.
* Všimněte si, že náhradní 764 W PCM moduly dodávané z továrny neobsahují záložní bateriový modul. Před provedením výměny budete muset vyjmout baterii z vadného pcm a poté ji vložit do náhradního modulu. Další informace naleznete v tématu [jak vyjmout a vložit záložní bateriový modul](storsimple-8000-battery-replacement.md).

## <a name="remove-a-pcm"></a>Odebrání pcm
Až budete připraveni odebrat power and cooling module (PCM) ze zařízení Microsoft Azure StorSimple, postupujte podle těchto pokynů.

> [!NOTE]
> Před odebráním pcm ověřte, zda máte správnou náhradu (764 W pro primární skříň nebo 580 W pro skříň EBOD).

#### <a name="to-remove-a-pcm"></a>Odebrání pcm
1. Na klasickém portálu Azure klikněte na **Nastavení > monitorování > stavu hardwaru**. Zkontrolujte stav součástí PCM v části **Sdílené součásti** a zjistěte, které pcm selhaly:
   
   * Pokud došlo k selhání napájení v počítači PCM 0, bude stav **napájení v počítači PCM 0** červený.
   * Pokud došlo k selhání napájení v počítači PCM 1, bude stav **napájení v pcm 1** červený.
   * Pokud ventilátor v PCM 1 selhal, stav **chlazení 0 pro PCM 0** nebo Chlazení 1 pro **PCM 0** bude červený.
2. Vyhledejte neúspěšný počítač PCM na zadní straně primární skříně. Pokud používáte model 8600, identifikujte primární skříň podle identifikačního čísla systémové jednotky zobrazeného na LED displeji na předním panelu. Výchozí ID jednotky zobrazené v primární skříni je **00**, zatímco výchozí ID jednotky zobrazené ve skříni EBOD je **01**. Následující schéma a tabulka vysvětlují přední panel LED displeje.
   
    ![ID systému na předním panelu OPS](./media/storsimple-power-cooling-module-replacement/IC740991.png)
   
     **Obrázek 1** Přední panel zařízení  
   
   | Popisek | Popis |
   |:--- |:--- |
   | 1 |Tlačítko Ztlumit |
   | 2 |Napájení systému |
   | 3 |Porucha modulu |
   | 4 |Logická chyba |
   | 5 |Zobrazení ID jednotky |
3. Indikátorledy monitorování v zadní části primárního krytu lze také použít k identifikaci vadného PCM. Informace o tom, jak pomocí LED diod najít vadný PCM, naleznete v následujícím diagramu a tabulce. Pokud například rozsvítí led dioda odpovídající **selhání ventilátoru,** ventilátor selhal. Podobně platí, že pokud svítí LED dioda odpovídající **ac fail,** napájení selhalo. 
   
    ![Zadní rovina indikátoru monitorování PCM zařízení LED](./media/storsimple-power-cooling-module-replacement/IC740992.png)
   
     **Obrázek 2** Zadní část PCM s indikátorovými LED diodami
   
   | Popisek | Popis |
   |:--- |:--- |
   | 1 |Výpadku napájení střídavého proudu |
   | 2 |Selhání ventilátoru |
   | 3 |Porucha baterie |
   | 4 |PCM je v pořádku |
   | 5 |Selhání napájení stejnosměrného proudu |
   | 6 |Baterie zdravá |
4. Vyhledejte vadný modul PCM na zadní straně zadní části zařízení StorSimple. PCM 0 je vlevo a PCM 1 je vpravo. Následující tabulka vysvětluje moduly.
   
     ![Zadní rovina primárních modulů skříně zařízení](./media/storsimple-power-cooling-module-replacement/IC740994.png)
   
     **Obrázek 3** Zadní část zařízení se zásuvnými moduly 
   
   | Popisek | Popis |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Kontroler 0 |
   | 4 |Kontroler 1 |
5. Vypněte vadný PCM a odpojte napájecí kabel. Nyní můžete odebrat PCM.
6. Uchopte západku a stranu rukojeti PCM mezi palcem a ukazováčkem a stlačte je k sobě, abyste ji otevřeli.
   
    ![Otevření rukojeti PCM](./media/storsimple-power-cooling-module-replacement/IC740995.png)
   
    **Obrázek 4** Otevření rukojeti PCM
7. Uchopte rukojeť a vyjměte PCM.
   
    ![Odebrání pcm zařízení](./media/storsimple-power-cooling-module-replacement/IC740996.png)
   
    **Obrázek 5** Odebrání PCM

## <a name="install-a-replacement-pcm"></a>Instalace náhradního pcm
Podle těchto pokynů nainstalujte pcm do zařízení StorSimple. Před instalací náhradního pcm (platí pouze pro pcm s výkonem 764 W) se ujistěte, že jste vložili záložní bateriový modul. Další informace naleznete v tématu [jak vyjmout a vložit záložní bateriový modul](storsimple-8000-battery-replacement.md).

#### <a name="to-install-a-pcm"></a>Instalace pcm
1. Ověřte, zda máte pro tuto přílohu správný náhradní počítač PCM. Primární skříň potřebuje 764 W PCM a skříň EBOD potřebuje 580 W PCM. Neměli byste se pokoušet použít 580 W PCM v primární skříni nebo 764 W PCM ve skříni EBOD. Následující obrázek ukazuje, kde identifikovat tyto informace na štítku, který je připojen k PCM.
   
    ![Popisek PCM zařízení](./media/storsimple-power-cooling-module-replacement/IC740973.png)
   
    **Obrázek 6** Popisek PCM
2. Zkontrolujte, zda není poškozen a nevěnuje se zejména konektorům. 
   
   > [!NOTE]
   > **Neinstalujte modul, pokud jsou ohnuté kolíky konektorů.**
   > 
   > 
3. S rukojetí PCM v otevřené poloze zasuňte modul do krytu.
   
    ![Instalace pcm zařízení](./media/storsimple-power-cooling-module-replacement/IC740975.png)
   
    **Obrázek 7** Instalace PCM
4. Ručně zavřete úchyt PCM. Měli byste slyšet cvaknutí, jak se západka rukojeti zapíná.
   
   > [!NOTE]
   > Chcete-li zajistit, aby se kolíky konektoru zatáhly, můžete jemně tahat za rukojeť bez uvolnění západky. Pokud pcm vyklouzne, znamená to, že západka byla uzavřena před zapojením konektorů.
   
5. Připojte napájecí kabely ke zdroji napájení a k PCM.
6. Zajistěte balíky odlehčení tahu.
7. Zapněte pcm.
8. Ověřte, zda byla výměna úspěšná: na portálu Azure služby StorSimple Device Manager přejděte do zařízení a potom do **nastavení > monitorování > stavu hardwaru**. V části **Sdílené součásti**by měl být stav PCM zelený.
   
   > [!NOTE]
   > Může trvat několik minut, než se náhradní pcm zcela inicializuje.

## <a name="next-steps"></a>Další kroky
Další informace o [výměně hardwarových komponent StorSimple](storsimple-8000-hardware-component-replacement.md).

