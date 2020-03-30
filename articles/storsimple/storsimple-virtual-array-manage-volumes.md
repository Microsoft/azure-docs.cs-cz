---
title: Správa svazků ve virtuálním poli StorSimple | Dokumenty společnosti Microsoft
description: Popisuje Správce zařízení StorSimple a vysvětluje, jak jej použít ke správě svazků ve virtuálním poli StorSimple.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: caa6a26b-b7ba-4a05-b092-1a79450225cf
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: a233a9deb58a7c1abc87a622a4f1f2581ee2e477
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "62125793"
---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>Použití služby Správce zařízení StorSimple ke správě svazků ve službě StorSimple Virtual Array

## <a name="overview"></a>Přehled

Tento kurz vysvětluje, jak pomocí služby StorSimple Device Manager vytvářet a spravovat svazky na vašem virtuálním poli StorSimple.

Služba StorSimple Device Manager je rozšíření na webu Azure Portal, které umožňuje spravovat vaše řešení StorSimple z jediného webového rozhraní. Kromě správy sdílených složek a svazků můžete pomocí služby StorSimple Device Manager zobrazit a spravovat zařízení, zobrazit výstrahy a zobrazit a spravovat zásady zálohování a katalog zálohování.

## <a name="volume-types"></a>Typy svazků

StorSimple svazky mohou být:

* **Místně připnuté**: Data v těchto svazcích zůstanou v poli po celou dobu a nepřelije se do cloudu.
* **Vrstvené**: Data v těchto svazcích se mohou přelít do cloudu. Když vytvoříte vrstvený svazek, přibližně 10 % místa je zřízena na místní úrovni a 90 % prostoru je zřízena v cloudu. Například pokud jste zřídit svazek 1 TB, 100 GB by se nasytit v místním prostoru a 900 GB by se používá v cloudu, když datové vrstvy. To zase znamená, že pokud vám dojde všechny místní místo na zařízení, nelze zřídit vrstvený svazek (protože 10 % požadované na místní úrovni nebude k dispozici).

### <a name="provisioned-capacity"></a>Zřízená kapacita
Maximální zřízená kapacita pro každý typ svazku naleznete v následující tabulce.

| **Identifikátor omezení**                                       | **Limit**     |
|------------------------------------------------------------|---------------|
| Minimální velikost vrstveného svazku                            | 500 GB        |
| Maximální velikost vrstveného svazku                            | 5 TB          |
| Minimální velikost místně vázanýho svazku                    | 50 GB         |
| Maximální velikost místně vázanýho svazku                    | 500 GB        |

## <a name="the-volumes-blade"></a>Čepel Objemy
V nabídce Svazky v okně souhrnu **služby** StorSimple se zobrazí seznam svazků úložiště v daném poli StorSimple a umožňuje jejich správu.

