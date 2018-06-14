---
title: Klonování svazku na řady StorSimple 8000 | Microsoft Docs
description: Popisuje různé klon typy a využití a vysvětluje, jak můžete pomocí zálohovacího skladu klonování svazku na zařízení řady StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/05/2017
ms.author: alkohli
ms.openlocfilehash: d3965c74d51996b3e4144130fad01fae9e265897
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2017
ms.locfileid: "26333198"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-clone-a-volume"></a>Použít službu StorSimple Manager zařízení na portálu Azure ke klonování svazku

## <a name="overview"></a>Přehled

Tento kurz popisuje, jak můžete pomocí zálohovacího skladu klonování svazku pomocí **katalog zálohování** okno. Také vysvětluje rozdíly mezi *přechodný* a *trvalé* provede klonování. Pokyny v tomto kurzu platí pro všechna zařízení řady StorSimple 8000 verzi Update 3 nebo novější.

Služby StorSimple Manager zařízení **katalog zálohování** zobrazuje všechny zálohovací sklady, které vytvářejí, když jsou provedeny ruční nebo automatické zálohy. Pak můžete vybrat svazek v zálohovacího skladu klonovat.

 ![Zálohovací sklad seznamu](./media/storsimple-8000-clone-volume-u2/bucatalog.png)

## <a name="considerations-for-cloning-a-volume"></a>Důležité informace týkající se klonování svazku

Při klonování svazku, zvažte následující informace.

- Klon se chová stejně jako regulární svazek. Všechny operace, které je možné na svazku je k dispozici pro klonování.

- Monitorování a výchozí zálohování jsou automaticky zakázán na klonovaný svazku. Musíte nakonfigurovat svazek klonovaný pro žádné zálohy.

- Místně vázaný svazek je klonovat jako vrstvený svazek. Pokud potřebujete klonovaný být místně vázaný svazek, můžete klonu převést místně vázaný svazek po úspěšném dokončení operace klonování. Informace o převodu vrstvený svazek na místně vázaný svazek, přejděte na [změnit typ svazku](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).

- Pokud se pokusíte převést svazek klonovaný ze zřízeny vrstvené místně vázaný okamžitě po klonování (Pokud je stále přechodný klon), převod selže s touto se následující chybová zpráva:

    `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`

    Tato chyba byl přijat pouze v případě, že se klonování na jiném zařízení. Pokud převedete přechodný klonu nejdřív na trvalé klon místně vázaný svazek můžete úspěšně převést. Pořízení snímku cloudu přechodný klon ho převést na trvalé klonu.

## <a name="create-a-clone-of-a-volume"></a>Vytvořit klon svazku

Můžete vytvořit klon na stejné zařízení, jiná zařízení nebo i o zařízení cloudu pomocí místní nebo cloudový snímek.

Následující postup popisuje vytvoření klonu z katalogu služby zálohování.  Alternativní metoda pro zahájení klonování je přejít na **svazky**, vyberte svazek, a pak klikněte pravým tlačítkem myši a vyvolání v místní nabídce vyberte **klon**.

Proveďte následující kroky k vytvoření klonu svazku z katalogu služby zálohování.

#### <a name="to-clone-a-volume"></a>Klonování svazku

1. Přejděte do služby StorSimple Manager zařízení a pak klikněte na tlačítko **katalog zálohování**.

2. Vyberte zálohovací sklad následujícím způsobem:
   
   1. Vyberte příslušné zařízení.
   2. V rozevíracím seznamu vyberte svazek nebo zálohování zásady pro zálohu, kterou chcete vybrat.
   3. Zadejte časový rozsah.
   4. Klikněte na tlačítko **použít** ke spuštění tohoto dotazu.

    Zálohování přidružené k vybranému svazku nebo zásady zálohování by se měla objevit v seznamu sad záloh.
   
    ![Zálohovací sklad seznamu](./media/storsimple-8000-clone-volume-u2/bucatalog.png)
     
3. Rozbalte zálohovací sklad zobrazení přidružený svazek a vyberte svazek v zálohovacím skladu. Klikněte pravým tlačítkem myši a potom v místní nabídce vyberte **klon**.

   ![Zálohovací sklad seznamu](./media/storsimple-8000-clone-volume-u2/clonevol3b.png) 

