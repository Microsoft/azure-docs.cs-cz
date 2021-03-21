---
title: Výměna baterie na zařízení řady Microsoft Azure StorSimple 8000
description: Popisuje, jak odebrat, nahradit a udržovat modul záložní baterie na zařízení StorSimple.
author: alkohli
ms.service: storsimple
ms.topic: troubleshooting
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 17a6a07d117557cdee23634bd211a14cf90d49e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023767"
---
# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>Výměna modulu záložní baterie na zařízení StorSimple

## <a name="overview"></a>Přehled
Modul Power and chladicího modulu (PCM) na vašem zařízení Microsoft Azure StorSimple má další balíček pro baterii. Tato sada poskytuje napájení, aby zařízení StorSimple mohlo ukládat data v případě výpadku napájení z elektrické sítě do primární skříně. Tato sada baterií je označována jako *modul záložní baterie*. Modul zálohování záložní baterie existuje jenom pro primární skříň v zařízení StorSimple (skříň EBOD neobsahuje modul záložní baterie).

Tento kurz vysvětluje následující postupy:

* Odebrat modul záložní baterie
* Nainstalovat nový modul záložní baterie
* Udržovat modul záložní baterie

> [!IMPORTANT]
> Než odeberete a nahradíte modul pro záložní baterii, Projděte si bezpečnostní informace v části [Úvod do StorSimple hardwarové součásti](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-backup-battery-module"></a>Odebrat modul záložní baterie
Modul záložní baterie pro zařízení StorSimple je jednotka, která je nahraditelným polem. Předtím, než se nainstaluje do PCM, by měl být modul baterie uložený ve svém původním balení. Chcete-li odebrat baterii zálohování, proveďte následující kroky.

#### <a name="to-remove-the-backup-battery-module"></a>Odebrání modulu záložní baterie
1. V Azure Portal otevřete okno služby pro Správce zařízení StorSimple. V seznamu **zařízení vyberte** zařízení. Přejděte na **monitor**  >  **stav hardwaru**. V části **sdílené součásti** se podívejte na stav baterie.
2. Identifikujte PCM, ve kterém se baterie nezdařila. Obrázek 1 ukazuje back-StorSimple zařízení.
   
    ![Naplánování modulů primární skříně zařízení](./media/storsimple-battery-replacement/IC740994.png)
   
    **Obrázek 1** Zpět primární zařízení, které zobrazuje moduly PCM a Controller
   
   | Popisek | Description |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Kontroler 0 |
   | 4 |Kontroler 1 |
   
    Jak je znázorněno číslem 3 na obrázku 2, indikátor monitorování VEDLO na PCM 0, který odpovídá **chybě baterie** , by měl být osvětlen.
   
    ![Naplánování indikátorů LED sledování zařízení PCM](./media/storsimple-battery-replacement/IC740992.png)
   
    **Obrázek 2** Zpět PCM ukazující indikátory LED sledování
   
   | Popisek | Description |
   |:--- |:--- |
   | 1 |Výpadek napájení elektrické sítě |
   | 2 |Selhání ventilátoru |
   | 3 |Chyba baterie |
   | 4 |PCM – OK |
   | 5 |Selhání napájení řadiče domény |
   | 6 |Stav baterie v pořádku |
3. Pokud chcete odstranit PCM s chybnou baterií, postupujte podle kroků v části [Odebrání PCM](storsimple-8000-power-cooling-module-replacement.md#remove-a-pcm).
4. Po odebrání PCM vyzvedněte a otočte modul baterie vzhůru, jak je znázorněno na následujícím obrázku, a Stáhněte si ho, abyste baterii odstranili.
   
    ![Odebrání baterie ze PCM](./media/storsimple-battery-replacement/IC741019.png)
   
    **Obrázek 3** Odebrání baterie z PCM
5. Umístěte modul do balíčku s nahraditelnými jednotkami.
6. Vraťte vadnou jednotku společnosti Microsoft pro zajištění správné údržby a zpracování.

## <a name="install-a-new-backup-battery-module"></a>Nainstalovat nový modul záložní baterie
Provedením následujících kroků nainstalujete Náhradní modul baterie do PCM v primární skříni zařízení StorSimple.

#### <a name="to-install-the-battery-module"></a>Instalace modulu baterie
1. Dodejte modul záložní baterie do správné orientace v PCM.
2. Stiskněte dolů a modul baterie zpracuje všechny způsoby, jak konektor zasedací.
3. Pomocí pokynů v části [Výměna napájení a chladicího modulu na zařízení StorSimple](storsimple-8000-power-cooling-module-replacement.md)nahraďte PCM v primární skříni.
4. Po nahrazení přejdete do svého zařízení a pak přejdete na **monitor**  >  **stav hardwaru** v Azure Portal. Ověřte stav baterie, abyste se ujistili, že instalace proběhla úspěšně. Zelený stav znamená, že je baterie v pořádku.

## <a name="maintain-the-backup-battery-module"></a>Udržovat modul záložní baterie
V zařízení StorSimple poskytuje modul pro zálohování záložní baterii napájení k řadiči během události ztráty napájení. Umožňuje zařízení StorSimple ukládat kritická data před vypnutím řízeným způsobem. U dvou plně účtovaných baterií v PCMs může systém zpracovávat dvě po sobě jdoucí události ztráty.

V Azure Portal **stav hardwaru** pod oknem **monitor** ukazuje, jestli baterie nefunguje nebo se blíží konci životního cyklu. Stav baterie je uveden na **baterii v PCM 0** nebo **na baterii v PCM 1** v části **sdílené součásti**. V tomto okně se zobrazí **snížený stav pro** blížící **se životnímu cyklu a bylo** dosaženo konce životního cyklu.

> [!NOTE]
> Baterie může hlásit **neúspěšné** , když je potřeba ji účtovat.


Pokud se zobrazí stav **sníženo** , doporučujeme, abyste provedli následující kroky:

* V systému možná došlo k nedávnému výpadku napájení nebo může docházet k pravidelné údržbě baterií. Než budete pokračovat, Sledujte systém po dobu 12 hodin.
  
  * Pokud je stav stále **snížen** po 12 hodinách nepřetržitého připojení k napájení ze sítě s řadiči a PCMs běžet, je nutné vyměnit baterii. [Kontaktujte prosím podpora Microsoftu](storsimple-8000-contact-microsoft-support.md) pro Náhradní modul záložní baterie.
  * Pokud se stav v pořádku po 12 hodinách, baterie bude funkční a bude potřebovat jenom poplatek za údržbu.
* Pokud není k dispozici žádná související ztráta napájení z elektrické sítě a modul PCM je zapnutý a připojený k napájení ze sítě, musí být tato baterie nahrazena. [Kontaktuje podpora Microsoftu](storsimple-8000-contact-microsoft-support.md) pro objednání náhradního modulu zálohování záložní baterie.

> [!IMPORTANT]
> Vyřazení baterie, která selhala, podle vnitrostátních a regionálních předpisů.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [nahrazení StorSimple hardwarové součásti](storsimple-8000-hardware-component-replacement.md).

