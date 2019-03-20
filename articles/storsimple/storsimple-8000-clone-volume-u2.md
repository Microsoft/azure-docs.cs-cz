---
title: Klonování svazku na StorSimple řady 8000 | Dokumentace Microsoftu
description: Popisuje typy různých klonování a využití a vysvětluje, jak můžete pomocí zálohovacího skladu klonování svazku na zařízení řady StorSimple 8000.
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
ms.openlocfilehash: 84734aefb72a3330d99c5707b461de2cd5e30484
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58097231"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-clone-a-volume"></a>Klonování svazku pomocí služby Správce zařízení StorSimple na webu Azure portal

## <a name="overview"></a>Přehled

Tento kurz popisuje, jak můžete pomocí zálohovacího skladu klonování svazku prostřednictvím **katalog zálohování** okno. Také vysvětluje rozdíl mezi *přechodné* a *trvalé* duplicity. Pokyny v tomto kurzu se vztahuje na všechna zařízení řady StorSimple 8000 s aktualizací Update 3 nebo novější.

Služba Správce zařízení StorSimple **katalog zálohování** okně se zobrazí všechny zálohovací sklady, které jsou vytvořeny při ruční nebo automatické zálohy jsou prováděny. Pak můžete vybrat svazek v zálohovacího skladu pro klonování.

 ![Zálohovací sklad seznamu](./media/storsimple-8000-clone-volume-u2/bucatalog.png)

## <a name="considerations-for-cloning-a-volume"></a>Důležité informace týkající se klonování svazku

Při klonování svazku, zvažte následující informace.

- Klon se chová stejně jako svazek regulárních. Jakékoli operaci, která je možné na svazku je k dispozici pro klonování.

- Monitorování a výchozí zálohování jsou automaticky zakázány na klonovaný svazek. Je třeba nakonfigurovat klonovaný svazek pro všechny zálohy.

- Místně vázaný svazek je klonují vrstveného svazku. Pokud potřebujete klonovaný svazek na místně připnutý, můžete převést klonu na místně vázaný svazek po úspěšném dokončení operace klonování. Informace o převod vrstveného svazku na místně vázaný svazek, přejděte na [změnu typu svazku](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).

- Pokud se pokusíte převést naklonované svazku z vrstveného na místně připnutý ihned po klonování (Pokud je stále klonem), převod nezdaří se následující chybová zpráva:

    `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`

    Tato chyba je přijata jenom v případě, že se klonování k jiné zařízení. Můžete převést úspěšně místně připnuté, pokud nejprve převeďte klonem na trvalé klon svazku. Pořiďte snímek v cloudu z klonem převést do trvalé klonování.

## <a name="create-a-clone-of-a-volume"></a>Vytvoření klonování svazku

Můžete vytvořit klon na stejné zařízení, jiné zařízení nebo dokonce cloudového zařízení pomocí místní nebo cloudový snímek.

Následující postup popisuje, jak na vytvoření klonu z katalogu záloh.  Alternativní metoda pro zahájení klonování je přejít k **svazky**, vyberte svazek a potom kliknutím pravým tlačítkem myši vyvolejte místní nabídku a vyberte **klonování**.

Proveďte následující kroky a vytvoříte klon svazku na katalog záloh.

#### <a name="to-clone-a-volume"></a>Klonování svazku

1. Přejděte do služby Správce zařízení StorSimple a potom klikněte na tlačítko **katalog zálohování**.

2. Vyberte zálohu, nastavte následujícím způsobem:
   
   1. Vyberte příslušné zařízení.
   2. V rozevíracím seznamu vyberte svazek nebo zálohování zásadu pro zálohu, kterou chcete vybrat.
   3. Zadejte časový rozsah.
   4. Klikněte na tlačítko **použít** ke spuštění tohoto dotazu.

      Zálohy přidružené vybraný svazek nebo zásady zálohování by se měla zobrazit v seznamu sad záloh.
   
      ![Zálohovací sklad seznamu](./media/storsimple-8000-clone-volume-u2/bucatalog.png)
     
3. Rozbalte zálohovacího skladu za účelem zobrazení přidružený svazek a vyberte svazek v zálohovacím skladu. Klikněte pravým tlačítkem a pak v místní nabídce vyberte **klonování**.

   ![Zálohovací sklad seznamu](./media/storsimple-8000-clone-volume-u2/clonevol3b.png) 

