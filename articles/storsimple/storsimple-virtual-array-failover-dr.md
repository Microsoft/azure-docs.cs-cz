---
title: Převzetí služeb při selhání a zotavení po havárii pro virtuální pole StorSimple
description: Přečtěte si o zotavení po havárii pro vaše Microsoft Azure StorSimple virtuální pole, včetně podrobných kroků pro převzetí služeb při selhání jiným virtuálním polem.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 3c1f9c62-af57-4634-a0d8-435522d969aa
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6a321b2eb79ea01bbf94e1a413c189ac7f4614ad
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2021
ms.locfileid: "99096419"
---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array-via-azure-portal"></a>Zotavení po havárii a převzetí služeb při selhání zařízení StorSimple Virtual Array prostřednictvím webu Azure Portal

## <a name="overview"></a>Přehled
Tento článek popisuje zotavení po havárii pro vaše Microsoft Azure StorSimple virtuální pole, včetně podrobných kroků pro převzetí služeb při selhání v jiném virtuálním poli. Převzetí služeb při selhání umožňuje přesunout data ze *zdrojového* zařízení v datacentru do *cílového* zařízení. Cílové zařízení se může nacházet ve stejném nebo jiném geografickém umístění. Převzetí služeb při selhání zařízení je pro celé zařízení. Během převzetí služeb při selhání změní cloudová data pro zdrojové zařízení vlastnictví cílového zařízení.

Tento článek se vztahuje pouze na virtuální pole StorSimple. Pokud chcete převzít služby při selhání zařízení řady 8000, přečtěte si [převzetí služeb při selhání zařízení a zotavení po havárii zařízení StorSimple](./storsimple-8000-device-failover-disaster-recovery.md).

## <a name="what-is-disaster-recovery-and-device-failover"></a>Co je zotavení po havárii a převzetí služeb při selhání zařízení?

Ve scénáři zotavení po havárii primární zařízení přestane fungovat. V tomto scénáři můžete přesunout data cloudu přidružená k zařízení, které selhalo, do jiného zařízení. Jako *zdroj* můžete použít primární zařízení a jako *cíl* zadat jiné zařízení. Tento proces se označuje jako *převzetí služeb při selhání*. Během převzetí služeb při selhání budou všechny svazky nebo sdílené složky ze zdrojového zařízení měnit vlastnictví a jsou přeneseny do cílového zařízení. Nepovoluje se žádné filtrování dat.

DR se modeluje jako úplné obnovení zařízení pomocí vrstvení a sledování na základě Heat mapy. Heat mapa je definována přiřazením hodnoty tepla k datům na základě vzorů pro čtení a zápis. Tato Heat mapa pak vrstvy nejnižší objemy tepelného zpracování dat do cloudu napřed a přitom udržuje horní datový proud (nejčastěji používané) datové bloky v místní vrstvě. Při zotavení po havárii používá StorSimple Heat mapu k obnovení a dehydratovanému využití dat z cloudu. Zařízení načte všechny svazky nebo sdílené složky v poslední poslední záloze (podle interního určení) a provede obnovení z této zálohy. Virtuální pole orchestruje celý proces zotavení po havárii.

> [!IMPORTANT]
> Zdrojové zařízení se odstraní na konci převzetí služeb při selhání zařízení, a proto se nepodporuje navrácení služeb po obnovení.
> 
> 

Zotavení po havárii probíhá prostřednictvím funkce převzetí služeb při selhání zařízení a inicializuje se z okna **zařízení** . Toto okno tabulates všechna zařízení StorSimple připojená ke službě StorSimple Správce zařízení. Pro každé zařízení můžete zobrazit popisný název, stav, zřízené a maximální kapacitu, typ a model.

## <a name="prerequisites-for-device-failover"></a>Požadavky na převzetí služeb při selhání zařízení

### <a name="prerequisites"></a>Požadavky

V případě převzetí služeb při selhání zařízení se ujistěte, že jsou splněné následující předpoklady:

* Zdrojové zařízení musí být v **deaktivovaném** stavu.
* Cílové zařízení se musí zobrazit jako **připravené k nastavení** v Azure Portal. Zřídit cílové virtuální pole stejné nebo větší kapacity. Pomocí místního webového uživatelského rozhraní můžete nakonfigurovat a úspěšně zaregistrovat cílové virtuální pole.
  
  > [!IMPORTANT]
  > Nepokoušejte se nakonfigurovat registrované virtuální zařízení prostřednictvím služby. Žádná konfigurace zařízení by se měla provádět prostřednictvím služby.
  > 
  > 
* Cílové zařízení nemůže mít stejný název jako zdrojové zařízení.
* Zdrojové a cílové zařízení musí být stejného typu. Můžete převzít služby jenom z virtuálního pole nakonfigurovaného jako souborový server na jiný souborový server. Totéž platí pro server iSCSI.
* U souborového serveru DR doporučujeme připojit cílové zařízení ke stejné doméně jako zdroj. Tato konfigurace zajistí, že se automaticky vyřeší oprávnění ke sdílení. Podporuje se jenom převzetí služeb při selhání cílového zařízení ve stejné doméně.
* Dostupná cílová zařízení pro DR jsou zařízení, která mají stejnou nebo větší kapacitu v porovnání se zdrojovým zařízením. Zařízení, která jsou připojená k vaší službě, ale nesplňují kritéria pro dostatečné místo, nejsou k dispozici jako cílová zařízení.

### <a name="other-considerations"></a>Další důležité informace

* Pro plánované převzetí služeb při selhání:
  
  * Doporučujeme, abyste všechny svazky nebo sdílené složky na zdrojovém zařízení převzali offline.
  * Doporučujeme, abyste provedli zálohování zařízení a pak pokračovali v převzetí služeb při selhání, abyste minimalizovali ztrátu dat.
* Pro neplánované převzetí služeb při selhání používá zařízení nejnovější zálohu k obnovení dat.

### <a name="device-failover-prechecks"></a>Předkontroly převzetí služeb při selhání zařízení

Před zahájením zotavení po havárii zařízení provede předkontrolu. Tyto kontroly pomůžou zajistit, aby při zahájení programu DR nedocházelo k žádným chybám. Předkontroly zahrnují:

* Ověřuje se účet úložiště.
* Kontroluje se konektivita cloudu k Azure.
* Kontroluje se volné místo na cílovém zařízení.
* Kontroluje se, jestli má zdrojový svazek zařízení serveru iSCSI
  
  * platné názvy ACR.
  * platný identifikátor IQN (nepřekračuje 220 znaků).
  * platná hesla protokolu CHAP (12-16 znaků dlouhé).

Pokud některá z předchozích předkontrol selže, nemůžete pokračovat v programu DR. Vyřešte tyto problémy a pak spusťte zotavení po havárii znovu.

Po úspěšném dokončení programu DR se vlastnictví cloudových dat na zdrojovém zařízení přenese do cílového zařízení. Zdrojové zařízení už není na portálu dostupné. Přístup ke všem svazkům a sdíleným složkám na zdrojovém zařízení je blokovaný a cílové zařízení se aktivuje.

> [!IMPORTANT]
> I když už zařízení není dostupné, virtuální počítač, který jste zřídili v hostitelském systému, pořád spotřebovává prostředky. Po úspěšném dokončení programu DR můžete tento virtuální počítač odstranit z hostitelského systému.
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>Převzetí služeb při selhání virtuálním polem

Před spuštěním tohoto postupu doporučujeme zřídit, nakonfigurovat a zaregistrovat jiné virtuální pole StorSimple pomocí služby StorSimple Správce zařízení.

> [!IMPORTANT]
> 
> * Nemůžete převzít služby při selhání ze zařízení řady StorSimple 8000 na virtuální zařízení 1200.
> * Z virtuálního zařízení s povoleným standardem FIPS (Federal Information Processing Standard) můžete převzít služby při selhání na jiné zařízení s povoleným FIPS nebo na jiné zařízení než FIPS nasazené na portálu státní správy.


