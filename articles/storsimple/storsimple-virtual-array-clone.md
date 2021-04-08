---
title: Naklonování zálohy virtuálního pole StorSimple | Microsoft Docs
description: Naučte se klonovat zálohu a obnovit soubor z virtuálního pole StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: af6e979c-55e3-477c-b53e-a76a697f80c9
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: b6cc89cb082cd0ed32abd88e3a6683c60a27ba90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023036"
---
# <a name="clone-from-a-backup-of-your-storsimple-virtual-array"></a>Klonování ze zálohy vašeho virtuálního pole StorSimple

## <a name="overview"></a>Přehled

Tento článek popisuje podrobný postup klonování zálohovací sady sdílených složek nebo svazků ve vašem Microsoft Azure StorSimplem virtuálním poli. Klonovaná záloha se používá k obnovení odstraněného nebo ztraceného souboru. Článek také obsahuje podrobné kroky k provedení obnovení na úrovni položek ve virtuálním poli StorSimple, které je nakonfigurováno jako souborový server.

## <a name="clone-shares-from-a-backup-set"></a>Klonování sdílených složek ze zálohovacího skladu

**Než se pokusíte klonovat sdílené složky, ujistěte se, že máte na zařízení dostatek místa k dokončení této operace.** Pokud chcete klonovat ze zálohy, v [Azure Portal](https://portal.azure.com/)proveďte následující kroky.

#### <a name="to-clone-a-share"></a>Naklonování sdílené složky

1. Přejděte na okno **zařízení** . Vyberte zařízení a klikněte na něj a pak klikněte na **sdílené složky**. Vyberte sdílenou složku, kterou chcete klonovat, klikněte pravým tlačítkem na sdílenou složku a vyvolejte místní nabídku. Vyberte **Clone** (Klonovat).
   
   ![Klonování zálohy](./media/storsimple-virtual-array-clone/cloneshare1.png)
2. V okně **klonovat** klikněte na **zálohovat > vyberte** a pak postupujte takto: 
   
   a.    Vyfiltrujte v tomto zařízení zálohu na základě časového rozsahu. Můžete si vybrat z **posledních 7 dní**, **posledních 30 dní** a **minulého roku**.
   
   b.    V zobrazeném seznamu filtrovaných záloh vyberte zálohu, ze které chcete klonovat.
   
   c.    Klikněte na **OK**.
   
   ![Klonování zálohy 2](./media/storsimple-virtual-array-clone/cloneshare3.png)
3. V okně **klonovat** klikněte na **Nastavení cíle** a pak postupujte takto:
   
   a.    Zadejte název sdílené složky. Název sdílené složky musí obsahovat 3-127 znaků.
   
   b.    Volitelně můžete zadat popis klonované sdílené složky.
   
   c.    Nemůžete změnit typ sdílené složky, na kterou obnovujete. Vrstvená sdílená složka se naklonuje jako vrstvená a místně připnutá sdílená složka jako připnuté místně.
   
   d.    Kapacita se nastaví tak, aby se rovnala velikosti sdílené složky, ze které se klonuje.
   
   e.    Přiřaďte správce pro tuto sdílenou složku. Po dokončení klonování budete moci upravit vlastnosti sdílení prostřednictvím Průzkumníka souborů.
   
   f.    Klikněte na **OK**.
   
   ![Klonování zálohy 3](./media/storsimple-virtual-array-clone/cloneshare6.png)

4. Kliknutím na **klonování** spusťte úlohu klonování. Po dokončení úlohy se spustí operace klonování a vy budete upozorněni. Pokud chcete monitorovat průběh klonování, přejděte do okna **úlohy** a kliknutím na úlohu zobrazte podrobnosti o úloze.
5. Po úspěšném vytvoření klonu přejděte zpátky do okna **sdílené složky** na vašem zařízení.
6. Nově naklonovaná sdílená složka se teď dá zobrazit v seznamu sdílených složek na vašem zařízení. Vrstvená sdílená složka je klonována jako vrstvená a místně připnuté sdílená složka jako místně připnuté sdílení.
   
   ![Klonování zálohy 4](./media/storsimple-virtual-array-clone/cloneshare10.png)

## <a name="clone-volumes-from-a-backup-set"></a>Klonování svazků ze zálohovacího skladu

Pokud chcete klonovat ze zálohy, musíte v Azure Portal při klonování sdílené složky provést postup podobný těm. Operace klonování naklonuje zálohu na nový svazek na stejném virtuálním zařízení. nemůžete klonovat na jiné zařízení.

#### <a name="to-clone-a-volume"></a>Naklonování svazku

1. Přejděte na okno **zařízení** . Vyberte zařízení a klikněte na něj a pak klikněte na **svazky**. Vyberte svazek, který chcete klonovat, klikněte pravým tlačítkem na svazek, aby se vyvolala kontextová nabídka. Vyberte **Clone** (Klonovat).
   
   ![Klonování svazku](./media/storsimple-virtual-array-clone/clonevolume1.png)
2. V okně **klonovat** klikněte na **zálohovat** a pak postupujte takto: 
   
   a.    Vyfiltrujte v tomto zařízení zálohu na základě časového rozsahu. Můžete si vybrat z **posledních 7 dní**, **posledních 30 dní** a **minulého roku**. 
   
   b.    V zobrazeném seznamu filtrovaných záloh vyberte zálohu, ze které chcete klonovat.
   
   c.    Klikněte na **OK**.
   
   ![Klonovat svazek 2](./media/storsimple-virtual-array-clone/clonevolume3.png)
3. V okně **klonovat** klikněte na **nastavení cílového svazku** a pak proveďte následující kroky:
   
   a. Název zařízení se vyplní automaticky.
   
   b. Zadejte název svazku **naklonovaného svazku**. Název svazku musí obsahovat 3 až 127 znaků.
   
   c. Typ svazku je automaticky nastaven na původní svazek. Vrstvený svazek je klonován jako vrstvený a místně připojený svazek jako připnuté místně.
   
   d. Pro **připojené hostitele** klikněte na **Vybrat**.
   
   ![Klonovat svazek 3](./media/storsimple-virtual-array-clone/clonevolume4.png)
4. V okně **připojení hostitelé** vyberte z existujícího ACR nebo přidejte nový ACR. Pokud chcete přidat nový ACR, budete muset zadat ACR název a hostitele IQN. Klikněte na **Vybrat**.
   
   ![Klonování svazku 4](./media/storsimple-virtual-array-clone/clonevolume5.png)
5. Kliknutím na **klonování** spusťte úlohu klonování.
   
   ![Klonovat svazek 5](./media/storsimple-virtual-array-clone/clonevolume6.png)  
6. Po vytvoření úlohy klonování se spustí klonování. Jakmile se klon vytvoří, zobrazí se v okně svazky na vašem zařízení. Počítejte s tím, že vrstvený svazek je klonován jako vrstvený a místně připojený svazek je klonován jako místně připojený svazek.
   
   ![Klonovat svazek 6](./media/storsimple-virtual-array-clone/clonevolume8.png)
7. Po zobrazení svazku v seznamu svazků online se svazek dá použít k dispozici. V hostiteli iniciátoru iSCSI aktualizujte seznam cílů v okně Vlastnosti iniciátoru iSCSI. Nový cíl, který obsahuje název klonovaného svazku, by měl být ve sloupci Stav zobrazen jako neaktivní.
8. Vyberte cíl a klikněte na **připojit**. Po připojení iniciátoru k cíli by měl být stav změněn na **připojeno**.
9. V okně **Správa disků** se připojené svazky zobrazí, jak je znázorněno na následujícím obrázku. Klikněte pravým tlačítkem myši na zjištěný svazek (klikněte na název disku) a potom klikněte na **Online**.

> [!IMPORTANT]
> Pokud se pokusíte naklonovat svazek nebo sdílenou složku ze zálohovacího skladu, v případě, že se úloha klonování nepodaří, může být na portálu stále vytvořen cílový svazek nebo sdílená složka. Je důležité odstranit tento cílový svazek nebo sdílenou složku na portálu, abyste minimalizovali případné budoucí problémy vyplývající z tohoto prvku.
> 
> 

## <a name="item-level-recovery-ilr"></a>Obnovení na úrovni položek (ILR)

Tato verze zavádí obnovení na úrovni položek (ILR) ve virtuálním poli StorSimple nakonfigurovaném jako souborový server. Tato funkce umožňuje provádět podrobné obnovení souborů a složek z cloudové zálohy všech sdílených složek na zařízení StorSimple. Odstraněné soubory můžete načíst z nedávných záloh pomocí modelu samoobslužné služby.

Každá sdílená složka má složku *.* backups obsahující nejnovější zálohy. Můžete přejít na požadovanou zálohu, zkopírovat příslušné soubory a složky ze zálohy a obnovit je. Tato funkce eliminuje volání správců pro obnovení souborů ze záloh.

1. Při provádění ILR můžete zobrazit zálohy prostřednictvím Průzkumníka souborů. Klikněte na konkrétní sdílenou složku, pro kterou chcete najít zálohu. Ve sdílené složce, ve které jsou uloženy všechny zálohy, se zobrazí složka *.* backups. Rozbalte složku *. zálohy* a zobrazte zálohy. Složka zobrazuje rozložený pohled na celou hierarchii zálohování. Toto zobrazení se vytvoří na vyžádání a obvykle se vytvoří jenom pár sekund.
   
   Tímto způsobem se zobrazí posledních pět záloh a dá se použít k provedení obnovení na úrovni položek. Pět nedávných záloh zahrnuje výchozí naplánované i ruční zálohy.
   
   * **Naplánované zálohy** pojmenované jako &lt; název zařízení &gt; parametr DAILYSCHEDULE-RRRRMMDD-HHMMSS-UTC.
   * **Ruční zálohy** s názvem ad-hoc-RRRRMMDD-HHMMSS-UTC.
     
     ![Snímek obrazovky s průzkumníkem souborů, kde se nachází složka. backups V této složce je vybrána složka ruční zálohy.](./media/storsimple-virtual-array-clone/image14.png)

2. Identifikujte zálohu obsahující nejnovější verzi odstraněného souboru. I když název složky obsahuje časové razítko UTC v každém z předchozích případů, čas vytvoření složky je skutečný čas zařízení při spuštění zálohování. K vyhledání a identifikaci záloh použijte časové razítko složky.

3. Vyhledejte složku nebo soubor, který chcete obnovit v záloze, kterou jste identifikovali v předchozím kroku. Všimněte si, že můžete zobrazit pouze soubory nebo složky, pro které máte oprávnění. Pokud nemůžete získat přístup k určitým souborům nebo složkám, obraťte se na správce sdílené složky. Správce může pomocí Průzkumníka souborů upravit oprávnění ke sdílení a dát vám přístup k určitému souboru nebo složce. Doporučeným postupem je, že správce sdílení je skupina uživatelů místo jednoho uživatele.

4. Zkopírujte soubor nebo složku do příslušné sdílené složky na StorSimple souborovém serveru.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [spravovat virtuální pole StorSimple pomocí místního webového uživatelského rozhraní](storsimple-ova-web-ui-admin.md).

