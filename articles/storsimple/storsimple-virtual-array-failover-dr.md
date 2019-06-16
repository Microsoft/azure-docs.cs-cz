---
title: StorSimple Virtual Array po havárii pro obnovení a zařízení převzetí služeb při selhání | Dokumentace Microsoftu
description: Další informace o tom, jak převzetí služeb při selhání StorSimple Virtual Array.
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
ms.openlocfilehash: be3d98dc0b3a8119fb853493440c6fc78d65c5a2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61409540"
---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array-via-azure-portal"></a>Po havárii pro obnovení a zařízení převzetí služeb při selhání pro StorSimple Virtual Array prostřednictvím webu Azure portal

## <a name="overview"></a>Přehled
Tento článek popisuje zotavení po havárii pro Microsoft Azure StorSimple Virtual Array včetně podrobné pokyny k převzetí služeb při selhání do jiné virtuální pole. Převzetí služeb při selhání umožňuje přesun dat z *zdroj* zařízení v datovém centru k *cílové* zařízení. Cílové zařízení mohou být umístěny ve stejné nebo jiné zeměpisné umístění. Převzetí služeb při selhání zařízení je pro celé zařízení. Během převzetí služeb při selhání Cloudová data pro zdrojové zařízení změny vlastnictví, která cílového zařízení.

Tento článek je jenom pro virtuální pole StorSimple. Převzetí služeb při selhání zařízením řady 8000, přejděte na [zařízení převzetí služeb při selhání a zotavení po havárii vašeho zařízení StorSimple](storsimple-device-failover-disaster-recovery.md).

## <a name="what-is-disaster-recovery-and-device-failover"></a>Co je po havárii pro obnovení a zařízení převzetí služeb při selhání?

V případě zotavení po havárii v primárním zařízení přestane fungovat. V tomto scénáři můžete přesunout Cloudová data přidružená k zařízení se nezdařilo na jiné zařízení. Můžete použít jako primární zařízení *zdroj* a zadejte jiné zařízení, jako *cílové*. Tento proces se označuje jako *převzetí služeb při selhání*. Během převzetí služeb při selhání všechny svazky nebo sdílené složky ze zdrojového zařízení změní vlastnictví a jsou přeneseny do cílového zařízení. Žádné filtrování dat je povolené.

Zotavení po Havárii je modelovaná jako plně obnovení pomocí heat mapy – na základě ovládání datových vrstev a sledování. Heat mapa je definována přiřazení hodnoty heat dat založených na čtení a zápisu vzorce. Tato heat mapy pak úrovně nejnižší bloků dat heat do cloudu nejprve přitom tyto datové bloky vysokou heat (nejpoužívanější) u místní vrstvy. StorSimple během zotavení po Havárii využívá heat mapa dosazení dat z cloudu a obnovení. Zařízení načte všechny svazky nebo sdílené složky v poslední poslední zálohu (počítáno interně) a provádí obnovení z této zálohy. Virtuální pole orchestruje celý proces zotavení po Havárii.

> [!IMPORTANT]
> Na konci převzetí služeb při selhání zařízení se odstraní zdrojové zařízení a proto není podporována navrácení služeb po obnovení.
> 
> 

Zotavení po havárii je orchestrované prostřednictvím funkce převzetí služeb při selhání zařízení a je inicializováno z **zařízení** okno. V tomto okně podporován všechna zařízení StorSimple připojené k vaší službě Správce zařízení StorSimple. Pro každé zařízení se zobrazí popisný název, stav, zřízená a maximální kapacitu, typ a model.

## <a name="prerequisites-for-device-failover"></a>Předpoklady pro převzetí služeb při selhání zařízení

### <a name="prerequisites"></a>Požadavky

Zařízení převzetí služeb při selhání Ujistěte se, že jsou splněné následující požadavky:

* Zdrojové zařízení musí být v **deaktivovány** stavu.
* Cílové zařízení je potřeba se zobrazují jako **připraveno k nastavení** na webu Azure Portal. Zřízení virtuálního pole Cíl kapacity stejné nebo vyšší. Pomocí místního webového uživatelského rozhraní můžete nakonfigurovat a úspěšně zaregistrovat virtuální pole cíl.
  
  > [!IMPORTANT]
  > Nepokoušejte se konfigurovat registrované virtuálního zařízení prostřednictvím služby. Žádná konfigurace zařízení je třeba provést prostřednictvím služby.
  > 
  > 
