---
title: Obnovení služby Při selhání a zotavení po havárii pro virtuální pole StorSimple
description: Přečtěte si další informace o tom, jak převzetí služeb při selhání virtuálního pole StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 3c1f9c62-af57-4634-a0d8-435522d969aa
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 464fa05f658dd6e6e25d79f8840ceeb939383149
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77467211"
---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array-via-azure-portal"></a>Zotavení po havárii a převzetí služeb při selhání zařízení StorSimple Virtual Array prostřednictvím webu Azure Portal

## <a name="overview"></a>Přehled
Tento článek popisuje zotavení po havárii pro vaše Virtuální pole Microsoft Azure StorSimple, včetně podrobných kroků převzetí služeb při selhání do jiného virtuálního pole. Převzetí služeb při selhání umožňuje přesunout data ze *zdrojového* zařízení v datovém centru do *cílového* zařízení. Cílové zařízení může být umístěno ve stejné nebo jiné zeměpisné poloze. Převzetí služeb při selhání zařízení je pro celé zařízení. Během převzetí služeb při selhání změní cloudová data pro zdrojové zařízení vlastnictví na vlastnictví cílového zařízení.

Tento článek je použitelný pouze pro virtuální pole StorSimple. Chcete-li přepojit zařízení řady 8000, přejděte na [obnovení služeb při selhání zařízení a zotavení po havárii zařízení StorSimple](storsimple-device-failover-disaster-recovery.md).

## <a name="what-is-disaster-recovery-and-device-failover"></a>Co je zotavení po havárii a převzetí služeb při selhání zařízení?

Ve scénáři zotavení po havárii (DR) primární zařízení přestane fungovat. V tomto scénáři můžete přesunout cloudová data přidružená k selhání zařízení do jiného zařízení. Jako *zdroj* můžete použít primární zařízení a jako *cíl*zadat jiné zařízení . Tento proces se označuje jako *převzetí služeb při selhání*. Během převzetí služeb při selhání změní vlastnictví všechny svazky nebo sdílené složky ze zdrojového zařízení a přenesou se do cílového zařízení. Filtrování dat není povoleno.

Zotavení po havárii je modelováno jako úplné obnovení zařízení pomocí vrstvení a sledování založené na teplotní mapě. Tepelná mapa je definována přiřazením hodnoty tepla k datům založeným na vzorcích čtení a zápisu. Tato tepelná mapa pak nejprve vrství nejnižší bloky dat tepla do cloudu při zachování bloků dat s vysokým teplem (nejpoužívanější) v místní vrstvě. Během zotavení po havárii StorSimple používá mapu tepla k obnovení a rehydrataci dat z cloudu. Zařízení načte všechny svazky nebo sdílené složky v poslední poslední zálohě (jak je stanoveno interně) a provede obnovení z této zálohy. Virtuální pole orchestruje celý proces zotavení po havárii.

> [!IMPORTANT]
> Zdrojové zařízení se odstraní na konci převzetí služeb při selhání zařízení, a proto není podporováno navrácení služeb po selhání.
> 
> 

Zotavení po havárii je řízeno prostřednictvím funkce převzetí služeb při selhání zařízení a je iniciováno z okna **Zařízení.** Toto okno tabulkouje všechna zařízení StorSimple připojená ke službě Správce zařízení StorSimple. Pro každé zařízení můžete zobrazit popisný název, stav, zřízenou a maximální kapacitu, typ a model.

## <a name="prerequisites-for-device-failover"></a>Požadavky na převzetí služeb při selhání zařízení

### <a name="prerequisites"></a>Požadavky

U převzetí služeb při selhání zařízení se ujistěte, že jsou splněny následující požadavky:

* Zdrojové zařízení musí být v **deaktivovaném** stavu.
* Cílové zařízení se musí zobrazit jako **připravené k nastavení** na webu Azure Portal. Zřizování cílovévirtuální pole stejné nebo vyšší kapacity. Pomocí místního webového uživatelského rozhraní nakonfigurujte a úspěšně zaregistrujte cílové virtuální pole.
  
  > [!IMPORTANT]
  > Nepokoušejte se konfigurovat registrované virtuální zařízení prostřednictvím služby. Prostřednictvím služby by neměla být provedena žádná konfigurace zařízení.
  > 
  > 
* Cílové zařízení nemůže mít stejný název jako zdrojové zařízení.
* Zdrojové a cílové zařízení musí být stejného typu. Můžete pouze převzetí služeb při selhání virtuálního pole nakonfigurovaného jako souborový server na jiném souborovém serveru. Totéž platí pro server iSCSI.
* Pro souborový server DR doporučujeme připojit cílové zařízení do stejné domény jako zdroj. Tato konfigurace zajišťuje automatické vyřešení oprávnění ke sdílení. Je podporováno pouze převzetí služeb při selhání cílovému zařízení ve stejné doméně.
* Dostupná cílová zařízení pro zotavení po havárii jsou zařízení, která mají stejnou nebo větší kapacitu ve srovnání se zdrojovým zařízením. Zařízení, která jsou připojena k vaší službě, ale nesplňují kritéria dostatečného místa, nejsou k dispozici jako cílová zařízení.