![Objemy čepele](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

Svazek se skládá z řady atributů:

* **Název svazku** – popisný název, který musí být jedinečný a pomáhá identifikovat svazek.
* **Stav** – může být online nebo offline. Pokud je svazek offline, není viditelný pro iniciátory (servery), kterým je povolen přístup k použití svazku.
* **Typ** – označuje, zda je svazek **vrstvený** (výchozí) nebo **místně vázaný**.
* **Kapacita** – určuje množství použitých dat ve srovnání s celkovým množstvím dat, která může iniciátor (server) uložit.
* **Zálohování** – V případě virtuálního pole StorSimple jsou všechny svazky automaticky povoleny pro zálohování.
* **Připojení hostitelé** – Určuje iniciátory (servery), kterým je povolen přístup k tomuto svazku.

![Podrobnosti o svazcích](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

Pomocí pokynů v tomto kurzu proveďte následující úkoly:

* Přidání svazku
* Úprava svazku
* Přepne svazek offline
* Odstranění svazku

## <a name="add-a-volume"></a>Přidání svazku

1. V okně souhrnu služby StorSimple klepněte na tlačítko **+ Přidat svazek** z panelu příkazů. Tím se otevře okno **Přidat hlasitost.**
   
    ![Přidání svazku](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. V okně **Přidat objem** postupujte takto:
   
   * Do pole **Název svazku** zadejte jedinečný název svazku. Název musí být řetězec, který obsahuje 3 až 127 znaků.
   * V rozevíracím seznamu **Typ** určete, zda se má vytvořit **vrstvený** nebo **místně vázaný** svazek. U úloh, které vyžadují místní záruky, nízkou latenci a vyšší výkon, vyberte **místně vázaný svazek**. Pro všechna ostatní data vyberte **Vrstvený** svazek.
   * V poli **Kapacita** zadejte velikost svazku. Vrstvený svazek musí být mezi 500 GB a 5 TB a místně vázaný svazek musí být mezi 50 GB a 500 GB.
   * * Klepněte na **položku Připojení hostitelé**, vyberte záznam řízení přístupu (ACR) odpovídající iniciátoru iSCSI, který chcete připojit k tomuto svazku, a klepněte na tlačítko **Vybrat**.
3. Chcete-li přidat nového připojeného hostitele, klepněte na tlačítko **Přidat nový**, zadejte název hostitele a jeho kvalifikovaný název iSCSI (IQN) a klepněte na tlačítko **Přidat**.
   
    ![Přidání svazku](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. Po dokončení konfigurace svazku klepněte na **tlačítko Vytvořit**. Svazek bude vytvořen se zadaným nastavením a zobrazí se oznámení o úspěšném vytvoření stejného. Ve výchozím nastavení bude pro svazek povoleno zálohování.
5. Chcete-li ověřit, zda byl svazek úspěšně vytvořen, přejděte do okna **Svazky.** Měl by se zobrazit svazek uvedený.
   
    ![Úspěch vytvoření svazku](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>Úprava svazku

Upravte svazek, když potřebujete změnit hostitele, kteří k němu přistupují. Ostatní atributy svazku nelze po vytvoření svazku změnit.

#### <a name="to-modify-a-volume"></a>Úprava svazku

1. V nastavení Svazky v okně souhrnu **služby** StorSimple vyberte virtuální pole, ve kterém se nachází svazek, který chcete upravit.
2. **Vyberte** svazek a klepnutím na **připojené hostitele** zobrazte aktuálně připojeného hostitele a upravte jej na jiný server.
   
    ![Upravit hlasitost](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. Změny uložte klepnutím na panel příkazů **Uložit.** Vaše zadané nastavení bude použito a zobrazí se oznámení.

## <a name="take-a-volume-offline"></a>Přepne svazek offline

Při jeho plánování jeho úpravy nebo odstranění může být nutné převést svazek do offline. Pokud je svazek offline, není k dispozici pro přístup pro čtení a zápis. Budete muset vzít svazek offline na hostiteli i na zařízení.

#### <a name="to-take-a-volume-offline"></a>Přepne svazek offline

1. Před přepnutím do provozu se ujistěte, že daný svazek není používán.
2. Nejprve přenesete svazek do úpájení na hostiteli. Tím se eliminuje potenciální riziko poškození dat na svazku. Konkrétní kroky naleznete v pokynech pro hostitelský operační systém.
3. Po vypnutí svazku na hostiteli převeďte svazek v poli do offline provedením následujících kroků:
   
   * V nastavení Svazky na okno souhrnu **služby** StorSimple vyberte virtuální pole, na kterém se nachází svazek, který chcete převzít offline.
   * **Vyberte** hlasitost a klepněte na **tlačítko ...** (střídavě klepněte pravým tlačítkem myši v tomto řádku) a v místní nabídce vyberte **Možnost Převzít offline**.
     
        ![Svazek offline](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * Zkontrolujte informace v okně **Take offline** a potvrďte, že operaci přijímáte. Chcete-li svazek převést do funkce offline, klepněte na tlačítko **Převést do offline** linky. Zobrazí se oznámení o probíhající operaci.
   * Chcete-li ověřit, zda byl svazek úspěšně přepnut do funkce offline, přejděte do okna **Svazky.** Stav svazku byste měli vidět jako offline.
     
       ![Potvrzení svazku offline](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>Odstranění svazku

> [!IMPORTANT]
> Svazek můžete odstranit pouze v případě, že je offline.
> 
> 

Chcete-li svazek odstranit, proveďte následující kroky.

#### <a name="to-delete-a-volume"></a>Odstranění svazku

1. V nastavení Svazky v okně souhrnu **služby** StorSimple vyberte virtuální pole, na kterém se nachází svazek, který chcete odstranit.
2. **Vyberte** hlasitost a klepněte na **tlačítko ...** (střídavě klepněte pravým tlačítkem myši v tomto řádku) a v místní nabídce vyberte **odstranit**.
   
    ![Odstranit svazek](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. Zkontrolujte stav svazku, který chcete odstranit. Pokud svazek, který chcete odstranit, není offline, přepnutí do offline nejprve postupujte podle kroků v [části Převedení svazku offline](#take-a-volume-offline).
4. Po zobrazení výzvy k potvrzení v okně **Odstranit** přijměte potvrzení a klepněte na tlačítko **Odstranit**. Svazek bude nyní odstraněn a v okně **Svazky** se zobrazí aktualizovaný seznam svazků ve virtuálním poli.

## <a name="next-steps"></a>Další kroky

Naučte se [klonovat svazek StorSimple](storsimple-virtual-array-clone.md).

