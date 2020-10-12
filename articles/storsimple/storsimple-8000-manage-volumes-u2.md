---
title: Správa svazků StorSimple (aktualizace 3)
description: Vysvětluje, jak přidat, upravit, monitorovat a odstranit svazky StorSimple a jak je v případě potřeby převést do režimu offline.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 12/08/2017
ms.author: alkohli
ms.openlocfilehash: 920f7f2fa6fb6e80b060f0308b124a3a1214cb3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86204459"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-volumes-update-3-or-later"></a>Použití služby StorSimple Device Manager ke správě svazků (Update 3 nebo novější)

## <a name="overview"></a>Přehled

V tomto kurzu se dozvíte, jak pomocí služby StorSimple Device Manager vytvářet a spravovat svazky na zařízeních řady StorSimple 8000 s aktualizací Update 3 nebo novější.

Služba StorSimple Device Manager je rozšíření v Azure Portal, které umožňuje spravovat řešení StorSimple z jediného webového rozhraní. Pomocí Azure Portal můžete spravovat svazky na všech svých zařízeních. Můžete také vytvářet a spravovat služby StorSimple Services, spravovat zařízení, zásady zálohování a katalog záloh a zobrazovat výstrahy.

## <a name="volume-types"></a>Typy svazků

StorSimple svazky mohou být:

* **Místně připojené svazky**: data na těchto svazcích zůstávají vždy na místním zařízení StorSimple.
* **Vrstvené svazky**: data v těchto svazcích můžou přelití do cloudu.

Archivní svazek je typ vrstveného svazku. Větší velikost bloku dat, která se používá pro archivované svazky, umožňuje zařízení přenášet do cloudu větší segmenty dat.