### <a name="other-considerations"></a>Další aspekty

* Pro plánované převzetí služeb při selhání:
  
  * Doporučujeme převzít všechny svazky nebo sdílené složky na zdrojovém zařízení offline.
  * Doporučujeme provést zálohu zařízení a potom pokračovat převzetí služeb při selhání minimalizovat ztrátu dat.
* Pro neplánované převzetí služeb při selhání zařízení používá nejnovější zálohu k obnovení dat.

### <a name="device-failover-prechecks"></a>Kontroly převzetí služeb při selhání zařízení

Před zahájením zotavení po havárii zařízení provede předběžné kontroly. Tyto kontroly pomáhají zajistit, že při zahájení zotavení po havárii nedojde k žádným chybám. Předběžné kontroly zahrnují:

* Ověření účtu úložiště.
* Kontrola připojení ke cloudu k Azure.
* Kontrola dostupného místa na cílovém zařízení.
* Kontrola, zda byl ke zdroji zdrojového svazku serveru iSCSI
  
  * platné názvy ACR.
  * iqn (nepřesahující 220 znaků).
  * hesla CHAP (délka 12-16 znaků).

Pokud některá z předchozích předběžných kontrol selže, nelze pokračovat v zotavení po havárii. Vyřešte tyto problémy a opakujte družku.

Po úspěšném dokončení zotavení po Havárii se vlastnictví cloudových dat na zdrojovém zařízení přenese do cílového zařízení. Zdrojové zařízení pak již není k dispozici na portálu. Přístup ke všem svazkům/sdíleným položkám ve zdrojovém zařízení je blokován a cílové zařízení se aktivuje.

> [!IMPORTANT]
> I když zařízení již není k dispozici, virtuální počítač, který jste zřídit v hostitelském systému stále spotřebovává prostředky. Po úspěšném dokončení zotavení po havárii můžete tento virtuální počítač odstranit z hostitelského systému.
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>Převzetí služeb při selhání do virtuálního pole

Doporučujeme zřídit, nakonfigurovat a zaregistrovat jiné virtuální pole StorSimple u služby StorSimple Device Manager před spuštěním tohoto postupu.

> [!IMPORTANT]
> 
> * Nelze převzetí služeb při selhání ze zařízení řady StorSimple 8000 na virtuální zařízení 1200.
> * Můžete převzetí služeb při selhání z federálního standardu zpracování informací (FIPS) povolené ho virtuálnízařízení na jiné zařízení s povoleno u FIPS nebo na jiné než FIPS zařízení nasazené na portálu pro správu.


Chcete-li zařízení obnovit do cílového virtuálního zařízení StorSimple, proveďte následující kroky.

1. Zřídit a nakonfigurovat cílové zařízení, které splňuje [požadavky pro převzetí služeb při selhání zařízení](#prerequisites). Dokončete konfiguraci zařízení prostřednictvím místního webového uživatelského rozhraní a zaregistrujte jej do služby Správce zařízení StorSimple. Pokud vytváříte souborový server, přejděte ke kroku 1 [nastavení jako souborového serveru](storsimple-virtual-array-deploy3-fs-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device). Pokud vytváříte server iSCSI, přejděte ke kroku 1 [nastavení jako server iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device).

2. Převedení svazků/sdílených složek do offline na hostiteli. Chcete-li převést svazky nebo sdílené složky do režimu offline, přečtěte si pokyny pro hostitele specifické pro operační systém. Pokud již nejste offline, musíte všechny svazky/sdílené složky v zařízení převést do offline akce následujícím způsobem.
   
    1. Přejděte do okna **Zařízení** a vyberte zařízení.
   
    2. Přejděte **na Nastavení > Správa sdílených > (nebo** Nastavení > Správa > **svazků**). 
   
    3. Vyberte sdílenou složku nebo svazek, klikněte pravým tlačítkem myši a vyberte **Převzít offline**. 
   
    4. Po zobrazení výzvy k potvrzení zkontrolujte, zda **chápu dopad offline převedení této sdílené položky do ústraní.** 
   
    5. Klepněte na **tlačítko Přejít do offline**.

3. Ve službě Správce zařízení StorSimple přejděte na **způsob správy > zařízení**. V okně **Zařízení** vyberte zdrojové zařízení a klikněte na něj.

4. V okně **řídicího panelu zařízení** klepněte na **tlačítko Deaktivovat**.

5. V okně **Deaktivovat** se zobrazí výzva k potvrzení. Deaktivace zařízení je *trvalý* proces, který nelze vrátit zpět. Také se vám připomene, abyste své sdílené složky/svazky přeřadili do offline linky na hostiteli. Zadejte název zařízení, který potvrďte, a klepněte na tlačítko **Deaktivovat**.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover1.png)
6. Spustí se deaktivace. Po úspěšném dokončení deaktivace obdržíte oznámení.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover2.png)
7. Na stránce Zařízení se nyní stav zařízení změní na **Deaktivovaný**.
    ![](./media/storsimple-virtual-array-failover-dr/failover3.png)
