---
title: Klonovat zálohu StorSimple Virtual Array | Dokumentace Microsoftu
description: Zjistěte, jak klonovat zálohování a obnovení souboru ze StorSimple Virtual Array.
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
ms.openlocfilehash: feffbb634af62d70a840febcf2a04afb7bdeeddd
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55496785"
---
# <a name="clone-from-a-backup-of-your-storsimple-virtual-array"></a>Klonování ze zálohy StorSimple Virtual Array

## <a name="overview"></a>Přehled

Tento článek popisuje podrobný postup naklonovat sadu záloh sdílených složek nebo svazků na Microsoft Azure StorSimple Virtual Array. Naklonované zálohování se používá k obnovení odstraněných nebo ztráty souboru. Článek taky obsahuje podrobné pokyny k provádění obnovení úrovni položek na StorSimple Virtual Array nakonfigurovali jako souborový server.

## <a name="clone-shares-from-a-backup-set"></a>Klonovat sdílené složky ze zálohovacího skladu

**Před pokusem o klonování sdílené složky, ujistěte se, že máte dostatek volného místa pro dokončení této operace.** Klonování ze zálohy, v [webu Azure portal](https://portal.azure.com/), proveďte následující kroky.

#### <a name="to-clone-a-share"></a>Klonovat sdílenou složku

1. Přejděte do **zařízení** okno. Vyberte a klikněte na své zařízení a potom klikněte na **sdílených složek**. Vyberte sdílenou složku, kterou chcete naklonovat, klikněte pravým tlačítkem na sdílenou složku vyvolejte místní nabídku. Vyberte **klonování**.
   
   ![Vytvořit kopii zálohy](./media/storsimple-virtual-array-clone/cloneshare1.png)
2. V **klonování** okna, klikněte na tlačítko **zálohování > vyberte** a proveďte následující kroky: 
   
   a.    Filtrovat zálohy na tomto zařízení pro časový rozsah. Můžete si vybrat z **posledních 7 dnů**, **za posledních 30 dnů**, a **za poslední rok**.
   
   b.    V seznamu zobrazí filtrovaná zálohy vyberte zálohu, kterou chcete klonovat z.
   
   c.    Klikněte na **OK**.
   
   ![Vytvořit kopii zálohy](./media/storsimple-virtual-array-clone/cloneshare3.png)
3. V **klonování** okna, klikněte na tlačítko **cílit na nastavení** a proveďte následující kroky:
   
   a.    Zadejte název sdílené složky. Název sdílené složky musí obsahovat 3 – 127 znaků.
   
   b.    Volitelně zadejte popis pro Klonovaná sdílená složka.
   
   c.    Typ sdílené složky, kterou provádíte obnovení k nelze změnit. Vrstvenou sdílenou složku se klonují místně vázaný jako místně připojené sdílené složky a vrstvený.
   
   d.    Kapacita je nastavena jako rovna velikosti sdílené složky, které jsou klonování z.
   
   e.    Přiřazení správců pro tuto sdílenou složku. Je možné upravit vlastnosti sdílené složky přes Průzkumníka souborů po dokončení klonování.
   
   f.    Klikněte na **OK**.
   
   ![Vytvořit kopii zálohy](./media/storsimple-virtual-array-clone/cloneshare6.png)

4. Klikněte na tlačítko **klonování** spustit úloha klonování. Po dokončení úlohy se spustí operace klonování a budete upozorněni. Pokud chcete monitorovat průběh klonování, přejděte na **úlohy** okně a klikněte na úlohu chcete-li zobrazit podrobnosti o úloze.
5. Po úspěšném vytvoření klonu přejděte zpět **sdílené složky** okno na vašem zařízení.
6. Nyní uvidíte novou Klonovaná sdílená složka v seznamu sdílených složek ve vašem zařízení. Naklonování vrstvenou sdílenou složku jako vrstvené a místně připojené sdílené složky jako místně připojené sdílené složky.
   
   ![Vytvořit kopii zálohy](./media/storsimple-virtual-array-clone/cloneshare10.png)

## <a name="clone-volumes-from-a-backup-set"></a>Klonování svazků ze zálohovacího skladu

Klonování ze zálohy, na webu Azure Portal, je nutné provést kroky, které jsou podobné těm, které jsou při klonování sdílenou složku. Operace klonování duplicity zálohování na nový svazek na stejném virtuálním zařízení; nemůže klonovat do různých zařízení.

#### <a name="to-clone-a-volume"></a>Klonování svazku

1. Přejděte do **zařízení** okno. Vyberte a klikněte na své zařízení a potom klikněte na **svazky**. Vyberte svazek, který chcete naklonovat, klikněte pravým tlačítkem na svazku, který má vyvolejte místní nabídku. Vyberte **klonování**.
   
   ![Klonování svazku](./media/storsimple-virtual-array-clone/clonevolume1.png)
2. V **klonování** okna, klikněte na tlačítko **zálohování** a proveďte následující kroky: 
   
   a.    Filtrovat zálohy na tomto zařízení pro časový rozsah. Můžete si vybrat z **posledních 7 dnů**, **za posledních 30 dnů**, a **za poslední rok**. 
   
   b.    V seznamu zobrazí filtrovaná zálohy vyberte zálohu, kterou chcete klonovat z.
   
   c.    Klikněte na **OK**.
   
   ![Vytvořit kopii zálohy](./media/storsimple-virtual-array-clone/clonevolume3.png)
3. V **klonování** okna, klikněte na tlačítko **cílit na nastavení pro svazky** a proveďte následující kroky::
   
   a. Název zařízení se automaticky načtou.
   
   b. Zadejte název svazku **klonovat svazek**. Název svazku musí obsahovat 3 až 127 znaků.
   
   c. Typ svazku je automaticky nastaven na původního svazku. Vrstvený svazek je klonovat jako vrstvené a místně vázaný svazek místně připnutý.
   
   d. Pro **připojení hostitelé**, klikněte na tlačítko **vyberte**.
   
   ![Vytvořit kopii zálohy](./media/storsimple-virtual-array-clone/clonevolume4.png)
4. V **připojení hostitelé** okna, vyberte z existující záznam ACR nebo přidat nový záznam ACR. Chcete-li přidat nový záznam ACR, je potřeba zadat název služby ACR a názvu IQN hostitele. Klikněte na **Vybrat**.
   
   ![Vytvořit kopii zálohy](./media/storsimple-virtual-array-clone/clonevolume5.png)
5. Klikněte na tlačítko **klonování** spustit úloha klonování.
   
   ![Vytvořit kopii zálohy](./media/storsimple-virtual-array-clone/clonevolume6.png)  
6. Klonování se spustí po vytvoření úloha klonování. Po vytvoření klonu se zobrazí v okně svazky na vašem zařízení. Všimněte si, že vytvoříte vrstvený svazek je klonovat jako vrstvené a místně vázaný svazek je klonují místně vázaný svazek.
   
   ![Vytvořit kopii zálohy](./media/storsimple-virtual-array-clone/clonevolume8.png)
7. Jakmile online na seznam svazků, zobrazí se svazek, svazek je k dispozici pro použití. Na hostiteli iniciátoru iSCSI aktualizujte seznam cílů v okně Vlastnosti iniciátoru iSCSI. Nový cíl, který obsahuje název klonovaný svazek se zobrazí jako "neaktivní" ve sloupci stav.
8. Vyberte cíl a klikněte na tlačítko **připojit**. Až iniciátor je připojený k cíli, by měl stav změnit na **připojeno**.
9. V **Správa disků** okně připojené svazky se zobrazí, jak je znázorněno na následujícím obrázku. Klikněte pravým tlačítkem myši na zjištěný svazek (klikněte na název disku) a potom klikněte na **Online**.

> [!IMPORTANT]
> Při pokusu o klonování svazku nebo sdílené složky ze zálohy, pokud úloha klonování selže, nastavte cílový svazek nebo sdílenou složku může být stále vytvořené na portálu. Je důležité odstranění tohoto cílového svazku nebo sdílené složky na portálu pro minimalizaci budoucí potíží vzniklých tohoto elementu.
> 
> 

## <a name="item-level-recovery-ilr"></a>Obnovení na úrovni položek (ILR)

Tato verze přináší obnovení na úrovni položek (ILR) na StorSimple Virtual Array nakonfigurovali jako souborový server. Tato funkce umožňuje provést podrobné obnovení souborů a složek ze zálohování v cloudu a všech sdílených složek na zařízení StorSimple. Odstraněné soubory můžete načíst z nedávných záloh pomocí modelu samoobslužné služby.

Každé sdílené složky mají *.backups* složku, která obsahuje nejnovější zálohy. Můžete přecházet do požadovaného zálohování, zkopírujte příslušné soubory a složky ze zálohy a jejich obnovení. Tato funkce eliminuje volání správci pro obnovení souborů ze záloh.

1. Při provádění ILR, můžete zobrazit záloh prostřednictvím Průzkumníka souborů. Kliknutím na konkrétní sdílenou složku, kterou chcete podívat zálohování. Zobrazí se *.backups* složky vytvořené v rámci sdílené složky, která ukládá všechny zálohy. Rozbalte *.backups* složku pro zobrazení záloh. Složka ukazuje pohledem zálohování celé hierarchie. Toto zobrazení je vytvářet na vyžádání a obvykle trvá jenom pár sekund se má vytvořit.
   
   Posledních pět zálohy se zobrazují tímto způsobem a je možné provádět obnovení úrovni položek. Pět nedávných záloh obsahovat výchozí naplánovaný i ručního zálohování.
   
   * **Naplánované zálohy** s názvem jako &lt;název zařízení&gt;parametrem DailySchedule RRRRMMDD HHMMSS UTC.
   * **Ruční zálohování** s názvem, protože Ad-hoc RRRRMMDD-HHMMSS-UTC.
     
     ![](./media/storsimple-virtual-array-clone/image14.png)

2. Identifikujte zálohování obsahující nejnovější verzi odstraněný soubor. I když název složky obsahuje časové razítko UTC ve všech předchozích případech, je čas, kdy byla vytvořena složka skutečné zařízení čas zahájení zálohování. Časové razítko složky můžete vyhledat a identifikovat zálohy.

3. Vyhledejte složku nebo soubor, který chcete obnovit do zálohování, který je identifikován v předchozím kroku. Všimněte si, že si můžete jenom zobrazit soubory nebo složky, ke kterým máte oprávnění pro. Pokud nelze získat přístup k určité soubory nebo složky, obraťte se na správce sdílené složky. Správce může použít Průzkumníka souborů pro úpravy oprávnění ke sdíleným složkám a poskytují přístup k určitého souboru nebo složky. Je součástí osvědčeného, že je sdílená složka správce skupinu uživatelů místo jenom jednoho konkrétního uživatele.

4. Zkopírujte soubor nebo složku do příslušné sdílené složky na souborovém serveru se StorSimple.

## <a name="next-steps"></a>Další postup

Další informace o tom, jak [spravovat StorSimple Virtual Array pomocí místního webového uživatelského rozhraní](storsimple-ova-web-ui-admin.md).

