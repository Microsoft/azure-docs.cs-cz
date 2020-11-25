---
title: Správa sdílených složek StorSimple virtuálních polí | Microsoft Docs
description: Popisuje StorSimple Device Manager a vysvětluje, jak ho použít ke správě sdílených složek ve virtuálním poli StorSimple.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 0a799c83-fde5-4f3f-af0e-67535d1882b6
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 340b910319cabe3379bdb1bad1c09bc71c17f072
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994940"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>Použití služby Správce zařízení StorSimple ke správě sdílených složek ve službě StorSimple Virtual Array

## <a name="overview"></a>Přehled

V tomto kurzu se dozvíte, jak pomocí služby StorSimple Device Manager vytvářet a spravovat sdílené složky ve vašem virtuálním poli StorSimple.

Služba StorSimple Device Manager je rozšíření v Azure Portal, které umožňuje spravovat řešení StorSimple z jediného webového rozhraní. Kromě správy sdílených složek a svazků můžete pomocí služby StorSimple Device Manager zobrazovat a spravovat zařízení, zobrazovat výstrahy, spravovat zásady zálohování a spravovat katalog záloh.

## <a name="share-types"></a>Typy sdílení

Sdílené složky StorSimple můžou být:

* **Místně připnuté**: data v těchto sdílených složkách zůstanou v poli nepřetržitě a nepřesahují se do cloudu.
* **Vrstveno**: data v těchto sdílených složkách můžou přelití do cloudu. Když vytvoříte vrstvenou sdílenou složku, na místní úrovni se zřídí přibližně 10% místa a 90% místa se zřídí v cloudu. Pokud jste například zřídili sdílení o velikosti 1 TB, 100 GB se bude nacházet v místním prostoru a v cloudu se v případě datových vrstev použije 900 GB. To znamená, že pokud vyčerpáte z místního prostoru na zařízení, nemůžete zřídit vrstvenou sdílenou složku (protože na místní úrovni nebude k dispozici 10%).

### <a name="provisioned-capacity"></a>Zřízená kapacita

Pro maximální zřízenou kapacitu pro každý typ sdílené složky použijte následující tabulku.

| **Identifikátor omezení** | **Omezení** |
| --- | --- |
| Minimální velikost vrstveného sdílení |500 GB |
| Maximální velikost vrstvené sdílené složky |20 TB |
| Minimální velikost místně připnuté sdílené složky |50 GB |
| Maximální velikost místně připnuté sdílené složky |2 TB |

## <a name="the-shares-blade"></a>Okno sdílené složky

V nabídce **akcie** v okně s přehledem služby StorSimple se zobrazuje seznam sdílených složek úložiště v daném poli StorSimple a umožňuje jejich správu.

