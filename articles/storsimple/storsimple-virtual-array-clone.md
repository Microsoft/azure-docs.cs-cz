---
title: Vytvořit kopii zálohy pole virtuální zařízení StorSimple | Microsoft Docs
description: Naučte se vytvořit kopii zálohy a obnovit soubor z pole virtuální zařízení StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: af6e979c-55e3-477c-b53e-a76a697f80c9
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: 768c9a1c906999f4690c9c8f7d075743ab1678ff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875988"
---
# <a name="clone-from-a-backup-of-your-storsimple-virtual-array"></a>Klonování ze záloh pole virtuální zařízení StorSimple

## <a name="overview"></a>Přehled

Tento článek popisuje podrobný postup klonovat zálohovacího skladu sdílené složky nebo svazky na vaše virtuální pole Microsoft Azure StorSimple. Klonovaný zálohování se používá k obnovení odstraněných nebo ke ztrátě souboru. Článek obsahuje taky podrobné pokyny k provedení obnovení na úrovni položek na pole virtuální zařízení StorSimple nakonfigurovaný jako souborový server.

## <a name="clone-shares-from-a-backup-set"></a>Klonování sdílené složky ze zálohovacího skladu

**Před pokusem o klonování sdílené složky, ujistěte se, že máte dostatek místa na zařízení k dokončení této operace.** Klonujte pouze ze zálohy, [portál Azure](https://portal.azure.com/), proveďte následující kroky.

#### <a name="to-clone-a-share"></a>Klonování sdílené složky

1. Přejděte do **zařízení** okno. Vyberte a klikněte na zařízení a pak klikněte na tlačítko **sdílené složky**. Vyberte sdílenou složku, kterou chcete vytvořit kopii, klikněte pravým tlačítkem na sdílenou složku, která má být vyvolán v místní nabídce. Vyberte **klon**.
   
   ![Vytvořit kopii zálohy](./media/storsimple-virtual-array-clone/cloneshare1.png)
2. V **klon** okně klikněte na tlačítko **zálohování > vyberte** a proveďte následující kroky: 
   
   a.    Filtrujte zálohování v tomto zařízení na základě rozsahu čas. Můžete si vybrat z **za posledních 7 dní**, **za posledních 30 dnů**, a **za poslední rok**.
   
   b.    V seznamu zobrazí filtrované záloh vyberte zálohu klonovat z.
   
   c.    Klikněte na **OK**.
   
   ![Vytvořit kopii zálohy](./media/storsimple-virtual-array-clone/cloneshare3.png)
3. V **klon** okně klikněte na tlačítko **cíle nastavení** a proveďte následující kroky:
   
   a.    Zadejte název sdílené složky. Název sdílené složky musí obsahovat 3 127 znaků.
   
   b.    Volitelně zadejte popis pro klonovaný sdílenou složku.
   
   c.    Nemůžete změnit typ sdílené složky, které obnovujete. Sdílenou složku vrstvené naklonována jako vrstvené a sdílenou složku místně vázaný jako místně vázaný.
   
   d.    Kapacitu nastavena na velikost sdílené složky, které jsou klonování z jako hodnotu.
   
   e.    Přiřazení správců pro tuto sdílenou složku. Bude moct změnit vlastností sdílené složky pomocí Průzkumníka souborů po dokončení klonování.
   
   f.    Klikněte na **OK**.
   
   ![Vytvořit kopii zálohy](./media/storsimple-virtual-array-clone/cloneshare6.png)

4. Klikněte na tlačítko **klon** při spuštění úlohy klonování. Po dokončení úlohy se spustí operace klonování a zobrazí se upozornění. Chcete-li sledovat průběh klonování, přejděte na **úlohy** okně a klikněte na úlohu chcete-li zobrazit podrobnosti úlohy.
5. Po úspěšném vytvoření klonu se přejděte zpět **sdílené složky** okno na vašem zařízení.
6. Nyní můžete zobrazit novou sdílenou složku klonovaný v seznamu sdílených složek ve vašem zařízení. Sdílenou složku vrstvené naklonována jako vrstvené a místně vázaný sdílenou složku jako sdílenou složku místně vázaný.
   
   ![Vytvořit kopii zálohy](./media/storsimple-virtual-array-clone/cloneshare10.png)

## <a name="clone-volumes-from-a-backup-set"></a>Klonování svazků ze zálohovacího skladu

Klonování ze zálohy, na portálu Azure, budete muset provést kroky, které jsou podobné těm, které jsou při klonování sdílenou složku. Operace klonování provede klonování zálohování do nového svazku na stejný virtuální zařízení; Nelze klonovat na jiném zařízení.

#### <a name="to-clone-a-volume"></a>Klonování svazku

1. Přejděte do **zařízení** okno. Vyberte a klikněte na zařízení a pak klikněte na tlačítko **svazky**. Vybr svazku, který chcete vytvořit kopii, klikněte pravým tlačítkem na svazku, který má být vyvolán v místní nabídce. Vyberte **klon**.
   
   ![Klonování svazku](./media/storsimple-virtual-array-clone/clonevolume1.png)
2. V **klon** okně klikněte na tlačítko **zálohování** a proveďte následující kroky: 
   
   a.    Filtrujte zálohování v tomto zařízení na základě rozsahu čas. Můžete si vybrat z **za posledních 7 dní**, **za posledních 30 dnů**, a **za poslední rok**. 
   
   b.    V seznamu zobrazí filtrované záloh vyberte zálohu klonovat z.
   
   c.    Klikněte na **OK**.
   
   ![Vytvořit kopii zálohy](./media/storsimple-virtual-array-clone/clonevolume3.png)
3. V **klon** okně klikněte na tlačítko **cíle svazku nastavení** a proveďte následující kroky::
   
   a. Název zařízení se automaticky vyplní.
   
   b. Zadejte název svazku **klonovat svazku**. Název svazku musí obsahovat 3 až 127 znaků.
   
   c. Typ svazku se automaticky nastaví na původního svazku. Vrstvený svazek je klonovat, protože vrstvené a připnuté místně vázaný svazek jako místně.
   
   d. Pro **připojení hostitele**, klikněte na tlačítko **vyberte**.
   
   ![Vytvořit kopii zálohy](./media/storsimple-virtual-array-clone/clonevolume4.png)
4. V **připojení hostitele** okně, vyberte z existující ACR nebo přidejte nové ACR. Pokud chcete přidat nový ACR, musíte zadat název ACR a IQN hostitele. Klikněte na **Vybrat**.
   
   ![Vytvořit kopii zálohy](./media/storsimple-virtual-array-clone/clonevolume5.png)
5. Klikněte na tlačítko **klon** spustíte úlohu klonování.
   
   ![Vytvořit kopii zálohy](./media/storsimple-virtual-array-clone/clonevolume6.png)  
6. Klonování se spustí po vytvoření úlohy klonování. Po vytvoření klonu se zobrazí v okně svazky na vašem zařízení. Všimněte si, že je jako vrstvené klonovat vrstvený svazek a místně vázaný svazek je klonovat jako místně vázaný svazek.
   
   ![Vytvořit kopii zálohy](./media/storsimple-virtual-array-clone/clonevolume8.png)
7. Jakmile online na svazků, zobrazí se svazek, svazek je k dispozici pro použití. Na hostiteli iniciátoru iSCSI aktualizujte seznam cílů, v okně Vlastnosti iniciátoru iSCSI. Nový cíl, který obsahuje název klonovaného svazku by se zobrazit jako "neaktivní" ve sloupci stav.
8. Vyberte cíl a klikněte na **Connect**. Po iniciátoru je připojený k cíli, měl stav změnit na **připojeno**.
9. V **Správa disků** se připojené svazky okno, jak je znázorněno na následujícím obrázku. Klikněte pravým tlačítkem myši na zjištěný svazek (klikněte na název disku) a potom klikněte na **Online**.

> [!IMPORTANT]
> Při pokusu o klonování svazek nebo sdílenou složku ze zálohy, pokud úloha klonování selže, nastavte cílový svazek nebo sdílenou složku stále možné vytvářet na portálu. Je důležité odstranit tohoto cílového svazku nebo sdílené složky v portálu, chcete-li minimalizovat budoucí potíží vzniklých tohoto elementu.
> 
> 

## <a name="item-level-recovery-ilr"></a>Obnovení na úrovni položek (ILR)

Tato verze přináší obnovení na úrovni položek (ILR) v poli virtuální zařízení StorSimple nakonfigurovaný jako souborový server. Tato funkce umožňuje provádět podrobné obnovení souborů a složek z cloudu zálohu všechny sdílené složky v zařízení StorSimple. Můžete načíst odstraněných souborů z poslední zálohy použití modelu samoobslužné služby.

Každé sdílené složky mají *.backups* složku, která obsahuje poslední zálohy. Můžete přejděte do požadovaného zálohování, zkopírujte příslušné soubory a složky ze zálohy a jejich obnovení. Tato funkce eliminuje volání správcům pro obnovení souborů ze zálohy.

1. Při provádění ILR, můžete zobrazit pomocí Průzkumníka souborů zálohy. Klikněte na konkrétní sdílenou složku, kterou chcete podívejte se na zálohování pro. Zobrazí se *.backups* složky vytvořil v rámci sdílené složce, která ukládá všechny zálohy. Rozbalte *.backups* složky zobrazíte zálohy. Ve složce zobrazují pohledem celý zálohování hierarchie. Toto zobrazení je vytvořený na vyžádání a obvykle trvá jenom pár sekund se má vytvořit.
   
   Posledních pět zálohy jsou zobrazeny tímto způsobem a slouží k provádění obnovení na úrovni položek. Pět nedávné zálohy zahrnují výchozí naplánovaný i ručního zálohování.
   
   * **Naplánované zálohy** pojmenovány jako &lt;název zařízení&gt;DailySchedule RRRRMMDD HHMMSS UTC.
   * **Ruční zálohování** pojmenovány jako Ad-hoc RRRRMMDD-HHMMSS-UTC.
     
     ![](./media/storsimple-virtual-array-clone/image14.png)

2. Identifikujte zálohu obsahující nejnovější verzi souboru odstraněné. V případě, že název složky obsahuje časové razítko UTC v každé z předchozí případů, je čas, kdy byla vytvořena složka zařízení skutečný čas zahájení zálohování. Použijte časové razítko složky vyhledat a identifikovat zálohy.

3. Vyhledejte složku nebo soubor, který chcete obnovit do zálohování, který jste určili v předchozím kroku. Všimněte si, že můžete zobrazit jenom soubory nebo složky, které máte oprávnění pro. Pokud nemají přístup k určité soubory nebo složky, obraťte se na správce sdílené složky. Správce může pomocí Průzkumníka souborů upravit oprávnění ke sdílení a umožňují přístup k určitého souboru nebo složky. Je součástí osvědčeného postupu, správce sdílená složka je skupina uživatelů místo jednoho uživatele.

4. Zkopírujte soubor nebo složku na příslušné sdílené složce na souborovém serveru StorSimple.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak [spravovat vaše pole virtuální zařízení StorSimple pomocí místního webového uživatelského rozhraní](storsimple-ova-web-ui-admin.md).

