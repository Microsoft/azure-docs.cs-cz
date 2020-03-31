---
title: Správa katalogu zálohování StorSimple | Dokumenty společnosti Microsoft
description: Vysvětluje, jak používat stránku katalogu zálohování služby Správce zařízení StorSimple pro zobrazení, výběr a odstranění zálohovacích sad.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60319619"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-backup-catalog"></a>Ke správě katalogu záloh použijte službu StorSimple Device Manager.
## <a name="overview"></a>Přehled
Okno **Katalog zálohování** služby StorSimple Device Manager zobrazuje všechny zálohovací sady, které jsou vytvořeny při ručním nebo naplánovaném zálohování. Na této stránce můžete vypsat všechny zálohy pro zásady zálohování nebo svazek, vybrat nebo odstranit zálohy nebo použít zálohu k obnovení nebo klonování svazku.

Tento kurz vysvětluje, jak vypsat, vybrat a odstranit zálohovací sadu. Chcete-li se dozvědět, jak obnovit zařízení ze zálohy, přejděte na [obnovit zařízení ze zálohovací sady](storsimple-8000-restore-from-backup-set-u2.md). Chcete-li se dozvědět, jak klonovat svazek, přejděte na [Clone a StorSimple svazek](storsimple-8000-clone-volume-u2.md).

![Katalog záloh](./media/storsimple-8000-manage-backup-catalog/bucatalog.png) 

Okno **Katalog zálohování** poskytuje dotaz na zúžení výběru zálohovací sady. Načtení zálohovacích sad můžete filtrovat na základě následujících parametrů:

* **Zařízení** – zařízení, na kterém byla vytvořena zálohovací sada.
* **Zásady zálohování nebo svazek** – zásady zálohování nebo svazek přidružené k této zálohovací sadě.
* **Od a Do** – datum a časový rozsah, kdy byla vytvořena zálohovací sada.

Filtrované zálohovací sady jsou pak tabulkovány na základě následujících atributů:

* **Název** – název zásady zálohování nebo svazku přidruženého k zálohovací sadě.
* **Velikost** – skutečná velikost zálohovací sady.
* **Vytvořeno –** datum a čas, kdy byly vytvořeny zálohy. 
* **Typ** – zálohovací sady mohou být místní snímky nebo cloudové snímky. Místní snímek je záloha všech dat svazku uložených místně v zařízení, zatímco snímek cloudu odkazuje na zálohování dat svazku s bydlištěm v cloudu. Místní snímky poskytují rychlejší přístup, zatímco cloudové snímky jsou vybrány pro odolnost proti chybám dat.
* **Iniciováno –** zálohy mohou být automaticky inicializovány podle plánu nebo ručně uživatelem. Zásady zálohování můžete použít k plánování zálohování. Alternativně můžete použít možnost **Převzít zálohu** k ručnímu zálohování.

## <a name="list-backup-sets-for-a-backup-policy"></a>Seznam zálohovacích sad pro zásady zálohování
Pomocí následujících kroků zobrazíte seznam všech záloh pro zásady zálohování.

#### <a name="to-list-backup-sets"></a>Seznam zálohovacích sad
1. Přejděte na službu Správce zařízení StorSimple a klepněte na tlačítko **Zálohovat katalog**.