![Okno sdílené složky](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

Sdílená složka se skládá z řady atributů:

* **Název sdílené složky** – popisný název, který musí být jedinečný a pomáhá identifikovat sdílenou složku.
* **Stav** – může být online nebo offline. Pokud je sdílená složka offline, uživatelé sdílené složky k ní nebudou mít přístup.
* **Typ** – určuje, jestli je sdílená složka ve **vrstvách** (výchozí nastavení) nebo **lokálně připnuté**.
* **Capacity** – určuje množství dat, která se mají použít ve srovnání s celkovým množstvím dat, která se dají ukládat na sdílenou složku.
* **Popis** – volitelné nastavení, které pomáhá popsání sdílené složky.
* **Oprávnění** – oprávnění NTFS ke sdílené složce, kterou lze spravovat pomocí Průzkumníka Windows.
* **Zálohování** – v případě virtuálního pole StorSimple jsou všechny sdílené složky automaticky povolené pro zálohování.

![Podrobnosti o sdílených složkách](./media/storsimple-virtual-array-manage-shares/share-details.png)

Pomocí pokynů v tomto kurzu proveďte následující úlohy:

* Přidání sdílené složky
* Úprava sdílené složky
* Pořídit sdílení offline
* Odstranění sdílené složky

## <a name="add-a-share"></a>Přidání sdílené složky

1. V okně s přehledem služby StorSimple klikněte na panelu příkazů na **+ Přidat sdílenou složku** . Otevře se okno **Přidat sdílenou složku** .

    ![Přidat sdílenou složku](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. V okně **Přidat sdílenou složku** proveďte následující kroky:
   
   1. Do pole **název sdílené složky** zadejte jedinečný název pro sdílenou složku. Název musí být řetězec, který obsahuje 3 až 127 znaků.

   2. Volitelný **Popis** sdílené složky. Popis vám pomůže identifikovat vlastníky sdílené složky.

   3. V rozevíracím seznamu **typ** určete, zda chcete vytvořit **vrstvenou** nebo **místně připojenou** sdílenou složku. Pro úlohy, které vyžadují místní záruky, nízkou latenci a vyšší výkon, vyberte **místně připojenou sdílenou složku**. U všech ostatních dat vyberte **vrstvenou** sdílenou složku.

   4. V poli **kapacita** zadejte velikost sdílené složky. Vrstvená sdílená složka musí být v rozmezí 500 GB až 20 TB a místně připojená sdílená složka musí být v rozmezí 50 GB až 2 TB.

   5. V poli **nastavit výchozí všechna oprávnění na** pole přiřaďte uživateli nebo skupině, která přistupuje k této sdílené složce. Zadejte jméno uživatele nebo skupiny uživatelů ve _john@contoso.com_ formátu. Pro povolení přístupu k těmto sdíleným složkám doporučujeme použít skupinu uživatelů (místo jednoho uživatele). Jakmile tady přiřadíte oprávnění, můžete pak tato oprávnění upravit pomocí Průzkumníka souborů.
3. Po dokončení konfigurace sdílené složky klikněte na **vytvořit**. Bude vytvořena sdílená složka se zadaným nastavením a zobrazí se oznámení. Ve výchozím nastavení bude pro sdílenou složku povolená záloha.
4. Pokud chcete potvrdit, že se sdílená složka úspěšně vytvořila, otevřete okno **sdílené složky** . Měla by se zobrazit uvedená sdílená složka.
   
    ![Nasdílení úspěšného vytvoření](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>Úprava sdílené složky

Pokud potřebujete změnit popis sdílené složky, upravte ji. Po vytvoření sdílené složky nelze upravovat žádné další vlastnosti sdílení.

#### <a name="to-modify-a-share"></a>Úprava sdílené složky

1. V nastavení **sdílené složky** v okně s přehledem služby StorSimple vyberte virtuální pole, na kterém se nachází sdílená složka, kterou chcete upravit.
2. **Vyberte** sdílenou složku pro zobrazení aktuálního popisu a upravte ji.
3. Uložte změny kliknutím na panel příkazů **Uložit** . Zadané nastavení se použije a zobrazí se oznámení.
   
    ![ Upravit sdílenou složku](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>Pořídit sdílení offline

Pokud plánujete upravit nebo odstranit, možná budete muset nastavit sdílení offline. Pokud je sdílená složka offline, není k dispozici pro přístup pro čtení i zápis. Tuto sdílenou složku budete muset převést do režimu offline na hostiteli i na zařízení.

#### <a name="to-take-a-share-offline"></a>Chcete-li nastavit sdílení offline

1. Ujistěte se, že se sdílená složka nepoužívá před tím, než ji převezmete offline.
2. Převeďte sdílenou složku na pole v režimu offline provedením následujících kroků:
   
    1. V nastavení **sdílené složky** v okně s přehledem služby StorSimple vyberte virtuální pole, ve kterém se má sdílet sdílená složka, kterou chcete převést do režimu offline.

    2. **Vyberte** sdílenou složku a klikněte na **...** (střídavě klikněte pravým tlačítkem myši na tento řádek) a v místní nabídce vyberte možnost **převést do režimu offline**.
     
        ![Offline sdílení](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. Přečtěte si informace v okně **převést do režimu offline** a potvrďte přijetí operace. Kliknutím na **převést do režimu offline** převeďte sdílení do offline režimu. Zobrazí se oznámení o probíhající operaci.

    4. Pokud chcete potvrdit, že se sdílená složka úspěšně převzala do režimu offline, otevřete okno **sdílené složky** . Stav sdílené složky byste měli vidět v režimu offline.

## <a name="delete-a-share"></a>Odstranění sdílené složky

> [!IMPORTANT]
> Sdílenou složku můžete odstranit pouze v případě, že je offline.


Provedením následujících kroků odstraňte sdílenou složku.

#### <a name="to-delete-a-share"></a>Odstranění sdílené složky

1. V nastavení **sdílené složky** v okně s přehledem služby StorSimple vyberte virtuální pole, na kterém se má sdílet sdílená složka, kterou chcete odstranit.
2. **Vyberte** sdílenou složku a klikněte na **...** (střídavě klikněte pravým tlačítkem myši na tento řádek) a v místní nabídce vyberte **Odstranit**.
   
    ![Odstranit sdílenou složku](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. Ověřte stav sdílené složky, kterou chcete odstranit. Pokud sdílená složka, kterou chcete odstranit, není v režimu offline, nejprve ji převeďte do režimu offline. Postupujte podle kroků popsaných v části [pořídit sdílení offline](#take-a-share-offline).
4. Po zobrazení výzvy k potvrzení v okně **Odstranit** přijměte potvrzení a klikněte na **Odstranit**. Sdílená složka se teď odstraní a okno **sdílené** složky zobrazí aktualizovaný seznam sdílených složek v rámci virtuálního pole.

## <a name="next-steps"></a>Další kroky
Naučte se [klonovat sdílenou složku StorSimple](storsimple-virtual-array-clone.md).

