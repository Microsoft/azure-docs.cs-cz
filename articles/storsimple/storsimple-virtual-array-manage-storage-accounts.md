---
title: Správa přihlašovacích údajů k účtu úložiště StorSimple Virtual Array | Dokumenty společnosti Microsoft
description: Vysvětluje, jak můžete pomocí stránky StorSimple Device Manager Configure přidat, upravit, odstranit nebo otočit klíče zabezpečení pro přihlašovací údaje účtu úložiště přidružené k virtuálnímu poli StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 234bf8bb-d5fe-40be-9d25-721d7482bc3b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: 5cedde1e7daa49aaa7a2786c9ad8a65fb8e452f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297579"
---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>Správa přihlašovacích údajů účtu úložiště pro virtuální pole StorSimple pomocí Správce zařízení StorSimple

## <a name="overview"></a>Přehled
Část **Konfigurace** v okně služby StorSimple Device Manager vašeho virtuálního pole StorSimple představuje parametry globální služby, které lze vytvořit ve službě StorSimple Manager. Tyto parametry lze použít na všechna zařízení připojená ke službě a zahrnují:

* Přihlašovací údaje účtu úložiště
* Záznamy řízení přístupu
  
  ![Řídicí panel služby Správce zařízení](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

Tento kurz vysvětluje, jak můžete přidat, upravit nebo odstranit přihlašovací údaje účtu úložiště pro vaše virtuální pole StorSimple. Informace v tomto kurzu platí pouze pro Virtuální pole StorSimple. Informace o tom, jak spravovat účty úložiště v řadách 8000, naleznete [v tématu Správa účtu úložiště pomocí služby StorSimple Manager](storsimple-manage-storage-accounts.md).

Přihlašovací údaje účtu úložiště obsahují přihlašovací údaje, které zařízení používá pro přístup k vašemu účtu úložiště u vašeho poskytovatele cloudových služeb. Pro účty úložiště Microsoft Azure se jedná o přihlašovací údaje, jako je název účtu a primární přístupový klíč.

V okně **přihlašovací údaje účtu úložiště** jsou všechna pověření účtu úložiště, která jsou vytvořena pro fakturační předplatné, zobrazena v tabulkovém formátu obsahujícím následující informace:

* **Název** – jedinečný název přiřazený účtu při jeho vytvoření.
* **SSL povoleno** – Zda je povolena TLS a komunikace mezi zařízeními a cloudem je přes zabezpečený kanál.
  
  ![Oddíl Konfigurace](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

Nejběžnější úlohy související s přihlašovacími údaji účtu úložiště, které lze provést v okně **přihlašovacích údajů účtu úložiště,** jsou:

* Přidání přihlašovacích údajů účtu úložiště
* Úprava přihlašovacích údajů účtu úložiště
* Odstranění přihlašovacích údajů účtu úložiště

## <a name="types-of-storage-account-credentials"></a>Typy přihlašovacích údajů účtu úložiště
Existují tři typy přihlašovacích údajů účtu úložiště, které lze použít s vaším zařízením StorSimple.

* **Pověření automaticky generovaného účtu úložiště** – Jak již název napovídá, tento typ přihlašovacích údajů účtu úložiště se automaticky vygeneruje při prvním vytvoření služby. Další informace o tom, jak se toto přihlašovací údaje účtu úložiště vytvářejí, najdete [v tématu Vytvoření nové služby](storsimple-virtual-array-manage-service.md#create-a-service).
* **přihlašovací údaje účtu úložiště v předplatném služby** – Jedná se o přihlašovací údaje účtu úložiště Azure, které jsou přidruženy ke stejnému předplatnému jako služba. Další informace o tom, jak se tyto přihlašovací údaje účtu úložiště vytvářejí, najdete v [tématu O účtech úložiště Azure](../storage/common/storage-create-storage-account.md).
* **přihlašovací údaje účtu úložiště mimo předplatné služby** – Jedná se o přihlašovací údaje účtu úložiště Azure, které nejsou přidruženy k vaší službě a pravděpodobně existovaly před vytvořením služby.

## <a name="add-a-storage-account-credential"></a>Přidání přihlašovacích údajů účtu úložiště
Do konfigurace služby Správce zařízení StorSimple můžete přidat pověření účtu úložiště tím, že poskytnete jedinečný popisný název a přístupová pověření, která jsou propojena s účtem úložiště. Máte také možnost povolit režim zabezpečení transportní vrstvy (TLS), dříve známý jako režim SSL (Secure Sockets Layer), k vytvoření zabezpečeného kanálu pro síťovou komunikaci mezi zařízením a cloudem.

Pro daného poskytovatele cloudových služeb můžete vytvořit více účtů. Při ukládání přihlašovacích údajů účtu úložiště se služba pokusí komunikovat s vaším poskytovatelem cloudových služeb. Pověření a přístupový materiál, který jste zadali, jsou v tuto chvíli ověřeny. Pověření účtu úložiště je vytvořeno pouze v případě, že ověření proběhne úspěšně. Pokud se ověření nezdaří, zobrazí se příslušná chybová zpráva.

Pomocí následujících postupů přidejte přihlašovací údaje účtu úložiště Azure:

* Přidání přihlašovacích údajů účtu úložiště, které má stejné předplatné Azure jako služba Správce zařízení
* Přidání přihlašovacích údajů účtu úložiště Azure, které je mimo předplatné služby Device Manager

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Přidání přihlašovacích údajů účtu úložiště, které má stejné předplatné Azure jako služba Správce zařízení

1. Přejděte do služby Správce zařízení, vyberte ji a poklikejte na ni. Tím se otevře okno **Přehled.**
2. V části **Konfigurace** vyberte **přihlašovací údaje účtu úložiště.**
3. Klikněte na **Přidat**.
4. V okně **Přidat účet úložiště** postupujte takto:
   
    1. V **případě předplatného**vyberte **aktuální**.
    2. Zadejte název svého účtu úložiště Azure.
    3. Výběrem **možnosti Povolit** vytvoříte zabezpečený kanál pro síťovou komunikaci mezi zařízením StorSimple a cloudem. Vyberte **Zakázat** pouze v případě, že pracujete v privátním cloudu.
    4. Klikněte na **Přidat**. Budete upozorněni po úspěšném vytvoření účtu úložiště.<br></br>
   
        ![Přidání existujícího přihlašovacího údaje účtu úložiště](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>Přidání přihlašovacích údajů účtu úložiště Azure, které je mimo předplatné služby Device Manager

1. Přejděte do služby Správce zařízení, vyberte ji a poklikejte na ni. Tím se otevře okno **Přehled.**
2. V části **Konfigurace** vyberte **přihlašovací údaje účtu úložiště.** V tomto seznamu jsou uvedena všechna existující pověření účtu úložiště přidružená ke službě StorSimple Device Manager.
3. Klikněte na **Přidat**.
4. V okně **Přidat účet úložiště** postupujte takto:
   
    1. V **programu Předplatné**vyberte možnost **Jiné**.
   
    2. Zadejte název přihlašovacích údajů účtu úložiště Azure.
   
    3. V textovém poli klíče **přístupu k účtu úložiště** zadejte primární přístupový klíč pro přihlašovací údaje účtu úložiště Azure. Pokud chcete získat tento klíč, přejděte na službu Azure Storage, vyberte přihlašovací údaje účtu úložiště a klikněte na **Spravovat klíče účtu**. Nyní můžete zkopírovat primární přístupový klíč.
   
    4. Chcete-li povolit tls, klikněte na tlačítko **Povolit** a vytvořte zabezpečený kanál pro síťovou komunikaci mezi službou StorSimple Device Manager a cloudem. Na tlačítko **Zakázat** klepněte pouze v případě, že pracujete v privátním cloudu.
   
    5. Klikněte na **Přidat**. Budete upozorněni po úspěšném vytvoření pověření účtu úložiště.

5. Nově vytvořené přihlašovací údaje účtu úložiště se zobrazí v okně služby StorSimple Configure Device Manager v části **Pověření účtu úložiště**.
   
    ![Přidání přihlašovacích údajů účtu úložiště mimo předplatné služby Správce zařízení](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>Úprava přihlašovacích údajů účtu úložiště
Můžete upravit přihlašovací údaje účtu úložiště používané zařízením. Pokud upravíte pověření účtu úložiště, které se právě používá, jsou pole, která jsou k dispozici k úpravám, přístupový klíč a režim TLS pro pověření účtu úložiště. Můžete zadat nový přístupový klíč úložiště nebo upravit výběr **režimu SSL** a uložit aktualizované nastavení.

#### <a name="to-edit-a-storage-account-credential"></a>Úprava přihlašovacích údajů účtu úložiště
1. Přejděte do služby Správce zařízení, vyberte ji a poklikejte na ni. Tím se otevře okno **Přehled.**
2. V části **Konfigurace** vyberte **přihlašovací údaje účtu úložiště.** V tomto seznamu jsou uvedena všechna existující pověření účtu úložiště přidružená ke službě StorSimple Device Manager.
3. V tabulkovém seznamu přihlašovacích údajů účtu úložiště vyberte a poklikejte na účet, který chcete upravit.
4. V okně **Vlastnosti** pověření účtu úložiště postupujte takto:
   
   1. V případě potřeby můžete upravit volbu režimu **SSL.**
   2. Můžete se rozhodnout znovu vygenerovat přístupové klíče přihlašovacích údajů účtu úložiště. Další informace naleznete v [tématu Správa přístupových klíčů účtu úložiště](../storage/common/storage-account-keys-manage.md). Zadej nový klíč pověření účtu úložiště. Pro účet úložiště Azure se jedná o primární přístupový klíč.
   3. Kliknutím na **Uložit** v horní části okna **Vlastnosti** uložte nastavení. Nastavení jsou aktualizovány v okně **pověření účtu úložiště.**
      
      ![Úprava přihlašovacích údajů účtu úložiště](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>Odstranění přihlašovacích údajů účtu úložiště
> [!IMPORTANT]
> Pověření účtu úložiště můžete odstranit pouze v případě, že se nepoužívá. Pokud se používá přihlašovací údaje účtu úložiště, budete upozorněni.
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>Odstranění přihlašovacích údajů účtu úložiště
1. Přejděte do služby Správce zařízení, vyberte ji a poklikejte na ni. Tím se otevře okno **Přehled.**
2. V části **Konfigurace** vyberte **přihlašovací údaje účtu úložiště.** V tomto seznamu jsou uvedena všechna existující pověření účtu úložiště přidružená ke službě StorSimple Device Manager.
3. V tabulkovém seznamu přihlašovacích údajů účtu úložiště vyberte a poklikejte na účet, který chcete odstranit.
4. V okně **Vlastnosti** pověření účtu úložiště postupujte takto:
   
   1. Kliknutím na **Odstranit** pověření odstraňte.
   2. Po zobrazení výzvy k potvrzení pokračujte klepnutím na tlačítko **Ano** v odstraňování. Tabulkový výpis je aktualizován tak, aby odrážel změny.
      
      ![Odstranění přihlašovacích údajů účtu úložiště](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>Synchronizace klíčů pověření účtu úložiště
Z bezpečnostních důvodů je střídání klíčů často požadavkem v datových centrech. Správce Microsoft Azure může obnovit nebo změnit primární nebo sekundární klíč přímým přístupem k přihlašovacím údajům účtu úložiště (prostřednictvím služby Microsoft Azure Storage). Služba Správce zařízení StorSimple tuto změnu automaticky neuvidí.

Chcete-li informovat službu StorSimple Device Manager o změně, musíte získat přístup ke službě StorSimple Device Manager, získat přístup k pověření účtu úložiště a poté synchronizovat primární nebo sekundární klíč (v závislosti na tom, který z nich byl změněn). Služba pak získá nejnovější klíč, zašifruje klíče a odešle šifrovaný klíč do zařízení.

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>Synchronizace klíčů pro přihlašovací údaje účtu úložiště ve stejném předplatném jako služba (jenom Azure)
1. Na přistávacím okně služby vyberte službu, poklepejte na název služby a potom v části **Konfigurace** klikněte na **přihlašovací údaje účtu úložiště**.
2. V okně **přihlašovací údaje účtu úložiště** vyberte v seznamu přihlašovacích údajů účtu úložiště přihlašovací údaje účtu úložiště, jehož klíče chcete synchronizovat.
3. V okně **Vlastnosti** pro vybrané přihlašovací údaje účtu úložiště postupujte takto:
   
    1. Klepněte na tlačítko **Další**a potom klepněte na tlačítko **Synchronizovat přístupový klíč**.
   
    2. Po zobrazení výzvy k potvrzení dokončete synchronizaci kliknutím na **tlačítko Synchronizovat.**
    
4. Ve službě StorSimple Device Manager je třeba aktualizovat klíč, který byl dříve změněn ve službě Microsoft Azure Storage. Pokud byl v okně **Synchronizovat klíč účtu úložiště** změněn (znovu vygenerován), klikněte na primární a potom klikněte na **tlačítko Synchronizovat klíč**. Pokud byl sekundární klíč změněn, klepněte na **položku Sekundární**a potom klepněte na tlačítko **Synchronizovat klíč**.
   
    ![Klíč pro přístup k synchronizaci](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-access-key.png)

## <a name="next-steps"></a>Další kroky
* Přečtěte si, jak [spravovat virtuální pole StorSimple](storsimple-ova-web-ui-admin.md).

