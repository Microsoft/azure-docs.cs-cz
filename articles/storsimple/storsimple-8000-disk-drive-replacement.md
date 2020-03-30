---
title: Výměna diskové jednotky na zařízení řady StorSimple 8000 | Dokumenty společnosti Microsoft
description: Vysvětluje, jak nahradit diskovou jednotku v primární skříni StorSimple nebo skříň EBOD.
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
ms.date: 8/25/2017
ms.author: alkohli
ms.openlocfilehash: 3d6ef22e4df36996d68194589f43ea0f57def22c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267908"
---
# <a name="replace-a-disk-drive-on-your-storsimple-8000-series-device"></a>Výměna diskové jednotky na zařízení StorSimple řady 8000

## <a name="overview"></a>Přehled
Tento kurz vysvětluje, jak můžete odebrat a nahradit nefunkční nebo neúspěšný pevný disk na zařízení Microsoft Azure StorSimple. Chcete-li nahradit diskovou jednotku, je třeba:

* Odpojte zámek proti tamperu
* Odebrání diskové jednotky
* Instalace náhradní diskové jednotky

> [!IMPORTANT]
> Před odebráním a výměnou diskové jednotky si přečtěte bezpečnostní informace v [části Výměna hardwarové součásti StorSimple](storsimple-8000-hardware-component-replacement.md).
 

## <a name="disengage-the-antitamper-lock"></a>Odpojte zámek proti tamperu
Tento postup vysvětluje, jak antitamper zámky na zařízení StorSimple může být zapojen nebo odpojen při výměně diskových jednotek. Antitamper zámky jsou namontovány v rukojeti nosiče pohonu a jsou přístupné přes malý otvor v západkové části rukojeti. Pohony jsou dodávány se zámky nastavenými do uzamčené polohy.

#### <a name="to-unlock-the-antitamper-lock"></a>Odemknutí antitamperzámku
1. Opatrně vložte zámek (šroubovák T10 odolný proti neoprávněné manipulaci, který společnost Microsoft poskytla) do otvoru v rukojeti a do zásuvky. 
   
   Pokud je aktivován zámek proti tamperům, červený indikátor je viditelný v otvoru.
  
    ![Uzamčená disková jednotka](./media/storsimple-disk-drive-replacement/IC741056.png)
   
    **Obrázek 1** Proti tamper zámek zapojen
   
   | Popisek | Popis |
   |:--- |:--- |
   | 1 |Clona indikátoru |
   | 2 |Zámek proti tamperu |
2. Otáčejte klávesou proti směru hodinových ručiček, dokud není v otvoru nad klíčem vidět červený indikátor.
3. Odstraňte klíč.
   
    ![Odemčená disková jednotka](./media/storsimple-disk-drive-replacement/IC741057.png)
   
    **Obrázek 2** Odemčená disková jednotka
4. Diskovou jednotku lze nyní odebrat.

Postupujte podle pokynů v opačném směru, aby se zámek zapojil.

## <a name="remove-the-disk-drive"></a>Odebrání diskové jednotky
Vaše zařízení StorSimple podporuje konfiguraci úložných prostorů podobných polím RAID 10. To znamená, že může pracovat normálně s jedním selháním disku, ssd disk (SSD) nebo pevný disk (HDD).

> [!IMPORTANT]
> * Pokud má váš systém více než jeden vadný disk, neodstraňujte ze systému v žádném okamžiku více než jeden disk SSD nebo HDD. Mohlo by dojít ke ztrátě dat.
> * Ujistěte se, že jste umístili náhradní SSD do slotu, který dříve obsahoval SSD. Podobně umístěte náhradní pevný disk do slotu, který dříve obsahoval pevný disk.
> * Na webu Azure Portal se sloty číslují od 0 do 11. Proto pokud portál ukazuje, že disk v patice 2 se nezdařilo, na zařízení, vyhledejte vadný disk ve třetí patici z levého horního rohu.
> 
> 

Jednotky lze vyjmout a vyměnit, když je systém v provozu.

#### <a name="to-remove-a-drive"></a>Odebrání jednotky
1. Pokud chcete identifikovat vadný disk, přejděte na webu Azure Portal na nastavení zařízení **> stav hardwaru**. Vzhledem k tomu, že disk může selhat v primární skříni a/nebo ve skříni EBOD (pokud používáte model 8600), podívejte se na stav disků v části **Sdílené součásti** a v části **Sdílené součásti EBOD**. Neúspěšný disk v obou skříních se zobrazí s červeným stavem.
2. Vyhledejte jednotky v přední části primární skříně nebo skříně EBOD. 
3. Pokud je disk odemčený, přejděte k dalšímu kroku. Pokud je disk uzamčen, odemkněte jej podle postupu v [odpojení antitamperzámku](#disengage-the-antitamper-lock).
4. Stiskněte černou západku na modulu nosiče pohonu a vytáhněte rukojeť nosiče pohonu ven a pryč od přední části podvozku.
   
    ![Uvolnění popisovače diskové jednotky](./media/storsimple-disk-drive-replacement/IC741051.png)
   
    **Obrázek 3** Uvolnění úchytu jednotky
5. Když je rukojeť nosiče pohonu zcela vysunuta, vysuňte nosič pohonu z podvozku. 
   
    ![Posunout disk z diskové jednotky](./media/storsimple-disk-drive-replacement/IC741052.png)
   
    **Obrázek 4** Posunutím diskové jednotky z nosiče

## <a name="install-the-replacement-disk-drive"></a>Instalace náhradní diskové jednotky
Po selhání jednotky v zařízení StorSimple a odebrání, postupujte podle tohoto postupu nahradit novou jednotku.

#### <a name="to-insert-a-drive"></a>Vložení jednotky
1. Ujistěte se, že rukojeť nosiče pohonu je plně vysunutá, jak je znázorněno na následujícím obrázku.
   
    ![Disková jednotka s prodlouženým popisovačem](./media/storsimple-disk-drive-replacement/IC741044.png)
   
    **Obrázek 5** Pohon s prodlouženou rukojetí
2. Zasuňte nosič pohonu až do podvozku.
   
    ![Posuvný disk do nosiče diskové jednotky](./media/storsimple-disk-drive-replacement/IC741045.png)
   
    **Obrázek 6**  Zasunutí nosiče pohonu do podvozku
3. Po vložení nosiče pohonu zavřete rukojeť nosiče pohonu a pokračujte v tlačení nosiče pohonu do podvozku, dokud se rukojeť nosiče pohonu nezaklapne do uzamčené polohy.
4. Pomocí zámkového klíče, který poskytl a disponuje společností Microsoft (šroubovák Torx odolný proti manipulaci), zajistěte rukojeť nosiče na místo otočením šroubu zámku o čtvrtinu otáčení ve směru hodinových ručiček.
5. Ověřte, zda byla výměna úspěšná a zda je jednotka funkční. Přístup k portálu Azure a přejděte na **nastavení** > zařízení**Stav hardwaru**. V části **Sdílené součásti** nebo **sdílené součásti EBOD**by měl být stav jednotky zelený, což znamená, že je v pořádku.

   
   > [!NOTE]
   > Může trvat několik hodin, než se stav disku po výměně zzelená.
  
## <a name="next-steps"></a>Další kroky
Další informace o [výměně hardwarových komponent StorSimple](storsimple-8000-hardware-component-replacement.md).

