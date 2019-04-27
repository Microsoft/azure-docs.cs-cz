---
title: Správa svazků na StorSimple Virtual Array | Dokumentace Microsoftu
description: Popisuje Správce zařízení StorSimple a vysvětluje, jak použít ho ke správě svazky na StorSimple Virtual Array.
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
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125793"
---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>Služba Správce zařízení StorSimple používá ke správě svazky na virtuálních polí StorSimple

## <a name="overview"></a>Přehled

Tento kurz vysvětluje, jak pomocí služby Správce zařízení StorSimple můžete vytvářet a spravovat svazcích StorSimple Virtual Array.

Služba Správce zařízení StorSimple je rozšíření na webu Azure Portal, který umožňuje spravovat řešení StorSimple v jediném webovém rozhraní. Kromě správy sdílených složek a svazků, můžete zobrazit a spravovat zařízení, zobrazovat výstrahy a zobrazit a spravovat zásady zálohování a katalog záloh ve službě Správce zařízení StorSimple.

## <a name="volume-types"></a>Typy svazků

Svazky zařízení StorSimple může být:

* **Místně připnuté**: Data v těchto svazcích za všech okolností zůstává v poli a uložená do cloudu.
* **Vrstvené**: Data v těchto svazků můžete uložená v cloudu. Když vytvoříte vrstvený svazek, přibližně 10 % prostoru musí být zřízena na místní úrovni a je zajištěno 90 % prostoru v cloudu. Například pokud jste zřídili svazku 1 TB, 100 GB by jsou umístěny v místním prostoru a v cloudu se použije 900 GB při datovou vrstvou. Pak z toho vyplývá, že pokud vyčerpáte veškeré volné místo na zařízení, nemůžete zřídit vrstveného svazku (protože 10 % u místní vrstvy vyžaduje nebude k dispozici).

### <a name="provisioned-capacity"></a>Zřízená kapacita
Naleznete v následující tabulce pro maximální zřízená kapacita pro každý typ svazku.

| **Identifikátor omezení**                                       | **Limit**     |
|------------------------------------------------------------|---------------|
| Minimální velikost vrstvený svazek                            | 500 GB        |
| Maximální velikost vrstvený svazek                            | 5 TB          |
| Minimální velikost místně vázaný svazek                    | 50 GB         |
| Maximální velikost místně vázaný svazek                    | 500 GB        |

## <a name="the-volumes-blade"></a>V okně svazky
**Svazky** nabídky na okno s přehledem StorSimple služby zobrazí seznam svazků úložiště v daném poli StorSimple a umožňuje je spravovat.