Provedením následujících kroků obnovíte zařízení na cílové virtuální zařízení StorSimple.

1. Zřízení a konfigurace cílového zařízení, které splňuje [požadavky pro převzetí služeb při selhání zařízení](#prerequisites). Dokončete konfiguraci zařízení prostřednictvím místního webového uživatelského rozhraní a zaregistrujte ho do služby StorSimple Správce zařízení. Pokud vytváříte souborový server, použijte krok 1 [Nastavení souborového serveru](storsimple-virtual-array-deploy3-fs-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device). Pokud vytváříte server iSCSI, přejít ke kroku 1 v [nastavení serveru iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device).

2. Převeďte svazky nebo sdílené složky na hostiteli v režimu offline. Pokud chcete svazky nebo sdílené složky převést do režimu offline, přečtěte si pokyny pro hostitele na základě konkrétního operačního systému. Pokud jste to ještě neučinili offline, musíte na zařízení přebírat všechny svazky nebo sdílené složky, a to následujícím způsobem.
   
    1. Otevřete okno **zařízení** a vyberte své zařízení.
   
    2. Přejít na **nastavení > spravovat > sdílené složky** (nebo **nastavení > spravovat > svazky**). 
   
    3. Vyberte sdílenou složku/svazek, klikněte pravým tlačítkem a vyberte **převést do režimu offline**. 
   
    4. Po zobrazení výzvy k potvrzení si přečtěte, **Jak mám dopad převedení této sdílené složky do režimu offline.** 
   
    5. Klikněte na **převést do režimu offline**.

3. Ve službě StorSimple Správce zařízení, navštivte **> Správa zařízení**. V okně **zařízení** vyberte a klikněte na zdrojové zařízení.

4. V okně **řídicí panel zařízení** klikněte na **deaktivovat**.

5. V okně **deaktivovat** se zobrazí výzva k potvrzení. Deaktivace zařízení je *trvalý* proces, který nelze vrátit zpět. Také budete upozorněni na to, že vaše sdílené složky nebo svazky převedete do režimu offline na hostiteli. Zadejte název zařízení, který chcete potvrdit, a klikněte na **deaktivovat**.
   
    ![Snímek obrazovky okna deaktivovat Pole název zařízení je vyplněno a zvýrazní se tlačítko deaktivovat.](./media/storsimple-virtual-array-failover-dr/failover1.png)
6. Spustí se deaktivace. Po úspěšném dokončení deaktivace vám obdržíte oznámení.
   
    ![Snímek obrazovky s indikátorem průběhu indikující, že zařízení je deaktivováno.](./media/storsimple-virtual-array-failover-dr/failover2.png)
7. Na stránce zařízení se teď stav zařízení změní na **deaktivovaný**.
    ![Snímek obrazovky se stránkou zařízení. Zobrazí se vlastnosti deaktivovaného zařízení, včetně stavu, který je uvedený jako deaktivovaný.](./media/storsimple-virtual-array-failover-dr/failover3.png)
8. V okně **zařízení** vyberte a klikněte na deaktivované zdrojové zařízení pro převzetí služeb při selhání. 
9. V okně **řídicí panel zařízení** klikněte na **převzít služby při selhání**. 
10. V okně **převzetí služeb při selhání** postupujte takto:
    
    1. Pole zdrojového zařízení se vyplní automaticky. Poznamenejte si celkovou velikost dat pro zdrojové zařízení. Velikost dat by měla být menší než dostupná kapacita na cílovém zařízení. Projděte si podrobnosti související se zdrojovým zařízením, jako je název zařízení, celková kapacita a názvy sdílených složek, u kterých došlo k převzetí služeb při selhání.

    2. V rozevíracím seznamu dostupných zařízení vyberte **cílové zařízení**. V rozevíracím seznamu se zobrazí pouze zařízení s dostatečnou kapacitou.

    3. Ověřte, že **beru na adresu, že tato operace převezme data do cílového zařízení**. 

    4. Klikněte na **převzetí služeb při selhání**.
    
        ![Snímek obrazovky okna pro převzetí služeb při selhání se zdrojovým a cílovým zařízením, zaškrtnutou možností a zvýrazněným tlačítkem pro převzetí služeb při selhání.](./media/storsimple-virtual-array-failover-dr/failover4.png)
11. Úloha převzetí služeb při selhání se inicializuje a obdržíte oznámení. Pokud chcete monitorovat převzetí služeb při selhání, přejdete na **zařízení > úlohy** .
    
     ![Snímek obrazovky s indikátorem průběhu indikující, že zařízení převezme služby při selhání.](./media/storsimple-virtual-array-failover-dr/failover5.png)
12. V okně **úlohy** uvidíte úlohu převzetí služeb při selhání vytvořenou pro zdrojové zařízení. Tato úloha provede předkontroly zotavení po havárii.
    
    ![Snímek obrazovky znázorňující úspěšné spuštění úlohy převzetí služeb při selhání](./media/storsimple-virtual-array-failover-dr/failover6.png)
    
     Po úspěšném dokončení kontroly zotavení po havárii budou úlohy převzetí služeb při selhání zavedeny úlohy obnovení pro každou sdílenou složku nebo svazek, které existují na vašem zdrojovém zařízení.
    
    ![Snímek obrazovky zobrazující podrobnosti úlohy převzetí služeb při selhání, například stav, zařízení a doba trvání.](./media/storsimple-virtual-array-failover-dr/failover7.png)
13. Po dokončení převzetí služeb při selhání přejdete do okna **zařízení** .
    
    1. Vyberte a klikněte na zařízení StorSimple, které se použilo jako cílové zařízení pro proces převzetí služeb při selhání.
    2. Přejít na **nastavení > správa > sdílené složky** (nebo **svazky** , pokud server iSCSI). V okně **akcie** můžete zobrazit všechny sdílené složky (svazky) ze starého zařízení.
        ![Snímek obrazovky okna zařízení Cílové zařízení je uvedené ve stavu online.](./media/storsimple-virtual-array-failover-dr/failover9.png)
14. Budete muset [vytvořit alias DNS](https://web.archive.org/web/20150307000707/http://support.microsoft.com:80/kb/168322) , aby se všechny aplikace, které se snaží připojit, mohly přesměrovat na nové zařízení.

## <a name="errors-during-dr"></a>Chyby během zotavení po havárii

**Výpadek cloudového připojení během zotavení po havárii**

Pokud dojde k přerušení připojení cloudu po spuštění programu DR a před dokončením obnovení zařízení, dojde k selhání nástroje DR. Obdržíte oznámení o selhání. Cílové zařízení pro nástroj DR je označeno jako *nepoužitelné.* Pro budoucí DRs nemůžete použít stejné cílové zařízení.

**Žádná kompatibilní cílová zařízení**

Pokud dostupná cílová zařízení nemají dostatek místa, zobrazí se v důsledku chyby, že nejsou k dispozici žádná kompatibilní cílová zařízení.

**Selhání předkontroly**

Pokud některá z předkontrol není splněná, zobrazí se chyba předkontroly.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Zotavení po havárii pro provozní kontinuitu (BCDR)

Když celé datacentrum Azure přestane fungovat, nastane Scénář zotavení po havárii (BCDR) pro provozní kontinuitu. To může mít vliv na vaši službu StorSimple Správce zařízení a přidružená zařízení StorSimple.

Pokud se StorSimple zařízení, která byla zaregistrována těsně před haváriemi, je možné, že tato zařízení StorSimple bude nutné odstranit. Po havárii můžete tato zařízení znovu vytvořit a nakonfigurovat.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [spravovat virtuální pole StorSimple pomocí místního webového uživatelského rozhraní](storsimple-ova-web-ui-admin.md).