2. Výběry můžete filtrovat následujícím způsobem:
   
   1. Zadejte časový rozsah.
   2. Vyberte příslušné zařízení.
   3. Filtrovat **podle zásady zálohování** zobrazíte odpovídající zálohy.
   3. V rozevíracím seznamu zásad zálohování zvolte **Vše,** chcete-li zobrazit všechny zálohy na vybraném zařízení.
   4. Chcete-li spustit tento dotaz, klepněte na **tlačítko Použít.**
      
      Zálohy přidružené k vybrané zásadě zálohování by se měly objevit v seznamu zálohovacích sad.

      ![Přejít do katalogu záloh](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

## <a name="select-a-backup-set"></a>Výběr zálohovací sady
Chcete-li vybrat zálohovací sadu pro zásady svazku nebo zálohování, proveďte následující kroky.

#### <a name="to-select-a-backup-set"></a>Výběr zálohovací sady
1. Přejděte na službu Správce zařízení StorSimple a klepněte na tlačítko **Zálohovat katalog**.
2. Výběry můžete filtrovat následujícím způsobem:
   
   1. Zadejte časový rozsah. 
   2. Vyberte příslušné zařízení. 
   3. Umožňuje vytvořit možnost Filtrovat podle svazku nebo zásady zálohování pro zálohu, kterou chcete vybrat.
   4. Chcete-li spustit tento dotaz, klepněte na **tlačítko Použít.**
      
      Zálohy přidružené k vybranému svazku nebo zásady zálohování by se měly objevit v seznamu zálohovacích sad.

      ![Přejít do katalogu záloh](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Vyberte a rozbalte zálohovací sklad. Nyní můžete zobrazit zálohovací sady rozdělené podle svazků, které obsahuje. Možnosti **Obnovit** a **odstranit** jsou k dispozici prostřednictvím kontextové nabídky (klepnutí pravým tlačítkem myši) pro zálohovací sadu. Některou z těchto akcí můžete provést ve vybrané zálohovací sadě.

    ![Přejít do katalogu záloh](./media/storsimple-8000-manage-backup-catalog/bucatalog2.png)

## <a name="delete-a-backup-set"></a>Odstranění zálohovací sady
Pokud již nechcete zachovat data, která jsou k ní přidružena, odstraňte zálohu. Chcete-li odstranit zálohovací sklad, proveďte následující kroky.

#### <a name="to-delete-a-backup-set"></a>Odstranění zálohovací sady
 Přejděte na službu Správce zařízení StorSimple a klepněte na tlačítko **Zálohovat katalog**.
1. Výběry můžete filtrovat následujícím způsobem:
   
   1. Zadejte časový rozsah. 
   2. Vyberte příslušné zařízení. 
   3. Umožňuje vytvořit možnost Filtrovat podle svazku nebo zásady zálohování pro zálohu, kterou chcete vybrat.
   4. Chcete-li spustit tento dotaz, klepněte na **tlačítko Použít.**
      
      Zálohy přidružené k vybranému svazku nebo zásady zálohování by se měly objevit v seznamu zálohovacích sad.

      ![Přejít do katalogu záloh](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

1. Vyberte a rozbalte zálohovací sklad. Nyní můžete zobrazit zálohovací sady rozdělené podle svazků, které obsahuje. Možnosti **Obnovit** a **odstranit** jsou k dispozici prostřednictvím kontextové nabídky (klepnutí pravým tlačítkem myši) pro zálohovací sadu. Klepněte pravým tlačítkem myši na vybranou zálohovací sadu a v místní nabídce vyberte **příkaz Odstranit**.

    ![Přejít do katalogu záloh](./media/storsimple-8000-manage-backup-catalog/bucatalog3.png)

1. Po zobrazení výzvy k potvrzení zkontrolujte zobrazené informace a klepněte na tlačítko **Odstranit**. Vybraná záloha bude trvale odstraněna.

    ![Přejít do katalogu záloh](./media/storsimple-8000-manage-backup-catalog/bucatalog4.png)  

1. Budete upozorněni, když probíhá odstranění a kdy bylo úspěšně dokončeno. Po dokončení odstranění aktualizujte dotaz na této stránce. Odstraněná zálohovací sada se již nebude zobrazovat v seznamu zálohovacích sad.

    ![Přejít do katalogu záloh](./media/storsimple-8000-manage-backup-catalog/bucatalog7.png)

## <a name="next-steps"></a>Další kroky
* Přečtěte si, jak [pomocí katalogu záloh obnovit zařízení ze zálohovací sady](storsimple-8000-restore-from-backup-set-u2.md).
* Přečtěte si, jak [používat službu StorSimple Device Manager ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