![Okno svazky](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

Svazek se skládá z řady atributy:

* **Název svazku** – popisný název, který musí být jedinečný a pomáhá identifikovat svazku.
* **Stav** – může být online nebo offline. Pokud je svazek offline, není viditelné pro iniciátorů (serverů), které mají povolený přístup ke svazku.
* **Typ** – Určuje, zda je svazek **Vrstvená** (výchozí) nebo **místně připnuté**.
* **Kapacita** – určuje množství dat, než celkové množství dat, která mohou být uloženy iniciátor (server).
* **Zálohování** – v případě, že StorSimple Virtual Array, všechny svazky jsou automaticky povoleny pro zálohování.
* **Připojení hostitelé** – určuje iniciátorů (serverů), které mají povolený přístup do tohoto svazku.

![Podrobnosti o svazky](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

Postupujte podle pokynů v tomto kurzu provádět následující úlohy:

* Přidat svazek
* Upravit svazek
* Svazek převést do režimu offline
* Odstranění svazku

## <a name="add-a-volume"></a>Přidat svazek

1. StorSimple okně přehledu služby, klikněte na **+ přidat svazek** na panelu příkazů. Otevře **přidat svazek** okno.
   
    ![Přidání svazku](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. V **přidat svazek** okno, postupujte takto:
   
   * V **název svazku** zadejte jedinečný název svazku. Název musí být řetězec, který obsahuje 3 až 127 znaků.
   * V **typ** rozevírací seznam seznamu, určete, jestli chcete vytvořit **Vrstvená** nebo **místně připnuté** svazku. Pro úlohy, které vyžadují místní záruky, nízkou latenci a vyšší výkon, vyberte **místně připnutý svazek**. Všechna ostatní data, vyberte **Vrstvená** svazku.
   * V **kapacity** zadejte velikost svazku. Vrstvený svazek musí být 500 GB až 5 TB a místně vázaný svazek musí být mezi 50 GB a 500 GB.
   * * Klikněte na tlačítko **připojení hostitelé**, vyberte záznam řízení přístupu (ACR) je iniciátor iSCSI, který chcete připojit k tomuto svazku a pak klikněte na odpovídající **vyberte**.
3. Přidání nové propojené hostitele, klikněte na tlačítko **přidat nový**, zadejte název hostitele a jeho iSCSI kvalifikovaný název (IQN) a pak klikněte na tlačítko **přidat**.
   
    ![Přidání svazku](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. Po dokončení konfigurace svazku, klikněte na tlačítko **vytvořit**. Vytvoří se svazek se zadaným nastavením a zobrazí se oznámení o úspěšném vytvoření stejné. Ve výchozím nastavení se povolí zálohování pro daný svazek.
5. Pokud chcete potvrdit, že svazek byl úspěšně vytvořen, přejděte na **svazky** okno. Měli byste vidět uvedené svazku.
   
    ![Úspěšné vytvoření svazku](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>Upravit svazek

Upravte svazek, když budete chtít změnit hostitele, kteří přístup ke svazku. Ostatní atributy svazku nelze změnit po vytvoření svazku.

#### <a name="to-modify-a-volume"></a>Chcete-li změnit svazek

1. Z **svazky** nastavení v okně s přehledem služby StorSimple vyberte virtuální pole, na kterém se nachází svazku, který chcete změnit.
2. **Vyberte** svazek a klikněte na tlačítko **připojení hostitelé** zobrazit aktuálně připojený hostitel nebo změnit na jiný server.
   
    ![Upravit svazek](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. Uložte změny kliknutím **Uložit** panelu příkazů. Zadané nastavení uplatní a zobrazí se oznámení.

## <a name="take-a-volume-offline"></a>Svazek převést do režimu offline

Budete muset převést svazku do režimu offline, když máte v úmyslu ho upravit nebo odstranit. Pokud je svazek offline, není k dispozici pro přístup pro čtení a zápis. Je potřeba provést svazek do režimu offline, na hostiteli, a také na zařízení.

#### <a name="to-take-a-volume-offline"></a>Uvedení svazku do režimu offline

1. Ujistěte se, že svazek dotyčný není používán před přepnutím do režimu offline.
2. Převedení svazku do režimu offline v hostiteli první. Tím se eliminují všechny potenciální riziko poškození dat na svazku. Konkrétní kroky použijte pokyny pro váš operační systém hostitele.
3. Jakmile je svazek na hostiteli offline, trvat, než svazku na pole v režimu offline podle následujících kroků:
   
   * Z **svazky** nastavení v okně s přehledem služby StorSimple vyberte virtuální pole, na kterém se nachází svazku, který chcete, aby vás k účasti v režimu offline.
   * **Vyberte** svazek a klikněte na tlačítko **...**  (případně klikněte pravým tlačítkem na tento řádek) a v místní nabídce vyberte **převést do režimu offline**.
     
        ![Svazek offline](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * Zkontrolujte informace **převést do režimu offline** blade a potvrdit svůj souhlas s operaci. Klikněte na tlačítko **převést do režimu offline** uvedení svazku do režimu offline. Zobrazí se oznámení o probíhající operace.
   * Pokud chcete potvrdit, že svazek bylo úspěšně převedeno do režimu offline, přejděte na **svazky** okno. Zobrazí se stav svazku jako offline.
     
       ![Potvrzení offline svazku](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>Odstranění svazku

> [!IMPORTANT]
> Svazku můžete odstranit pouze v případě, že je offline.
> 
> 

Proveďte následující kroky k odstranění svazku.

#### <a name="to-delete-a-volume"></a>Chcete-li odstranit svazek

1. Z **svazky** nastavení v okně s přehledem služby StorSimple vyberte virtuální pole, na kterém se nachází svazku, který chcete odstranit.
2. **Vyberte** svazek a klikněte na tlačítko **...**  (případně klikněte pravým tlačítkem na tento řádek) a v místní nabídce vyberte **odstranit**.
   
    ![Odstranit svazek](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. Zkontrolujte stav svazku, který chcete odstranit. Pokud chcete odstranit svazek není v režimu offline, odpojit, ji nejprve podle kroků v [svazek převést do režimu offline](#take-a-volume-offline).
4. Po zobrazení výzvy k potvrzení **odstranit** okně přijměte potvrzení a klikněte na tlačítko **odstranit**. Svazek se teď odstraní a **svazky** okno se zobrazí aktualizovaný seznam svazků v rámci virtuální pole.

## <a name="next-steps"></a>Další postup

Zjistěte, jak [klonovat svazek StorSimple](storsimple-virtual-array-clone.md).

