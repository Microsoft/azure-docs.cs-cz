---
title: Správa sdílených složek StorSimple Virtual Array | Dokumenty společnosti Microsoft
description: Popisuje Správce zařízení StorSimple a vysvětluje, jak jej použít ke správě sdílených složek ve virtuálním poli StorSimple.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 0a799c83-fde5-4f3f-af0e-67535d1882b6
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 82a6cdb6c9a39a0d196049a7ba662681ea06b36a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "62116862"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>Použití služby Správce zařízení StorSimple ke správě sdílených složek ve službě StorSimple Virtual Array

## <a name="overview"></a>Přehled

Tento kurz vysvětluje, jak pomocí služby StorSimple Device Manager vytvářet a spravovat sdílené složky na virtuálním poli StorSimple.

Služba StorSimple Device Manager je rozšíření na webu Azure Portal, které umožňuje spravovat vaše řešení StorSimple z jediného webového rozhraní. Kromě správy sdílených složek a svazků můžete pomocí služby StorSimple Device Manager zobrazit a spravovat zařízení, zobrazit výstrahy, spravovat zásady zálohování a spravovat katalog zálohování.

## <a name="share-types"></a>Typy sdílení

StorSimple akcie mohou být:

* **Místně připnuté**: Data v těchto sdílených složek zůstane v poli po celou dobu a nepřelije do cloudu.
* **Vrstvené**: Data v těchto sdílených složek se mohou přelít do cloudu. Když vytvoříte vrstvenou sdílenou složku, přibližně 10 % místa je zřízeno na místní úrovni a 90 % prostoru je zřízena v cloudu. Například pokud jste zřídit 1 TB sdílené složky, 100 GB by se nasytit v místním prostoru a 900 GB by se používá v cloudu, když datové vrstvy. To zase znamená, že pokud vám dojdou všechny místní místo na zařízení, nelze zřídit vrstvené sdílené složky (protože 10 % požadované na místní úrovni nebude k dispozici).

### <a name="provisioned-capacity"></a>Zřízená kapacita

Maximální zřízená kapacita pro každý typ sdílené složky naleznete v následující tabulce.

| **Identifikátor omezení** | **Limit** |
| --- | --- |
| Minimální velikost vrstvené sdílené složky |500 GB |
| Maximální velikost vrstvené sdílené složky |20 TB |
| Minimální velikost místně připnuté sdílené složky |50 GB |
| Maximální velikost místně připnuté sdílené složky |2 TB |

## <a name="the-shares-blade"></a>Čepel Akcie

Nabídka **Sdílené složky** v okně souhrnu služby StorSimple zobrazuje seznam sdílených složek úložiště v daném poli StorSimple a umožňuje jejich správu.

