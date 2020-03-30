---
title: Správa svazků StorSimple (aktualizace 3)
description: Vysvětluje, jak přidat, upravit, sledovat a odstranit svazky StorSimple a jak je v případě potřeby převést do offline.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 12/08/2017
ms.author: alkohli
ms.openlocfilehash: f32f8925bca33d90afa48071d0c0944ba63861cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254765"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-volumes-update-3-or-later"></a>Ke správě svazků použijte službu StorSimple Device Manager (aktualizace 3 nebo novější)

## <a name="overview"></a>Přehled

Tento kurz vysvětluje, jak pomocí služby StorSimple Device Manager vytvářet a spravovat svazky na zařízeních řady StorSimple 8000 se spuštěnou aktualizací 3 a novějšími.

Služba StorSimple Device Manager je rozšíření na webu Azure Portal, které umožňuje spravovat vaše řešení StorSimple z jediného webového rozhraní. Pomocí portálu Azure můžete spravovat svazky na všech svých zařízeních. Můžete také vytvářet a spravovat služby StorSimple, spravovat zařízení, zásady zálohování a katalog zálohování a zobrazovat výstrahy.

## <a name="volume-types"></a>Typy svazků

StorSimple svazky mohou být:

* **Místně vázaných svazků**: Data v těchto svazcích zůstane na místním zařízení StorSimple po celou dobu.
* **Vrstvené svazky**: Data v těchto svazcích se mohou přelít do cloudu.

Archivní svazek je typ vrstveného svazku. Větší velikost bloku deduplikace používaná pro archivní svazky umožňuje zařízení přenášet větší segmenty dat do cloudu.

