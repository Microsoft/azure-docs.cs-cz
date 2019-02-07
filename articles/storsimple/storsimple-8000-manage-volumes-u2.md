---
title: Správa svazků StorSimple (Update 3) | Dokumentace Microsoftu
description: Vysvětluje, jak přidat, upravit, monitorovat a odstraňte svazky zařízení StorSimple a jak je převést do režimu offline, v případě potřeby.
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
ms.openlocfilehash: b748e203e3bf769eef8ce728bbb9471b8d13fb9a
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55822302"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-volumes-update-3-or-later"></a>Použití služby Správce zařízení StorSimple pro správu svazků (aktualizace 3 nebo novější)

## <a name="overview"></a>Přehled

Tento kurz vysvětluje, jak pomocí služby Správce zařízení StorSimple můžete vytvářet a spravovat svazcích zařízeních StorSimple řady 8000 s aktualizací 3 nebo novějším.

Služba Správce zařízení StorSimple je rozšíření na webu Azure Portal, který umožňuje spravovat řešení StorSimple v jediném webovém rozhraní. Správa svazků na všechna svá zařízení pomocí webu Azure portal. Můžete také vytvořit a spravovat služby StorSimple, spravovat zařízení, zásady zálohování a katalog záloh a zobrazovat výstrahy.

## <a name="volume-types"></a>Typy svazků

Svazky zařízení StorSimple může být:

* **Místně připojené svazky**: Data v těchto svazcích zůstanou na místním zařízení StorSimple za všech okolností.
* **Vrstvené svazky**: Data v těchto svazků můžete uložená v cloudu.

Archivace svazek je typ vrstveného svazku. Větší velikost bloku odstranění duplicit použít pro archivní svazky umožňuje, aby zařízení přenášet větší segmenty dat do cloudu.