* Cílové zařízení nemůže mít stejný název jako zdrojové zařízení.
* Zdrojové a cílové zařízení musí být stejného typu. Pouze můžete převzít služby virtuálního pole nakonfigurovali jako souborový server do jiného souborového serveru. Totéž platí pro iSCSI server.
* Pro souborový server se zotavení po Havárii doporučujeme připojit cílové zařízení ke stejné doméně jako zdroj. Tato konfigurace zajistí, že jsou oprávnění ke sdíleným složkám automaticky vyřešeny. Pouze převzetí služeb při selhání na cílové zařízení ve stejné doméně.
* Dostupné cílové zařízení pro zotavení po Havárii jsou zařízení, která mají stejné nebo větší kapacitě ve srovnání s zdrojové zařízení. Zařízení, které jsou připojené k vaší službě, ale nesplňují kritéria dostatek místa nejsou k dispozici jako cílová zařízení.

### <a name="other-considerations"></a>Další důležité informace

* Plánované převzetí služeb při selhání 
  
  * Doporučujeme provést na zdrojové zařízení do režimu offline všechny svazky nebo sdílené složky.
  * Doporučujeme vytvořit zálohu zařízení a pak pokračujte převzetí služeb při selhání Chcete-li minimalizovat ztrátu dat. 
* Neplánované převzetí služeb při selhání zařízení použije nejnovější zálohy obnovit data.

### <a name="device-failover-prechecks"></a>Prechecks převzetí služeb při selhání zařízení

Před zahájením zotavení po Havárii, zařízení provede prechecks. Tyto kontroly zajistit, že se nevyskytnou žádné chyby při zotavení po Havárii začíná. Prechecks patří:

* Ověřuje se účet úložiště.
* Kontroluje se dostupnost připojení cloudu do Azure.
* Kontrola dostupného místa na cílové zařízení.
* Kontroluje, jestli má zařízení jednotce zdrojový server iSCSI
  
  * platné názvy služby ACR.
  * platný identifikátor IQN (ne vyšší než 220 znaků).
  * platný hesla CHAP (12 – 16 znaků).

Pokud některý z předchozí prechecks selhání, nemůžete pokračovat zotavení po Havárii. Řešení těchto problémů a pak zkuste zotavení po Havárii.

Po úspěšném dokončení zotavení po Havárii je převedeno vlastnictví Cloudová data na zdrojovém zařízení na cílové zařízení. Zdrojové zařízení je pak už nebude k dispozici na portálu. Zablokovaný přístup do všech svazků nebo sdílených složek na zdrojovém zařízení a cílové zařízení stane aktivním.

> [!IMPORTANT]
> I když zařízení už nejsou k dispozici, je virtuální počítač, který jste zřídili v hostitelském systému nadále spotřebovávají prostředky. Jakmile zotavení po Havárii se úspěšně dokončí, odstraníte tento virtuální počítač z vašeho hostitelského systému.
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>Převzetí služeb při selhání pro virtuální pole

Doporučujeme, abyste zřizovat, konfigurovat a zaregistrovat další StorSimple Virtual Array ve vaší službě Správce zařízení StorSimple před spuštěním této procedury.

> [!IMPORTANT]
> 
> * Nelze převezme služby při selhání ze zařízení řady StorSimple 8000 1200 virtuálního zařízení.
> * Vám může převzetí služeb při selhání z virtuálního zařízení federální informace o zpracování Standard (FIPS) povolena na jiné zařízení FIPS povolené a nasazené na portálu Government – podle standardu FIPS zařízení.


Proveďte následující kroky a obnovit zařízení do cílové virtuální zařízení StorSimple.

1. Zřídíte a nakonfigurujete cílové zařízení, která splňuje [předpoklady pro převzetí služeb při selhání zařízení](#prerequisites). Dokončete konfiguraci zařízení prostřednictvím místního webového uživatelského rozhraní a zaregistrujte ho do služby Správce zařízení StorSimple. Při vytváření souborového serveru, přejděte ke kroku 1 tohoto [nastavení jako souborového serveru se](storsimple-virtual-array-deploy3-fs-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device). Při vytváření serveru iSCSI, přejděte ke kroku 1 tohoto [nastavení jako serveru iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device).

2. Využijte svazky nebo sdílené složky offline na hostiteli. Uvedení svazky nebo sdílené složky do režimu offline, použijte pokyny specifické pro operační systém hostitele. Pokud již offline, budete muset provést všechny svazky nebo sdílené složky do režimu offline v zařízení následujícím způsobem.
   
    1. Přejděte na **zařízení** okna a vyberte své zařízení.
   
    2. Přejděte na **Nastavení > Správa > sdílené složky** (nebo **Nastavení > Správa > svazky**). 
   
    3. Vyberte sdílené složky nebo svazku, klikněte pravým tlačítkem myši a vyberte **převést do režimu offline**. 
   
    4. Po zobrazení výzvy k potvrzení zkontrolujte **beru na vědomí následky převodu této sdílené složky do režimu offline.** 
   
    5. Klikněte na tlačítko **převést do režimu offline**.

3. Přejděte ve službě Správce zařízení StorSimple **správy > zařízení**. V **zařízení** okně vyberte a klikněte na zdrojové zařízení.

4. Ve vaší **řídicího panelu zařízení** okna, klikněte na tlačítko **deaktivovat**.

5. V **deaktivovat** okně se zobrazí výzva k potvrzení. Deaktivace zařízení je *trvalé* proces, který není možné vrátit zpět. Budete také upozorněni, umožní vaší sdílené složky nebo svazky na hostiteli. Zadejte název zařízení a potvrďte a klikněte na tlačítko **deaktivovat**.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover1.png)
6. Deaktivaci spustí. Zobrazí se oznámení a po deaktivaci se úspěšně dokončila.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover2.png)
7. Na stránce zařízení, stavu zařízení se teď změní na **deaktivovány**.
    ![](./media/storsimple-virtual-array-failover-dr/failover3.png)