3. V **klon** okno, proveďte následující kroky:
   
    1. Určete cílové zařízení. Toto je umístění, kde bude vytvořen klonu. Můžete vybrat stejné zařízení nebo zadejte jiné zařízení.

      > [!NOTE]
      > Ujistěte se, že kapacitu, vyžaduje se pro klonování je nižší než kapacity, které jsou k dispozici na cílovém zařízení.
       
    2. Zadejte název jedinečné svazku pro vaše klon. Název musí obsahovat 3 až 127 znaků.
      
        > [!NOTE]
        > **Klonování svazku jako** pole bude **nastavování** i v případě, že se klonování místně vázaný svazek. Nelze změnit toto nastavení; ale pokud budete potřebovat klonovaný svazku, který má být místně vázaný i, můžete převést klonu pro místně vázaný svazek po úspěšném vytvoření klonu. Informace o převodu vrstvený svazek na místně vázaný svazek, přejděte na [změnit typ svazku](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).
          
    3. V části **připojení hostitele**, zadejte záznam řízení přístupu (ACR) pro klonování. Můžete přidat nové ACR nebo zvolte ze seznamu existující. ACR určí, které hostitele může přístup Tenhle klon.
      
        ![Zálohovací sklad seznamu](./media/storsimple-8000-clone-volume-u2/clonevol3a.png) 

    4. Klikněte na tlačítko **klon** k dokončení operace.

4. Úloha klonování se zahájí a zobrazí se upozornění při klonování se úspěšně vytvořil. Kliknutím na úlohu oznámení nebo přejděte na **úlohy** okně můžete sledovat úlohu klonování.

    ![Zálohovací sklad seznamu](./media/storsimple-8000-clone-volume-u2/clonevol5.png)

7. Po dokončení úlohy klon přejít na zařízení a potom klikněte na **svazky**. V seznamu svazků měli byste vidět klon, který byl právě vytvořen ve stejném kontejneru svazku, který má zdrojový svazek.

    ![Zálohovací sklad seznamu](./media/storsimple-8000-clone-volume-u2/clonevol6.png)

Klon, který je vytvořen tímto způsobem je přechodný klonu. Další informace o typech klonování najdete v tématu [přechodný oproti trvalé klony](#transient-vs-permanent-clones).


## <a name="transient-vs-permanent-clones"></a>Pouze dočasné a trvalé klony
Přechodný klony vytvoří jenom v případě, že se klonování na jiné zařízení. Může klonovat svazek konkrétní ze zálohovacího skladu na jiném zařízení spravovaných pomocí Správce zařízení StorSimple. Přechodný klonu má odkazů na data v původním svazku a používá tato data ke čtení a zápisu místně na cílovém zařízení.

Po provedení cloudový snímek přechodný klonování je výsledný klonování *trvalé* klonování. Během tohoto procesu se vytvoří kopie dat v cloudu a čas zkopírovat tato data je určena velikostí dat a Azure latenci (Toto je kopie Azure do Azure). Tento proces může trvat dnů, týdnů. Přechodný klon stane trvalé klonování a nemá všechny odkazy na původní data svazku, která byla naklonována ze.

## <a name="scenarios-for-transient-and-permanent-clones"></a>Scénáře pro přechodný a trvalé klony
Následující části popisují příklad situacích, ve kterých může být použit klony přechodný a trvalé.

### <a name="item-level-recovery-with-a-transient-clone"></a>Obnovení na úrovni položek s přechodný klon
Budete muset obnovit soubor s jedním. roku starý Microsoft PowerPoint. Váš správce IT identifikuje konkrétní zálohování z této doby a pak filtry svazku. Správce pak provede klonování svazku, vyhledá soubor, který hledáte a poskytuje vám. V tomto scénáři se používá přechodný klonu.

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Testování v produkčním prostředí s trvalé klon
Je třeba ověřit chyby testování v produkčním prostředí. Vytvořit klon svazku v provozním prostředí a pak proveďte cloudový snímek Tenhle klon. k vytvoření klonovaného svazek nezávislé. V tomto scénáři se používá trvalá klonu.

## <a name="next-steps"></a>Další kroky
* Zjistěte, jak [obnovit svazek StorSimple ze zálohovacího skladu](storsimple-8000-restore-from-backup-set-u2.md).
* Zjistěte, jak [použít službu StorSimple Manager zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