![Radlice](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

Podíl se skládá z řady atributů:

* **Název sdílené položky** – popisný název, který musí být jedinečný a pomáhá identifikovat sdílenou složku.
* **Stav** – může být online nebo offline. Pokud je sdílená akcie offline, uživatelé sdílené položky k ní nebudou mít přístup.
* **Typ** – označuje, zda je **sdílená složky vrstvené** (výchozí) nebo **místně připnuté**.
* **Kapacita** – určuje množství dat použitých ve srovnání s celkovým množstvím dat, která mohou být uložena ve sdílené složce.
* **Popis** – volitelné nastavení, které pomáhá popsat sdílenou složku.
* **Oprávnění** – oprávnění systému souborů NTFS ke sdílené položce, kterou lze spravovat pomocí Průzkumníka Windows.
* **Zálohování** – V případě virtuálního pole StorSimple jsou všechny sdílené složky automaticky povoleny pro zálohování.

![Podrobnosti o sdílení](./media/storsimple-virtual-array-manage-shares/share-details.png)

Pomocí pokynů v tomto kurzu proveďte následující úkoly:

* Přidání sdílené složky
* Úprava sdílené složky
* Přepzení sdílené položky do offline
* Odstranění sdílené složky

## <a name="add-a-share"></a>Přidání sdílené složky

1. V okně souhrnu služby StorSimple klikněte na **+ Přidat sdílenou složku** z panelu příkazů. Tím se otevře přidat **sdílet** okno.

    ![Přidat sdílenou složku](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. V okně **Přidat sdílenou** položku postupujte takto:
   
   1. Do pole **Sdílet název** zadejte jedinečný název sdílené položky. Název musí být řetězec, který obsahuje 3 až 127 znaků.

   2. Volitelný **popis** sdílené složky. Popis pomůže identifikovat vlastníky akcií.

   3. V rozevíracím seznamu **Typ** určete, jestli chcete vytvořit **vrstvenou** nebo **místně připnutou sdílenou** složku. U úloh, které vyžadují místní záruky, nízkou latenci a vyšší výkon, vyberte **místně připnutá sdílená složky**. Pro všechna ostatní data vyberte **Vrstvená sdílená** složky.

   4. V poli **Kapacita** zadejte velikost sdílené složky. Vrstvená sdílená položka musí být mezi 500 GB a 20 TB a místně připnutá sdílená položka musí být mezi 50 GB a 2 TB.

   5. V poli **Nastavit výchozí úplná oprávnění** přiřaďte oprávnění uživateli nebo skupině, která k této sdílené položce přistupuje. Zadejte jméno uživatele nebo skupiny _john@contoso.com_ uživatelů ve formátu. Doporučujeme použít skupinu uživatelů (namísto jednoho uživatele) k povolení oprávnění správce pro přístup k těmto sdíleným položkám. Jakmile tady přiřadíte oprávnění, můžete pak tato oprávnění upravit pomocí Průzkumníka souborů.
3. Po dokončení konfigurace sdílené složky klikněte na **Vytvořit**. Sdílená sdílená akcie bude vytvořena se zadaným nastavením a zobrazí se oznámení. Ve výchozím nastavení bude pro sdílenou složku povolena záloha.
4. Chcete-li potvrdit, že sdílená složky byla úspěšně vytvořena, přejděte do listy **sdílené složky.** Měli byste vidět podíl uvedený.
   
    ![Sdílet úspěch vytvoření](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>Úprava sdílené složky

Upravte sdílenou složku, když potřebujete změnit popis sdílené položky. Po vytvoření sdílené položky nelze změnit žádné jiné vlastnosti sdílené složky.

#### <a name="to-modify-a-share"></a>Úprava sdílené složky

1. V nastavení **Sdílené složky** v okně souhrnu služby StorSimple vyberte virtuální pole, ve kterém se nachází sdílená složky, ve které chcete sdílet.
2. **Vyberte** sdílenou složku, chcete-li zobrazit aktuální popis a upravit jej.
3. Změny uložte klepnutím na panel příkazů **Uložit.** Vaše zadané nastavení bude použito a zobrazí se oznámení.
   
    ![ Upravit sdílenou složku](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>Přepzení sdílené položky do offline

Při plánování úpravy nebo odstranění může být nutné sdílet sdílenou složku převést do offline. Pokud je sdílená akcie offline, není k dispozici pro přístup pro čtení a zápis. Budete muset převést sdílenou složku do offline na hostiteli i na zařízení.

#### <a name="to-take-a-share-offline"></a>Přepne sdílenou složku do offline

1. Před přepnutím do offline se ujistěte, že se dotyčná sdílená část nepoužívá.
2. Převeďte sdílenou složku v poli do pouzdla provedením následujících kroků:
   
    1. V nastavení **Sdílené složky** v okně souhrnu služby StorSimple vyberte virtuální pole, ve kterém se nachází sdílená složky, kterou chcete převzít offline.

    2. **Vyberte** sdílenou složku a klepněte **na tlačítko ...** (střídavě klepněte pravým tlačítkem myši na tento řádek) a v místní nabídce vyberte možnost **Převzít offline**.
     
        ![Offline sdílená](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. Zkontrolujte informace v okně **Take offline** a potvrďte, že operaci přijímáte. Chcete-li sdílet sdílenou složku převést do offline, klepněte na tlačítko **Převést do offline.** Zobrazí se oznámení o probíhající operaci.

    4. Chcete-li potvrdit, že sdílená složky byla úspěšně převzata do offline, přejděte do okna **Sdílené** položky. Stav sdílené položky byste měli vidět jako offline.

## <a name="delete-a-share"></a>Odstranění sdílené složky

> [!IMPORTANT]
> Sdílenou složku můžete odstranit pouze v případě, že je offline.


Chcete-li sdílenou složku odstranit, proveďte následující kroky.

#### <a name="to-delete-a-share"></a>Odstranění sdílené položky

1. V nastavení **Sdílené složky** v okně souhrnu služby StorSimple vyberte virtuální pole, ve kterém se nachází sdílená složky, kterou chcete odstranit.
2. **Vyberte** sdílenou složku a klepněte **na tlačítko ...** (střídavě klepněte pravým tlačítkem myši na tento řádek) a v místní nabídce vyberte **odstranit**.
   
    ![Odstranit sdílenou složku](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. Zkontrolujte stav sdílené složky, kterou chcete odstranit. Pokud sdílená sdílená složku, kterou chcete odstranit, není offline, přeneseji ji nejprve do offline. Postupujte podle pokynů v [části Převedení sdílené položky do offline](#take-a-share-offline).
4. Po zobrazení výzvy k potvrzení v okně **Odstranit** přijměte potvrzení a klepněte na tlačítko **Odstranit**. Sdílená sdílená složky budou nyní odstraněny a **sdílené** položky okno zobrazuje aktualizovaný seznam sdílených složek ve virtuálním poli.

## <a name="next-steps"></a>Další kroky
Naučte se [klonovat sdílenou složku StorSimple](storsimple-virtual-array-clone.md).

