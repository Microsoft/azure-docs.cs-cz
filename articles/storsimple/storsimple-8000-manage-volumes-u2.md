---
title: Spravovat svazky zařízení StorSimple (Update 3) | Microsoft Docs
description: Vysvětluje, jak přidat, upravit, sledovat a odstraňovat svazky zařízení StorSimple a jak je převést do režimu offline, v případě potřeby.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/08/2017
ms.author: alkohli
ms.openlocfilehash: c9c575f42e6c8730b9404c62fb60e710d9d3bc80
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2017
ms.locfileid: "26578869"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-volumes-update-3-or-later"></a>Použít službu StorSimple Manager zařízení ke správě svazky (Update 3 nebo novější)

## <a name="overview"></a>Přehled

Tento kurz vysvětluje, jak používat službu StorSimple Manager zařízení k vytváření a správě svazky na řadu zařízení StorSimple 8000 verzi Update 3 nebo novější.

Služba Správce zařízení StorSimple je rozšíření na portálu Azure, která umožňuje spravovat z jedné webové rozhraní vašeho řešení StorSimple. Použití portálu Azure ke správě svazky na všech zařízeních. Můžete také vytvořit a spravovat služby StorSimple, spravovat zařízení, zásady zálohování a zálohování katalogu a zobrazovat výstrahy.

## <a name="volume-types"></a>Typy svazku

Svazky zařízení StorSimple může být:

* **Místně vázaný svazky**: Data v těchto svazcích zůstane na místním zařízení StorSimple za všech okolností.
* **Zřízeny vrstvené svazky**: Data v těchto svazků můžete distribuována do cloudu.

Archivace svazek je typ vrstvený svazek. Použít pro archivaci svazky větší velikost bloku odstranění duplicitních dat umožňuje zařízení k přenosu větší segmenty dat do cloudu.