V případě potřeby můžete změnit typ svazku z místní na vrstvené nebo z vrstvené na místní. Další informace naleznete v části [Změna typu svazku](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Místně připojené svazky

Místně vázaných svazků jsou plně zřízené svazky, které nejsou vrstvené data do cloudu, čímž se zajistí místní záruky pro primární data, nezávisle na připojení ke cloudu. Data na místně vázaných svazcích nejsou deduplikována a komprimována. snímky místně vázaných svazků jsou však deduplikovány. 

Místně vázaných svazků jsou plně zřízeny; proto musíte mít dostatek místa v zařízení při jejich vytváření. Na zařízení StorSimple 8100 a 20 TB na zařízení 8600 můžete zřídit místně připojené svazky do maximální velikosti 8 TB. StorSimple rezervuje zbývající místní místo v zařízení pro snímky, metadata a zpracování dat. Velikost místně vázanýho svazku můžete zvětšit na maximální dostupné místo, ale nemůžete zmenšit velikost svazku po vytvoření.

Když vytvoříte místně vázaný svazek, zmenší se dostupné místo pro vytváření vrstvených svazků. Platí také opak: pokud máte existující vrstvené svazky, místo, které je k dispozici pro vytváření místně vázaných svazků, bude nižší než maximální limity uvedené výše. Další informace o místních svazcích naleznete v [často kladených otázkách týkajících se místně vázaných svazků](storsimple-8000-local-volume-faq.md).

### <a name="tiered-volumes"></a>Vrstvené svazky

Vrstvené svazky jsou řídce zřízené svazky, ve kterých často používaná data zůstávají místní v zařízení a méně často používaná data se automaticky vrství do cloudu. Tenké zřizování je virtualizační technologie, ve které se zdá, že dostupné úložiště překračuje fyzické prostředky. Místo vyhrazení dostatečné úložiště předem, StorSimple používá tenké zřizování přidělit dostatek místa pro splnění aktuálních požadavků. Elastická povaha cloudového úložiště usnadňuje tento přístup, protože StorSimple může zvýšit nebo snížit cloudové úložiště tak, aby splňovalo měnící se požadavky.

Pokud používáte vrstvený svazek pro archivní data, zaškrtněte políčko **Použít tento svazek pro méně často přistupující archivní data,** chcete-li změnit velikost bloku deduplikace svazku na 512 kB. Pokud tuto možnost nevyberete, odpovídající vrstvený svazek použije velikost bloku 64 kB. Větší velikost deduplikačního bloku dat umožňuje zařízení urychlit přenos velkých objemů archivních dat do cloudu.


### <a name="provisioned-capacity"></a>Zřízená kapacita

Maximální zřízená kapacita pro každé zařízení a typ svazku naleznete v následující tabulce. (Všimněte si, že místně vázaných svazků nejsou k dispozici na virtuálním zařízení.)

|  | Maximální velikost vrstveného svazku | Maximální místně vázaný objemový objem |
| --- | --- | --- |
| **Fyzická zařízení** | | |
| 8100 |64 TB |8 TB |
| 8600 |64 TB |20 TB |
| **Virtuální zařízení** | | |
| 8010 |30 TB |Není dostupné. |
| 8020 |64 TB |Není dostupné. |

## <a name="the-volumes-blade"></a>Kotouč objemů

Okno **Svazky** umožňuje spravovat svazky úložiště, které jsou zřízeny na zařízení Microsoft Azure StorSimple pro iniciátory (servery). Zobrazí seznam svazků na zařízeních StorSimple připojených k vaší službě.

 ![Stránka Svazky](./media/storsimple-8000-manage-volumes-u2/volumeslist.png)

Svazek se skládá z řady atributů:

* **Název svazku** – popisný název, který musí být jedinečný a pomáhá identifikovat svazek. Tento název se používá také v monitorovacích sestavách při filtrování na určitém svazku. Jakmile je svazek vytvořen, nelze jej přejmenovat.
* **Stav** – může být online nebo offline. Pokud je svazek offline, není viditelný pro iniciátory (servery), kterým je povolen přístup k použití svazku.
* **Kapacita** – určuje celkové množství dat, která může iniciátor (server) uložit. Místně vázaných svazků jsou plně zřízena a umístěny na zařízení StorSimple. Vrstvené svazky jsou řídce zřízené a data jsou deduplikovány. U tence zřízených svazků vaše zařízení předem nepřiděluje fyzickou úložnou kapacitu interně ani v cloudu podle nakonfigurované kapacity svazku. Objemová kapacita je přidělena a spotřebována na vyžádání.
* **Typ** – označuje, zda je svazek **vrstvený** (výchozí) nebo **místně vázaný**.

Pomocí pokynů v tomto kurzu proveďte následující úkoly:

* Přidání svazku 
* Úprava svazku 
* Změna typu svazku
* Odstranění svazku 
* Přepne svazek offline 
* Sledování hlasitosti 

## <a name="add-a-volume"></a>Přidání svazku

Během nasazení zařízení řady StorSimple 8000 jste [vytvořili svazek.](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) Přidání svazku je podobný postup.

#### <a name="to-add-a-volume"></a>Přidání svazku

1. V tabulkovém výpisu zařízení v okně **Zařízení** vyberte vaše zařízení. Klikněte na **+ Přidat svazek**.

    ![Přidání nového svazku](./media/storsimple-8000-manage-volumes-u2/step5createvol1.png)

2. V okně **Přidat svazek**:
   
    1. Do pole **Vybrat zařízení** se automaticky vyplní vaše aktuální zařízení.

    2. Z rozevíracího seznamu vyberte kontejner svazků, do kterého potřebujete přidat svazek.

    3.  Zadejte **Název** svazku. Po vytvoření svazku nelze svazek přejmenovat.

    4. V rozevíracím seznamu vyberte **Typ** svazku. Pro úlohy, které vyžadují místní záruky, nízkou latenci a vyšší výkon, vyberte typ svazku **Místně vázaný**. Pro ostatní typy dat vyberte typ svazku **Vrstvený**. Pokud tento svazek používáte k archivaci dat, zaškrtněte políčko **Použít tento svazek pro archivní data s méně častým přístupem**.
      
       Vrstvený svazek je zřizovaný dynamicky a lze jej rychle vytvořit. Výběrem možnosti **Použít tento svazek pro archivní data s méně častým přístupem** u vrstveného svazku určeného k uchovávání archivních dat změníte velikost bloku odstranění duplicit vašeho svazku na 512 kB. Pokud políčko není zaškrtnuté, příslušný vrstvený svazek bude používat velikost bloku 64 kB. Větší velikost deduplikačního bloku dat umožňuje zařízení urychlit přenos velkých objemů archivních dat do cloudu.
       
       Místně vázaný svazek je tlustě zřízený a zajišťuje, že primární data uložená ve svazku zůstanou uložená v místním zařízení a nebudou distribuována do cloudu.  Pokud vytvoříte místně vázaný svazek, zařízení zkontroluje dostupné místo v místních vrstvách a zřídí svazek požadované velikosti. Při operaci vytváření místně vázaného svazku mohou být existující data ze zařízení distribuována do cloudu a vytvoření svazku může trvat dlouhou dobu. Celková doba závisí na velikosti zřizovaného svazku, dostupné šířce pásma sítě a datech uložených v zařízení.

    5. Zadejte hodnotu **Zřízená kapacita** svazku. Poznamenejte si kapacitu dostupnou na základě vybraného typu svazku. Zadaná velikost svazku nesmí překročit velikost dostupného místa.
      
       Zařízení 8100 umožňuje zřizovat místně vázané svazky o velikosti až 8.5 TB a vrstvené svazky o velikosti až 200 TB. Větší zařízení 8600 umožňuje zřizovat místně vázané svazky o velikosti až 22.5 TB a vrstvené svazky o velikosti až 500 TB. Protože k hostování fungující sady vrstvených svazků je vyžadováno volné místo v zařízení, vytváření místně vázaných svazků ovlivňuje volné místo dostupné ke zřizování vrstvených svazků. Proto pokud vytvoříte místně připojený svazek, zmenší se dostupné volné místo pro vytváření vrstvených svazků. Podobně pokud vytvoříte vrstvený svazek, zmenší se dostupné volné místo potřebné k vytváření místně vázaných svazků.
      
       Pokud v zařízení 8100 zřídíte místně vázaný svazek o velikosti 8.5 TB (maximální možná velikost), vyčerpáte tím veškeré volné místo dostupné v zařízení. Od této chvíle už nebudete moct vytvořit žádné vrstvené svazky, protože v zařízení už nebude žádné volné místo k hostování pracovní sady vrstveného svazku. Objem dostupného volného místa ovlivňují také vrstvené svazky. Pokud například používáte zařízení 8100, ve kterém jsou už zřízeny vrstvené svazky o velikosti zhruba 106 TB, k vytváření místně vázaných svazků zbude už jenom 4 TB dostupného volného místa.

    6. V poli **Připojení hostitelé** klikněte na šipku. V okně **Připojení hostitelé** zvolte existující ACR nebo přidejte nový ACR. Pokud zvolíte nový acr, zadejte **název** pro acr, zadejte **iSCSI kvalifikovaný název** (IQN) hostitele systému Windows. Pokud název IQN nemáte, přejděte do části Získání názvu hostitele se systémem Windows Server. Klikněte na **Vytvořit**. Vytvoří se svazek se zadaným nastavením.

        ![Kliknutí na Vytvořit](./media/storsimple-8000-manage-volumes-u2/step5createvol3.png)

Nový svazek je nyní připraven k použití.

> [!NOTE]
> Pokud vytvoříte místně vázaný svazek a poté vytvoříte další místně vázaný svazek ihned poté, úlohy vytváření svazků se spustí postupně. První úloha vytvoření svazku musí být dokončena před zahájením další úlohy vytváření svazku.

## <a name="modify-a-volume"></a>Úprava svazku

Upravte svazek, když ho potřebujete rozbalit nebo změnit hostitele, kteří k němu přistupují.

> [!IMPORTANT]
> * Pokud změníte velikost svazku v zařízení, je třeba změnit velikost svazku také na hostiteli.
> * Zde popsané kroky na straně hostitele jsou pro Systém Windows Server 2012 (2012R2). Postupy pro Linux nebo jiné hostitelské operační systémy se budou lišit. Při úpravách svazku na hostiteli s jiným operačním systémem se řiďte pokyny k hostitelskému operačnímu systému.

#### <a name="to-modify-a-volume"></a>Úprava svazku

1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V tabulkovém výpisu zařízení vyberte zařízení, které má svazek, který chcete upravit. Klepněte **na položku Nastavení > svazků**.

    ![Přejít na Okno Objemy](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

2. Z tabulkového výpisu svazků vyberte svazek a klepnutím pravým tlačítkem myši vyvoláte místní nabídku. Chcete-li převést svazek, který budete upravovat offline, vyberte **možnost Převést do offline.**

    ![Výběr a převedení hlasitosti offline](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. V okně **Převzít offline** zkontrolujte dopad převedení svazku do offline a zaškrtněte odpovídající políčko. Ujistěte se, že odpovídající svazek na hostiteli je nejprve offline. Informace o tom, jak převést svazek do režimu offline na hostitelském serveru připojeném k programu StorSimple, naleznete v pokynech specifických pro operační systém. Klepněte na **tlačítko Přejít do offline**.

    ![Kontrola dopadu offline objemu](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

4. Po vypnutí svazku (jak ukazuje stav svazku) vyberte svazek a klepnutím pravým tlačítkem myši vyvoláte místní nabídku. Vyberte **Změnit hlasitost**.

    ![Vybrat změnit svazek](./media/storsimple-8000-manage-volumes-u2/modifyvol9.png)


5. V okně **Změnit hlasitost** můžete provést následující změny:
   
   1. **Název** svazku nelze upravit.
   2. Převeďte **typ** z místně připnutý na vrstvené nebo z vrstvené na místně připnuté (viz [Změna typu svazku](#change-the-volume-type) pro další informace).
   3. Zvýšit **zřízenou kapacitu**. **Zřízenou kapacitu** lze pouze zvýšit. Svazek nelze po jeho vytvoření zmenšit.
   4. V **části Připojená hostitelé**můžete acr upravit. Chcete-li upravit acr, svazek musí být offline.

       ![Kontrola dopadu offline objemu](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

5. Kliknutím na **Uložit** uložte změny. Po zobrazení výzvy k potvrzení klikněte na **Ano**. Na portálu Azure se zobrazí aktualizační zpráva o svazku. Zobrazí se zpráva o úspěchu, pokud byl svazek úspěšně aktualizován.

    ![Kontrola dopadu offline objemu](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

7. Pokud rozšiřujete svazek, proveďte v hostitelském počítači se systémem Windows následující kroky:
   
   1. Přejděte na**správu disků** **pro správu** ->počítače .
   2. Klepněte pravým tlačítkem myši na **položku Správa disků** a vyberte možnost **Znovu prohledávat disky**.
   3. V seznamu disků vyberte aktualizovaný svazek, klepněte pravým tlačítkem myši a vyberte **možnost Rozšířit svazek**. Spustí se Průvodce rozšířením svazku. Klikněte na **Další**.
   4. Dokončete průvodce přijetím výchozích hodnot. Po dokončení průvodce by měl svazek zobrazit zvětšenou velikost.
      
      > [!NOTE]
      > Pokud rozbalíte místně vázaný svazek a potom rozbalíte další místně vázaný svazek ihned poté, úlohy rozšíření svazku se spustí postupně. První úloha rozšíření svazku musí být dokončena před zahájením další úlohy rozšíření svazku.
      

## <a name="change-the-volume-type"></a>Změna typu svazku

Typ svazku můžete změnit z vrstveného na místně připnutý nebo z místně připnutý na vrstvený. Tento převod by však neměl být častým výskytem.

### <a name="tiered-to-local-volume-conversion-considerations"></a>Vrstvené na místní objem převodu úvahy

Některé důvody pro převod svazku z vrstveného na místně vázaný jsou:

* Místní záruky týkající se dostupnosti a výkonu dat
* Eliminace latence cloudu a problémy s připojením cloudu.

Obvykle se jedná o malé existující svazky, ke kterým chcete často přistupovat. Místně vázaný svazek je plně zřízena při jeho vytvoření. 

Pokud převádíte vrstvený svazek na místně vázaný svazek, StorSimple ověří, že máte dostatek místa v zařízení před zahájením převodu. Pokud nemáte dostatek místa, zobrazí se chyba a operace bude zrušena. 

> [!NOTE]
> Než začnete konverzi z vrstvené na místně připnuté, ujistěte se, že jste zvážili požadavky na místo ostatních úloh. 

Převod z vrstveného na místně vázaný svazek může nepříznivě ovlivnit výkon zařízení. Následující faktory mohou navíc prodloužit dobu potřebný k dokončení převodu:

* Není dostatečná šířka pásma.
* Neexistuje žádná aktuální záloha.

Chcete-li minimalizovat účinky, které tyto faktory mohou mít:

* Zkontrolujte zásady omezení šířky pásma a ujistěte se, že je k dispozici vyhrazená šířka pásma 40 Mb/s.
* Naplánujte konverzi na pracovní dobu mimo špičku.
* Před zahájením převodu pořizovat snímek cloudu.

Pokud převádíte více svazků (podporujete různé úlohy), měli byste určit prioritu převodu svazku tak, aby byly svazky s vyšší prioritou převedeny jako první. Před převodem svazků s úlohami sdílení souborů byste například měli převést svazky, které hostují virtuální počítače (VM) nebo svazky s úlohami SQL.

### <a name="local-to-tiered-volume-conversion-considerations"></a>Aspekty převodu místního na vrstvený objem

Pokud potřebujete další místo pro zřízení dalších svazků, můžete změnit místně vázaný svazek na vrstvený svazek. Když převedete místně vázaný svazek na vrstvený, dostupná kapacita na zařízení se zvětší o velikost uvolněné kapacity. Pokud problémy s připojením brání převodu svazku z místního typu na vrstvený typ, bude místní svazek vykazovat vlastnosti vrstveného svazku, dokud nebude převod dokončen. Důvodem je, že některá data mohla být vylita do cloudu. Tato rozlitá data nadále zabírají místní místo v zařízení, které nelze uvolnit, dokud není operace restartována a dokončena.

> [!NOTE]
> Převod svazku může nějakou dobu trvat a po spuštění nelze převod zrušit. Svazek zůstane během převodu online a můžete zálohovat, ale nemůžete svazek rozbalit ani obnovit během převodu.


#### <a name="to-change-the-volume-type"></a>Změna typu svazku

1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V tabulkovém výpisu zařízení vyberte zařízení, které má svazek, který chcete upravit. Klepněte **na položku Nastavení > svazků**.

    ![Přejít na Okno Objemy](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Z tabulkového výpisu svazků vyberte svazek a klepnutím pravým tlačítkem myši vyvoláte místní nabídku. Vyberte **Změnit**.

    ![Výběr možnosti změnit z místní nabídky](./media/storsimple-8000-manage-volumes-u2/changevoltype2.png)

4. V okně **Změnit hlasitost** změňte typ svazku výběrem nového typu z rozevíracího seznamu **Typ.**
   
   * Pokud měníte typ **místně připnuté**, StorSimple zkontroluje, zda je dostatečná kapacita.
   * Pokud měníte typ na **Vrstvený** a tento svazek se použije pro archivní data, zaškrtněte políčko **Použít tento svazek pro méně často používaná archivní data.**
   * Pokud konfigurujete místně vázaný svazek jako vrstvený nebo _naopak_, zobrazí se následující zpráva.
   
     ![Změna zprávy o typu svazku](./media/storsimple-8000-manage-volumes-u2/changevoltype3.png)

7. Kliknutím na **Uložit** uložte změny. Po zobrazení výzvy k potvrzení spusťte proces převodu klepnutím na tlačítko **Ano.** 

    ![Uložit a potvrdit](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

8. Portál Azure zobrazí oznámení pro vytvoření úlohy, která by svazek aktualizovala. Kliknutím na oznámení můžete sledovat stav úlohy převodu objemu.

    ![Úloha pro převod objemu](./media/storsimple-8000-manage-volumes-u2/changevoltype5.png)

## <a name="take-a-volume-offline"></a>Přepne svazek offline

Při plánování úpravy nebo odstranění svazku může být nutné převést svazek do offline. Pokud je svazek offline, není k dispozici pro přístup pro čtení a zápis. Svazek je nutné přepnout do úpáze na hostiteli a v zařízení.

#### <a name="to-take-a-volume-offline"></a>Přepne svazek offline

1. Před přepnutím do provozu se ujistěte, že daný svazek není používán.
2. Nejprve přenesete svazek do úpájení na hostiteli. Tím se eliminuje potenciální riziko poškození dat na svazku. Konkrétní kroky naleznete v pokynech pro hostitelský operační systém.
3. Po vypnutí hostitele převeďte svazek zařízení do offline provedením následujících kroků:
   
    1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V tabulkovém výpisu zařízení vyberte zařízení, které má svazek, který chcete upravit. Klepněte **na položku Nastavení > svazků**.

        ![Přejít na Okno Objemy](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

    2. Z tabulkového výpisu svazků vyberte svazek a klepnutím pravým tlačítkem myši vyvoláte místní nabídku. Chcete-li převést svazek, který budete upravovat offline, vyberte **možnost Převést do offline.**

        ![Výběr a převedení hlasitosti offline](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. V okně **Převzít offline** zkontrolujte dopad převedení svazku do offline a zaškrtněte odpovídající políčko. Klepněte na **tlačítko Přejít do offline**. 

    ![Kontrola dopadu offline objemu](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)
      
      Budete upozorněni, když je svazek offline. Stav svazku se také aktualizuje na offline.
      
4. Pokud po vypnutí svazku vyberete svazek a klepnete pravým tlačítkem myši, bude v místní nabídce k dispozici možnost **Převést do režimu online.**

> [!NOTE]
> Příkaz **Převést offline** odešle zařízení požadavek na přepzení svazku do offline. Pokud hostitelé stále používají svazek, výsledkem je přerušené připojení, ale převedení svazku offline se nezdaří.

## <a name="delete-a-volume"></a>Odstranění svazku

> [!IMPORTANT]
> Svazek můžete odstranit pouze v případě, že je offline.

Chcete-li svazek odstranit, proveďte následující kroky.

#### <a name="to-delete-a-volume"></a>Odstranění svazku

1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V tabulkovém výpisu zařízení vyberte zařízení, které má svazek, který chcete upravit. Klepněte **na položku Nastavení > svazků**.

    ![Přejít na Okno Objemy](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Zkontrolujte stav svazku, který chcete odstranit. Pokud svazek, který chcete odstranit, není offline, přenesete jej nejprve do offline. Postupujte podle pokynů v [části Převedení svazku do funkce offline](#take-a-volume-offline).
4. Po vypnutí svazku vyberte svazek, klepnutím pravým tlačítkem myši vyvoláte místní nabídku a pak vyberte **odstranit**.

    ![Vybrat odstranit z místní nabídky](./media/storsimple-8000-manage-volumes-u2/deletevol1.png)

5. V okně **Odstranit** zkontrolujte a zaškrtněte políčko proti dopadu odstranění svazku. Po odstranění svazku dojde ke ztrátě všech dat, která jsou na svazku umístěna. 

    ![Uložení a potvrzení změn](./media/storsimple-8000-manage-volumes-u2/deletevol2.png)

6. Po odstranění svazku se aktualizuje tabulkový seznam svazků, který odstranění označuje.

    ![Aktualizovaný seznam svazků](./media/storsimple-8000-manage-volumes-u2/deletevol3.png)
   
   > [!NOTE]
   > Pokud odstraníte místně vázaný svazek, místo, které je k dispozici pro nové svazky, nemusí být okamžitě aktualizováno. Služba StorSimple Device Manager pravidelně aktualizuje místní prostor, který je k dispozici. Doporučujeme počkat několik minut, než se pokusíte vytvořit nový svazek.
   >
   > Navíc pokud odstraníte místně vázaný svazek a potom odstranit jiný místně vázaný svazek ihned poté, úlohy odstranění svazku spustit postupně. První úloha odstranění svazku musí být dokončena před zahájením další úlohy odstranění svazku.

## <a name="monitor-a-volume"></a>Sledování hlasitosti

Sledování objemu umožňuje shromažďovat statistiky související s vstupně-in pro svazek. Monitorování je ve výchozím nastavení povoleno pro prvních 32 svazků, které vytvoříte. Sledování dalších svazků je ve výchozím nastavení zakázáno. 

> [!NOTE]
> Monitorování klonovaných svazků je ve výchozím nastavení zakázáno.


Chcete-li povolit nebo zakázat monitorování svazku, proveďte následující kroky.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Povolení nebo zakázání sledování hlasitosti

1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V tabulkovém výpisu zařízení vyberte zařízení, které má svazek, který chcete upravit. Klepněte **na položku Nastavení > svazků**.
2. Z tabulkového výpisu svazků vyberte svazek a klepnutím pravým tlačítkem myši vyvoláte místní nabídku. Vyberte **Změnit**.
3. V okně **Změnit hlasitost** vyberte v části **Sledování** **možnost Povolit** nebo **Zakázat,** chcete-li monitorování povolit nebo zakázat.

    ![Zákaz monitorování](./media/storsimple-8000-manage-volumes-u2/monitorvol1.png) 

4. Klikněte na **Uložit** a po zobrazení výzvy k potvrzení klepněte na tlačítko **Ano**. Portál Azure zobrazí oznámení o aktualizaci svazku a pak zprávu o úspěchu po úspěšné aktualizaci svazku.

## <a name="next-steps"></a>Další kroky

* Naučte se [klonovat svazek StorSimple](storsimple-8000-clone-volume-u2.md).
* Přečtěte si, jak [používat službu StorSimple Device Manager ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

