---
title: Spravovat katalog záloh StorSimple | Microsoft Docs
description: Vysvětluje, jak pomocí Správce zařízení StorSimple na stránce Katalog zálohování seznamu, vyberte a odstraňte zálohovací sklady.
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
ms.openlocfilehash: ac2577c6cd350d6d437d55e61ec73d954cb24893
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874924"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-backup-catalog"></a>Použít službu StorSimple Manager zařízení ke správě katalog záloh
## <a name="overview"></a>Přehled
Služby StorSimple Manager zařízení **zálohování katalogu** zobrazuje všechny zálohovací sklady, které vytvářejí, když jsou provedeny ruční nebo plánované zálohy. Můžete tato stránka slouží k zobrazení seznamu všech zálohy pro zásady zálohování nebo svazek, vyberte nebo odstranit zálohy, nebo použít zálohu k obnovení nebo klonování svazku.

Tento kurz vysvětluje, jak zobrazit seznam, vyberte a odstraňte zálohovacího skladu. Chcete-li se dozvědět, jak obnovit ze zálohy zařízení, přejděte na [obnovit vaše zařízení ze zálohovacího skladu](storsimple-8000-restore-from-backup-set-u2.md). Chcete-li se dozvědět, jak vytvořit kopii svazku, přejděte na [klonovat svazek StorSimple](storsimple-8000-clone-volume-u2.md).

![Zálohování katalogu](./media/storsimple-8000-manage-backup-catalog/bucatalog.png) 

**Zálohování katalogu** okno obsahuje dotaz, chcete-li zúžit zálohování sadu výběr. Můžete filtrovat zálohovací sklady, které jsou načteny, na základě následujících parametrů:

* **Zařízení** – zařízení, v němž byla vytvořena zálohovacího skladu.
* **Zásady zálohování nebo svazku** – zásady zálohování nebo svazku přidruženém k tohoto zálohovacího skladu.
* **Od a do** – rozsah data a času v okamžiku vytvoření zálohovacího skladu.

Filtrované zálohovací sklady jsou pak poskytovalo na základě následujících atributů:

* **Název** – název zásady zálohování nebo svazku přidruženém k zálohovacího skladu.
* **Velikost** – skutečná velikost zálohovacího skladu.
* **Vytvořit na** – datum a čas, kdy byly vytvořeny zálohy. 
* **Typ** – zálohovací sklady může být místní snímky nebo cloudových snímků. Místní snímek je zálohování všech dat uložených místně na zařízení, svazku, zatímco cloudový snímek odkazuje na zálohování svazku dat umístěných v cloudu. Místní snímky poskytují rychlejší přístup, že jsou pro záleží na odolnosti dat zvolena cloudových snímků.
* **Iniciováno** – zálohy lze inicializovat automaticky podle plánu nebo ručně uživatelem. Zásady zálohování můžete naplánovat zálohování. Alternativně můžete použít **provést zálohování** možnost provedení ruční zálohy.

## <a name="list-backup-sets-for-a-backup-policy"></a>Zálohování seznamu nastaví pro zásady zálohování
Pomocí následujících kroků na seznam všech záloh pro zásady zálohování.

#### <a name="to-list-backup-sets"></a>Do seznamu zálohovací sklady
1. Přejděte k vaší Správce zařízení StorSimple služby a klikněte na tlačítko **katalog zálohování**.