3. V **klonování** okno, proveďte následující kroky:
   
   1. Určí, že cílové zařízení. Toto je umístění, kde budou vytvářeny klonu. Můžete vybrat stejné zařízení nebo zadejte jiné zařízení.

      > [!NOTE]
      > Ujistěte se, že je nižší než kapacita na cílovém zařízení dostupná kapacita požadovaná pro klonování.
       
   2. Zadejte název jedinečné svazku vašem klonu. Název musí obsahovat 3 až 127 znaků.
      
       > [!NOTE]
       > **Klonování svazku jako** pole bude **Vrstvená** i v případě, že se klonování místně vázaný svazek. Toto nastavení; nelze změnit. ale pokud potřebujete klonovaný svazku, který má být místně připnuté také, můžete převést klonování pro místně vázaný svazek po úspěšném vytvoření klonu. Informace o převod vrstveného svazku na místně vázaný svazek, přejděte na [změnu typu svazku](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).
          
   3. V části **připojení hostitelé**, zadat záznam řízení přístupu (ACR) pro klonování. Můžete přidat nový záznam ACR nebo zvolte ze seznamu existující. ACR určí, které hostitele může přistupovat k Tenhle klon.
      
       ![Zálohovací sklad seznamu](./media/storsimple-8000-clone-volume-u2/clonevol3a.png) 

   4. Klikněte na tlačítko **klonování** k dokončení operace.

4. Úloha klonování se zahájí a zobrazí se oznámení po úspěšném vytvoření klonu. Klikněte na oznámení úloh nebo můžete přejít na **úlohy** okno monitorování úloha klonování.

    ![Zálohovací sklad seznamu](./media/storsimple-8000-clone-volume-u2/clonevol5.png)

7. Po dokončení úloha klonování se přejít na vaše zařízení a potom klikněte na tlačítko **svazky**. V seznamu svazků měli byste vidět klonování, který byl právě vytvořen ve stejném kontejneru svazku, který má zdrojového svazku.

    ![Zálohovací sklad seznamu](./media/storsimple-8000-clone-volume-u2/clonevol6.png)

Klon, který je vytvořen tímto způsobem je klonem. Další informace o typech klonování, naleznete v tématu [přechodné nebo trvalé duplicity](#transient-vs-permanent-clones).


## <a name="transient-vs-permanent-clones"></a>Přechodné nebo trvalé duplicity
Přechodné klony vytvoří jenom v případě, že je naklonujete na jiné zařízení. Můžete naklonovat určitý svazek ze zálohovacího skladu na jiném zařízení spravovaných pomocí Správce zařízení StorSimple. Přechodné klonování má odkazy na data v původním svazku a používá tato data ke čtení a zápisu místně na cílovém zařízení.

Až podniknete cloudový snímek klonem, je výsledný klonování *trvalé* klonování. Během tohoto procesu se vytvoří kopie dat v cloudu a čas potřebný ke kopírování dat se určuje podle velikosti dat a Azure latenci (Toto je kopie Azure do Azure). Tento proces může trvat dny týdnů. Klonem stane trvalé klonování a nemá žádné odkazy na původní data svazek, který bylo naklonováno z.

## <a name="scenarios-for-transient-and-permanent-clones"></a>Scénáře pro přechodné nebo trvalé duplicity
Následující části popisují příklad situací, ve kterých je možné přechodné nebo trvalé duplicity.

### <a name="item-level-recovery-with-a-transient-clone"></a>Obnovení na úrovni položky s klonem
Budete muset obnovit soubor prezentace aplikace PowerPoint jeden 106letou tradicí. Váš správce IT identifikuje konkrétní zálohy v čase a pak filtruje svazku. Správce pak provede klonování svazku, vyhledá soubor, který hledáte a poskytuje vám. V tomto scénáři se používá klonem.

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Testování v produkčním prostředí s trvalou klon
Je třeba ověřit chyby testování v produkčním prostředí. Vytvoření klonu svazku v provozním prostředí a pak proveďte cloudový snímek Tenhle klon. Chcete-li vytvořit nezávislé klonovaný svazek. V tomto scénáři se používá trvalá klonování.

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [obnovení svazku StorSimple ze zálohovacího skladu](storsimple-8000-restore-from-backup-set-u2.md).
* Zjistěte, jak [použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

