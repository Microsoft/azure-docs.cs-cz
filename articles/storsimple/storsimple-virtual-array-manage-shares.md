---
title: Spravovat sdílené složky pole virtuální zařízení StorSimple | Microsoft Docs
description: Popisuje Správce zařízení StorSimple a vysvětluje, jak použít ho ke správě sdílených složek na pole virtuální zařízení StorSimple.
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
ms.openlocfilehash: e5c62689de36baa175001f5f4f70d87568876ef0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876268"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>Použít službu StorSimple Manager zařízení ke správě sdílených složek na poli virtuální zařízení StorSimple

## <a name="overview"></a>Přehled

Tento kurz vysvětluje, jak používat službu StorSimple Manager zařízení k vytváření a správě sdílených složek na pole virtuální zařízení StorSimple.

Služba Správce zařízení StorSimple je rozšíření na portálu Azure, která umožňuje spravovat z jedné webové rozhraní vašeho řešení StorSimple. Kromě správy sdílené složky a svazky, můžete použít službu StorSimple Manager zařízení zobrazit a spravovat zařízení, zobrazení výstrah, spravovat zásady zálohování a Správa katalogu zálohování.

## <a name="share-types"></a>Typy sdílené složky

Sdílené složky StorSimple může být:

* **Místně vázaný**: Data v těchto sdílených složek zůstává v poli za všech okolností a nebudou distribuována do cloudu.
* **Víceúrovňová**: Data v těchto sdílených složek můžete distribuována do cloudu. Když vytvoříte sdílenou složku vrstvené, přibližně 10 % prostoru je zřízený na místní úrovni a 90 % prostoru se zřizuje v cloudu. Například pokud jste zřídili sdílenou složku 1 TB, 100 GB by nacházet v místním prostoru a 900 GB se použije v cloudu při datové vrstvy. Pak z toho vyplývá, že pokud spustíte mimo veškeré volné místo na zařízení, nejde zřídit vrstvené sdílené složky (protože 10 % požadované na místní úrovni nebudete mít k dispozici).

### <a name="provisioned-capacity"></a>Zřízená kapacita

Naleznete v následující tabulce maximální zřízená kapacita pro každý typ sdílené složky.

| **Identifikátor limit** | **Limit** |
| --- | --- |
| Minimální velikost vrstvené sdílené složky |500 GB |
| Maximální velikost vrstvené sdílené složky |20 TB |
| Minimální velikost místně vázaný sdílené složky |50 GB |
| Maximální velikost místně vázaný sdílené složky |2 TB |

## <a name="the-shares-blade"></a>V okně sdílené složky

**Sdílené složky** nabídky na souhrnné okně vaší StorSimple služby zobrazí seznamu sdílených složek úložiště v daném poli StorSimple a umožňuje je spravovat.