2. Výběr filtru následujícím způsobem:
   
   1. Zadejte časový rozsah.
   2. Vyberte příslušné zařízení.
   3. Filtrovat podle **zálohování zásad** k zobrazení odpovídající zálohy.
   3. Z rozevíracího seznamu zásady zálohování vyberte **všechny** zobrazíte všechny zálohy na vybraná zařízení.
   4. Klikněte na tlačítko **použít** ke spuštění tohoto dotazu.
      
      Zálohování přidružených k vybrané zásadě zálohování by se zobrazit v seznamu sad záloh.

      ![Přejděte do katalogu zálohování](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

## <a name="select-a-backup-set"></a>Vyberte zálohovací sklad
Pomocí následujících kroků vyberte zálohovacího skladu pro svazek nebo zásady zálohování.

#### <a name="to-select-a-backup-set"></a>Chcete-li vybrat zálohovacího skladu
1. Přejděte k vaší Správce zařízení StorSimple služby a klikněte na tlačítko **katalog zálohování**.
2. Výběr filtru následujícím způsobem:
   
   1. Zadejte časový rozsah. 
   2. Vyberte příslušné zařízení. 
   3. Filtrovat podle zásad svazku nebo zálohu pro zálohování, který chcete vybrat.
   4. Klikněte na tlačítko **použít** ke spuštění tohoto dotazu.
      
      Zálohování přidružené k vybranému svazku nebo zásady zálohování by se měla objevit v seznamu sad záloh.

      ![Přejděte do katalogu zálohování](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Vyberte a rozbalte zálohovacího skladu. Nyní můžete vidět zálohovací sklady členěné podle svazků, které obsahuje. **Obnovení** a **odstranit** možnosti jsou dostupné prostřednictvím kontextovou nabídku (klikněte pravým tlačítkem) zálohovacího skladu. Můžete provést některé z těchto akcí na zálohovací sklad, který jste vybrali.

    ![Přejděte do katalogu zálohování](./media/storsimple-8000-manage-backup-catalog/bucatalog2.png)

## <a name="delete-a-backup-set"></a>Odstranit zálohovacího skladu
Odstraňte zálohy, když už chcete zachovat data s ním spojená. Proveďte následující kroky a odstraňte zálohovacího skladu.

#### <a name="to-delete-a-backup-set"></a>Chcete-li odstranit zálohovacího skladu
 Přejděte k vaší Správce zařízení StorSimple služby a klikněte na tlačítko **katalog zálohování**.
2. Výběr filtru následujícím způsobem:
   
   1. Zadejte časový rozsah. 
   2. Vyberte příslušné zařízení. 
   3. Filtrovat podle zásad svazku nebo zálohu pro zálohování, který chcete vybrat.
   4. Klikněte na tlačítko **použít** ke spuštění tohoto dotazu.
      
      Zálohování přidružené k vybranému svazku nebo zásady zálohování by se měla objevit v seznamu sad záloh.

      ![Přejděte do katalogu zálohování](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Vyberte a rozbalte zálohovacího skladu. Nyní můžete vidět zálohovací sklady členěné podle svazků, které obsahuje. **Obnovení** a **odstranit** možnosti jsou dostupné prostřednictvím kontextovou nabídku (klikněte pravým tlačítkem) zálohovacího skladu. Klikněte pravým tlačítkem na vybrané zálohovacího skladu a v místní nabídce vyberte **odstranit**.

    ![Přejděte do katalogu zálohování](./media/storsimple-8000-manage-backup-catalog/bucatalog3.png)

4. Po zobrazení výzvy k potvrzení, zkontrolujte zobrazené informace a klikněte na **odstranit**. Vybranou zálohu se trvale odstraní.

    ![Přejděte do katalogu zálohování](./media/storsimple-8000-manage-backup-catalog/bucatalog4.png)  

5. Při odstranění je v průběhu a kdy má úspěšně dokončena, budete upozorněni. Po odstranění probíhá, aktualizujte dotaz na této stránce. Odstraněné zálohovací sklad se nebude zobrazovat v seznamu sad záloh.

    ![Přejděte do katalogu zálohování](./media/storsimple-8000-manage-backup-catalog/bucatalog7.png)

## <a name="next-steps"></a>Další kroky
* Zjistěte, jak [zálohování katalog používat k obnovení vašeho zařízení ze zálohovacího skladu](storsimple-8000-restore-from-backup-set-u2.md).
* Zjistěte, jak [použít službu StorSimple Manager zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

