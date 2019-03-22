---
title: Správa sdílených složek virtuálních polí StorSimple | Dokumentace Microsoftu
description: Popisuje Správce zařízení StorSimple a vysvětluje, jak použít ho ke správě sdílených složek na StorSimple Virtual Array.
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
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58092425"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>Použití služby Správce zařízení StorSimple pro správu sdílených složek na StorSimple Virtual Array

## <a name="overview"></a>Přehled

Tento kurz vysvětluje, jak použít službu Správce zařízení StorSimple k vytváření a správě sdílených složek na StorSimple Virtual Array.

Služba Správce zařízení StorSimple je rozšíření na webu Azure Portal, který umožňuje spravovat řešení StorSimple v jediném webovém rozhraní. Kromě správy sdílených složek a svazků, můžete zobrazit a spravovat zařízení, Zobrazit výstrahy, Správa zásad zálohování a Správa katalogu záloh ve službě Správce zařízení StorSimple.

## <a name="share-types"></a>Sdílení typů

Sdílené složky StorSimple může být:

* **Místně připnuté**: Data v těchto sdílených složek za všech okolností zůstává v poli a uložená do cloudu.
* **Vrstvené**: Data v těchto sdílených složek můžete uložená v cloudu. Když vytváříte vrstvenou sdílenou složku, přibližně 10 % prostoru musí být zřízena na místní úrovni a je zajištěno 90 % prostoru v cloudu. Například pokud jste zřídili sdílenou složku 1 TB, 100 GB by jsou umístěny v místním prostoru a v cloudu se použije 900 GB při datovou vrstvou. Pak z toho vyplývá, že pokud vyčerpáte veškeré volné místo na zařízení, nemůžete zřídit vrstvenou sdílenou složku (protože 10 % u místní vrstvy vyžaduje nebude k dispozici).

### <a name="provisioned-capacity"></a>Zřízená kapacita

Naleznete v následující tabulce pro maximální zřízená kapacita pro každý typ sdílené složky.

| **Identifikátor omezení** | **Limit** |
| --- | --- |
| Minimální velikost vrstvenou sdílenou složku |500 GB |
| Maximální velikost vrstvenou sdílenou složku |20 TB |
| Minimální velikost místně připojené sdílené složky |50 GB |
| Maximální velikost místně připojené sdílené složky |2 TB |

## <a name="the-shares-blade"></a>V okně sdílené složky

**Sdílené složky** nabídky na okno s přehledem StorSimple služby zobrazí seznam sdílených složek úložiště v daném poli StorSimple a umožňuje je spravovat.

