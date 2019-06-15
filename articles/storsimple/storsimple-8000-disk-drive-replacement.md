---
title: Výměna diskové jednotky na zařízení StorSimple 8000 series | Dokumentace Microsoftu
description: Vysvětluje, jak Výměna diskové jednotky na primární zařízení StorSimple nebo EBOD skříň.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60576852"
---
# <a name="replace-a-disk-drive-on-your-storsimple-8000-series-device"></a>Výměna diskové jednotky ve vašem zařízení řady StorSimple 8000

## <a name="overview"></a>Přehled
Tento kurz vysvětluje, jak můžete odebrat a nahraďte nefunkční nebo selhání jednotky pevného disku na zařízení s Microsoft Azure StorSimple. Nahradit diskové jednotce, budete muset:

* Musí se vypnout antitamper zámku
* Odeberte diskové jednotky
* Nainstalujte na náhradní disk

> [!IMPORTANT]
> Před odebírání a nahrazování diskovou jednotku, přečtěte si informace bezpečnost v [StorSimple hardwarové komponenty nahrazení](storsimple-8000-hardware-component-replacement.md).
 

## <a name="disengage-the-antitamper-lock"></a>Musí se vypnout antitamper zámku
Tento postup vysvětluje, jak můžete zapojení nebo odpojí po nahrazení diskové jednotky antitamper uzamčení zařízení StorSimple. Antitamper zámky jsou umístěny v popisovačích dopravce jednotky a jsou přístupné prostřednictvím malé clona v části zámek popisovače. Jednotky se dodávají s zámky. Nastavte k poloze.

#### <a name="to-unlock-the-antitamper-lock"></a>Odemknout antitamper zámku
1. Pečlivě vložte klíč zámku ("tamperproof" T10 šroubovák, které Microsoft poskytuje) do otvorů v popisovač a do jeho soketu. 
   
   Pokud je aktivovaná antitamper zámek, je zobrazen v clona red indikátoru.
  
    ![Uzamčené diskovou jednotku](./media/storsimple-disk-drive-replacement/IC741056.png)
   
    **Obrázek 1** zámek proti proti zapojení
   
   | Štítek | Popis |
   |:--- |:--- |
   | 1 |Clona indikátor |
   | 2 |Antitamper zámku |
2. Proveďte otočení klíče v proti směru hodinových ručiček směr, dokud indikátor red není zobrazená v clona nad klíči.
3. Odebrání klíče.
   
    ![Odemknout diskovou jednotku](./media/storsimple-disk-drive-replacement/IC741057.png)
   
    **Obrázek 2** Odemknutý diskovou jednotku
4. Na disk je teď možné odebrat.

Postupujte podle kroků v opačném pořadí provozovat zámek.

## <a name="remove-the-disk-drive"></a>Odeberte diskové jednotky
Zařízení StorSimple podporuje konfiguraci RAID 10 jako úložiště prostorů. Z toho vyplývá, že může fungovat normálně s jedním neúspěšné diskem SSD (SOLID-State drive), nebo pevného disku (HDD).

> [!IMPORTANT]
> * Pokud váš systém má více než jeden selhání disku, neodebírejte více než jeden SSD nebo pevný disk ze systému v libovolném bodě v čase. To může vést ke ztrátě dat.
> * Ujistěte se, že umístíte nahrazení SSD v přihrádce, která dříve obsahovala SSD. Podobně umístěte nahrazení pevného disku v přihrádce, která dříve obsahovala pevný disk.
> * Na webu Azure Portal, sloty jsou číslovány od 0 – 11. Proto pokud na portálu se zobrazí, že ve slotu 2 selhání disku, na zařízení, Hledat poškozeném disku na třetí pozici od levého horního.
> 
> 

Jednotky můžete odebrán a nahrazen, když je operační systém.

#### <a name="to-remove-a-drive"></a>K odebrání jednotky
1. K identifikaci selhání disku, na webu Azure Portal, přejděte do svého zařízení **Nastavení > Stav hardwaru**. Protože disk může selhat v primárním zařízení a/nebo v EBOD skříň (Pokud používáte 8600 model), podívejte se na stav disků ve skupinovém rámečku **sdílené komponenty** a v části **sdílené komponenty EBOD**. Selhání disku v obou skříň zobrazí červený stav.
2. Vyhledejte disky ve primárního skříň nebo EBOD skříň. 
3. Pokud je disk odemknutí, pokračujte k dalšímu kroku. Pokud je na disku, odemknout pomocí postupu v [musí vypnout antitamper Zámek](#disengage-the-antitamper-lock).
4. Stiskněte černé zámek na modulu dopravce jednotky a vyžádaná popisovač dopravce jednotky out a okamžitě z přední části skříni.
   
    ![Uvolnění popisovače diskovou jednotku](./media/storsimple-disk-drive-replacement/IC741051.png)
   
    **Obrázek 3** uvolnění popisovače jednotky
5. Jakmile popisovač dopravce jednotky se plně rozšíří, potáhněte dopravce jednotky mimo skříni. 
   
    ![Klouzavé disku z disku](./media/storsimple-disk-drive-replacement/IC741052.png)
   
    **Obrázek 4** klouzavé disku mimo dopravce

## <a name="install-the-replacement-disk-drive"></a>Nainstalujte na náhradní disk
Poté, co jednotku se nezdařilo v zařízení StorSimple a odeberete ji, pomocí následujícího postupu ji nahraďte novou jednotku.

#### <a name="to-insert-a-drive"></a>Chcete-li vložit na jednotku
1. Ujistěte se, že popisovač dopravce jednotky plně prodlouží, jak je znázorněno na následujícím obrázku.
   
    ![Diskovou jednotku s popisovačem rozšířené](./media/storsimple-disk-drive-replacement/IC741044.png)
   
    **Obrázek 5** jednotku s popisovačem rozšířené
2. Ukázat dopravce jednotky úplně ve skříni.
   
    ![Klouzavé disku do dopravce diskovou jednotku](./media/storsimple-disk-drive-replacement/IC741045.png)
   
    **Obrázek 6** klouzavé dopravce jednotky do skříň.
3. S dopravce jednotky vložen zavřete popisovač dopravce jednotky přitom pro nasdílení změn do skříně, jednotky dopravce dokud popisovač dopravce jednotky přichytí k uzamčené umístění.
4. Použijte klíč zámek dodaný společností Microsoft (tamperproof Torx šroubovák) k zabezpečení dopravce popisovač do místa zapnutí zámku šroubovacím čtvrtletí zapnout po směru hodinových ručiček.
5. Ověřte, že nahrazení proběhlo úspěšně a je funkční jednotku. Přístup k webu Azure portal a přejděte do **nastavení zařízení** > **stav hardwaru**. V části **sdílené komponenty** nebo **sdílené komponenty EBOD**, stav jednotky by měla být zelenou, která udává, že je v pořádku.

   
   > [!NOTE]
   > Může trvat několik hodin disku stav změní na zelenou po nahrazení.
  
## <a name="next-steps"></a>Další postup
Další informace o [StorSimple hardwarové komponenty nahrazení](storsimple-8000-hardware-component-replacement.md).