![Okno sdílené složky](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

Sdílenou složku se skládá z řady atributy:

* **Název sdílené složky** – popisný název, který musí být jedinečný a pomáhá identifikovat sdílenou složku.
* **Stav** – může být online nebo offline. Pokud sdílenou složku offline, uživatelé sdílené složky nebudou moci k němu přístup.
* **Typ** – Určuje, zda sdílená složka je **nastavování** (výchozí) nebo **místně vázaný**.
* **Kapacita** – Určuje velikost dat používaných ve srovnání se celkové množství dat, které můžou být uložené ve sdílené složce.
* **Popis** – volitelné nastavení, která pomáhá popisují sdílenou složku.
* **Oprávnění** – systému souborů NTFS oprávnění ke sdílené složce, kterou je možné spravovat pomocí Průzkumníka Windows.
* **Zálohování** – v případě, že pole virtuální zařízení StorSimple se automaticky povolí všechny sdílené složky pro zálohování.

![Podrobnosti o sdílených složek](./media/storsimple-virtual-array-manage-shares/share-details.png)

Postupujte podle pokynů v tomto kurzu k provádění následujících úloh:

* Přidání sdílené složky
* Upravit sdílené složky
* Sdílenou složku do offline
* Odstranění sdílené složky

## <a name="add-a-share"></a>Přidání sdílené složky

1. Ze služby StorSimple souhrnu okna, klikněte na tlačítko **+ přidat sdílenou složku** z řádku nabídek. Otevře **přidat sdílenou složku** okno.

    ![Přidejte sdílenou složku](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. V **přidat sdílenou složku** okno, postupujte takto:
   
    1. V **název sdílené složky** pole, zadejte jedinečný název pro vaši sdílenou složku. Název musí být řetězec, který obsahuje 3 až 127 znaků.

    2. Volitelný **popis** pro sdílenou složku. Popis pomůže identifikovat vlastníkům sdílené složky.

    3. V **typ** rozevíracího seznamu, určete, zda chcete vytvořit **nastavování** nebo **místně vázaný** sdílet. Pro úlohy, které vyžadují místní záruky, nízkou latenci a vyšší výkon, vyberte **místně vázaný sdílené složky**. Všechna ostatní data, vyberte **nastavování** sdílet.

    4. V **kapacity** pole, zadejte velikost sdílené složky. Vrstvený sdílené složky musí být mezi 500 GB a 20 TB a místně vázaný sdílené složky musí být mezi 50 GB a 2 TB.

    5. V **nastavit výchozí úplná oprávnění** pole, přiřadit oprávnění uživatele nebo skupiny, který přistupuje k této sdílené složce. Zadejte název tohoto uživatele nebo skupiny uživatelů v  _john@contoso.com_  formátu. Doporučujeme vám, že používáte skupinu uživatelů (ne jenom jednoho konkrétního uživatele) umožňuje správu oprávnění k přístupu k tyto sdílené složky. Po přiřazení oprávnění tady, můžete pomocí Průzkumníka souborů upravit tato oprávnění.
3. Po dokončení konfigurace vaší sdílené složky, klikněte na tlačítko **vytvořit**. Vytvoří sdílené složky se zadaným nastavením a zobrazí se oznámení. Ve výchozím nastavení zálohování budou povolené pro sdílenou složku.
4. Chcete-li potvrdit, že byl úspěšně vytvořen sdílenou složku, přejděte na **sdílené složky** okno. Měli byste vidět sdílené složky uvedené.
   
    ![Úspěšné vytvoření sdílené složky](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>Upravit sdílené složky

Upravte sdílenou složku, pokud potřebujete změnit popis sdílené složky. Žádné jiné vlastnosti sdílené složky můžete změnit po vytvoření sdílené složky.

#### <a name="to-modify-a-share"></a>Chcete-li upravit sdílené složky

1. Z **sdílené složky** nastavení v okně Souhrn služby StorSimple, vyberte virtuální pole, v němž jsou uložena vám upravit chcete sdílenou složku.
2. **Vyberte** sdílenou složku na aktuální popis zobrazit a upravit ho.
3. Uložte změny kliknutím **Uložit** panelu příkazů. Zadané nastavení uplatní a zobrazí se oznámení.
   
    ![ Upravit sdílené složky](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>Sdílenou složku do offline

Musíte uvedení sdílenou složku do režimu offline, když máte v úmyslu upravit nebo odstranit. Když sdílenou složku do režimu offline, není k dispozici pro přístup pro čtení a zápis. Musíte provést offline sdílenou složku na hostiteli, a také na zařízení.

#### <a name="to-take-a-share-offline"></a>Uvedení sdílenou složku do režimu offline

1. Ujistěte se, že není sdílená složka dotyčném používá před přepnutím do režimu offline.
2. Využít sdílené složky v poli do offline režimu provedením následujících kroků:
   
    1. Z **sdílené složky** nastavení v okně Souhrn služby StorSimple, vyberte pole virtuální, na kterém se nachází sdílená složka chcete přepnout do režimu offline.

    2. **Vyberte** do sdílené složky a klikněte na tlačítko **...**  (případně klikněte pravým tlačítkem na tento řádek) a v místní nabídce vyberte **převést do režimu offline**.
     
        ![Offline sdílené složky](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. Informace v **převést do režimu offline** okno a potvrďte, že přijímáte operace. Klikněte na tlačítko **převést do režimu offline** uvedení sdílenou složku do režimu offline. Zobrazí se oznámení o probíhající operaci.

    4. Chcete-li potvrdit, že sdílené složky byla úspěšně převedeno do režimu offline, přejděte na **sdílené složky** okno. Měli byste vidět stav sdílené složky jako offline.

## <a name="delete-a-share"></a>Odstranění sdílené složky

> [!IMPORTANT]
> Sdílené složky lze odstranit pouze v případě, že je offline.


Dokončete následující postup odstranění sdílené složky.

#### <a name="to-delete-a-share"></a>Chcete-li odstranit sdílenou složku

1. Z **sdílené složky** nastavení v okně Souhrn služby StorSimple, vyberte virtuální pole, v němž jsou uložena chcete odstranit sdílenou složku.
2. **Vyberte** do sdílené složky a klikněte na tlačítko **...**  (případně klikněte pravým tlačítkem na tento řádek) a v místní nabídce vyberte **odstranit**.
   
    ![Odstranit sdílenou složku](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. Zkontrolujte stav sdílené složky, které chcete odstranit. Pokud není sdílená složka, kterou chcete odstranit offline, odpojte jej nejprve. Postupujte podle kroků v [do offline režimu sdílenou složku](#take-a-share-offline).
4. Po zobrazení výzvy k potvrzení v **odstranit** okně přijměte potvrzení a klikněte na tlačítko **odstranit**. Sdílenou složku bude odstraněno a **sdílené složky** aktualizovaný seznam sdílených složek v rámci virtuální pole se zobrazí okno.

## <a name="next-steps"></a>Další kroky
Zjistěte, jak [klonovat sdílenou složku StorSimple](storsimple-virtual-array-clone.md).

