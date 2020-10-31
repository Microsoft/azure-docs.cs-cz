---
title: Správa svazků ve virtuálním poli StorSimple | Microsoft Docs
description: Popisuje StorSimple Device Manager a vysvětluje, jak ho použít ke správě svazků ve virtuálním poli StorSimple.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: caa6a26b-b7ba-4a05-b092-1a79450225cf
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 79e3ce8c1605e5d68ff44901f53854d2f5f10abc
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129945"
---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>Použití služby Správce zařízení StorSimple ke správě svazků ve službě StorSimple Virtual Array

## <a name="overview"></a>Přehled

V tomto kurzu se dozvíte, jak pomocí služby StorSimple Device Manager vytvářet a spravovat svazky ve virtuálním poli StorSimple.

Služba StorSimple Device Manager je rozšíření v Azure Portal, které umožňuje spravovat řešení StorSimple z jediného webového rozhraní. Kromě správy sdílených složek a svazků můžete pomocí služby StorSimple Device Manager zobrazovat a spravovat zařízení, zobrazovat výstrahy a zobrazovat a spravovat zásady zálohování a katalog záloh.

## <a name="volume-types"></a>Typy svazků

StorSimple svazky mohou být:

* **Místně připnuté** : data v těchto svazcích zůstanou v poli nepřetržitě a nepřesahují se do cloudu.
* **Vrstvený** : data v těchto svazcích můžou přelití do cloudu. Když vytvoříte vrstvený svazek, přibližně 10% místa se zřídí na místní úrovni a 90% místa se zřídí v cloudu. Pokud jste například zřídili svazek o velikosti 1 TB, 100 GB by se nacházelo v místním prostoru a v cloudu se v případě datových vrstev používalo 900 GB. To znamená, že pokud vyčerpáte místo na svém zařízení, nemůžete zřídit vrstvený svazek (protože na místní úrovni nebude k dispozici 10%).

### <a name="provisioned-capacity"></a>Zřízená kapacita
V následující tabulce najdete maximální zřízenou kapacitu pro každý typ svazku.

| **Identifikátor omezení**                                       | **Počtu**     |
|------------------------------------------------------------|---------------|
| Minimální velikost vrstveného svazku                            | 500 GB        |
| Maximální velikost vrstveného svazku                            | 5 TB          |
| Minimální velikost místně připnutého svazku                    | 50 GB         |
| Maximální velikost místně připnutého svazku                    | 200 GB        |

## <a name="the-volumes-blade"></a>Okno svazky
Nabídka **svazky** v okně s přehledem služby StorSimple zobrazuje seznam svazků úložiště v daném poli StorSimple a umožňuje jejich správu.

