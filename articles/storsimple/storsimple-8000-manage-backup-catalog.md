---
title: Správa katalogu zálohování StorSimple | Microsoft Docs
description: Vysvětluje způsob použití stránky katalogu zálohování služby StorSimple Device Manager k vypsání, výběru a odstranění zálohovacích skladů.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 6ab4694159c6269f2f72d3d591147d43aef34a0a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961591"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-backup-catalog"></a>Správa katalogu záloh pomocí služby StorSimple Device Manager
## <a name="overview"></a>Overview
V okně StorSimple Device Manager Service **Backup Catalog** se zobrazí všechny zálohovací sklady, které se vytvoří při ručním nebo plánovaném zálohování. Pomocí této stránky můžete zobrazit seznam všech záloh pro zásady zálohování nebo svazek, vybrat nebo odstranit zálohy nebo použít zálohu k obnovení nebo klonování svazku.

Tento kurz vysvětluje, jak vytvořit seznam, vybrat a odstranit zálohovací sklad. Informace o tom, jak obnovit zařízení ze zálohy, najdete [v tématu Obnovení zařízení ze zálohovacího skladu](storsimple-8000-restore-from-backup-set-u2.md). Informace o tom, jak klonovat svazek, najdete v tématu [klonování svazku StorSimple](storsimple-8000-clone-volume-u2.md).

![Katalog záloh](./media/storsimple-8000-manage-backup-catalog/bucatalog.png) 

Okno **katalog záloh** poskytuje dotaz pro zúžení výběru zálohovacích skladů. Načtené zálohovací sady můžete filtrovat na základě následujících parametrů:

* **Zařízení** – zařízení, na kterém se vytvořil zálohovací sklad.
* **Zásady zálohování nebo svazek** – zásady zálohování nebo svazek přidružené k tomuto zálohovacímu skladu.
* **Od a do** – rozsah data a času, kdy byl zálohovací sklad vytvořen.

Filtrované zálohovací sklady jsou pak na základě těchto atributů:

* **Název** – název zásady zálohování nebo svazku přidruženého k zálohovacímu skladu.
* **Size** – skutečná velikost zálohovacího skladu.
* **Vytvořeno** – datum a čas vytvoření zálohy. 
* **Typ** – zálohovací sklady můžou být místní snímky nebo cloudové snímky. Místní snímek je záloha všech dat svazků uložených místně na zařízení, zatímco cloudový snímek odkazuje na zálohování dat svazků umístěných v cloudu. Místní snímky poskytují rychlejší přístup, zatímco se pro zajištění odolnosti dat volí cloudové snímky.
* **Iniciováno** – zálohy je možné iniciovat automaticky podle plánu nebo ručně uživatelem. K naplánování záloh můžete použít zásady zálohování. Alternativně můžete použít možnost **provést zálohování** a provést ruční zálohování.

## <a name="list-backup-sets-for-a-backup-policy"></a>Seznam zálohovacích skladů pro zásady zálohování
Provedením následujících kroků zobrazíte seznam všech záloh pro zásady zálohování.

#### <a name="to-list-backup-sets"></a>Seznam zálohovacích sad
1. Přejděte do služby StorSimple Device Manager a klikněte na **katalog záloh**.

2. Filtrovat výběry následujícím způsobem:
   
   1. Zadejte časový rozsah.
   2. Vyberte příslušné zařízení.
   3. Filtrovat podle **zásad zálohování** , aby se zobrazily odpovídající zálohy.
   3. V rozevíracím seznamu zásady zálohování vyberte možnost **vše** , pokud chcete zobrazit všechny zálohy na vybraném zařízení.
   4. Kliknutím na **použít** spusťte tento dotaz.
      
      Zálohy přidružené k vybraným zásadám zálohování by se měly zobrazit v seznamu zálohovacích skladů.

      ![Přejít na katalog záloh](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

## <a name="select-a-backup-set"></a>Vybrat zálohovací sklad
Provedením následujících kroků vyberte zálohovací sklad pro svazek nebo zásady zálohování.

#### <a name="to-select-a-backup-set"></a>Výběr zálohovacího sady
1. Přejděte do služby StorSimple Device Manager a klikněte na **katalog záloh**.
2. Filtrovat výběry následujícím způsobem:
   
   1. Zadejte časový rozsah. 
   2. Vyberte příslušné zařízení. 
   3. Vyfiltrujte podle svazku nebo zásad zálohování pro zálohu, kterou chcete vybrat.
   4. Kliknutím na **použít** spusťte tento dotaz.
      
      Zálohy přidružené k vybranému svazku nebo zásadám zálohování by se měly zobrazit v seznamu zálohovacích skladů.

      ![Přejít na katalog záloh 2](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Vyberte a rozbalte zálohovací sklad. Teď vidíte zálohovací sklady, které jsou rozdělené podle svazků, které obsahuje. Možnosti **obnovení** a **odstranění** jsou k dispozici prostřednictvím místní nabídky (klikněte pravým tlačítkem myši) pro zálohovací sklad. Můžete provést jednu z těchto akcí pro zálohovací sklad, který jste vybrali.

    ![Přejít na katalog záloh 3](./media/storsimple-8000-manage-backup-catalog/bucatalog2.png)

## <a name="delete-a-backup-set"></a>Odstraní zálohovací sklad.
Pokud už nechcete uchovávat data, která jsou k němu přidružená, odstraňte zálohu. Chcete-li odstranit zálohovací sklad, proveďte následující kroky.

#### <a name="to-delete-a-backup-set"></a>Odstranění zálohovacího skladu
 Přejděte do služby StorSimple Device Manager a klikněte na **katalog záloh**.
1. Filtrovat výběry následujícím způsobem:
   
   1. Zadejte časový rozsah. 
   2. Vyberte příslušné zařízení. 
   3. Vyfiltrujte podle svazku nebo zásad zálohování pro zálohu, kterou chcete vybrat.
   4. Kliknutím na **použít** spusťte tento dotaz.
      
      Zálohy přidružené k vybranému svazku nebo zásadám zálohování by se měly zobrazit v seznamu zálohovacích skladů.

      ![Přejít na katalog záloh 4](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

1. Vyberte a rozbalte zálohovací sklad. Teď vidíte zálohovací sklady, které jsou rozdělené podle svazků, které obsahuje. Možnosti **obnovení** a **odstranění** jsou k dispozici prostřednictvím místní nabídky (klikněte pravým tlačítkem myši) pro zálohovací sklad. Klikněte pravým tlačítkem na vybraný zálohovací sklad a v místní nabídce vyberte **Odstranit**.

    ![Přejít na katalog záloh 5](./media/storsimple-8000-manage-backup-catalog/bucatalog3.png)

1. Po zobrazení výzvy k potvrzení Zkontrolujte zobrazené informace a klikněte na **Odstranit**. Vybraná záloha se trvale odstraní.

    ![Přejít na katalog záloh 6](./media/storsimple-8000-manage-backup-catalog/bucatalog4.png)  

1. Když Probíhá odstraňování a když se úspěšně dokončí, zobrazí se oznámení. Po dokončení odstranění aktualizujte dotaz na této stránce. Odstraněný zálohovací sklad se už nebude zobrazovat v seznamu zálohovacích skladů.

    ![Přejít na katalog záloh 7](./media/storsimple-8000-manage-backup-catalog/bucatalog7.png)

## <a name="next-steps"></a>Další kroky
* Naučte se [používat katalog záloh k obnovení zařízení ze zálohovacího skladu](storsimple-8000-restore-from-backup-set-u2.md).
* Naučte se [používat službu StorSimple Device Manager ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

