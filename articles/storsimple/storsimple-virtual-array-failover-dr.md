---
title: Pole virtuální zařízení StorSimple po havárii obnovení a zařízení převzetí služeb při selhání | Microsoft Docs
description: Další informace o tom, jak převzetí služeb při selhání pole virtuální zařízení StorSimple.
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
ms.openlocfilehash: 12079f8dbc409afe5acc274fa08bda878c90b76e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23927765"
---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array-via-azure-portal"></a>Po havárii obnovení a zařízení převzetí služeb při selhání pro virtuální zařízení StorSimple pole prostřednictvím portálu Azure

## <a name="overview"></a>Přehled
Tento článek popisuje zotavení po havárii pro Microsoft Azure StorSimple virtuální pole včetně podrobné kroky k převzetí služeb při selhání na jiný virtuální pole. Převzetí služeb při selhání umožňuje přesun dat z *zdroj* zařízení v datovém centru k *cílový* zařízení. Cílové zařízení může nacházet ve stejné nebo jiné zeměpisné umístění. Převzetí služeb při selhání zařízení je ze zařízení. Během převzetí služeb při selhání Cloudová data zdrojového zařízení změny vlastnictví u cílové zařízení.

V tomto článku se vztahuje na pole virtuální zařízení StorSimple pouze. Zařízení se systémem řady 8000 převzít, přejděte na [zařízení převzetí služeb při selhání a zotavení po havárii zařízení StorSimple](storsimple-device-failover-disaster-recovery.md).

## <a name="what-is-disaster-recovery-and-device-failover"></a>Co je po havárii obnovení a zařízení převzetí služeb při selhání?

Ve scénáři zotavení po havárii primární zařízení přestane fungovat. V tomto scénáři můžete přesunout data cloudu přidružené k selhání zařízení na jiné zařízení. Můžete použít jako primární zařízení *zdroj* a zadejte jiné zařízení, jako *cíl*. Tento proces se označuje jako *převzetí služeb při selhání*. Během převzetí služeb při selhání všechny svazky nebo sdílené složky ze zdrojového zařízení změnit vlastnictví a jsou přeneseny na cílovém zařízení. Žádné filtrování dat je povolen.

Zotavení po Havárii je modelovaná jako obnovení úplné zařízení pomocí heat mapa – na základě vrstvení a sledování. Heat mapa je definována přiřazením heat hodnotu data v závislosti na čtení a zápis vzory. Tento heat mapovat pak vrstev nejnižší bloky dat heat cloudu nejprve zachováním bloky dat vysoké heat (většina používá) u místní vrstvy v. Během zotavení po Havárii používá StorSimple heat mapa při obnovení a rehydrataci při spotřebě data z cloudu. Zařízení načte všechny svazky nebo sdílené složky v poslední poslední zálohu (jak stanoví interně) a provádí obnovení z této zálohy. Pole virtuální orchestruje celý proces zotavení po Havárii.

> [!IMPORTANT]
> Zdrojového zařízení se odstraní na konci tohoto zařízení převzetí služeb při selhání, a proto není podporována navrácení služeb po obnovení.
> 
> 

Zotavení po havárii je pomocí funkce převzetí služeb při selhání zařízení orchestrovat a je zahájeno z **zařízení** okno. Toto okno podporován všechna zařízení StorSimple připojené k vaší službě StorSimple Manager zařízení. Pro každé zařízení se zobrazí popisný název, stav, zřízený a maximální kapacitu, typ a modelu.

## <a name="prerequisites-for-device-failover"></a>Předpoklady pro převzetí služeb při selhání zařízení

### <a name="prerequisites"></a>Požadavky

Zařízení převzetí služeb při selhání Ujistěte se, že jsou splněny následující požadavky:

* Zdrojového zařízení musí být ve **deaktivováno** stavu.
* Cílové zařízení musí zobrazují jako **připravení nastavit** na portálu Azure. Zřídit virtuální cílového pole kapacity stejná nebo vyšší. Použijte místní webového uživatelského rozhraní konfigurace a registrace úspěšně virtuální cílového pole.
  
  > [!IMPORTANT]
  > Nepokoušejte se konfigurovat registrované virtuálního zařízení prostřednictvím služby. Žádná konfigurace zařízení je třeba provést prostřednictvím služby.
  > 
  > 
* Cílové zařízení nemůže mít stejný název jako zdrojového zařízení.
* Zdrojové a cílové zařízení musí být stejného typu. Pouze můžete převzít virtuální pole nakonfigurovaný jako souborový server do jiného souborového serveru. Totéž platí pro server se službou iSCSI.
* Pro souborový server zotavení po Havárii doporučujeme, abyste do stejné domény jako zdroj připojení k cílové zařízení. Tato konfigurace zajistí, že oprávnění ke sdílení automaticky vyřešeny. Pouze převzetí služeb při selhání na cílovém zařízení ve stejné doméně.
* Dostupných cílových zařízení pro zotavení po Havárii jsou zařízení, která mají stejné nebo větší kapacitě ve srovnání s zdrojového zařízení. Zařízení, která jsou připojena k službě ale nesplňují kritéria dostatek místa nejsou k dispozici jako cílová zařízení.

