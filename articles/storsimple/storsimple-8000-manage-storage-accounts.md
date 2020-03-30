---
title: Správa přihlašovacích údajů účtu úložiště, zařízení řady StorSimple 8000
description: Vysvětluje, jak můžete pomocí stránky StorSimple Device Manager Configure přidávat, upravovat, odstraňovat nebo otáčet klíče zabezpečení pro účet úložiště.
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
ms.openlocfilehash: 65aa83731be97b59a36a5baf60ea308690a0dcf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297747"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>Správa přihlašovacích údajů účtu úložiště pomocí služby StorSimple Device Manager

## <a name="overview"></a>Přehled

Část **Konfigurace** v okně služby Správce zařízení StorSimple obsahuje všechny globální parametry služby, které lze vytvořit ve službě StorSimple Device Manager. Tyto parametry lze použít na všechna zařízení připojená ke službě a zahrnují:

* Přihlašovací údaje účtu úložiště
* Šablony šířky pásma 
* Záznamy řízení přístupu 

Tento kurz vysvětluje, jak přidat, upravit nebo odstranit přihlašovací údaje účtu úložiště nebo otočit klíče zabezpečení pro účet úložiště.

 ![Seznam přihlašovacích údajů účtu úložiště](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

Účty úložiště obsahují přihlašovací údaje, které zařízení StorSimple používá pro přístup k účtu úložiště u vašeho poskytovatele cloudových služeb. Pro účty úložiště Microsoft Azure se jedná o přihlašovací údaje, jako je název účtu a primární přístupový klíč. 

V okně **přihlašovací údaje účtu úložiště** jsou všechny účty úložiště, které jsou vytvořeny pro fakturační předplatné, zobrazeny v tabulkovém formátu obsahujícím následující informace:

* **Název** – jedinečný název přiřazený účtu při jeho vytvoření.
* **SSL povoleno** – Zda je povolena TLS a komunikace mezi zařízeními a cloudem je přes zabezpečený kanál.
* **Používá** – počet svazků pomocí účtu úložiště.

Nejběžnější úkoly související s účty úložiště, které lze provádět, jsou:

* Přidání účtu úložiště 
* Úprava účtu úložiště 
* Odstranění účtu úložiště 
* Střídání klíčů účtů úložiště 

## <a name="types-of-storage-accounts"></a>Typy účtů úložiště

Existují tři typy účtů úložiště, které lze použít s vaším zařízením StorSimple.

* **Účty automaticky generovaného úložiště** – Jak již název napovídá, tento typ účtu úložiště se automaticky vygeneruje při prvním vytvoření služby. Další informace o tom, jak se tento účet úložiště vytváří, najdete [v tématu Krok 1: Vytvoření nové služby](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service) v [tématu Nasazení místního zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md). 
* **Účty úložiště v předplatném služby** – Jedná se o účty úložiště Azure, které jsou přidružené ke stejnému předplatnému jako služba. Další informace o tom, jak se tyto účty úložiště vytvářejí, najdete v [tématu O účtech úložiště Azure](../storage/common/storage-create-storage-account.md). 
* **Účty úložiště mimo předplatné služby** – Jedná se o účty úložiště Azure, které nejsou přidruženy k vaší službě a pravděpodobně existovaly před vytvořením služby.

## <a name="add-a-storage-account"></a>Přidání účtu úložiště

Účet úložiště můžete přidat poskytnutím jedinečného popisného názvu a přístupových přihlašovacích údajů, které jsou propojeny s účtem úložiště (se zadaným poskytovatelem cloudových služeb). Máte také možnost povolit režim zabezpečení transportní vrstvy (TLS), dříve známý jako režim SSL (Secure Sockets Layer), k vytvoření zabezpečeného kanálu pro síťovou komunikaci mezi zařízením a cloudem.

Pro daného poskytovatele cloudových služeb můžete vytvořit více účtů. Uvědomte si však, že po vytvoření účtu úložiště nelze změnit poskytovatele cloudových služeb.

Při ukládání účtu úložiště se služba pokusí komunikovat s vaším poskytovatelem cloudových služeb. Pověření a přístupový materiál, který jste zadali, budou v tuto chvíli ověřeny. Účet úložiště se vytvoří pouze v případě, že ověření proběhne úspěšně. Pokud se ověření nezdaří, zobrazí se příslušná chybová zpráva.

Pomocí následujících postupů přidejte přihlašovací údaje účtu úložiště Azure:

* Přidání přihlašovacích údajů účtu úložiště, které má stejné předplatné Azure jako služba Správce zařízení
* Přidání přihlašovacích údajů účtu úložiště Azure, které je mimo předplatné služby Device Manager

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>Přidání přihlašovacích údajů účtu úložiště Azure mimo předplatné služby StorSimple Device Manager

1. Přejděte do služby Správce zařízení StorSimple, vyberte ji a poklikejte na ni. Tím se otevře okno **Přehled.**
2. V části **Konfigurace** vyberte **přihlašovací údaje účtu úložiště.** V tomto seznamu jsou uvedena všechna existující pověření účtu úložiště přidružená ke službě StorSimple Device Manager.
3. Klikněte na **Přidat**.
4. V okně **Přidat přihlašovací údaje účtu úložiště** postupujte takto:
   
    1. V **programu Předplatné**vyberte možnost **Jiné**.
   
    2. Zadejte název přihlašovacích údajů účtu úložiště Azure.
   
    3. V textovém poli klíče **přístupu k účtu úložiště** zadejte primární přístupový klíč pro přihlašovací údaje účtu úložiště Azure. Pokud chcete získat tento klíč, přejděte na službu Azure Storage, vyberte přihlašovací údaje účtu úložiště a klikněte na **Spravovat klíče účtu**. Nyní můžete zkopírovat primární přístupový klíč.
   
    4. Chcete-li povolit tls, klikněte na tlačítko **Povolit** a vytvořte zabezpečený kanál pro síťovou komunikaci mezi službou StorSimple Device Manager a cloudem. Na tlačítko **Zakázat** klepněte pouze v případě, že pracujete v privátním cloudu.
   
    5. Klikněte na **Přidat**. Budete upozorněni po úspěšném vytvoření pověření účtu úložiště.

5. Nově vytvořené přihlašovací údaje účtu úložiště se zobrazí v okně služby StorSimple Configure Device Manager v části **Pověření účtu úložiště**.
   


## <a name="edit-a-storage-account"></a>Úprava účtu úložiště

Můžete upravit účet úložiště, který používá kontejner svazku. Pokud upravíte účet úložiště, který je aktuálně používán, jediné pole, které je k dispozici pro úpravu, je přístupový klíč pro účet úložiště. Můžete zadat nový přístupový klíč úložiště a uložit aktualizované nastavení.

#### <a name="to-edit-a-storage-account"></a>Úprava účtu úložiště

1. Přejděte do služby Správce zařízení StorSimple. V části **Konfigurace** klikněte na **Přihlašovací údaje účtu úložiště**.

    ![Přihlašovací údaje účtu úložiště](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. V okně **přihlašovací údaje účtu úložiště** vyberte ze seznamu přihlašovacích údajů účtu úložiště a klikněte na něj, který chcete upravit. 

3. Můžete upravit výběr **Povolit ssl.** Můžete taky kliknout na **Další...** a pak vybrat **Možnost Synchronizovat přístupový klíč a otočit** přístupové klíče k účtu úložiště. Další informace o tom, jak provádět střídání klíčů, najdete v [tématu Střídání klíčů účtů úložiště.](#key-rotation-of-storage-accounts) Po úpravě nastavení klepněte na tlačítko **Uložit**. 

    ![Uložení upravených přihlašovacích údajů účtu úložiště](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. Po zobrazení výzvy k potvrzení klikněte na **Ano**. 

    ![Potvrdit změny](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

Nastavení se aktualizuje a uloží pro váš účet úložiště. 

## <a name="delete-a-storage-account"></a>Odstranění účtu úložiště

> [!IMPORTANT]
> Účet úložiště můžete odstranit pouze v případě, že jej kontejner svazku nepoužívá. Pokud účet úložiště používá kontejner svazku, nejprve odstraňte kontejner svazku a potom odstraňte přidružený účet úložiště.

#### <a name="to-delete-a-storage-account"></a>Odstranění účtu úložiště

1. Přejděte do služby Správce zařízení StorSimple. V části **Konfigurace** klikněte na **Přihlašovací údaje účtu úložiště**.

2. V tabulkovém seznamu účtů úložiště najeďte na účet, který chcete odstranit. Klepnutím pravým tlačítkem myši vyvoláte místní nabídku a klepněte na příkaz **Odstranit**.

    ![Odstranění přihlašovacích údajů účtu úložiště](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. Po zobrazení výzvy k potvrzení pokračujte klepnutím na tlačítko **Ano** v odstraňování. Tabulkový výpis bude aktualizován tak, aby odrážel změny.

    ![Potvrzení odstranění](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>Střídání klíčů účtů úložiště

Z bezpečnostních důvodů je střídání klíčů často požadavkem v datových centrech. Každé předplatné Microsoft Azure může mít jeden nebo více přidružených účtů úložiště. Přístup k těmto účtům je řízen předplatným a přístupovými klíči pro každý účet úložiště. 

Když vytvoříte účet úložiště, Microsoft Azure vygeneruje dva 512bitové přístupové klíče úložiště, které se používají k ověřování při přístupu k účtu úložiště. Díky dvěma přístupovým klíčům úložiště můžete klíče obnovit bez přerušení služby úložiště nebo přístupu k této službě. Klíč, který je aktuálně používán, je *primární* klíč a záložní klíč se označuje jako *sekundární* klíč. Jeden z těchto dvou klíčů musí být zadán, když vaše zařízení Microsoft Azure StorSimple přistupuje k poskytovateli služeb cloudového úložiště.

## <a name="what-is-key-rotation"></a>Co je střídání klíčů?

Aplikace obvykle používají pouze jeden z klíčů pro přístup k datům. Po určité době můžete nechat aplikace přepnout na druhý klíč. Po přepnutí aplikací na sekundární klíč můžete vyřadit první klíč a pak vygenerovat nový klíč. Pomocí dvou kláves tímto způsobem umožňuje aplikacím přístup k datům bez vzniku prostojů.

Klíče účtu úložiště jsou vždy uloženy ve službě v zašifrované podobě. Tyto však lze obnovit prostřednictvím služby StorSimple Device Manager. Služba může získat primární a sekundární klíč pro všechny účty úložiště ve stejném předplatném, včetně účtů vytvořených ve službě Storage a také výchozích účtů úložiště generovaných při prvním vytvoření služby Správce zařízení StorSimple Vytvořen. Služba StorSimple Device Manager tyto klíče vždy získá z klasického portálu Azure a pak je uloží šifrovaným způsobem.

## <a name="rotation-workflow"></a>Pracovní postup otočení

Správce Microsoft Azure může obnovit nebo změnit primární nebo sekundární klíč přímým přístupem k účtu úložiště (prostřednictvím služby Microsoft Azure Storage). Služba Správce zařízení StorSimple tuto změnu automaticky neuvidí.

Chcete-li informovat službu StorSimple Device Manager o změně, budete muset získat přístup ke službě StorSimple Device Manager, přistupovat k účtu úložiště a poté synchronizovat primární nebo sekundární klíč (v závislosti na tom, který z nich byl změněn). Služba pak získá nejnovější klíč, zašifruje klíče a odešle šifrovaný klíč do zařízení.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>Synchronizace klíčů pro účty úložiště ve stejném předplatném jako služba 
1. Přejděte do služby Správce zařízení StorSimple. V části **Konfigurace** klikněte na **Přihlašovací údaje účtu úložiště**.
2. V tabulkovém výpisu účtů úložiště klikněte na ten, který chcete upravit. 

    ![synchronizace klíčů](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. Klikněte na **... Další** a pak vyberte **Synchronizovat přístupový klíč k otočení**.   

    ![synchronizace klíčů](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. Ve službě StorSimple Device Manager je třeba aktualizovat klíč, který byl dříve změněn ve službě Microsoft Azure Storage. Pokud byl primární přístupový klíč změněn (obnoven), vyberte **primární** klíč. Pokud byl sekundární klíč změněn, vyberte **sekundární** klíč. Klepněte na **tlačítko Synchronizovat .**
      
      ![synchronizace klíčů](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

Po úspěšné synchronizaci klíče budete upozorněni.

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Synchronizace klíčů pro účty úložiště mimo předplatné služby
1. Na stránce **Služby** klikněte na kartu **Konfigurovat.**
2. Klikněte na **Přidat nebo upravit účty úložiště**.
3. V dialogovém okně postupujte takto:
   
   1. Vyberte účet úložiště s přístupovým klíčem, který chcete aktualizovat.
   2. Budete muset aktualizovat přístupový klíč úložiště ve službě StorSimple Device Manager. V takovém případě se zobrazí přístupový klíč úložiště. Zadejte nový klíč do pole **Přístupový klíč účtu úložiště.** 
   3. Uložte provedené změny. Přístupový klíč účtu úložiště by teď měl být aktualizován.

## <a name="next-steps"></a>Další kroky
* Další informace o [zabezpečení StorSimple](storsimple-8000-security.md).
* Další informace o [správě zařízení StorSimple Device Manager pomocí služby StorSimple](storsimple-8000-manager-service-administration.md).

