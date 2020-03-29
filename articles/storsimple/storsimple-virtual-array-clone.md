---
title: Klonovat zálohuji storsimple virtuálního pole | Dokumenty společnosti Microsoft
description: Naučte se klonovat zálohu a obnovit soubor z virtuálního pole StorSimple.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60580789"
---
# <a name="clone-from-a-backup-of-your-storsimple-virtual-array"></a>Klonování ze zálohy virtuálního pole StorSimple

## <a name="overview"></a>Přehled

Tento článek popisuje krok za krokem, jak naklonovat zálohovací sadu vašich sdílených složek nebo svazků na virtuálním poli Microsoft Azure StorSimple. Klonovaná záloha se používá k obnovení odstraněného nebo ztraceného souboru. Článek také obsahuje podrobné kroky k provedení obnovení na úrovni položky na virtuálním poli StorSimple nakonfigurovaném jako souborový server.

## <a name="clone-shares-from-a-backup-set"></a>Klonování sdílených složek ze zálohovacího skladu

**Než se pokusíte klonovat sdílené složky, ujistěte se, že máte dostatek místa na zařízení k dokončení této operace.** Chcete-li klonovat ze zálohy, proveďte na [webu Azure Portal](https://portal.azure.com/)následující kroky.

#### <a name="to-clone-a-share"></a>Klonování sdílené položky

1. Přejděte do okna **Zařízení.** Vyberte zařízení a klikněte na něj a potom klikněte na **Sdílené položky**. Vyberte sdílenou složku, kterou chcete klonovat, klikněte pravým tlačítkem myši na sdílenou složku a vyvoláte místní nabídku. Vyberte **možnost Klonovat**.
   
   ![Klonování zálohy](./media/storsimple-virtual-array-clone/cloneshare1.png)
2. V okně **Klonování** klikněte na **Položku Výběr > zálohování** a pak postupujte takto: 
   
   a.    V yfiltrujte zálohu na tomto zařízení na základě časového rozsahu. Můžete si vybrat z **posledních 7 dní**, posledních **30 dní**a **uplynulých roků**.
   
   b.    V seznamu zobrazených filtrovaných záloh vyberte zálohu, ze které chcete klonovat.
   
   c.    Klikněte na tlačítko **OK**.
   
   ![Klonování zálohy](./media/storsimple-virtual-array-clone/cloneshare3.png)
3. V okně **Klonování** klikněte na **Nastavení cíle** a pak postupujte takto:
   
   a.    Zadejte název sdílené položky. Název sdílené položky musí obsahovat 3-127 znaků.
   
   b.    Volitelně zadejte popis klonované sdílené složky.
   
   c.    Typ sdílené složky, do které obnovujete, nelze změnit. Vrstvená sdílená složky je klonována jako vrstvená a místně připnutá sdílená složky jako místně připnutá.
   
   d.    Kapacita je nastavena jako rovna velikosti sdílené složky, ze které klonujete.
   
   e.    Přiřaďte správci pro tuto sdílenou složku. Po dokončení klonování budete moci upravit vlastnosti sdílené složky prostřednictvím Průzkumníka souborů.
   
   f.    Klikněte na tlačítko **OK**.
   
   ![Klonování zálohy](./media/storsimple-virtual-array-clone/cloneshare6.png)

4. Chcete-li zahájit úlohu klonování, klepněte na tlačítko **Klonovat.** Po dokončení úlohy se spustí operace klonování a budete upozorněni. Chcete-li sledovat průběh klonování, přejděte do okna **Úlohy** a klepnutím na úlohu zobrazte podrobnosti o úloze.
5. Po úspěšném vytvoření klonu přejděte zpět do okna **Sdílené složky** v zařízení.
6. Nyní můžete zobrazit novou klonokovanou sdílenou složku v seznamu sdílených složek v zařízení. Vrstvená sdílená složky je klonována jako vrstvená a místně připnutá sdílená složky jako místně připnutá sdílená složky.
   
   ![Klonování zálohy](./media/storsimple-virtual-array-clone/cloneshare10.png)

## <a name="clone-volumes-from-a-backup-set"></a>Klonování svazků ze zálohovacího skladu

Chcete-li klonovat ze zálohy, na webu Azure Portal, musíte provést kroky podobné těm, které při klonování sdílené složky. Operace klonování klonuje zálohu na nový svazek na stejném virtuálním zařízení. nelze klonovat na jiné zařízení.

#### <a name="to-clone-a-volume"></a>Klonování svazku

1. Přejděte do okna **Zařízení.** Vyberte zařízení a klikněte na něj a potom klikněte na **Svazky**. Vyberte hlasitost, kterou chcete klonovat, klikněte pravým tlačítkem myši na svazek a vyvoláte místní nabídku. Vyberte **možnost Klonovat**.
   
   ![Klonování svazku](./media/storsimple-virtual-array-clone/clonevolume1.png)
2. V okně **Klonování** klikněte na **Zálohovat** a pak postupujte takto: 
   
   a.    V yfiltrujte zálohu na tomto zařízení na základě časového rozsahu. Můžete si vybrat z **posledních 7 dní**, posledních **30 dní**a **uplynulých roků**. 
   
   b.    V seznamu zobrazených filtrovaných záloh vyberte zálohu, ze které chcete klonovat.
   
   c.    Klikněte na tlačítko **OK**.
   
   ![Klonování zálohy](./media/storsimple-virtual-array-clone/clonevolume3.png)
3. V okně **Klonování** klikněte na **Cílové nastavení hlasitosti** a proveďte následující kroky:.
   
   a. Název zařízení je automaticky naplněn.
   
   b. Zadejte název svazku **pro klonovaný svazek**. Název svazku musí obsahovat 3 až 127 znaků.
   
   c. Typ svazku je automaticky nastaven na původní svazek. Vrstvený svazek je klonován jako vrstvený a místně vázaný svazek jako místně vázaný.
   
   d. U **připojených hostitelů**klepněte na tlačítko **Vybrat**.
   
   ![Klonování zálohy](./media/storsimple-virtual-array-clone/clonevolume4.png)
4. V okně **Připojení hostitelé** vyberte z existujícího acr nebo přidejte nový ACR. Chcete-li přidat nový ACR, budete muset zadat název ACR a hostitelské IQN. Klepněte na **tlačítko Vybrat**.
   
   ![Klonování zálohy](./media/storsimple-virtual-array-clone/clonevolume5.png)
5. Kliknutím na **Klonovat** spusťte úlohu klonování.
   
   ![Klonování zálohy](./media/storsimple-virtual-array-clone/clonevolume6.png)  
6. Po vytvoření úlohy klonování se spustí klonování. Jakmile je klon vytvořen, zobrazí se v okně Svazky v zařízení. Všimněte si, že vrstvený svazek je klonován jako vrstvený a místně vázaný svazek je klonován jako místně vázaný svazek.
   
   ![Klonování zálohy](./media/storsimple-virtual-array-clone/clonevolume8.png)
7. Jakmile se svazek zobrazí online v seznamu svazků, je k dispozici pro použití. Na hostiteli iniciátoru iSCSI aktualizujte seznam cílů v okně vlastností iniciátoru iSCSI. Nový cíl, který obsahuje název klonovaného svazku, by se měl ve sloupci stavu zobrazit jako neaktivní.
8. Vyberte cíl a klepněte na **tlačítko Připojit**. Po připojení iniciátoru k cíli by se měl stav změnit na **Připojeno**.
9. V okně **Správa disků** se připojené svazky zobrazí tak, jak je znázorněno na následujícím obrázku. Klikněte pravým tlačítkem myši na zjištěný svazek (klikněte na název disku) a potom klikněte na **Online**.

> [!IMPORTANT]
> Při pokusu o klonování svazku nebo sdílené složky ze zálohovací sady, pokud úloha klonování selže, může být na portálu stále vytvořen cílový svazek nebo sdílená sada. Je důležité odstranit tento cílový svazek nebo sdílet na portálu minimalizovat všechny budoucí problémy vyplývající z tohoto prvku.
> 
> 

## <a name="item-level-recovery-ilr"></a>Obnovení na úrovni položek (ILR)

Tato verze zavádí obnovení na úrovni položky (ILR) na Virtuální pole StorSimple nakonfigurované jako souborový server. Tato funkce umožňuje provést granulární obnovení souborů a složek z cloudové zálohy všech sdílených složek na zařízení StorSimple. Odstraněné soubory z posledních záloh můžete načíst pomocí samoobslužného modelu.

Každá sdílená složka má složku *.backups,* která obsahuje nejnovější zálohy. Můžete přejít na požadovanou zálohu, zkopírovat příslušné soubory a složky ze zálohy a obnovit je. Tato funkce eliminuje volání správců pro obnovení souborů ze záloh.

1. Při provádění ILR, můžete zobrazit zálohy prostřednictvím Průzkumníka souborů. Klikněte na konkrétní sdílenou složku, pro kterou se chcete podívat na zálohu. Zobrazí se složka *.backups* vytvořená pod sdílenou složkou, ve které jsou uloženy všechny zálohy. Rozbalte složku *Zálohy* a zobrazte je. Složka zobrazuje rozložené zobrazení celé hierarchie zálohování. Toto zobrazení je vytvořeno na vyžádání a obvykle trvá pouze několik sekund k vytvoření.
   
   Posledních pět záloh jsou zobrazeny tímto způsobem a lze použít k provedení obnovení na úrovni položky. Pět posledních záloh obsahuje výchozí naplánované i ruční zálohování.
   
   * **Plánované zálohy** s &lt;názvem&gt;Název zařízení DailySchedule-YYYMMDD-HHMMSS-UTC.
   * **Ruční zálohování** s názvem Ad-hoc-YYYMMDD-HHMMSS-UTC.
     
     ![](./media/storsimple-virtual-array-clone/image14.png)

2. Identifikujte zálohu obsahující nejnovější verzi odstraněného souboru. Přestože název složky obsahuje časové razítko UTC v každém z předchozích případů, čas, kdy byla složka vytvořena, je skutečný čas zařízení při spuštění zálohy. Pomocí časového razítka složky vyhledejte a identifikujte zálohy.

3. Vyhledejte složku nebo soubor, který chcete obnovit, v záloze, kterou jste identifikovali v předchozím kroku. Poznámka: Můžete zobrazit pouze soubory nebo složky, ke kterým máte oprávnění. Pokud nemáte přístup k určitým souborům nebo složkám, obraťte se na správce sdílené složky. Správce může pomocí Průzkumníka souborů upravit oprávnění ke sdílení a poskytnout vám přístup k určitému souboru nebo složce. Doporučujeme osvědčeným postupem, aby správce sdílené složky byla skupina uživatelů namísto jednoho uživatele.

4. Zkopírujte soubor nebo složku do příslušné sdílené složky na souborovém serveru StorSimple.

## <a name="next-steps"></a>Další kroky

Další informace o [správě virtuálního pole StorSimple pomocí místního webového uživatelského prostředí](storsimple-ova-web-ui-admin.md).

