---
title: Výměna diskové jednotky na zařízení řady StorSimple 8000 | Microsoft Docs
description: Vysvětluje, jak nahradit diskovou jednotku v StorSimple primární skříni nebo skříni EBOD.
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
ms.date: 8/25/2017
ms.author: alkohli
ms.openlocfilehash: a5a7a31c174271f963bb005f10b8376a59133d36
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014869"
---
# <a name="replace-a-disk-drive-on-your-storsimple-8000-series-device"></a>Výměna diskové jednotky na zařízení StorSimple řady 8000

## <a name="overview"></a>Přehled
V tomto kurzu se dozvíte, jak můžete odebrat a nahradit jednotku pevného disku, která selhala, na zařízení Microsoft Azure StorSimple. Chcete-li nahradit diskovou jednotku, musíte:

* Odpojí zámek proti falšování.
* Odebrání diskové jednotky
* Instalace náhradní diskové jednotky

> [!IMPORTANT]
> Před odebráním a nahrazením diskové jednotky si Projděte bezpečnostní informace v části [StorSimple hardwarové součásti](storsimple-8000-hardware-component-replacement.md).
 

## <a name="disengage-the-antitamper-lock"></a>Odpojí zámek proti falšování.
Tento postup vysvětluje, jak můžou být zámky proti falšování v zařízení StorSimple zavedeny nebo Nezařazené, když nahradíte diskové jednotky. Zámky proti neoprávněným zásahům jsou nainstalovány v obslužných rutinách hnacího dopravce a jsou k nim přistupované prostřednictvím malého otvoru v části západ v popisovači. Jednotky jsou dodávány s zámky nastavenými na uzamčené umístění.

#### <a name="to-unlock-the-antitamper-lock"></a>Odemčení zámku proti neoprávněné manipulaci
1. Pečlivě vložte klíč zámku ("tamperproof" T10 Screwdriver, který poskytuje společnost Microsoft), do otvoru v popisovači a do jeho soketu. 
   
   Pokud je zámek proti neoprávněnému zásahu aktivovaný, zobrazí se v otvoru červený indikátor.
  
    ![Uzamčená disková jednotka](./media/storsimple-disk-drive-replacement/IC741056.png)
   
    **Obrázek 1** Zapojení zámku proti falšování
   
   | Popisek | Description |
   |:--- |:--- |
   | 1 |Clona indikátoru |
   | 2 |Zámek proti falšování |
2. Otočte klíč za směr proti směru hodinových ručiček, dokud červený indikátor není viditelný na otvoru nad klíčem.
3. Odeberte klíč.
   
    ![Odemčená disková jednotka](./media/storsimple-disk-drive-replacement/IC741057.png)
   
    **Obrázek 2** Odemčená disková jednotka
4. Diskovou jednotku je teď možné odebrat.

Pomocí kroků v opačném případě zapojte zámek.

## <a name="remove-the-disk-drive"></a>Odebrání diskové jednotky
Vaše zařízení StorSimple podporuje konfiguraci prostorů úložiště RAID 10, jako je třeba. To znamená, že může fungovat normálně s jedním vadným diskem, jednotkou SSD (Solid-State Drive) nebo pevným diskem (HDD).

> [!IMPORTANT]
> * Pokud má váš systém více než jeden vadný disk, neodstraňujte v jakémkoli časovém bodě v systému více než jednu jednotku SSD nebo HDD. V důsledku toho může dojít ke ztrátě dat.
> * Ujistěte se, že zadáváte náhradní jednotku SSD do patice, která dříve obsahovala jednotku SSD. Podobně umístěte náhradní pevný disk do patice, která dříve obsahovala pevný disk.
> * V Azure Portal sloty jsou číslovány od 0 – 11. Proto pokud na portálu vidíte, že se disk ve slotu 2 nezdařil, vyhledejte v zařízení disk, který selhal, na třetí pozici vlevo nahoře.
> 
> 

Jednotky je možné odebrat a nahradit, když systém pracuje.

#### <a name="to-remove-a-drive"></a>Odebrání jednotky
1. Chcete-li identifikovat disk, na kterém došlo k chybě, v části Azure Portal přejít do **nastavení zařízení > stav hardwaru**. Vzhledem k tomu, že disk může selhat v primární skříni a/nebo ve skříni EBOD (Pokud používáte model 8600), podívejte se na stav disků v části **sdílené součásti** a v části **sdílené součásti eBOD**. Disk, který selhal v některé skříni, se zobrazí s červeným stavem.
2. Vyhledejte jednotky na začátku primární skříně nebo skříně EBOD. 
3. Pokud je disk odemčený, přejděte k dalšímu kroku. Pokud je disk uzamčený, odemkněte ho pomocí postupu v části zapojte [Zámek proti falšování](#disengage-the-antitamper-lock).
4. Stiskněte černou klávesovou zkratku v modulu nosiče jednotek a vyžádejte si z přední části skříně popisovač jednotky a napravte ho.
   
    ![Uvolňování popisovače diskové jednotky](./media/storsimple-disk-drive-replacement/IC741051.png)
   
    **Obrázek 3** Uvolňuje se popisovač jednotky.
5. Když je popisovač obslužné rutiny jednotky plně rozšířený, přesuňte ho od skříně. 
   
    ![Posunutí disku na diskové jednotce](./media/storsimple-disk-drive-replacement/IC741052.png)
   
    **Obrázek 4** Odsuňte diskovou jednotku od dopravce.

## <a name="install-the-replacement-disk-drive"></a>Instalace náhradní diskové jednotky
Po selhání jednotky ve vašem zařízení StorSimple a jejím odebráním se pomocí tohoto postupu nahraďte novou jednotkou.

#### <a name="to-insert-a-drive"></a>Vložení jednotky
1. Zajistěte, aby byl popisovač nosné jednotky plně rozšířený, jak je znázorněno na následujícím obrázku.
   
    ![Disková jednotka se zvýšeným popisovačem](./media/storsimple-disk-drive-replacement/IC741044.png)
   
    **Obrázek 5** Jednotka s rozšířeným popisovačem
2. Přesuňte nosiči jednotky na skříň.
   
    ![Posunutí disku do nosiče diskové jednotky](./media/storsimple-disk-drive-replacement/IC741045.png)
   
    **Obrázek 6**  Posuňte nosič jednotky na skříň.
3. Když je vložen nosič jednotek, uzavřete popisovač obslužné rutiny jednotky a pokračujte tak, že přehrajete nosič jednotek do skříně, dokud se popisovač obslužné rutiny jednotky nepřichytí do uzamčené pozice.
4. Použijte klíč zámku, který byl poskytnutý Microsoftem (tamperproof Torx Screwdriver), abyste zabezpečili popisovač nosné frekvence tím, že zapnete zámek a posunete ho po směru hodinových ručiček.
5. Ověřte, zda byla náhrada úspěšná a zda je jednotka funkční. Přejděte k Azure Portal a přejděte do **nastavení zařízení**  >  **stav hardwaru**. V části **sdílené součásti** nebo **sdílené součásti eBOD** by měl být stav jednotky zelený, což značí, že je v pořádku.

   
   > [!NOTE]
   > Může trvat několik hodin, než se stav disku po nahrazení změní na zelený.
  
## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [nahrazení StorSimple hardwarové součásti](storsimple-8000-hardware-component-replacement.md).

