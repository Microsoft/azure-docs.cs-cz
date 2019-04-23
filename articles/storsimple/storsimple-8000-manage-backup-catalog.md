---
title: Správa katalogu záloh vašich StorSimple | Dokumentace Microsoftu
description: Vysvětluje způsob používání katalogu záloh stránce služby Správce zařízení StorSimple do seznamu, vyberte a odstranit zálohovací sklady.
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
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 07d9e03f1631ebce88a7a7c2e33be62f21dda522
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60319619"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-backup-catalog"></a>Použití služby Správce zařízení StorSimple ke správě vašeho katalogu záloh
## <a name="overview"></a>Přehled
Služba Správce zařízení StorSimple **katalog záloh** okně se zobrazí všechny zálohovací sklady, které jsou vytvořeny při ruční nebo plánované zálohy jsou prováděny. Můžete pomocí této stránky můžete vypsat všechny zálohy pro zásadu zálohování nebo svazek, vyberte nebo odstranit zálohy, nebo použijte zálohu k obnovení nebo klonování svazku.

Tento kurz vysvětluje, jak vyberte a odstranit zálohovací sklad. Zjistěte, jak obnovit zařízení ze zálohy, přejděte na [obnovit zařízení ze zálohovacího skladu](storsimple-8000-restore-from-backup-set-u2.md). Chcete-li získat informace o klonování svazku, přejděte na [klonovat svazek StorSimple](storsimple-8000-clone-volume-u2.md).

![katalog záloh](./media/storsimple-8000-manage-backup-catalog/bucatalog.png) 

**Katalog záloh** okno obsahuje dotaz, chcete-li zúžit zálohování sadu výběru. Můžete filtrovat zálohovacích skladů, které se načítají na základě následujících parametrů:

* **Zařízení** – zařízení, na kterém byl zálohovací sklad vytvořen.
* **Zásady zálohování nebo svazek** – zásady zálohování nebo svazku přidruženém k tohoto zálohovacího skladu.
* **Od a do** – rozsah data a času při vytvoření zálohovacího skladu.

Filtrované sady záloh jsou pak uvedených na základě následujících atributů:

* **Název** – název zásady zálohování nebo svazku přidruženém k zálohovacího skladu.
* **Velikost** – skutečná velikost zálohovacího skladu.
* **Datum vytvoření** – datum a čas, kdy byly vytvořeny zálohy. 
* **Typ** – zálohovací sklady může být místní snímky nebo cloudových snímků. Místní snímek je zálohu všechna data svazku ukládají místně na zařízení, zatímco odkazuje snímek v cloudu k zálohování dat svazků umístěných v cloudu. Místní snímky poskytují rychlejší přístup, že cloudové snímky jsou zvolena větší odolnost dat.
* **Iniciováno** – zálohy, nepůjdou automaticky podle plánu nebo ručně uživatelem. Zásady zálohování můžete použít k naplánování zálohování. Alternativně můžete použít **vytvořit zálohu** možnost provedení ruční zálohy.

## <a name="list-backup-sets-for-a-backup-policy"></a>Seznam zálohovací sklady pro zásady zálohování
Proveďte následující kroky, chcete-li vypsat všechny zálohy pro zásadu zálohování.

#### <a name="to-list-backup-sets"></a>Do seznamu sad záloh
1. Služby do Správce zařízení StorSimple a klikněte na tlačítko **katalog zálohování**.

2. Filtrovat výběr následujícím způsobem:
   
   1. Zadejte časový rozsah.
   2. Vyberte příslušné zařízení.
   3. Filtrovat podle **zásady zálohování** zobrazíte odpovídající zálohy.
   3. Z rozevíracího seznamu zásad zálohování zvolte **všechny** zobrazíte všechny zálohy na vybraném zařízení.
   4. Klikněte na tlačítko **použít** ke spuštění tohoto dotazu.
      
      Zálohy přidružené k vybrané zásady zálohování by se zobrazit v seznamu sad záloh uvedených.

      ![Přejděte do katalogu záloh](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

## <a name="select-a-backup-set"></a>Vyberte sadu záloh
Proveďte následující kroky, chcete-li vybrat zálohovacího skladu pro svazek nebo zásady zálohování.

#### <a name="to-select-a-backup-set"></a>Vyberte sadu záloh
1. Služby do Správce zařízení StorSimple a klikněte na tlačítko **katalog zálohování**.
2. Filtrovat výběr následujícím způsobem:
   
   1. Zadejte časový rozsah. 
   2. Vyberte příslušné zařízení. 
   3. Filtrovat podle zásad svazku nebo zálohu pro zálohu, kterou chcete vybrat.
   4. Klikněte na tlačítko **použít** ke spuštění tohoto dotazu.
      
      Zálohy přidružené vybraný svazek nebo zásady zálohování by se měla zobrazit v seznamu sad záloh.

      ![Přejděte do katalogu záloh](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Vyberte a rozbalte zálohovacího skladu. Nyní je vidět zálohovacích skladů porušena svazky, které obsahuje. **Obnovení** a **odstranit** možnosti jsou dostupné přes místní nabídku (kliknutím pravým tlačítkem) pro zálohovací sklad. Obě tyto akce můžete provádět na zálohovací sklad, který jste vybrali.

    ![Přejděte do katalogu záloh](./media/storsimple-8000-manage-backup-catalog/bucatalog2.png)

## <a name="delete-a-backup-set"></a>Odstranit zálohovací sklad
Odstraňte zálohu, pokud již nechcete zachovat data s ním spojená. Proveďte následující kroky k odstranění zálohovacího skladu.

#### <a name="to-delete-a-backup-set"></a>Chcete-li odstranit sadu záloh
 Služby do Správce zařízení StorSimple a klikněte na tlačítko **katalog zálohování**.
1. Filtrovat výběr následujícím způsobem:
   
   1. Zadejte časový rozsah. 
   2. Vyberte příslušné zařízení. 
   3. Filtrovat podle zásad svazku nebo zálohu pro zálohu, kterou chcete vybrat.
   4. Klikněte na tlačítko **použít** ke spuštění tohoto dotazu.
      
      Zálohy přidružené vybraný svazek nebo zásady zálohování by se měla zobrazit v seznamu sad záloh.

      ![Přejděte do katalogu záloh](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

1. Vyberte a rozbalte zálohovacího skladu. Nyní je vidět zálohovacích skladů porušena svazky, které obsahuje. **Obnovení** a **odstranit** možnosti jsou dostupné přes místní nabídku (kliknutím pravým tlačítkem) pro zálohovací sklad. Klikněte pravým tlačítkem na vybrané zálohovacího skladu a v místní nabídce vyberte **odstranit**.

    ![Přejděte do katalogu záloh](./media/storsimple-8000-manage-backup-catalog/bucatalog3.png)

1. Po zobrazení výzvy k potvrzení zkontrolujte zobrazené informace a klikněte na **odstranit**. Vybraná záloha se trvale odstraní.

    ![Přejděte do katalogu záloh](./media/storsimple-8000-manage-backup-catalog/bucatalog4.png)  

1. Při odstranění je v průběhu a kdy byla úspěšně dokončena, budete upozorněni. Po odstranění je dokončeno, aktualizujte dotaz na této stránce. Odstraněné zálohovacího skladu už se zobrazí v seznamu sad záloh uvedených.

    ![Přejděte do katalogu záloh](./media/storsimple-8000-manage-backup-catalog/bucatalog7.png)

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [použít katalog záloh k obnovení zařízení ze zálohovacího skladu](storsimple-8000-restore-from-backup-set-u2.md).
* Zjistěte, jak [použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