![Okno svazky](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

Svazek se skládá z řady atributů:

* **Název svazku** – popisný název, který musí být jedinečný a pomáhá identifikovat svazek.
* **Stav** – může být online nebo offline. Pokud je svazek offline, není viditelný pro iniciátory (servery), které mají povolený přístup k použití svazku.
* **Typ** – určuje, jestli je svazek **vrstvený** (výchozí) nebo **připojený místně** .
* **Capacity** – určuje množství dat použitých ve srovnání s celkovým množstvím dat, které může iniciátor (Server) uložit.
* **Zálohování** – v případě virtuálního pole StorSimple jsou všechny svazky automaticky povoleny pro zálohování.
* **Připojení hostitelé** – určuje iniciátory (servery), které mají povolený přístup k tomuto svazku.

![Podrobnosti o svazcích](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

Pomocí pokynů v tomto kurzu proveďte následující úlohy:

* Přidat svazek
* Úprava svazku
* Přepnout svazek do offline režimu
* Odstranění svazku

## <a name="add-a-volume"></a>Přidat svazek

1. V okně s přehledem služby StorSimple klikněte na panelu příkazů na **+ Přidat svazek** . Otevře se okno **Přidat svazek** .
   
    ![Snímek obrazovky se zobrazí tlačítko Přidat svazek a podokno přidat svazek.](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. V okně **Přidat svazek** udělejte toto:
   
   * Do pole **název svazku** zadejte jedinečný název svazku. Název musí být řetězec, který obsahuje 3 až 127 znaků.
   * V rozevíracím seznamu **typ** určete, zda má být vytvořen **vrstvený** nebo **místně připojený** svazek. Pro úlohy, které vyžadují místní záruky, nízkou latenci a vyšší výkon, vyberte **místně připojený svazek** . U všech ostatních dat vyberte **vrstvený** svazek.
   * V poli **kapacita** zadejte velikost svazku. Vrstvený svazek musí být v rozsahu od 500 GB do 5 TB a místně připojený svazek musí být v rozmezí 50 GB až 500 GB.
   * * Klikněte na **připojené hostitele** , vyberte záznam řízení přístupu (ACR) odpovídající iniciátoru iSCSI, ke kterému se chcete připojit, a potom klikněte na **Vybrat** .
3. Chcete-li přidat nového připojeného hostitele, klikněte na tlačítko **Přidat nový** , zadejte název hostitele a jeho kvalifikovaný název iSCSI (IQN) a pak klikněte na tlačítko **Přidat** .
   
    ![Snímek obrazovky se zobrazí v podokně připojené hostitele, kde můžete přidat nové.](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. Po dokončení konfigurace svazku klikněte na **vytvořit** . Vytvoří se svazek se zadaným nastavením a zobrazí se oznámení o úspěšném vytvoření stejného. Ve výchozím nastavení bude pro svazek povolená záloha.
5. Pokud chcete ověřit, že se svazek úspěšně vytvořil, otevřete okno **svazky** . Měl by se zobrazit uvedený svazek.
   
    ![Svazek se úspěšně vytvořil.](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>Úprava svazku

Pokud potřebujete změnit hostitele, kteří přistupují ke svazku, upravte svazek. Po vytvoření svazku nelze změnit ostatní atributy svazku.

#### <a name="to-modify-a-volume"></a>Úprava svazku

1. Z nastavení **svazky** v okně s přehledem služby StorSimple vyberte virtuální pole, na kterém se svazek, který chcete upravit, nachází.
2. **Vyberte** svazek a kliknutím na **připojené hostitele** Zobrazte aktuálně připojeného hostitele a upravte ho na jiném serveru.
   
    ![Upravit svazek](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. Uložte změny kliknutím na panel příkazů **Uložit** . Zadané nastavení se použije a zobrazí se oznámení.

## <a name="take-a-volume-offline"></a>Přepnout svazek do offline režimu

Pokud plánujete upravit nebo odstranit, možná budete muset svazek převést do režimu offline. Pokud je svazek offline, není k dispozici pro přístup pro čtení i zápis. Svazek budete muset převést do režimu offline na hostiteli i na zařízení.

#### <a name="to-take-a-volume-offline"></a>Převedení svazku do režimu offline

1. Před přepnutím do režimu offline se ujistěte, že se svazek nepoužívá.
2. Napřed si svazek na hostiteli převeďte do režimu offline. Tím se eliminuje případné riziko poškození dat na svazku. Konkrétní postup najdete v pokynech k hostitelskému operačnímu systému.
3. Jakmile je svazek na hostiteli offline, převeďte svazek na pole v režimu offline provedením následujících kroků:
   
   * Z nastavení **svazky** v okně s přehledem služby StorSimple vyberte virtuální pole, na kterém se nachází svazek, který chcete převést do režimu offline.
   * **Vyberte** svazek a klikněte na **...** (střídavě klikněte na něj pravým tlačítkem myši) a v místní nabídce vyberte možnost **převést do režimu offline** .
     
        ![Offline svazek](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * Přečtěte si informace v okně **převést do režimu offline** a potvrďte přijetí operace. Pokud chcete svazek převést do režimu offline, klikněte na **převést do režimu offline** . Zobrazí se oznámení o probíhající operaci.
   * Chcete-li ověřit, že byl svazek úspěšně přepnut do režimu offline, otevřete okno **svazky** . Stav svazku by měl být v režimu offline.
     
       ![Potvrzení svazku offline](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>Odstranění svazku

> [!IMPORTANT]
> Svazek lze odstranit pouze v případě, že je offline.
> 
> 

Provedením následujících kroků odstraňte svazek.

#### <a name="to-delete-a-volume"></a>Odstranění svazku

1. Z nastavení **svazky** v okně s přehledem služby StorSimple vyberte virtuální pole, na kterém se svazek, který chcete odstranit, nachází.
2. **Vyberte** svazek a klikněte na **...** (střídavě klikněte pravým tlačítkem myši na tento řádek) a v místní nabídce vyberte **Odstranit** .
   
    ![Odstranit svazek](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. Ověřte stav svazku, který chcete odstranit. Pokud svazek, který chcete odstranit, není v režimu offline, nejprve ho proveďte offline, a to podle kroků v části [převedení svazku do režimu offline](#take-a-volume-offline).
4. Po zobrazení výzvy k potvrzení v okně **Odstranit** přijměte potvrzení a klikněte na **Odstranit** . Svazek se teď odstraní a okno **svazky** zobrazí aktualizovaný seznam svazků v rámci virtuálního pole.

## <a name="next-steps"></a>Další kroky

Naučte se [klonovat svazek StorSimple](storsimple-virtual-array-clone.md).