8. V okně **Zařízení** vyberte a klikněte na deaktivované zdrojové zařízení pro převzetí služeb při selhání. 
9. V okně **Řídicí panel zařízení** klepněte na příkaz Převzetí služeb při **selhání**. 
10. V okně **Převzetí služeb při selhání zařízení** postupujte takto:
    
    1. Pole zdrojového zařízení je automaticky vyplněno. Poznamenejte si celkovou velikost dat pro zdrojové zařízení. Velikost dat by měla být menší než dostupná kapacita na cílovém zařízení. Zkontrolujte podrobnosti přidružené ke zdrojovému zařízení, jako je název zařízení, celková kapacita a názvy sdílených složek, které jsou převzetím služby při selhání.

    2. V rozevíracím seznamu dostupných zařízení zvolte **cílové zařízení**. V rozevíracím seznamu se zobrazí pouze zařízení s dostatečnou kapacitou.

    3. Zkontrolujte, zda **rozumím tomu, že tato operace převezme převzetí služeb při selhání dat do cílového zařízení**. 

    4. Klepněte na tlačítko **Převzetí služeb při selhání**.
    
        ![](./media/storsimple-virtual-array-failover-dr/failover4.png)
11. Úloha převzetí služeb při selhání se inicializuje a obdržíte oznámení. Chcete-li sledovat převzetí služeb při selhání, přejděte na **zařízení > úlohy.**
    
     ![](./media/storsimple-virtual-array-failover-dr/failover5.png)
12. V okně **Úlohy** se zobrazí úloha převzetí služeb při selhání vytvořená pro zdrojové zařízení. Tato úloha provádí předběžné kontroly zotavení po havárii.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover6.png)
    
     Po předběžné kontroly zotavení po havárii jsou úspěšné, úloha převzetí služeb při selhání se vytvoří obnovit úlohy pro každou sdílenou složku nebo svazek, který existuje ve zdrojovém zařízení.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover7.png)
13. Po dokončení převzetí služeb při selhání přejděte do okna **Zařízení.**
    
    1. Vyberte a klikněte na zařízení StorSimple, které bylo použito jako cílové zařízení pro proces převzetí služeb při selhání.
    2. Přejděte na **Nastavení > správa > sdílených složek** (nebo **svazky,** pokud server iSCSI). V okně **Sdílené** položky můžete zobrazit všechny sdílené složky (svazky) ze starého zařízení.
        ![](./media/storsimple-virtual-array-failover-dr/failover9.png)
14. Budete muset [vytvořit alias DNS,](https://support.microsoft.com/kb/168322) aby všechny aplikace, které se pokoušejí připojit, mohly být přesměrovány na nové zařízení.

## <a name="errors-during-dr"></a>Chyby během zotavení po havárii

**Výpadek připojení ke cloudu během zotavení po havárii**

Pokud je připojení ke cloudu přerušeno po spuštění zotavení po havárii a před dokončením obnovení zařízení se zotavení po havárii nezdaří. Obdržíte oznámení o selhání. Cílové zařízení pro zotavení po havárii je označeno jako *nepoužitelné.* Pro budoucí dre nelze použít stejné cílové zařízení.

**Žádná kompatibilní cílová zařízení**

Pokud dostupná cílová zařízení nemají dostatek místa, zobrazí se chyba v tom smyslu, že neexistují žádná kompatibilní cílová zařízení.

**Chyby předběžné kontroly**

Pokud jedna z předběžných kontrol není splněna, zobrazí se chyby předběžné kontroly.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Zotavení po havárii kontinuity provozu (BCDR)

Scénář zotavení po havárii kontinuity podnikání (BCDR) nastane, když přestane fungovat celé datové centrum Azure. To může ovlivnit službu StorSimple Device Manager a přidružená zařízení StorSimple.

Pokud existují StorSimple zařízení, která byla registrována těsně před došlo k havárii, pak tyto StorSimple zařízení může být nutné odstranit. Po havárii můžete tato zařízení znovu vytvořit a nakonfigurovat.

## <a name="next-steps"></a>Další kroky

Další informace o [správě virtuálního pole StorSimple pomocí místního webového uživatelského prostředí](storsimple-ova-web-ui-admin.md).