Pokud potřeby můžete změnit svazek typu z místní pro vrstvené nebo z vrstvené na místní. Další informace, přejděte na [změnit typ svazku](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Místně vázaných svazků

Místně vázaných svazků jsou zcela zřizované svazky, které provádějí není dat vrstev do cloudu, a zajistí tak místní záruky pro primární data, nezávisle na cloudu připojení. Není data na místně vázaných svazků s odstraněním duplicitních dat a komprimované; Nicméně jsou odstranění duplicit snímky místně vázaných svazků. 

Místně vázaných svazků jsou plně zřízený; proto musí mít dostatek místa na vašem zařízení, při jejich vytváření. Můžete zřídit místně vázaných svazků až maximální velikosti 8 TB zařízení StorSimple 8100 a 20 TB na zařízení 8600. StorSimple si vyhrazuje zbývající volné místo na zařízení pro snímky, metadata a data zpracování. Můžete zvýšit velikost místně vázaný svazek na maximální místo k dispozici, ale nelze snížit velikost svazku po vytvoření.

Když vytvoříte místně vázaný svazek, dostupné místo pro vytvoření vrstvené svazky se snižuje. Platí to i naopak: Pokud máte vrstvené svazky, volné místo dostupné pro vytváření místně připojené svazky bude nižší než maximální limit uvedené výše. Další informace o místní svazky, najdete v části [nejčastější dotazy na místně vázaných svazků](storsimple-8000-local-volume-faq.md).

### <a name="tiered-volumes"></a>Vrstvené svazky

Vrstvené svazky jsou dynamicky zřizované svazky, ve kterých často používaná data zůstává místní na zařízení a automaticky vrstveny méně často používaná data do cloudu. Dynamické zajišťování je technologie virtualizace, ve kterém se zobrazuje dostupné úložiště delší než fyzické prostředky. StorSimple používá k přidělení právě dostatek místa, aby splňovala aktuální požadavky místo rezervování dostatečné úložiště předem, dynamické zajišťování. Elastické povaha cloudového úložiště usnadňuje tento přístup, protože StorSimple můžete zvýšit nebo snížit úložiště v cloudu splňovat požadavky na změny.

Pokud používáte vrstvený svazek pro archivní data, vyberte **použít tento svazek pro archivní data s méně často používaná** zaškrtávací políčko, chcete-li změnit velikost bloku odstranění duplicitních dat pro svazek na 512 KB. Pokud tuto možnost nevyberete, příslušný vrstvený svazek bude používat velikost bloku dat 64 KB. Větší velikost deduplikačního bloku dat umožňuje zařízení urychlit přenos velkých objemů archivních dat do cloudu.


### <a name="provisioned-capacity"></a>Zřízená kapacita

Naleznete v následující tabulce maximální zřízená kapacita pro každý typ zařízení a svazku. (Všimněte si, že nejsou dostupné na virtuálním zařízení místně vázaných svazků.)

|  | Vrstvený svazek maximální velikost | Maximální místně připnutý velikost svazku |
| --- | --- | --- |
| **Fyzické zařízení** | | |
| 8100 |64 TB |8 TB |
| 8600 |64 TB |20 TB |
| **Virtuální zařízení** | | |
| 8010 |30 TB |Není k dispozici |
| 8020 |64 TB |Není k dispozici |

## <a name="the-volumes-blade"></a>V okně svazky

**Svazky** okno umožňuje správu svazků úložiště, které jsou zřízené v Microsoft Azure StorSimple zařízení pro vaše iniciátorů (serverů). Zobrazuje seznam svazků v zařízení StorSimple připojené k vaší službě.

 ![Stránka svazky](./media/storsimple-8000-manage-volumes-u2/volumeslist.png)

Svazek se skládá z řady atributy:

* **Název svazku** – popisný název, který musí být jedinečný a pomáhá identifikovat svazku. Tento název se také používá v monitorování sestavy při filtrování na konkrétním svazku. Po vytvoření svazku nelze přejmenovat.
* **Stav** – může být online nebo offline. Pokud je svazek offline, není viditelná pro iniciátorů (serverů), které jsou povoleny přístup k použití svazku.
* **Kapacita** – Určuje celkový objem dat, která mohou být uloženy iniciátor (server). Místně vázaný svazky jsou plně zřízený a jsou umístěny v zařízení StorSimple. Vrstvené svazky jsou tence zřízený a data se odstraňují. S dynamicky zřizované svazky nebude zařízení předem přidělit kapacity fyzického úložiště, interně, nebo v cloudu podle kapacita nakonfigurované svazku. Kapacita svazku je přidělené a využívat na vyžádání.
* **Typ** – Určuje, zda je svazek **nastavování** (výchozí) nebo **místně vázaný**.

Postupujte podle pokynů v tomto kurzu k provádění následujících úloh:

* Přidání svazku 
* Upravit svazku 
* Změnit typ svazku
* Odstranění svazku 
* Svazek převést do režimu offline 
* Monitorování svazku 

## <a name="add-a-volume"></a>Přidání svazku

Můžete [vytvořili svazek](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) během nasazování zařízení řady StorSimple 8000. Přidání svazku je podobným způsobem.

#### <a name="to-add-a-volume"></a>Chcete-li přidat svazek

1. V tabulkovém výpisu zařízení v okně **Zařízení** vyberte vaše zařízení. Klikněte na **+ Přidat svazek**.

    ![Přidání nového svazku](./media/storsimple-8000-manage-volumes-u2/step5createvol1.png)

2. V okně **Přidat svazek**:
   
    1. Do pole **Vybrat zařízení** se automaticky vyplní vaše aktuální zařízení.

    2. Z rozevíracího seznamu vyberte kontejner svazků, do kterého potřebujete přidat svazek.

    3.  Zadejte **Název** svazku. Po vytvoření svazku nelze přejmenovat svazku.

    4. V rozevíracím seznamu vyberte **Typ** svazku. Pro úlohy, které vyžadují místní záruky, nízkou latenci a vyšší výkon, vyberte typ svazku **Místně vázaný**. Pro ostatní typy dat vyberte typ svazku **Vrstvený**. Pokud tento svazek používáte k archivaci dat, zaškrtněte políčko **Použít tento svazek pro archivní data s méně častým přístupem**.
      
       Vrstvený svazek je zřizovaný dynamicky a lze jej rychle vytvořit. Výběrem možnosti **Použít tento svazek pro archivní data s méně častým přístupem** u vrstveného svazku určeného k uchovávání archivních dat změníte velikost bloku odstranění duplicit vašeho svazku na 512 kB. Pokud políčko není zaškrtnuté, příslušný vrstvený svazek bude používat velikost bloku 64 kB. Větší velikost deduplikačního bloku dat umožňuje zařízení urychlit přenos velkých objemů archivních dat do cloudu.
       
       Místně vázaný svazek je tlustě zřízený a zajišťuje, že primární data uložená ve svazku zůstanou uložená v místním zařízení a nebudou distribuována do cloudu.  Pokud vytvoříte místně vázaný svazek, zařízení zkontroluje dostupné místo v místních vrstvách a zřídí svazek požadované velikosti. Při operaci vytváření místně vázaného svazku mohou být existující data ze zařízení distribuována do cloudu a vytvoření svazku může trvat dlouhou dobu. Celková doba závisí na velikosti zřizovaného svazku, dostupné šířce pásma sítě a datech uložených v zařízení.

    5. Zadejte hodnotu **Zřízená kapacita** svazku. Poznamenejte si kapacitu dostupnou na základě vybraného typu svazku. Zadaná velikost svazku nesmí překročit velikost dostupného místa.
      
       Zařízení 8100 umožňuje zřizovat místně vázané svazky o velikosti až 8.5 TB a vrstvené svazky o velikosti až 200 TB. Větší zařízení 8600 umožňuje zřizovat místně vázané svazky o velikosti až 22.5 TB a vrstvené svazky o velikosti až 500 TB. Protože k hostování fungující sady vrstvených svazků je vyžadováno volné místo v zařízení, vytváření místně vázaných svazků ovlivňuje volné místo dostupné ke zřizování vrstvených svazků. Proto pokud vytvoříte místně připojený svazek, zmenší se dostupné volné místo pro vytváření vrstvených svazků. Podobně pokud vytvoříte vrstvený svazek, zmenší se dostupné volné místo potřebné k vytváření místně vázaných svazků.
      
       Pokud v zařízení 8100 zřídíte místně vázaný svazek o velikosti 8.5 TB (maximální možná velikost), vyčerpáte tím veškeré volné místo dostupné v zařízení. Od této chvíle už nebudete moct vytvořit žádné vrstvené svazky, protože v zařízení už nebude žádné volné místo k hostování pracovní sady vrstveného svazku. Objem dostupného volného místa ovlivňují také vrstvené svazky. Pokud například používáte zařízení 8100, ve kterém jsou už zřízeny vrstvené svazky o velikosti zhruba 106 TB, k vytváření místně vázaných svazků zbude už jenom 4 TB dostupného volného místa.

    6. V poli **Připojení hostitelé** klikněte na šipku. V **připojení hostitele** okně zvolte existující ACR nebo přidat nový ACR. Pokud si zvolíte nové ACR, potom zadejte **název** acr, zadejte **iSCSI Qualified Name** (IQN) hostitele s Windows. Pokud název IQN nemáte, přejděte do části [Získání názvu hostitele se systémem Windows Server](#get-the-iqn-of-a-windows-server-host). Klikněte na možnost **Vytvořit**. Vytvoří se svazek se zadaným nastavením.

        ![Kliknutí na Vytvořit](./media/storsimple-8000-manage-volumes-u2/step5createvol3.png)

Nový svazek je nyní připraven k použití.

> [!NOTE]
> Pokud vytvoříte místně vázaný svazek a pak vytvořit jiné místně vázaný svazek okamžitě později, vytvoření svazku, postupně spuštění úlohy. První úloha vytvoření svazku se musí ukončit předtím, než můžete začít další úloha vytvoření svazku.

## <a name="modify-a-volume"></a>Upravit svazku

Upravte svazek, když je potřeba ho rozšířit nebo změnit hostitelů, které přístup ke svazku.

> [!IMPORTANT]
> * Pokud změníte velikost svazku v zařízení, je potřeba změnit na hostitele a velikost svazku.
> * Hostitelské kroků popsaných v tomto poli se pro Windows Server 2012 (2012 R2). Postupy pro Linux nebo jiných hostitelských operačních systémech se liší. Vyhledejte vaše hostitele operačního systému při úpravě svazek na hostitele s jiným operačním systémem.

#### <a name="to-modify-a-volume"></a>Chcete-li upravit svazku

1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. Tabulkový seznam zařízení, vyberte zařízení, která obsahuje svazek, který chcete upravit. Klikněte na tlačítko **Nastavení > svazky**.

    ![Přejděte do okna svazky](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

2. Tabulkový seznam svazků, vyberte svazek a klikněte pravým tlačítkem na má být vyvolán v místní nabídce. Vyberte **převést do režimu offline** uvedení svazku upravíte do režimu offline.

    ![Vyberte a do offline režimu svazku](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. V **převést do režimu offline** okně zkontrolovat dopad svazku přepnutím do režimu offline a zaškrtněte odpovídající políčko. Zajistěte, aby byl odpovídající svazek na hostiteli offline nejdřív. Informace o tom, jak provést offline svazku na serveru hostitele připojené k StorSimple postupujte podle pokynů pro konkrétní operační systém. Klikněte na tlačítko **převést do režimu offline**.

    ![Zkontrolujte dopad svazku přepnutím do režimu offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

4. Po svazek je offline (jak ukazují stav svazku), vyberte svazek a klikněte pravým tlačítkem na má být vyvolán v místní nabídce. Vyberte **upravit svazek**.

    ![Vyberte Změnit svazku](./media/storsimple-8000-manage-volumes-u2/modifyvol9.png)


5. V **upravit svazek** okno, můžete provést následující změny:
   
   1. Svazek **název** nelze upravit.
   2. Převést **typ** z místně vázaný k vrstvené nebo z vrstvené pro místně vázaný (najdete v části [změnit typ svazku](#change-the-volume-type) informace).
   3. Zvýšit **zřídit kapacitu**. **Zřízená kapacita** může být pouze zvýšena. Po vytvoření, nelze zmenšit svazek.
   4. V části **připojení hostitele**, můžete upravit ACR. Pokud chcete upravit ACR, musí být svazek offline.

       ![Zkontrolujte dopad svazku přepnutím do režimu offline](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

5. Klikněte na tlačítko **Uložit** uložte provedené změny. Po zobrazení výzvy k potvrzení klikněte na **Ano**. Portál Azure se zobrazí zprávu aktualizací svazku. Zpráva o úspěšném provedení zobrazí při svazku byla úspěšně aktualizována.

    ![Zkontrolujte dopad svazku přepnutím do režimu offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

7. Pokud se rozšířit svazek, proveďte následující kroky v hostitelském počítači Windows:
   
   1. Přejděte na **Správa počítače** ->**disku správu**.
   2. Klikněte pravým tlačítkem na **Správa disků** a vyberte **Prohledat disky**.
   3. V seznamu disků, vyberte svazek, který jste aktualizaci, klikněte pravým tlačítkem a pak vyberte **rozšířit svazek**. Spustí se Průvodce rozšířit svazek. Klikněte na **Další**.
   4. Dokončete průvodce, přijetím výchozích hodnot. Po dokončení průvodce by se zobrazit vyšší velikost svazku.
      
      > [!NOTE]
      > Pokud rozbalte místně vázaný svazek a potom rozbalte jiné místně připnuli svazku okamžitě později, úlohy rozšíření svazku spouští sekvenčně. První úloha rozšíření svazku se musí ukončit předtím, než můžete začít další úlohy rozšíření svazku.
      

## <a name="change-the-volume-type"></a>Změnit typ svazku

Můžete změnit typ svazku z vrstvené pro místně vázaný nebo z místně vázaný na vrstvené. Tento převod však by neměl být časté výskyt.

### <a name="tiered-to-local-volume-conversion-considerations"></a>Víceúrovňová aspektech převod místního svazku

Důvody pro převod svazku z vrstvené pro místně vázaný zahrnují:

* Místní záruky týkající se data dostupnosti a výkonu
* Odstranění cloudu latenci a problémy s připojením k cloudu.

Obvykle jsou malé existující svazky, které chcete pro přístup k často. Když je vytvořeno místně vázaný svazek plně zřízený. 

Při převodu vrstvený svazek k místně vázaný svazek, StorSimple ověřuje mít dostatek místa na zařízení, než začne převod. Pokud máte dostatek místa, dojde k chybě a operace bude zrušena. 

> [!NOTE]
> Před zahájením převodu z vrstvené pro místně vázaný, ujistěte se, vezměte v úvahu požadavky na místo vašich dalších zatížení. 

Převod vrstvené na místně vázaný svazek může nepříznivě ovlivnit výkon zařízení. Kromě toho následující faktory může se prodloužit doba potřebná k dokončení převodu:

* Není k dispozici dostatečná šířka pásma.
* Není žádná aktuální záloha.

Chcete-li minimalizovat účinky, které může mít tyto faktory:

* Zkontrolujte vaše zásady omezení šířky pásma a ujistěte se, že vyhrazené šířky pásma 40 MB/s je k dispozici.
* Plánování převod hodiny mimo špičku.
* Pořízení snímku cloudu před zahájením převodu.

Pokud převádíte více svazků (Podpora různých zátěží), by měl tak, aby vyšší prioritu svazky se převedou nejprve upřednostňovat převodu svazku. Například je třeba převést svazky, které jsou hostiteli virtuálních počítačů (VM) nebo svazky s úlohami SQL před převodem svazky s úlohami sdílené složky souborů.

### <a name="local-to-tiered-volume-conversion-considerations"></a>Místní vrstvený svazek převod aspektech

Můžete změnit místně vázaný svazek na vrstvený svazek, pokud potřebujete další místa pro zřízení jiných svazků. Při převodu místně vázaný svazek k vrstvené dostupné kapacity na zařízení zvyšuje velikost vydaná kapacitu. Pokud problémy s připojením k zabránit převod svazku místního typu vrstvené typ, místní svazek bude mít květy vlastnosti vrstvený svazek, až po dokončení převodu. Je to proto, že některá data mohou přesahovat do cloudu. Tato spilled data i nadále zabírají volné místo na zařízení, které nelze uvolnit, dokud je restartovat a dokončit operaci.

> [!NOTE]
> Převod svazku může určitou dobu trvat a nelze zrušit převodu z po jeho spuštění. Zachování svazku online během převodu a můžete provést zálohování, ale nelze rozbalit nebo obnovit svazek během probíhající převod.


#### <a name="to-change-the-volume-type"></a>Chcete-li změnit typ svazku

1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. Tabulkový seznam zařízení, vyberte zařízení, která obsahuje svazek, který chcete upravit. Klikněte na tlačítko **Nastavení > svazky**.

    ![Přejděte do okna svazky](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Tabulkový seznam svazků, vyberte svazek a klikněte pravým tlačítkem na má být vyvolán v místní nabídce. Vyberte **upravit**.

    ![Vyberte změnit z kontextové nabídky](./media/storsimple-8000-manage-volumes-u2/changevoltype2.png)

4. Na **upravit svazek** okně změnit typ svazku výběrem nového typu z **typ** rozevíracího seznamu.
   
   * Pokud chcete změnit typ, který má **místně vázaný**, StorSimple zkontroluje, jestli je dostatečnou kapacitu.
   * Pokud chcete změnit typ, který má **nastavování** a použije tento svazek pro archivní data, vyberte **použít tento svazek pro archivní data s méně často používaná** zaškrtávací políčko.
   * Pokud konfigurujete místně vázaný svazek jako vrstvené nebo _naopak_, zobrazí se následující zpráva.
   
    ![Změna svazek zadejte text zprávy.](./media/storsimple-8000-manage-volumes-u2/changevoltype3.png)

7. Kliknutím na **Uložit** uložte změny. Po zobrazení výzvy k potvrzení, klikněte na tlačítko **Ano** zahájíte proces převodu. 

    ![Uložte a potvrďte](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

8. Portál Azure zobrazí oznámení pro vytvoření úlohy, které by aktualizaci svazku. Kliknutím na oznámení k monitorování stavu úlohy převod svazku.

    ![Úlohy pro převod svazku](./media/storsimple-8000-manage-volumes-u2/changevoltype5.png)

## <a name="take-a-volume-offline"></a>Svazek převést do režimu offline

Potřebujete do offline režimu svazku při plánování k úpravě nebo odstranění svazku. Pokud je svazek offline, není k dispozici pro přístup pro čtení a zápis. Je třeba provést svazek offline na hostiteli a zařízení.

#### <a name="to-take-a-volume-offline"></a>Uvedení svazku do režimu offline

1. Ujistěte se, že dotyčný svazek není používána před přepnutím do režimu offline.
2. Proveďte offline svazek na hostiteli první. Tím se eliminuje všechny potenciální riziko poškození dat na svazku. Konkrétní kroky přečtěte si pokyny pro operační systém hostitele.
3. Až bude hostitel v režimu offline, proveďte svazek v zařízení do offline režimu provedením následujících kroků:
   
    1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. Tabulkový seznam zařízení, vyberte zařízení, která obsahuje svazek, který chcete upravit. Klikněte na tlačítko **Nastavení > svazky**.

        ![Přejděte do okna svazky](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

    2. Tabulkový seznam svazků, vyberte svazek a klikněte pravým tlačítkem na má být vyvolán v místní nabídce. Vyberte **převést do režimu offline** uvedení svazku upravíte do režimu offline.

        ![Vyberte a do offline režimu svazku](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. V **převést do režimu offline** okně zkontrolovat dopad svazku přepnutím do režimu offline a zaškrtněte odpovídající políčko. Klikněte na tlačítko **převést do režimu offline**. 

    ![Zkontrolujte dopad svazku přepnutím do režimu offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)
      
      Budete upozorněni, když je svazek offline. Stav svazku se také aktualizace Offline.
      
4. Jakmile je svazek offline, pokud vyberete svazek a klikněte pravým tlačítkem, **přepnout do režimu Online** bude k dispozici v místní nabídce.

> [!NOTE]
> **Přepnout do režimu Offline** příkaz odešle požadavek na zařízení do offline režimu svazku. Pokud svazek stále používají hostitelé, to vede k přerušení připojení, ale nebudou svazku přepnutím do režimu offline.

## <a name="delete-a-volume"></a>Odstranění svazku

> [!IMPORTANT]
> Svazek můžete odstranit pouze v případě, že je offline.

Proveďte následující kroky odstranění svazku.

#### <a name="to-delete-a-volume"></a>Odstranění svazku

1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. Tabulkový seznam zařízení, vyberte zařízení, která obsahuje svazek, který chcete upravit. Klikněte na tlačítko **Nastavení > svazky**.

    ![Přejděte do okna svazky](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Zkontrolujte stav svazku, který chcete odstranit. Pokud na svazek, který chcete odstranit není v režimu offline, odpojte jej nejprve. Postupujte podle kroků v [do offline režimu svazku](#take-a-volume-offline).
4. Jakmile je svazek offline, vyberte svazek, klikněte pravým tlačítkem na vyvolání v místní nabídce a potom vyberte **odstranit**.

    ![Vyberte možnost odstranit z kontextové nabídky](./media/storsimple-8000-manage-volumes-u2/deletevol1.png)

5. V **odstranit** okně zkontrolovat a zaškrtněte políčko proti důsledcích odstranění svazku. Pokud odstraníte svazek, dojde ke ztrátě všechna data, která se nachází na svazku. 

    ![Uložte a potvrďte změny](./media/storsimple-8000-manage-volumes-u2/deletevol2.png)

6. Po odstranění svazku tabulkového seznamu svazků aktualizuje označit pro odstranění.

    ![Seznam aktualizované svazků](./media/storsimple-8000-manage-volumes-u2/deletevol3.png)
   
   > [!NOTE]
   > Pokud odstraníte místně vázaný svazek, místa na disku pro nové svazky nemusí být okamžitě aktualizován. Volné místo dostupné aktualizace služby StorSimple Manager zařízení pravidelně. Doporučujeme, že Počkejte několik minut, než se pokusíte vytvořit nový svazek.
   >
   > Kromě toho pokud odstraníte místně vázaný svazek a potom odstraňte jiné místně připnuli svazku okamžitě později, postupně spuštění úlohy odstranění svazku. První úlohy odstranění svazku se musí ukončit předtím, než můžete začít další úlohy odstranění svazku.

## <a name="monitor-a-volume"></a>Monitorování svazku

Svazek monitorování umožňuje shromažďování statistik I/E-související pro svazek. Monitorování je povoleno ve výchozím nastavení nejprve 32 svazků, které vytvoříte. Ve výchozím nastavení vypnutá monitorování další svazky. 

> [!NOTE]
> Monitorování klonovaný svazků je ve výchozím nastavení zakázáno.


Proveďte následující kroky, které chcete povolit nebo zakázat monitorování pro svazek.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Chcete povolit nebo zakázat monitorování svazku

1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. Tabulkový seznam zařízení, vyberte zařízení, která obsahuje svazek, který chcete upravit. Klikněte na tlačítko **Nastavení > svazky**.
2. Tabulkový seznam svazků, vyberte svazek a klikněte pravým tlačítkem na má být vyvolán v místní nabídce. Vyberte **upravit**.
3. V **upravit svazek** okně pro **monitorování** vyberte **povolit** nebo **zakázat** chcete povolit nebo zakázat monitorování.

    ![Zakázání sledování](./media/storsimple-8000-manage-volumes-u2/monitorvol1.png) 

4. Klikněte na tlačítko **Uložit** a po zobrazení výzvy k potvrzení, klikněte na tlačítko **Ano**. Portál Azure zobrazí oznámení pro aktualizaci svazek a potom zpráva o úspěšném provedení po úspěšné aktualizaci svazku.

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak [klonovat svazek StorSimple](storsimple-8000-clone-volume-u2.md).
* Zjistěte, jak [použít službu StorSimple Manager zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