V případě potřeby můžete změnit typ svazku z místní na vrstvený nebo z vrstveného na místní. Další informace získáte, když přejdete na [změnit typ svazku](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Místně připojené svazky

Místně připnuté svazky jsou plně zřízené svazky, které neobsahují data v cloudu, což zajišťuje místní záruky primárních dat, nezávisle na cloudovém připojení. Data na místně připojených svazcích nejsou zbavená duplicit a komprimovaná; snímky místně připnutého svazku se ale odstraňují. 

Místně připnuté svazky jsou plně zřízené; Proto musíte mít při vytváření zařízení dostatek místa. Na zařízení StorSimple 8100 můžete zřídit místně připojené svazky až do maximální velikosti 8 TB a na zařízení 8600 20 TB. StorSimple rezervuje zbývající místní prostor na zařízení pro snímky, metadata a zpracování dat. Velikost místně připnutého svazku můžete zvětšit na maximální dostupné místo, ale velikost svazku po jeho vytvoření nemůžete zmenšit.

Při vytváření místně připnutého svazku se zmenší dostupné místo pro vytváření vrstvených svazků. Také platí, že pokud máte existující vrstvené svazky, volné místo pro vytváření místně připojených svazků bude nižší než maximální výše uvedená omezení. Další informace o místních svazcích najdete v [nejčastějších dotazech na místně připojených svazcích](storsimple-8000-local-volume-faq.md).

### <a name="tiered-volumes"></a>Vrstvené svazky

Vrstvené svazky jsou dynamicky zřízené svazky, ve kterých se často používaná data na zařízení ukládají místně a méně často používaná data se automaticky vrství do cloudu. Dynamické zajišťování je virtualizační technologie, ve které se zdá, že dostupné úložiště překračuje fyzické prostředky. Místo opětovné obsluhy dostatečného úložného prostoru StorSimple využívá dynamické zajišťování k přidělení dostatečného prostoru pro splnění aktuálních požadavků. Elastická povaha cloudového úložiště usnadňuje tento přístup, protože StorSimple může zvýšit nebo snížit cloudové úložiště tak, aby splňovalo měnící se požadavky.

Pokud používáte vrstvený svazek pro Archivovaná data, zaškrtněte políčko **použít tento svazek pro archivní data méně často** používaných ke změně velikosti bloku dat odstranění duplicit pro svazek na 512 KB. Pokud tuto možnost nevyberete, bude odpovídající vrstvený svazek používat velikost bloku 64 KB. Větší velikost deduplikačního bloku dat umožňuje zařízení urychlit přenos velkých objemů archivních dat do cloudu.


### <a name="provisioned-capacity"></a>Zřízená kapacita

V následující tabulce najdete maximální zřízenou kapacitu pro každé zařízení a typ svazku. (Upozorňujeme, že místně připojené svazky nejsou k dispozici na virtuálním zařízení.)

| Typ | Maximální velikost vrstveného svazku | Maximální velikost místně připnutého svazku |
| --- | --- | --- |
| **Fyzická zařízení** | | |
| 8100 |64 TB |8 TB |
| 8600 |64 TB |20 TB |
| **Virtuální zařízení** | | |
| 8010 |30 TB |Není k dispozici |
| 8020 |64 TB |Není k dispozici |

## <a name="the-volumes-blade"></a>Okno svazky

Okno **svazky** umožňuje spravovat svazky úložiště, které jsou zřízené na Microsoft Azure StorSimple zařízení pro vaše iniciátory (servery). Zobrazuje seznam svazků na zařízeních StorSimple připojených ke službě.

 ![Stránka svazky](./media/storsimple-8000-manage-volumes-u2/volumeslist.png)

Svazek se skládá z řady atributů:

* **Název svazku** – popisný název, který musí být jedinečný a pomáhá identifikovat svazek. Tento název se používá také v sestavách monitorování při filtrování na konkrétním svazku. Po vytvoření se svazek nedá přejmenovat.
* **Stav** – může být online nebo offline. Pokud je svazek offline, není viditelný pro iniciátory (servery), které mají povolený přístup k použití svazku.
* **Capacity** – Určuje celkové množství dat, které může iniciátor (Server) uložit. Místně připnuté svazky jsou plně zřízené a nacházejí se na zařízení StorSimple. Vrstvené svazky se dynamicky zřídí a data se odstraňují z duplicit. V případě dynamicky zajištěných svazků vaše zařízení předem nepřiřazuje kapacitu fyzického úložiště interně nebo v cloudu v závislosti na konfiguraci kapacity svazku. Kapacita svazku se přiděluje a spotřebovává na vyžádání.
* **Typ** – určuje, jestli je svazek **vrstvený** (výchozí) nebo **připojený místně**.

Pomocí pokynů v tomto kurzu proveďte následující úlohy:

* Přidat svazek 
* Úprava svazku 
* Změnit typ svazku
* Odstranění svazku 
* Přepnout svazek do offline režimu 
* Monitorování svazku 

## <a name="add-a-volume"></a>Přidat svazek

Během nasazování zařízení řady StorSimple 8000 jste [vytvořili svazek](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) . Přidání svazku je podobný postup.

#### <a name="to-add-a-volume"></a>Přidání svazku

1. V tabulkovém výpisu zařízení v okně **Zařízení** vyberte vaše zařízení. Klikněte na **+ Přidat svazek**.

    ![Přidání nového svazku](./media/storsimple-8000-manage-volumes-u2/step5createvol1.png)

2. V okně **Přidat svazek**:
   
    1. Do pole **Vybrat zařízení** se automaticky vyplní vaše aktuální zařízení.

    2. Z rozevíracího seznamu vyberte kontejner svazků, do kterého potřebujete přidat svazek.

    3.  Zadejte **Název** svazku. Po vytvoření svazku se svazek nedá přejmenovat.

    4. V rozevíracím seznamu vyberte **Typ** svazku. Pro úlohy, které vyžadují místní záruky, nízkou latenci a vyšší výkon, vyberte typ svazku **Místně vázaný**. Pro ostatní typy dat vyberte typ svazku **Vrstvený**. Pokud tento svazek používáte k archivaci dat, zaškrtněte políčko **Použít tento svazek pro archivní data s méně častým přístupem**.
      
       Vrstvený svazek je zřizovaný dynamicky a lze jej rychle vytvořit. Výběrem možnosti **Použít tento svazek pro archivní data s méně častým přístupem** u vrstveného svazku určeného k uchovávání archivních dat změníte velikost bloku odstranění duplicit vašeho svazku na 512 kB. Pokud políčko není zaškrtnuté, příslušný vrstvený svazek bude používat velikost bloku 64 kB. Větší velikost deduplikačního bloku dat umožňuje zařízení urychlit přenos velkých objemů archivních dat do cloudu.
       
       Místně vázaný svazek je tlustě zřízený a zajišťuje, že primární data uložená ve svazku zůstanou uložená v místním zařízení a nebudou distribuována do cloudu.  Pokud vytvoříte místně vázaný svazek, zařízení zkontroluje dostupné místo v místních vrstvách a zřídí svazek požadované velikosti. Při operaci vytváření místně vázaného svazku mohou být existující data ze zařízení distribuována do cloudu a vytvoření svazku může trvat dlouhou dobu. Celková doba závisí na velikosti zřizovaného svazku, dostupné šířce pásma sítě a datech uložených v zařízení.

    5. Zadejte hodnotu **Zřízená kapacita** svazku. Poznamenejte si kapacitu dostupnou na základě vybraného typu svazku. Zadaná velikost svazku nesmí překročit velikost dostupného místa.
      
       Zařízení 8100 umožňuje zřizovat místně vázané svazky o velikosti až 8.5 TB a vrstvené svazky o velikosti až 200 TB. Větší zařízení 8600 umožňuje zřizovat místně vázané svazky o velikosti až 22.5 TB a vrstvené svazky o velikosti až 500 TB. Protože k hostování fungující sady vrstvených svazků je vyžadováno volné místo v zařízení, vytváření místně vázaných svazků ovlivňuje volné místo dostupné ke zřizování vrstvených svazků. Proto pokud vytvoříte místně připojený svazek, zmenší se dostupné volné místo pro vytváření vrstvených svazků. Podobně pokud vytvoříte vrstvený svazek, zmenší se dostupné volné místo potřebné k vytváření místně vázaných svazků.
      
       Pokud v zařízení 8100 zřídíte místně vázaný svazek o velikosti 8.5 TB (maximální možná velikost), vyčerpáte tím veškeré volné místo dostupné v zařízení. Od této chvíle už nebudete moct vytvořit žádné vrstvené svazky, protože v zařízení už nebude žádné volné místo k hostování pracovní sady vrstveného svazku. Objem dostupného volného místa ovlivňují také vrstvené svazky. Pokud například používáte zařízení 8100, ve kterém jsou už zřízeny vrstvené svazky o velikosti zhruba 106 TB, k vytváření místně vázaných svazků zbude už jenom 4 TB dostupného volného místa.

    6. V poli **Připojení hostitelé** klikněte na šipku. V okně **připojení hostitelé** vyberte existující ACR nebo přidejte nové ACR. Pokud zvolíte novou ACR, zadejte název vašeho hostitele Windows, zadejte **název** IQN ( **iSCSI Qualified Name** ). Pokud název IQN nemáte, přejděte do části Získání názvu hostitele se systémem Windows Server. Klikněte na **Vytvořit**. Vytvoří se svazek se zadaným nastavením.

        ![Kliknutí na Vytvořit](./media/storsimple-8000-manage-volumes-u2/step5createvol3.png)

Váš nový svazek je teď připravený k použití.

> [!NOTE]
> Pokud vytvoříte místně připojený svazek a pak ihned vytvoříte další místně připojený svazek, úlohy vytváření svazků se spustí sekvenčně. První úloha vytvoření svazku musí být dokončena, než bude možné začít úlohu vytváření dalšího svazku.

## <a name="modify-a-volume"></a>Úprava svazku

Upravte svazek, pokud ho potřebujete rozšířit, nebo změňte hostitele, kteří přistupují ke svazku.

> [!IMPORTANT]
> * Pokud upravíte velikost svazku v zařízení, musí se na hostiteli změnit i velikost svazku.
> * Postup na straně hostitele, který je zde popsán, je pro systém Windows Server 2012 (2012R2). Postupy pro Linux nebo jiné hostitelské operační systémy se budou lišit. Pokud upravujete svazek na hostiteli, na kterém běží jiný operační systém, přečtěte si pokyny k hostitelskému operačnímu systému.

#### <a name="to-modify-a-volume"></a>Úprava svazku

1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V tabulkovém výpisu zařízení vyberte zařízení, které má svazek, který chcete upravit. Klikněte na **nastavení > svazky**.

    ![Okno přejít do svazků](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

2. V tabulkovém výpisu svazků vyberte svazek a kliknutím pravým tlačítkem vyvolání místní nabídky. Vyberte možnost **převést do režimu offline** , pokud chcete změnit svazek, který budete upravovat offline.

    ![Výběr a převedení svazku do režimu offline](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. V okně **převést do režimu offline** zkontrolujte dopad přepnutí svazku do režimu offline a zaškrtněte příslušné políčko. Ujistěte se, že je odpovídající svazek na hostiteli nejprve offline. Informace o tom, jak převést svazek do režimu offline na hostitelském serveru připojeném k StorSimple, najdete v pokynech pro konkrétní operační systém. Klikněte na **převést do režimu offline**.

    ![Kontrola dopadu převedení svazku do režimu offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

4. Jakmile je svazek offline (jak je znázorněno v části stav svazku), vyberte svazek a kliknutím pravým tlačítkem vyvolání místní nabídky. Vyberte **Upravit svazek**.

    ![Vyberte změnit svazek](./media/storsimple-8000-manage-volumes-u2/modifyvol9.png)


5. V okně **Upravit svazek** můžete provést následující změny:
   
   1. **Název** svazku nelze upravit.
   2. Převést **typ** z místně připnuté na vrstvený nebo z vrstveného na místně připnuté (Další informace najdete v tématu [Změna typu svazku](#change-the-volume-type) ).
   3. Zvyšte **zřízenou kapacitu**. **Zřízenou kapacitu** je možné zvýšit. Po vytvoření se svazek nedá zmenšit.
   4. V části **připojení hostitelé**můžete upravit ACR. Pokud chcete upravit ACR, musí být svazek offline.

       ![Kontrola dopadu převedení svazku do režimu offline](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

5. Kliknutím na **Uložit** uložte změny. Po zobrazení výzvy k potvrzení klikněte na **Ano**. V Azure Portal se zobrazí zpráva o aktualizaci svazku. Po úspěšné aktualizaci svazku se zobrazí zpráva o úspěchu.

    ![Kontrola dopadu převedení svazku do režimu offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

7. Pokud rozbalíte svazek, proveďte následující kroky na hostitelském počítači s Windows:
   
   1. Přejít do správy disků v **nástroji Správa počítače**  -> **Disk Management**.
   2. Klikněte pravým tlačítkem na **Správa disků** a vyberte znovu **Prohledat disky**.
   3. V seznamu disků vyberte svazek, který jste aktualizovali, klikněte na něj pravým tlačítkem myši a vyberte možnost **Zvětšit svazek**. Spustí se Průvodce rozšířeným svazkem. Klikněte na **Next** (Další).
   4. Dokončete průvodce a přijměte výchozí hodnoty. Po dokončení průvodce by měl svazek Zobrazit větší velikost.
      
      > [!NOTE]
      > Pokud rozbalíte místně připojený svazek a následně ihned rozbalíte další místně připojený svazek, úlohy rozšíření svazku se spustí sekvenčně. První úloha rozšíření svazku musí být dokončena předtím, než může začít další úloha rozšíření svazku.
      

## <a name="change-the-volume-type"></a>Změnit typ svazku

Typ svazku můžete změnit z vrstveného na místně připnuté nebo z místně připnuté na vrstvený. Tento převod by však neměl být často výskyt.

### <a name="tiered-to-local-volume-conversion-considerations"></a>S ohledem na převod místních svazků na převod

Mezi důvody pro převedení svazku z vrstveného na místně připnuté jsou tyto:

* Místní záruky týkající se dostupnosti a výkonu dat
* Eliminujte latence v cloudu a problémy s cloudovým připojením.

Obvykle se jedná o malé existující svazky, ke kterým chcete často přistupovat. Místně připojený svazek je při vytvoření plně zřízený. 

Pokud převedete vrstvené svazky na místně připojený svazek, StorSimple ověří, že máte na svém zařízení dostatek místa, než spustí převod. Pokud nemáte dostatek místa, dojde k chybě a operace se zruší. 

> [!NOTE]
> Než začnete s převodem z vrstveného na místně připnuté, nezapomeňte zvážit požadavky na prostor pro ostatní úlohy. 

Převod z vrstveného na místně připojený svazek může negativně ovlivnit výkon zařízení. Kromě toho mohou následující faktory prodloužit dobu potřebnou k dokončení převodu:

* Šířka pásma není dostatečná.
* Neexistuje žádná aktuální záloha.

Chcete-li minimalizovat důsledky, které tyto faktory mohou mít:

* Zkontrolujte zásady omezování šířky pásma a ujistěte se, že je k dispozici vyhrazená šířka pásma 40 MB/s.
* Naplánujte převod na dobu mimo špičku.
* Před zahájením převodu si Převezměte snímek cloudu.

Pokud převádíte více svazků (s podporou různých úloh), měli byste upřednostnit převod svazku, aby se nejdříve převedly svazky s vyšší prioritou. Před převodem svazků s úlohami sdílení souborů byste například měli převést svazky, které hostují virtuální počítače nebo svazky s úlohami SQL.

### <a name="local-to-tiered-volume-conversion-considerations"></a>Požadavky na převod z místního na vrstvený svazek

Pokud potřebujete další místo pro zřízení dalších svazků, můžete chtít změnit místně připojený svazek na vrstvený svazek. Když převedete místně připojený svazek na vrstvený, zvětší se dostupná kapacita zařízení o velikost uvolněné kapacity. Pokud potíže s připojením znemožňují převod svazku z místního typu na vrstvený typ, na místním svazku se zobrazí vlastnosti vrstveného svazku, dokud se převod nedokončí. Důvodem je to, že některá data mohla být předaná do cloudu. Tato nepřesahující data nadále zabírají místní prostor na zařízení, které není možné uvolnit, dokud se operace nerestartuje a nedokončí.

> [!NOTE]
> Převod svazku může nějakou dobu trvat a převod nelze po jeho spuštění zrušit. Svazek zůstane v online režimu a můžete provádět zálohování, ale během konverze se svazek nedá rozšířit ani obnovit.


#### <a name="to-change-the-volume-type"></a>Změna typu svazku

1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V tabulkovém výpisu zařízení vyberte zařízení, které má svazek, který chcete upravit. Klikněte na **nastavení > svazky**.

    ![Okno přejít do svazků](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. V tabulkovém výpisu svazků vyberte svazek a kliknutím pravým tlačítkem vyvolání místní nabídky. Vyberte **Upravit**.

    ![V místní nabídce vyberte změnit.](./media/storsimple-8000-manage-volumes-u2/changevoltype2.png)

4. V okně **Upravit svazek** změňte typ svazku tak, že v rozevíracím seznamu **typ** vyberete nový typ.
   
   * Pokud měníte typ na **místně připnuté**, StorSimple zkontroluje, jestli existuje dostatečná kapacita.
   * Pokud měníte typ na **vrstvený** a tento svazek bude použit pro Archivovaná data, zaškrtněte políčko **použít tento svazek pro archivní data, ke kterým se přistupuje méně často** .
   * Pokud konfigurujete místně připojený svazek jako vrstvený nebo _naopak_, zobrazí se následující zpráva.
   
     ![Změna typu svazku zpráva](./media/storsimple-8000-manage-volumes-u2/changevoltype3.png)

7. Kliknutím na **Uložit** uložte změny. Po zobrazení výzvy k potvrzení klikněte na **Ano** a zahajte proces převodu. 

    ![Uložit a potvrdit](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

8. Azure Portal zobrazí oznámení o vytvoření úlohy, která by aktualizovala svazek. Kliknutím na oznámení můžete monitorovat stav úlohy převodu svazku.

    ![Úloha pro převod svazku](./media/storsimple-8000-manage-volumes-u2/changevoltype5.png)

## <a name="take-a-volume-offline"></a>Přepnout svazek do offline režimu

Pokud plánujete upravit nebo odstranit svazek, možná budete muset svazek převést do režimu offline. Pokud je svazek offline, není k dispozici pro přístup pro čtení i zápis. Svazek je nutné převést do režimu offline na hostiteli a v zařízení.

#### <a name="to-take-a-volume-offline"></a>Převedení svazku do režimu offline

1. Před přepnutím do režimu offline se ujistěte, že se svazek nepoužívá.
2. Napřed si svazek na hostiteli převeďte do režimu offline. Tím se eliminuje případné riziko poškození dat na svazku. Konkrétní postup najdete v pokynech k hostitelskému operačnímu systému.
3. Jakmile je hostitel v režimu offline, převeďte svazek na zařízení do offline režimu provedením následujících kroků:
   
    1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V tabulkovém výpisu zařízení vyberte zařízení, které má svazek, který chcete upravit. Klikněte na **nastavení > svazky**.

        ![Okno přejít do svazků](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

    2. V tabulkovém výpisu svazků vyberte svazek a kliknutím pravým tlačítkem vyvolání místní nabídky. Vyberte možnost **převést do režimu offline** , pokud chcete změnit svazek, který budete upravovat offline.

        ![Výběr a převedení svazku do režimu offline](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. V okně **převést do režimu offline** zkontrolujte dopad přepnutí svazku do režimu offline a zaškrtněte příslušné políčko. Klikněte na **převést do režimu offline**. 

    ![Kontrola dopadu převedení svazku do režimu offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)
      
      Když je svazek offline, zobrazí se vám oznámení. Stav svazku se taky aktualizuje na offline.
      
4. Pokud je svazek offline, pokud vyberete svazek a kliknete pravým tlačítkem, bude možnost **převést online** v místní nabídce k dispozici.

> [!NOTE]
> Příkaz **převést do režimu offline** odešle požadavek na zařízení pro převedení svazku do režimu offline. Pokud hostitel stále používají svazek, výsledkem je přerušená připojení, ale přepnutí svazku do režimu offline selže.

## <a name="delete-a-volume"></a>Odstranění svazku

> [!IMPORTANT]
> Svazek lze odstranit pouze v případě, že je offline.

Provedením následujících kroků odstraňte svazek.

#### <a name="to-delete-a-volume"></a>Odstranění svazku

1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V tabulkovém výpisu zařízení vyberte zařízení, které má svazek, který chcete upravit. Klikněte na **nastavení > svazky**.

    ![Okno přejít do svazků](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Ověřte stav svazku, který chcete odstranit. Pokud se svazek, který chcete odstranit, nenachází v režimu offline, nejprve ho převeďte do režimu offline. Postupujte podle kroků v části [převedení svazku do režimu offline](#take-a-volume-offline).
4. Po převedení svazku na offline vyberte svazek, klikněte pravým tlačítkem na vyvolání kontextové nabídky a pak vyberte **Odstranit**.

    ![Vyberte možnost odstranit z kontextové nabídky.](./media/storsimple-8000-manage-volumes-u2/deletevol1.png)

5. V okně **Odstranit** zkontrolujte a zaškrtněte políčko s dopadem na odstranění svazku. Při odstraňování svazku dojde ke ztrátě všech dat, která se nachází na svazku. 

    ![Uložit a potvrdit změny](./media/storsimple-8000-manage-volumes-u2/deletevol2.png)

6. Po odstranění svazku se Tabulkový seznam svazků aktualizuje, aby označoval odstranění.

    ![Aktualizovaný seznam svazků](./media/storsimple-8000-manage-volumes-u2/deletevol3.png)
   
   > [!NOTE]
   > Odstraníte-li místně připojený svazek, nebude možné okamžitě aktualizovat místo dostupné pro nové svazky. Služba StorSimple Device Manager v pravidelných intervalech aktualizuje místně dostupné místní místo. Doporučujeme počkat několik minut, než se pokusíte vytvořit nový svazek.
   >
   > Pokud navíc odstraníte místně připojený svazek a následně ihned odstraníte další místně připojený svazek, úlohy odstranění svazku se spustí sekvenčně. První úloha odstranění svazku musí být dokončena, než bude možné zahájit další úlohu odstranění svazku.

## <a name="monitor-a-volume"></a>Monitorování svazku

Monitorování hlasitosti umožňuje shromáždit statistiku týkající se vstupně-výstupních operací pro svazek. Monitorování je ve výchozím nastavení povolené pro prvních 32 svazků, které vytvoříte. Monitorování dalších svazků je ve výchozím nastavení zakázáno. 

> [!NOTE]
> Monitorování klonovaných svazků je ve výchozím nastavení zakázáno.


Provedením následujících kroků povolíte nebo zakážete monitorování svazku.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Povolení nebo zakázání monitorování svazků

1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V tabulkovém výpisu zařízení vyberte zařízení, které má svazek, který chcete upravit. Klikněte na **nastavení > svazky**.
2. V tabulkovém výpisu svazků vyberte svazek a kliknutím pravým tlačítkem vyvolání místní nabídky. Vyberte **Upravit**.
3. V okně **Upravit svazek** zvolte pro **monitorování** možnost **Povolit** nebo **Zakázat** , aby bylo monitorování povoleno nebo zakázáno.

    ![Zákaz monitorování](./media/storsimple-8000-manage-volumes-u2/monitorvol1.png) 

4. Klikněte na **Uložit** a po zobrazení výzvy k potvrzení klikněte na **Ano**. Azure Portal po úspěšné aktualizaci svazku zobrazuje oznámení o aktualizaci svazku a následné zprávě o úspěchu.

## <a name="next-steps"></a>Další kroky

* Naučte se [klonovat svazek StorSimple](storsimple-8000-clone-volume-u2.md).
* Naučte se [používat službu StorSimple Device Manager ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