![Okno sdílené složky](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

Sdílenou složku se skládá z řady atributy:

* **Název sdílené složky** – popisný název, který musí být jedinečný a pomáhá identifikovat sdílené složky.
* **Stav** – může být online nebo offline. Pokud sdílenou složku je offline, uživatele sdílené složky nebude možné k němu přistupovat.
* **Typ** – Určuje, zda je sdílená složka **Vrstvená** (výchozí) nebo **místně připnuté**.
* **Kapacita** – určuje množství dat, než celkové množství dat, které mohou být uloženy ve sdílené složce.
* **Popis** – volitelné nastavení, která pomáhá popisují sdílenou složku.
* **Oprávnění** – systému souborů NTFS oprávnění ke sdílené složce, které jde spravovat pomocí Průzkumníka Windows.
* **Zálohování** – v případě, že StorSimple Virtual Array, jsou automaticky povoleny všechny sdílené složky pro zálohu.

![Podrobnosti o sdílené složky](./media/storsimple-virtual-array-manage-shares/share-details.png)

Postupujte podle pokynů v tomto kurzu provádět následující úlohy:

* Přidání sdílené složky
* Upravit sdílené složky
* Sdílenou složku převést do režimu offline
* Odstranění sdílené složky

## <a name="add-a-share"></a>Přidání sdílené složky

1. StorSimple okně přehledu služby, klikněte na **+ přidat sdílenou složku** na panelu příkazů. Otevře **přidat sdílenou složku** okno.

    ![Přidat sdílenou složku](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. V **přidat sdílenou složku** okno, postupujte takto:
   
   1. V **název sdílené složky** zadejte jedinečný název pro svou sdílenou složku. Název musí být řetězec, který obsahuje 3 až 127 znaků.

   2. Volitelně **popis** pro sdílenou složku. Popis vám pomůže identifikovat vlastníkům sdílené složky.

   3. V **typ** rozevírací seznam seznamu, určete, jestli chcete vytvořit **Vrstvená** nebo **místně připnuté** sdílet. Pro úlohy, které vyžadují místní záruky, nízkou latenci a vyšší výkon, vyberte **místně připojené sdílené složky**. Všechna ostatní data, vyberte **Vrstvená** sdílet.

   4. V **kapacity** zadejte velikost sdílené složky. Vrstvené sdílené složky musí být 500 GB až 20 TB a místně připojené sdílené složky musí být mezi 50 GB a 2 TB.

   5. V **nastavit výchozí úplné oprávnění** pole, přiřaďte oprávnění pro uživatele nebo skupinu, která přistupuje k této sdílené složce. Zadejte název uživatele nebo skupiny uživatelů v _john@contoso.com_ formátu. Doporučujeme použít skupiny uživatelů (místo jednoho uživatele) umožňující správu oprávnění pro přístup k těmto sdíleným složkám. Jakmile tady přiřadíte oprávnění, můžete pak tato oprávnění upravit pomocí Průzkumníka souborů.
3. Po dokončení konfigurace vaší sdílené složky, klikněte na tlačítko **vytvořit**. Vytvoří sdílenou složku se zadaným nastavením a zobrazí se oznámení. Ve výchozím nastavení budou povolené zálohování pro sdílenou složku.
4. Pokud chcete potvrdit, že je sdílená složka úspěšně vytvořená, přejděte na **sdílené složky** okno. Měli byste vidět sdílené složky uvedené.
   
    ![Úspěšné vytvoření sdílené složky](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>Upravit sdílené složky

Pokud potřebujete změnit popis sdílené složky, upravte sdílenou složku. Žádné jiné vlastnosti sdílené složky můžete změnit po vytvoření sdílené složky.

#### <a name="to-modify-a-share"></a>Chcete-li změnit sdílené složky

1. Z **sdílené složky** nastavení v okně s přehledem služby StorSimple vyberte virtuální pole, na kterém se nachází sdílené složky, které chcete změnit.
2. **Vyberte** sdílenou složku aktuální popis zobrazit a upravit ji.
3. Uložte změny kliknutím **Uložit** panelu příkazů. Zadané nastavení uplatní a zobrazí se oznámení.
   
    ![ Upravit sdílené složky](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>Sdílenou složku převést do režimu offline

Budete muset sdílenou složku převést do režimu offline, když máte v úmyslu ho upravit nebo odstranit. Když sdílenou složku je offline, není k dispozici pro přístup pro čtení a zápis. Je potřeba provést offline sdílenou složku na hostiteli, a také na zařízení.

#### <a name="to-take-a-share-offline"></a>Chcete-li sdílenou složku převést do režimu offline

1. Ujistěte se, že sdílená složka dotyčný není používán před přepnutím do režimu offline.
2. Sdílené složky na pole v režimu offline reagovat provedením následujících kroků:
   
    1. Z **sdílené složky** nastavení v okně s přehledem služby StorSimple vyberte virtuální pole, na kterém se nachází sdílená složka, chcete, aby vás k účasti v režimu offline.

    2. **Vyberte** do sdílené složky a klikněte na tlačítko **...**  (případně klikněte pravým tlačítkem na tento řádek) a v místní nabídce vyberte **převést do režimu offline**.
     
        ![Sdílené složky offline](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. Zkontrolujte informace **převést do režimu offline** blade a potvrdit svůj souhlas s operaci. Klikněte na tlačítko **převést do režimu offline** na sdílenou složku převést do režimu offline. Zobrazí se oznámení o probíhající operace.

    4. Pokud chcete potvrdit, že sdílené složky bylo úspěšně převedeno do režimu offline, přejděte na **sdílené složky** okno. Zobrazí se stav sdílenou složku jako offline.

## <a name="delete-a-share"></a>Odstranění sdílené složky

> [!IMPORTANT]
> Sdílené složky můžete odstranit pouze v případě, že je offline.


Proveďte následující kroky, chcete-li odstranit sdílenou složku.

#### <a name="to-delete-a-share"></a>Chcete-li odstranit sdílenou složku

1. Z **sdílené složky** nastavení v okně s přehledem služby StorSimple vyberte virtuální pole, na kterém se nachází sdílené složky chcete odstranit.
2. **Vyberte** do sdílené složky a klikněte na tlačítko **...**  (případně klikněte pravým tlačítkem na tento řádek) a v místní nabídce vyberte **odstranit**.
   
    ![Odstranit sdílenou složku](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. Zkontrolujte stav, který chcete odstranit sdílenou složku. Pokud není sdílená složka, kterou chcete odstranit offline, odpojit, ji nejprve. Postupujte podle kroků v [sdílenou složku převést do režimu offline](#take-a-share-offline).
4. Po zobrazení výzvy k potvrzení **odstranit** okně přijměte potvrzení a klikněte na tlačítko **odstranit**. Sdílená složka se teď odstraní a **sdílené složky** okno aktualizovaný seznam sdílených složek v rámci virtuální pole.

## <a name="next-steps"></a>Další postup
Zjistěte, jak [klonovat sdílenou složku StorSimple](storsimple-virtual-array-clone.md).

