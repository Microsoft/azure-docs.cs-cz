---
title: Nahraďte baterie na zařízení řady Microsoft Azure StorSimple 8000 | Microsoft Docs
description: Popisuje, jak odebrat, nahraďte a udržovat modul zálohování baterie zařízení StorSimple.
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
ms.openlocfilehash: f8071cde67017ff031418f0d97da15a618c4969b
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2018
ms.locfileid: "27742809"
---
# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>Nahraďte modul zálohování baterie zařízení StorSimple

## <a name="overview"></a>Přehled
Primární skříň napájení a chlazení modulu (PCM) na zařízení s Microsoft Azure StorSimple má balík další stav baterie. Tento balíček poskytuje power tak, aby zařízení StorSimple můžete uložit data, pokud dojde ke ztrátě napájení ke skříni primární. Tato sada pack baterie se označuje jako *zálohování baterie modulu*. Modul zálohování baterie existuje pouze pro primární skříň v zařízení StorSimple (skříni EBOD neobsahuje modul zálohování baterie).

Tento kurz vysvětluje postup:

* Odebere modul zálohování baterie
* Nainstalujte nový modul zálohování baterie
* Udržovat modul zálohování baterie

> [!IMPORTANT]
> Před odebírání a nahrazování modul zálohování baterie zkontrolovat informace o zabezpečení v [Úvod do StorSimple hardwarové součásti nahrazení](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-backup-battery-module"></a>Odebere modul zálohování baterie
Modul zálohování baterie pro zařízení StorSimple je periferní Výměnná jednotka. Před instalací v PCM, modul baterie by měly být uložené v původním balení. Proveďte následující kroky k odebrání zálohování baterie.

#### <a name="to-remove-the-backup-battery-module"></a>Chcete-li odebrat modul zálohování baterie
1. Na portálu Azure přejděte do okna služby váš správce zařízení StorSimple. Přejděte na **zařízení** a potom vyberte zařízení ze seznamu zařízení. Přejděte na **monitorování** > **stavu hardwaru**. V části **sdílené součásti**, podívejte se na stav baterie.
2. Identifikujte PCM, ve kterém se nezdařilo baterie. Obrázek 1 zobrazuje zadní straně zařízení StorSimple.
   
    ![Propojovací rozhraní systému modulů skříň primární zařízení](./media/storsimple-battery-replacement/IC740994.png)
   
    **Obrázek 1** zadní zobrazující modulů PCM a řadiče primární zařízení
   
   | Štítek | Popis |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Řadič 0 |
   | 4 |Řadič 1 |
   
    Jak je znázorněno číslem 3 na obrázku 2, monitorování indikátoru VEDLA na PCM 0, která odpovídá **baterie selhání** by měl být lit.
   
    ![Propojovací rozhraní systému zařízení PCM monitorování kláves](./media/storsimple-battery-replacement/IC740992.png)
   
    **Obrázek 2** zpět of PCM zobrazující monitorování indikátoru LED
   
   | Štítek | Popis |
   |:--- |:--- |
   | 1 |Výpadku napájení ze sítě |
   | 2 |Ventilátor selhání |
   | 3 |Selhání stav baterie. |
   | 4 |PCM OK |
   | 5 |Řadič domény výpadku proudu |
   | 6 |Dobrý stav baterie |
3. Chcete-li odebrat PCM s selhání baterie, postupujte podle kroků v [odebrat PCM](storsimple-8000-power-cooling-module-replacement.md#remove-a-pcm).
4. S PCM odebrána navýšení otočit popisovač modulu baterie směrem nahoru, jak je uvedeno v následující obrázek a načítat až odebrat baterie.
   
    ![Odebráním PCM stav baterie.](./media/storsimple-battery-replacement/IC741019.png)
   
    **Obrázek 3** odebráním PCM baterie
5. Umístěte modul periferní výměnná Jednotka balení.
6. Vrátí vadný jednotky společnosti Microsoft pro správné údržby a zpracování.

## <a name="install-a-new-backup-battery-module"></a>Nainstalujte nový modul zálohování baterie
Proveďte následující kroky k instalaci modulu baterie nahrazení v PCM ve skříni primární zařízení StorSimple.

#### <a name="to-install-the-battery-module"></a>Nainstalovat modul stav baterie.
1. Umístěte modul zálohování baterie správnou orientaci v PCM.
2. Podržte popisovač modulu baterie úplně pro konektor.
3. Nahraďte PCM ve skříni primární podle pokynů v [nahrazení energii a chlazení modulu zařízení StorSimple](storsimple-8000-power-cooling-module-replacement.md).
4. Po dokončení nahrazení přejděte na zařízení a potom přejděte na **monitorování** > **stavu hardwaru** na portálu Azure. Zkontrolujte stav baterie a ujistěte se, že instalace proběhla úspěšně. Zelený stav označuje, že je v pořádku baterie.

## <a name="maintain-the-backup-battery-module"></a>Udržovat modul zálohování baterie
V zařízení StorSimple poskytuje modul zálohování baterie power řadiče při ztrátě událostí do power. Umožňuje zařízení StorSimple pro uložení důležitých dat před vypíná řízené způsobem. S dvě plně účtovat baterie v PCMs systému může zpracovávat dvě po sobě jdoucích ztrátu události.

Na portálu Azure **stavu hardwaru** pod **monitorování** okno určuje, zda pracuje správně baterie nebo se blíží koncoví-dobu životnosti. Je indikován stav baterie **baterie v PCM 0** nebo **baterie v PCM 1** pod **sdílené součásti**. Toto okno se zobrazí **SNÍŽENÝ** stavu pro ukončenou životností blíží, a **se nezdařilo** pro koncové životnosti dostupný.

> [!NOTE]
> Může hlásit baterie **se nezdařilo** když je jednoduše potřeba nic nestrhne.


Pokud **SNÍŽENÝ** stav se zobrazí, doporučujeme během následující akce:

* Systém pravděpodobně došlo k poslední výpadku napájení nebo baterie může probíhat periodické údržby. Sledujte systému 12 hodin, než budete pokračovat.
  
  * Pokud stav není stále **SNÍŽENÝ** po 12 hodinách nepřetržité připojení k AC spotřeby s řadiči a PCMs systémem, pak baterie je nutné vyměnit. Prosím [kontaktovat Microsoft Support](storsimple-8000-contact-microsoft-support.md) pro modul zálohování baterie nahrazení.
  * Pokud stav se změní na OK po 12 hodinách, baterie funkční a potřeba jenom údržby poplatků.
* Pokud nedošlo k přidružené ke ztrátě napájení a PCM je zapnutý a připojený k napájení ze sítě, je nutné vyměnit baterie. [Kontaktujte Microsoft Support](storsimple-8000-contact-microsoft-support.md) pořadí modul zálohování baterie nahrazení.

> [!IMPORTANT]
> Odstranění se nezdařilo baterie podle national a místní předpisy.

## <a name="next-steps"></a>Další postup
Další informace o [StorSimple hardwarové součásti nahrazení](storsimple-8000-hardware-component-replacement.md).

