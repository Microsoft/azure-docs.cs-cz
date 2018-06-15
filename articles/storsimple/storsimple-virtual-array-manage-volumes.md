---
title: Správa svazků v poli virtuální zařízení StorSimple | Microsoft Docs
description: Popisuje Správce zařízení StorSimple a vysvětluje, jak použít ho ke správě svazky na pole virtuální zařízení StorSimple.
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
ms.openlocfilehash: a507bf1866952cb79fa6334fed80c88cd207cd0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876051"
---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>Pomocí Správce zařízení StorSimple službou pro správu svazků na poli virtuální zařízení StorSimple

## <a name="overview"></a>Přehled

Tento kurz vysvětluje, jak používat službu StorSimple Manager zařízení k vytváření a správě svazky na pole virtuální zařízení StorSimple.

Služba Správce zařízení StorSimple je rozšíření na portálu Azure, která umožňuje spravovat z jedné webové rozhraní vašeho řešení StorSimple. Kromě správy sdílené složky a svazky, můžete službu StorSimple Manager zařízení zobrazit a spravovat zařízení, Zobrazit výstrahy a zobrazit a spravovat zásady zálohování a zálohování katalog.

## <a name="volume-types"></a>Typy svazku

Svazky zařízení StorSimple může být:

* **Místně vázaný**: Data v těchto svazcích zůstává v poli za všech okolností a nebudou distribuována do cloudu.
* **Víceúrovňová**: Data v těchto svazků můžete distribuována do cloudu. Když vytvoříte vrstvený svazek, je přibližně 10 % prostoru zřízený na místní úrovni a 90 % prostoru se zřizuje v cloudu. Například pokud jste zřídili svazku 1 TB, 100 GB by nacházet v místním prostoru a 900 GB se použije v cloudu při datové vrstvy. Pak z toho vyplývá, že pokud spustíte mimo veškeré volné místo na zařízení, nejde zřídit vrstvený svazek (protože 10 % požadované na místní úrovni nebudete mít k dispozici).

### <a name="provisioned-capacity"></a>Zřízená kapacita
Naleznete v následující tabulce maximální zřízená kapacita pro každý typ svazku.

| **Identifikátor limit**                                       | **Limit**     |
|------------------------------------------------------------|---------------|
| Minimální velikost vrstvený svazek                            | 500 GB        |
| Maximální velikost vrstvený svazek                            | 5 TB          |
| Minimální velikost místně vázaný svazek                    | 50 GB         |
| Maximální velikost místně vázaný svazek                    | 500 GB        |

## <a name="the-volumes-blade"></a>V okně svazky
**Svazky** nabídky na souhrnné okně vaší StorSimple služby zobrazí seznam svazků úložiště v daném poli StorSimple a umožňuje je spravovat.

