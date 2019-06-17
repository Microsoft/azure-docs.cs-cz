---
title: Výměna baterie na zařízení Microsoft Azure StorSimple 8000 series | Dokumentace Microsoftu
description: Popisuje postup odstranění, nahrazení a udržovat modulu záložní baterie zařízení StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 4ebf3f28d40e0461d140a3fe74fb940720f26db6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64693756"
---
# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>Nahraďte modulu záložní baterie zařízení StorSimple

## <a name="overview"></a>Přehled
Primární skříň napájení a chlazení modulu (PCM) na zařízení s Microsoft Azure StorSimple má balíček dalších baterie. Tento balíček poskytuje power tak, aby zařízení StorSimple může ukládat data, pokud dojde ke ztrátě napájení na primární zařízení. Tento balíček baterie se označuje jako *záložní baterie modulu*. Modul záložní baterie existuje pouze pro primární skříň v zařízení StorSimple (EBOD skříň neobsahuje modul záložní baterie).

Tento kurz vysvětluje následující postupy:

* Odebrání modulu záložní baterie
* Nainstalovat nový modul záložní baterie
* Udržovat modulu záložní baterie

> [!IMPORTANT]
> Před odebírání a nahrazování modul záložní baterie, přečtěte si informace bezpečnost v [Úvod do služby StorSimple hardwarové komponenty nahrazení](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-backup-battery-module"></a>Odebrání modulu záložní baterie
Modul záložní baterie pro zařízení StorSimple je Výměnná jednotka. Před instalací v PCM, baterie modul by měla být uložena v původním balení. Proveďte následující kroky k odebrání záložní baterie.

#### <a name="to-remove-the-backup-battery-module"></a>Odebrání modulu záložní baterie
1. Na webu Azure Portal přejděte do okna služby Správce zařízení StorSimple. Přejděte na **zařízení** a pak vyberte zařízení ze seznamu zařízení. Přejděte do **monitorování** > **stav hardwaru**. V části **sdílených komponent**, podívejte se na stav baterie.
2. Identifikace PCM, ve kterém se nezdařilo baterie. Obrázek 1 ukazuje zpět zařízení StorSimple.
   
    ![Propojovací rozhraní modulů primární skříň zařízení](./media/storsimple-battery-replacement/IC740994.png)
   
    **Obrázek 1** zadní primární zařízení, na kterém PCM a kontroler moduly
   
   | Štítek | Popis |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Kontroler 0 |
   | 4 |Kontroler 1 |
   
    Jak je znázorněno na obrázku 2 číslem 3, indikátorů VEDLA v PCM 0, která odpovídá **baterie selhání** by měl být lit.
   
    ![Propojovací rozhraní systému zařízení PCM monitorování indikátorů LED](./media/storsimple-battery-replacement/IC740992.png)
   
    **Obrázek 2** PCM zpět o zobrazení monitorování indikátorů LED
   
   | Štítek | Popis |
   |:--- |:--- |
   | 1 |Výpadku napájení AC. |
   | 2 |Chyba větráku |
   | 3 |Selhání baterie |
   | 4 |PCM OK |
   | 5 |Výpadku napájení řadiče domény |
   | 6 |Baterie v pořádku |
3. Chcete-li odebrat PCM s neúspěšné baterie, postupujte podle kroků v [odebrat PCM](storsimple-8000-power-cooling-module-replacement.md#remove-a-pcm).
4. S PCM odebrána přenést otočit popisovač modulu baterie směrem nahoru, jak je uvedeno na následujícím obrázku a o přijetí změn až odebrat baterie.
   
    ![Odebrání baterie v PCM](./media/storsimple-battery-replacement/IC741019.png)
   
    **Obrázek 3** odebrání baterie PCM
5. Umístěte modulu výměnná Jednotka balení.
6. Vrátí vadné částí společnosti Microsoft pro správnou údržbu a zpracování.

## <a name="install-a-new-backup-battery-module"></a>Nainstalovat nový modul záložní baterie
Proveďte následující kroky k instalaci modulu nahrazení baterie v PCM ve primárního skříni zařízení StorSimple.

#### <a name="to-install-the-battery-module"></a>Chcete-li nainstalovat modul baterie
1. Místo modulu záložní baterie ve správné orientaci v PCM.
2. Popisovač modulu baterie klávesu až konektor pracovní stanice.
3. Výměna PCM ve primárního skříni podle pokynů v [nahradit napájení a chlazení modulu zařízení StorSimple](storsimple-8000-power-cooling-module-replacement.md).
4. Po dokončení nahrazení přejděte do svého zařízení a potom přejděte ke **monitorování** > **stav hardwaru** na webu Azure Portal. Stav baterie, abyste měli jistotu, že instalace proběhla úspěšně ověřte. Zelený stav označuje, že je v dobrém stavu baterie.

## <a name="maintain-the-backup-battery-module"></a>Udržovat modulu záložní baterie
V zařízení StorSimple modul záložní baterie poskytuje výkonné nástroje pro kontroler během události ke ztrátě výkonu. To umožňuje zařízení StorSimple k ukládání důležitých dat před vypínání kontrolovaně. S dvěma plně nabitá baterie v PCMs může systém zpracovat dvě po sobě jdoucích ztrátu události.

Na webu Azure Portal **stav hardwaru** pod **monitorování** okno označuje, zda pracuje správně baterie nebo ukončení životnosti technologie se blíží. Je indikován stav baterie **baterie v PCM 0** nebo **baterie v PCM 1** pod **sdílených komponent**. V tomto okně se zobrazí **SNÍŽENÝ** stavu blíží ukončenou životností a **NEÚSPĚŠNÉ** pro koncové zásadní bylo dosaženo.

> [!NOTE]
> Baterie může hlásit **NEÚSPĚŠNÉ** když je jednoduše potřeba účtovat.


Pokud **SNÍŽENÝ** se zobrazí stav, doporučujeme následující kurz akce:

* Systém pravděpodobně došlo k poslední výpadku napájení nebo baterie může probíhá pravidelná údržba. Sledujte systém po dobu 12 hodin, než budete pokračovat.
  
  * Pokud stav není stále **SNÍŽENÝ** po 12 hodinách průběžný připojení k AC napájení řadiče a PCMs systémem, pak baterie potřebuje vyměnit. Prosím [obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md) pro modul záložní baterie nahrazení.
  * Pokud stav změní na OK po 12 hodinách, baterie je funkční a jenom potřebné údržby poplatek.
* Pokud nebyl související ztráta napájení ze sítě a je zapnuté a připojené k elektrické PCM, baterie potřebuje vyměnit. [Obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md) řazení modulu záložní baterie nahrazení.

> [!IMPORTANT]
> Vyřazení se nezdařilo baterie podle státní a místní předpisy.

## <a name="next-steps"></a>Další postup
Další informace o [StorSimple hardwarové komponenty nahrazení](storsimple-8000-hardware-component-replacement.md).