Pokud třeba, můžete změnit svazek typ z místního na vrstvený nebo z vrstveného na místní. Další informace najdete v části [změnu typu svazku](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Místně připojené svazky

Místně připojené svazky jsou zcela zřizované svazky, které nejsou úrovně dat do cloudu, a zajistí tak místní záruky pro primární data, nezávisle na připojení ke cloudu. Není data na místně připojených svazků s odstraněním duplicitních dat a komprimované; Nicméně jsou odstranění duplicit snímky místně vázaných svazků. 

Místně připojené svazky jsou plně zřízený; proto musí mít dostatek místa na vašem zařízení při jejich vytváření. Můžete zřídit místně připojené svazky až do maximální velikosti 8 TB na zařízení StorSimple 8100 a 20 TB zařízení 8600. StorSimple rezervuje zbývající volné místo v zařízení pro zpracování dat, snímky a metadata. Můžete zvýšit velikost místně vázaný svazek na maximální místo k dispozici, ale nedá snížit velikost svazku po vytvoření.

Pokud vytvoříte místně vázaný svazek, se snižuje dostupný prostor pro vytváření vrstvených svazků. Platí to i naopak: Pokud máte stávající vrstvené svazky, volné místo dostupné pro vytváření místně připojené svazky bude nižší než maximální limity výše. Další informace na místních svazcích [– nejčastější dotazy na místně připojených svazků](storsimple-8000-local-volume-faq.md).

### <a name="tiered-volumes"></a>Vrstvené svazky

Vrstvené svazky se dynamicky zřizované svazky, ve kterých často používaná data zůstanou místní na zařízení a méně často používaná data se automaticky Vrstvená do cloudu. Dynamické zajišťování je technologie virtualizace, ve kterém se zobrazí dostupné úložiště překročí fyzické prostředky. Místo předem rezervace dostatečné úložiště StorSimple používá dynamické zajišťování přidělit jenom dostatek místa, aby splňovala aktuální požadavky. K elastické povaze cloudu úložiště usnadňuje tento přístup, protože StorSimple můžete zvýšit nebo snížit cloudové úložiště s cílem splnit měnící požadavky.

Pokud používáte vrstvený svazek pro archivní data, vyberte **použít tento svazek pro archivní data s méně častým** zaškrtávací políčko, chcete-li změnit velikost bloku odstranění duplicit vašeho svazku na 512 KB. Pokud tuto možnost nevyberete, příslušný vrstvený svazek bude používat velikost bloku dat 64 KB. Větší velikost deduplikačního bloku dat umožňuje zařízení urychlit přenos velkých objemů archivních dat do cloudu.


### <a name="provisioned-capacity"></a>Zřízená kapacita

Naleznete v následující tabulce pro maximální zřízená kapacita pro každý typ zařízení a svazku. (Všimněte si, že se místně připojené svazky nejsou k dispozici na virtuálním zařízení.)

|  | Vrstvený svazek maximální velikost | Maximální počet místně připnuté velikost svazku |
| --- | --- | --- |
| **Fyzické zařízení** | | |
| 8100 |64 TB |8 TB |
| 8600 |64 TB |20 TB |
| **Virtuální zařízení** | | |
| 8010 |30 TB |neuvedeno |
| 8020 |64 TB |neuvedeno |

## <a name="the-volumes-blade"></a>V okně svazky

**Svazky** okno umožňuje spravovat svazky úložiště, které jsou zřízené v Microsoft Azure StorSimple zařízení pro vaše iniciátorů (serverů). Zobrazí seznam svazků na zařízení StorSimple připojené k vaší službě.

 ![Stejnojmennou stránku](./media/storsimple-8000-manage-volumes-u2/volumeslist.png)

Svazek se skládá z řady atributy:

* **Název svazku** – popisný název, který musí být jedinečný a pomáhá identifikovat svazku. Tento název se používá také v monitorování sestavy při filtrování na konkrétním svazku. Po vytvoření svazku se nedá přejmenovat.
* **Stav** – může být online nebo offline. Pokud je svazek offline, není viditelné pro iniciátorů (serverů), které mají povolený přístup ke svazku.
* **Kapacita** – určuje celkové množství dat, která mohou být uloženy iniciátor (server). Místně připojené svazky jsou plně zřízený a jsou umístěny na zařízení StorSimple. Vrstvených svazků je zřizovaný dynamicky a data se odstraňují duplicity. Se dynamicky zřizované svazky nebude zařízení předem přidělit kapacitu fyzického úložiště, interně nebo v cloudu podle nakonfigurovaného kapacitě. Kapacita svazku je přidělena a využívat na vyžádání.
* **Typ** – Určuje, zda je svazek **Vrstvená** (výchozí) nebo **místně připnuté**.

Postupujte podle pokynů v tomto kurzu provádět následující úlohy:

* Přidat svazek 
* Upravit svazek 
* Změnit typ svazku
* Odstranění svazku 
* Svazek převést do režimu offline 
* Monitorování svazku 

## <a name="add-a-volume"></a>Přidat svazek

Můžete [vytvořili svazek](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) během nasazování zařízení řady StorSimple 8000. Přidání svazku je podobný postup.

#### <a name="to-add-a-volume"></a>Chcete-li přidat svazek

1. V tabulkovém výpisu zařízení v okně **Zařízení** vyberte vaše zařízení. Klikněte na **+ Přidat svazek**.

    ![Přidání nového svazku](./media/storsimple-8000-manage-volumes-u2/step5createvol1.png)

2. V okně **Přidat svazek**:
   
    1. Do pole **Vybrat zařízení** se automaticky vyplní vaše aktuální zařízení.

    2. Z rozevíracího seznamu vyberte kontejner svazků, do kterého potřebujete přidat svazek.

    3.  Zadejte **Název** svazku. Po vytvoření svazku se už nedá přejmenovat svazku.

    4. V rozevíracím seznamu vyberte **Typ** svazku. Pro úlohy, které vyžadují místní záruky, nízkou latenci a vyšší výkon, vyberte typ svazku **Místně vázaný**. Pro ostatní typy dat vyberte typ svazku **Vrstvený**. Pokud tento svazek používáte k archivaci dat, zaškrtněte políčko **Použít tento svazek pro archivní data s méně častým přístupem**.
      
       Vrstvený svazek je zřizovaný dynamicky a lze jej rychle vytvořit. Výběrem možnosti **Použít tento svazek pro archivní data s méně častým přístupem** u vrstveného svazku určeného k uchovávání archivních dat změníte velikost bloku odstranění duplicit vašeho svazku na 512 kB. Pokud políčko není zaškrtnuté, příslušný vrstvený svazek bude používat velikost bloku 64 kB. Větší velikost deduplikačního bloku dat umožňuje zařízení urychlit přenos velkých objemů archivních dat do cloudu.
       
       Místně vázaný svazek je tlustě zřízený a zajišťuje, že primární data uložená ve svazku zůstanou uložená v místním zařízení a nebudou distribuována do cloudu.  Pokud vytvoříte místně vázaný svazek, zařízení zkontroluje dostupné místo v místních vrstvách a zřídí svazek požadované velikosti. Při operaci vytváření místně vázaného svazku mohou být existující data ze zařízení distribuována do cloudu a vytvoření svazku může trvat dlouhou dobu. Celková doba závisí na velikosti zřizovaného svazku, dostupné šířce pásma sítě a datech uložených v zařízení.

    5. Zadejte hodnotu **Zřízená kapacita** svazku. Poznamenejte si kapacitu dostupnou na základě vybraného typu svazku. Zadaná velikost svazku nesmí překročit velikost dostupného místa.
      
       Zařízení 8100 umožňuje zřizovat místně vázané svazky o velikosti až 8.5 TB a vrstvené svazky o velikosti až 200 TB. Větší zařízení 8600 umožňuje zřizovat místně vázané svazky o velikosti až 22.5 TB a vrstvené svazky o velikosti až 500 TB. Protože k hostování fungující sady vrstvených svazků je vyžadováno volné místo v zařízení, vytváření místně vázaných svazků ovlivňuje volné místo dostupné ke zřizování vrstvených svazků. Proto pokud vytvoříte místně připojený svazek, zmenší se dostupné volné místo pro vytváření vrstvených svazků. Podobně pokud vytvoříte vrstvený svazek, zmenší se dostupné volné místo potřebné k vytváření místně vázaných svazků.
      
       Pokud v zařízení 8100 zřídíte místně vázaný svazek o velikosti 8.5 TB (maximální možná velikost), vyčerpáte tím veškeré volné místo dostupné v zařízení. Od této chvíle už nebudete moct vytvořit žádné vrstvené svazky, protože v zařízení už nebude žádné volné místo k hostování pracovní sady vrstveného svazku. Objem dostupného volného místa ovlivňují také vrstvené svazky. Pokud například používáte zařízení 8100, ve kterém jsou už zřízeny vrstvené svazky o velikosti zhruba 106 TB, k vytváření místně vázaných svazků zbude už jenom 4 TB dostupného volného místa.

    6. V poli **Připojení hostitelé** klikněte na šipku. V **připojení hostitelé** okně zvolte existující záznam ACR nebo přidejte nový záznam ACR. Pokud se rozhodnete nový záznam ACR, pak poskytnete **název** záznamu acr, zadejte **iSCSI Qualified Name** (IQN) vašeho hostitele Windows. Pokud je identifikátor IQN nemáte, přejděte k získání názvu IQN hostitele se Windows Server. Klikněte na možnost **Vytvořit**. Vytvoří se svazek se zadaným nastavením.

        ![Kliknutí na Vytvořit](./media/storsimple-8000-manage-volumes-u2/step5createvol3.png)

Nový svazek je teď připravený k použití.

> [!NOTE]
> Pokud vytvoříte místně vázaný svazek a pak vytvořit jiné místně připojený svazek okamžitě poté, vytvořit svazek, ve kterém úlohy se spouští sekvenčně. První úloha vytvoření svazku musí dokončit před zahájením další úloha vytvoření svazku.

## <a name="modify-a-volume"></a>Upravit svazek

Pokud je potřeba ho rozšířit nebo změnit hostitele, kteří přístup ke svazku, upravte svazku.

> [!IMPORTANT]
> * Pokud změníte velikost svazku na zařízení, je potřeba změnit na hostitele a velikost svazku.
> * Hostitelské kroky, je zde popsáno, jsou určené pro Windows Server 2012 (2012 R2). Postupy pro systém Linux nebo jiných hostitelských operačních systémech se liší. Při úpravě svazek na hostiteli s jiným operačním systémem najdete pokyny operačního systému hostitele.

#### <a name="to-modify-a-volume"></a>Chcete-li změnit svazek

1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V tabulkovém výpisu zařízení vyberte zařízení, která má svazek, který máte v úmyslu změnit. Klikněte na tlačítko **Nastavení > svazky**.

    ![Přejděte do okna svazky](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

2. V tabulkovém výpisu svazků vyberte svazek a klikněte pravým tlačítkem myši vyvolejte místní nabídku. Vyberte **převést do režimu offline** svazku upravíte uvedení do režimu offline.

    ![Vyberte a svazek převést do režimu offline](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. V **převést do režimu offline** okně zkontrolujte dopad převodu svazku do režimu offline a zaškrtněte odpovídající políčko. Ujistěte se, že je příslušný svazek na hostiteli nejdříve offline. Informace o tom, jak provést offline svazku na serveru hostitele připojené k řešení StorSimple najdete pokyny pro konkrétní operační systém. Klikněte na tlačítko **převést do režimu offline**.

    ![Zkontrolujte dopad převodu svazku do režimu offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

4. Jakmile je svazek offline (jak je znázorněno podle stavu svazku), vyberte svazek a klikněte pravým tlačítkem myši vyvolejte místní nabídku. Vyberte **změnu svazku se**.

    ![Vyberte možnost změnit svazek](./media/storsimple-8000-manage-volumes-u2/modifyvol9.png)


5. V **změnu svazku se** okně můžete provádět následující změny:
   
   1. Svazek **název** nelze upravit.
   2. Převést **typ** z místně připnuté na vrstvené nebo z vrstveného na místně připnutý (naleznete v tématu [změnu typu svazku](#change-the-volume-type) Další informace).
   3. Zvětšit **zřízené kapacity**. **Zřízená kapacita** může být pouze zvýšena. Po vytvoření nedá zmenšit svazek.
   4. V části **připojení hostitelé**, můžete upravit ACR. Pokud chcete upravit ACR, musí být svazek offline.

       ![Zkontrolujte dopad převodu svazku do režimu offline](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

5. Klikněte na tlačítko **Uložit** uložte provedené změny. Po zobrazení výzvy k potvrzení klikněte na **Ano**. Na webu Azure portal zobrazí zpráva o aktualizaci svazku. Zpráva o úspěchu zobrazí při svazku se úspěšně aktualizoval.

    ![Zkontrolujte dopad převodu svazku do režimu offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

7. Pokud se chystáte se rozšířit svazek, proveďte následující kroky v hostitelském počítači Windows:
   
   1. Přejděte na **Správa počítače** ->**nástroji Správa disků**.
   2. Klikněte pravým tlačítkem na **Správa disků** a vyberte **znovu prohledat disky**.
   3. V seznamu disků, vyberte svazek, který jste aktualizovali, klikněte pravým tlačítkem a pak vyberte **rozšířit svazek**. Spustí se Průvodce rozšířit svazek. Klikněte na **Další**.
   4. Dokončete Průvodce přijetím výchozích hodnot. Po dokončení průvodce by se zobrazit zvětšení velikosti svazku.
      
      > [!NOTE]
      > Pokud rozbalte místně vázaný svazek a potom rozbalte jiné místně připnutý svazek hned potom úlohy rozšíření svazku se spouští sekvenčně. První úloha rozšíření svazků musí dokončit před zahájením další úlohy rozšíření svazku.
      

## <a name="change-the-volume-type"></a>Změnit typ svazku

Můžete změnit typ svazku z vrstveného na místně připnutý nebo z místně připnuté na vrstvený. Tento převod ale by neměly být časté výskyt.

### <a name="tiered-to-local-volume-conversion-considerations"></a>Vrstveného na místní svazek hlediska převodů

Mezi důvody pro převod svazku z vrstveného na místně připnutý jsou:

* Místní záruky týkající se dat, dostupnost a výkon
* Odstranění cloudové latenci a problémy s připojením cloudu.

Obvykle jsou malé existující svazky, které chcete získat přístup k často. Místně vázaný svazek je plně zřízený při jeho vytvoření. 

Pokud vytvoříte vrstvený svazek se převod na místně vázaný svazek, StorSimple ověřuje, že máte dostatek místa na vašem zařízení před zahájením převodu. Pokud máte dost místa, dojde k chybě a operace se zrušila. 

> [!NOTE]
> Před zahájením konverze z vrstveného na místně připnutý, nezapomeňte brát v úvahu požadavky na místo vašich dalších zatížení. 

Převod vrstveného na místně vázaný svazek může nepříznivě ovlivnit výkon zařízení. Kromě toho těchto faktorů může zvýšit čas potřebný k dokončení převodu:

* Je dostatečná šířka pásma.
* Neexistuje žádná aktuální záloha.

Chcete-li minimalizovat efekty, které mohou mít tyto faktory:

* Zkontrolujte omezení zásad šířky pásma a ujistěte se, že je k dispozici vyhrazené šířky pásma 40 MB/s.
* Plánování je převod hodiny mimo špičku.
* Pořídit snímek v cloudu, před zahájením převodu.

Pokud převádíte více svazků (podporují různé úlohy), měli byste tak, aby vyšší prioritu svazky jsou nejprve převést prioritně převodu svazku. Například byste měli převést svazky, které jsou hostiteli virtuálních počítačů (VM) nebo svazky s úlohami SQL před zahájením převodu svazků s úlohami sdílené složky souboru.

### <a name="local-to-tiered-volume-conversion-considerations"></a>Z místního na vrstvený svazek hlediska převodů

Můžete chtít změnit místně vázaný svazek vrstvený svazek, pokud potřebujete další místa pro zřízení jiných svazků. Při převodu místně vázaný svazek do vrstev, dostupná kapacita na zařízení se zvýší o velikosti vydané kapacity. Problémy s připojením zabránit převod svazku z místního typu na typ vrstvené, místní svazek produkovány vlastnosti vrstvené svazky až do dokončení převodu. Je to proto, že některá data mohou přesahovat do cloudu. Tato spilled data i nadále zabírat místní prostor zařízení, která nemůže být uvolněna až po restartování a dokončit operaci.

> [!NOTE]
> Převedení svazku může nějakou dobu trvat a nelze zrušit převod po jeho spuštění. Zachování svazku online při převodu a můžete provádět zálohování, ale nelze rozbalit nebo obnovte svazek, zatímco probíhá převod.


#### <a name="to-change-the-volume-type"></a>Chcete-li změnit typ svazku

1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V tabulkovém výpisu zařízení vyberte zařízení, která má svazek, který máte v úmyslu změnit. Klikněte na tlačítko **Nastavení > svazky**.

    ![Přejděte do okna svazky](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. V tabulkovém výpisu svazků vyberte svazek a klikněte pravým tlačítkem myši vyvolejte místní nabídku. Vyberte **upravit**.

    ![Vyberte možnost změnit z místní nabídky](./media/storsimple-8000-manage-volumes-u2/changevoltype2.png)

4. Na **změnu svazku se** okně změnit tak, že vyberete nový typ z typu svazku **typ** rozevíracího seznamu.
   
   * Pokud chcete změnit typ, který má **místně připnuté**, StorSimple zkontroluje, jestli je dostatečnou kapacitu.
   * Pokud chcete změnit typ, který má **Vrstvená** a použije se tento svazek pro archivní data, vyberte **použít tento svazek pro archivní data s méně častým** zaškrtávací políčko.
   * Pokud konfigurujete místně vázaný svazek jako vrstvené nebo _naopak_, zobrazí se následující zpráva.
   
    ![Změnit svazek typ zprávy](./media/storsimple-8000-manage-volumes-u2/changevoltype3.png)

7. Kliknutím na **Uložit** uložte změny. Po zobrazení výzvy k potvrzení, klikněte na tlačítko **Ano** spustit proces převodu. 

    ![Uložte a potvrďte](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

8. Na webu Azure portal zobrazí oznámení o vytvoření úlohy, které by aktualizaci svazku. Klikněte na oznámení s monitorováním stavu úloha převodu svazků.

    ![Úloha převodu svazků](./media/storsimple-8000-manage-volumes-u2/changevoltype5.png)

## <a name="take-a-volume-offline"></a>Svazek převést do režimu offline

Budete muset převést svazek do režimu offline, když máte v úmyslu změnit nebo Odstranit svazek. Pokud je svazek offline, není k dispozici pro přístup pro čtení a zápis. Je nutné provést svazek do režimu offline na tomto hostiteli a zařízení.

#### <a name="to-take-a-volume-offline"></a>Uvedení svazku do režimu offline

1. Ujistěte se, že svazek dotyčný není používán před přepnutím do režimu offline.
2. Převedení svazku do režimu offline v hostiteli první. Tím se eliminují všechny potenciální riziko poškození dat na svazku. Konkrétní kroky použijte pokyny pro váš operační systém hostitele.
3. Jakmile je hostitel v režimu offline, trvat, než svazku na zařízení do offline režimu provedením následujících kroků:
   
    1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V tabulkovém výpisu zařízení vyberte zařízení, která má svazek, který máte v úmyslu změnit. Klikněte na tlačítko **Nastavení > svazky**.

        ![Přejděte do okna svazky](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

    2. V tabulkovém výpisu svazků vyberte svazek a klikněte pravým tlačítkem myši vyvolejte místní nabídku. Vyberte **převést do režimu offline** svazku upravíte uvedení do režimu offline.

        ![Vyberte a svazek převést do režimu offline](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. V **převést do režimu offline** okně zkontrolujte dopad převodu svazku do režimu offline a zaškrtněte odpovídající políčko. Klikněte na tlačítko **převést do režimu offline**. 

    ![Zkontrolujte dopad převodu svazku do režimu offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)
      
      Pokud je svazek offline, se zobrazí oznámení. Stav svazku se rovněž aktualizuje na Offline.
      
4. Jakmile je svazek offline, pokud vyberete svazek a klikněte pravým tlačítkem, **přepnout do režimu Online** bude k dispozici v místní nabídce.

> [!NOTE]
> **Přepnout do režimu Offline** příkaz odešle požadavek do zařízení a svazek převést do režimu offline. Pokud hostitelích stále používají svazek, výsledkem je přerušená připojení, ale přepínat do offline režimu svazek k selhání.

## <a name="delete-a-volume"></a>Odstranění svazku

> [!IMPORTANT]
> Svazku můžete odstranit pouze v případě, že je offline.

Proveďte následující kroky k odstranění svazku.

#### <a name="to-delete-a-volume"></a>Chcete-li odstranit svazek

1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V tabulkovém výpisu zařízení vyberte zařízení, která má svazek, který máte v úmyslu změnit. Klikněte na tlačítko **Nastavení > svazky**.

    ![Přejděte do okna svazky](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Zkontrolujte stav svazku, který chcete odstranit. Pokud svazek, který chcete odstranit není v režimu offline, odpojit, ji nejprve. Postupujte podle kroků v [svazek převést do režimu offline](#take-a-volume-offline).
4. Jakmile je svazek offline, vyberte svazek, kliknutím pravým tlačítkem myši vyvolejte místní nabídku a zvolte **odstranit**.

    ![Vyberte odstranit z místní nabídky](./media/storsimple-8000-manage-volumes-u2/deletevol1.png)

5. V **odstranit** okno, zkontrolujte a zaškrtněte políčko proti následky odstranění svazku. Když odstraníte svazek, dojde ke ztrátě všech dat, která se nacházejí na svazku. 

    ![Uložte a potvrďte změny](./media/storsimple-8000-manage-volumes-u2/deletevol2.png)

6. Po odstranění svazku tabulkový seznam svazků aktualizuje k označení odstranění.

    ![Seznam aktualizovaných svazků](./media/storsimple-8000-manage-volumes-u2/deletevol3.png)
   
   > [!NOTE]
   > Pokud odstraníte místně vázaný svazek, volné místo dostupné pro nové svazky nemusí aktualizovat okamžitě. Volné místo dostupné aktualizace služby StorSimple Device Manager pravidelně. Doporučujeme, abyste pro že Počkejte několik minut, než se pokusíte vytvořit nového svazku.
   >
   > Navíc pokud odstraníte místně vázaný svazek a potom odstraňte jiné místně připnutý svazek okamžitě později, odstranění svazku, ve kterém úlohy se spouští sekvenčně. První úloha odstranění svazků musí dokončit před zahájením další úlohy odstranění svazku.

## <a name="monitor-a-volume"></a>Monitorování svazku

Monitorování svazku umožňuje shromažďovat I O souvisejícím s/Statistika pro svazek. Monitorování je povoleno standardně pro prvních 32 svazků, které vytvoříte. Monitorování další svazky je ve výchozím nastavení zakázáno. 

> [!NOTE]
> Monitorování naklonované svazků je ve výchozím nastavení zakázáno.


Proveďte následující kroky k povolení nebo zakázání monitorování svazku.

#### <a name="to-enable-or-disable-volume-monitoring"></a>K povolení nebo zakázání monitorování svazku

1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V tabulkovém výpisu zařízení vyberte zařízení, která má svazek, který máte v úmyslu změnit. Klikněte na tlačítko **Nastavení > svazky**.
2. V tabulkovém výpisu svazků vyberte svazek a klikněte pravým tlačítkem myši vyvolejte místní nabídku. Vyberte **upravit**.
3. V **změnu svazku se** okně pro **monitorování** vyberte **povolit** nebo **zakázat** k povolení nebo zakázání monitorování.

    ![Zákaz monitorování](./media/storsimple-8000-manage-volumes-u2/monitorvol1.png) 

4. Klikněte na tlačítko **Uložit** a po zobrazení výzvy k potvrzení, klikněte na tlačítko **Ano**. Na webu Azure portal zobrazí oznámení o aktualizaci svazku a pak zprávu o úspěšném dokončení po úspěšné aktualizaci svazku.

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [klonovat svazek StorSimple](storsimple-8000-clone-volume-u2.md).
* Zjistěte, jak [použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