![Okno svazky](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

Svazek se skládá z řady atributy:

* **Název svazku** – popisný název, který musí být jedinečný a pomáhá identifikovat svazku.
* **Stav** – může být online nebo offline. Pokud je svazek offline, není viditelná pro iniciátorů (serverů), které jsou povoleny přístup k použití svazku.
* **Typ** – Určuje, zda je svazek **nastavování** (výchozí) nebo **místně vázaný**.
* **Kapacita** – Určuje velikost dat používaných ve srovnání se celkové množství dat, které můžou být uložené iniciátor (server).
* **Zálohování** – v případě, že pole virtuální zařízení StorSimple, jsou všechny svazky automaticky povolené pro zálohování.
* **Připojení hostitele** – určuje iniciátorů (serverů), které mají přístup do tohoto svazku.

![Podrobnosti o svazky](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

Postupujte podle pokynů v tomto kurzu k provádění následujících úloh:

* Přidání svazku
* Upravit svazku
* Svazek převést do režimu offline
* Odstranění svazku

## <a name="add-a-volume"></a>Přidání svazku

1. Ze služby StorSimple souhrnu okna, klikněte na tlačítko **+ přidat svazek** z řádku nabídek. Otevře **Přidání svazku** okno.
   
    ![Přidání svazku](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. V **Přidání svazku** okno, postupujte takto:
   
   * V **název svazku** pole, zadejte jedinečný název pro svazek. Název musí být řetězec, který obsahuje 3 až 127 znaků.
   * V **typ** rozevíracího seznamu, určete, zda chcete vytvořit **nastavování** nebo **místně vázaný** svazku. Pro úlohy, které vyžadují místní záruky, nízkou latenci a vyšší výkon, vyberte **místně vázaný svazek**. Všechna ostatní data, vyberte **nastavování** svazku.
   * V **kapacity** pole, zadejte velikost svazku. Vrstvený svazek musí být mezi 500 GB a 5 TB a místně vázaný svazek musí být mezi 50 GB a 500 GB.
   * * Klikněte na tlačítko **připojení hostitele**, vyberte záznam řízení přístupu (ACR) odpovídající iniciátoru iSCSI, který chcete připojit tohoto svazku a pak klikněte na tlačítko **vyberte**.
3. Chcete-li přidat nový připojené hostitele, klikněte na tlačítko **přidat nový**, zadejte název hostitele a jeho iSCSI kvalifikovaný název IQN () a pak klikněte na tlačítko **přidat**.
   
    ![Přidání svazku](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. Po dokončení konfigurace svazku, klikněte na tlačítko **vytvořit**. Vytvoří se svazek se zadaným nastavením a zobrazí se oznámení na úspěšné vytvoření stejné. Ve výchozím nastavení zálohování budou povolené pro svazek.
5. Chcete-li potvrdit, že svazek byl úspěšně vytvořen, přejděte na **svazky** okno. Měli byste vidět svazku uvedené.
   
    ![Úspěšné vytvoření svazku](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>Upravit svazku

Upravte svazku, pokud potřebujete změnit hostitelů, které přístup ke svazku. Ostatní atributy svazku nemůže být upraven po vytvoření svazku.

#### <a name="to-modify-a-volume"></a>Chcete-li upravit svazku

1. Z **svazky** nastavení v okně Souhrn služby StorSimple, vyberte virtuální pole, v němž jsou uložena svazek, který chcete změnit.
2. **Vyberte** svazku a klikněte na tlačítko **připojení hostitele** zobrazení aktuálně připojené hostitele a upravit ho na jiný server.
   
    ![Upravit svazku](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. Uložte změny kliknutím **Uložit** panelu příkazů. Zadané nastavení uplatní a zobrazí se oznámení.

## <a name="take-a-volume-offline"></a>Svazek převést do režimu offline

Musíte uvedení svazku do režimu offline, když máte v úmyslu upravit nebo odstranit. Pokud je svazek offline, není k dispozici pro přístup pro čtení a zápis. Musíte provést offline svazek na hostiteli, a také na zařízení.

#### <a name="to-take-a-volume-offline"></a>Uvedení svazku do režimu offline

1. Ujistěte se, že dotyčný svazek není používána před přepnutím do režimu offline.
2. Proveďte offline svazek na hostiteli první. Tím se eliminuje všechny potenciální riziko poškození dat na svazku. Konkrétní kroky přečtěte si pokyny pro operační systém hostitele.
3. Po svazek na hostiteli je v režimu offline, trvat svazek v poli offline provedením následujících kroků:
   
   * Z **svazky** nastavení v okně Souhrn služby StorSimple, vyberte virtuální pole, v němž jsou uložena svazku chcete přepnout do režimu offline.
   * **Vyberte** svazku a klikněte na tlačítko **...**  (případně klikněte pravým tlačítkem na tento řádek) a v místní nabídce vyberte **převést do režimu offline**.
     
        ![Offline svazku](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * Informace v **převést do režimu offline** okno a potvrďte, že přijímáte operace. Klikněte na tlačítko **převést do režimu offline** uvedení svazku do režimu offline. Zobrazí se oznámení o probíhající operaci.
   * Chcete-li potvrdit, že svazek byl úspěšně převedeno do režimu offline, přejděte na **svazky** okno. Měli byste vidět stav svazku jako offline.
     
       ![Potvrzení offline svazku](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>Odstranění svazku

> [!IMPORTANT]
> Svazek můžete odstranit pouze v případě, že je offline.
> 
> 

Proveďte následující kroky odstranění svazku.

#### <a name="to-delete-a-volume"></a>Odstranění svazku

1. Z **svazky** nastavení v okně Souhrn služby StorSimple, vyberte virtuální pole, v němž jsou uložena svazek, který chcete odstranit.
2. **Vyberte** svazku a klikněte na tlačítko **...**  (případně klikněte pravým tlačítkem na tento řádek) a v místní nabídce vyberte **odstranit**.
   
    ![Odstranění svazku](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. Zkontrolujte stav svazku, který chcete odstranit. Pokud chcete odstranit svazek není v režimu offline, offline režimu se nejprve kroků v [do offline režimu svazku](#take-a-volume-offline).
4. Po zobrazení výzvy k potvrzení v **odstranit** okně přijměte potvrzení a klikněte na tlačítko **odstranit**. Svazek bude odstraněno a **svazky** okno se zobrazí aktualizovaný seznam svazků v rámci virtuální pole.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak [klonovat svazek StorSimple](storsimple-virtual-array-clone.md).

