---
title: Spravovat přihlašovací údaje účtu úložiště virtuálních polí StorSimple | Microsoft Docs
description: Vysvětluje, jak můžete pomocí stránky StorSimple Device Manager nakonfigurovat přidat, upravit, odstranit nebo otočit klíče zabezpečení pro přihlašovací údaje účtu úložiště přidružené k virtuálnímu poli StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 234bf8bb-d5fe-40be-9d25-721d7482bc3b
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: 8267a678f35d01119ae3e7d89a07c789dc969614
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85507496"
---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>Použití Device Manager StorSimple ke správě přihlašovacích údajů účtu úložiště pro virtuální pole StorSimple

## <a name="overview"></a>Přehled
**Konfigurační** oddíl okna StorSimple Device Manager služby ve virtuálním poli StorSimple představuje globální parametry služby, které se dají vytvořit ve službě StorSimple Manager. Tyto parametry se dají použít pro všechna zařízení připojená ke službě a zahrnují:

* Přihlašovací údaje účtu úložiště
* Záznamy řízení přístupu
  
  ![Řídicí panel služby Device Manager](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

V tomto kurzu se dozvíte, jak můžete přidat, upravit nebo odstranit přihlašovací údaje účtu úložiště pro virtuální pole StorSimple. Informace v tomto kurzu se vztahují jenom na virtuální pole StorSimple. Informace o tom, jak spravovat účty úložiště v řadě 8000, najdete v tématu [Správa účtu úložiště pomocí služby StorSimple Manager](storsimple-manage-storage-accounts.md).

Přihlašovací údaje účtu úložiště obsahují přihlašovací údaje, které zařízení používá pro přístup k vašemu účtu úložiště ve vašem poskytovateli cloudové služby. U Microsoft Azure účtů úložiště se jedná o přihlašovací údaje, jako je název účtu a primární přístupový klíč.

V okně **přihlašovací údaje účtu úložiště** se všechny přihlašovací údaje účtu úložiště, které jsou vytvořené pro fakturační předplatné, zobrazují v tabulkovém formátu, který obsahuje následující informace:

* **Název** – jedinečný název přiřazený k účtu při jeho vytvoření.
* **SSL povoleno** – zda je povolen protokol TLS a komunikace mezi zařízeními a cloudem je přes zabezpečený kanál.
  
  ![Konfigurační oddíl](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

Nejběžnější úlohy související s přihlašovacími údaji účtu úložiště, které je možné provádět v okně **přihlašovací údaje účtu úložiště** , jsou:

* Přidání přihlašovacích údajů účtu úložiště
* Úprava přihlašovacích údajů účtu úložiště
* Odstraní přihlašovací údaje účtu úložiště.

## <a name="types-of-storage-account-credentials"></a>Typy přihlašovacích údajů účtu úložiště
Existují tři typy přihlašovacích údajů účtu úložiště, které se dají používat se zařízením StorSimple.

* **Automaticky vygenerované přihlašovací údaje účtu úložiště** – když název navrhuje, tento typ přihlašovacích údajů účtu úložiště se automaticky vygeneruje při prvním vytvoření služby. Další informace o tom, jak se tyto přihlašovací údaje k účtu úložiště vytvářejí, najdete v tématu [Vytvoření nové služby](storsimple-virtual-array-manage-service.md#create-a-service).
* **přihlašovací údaje účtu úložiště v předplatném služby** – jedná se o přihlašovací údaje účtu úložiště Azure, které jsou přidružené ke stejnému předplatnému jako služba. Další informace o tom, jak se tyto přihlašovací údaje k účtu úložiště vytvářejí, najdete v tématu [informace o Azure Storagech účtech](../storage/common/storage-create-storage-account.md).
* **přihlašovací údaje účtu úložiště mimo předplatné služby** – jedná se o přihlašovací údaje účtu úložiště Azure, které nejsou přidružené k vaší službě, a nejspíš existovaly před vytvořením služby.

## <a name="add-a-storage-account-credential"></a>Přidání přihlašovacích údajů účtu úložiště
Přihlašovací údaje účtu úložiště můžete přidat do konfigurace služby StorSimple Device Manager zadáním jedinečného popisného názvu a přístupových přihlašovacích údajů, které jsou propojené s účtem úložiště. Máte také možnost povolit režim TLS (Transport Layer Security), dříve označovaný jako SSL (Secure Sockets Layer) (SSL), a vytvořit zabezpečený kanál pro síťovou komunikaci mezi vaším zařízením a cloudem.

Pro daného poskytovatele cloudové služby můžete vytvořit několik účtů. Při ukládání přihlašovacích údajů účtu úložiště se služba pokusí o komunikaci s vaším poskytovatelem cloudových služeb. Přihlašovací údaje a přístupový materiál, které jste zadali, se v tuto chvíli ověřují. Přihlašovací údaje účtu úložiště se vytvoří jenom v případě, že je ověřování úspěšné. Pokud se ověření nepovede, zobrazí se příslušná chybová zpráva.

K přidání přihlašovacích údajů k účtu úložiště Azure použijte následující postupy:

* Přidání přihlašovacích údajů účtu úložiště, které mají stejné předplatné Azure jako služba Device Manager
* Přidání přihlašovacích údajů účtu Azure Storage mimo předplatné služby Device Manager

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Přidání přihlašovacích údajů účtu úložiště, které mají stejné předplatné Azure jako služba Device Manager

1. Přejděte ke službě Device Manager, vyberte ji a dvakrát na ni klikněte. Tím se otevře okno **Přehled** .
2. V části **Konfigurace** vyberte **přihlašovací údaje účtu úložiště** .
3. Klikněte na **Přidat**.
4. V okně **Přidat účet úložiště** udělejte toto:
   
    1. V **předplatném**vyberte **aktuální**.
    2. Zadejte název svého účtu úložiště Azure.
    3. Pokud chcete vytvořit zabezpečený kanál pro síťovou komunikaci mezi zařízením StorSimple a cloudem, vyberte **Povolit** . Vyberte možnost **Zakázat** pouze v případě, že pracujete v rámci privátního cloudu.
    4. Klikněte na **Přidat**. Po úspěšném vytvoření účtu úložiště se zobrazí oznámení.<br></br>
   
        ![Přidat existující přihlašovací údaje účtu úložiště](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>Přidání přihlašovacích údajů účtu Azure Storage mimo předplatné služby Device Manager

1. Přejděte ke službě Device Manager, vyberte ji a dvakrát na ni klikněte. Tím se otevře okno **Přehled** .
2. V části **Konfigurace** vyberte **přihlašovací údaje účtu úložiště** . Tato seznam obsahuje všechna existující pověření účtu úložiště přidružená ke službě StorSimple Device Manager.
3. Klikněte na **Přidat**.
4. V okně **Přidat účet úložiště** udělejte toto:
   
    1. U **předplatného**vyberte **jiné**.
   
    2. Zadejte název přihlašovacích údajů k účtu úložiště Azure.
   
    3. Do textového pole **přístupový klíč účtu úložiště** zadejte primární přístupový klíč pro přihlašovací údaje k účtu úložiště Azure. Tento klíč získáte tak, že přejdete do služby Azure Storage, vyberete přihlašovací údaje účtu úložiště a kliknete na **spravovat klíče účtu**. Nyní můžete zkopírovat primární přístupový klíč.
   
    4. Pokud chcete povolit protokol TLS, klikněte na tlačítko **Povolit** a vytvořte zabezpečený kanál pro síťovou komunikaci mezi službou StorSimple Device Manager a cloudem. Klikněte na tlačítko **Zakázat** jenom v případě, že pracujete v privátním cloudu.
   
    5. Klikněte na **Přidat**. Po úspěšném vytvoření přihlašovacích údajů účtu úložiště se zobrazí oznámení.

5. Nově vytvořené přihlašovací údaje účtu úložiště se zobrazí v okně StorSimple konfigurace služby Device Manager v části **přihlašovací údaje účtu úložiště**.
   
    ![Přidání přihlašovacích údajů účtu úložiště mimo předplatné služby Device Manager](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>Úprava přihlašovacích údajů účtu úložiště
Můžete upravit přihlašovací údaje účtu úložiště používané vaším zařízením. Pokud upravíte přihlašovací údaje účtu úložiště, které se právě používají, jsou pole, která je k dispozici k úpravě, přístupová klávesa a režim TLS pro přihlašovací údaje účtu úložiště. Můžete dodat nový přístupový klíč k úložišti nebo upravit výběr **režimu SSL** a uložit aktualizované nastavení.

#### <a name="to-edit-a-storage-account-credential"></a>Úprava přihlašovacích údajů účtu úložiště
1. Přejděte ke službě Device Manager, vyberte ji a dvakrát na ni klikněte. Tím se otevře okno **Přehled** .
2. V části **Konfigurace** vyberte **přihlašovací údaje účtu úložiště** . Tato seznam obsahuje všechna existující pověření účtu úložiště přidružená ke službě StorSimple Device Manager.
3. V tabulkovém seznamu pověření účtu úložiště vyberte a dvakrát klikněte na účet, který chcete upravit.
4. V okně **vlastnosti** přihlašovacích údajů účtu úložiště udělejte toto:
   
   1. V případě potřeby můžete upravit možnost **Povolit režim SSL** .
   2. Můžete zvolit, že se mají znovu vygenerovat přístupové klíče přihlašovacích údajů účtu úložiště. Další informace najdete v tématu [Správa přístupových klíčů účtu úložiště](../storage/common/storage-account-keys-manage.md). Zadejte nový klíč přihlašovacích údajů účtu úložiště. Pro účet služby Azure Storage se jedná o primární přístupový klíč.
   3. Uložte nastavení kliknutím na **Uložit** v horní části okna **vlastnosti** . Nastavení se aktualizují v okně **pověření účtu úložiště** .
      
      ![Úprava přihlašovacích údajů účtu úložiště](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>Odstraní přihlašovací údaje účtu úložiště.
> [!IMPORTANT]
> Přihlašovací údaje účtu úložiště můžete odstranit jenom v případě, že se nepoužívá. Pokud se přihlašovací údaje účtu úložiště používají, dostanete oznámení.
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>Odstranění přihlašovacích údajů účtu úložiště
1. Přejděte ke službě Device Manager, vyberte ji a dvakrát na ni klikněte. Tím se otevře okno **Přehled** .
2. V části **Konfigurace** vyberte **přihlašovací údaje účtu úložiště** . Tato seznam obsahuje všechna existující pověření účtu úložiště přidružená ke službě StorSimple Device Manager.
3. V tabulkovém seznamu pověření účtu úložiště vyberte a dvakrát klikněte na účet, který chcete odstranit.
4. V okně **vlastnosti** přihlašovacích údajů účtu úložiště udělejte toto:
   
   1. Kliknutím na **Odstranit** odstraňte přihlašovací údaje.
   2. Po zobrazení výzvy k potvrzení klikněte na **Ano** , aby se odstraňování pokračovalo. Tabulkový výpis je aktualizován, aby odrážel změny.
      
      ![Odstraní přihlašovací údaje účtu úložiště.](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>Synchronizují se klíče přihlašovacích údajů účtu úložiště.
Z bezpečnostních důvodů je střídání klíčů často v datových centrech potřeba. Správce Microsoft Azure může znovu vygenerovat nebo změnit primární nebo sekundární klíč přímým přístupem k přihlašovacím údajům k účtu úložiště (prostřednictvím služby Microsoft Azure Storage). Služba StorSimple Device Manager nevidí tuto změnu automaticky.

Abyste informovali službu StorSimple Device Manager ke změně, budete potřebovat přístup ke službě StorSimple Device Manager, získat přístup k přihlašovacím údajům k účtu úložiště a pak synchronizovat primární nebo sekundární klíč (v závislosti na tom, který z nich byl změněn). Služba pak získá nejnovější klíč, zašifruje klíče a odešle šifrovaný klíč do zařízení.

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>Synchronizace klíčů pro přihlašovací údaje účtu úložiště ve stejném předplatném jako služba (jenom Azure)
1. V okně cílová služba vyberte svou službu, dvakrát klikněte na název služby a potom v části **Konfigurace** klikněte na **přihlašovací údaje účtu úložiště**.
2. V okně **přihlašovací údaje účtu úložiště** vyberte v seznamu přihlašovací údaje účtu úložiště přihlašovací údaje účtu úložiště, jejichž klíče se mají synchronizovat.
3. V okně **vlastnosti** pro vybrané přihlašovací údaje účtu úložiště udělejte toto:
   
    1. Klikněte na **Další**a pak klikněte na **synchronizovat přístupový klíč**.
   
    2. Po zobrazení výzvy k potvrzení klikněte na **synchronizovat klíč** a dokončete synchronizaci.
    
4. Ve službě StorSimple Device Manager musíte aktualizovat klíč, který se dříve změnil ve službě Microsoft Azure Storage. Pokud se primární přístupový klíč změnil (znovu vygeneroval) v okně **synchronizovat klíč účtu úložiště** , klikněte na primární a pak klikněte na **synchronizovat klíč**. Pokud se sekundární klíč změnil, klikněte na **sekundární**a pak klikněte na **synchronizovat klíč**.
   
    ![Přístupový klíč synchronizace](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-access-key.png)

## <a name="next-steps"></a>Další kroky
* Naučte se [spravovat virtuální pole StorSimple](storsimple-ova-web-ui-admin.md).