8. V **zařízení** okně vyberte a klikněte na tlačítko deaktivované zdrojové zařízení pro převzetí služeb při selhání. 
9. V **řídicího panelu zařízení** okna, klikněte na tlačítko **převzetí služeb při selhání**. 
10. V **převzetí služeb při selhání zařízení** okno, postupujte takto:
    
    1. Zdrojové zařízení pole se vyplní automaticky. Všimněte si celkové velikosti dat pro zdrojové zařízení. Velikost dat musí být menší než na cílovém zařízení dostupná kapacita. Zkontrolujte podrobnosti přidružené zdrojové zařízení, jako je například název zařízení, celková kapacita a názvy sdílených složek, které jsou převzetí služeb při selhání.

    2. Z rozevíracího seznamu dostupných zařízení, zvolte **cílové zařízení**. V rozevíracím seznamu se zobrazují jenom zařízení, které mají dostatečnou kapacitu.

    3. Zkontrolujte, že **beru na vědomí, že tato operace převede data na cílové zařízení**. 

    4. Klikněte na tlačítko **převzetí služeb při selhání**.
    
        ![](./media/storsimple-virtual-array-failover-dr/failover4.png)
11. Spustí úlohu převzetí služeb při selhání a dostanete oznámení. Přejděte na **zařízení > úlohy** monitorovat převzetí služeb při selhání.
    
     ![](./media/storsimple-virtual-array-failover-dr/failover5.png)
12. V **úlohy** okně uvidíte převzetí služeb při selhání úlohy vytvořené pro zdrojové zařízení. Tato úloha provádí prechecks zotavení po Havárii.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover6.png)
    
     Po úspěšném prechecks zotavení po Havárii úlohy převzetí služeb při selhání se vytvořit podřízený úlohy obnovení pro každé sdílené složky nebo svazku, která existuje na zdrojové zařízení.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover7.png)
13. Po dokončení převzetí služeb, přejděte **zařízení** okno.
    
    1. Vyberte a klikněte na zařízení StorSimple, která byla použita jako cílové zařízení pro proces převzetí služeb při selhání.
    2. Přejděte na **Nastavení > Správa > sdílené složky** (nebo **svazky** Pokud iSCSI server). V **sdílené složky** okně můžete zobrazit všechny sdílené složky (svazky) ze staré zařízení.
        ![](./media/storsimple-virtual-array-failover-dr/failover9.png)
14. Budete muset [vytvoření aliasu DNS](https://support.microsoft.com/kb/168322) tak, aby všechny aplikace, které se pokoušíte připojit, může získat přesměrována na nové zařízení.

## <a name="errors-during-dr"></a>Chyby při zotavení po Havárii

**Cloudové připojení výpadek během zotavení po Havárii**

Pokud po zahájení zotavení po Havárii, než bude zařízení obnovení je dokončeno, naruší se připojení ke cloudu, zotavení po Havárii se nezdaří. Obdržíte oznámení o selhání. Cílové zařízení pro zotavení po Havárii je označen jako *nepoužitelné.* Stejné cílové zařízení nelze použít pro budoucí DRs.

**Žádné kompatibilní cílové zařízení**

Pokud k dispozici cílová zařízení nemají dostatek místa, se zobrazí chyba o tom, že neexistují žádné kompatibilní cílové zařízení.

**Precheck selhání**

Pokud není splněna jedna z prechecks, se zobrazí precheck chyby.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Obchodní kontinuity podnikových procesů zotavení po havárii (BCDR)

O obchodní kontinuity podnikových procesů zotavení po havárii (BCDR) nastane, pokud celé datové centrum Azure přestane fungovat. To může ovlivnit služby Správce zařízení StorSimple a jeho přiřazená zařízení StorSimple.

Pokud je zařízení StorSimple, které jste zaregistrovali těsně před došlo k havárii, tato zařízení StorSimple možná muset odstranit. Po havárii můžete znovu vytvořit a nakonfigurovat tato zařízení.

## <a name="next-steps"></a>Další postup

Další informace o tom, jak [spravovat StorSimple Virtual Array pomocí místního webového uživatelského rozhraní](storsimple-ova-web-ui-admin.md).