### <a name="other-considerations"></a>Další důležité informace

* Plánované převzetí služeb při selhání 
  
  * Doporučujeme provést všechny svazky nebo sdílené složky na zdrojového zařízení do offline režimu.
  * Doporučujeme provést zálohu zařízení a poté účelem minimalizace ztráty dat pokračovat převzetí služeb při selhání. 
* Neplánované převzetí služeb při selhání zařízení používá nejnovější zálohy obnovit data.

### <a name="device-failover-prechecks"></a>Zařízení prechecks převzetí služeb při selhání

Před zahájením zotavení po Havárii, provede zařízení prechecks. Těmito kontrolami pomáhají zajistit, že nevyskytnou žádné chyby při zahájí zotavení po Havárii. Prechecks patří:

* Probíhá ověřování účtu úložiště.
* Kontroluje se připojení cloudu do Azure.
* Kontrola dostupného místa na cílové zařízení.
* Kontrola, zda má svazek iSCSI zdrojového serveru zařízení
  
  * platné názvy ACR.
  * Neplatný název IQN (ne vyšší než 220 znaků).
  * platný hesla CHAP (12 až 16 znaků).

Pokud některé z předchozí prechecks selhání, nemůže pokračovat s zotavení po Havárii. Tyto potíže odstranit a poté opakujte zotavení po Havárii.

Po úspěšném dokončení zotavení po Havárii, vlastnictví data cloudu na zdrojového zařízení se přenese na cílové zařízení. Zdrojového zařízení je pak už k dispozici na portálu. Je blokován přístup na všechny svazky nebo sdílené složky v zařízení zdrojové a cílové zařízení stane aktivní.

> [!IMPORTANT]
> Když zařízení již není k dispozici, je virtuální počítač, který jste zřídili v hostitelském systému nadále spotřebovávají prostředky. Po úspěšném dokončení zotavení po Havárii můžete odstranit tento virtuální počítač z vašeho systému hostitele.
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>Převzetí služeb při selhání virtuální pole

Doporučujeme zřídit, nakonfigurovat a zaregistrovat další pole virtuální zařízení StorSimple pomocí služby StorSimple Manager zařízení před spuštěním tohoto postupu.

> [!IMPORTANT]
> 
> * Můžete nelze převzetí služeb při selhání ze zařízení řady StorSimple 8000 1200 virtuálního zařízení.
> * Můžete převzít služby z virtuálního zařízení informace o zpracování Standard FIPS (Federal) povolena na jiné zařízení FIPS povolené a nasazené na portálu Government zařízení standardu FIPS.


Proveďte následující kroky a v zařízení obnovit do cílového virtuálního zařízení StorSimple.

1. Zřídíte a nakonfigurujete cílového zařízení, která splňuje [požadavky pro převzetí služeb při selhání zařízení](#prerequisites). Dokončení konfigurace zařízení prostřednictvím místního webového uživatelského rozhraní a zaregistrovat ji do služby StorSimple Manager zařízení. Pokud vytváříte souborový server, přejděte ke kroku 1 tohoto [nastavit jako souborový server](storsimple-virtual-array-deploy3-fs-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device). Pokud vytváříte serveru iSCSI, přejděte ke kroku 1 tohoto [nastavený jako iSCSI server](storsimple-virtual-array-deploy3-iscsi-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device).

2. Trvat svazky nebo sdílené složky do offline režimu na hostiteli. Uvedení svazky sdílené složky do režimu offline, použijte pokyny specifické pro operační systém pro hostitele. Pokud již offline, budete muset provést všechny svazky nebo sdílené složky do režimu offline v zařízení pomocí následujícího postupu.
   
    1. Přejděte na **zařízení** okno a vybrat zařízení.
   
    2. Přejděte na **Nastavení > Správa > sdílené složky** (nebo **Nastavení > Správa > svazky**). 
   
    3. Vyberte sdílené složky nebo svazek, klikněte pravým tlačítkem a vyberte **převést do režimu offline**. 
   
    4. Po zobrazení výzvy k potvrzení, zkontrolujte **beru na vědomí dopad této sdílené složce přepnutím do režimu offline.** 
   
    5. Klikněte na tlačítko **převést do režimu offline**.

3. Přejděte ve službě StorSimple Manager zařízení **správy > zařízení**. V **zařízení** okně vyberte a klikněte na vaše zdrojové zařízení.

4. Ve vaší **řídicí panel zařízení** okně klikněte na tlačítko **deaktivovat**.

5. V **deaktivovat** okně se zobrazí výzva k potvrzení. Deaktivace zařízení je *trvalé* proces, který nelze vrátit zpět. Zobrazí se upozornění, také provést sdílených složek nebo svazků do offline režimu na hostiteli. Zadejte název zařízení a potvrďte a klikněte na **deaktivovat**.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover1.png)
6. Deaktivaci spustí. Po úspěšném dokončení deaktivaci obdržíte oznámení.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover2.png)
7. Na stránce zařízení stavu zařízení se teď změní na **deaktivováno**.
    ![](./media/storsimple-virtual-array-failover-dr/failover3.png)
8. V **zařízení** okně vyberte a klikněte na zařízení deaktivované zdroje pro převzetí služeb při selhání. 
9. V **řídicí panel zařízení** okně klikněte na tlačítko **převzetí služeb při selhání**. 
10. V **převzít zařízení** okno, postupujte takto:
    
    1. Pole zdrojového zařízení se automaticky vyplní. Poznámka: celkové velikosti dat pro zdrojového zařízení. Velikost dat musí být menší než dostupné kapacity na cílovém zařízení. Zkontrolujte podrobnosti přidružené k zařízení zdroj, například název zařízení, celková kapacita a názvy sdílených složek, které jsou převzetí služeb při selhání.

    2. Z rozevíracího seznamu dostupných zařízení, vyberte **cílové zařízení**. V rozevíracím seznamu se zobrazí jenom zařízení, které mají dostatečnou kapacitu.

    3. Zkontrolujte, zda **beru na vědomí, že tato operace bude převzít data do cílové zařízení**. 

    4. Klikněte na tlačítko **převzetí služeb při selhání**.
    
        ![](./media/storsimple-virtual-array-failover-dr/failover4.png)
11. Spustí úlohu převzetí služeb při selhání a přijímat oznámení. Přejděte na **zařízení > úlohy** monitorování převzetí služeb při selhání.
    
     ![](./media/storsimple-virtual-array-failover-dr/failover5.png)
12. V **úlohy** okně uvidíte převzetí služeb při selhání úlohy vytvořené pro zdrojového zařízení. Tato úloha provede prechecks zotavení po Havárii.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover6.png)
    
     Po úspěšné prechecks zotavení po Havárii se bude spawn úlohu převzetí služeb při selhání úlohy obnovení pro každou sdílenou složku nebo svazek, který existuje na vaše zdrojové zařízení.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover7.png)
13. Po dokončení převzetí přejít na **zařízení** okno.
    
    1. Vyberte a klikněte na zařízení StorSimple, která byla použita jako cílové zařízení pro proces převzetí služeb při selhání.
    2. Přejděte na **Nastavení > Správa > sdílené složky** (nebo **svazky** Pokud iSCSI server). V **sdílené složky** okno, můžete zobrazit všechny sdílené složky (svazky) ze staré zařízení.
        ![](./media/storsimple-virtual-array-failover-dr/failover9.png)
14. Budete muset [vytvoření aliasu DNS](https://support.microsoft.com/kb/168322) tak, aby všechny aplikace, které se pokoušíte připojit můžete přesměrováni na nové zařízení.

## <a name="errors-during-dr"></a>Chyby během zotavení po Havárii

**Výpadek připojení cloudu během zotavení po Havárii**

Pokud připojení cloudu dojde k narušení po zahájení zotavení po Havárii, než se zařízení obnovení je dokončeno, zotavení po Havárii se nezdaří. Dostanete oznámení, failore. Cílové zařízení pro zotavení po Havárii je označena jako *nepoužitelný.* Pro budoucí DRs nelze použít stejný cílové zařízení.

**Žádné kompatibilní cílových zařízení**

Pokud zařízení dostupných cílových nemají dostatek místa, zobrazí chybu v tom smyslu, že neexistují žádné kompatibilní cílových zařízení.

**Precheck selhání**

Pokud není splněna jedna z prechecks, uvidíte precheck selhání.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Obchodní kontinuity zotavení po havárii (BCDR)

Obchodní kontinuity po havárii (BCDR) obnovení scénář nastane, když celého datového centra Azure přestane fungovat. To může mít vliv služby StorSimple Manager zařízení a jeho přiřazená zařízení StorSimple.

Pokud je zařízení StorSimple, které byly zaregistrovány těsně před došlo k havárii, tato zařízení StorSimple možná muset odstranit. Po havárii můžete znovu vytvořit a nakonfigurovat tato zařízení.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak [spravovat vaše pole virtuální zařízení StorSimple pomocí místního webového uživatelského rozhraní](storsimple-ova-web-ui-admin.md).

