---
title: Správa přihlašovacích údajů účtu úložiště StorSimple Virtual Array | Dokumentace Microsoftu
description: Vysvětluje, jak můžete na stránce konfigurace Správce zařízení StorSimple můžete přidat, upravit, odstranit nebo obměna klíčů zabezpečení pro přihlašovací údaje účtu úložiště přidruženého StorSimple Virtual Array.
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
ms.openlocfilehash: d2b6b7a5d2baf9e46cca18aec97f31beda1e2a38
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45736729"
---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>Pomocí Správce zařízení StorSimple ke správě přihlašovacích údajů účtu úložiště pro StorSimple Virtual Array

## <a name="overview"></a>Přehled
**Konfigurace** okně služby Správce zařízení StorSimple StorSimple Virtual Array nabízí globální službu parametry, které lze vytvořit ve službě StorSimple Manager. Tyto parametry můžete použít pro všechna zařízení připojená ke službě a zahrnují:

* Přihlašovací údaje účtu úložiště
* Záznamy řízení přístupu
  
  ![Řídicí panel služby Správce zařízení](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

Tento kurz vysvětluje, jak můžete přidat, upravit nebo odstranit přihlašovací údaje účtu úložiště pro StorSimple Virtual Array. Informace v tomto kurzu platí pouze pro StorSimple Virtual Array. Informace o tom, jak spravovat účty úložiště v 8000 series najdete v tématu [použít službu StorSimple Manager ke správě vašeho účtu úložiště](storsimple-manage-storage-accounts.md).

Přihlašovací údaje účtu úložiště obsahovat přihlašovací údaje, které zařízení použije pro přístup k vašemu účtu úložiště pomocí poskytovatele cloudových služeb. Pro účty úložiště Microsoft Azure jsou tyto přihlašovací údaje, jako je například název účtu a primární přístupový klíč.

Na **přihlašovací údaje účtu úložiště** okno, všechny přihlašovací údaje účtu úložiště, které jsou vytvořené pro předplatné pro fakturaci se zobrazí v tabulkovém formátu, který obsahuje následující informace:

* **Název** – jedinečný název přiřazený k účtu při vytvoření rovnou uložil.
* **Protokol SSL povolený** – SSL, zda je povolen a je komunikace typu zařízení cloud přes zabezpečený kanál.
  
  ![Oddíl konfigurace](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

Zvládnout běžné úkoly související se přihlašovací údaje účtu úložiště, které lze provést u **přihlašovací údaje účtu úložiště** okna jsou:

* Přidání přihlašovacích údajů účtu úložiště
* Upravit přihlašovací údaje účtu úložiště
* Odstraňování přihlašovacích údajů účtu úložiště

## <a name="types-of-storage-account-credentials"></a>Typy přihlašovacích údajů účtu úložiště
Existují tři typy přihlašovacích údajů účtu úložiště, které je možné se zařízením StorSimple.

* **Přihlašovací údaje účtu úložiště automaticky generované** – jak název napovídá, tento typ přihlašovacích údajů účtu úložiště se automaticky generuje při prvním vytvoření služby. Další informace o vytvoření tento přihlašovací údaj účtu úložiště najdete v tématu [vytvořit novou službu](storsimple-virtual-array-manage-service.md#create-a-service).
* **přihlašovací údaje účtu úložiště v předplatném služby** – jde o přihlašovací údaje účtu úložiště Azure, které jsou přidruženy stejném předplatném jako služby. Další informace o tom, jak tyto úložiště jsou vytvořené přihlašovací údaje k účtu, najdete v článku [o účtech Azure Storage](../storage/common/storage-create-storage-account.md).
* **přihlašovací údaje účtu úložiště mimo předplatné služby** – jde o přihlašovací údaje účtu úložiště Azure, které nejsou přidružené k vaší službě a pravděpodobně existovala předtím, než služba byla vytvořena.

## <a name="add-a-storage-account-credential"></a>Přidání přihlašovacích údajů účtu úložiště
Konfigurace služby Správce zařízení StorSimple můžete přidat přihlašovací údaje účtu úložiště tím, že poskytuje jedinečný popisný název a přihlašovacích údajů pro přístup, které jsou propojeny do účtu úložiště. Máte také možnost zapnutí režimu secure Sockets Layer (SSL) layer vytvořit zabezpečený kanál pro síťovou komunikaci mezi zařízením a cloudem.

Můžete vytvořit více účtů pro poskytovatele služeb daného cloudu. Při uložení přihlašovacích údajů účtu úložiště se služba pokusí komunikovat se svým poskytovatelem cloudových služeb. V tuto chvíli se ověřit přihlašovací údaje a přístup materiál, který jste zadali. Přihlašovací údaje účtu úložiště se vytvoří pouze v případě, že bude ověřování úspěšné. Pokud se ověření nezdaří, se zobrazí příslušná chybová zpráva.

Pomocí následujících postupů můžete přidat přihlašovací údaje účtu úložiště Azure:

* Chcete-li přidat přihlašovací údaje účtu úložiště, který má stejné předplatné Azure jako službu Správce zařízení
* Přidání Azure přihlašovací údaje účtu úložiště, které je mimo předplatné služby Správce zařízení

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Chcete-li přidat přihlašovací údaje účtu úložiště, který má stejné předplatné Azure jako službu Správce zařízení

1. Přejděte do služby Správce zařízení, vyberte a dvojím kliknutím. Tím se otevře **přehled** okno.
2. Vyberte **přihlašovací údaje účtu úložiště** v rámci **konfigurace** oddílu.
3. Klikněte na tlačítko **Add** (Přidat).
4. V **přidání účtu úložiště** okno, postupujte takto:
   
    1. Pro **předplatné**vyberte **aktuální**.
    2. Zadejte název účtu služby Azure storage.
    3. Vyberte **povolit** vytvořit zabezpečený kanál pro síťovou komunikaci mezi zařízením StorSimple a cloudu. Vyberte **zakázat** pouze v případě, že pracujete v privátním cloudu.
    4. Klikněte na tlačítko **Add** (Přidat). Po úspěšném vytvoření účtu úložiště, se zobrazí oznámení.<br></br>
   
        ![Přidat existující přihlašovací údaje účtu úložiště](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>Přidání Azure přihlašovací údaje účtu úložiště, které je mimo předplatné služby Správce zařízení

1. Přejděte do služby Správce zařízení, vyberte a dvojím kliknutím. Tím se otevře **přehled** okno.
2. Vyberte **přihlašovací údaje účtu úložiště** v rámci **konfigurace** oddílu. Vypíšou se všechny existující přihlašovací údaje účtu úložiště přidruženého ke službě Správce zařízení StorSimple.
3. Klikněte na tlačítko **Add** (Přidat).
4. V **přidání účtu úložiště** okno, postupujte takto:
   
    1. Pro **předplatné**vyberte **jiných**.
   
    2. Zadejte název své přihlašovací údaje účtu úložiště Azure.
   
    3. V **přístupový klíč účtu úložiště** textové pole, zadat primární přístupový klíč pro vaše přihlašovací údaje účtu úložiště Azure. Chcete-li tento klíč získat, přejděte do služby Azure Storage, vyberte vaše přihlašovací údaje účtu úložiště a klikněte na **spravovat klíče účtu**. Nyní můžete zkopírovat primární přístupový klíč.
   
    4. Chcete-li povolit protokol SSL, klikněte na tlačítko **povolit** tlačítko vytvořit zabezpečený kanál pro síťovou komunikaci mezi službou Správce zařízení StorSimple a cloudu. Klikněte na tlačítko **zakázat** tlačítko pouze v případě, že pracujete v privátním cloudu.
   
    5. Klikněte na tlačítko **Add** (Přidat). Po úspěšném vytvoření přihlašovacích údajů účtu úložiště, se zobrazí oznámení.

5. Zobrazí se přihlašovací údaje účtu úložiště vytvořeného v okně služby Správce zařízení StorSimple nakonfigurujte podle **přihlašovací údaje účtu úložiště**.
   
    ![Přidat přihlašovací údaj účtu úložiště mimo předplatné služby Správce zařízení](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>Upravit přihlašovací údaje účtu úložiště
Můžete upravit přihlašovací údaje účtu úložiště vaše zařízení používá. Pokud upravíte přihlašovacích údajů účtu úložiště, který je aktuálně používán, lze upravit pole se přístupový klíč a režim SSL pro přihlašovací údaje účtu úložiště. Můžete zadat nový přístupový klíč úložiště nebo upravit **režim povolit šifrování SSL** výběru a uložte aktualizované nastavení.

#### <a name="to-edit-a-storage-account-credential"></a>Chcete-li upravit přihlašovací údaje účtu úložiště
1. Přejděte do služby Správce zařízení, vyberte a dvojím kliknutím. Tím se otevře **přehled** okno.
2. Vyberte **přihlašovací údaje účtu úložiště** v rámci **konfigurace** oddílu. Vypíšou se všechny existující přihlašovací údaje účtu úložiště přidruženého ke službě Správce zařízení StorSimple.
3. Tabulkový seznam přihlašovacích údajů účtu úložiště vyberte a klikněte dvakrát na účet, který chcete upravit.
4. V přihlašovacích údajů účtu úložiště **vlastnosti** okno, postupujte takto:
   
   1. Pokud třeba, můžete upravit **povolit šifrování SSL** režim výběru.
   2. Můžete nastavit opětovné vygenerování přístupových klíčů přihlašovacích údajů účtu úložiště. Další informace najdete v tématu [obnovit klíče účtu úložiště](../storage/common/storage-account-manage.md#access-keys). Zadejte nový klíč přihlašovacích údajů účtu úložiště. Pro účet úložiště Azure, jedná se o primární přístupový klíč.
   3. Klikněte na tlačítko **Uložit** v horní části **vlastnosti** okno a uložte nastavení. Nastavení se aktualizovala na **přihlašovací údaje účtu úložiště** okno.
      
      ![Upravit přihlašovací údaje účtu úložiště](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>Odstraňování přihlašovacích údajů účtu úložiště
> [!IMPORTANT]
> Přihlašovací údaje účtu úložiště můžete odstranit pouze v případě, že není používán. Pokud přihlašovací údaje účtu úložiště se používá, se zobrazí oznámení.
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>Chcete-li odstranit přihlašovací údaj účtu úložiště
1. Přejděte do služby Správce zařízení, vyberte a dvojím kliknutím. Tím se otevře **přehled** okno.
2. Vyberte **přihlašovací údaje účtu úložiště** v rámci **konfigurace** oddílu. Vypíšou se všechny existující přihlašovací údaje účtu úložiště přidruženého ke službě Správce zařízení StorSimple.
3. Tabulkový seznam přihlašovacích údajů účtu úložiště vyberte a klikněte dvakrát na účet, který chcete odstranit.
4. V přihlašovacích údajů účtu úložiště **vlastnosti** okno, postupujte takto:
   
   1. Klikněte na tlačítko **odstranit** se odstranit přihlašovací údaje.
   2. Po zobrazení výzvy k potvrzení, klikněte na tlačítko **Ano** pro pokračování v odstranění. Tabulkovém výpisu se aktualizuje tak, aby odrážely změny.
      
      ![Odstraňování přihlašovacích údajů účtu úložiště](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>Synchronizují se klíče přihlašovacích údajů účtu úložiště
Z bezpečnostních důvodů obměna klíčů je často požadavek v datových centrech. Správce Microsoft Azure můžete znovu nebo změnit primární nebo sekundární klíč přímý přístup k přihlašovací údaje účtu úložiště (prostřednictvím služby Microsoft Azure Storage). Služba Správce zařízení StorSimple nezobrazují tato změna automaticky.

Informovat službu Správce zařízení StorSimple změny, potřebujete přístup ke službě Správce zařízení StorSimple, přístup k přihlašovací údaje účtu úložiště a potom synchronizovat primární nebo sekundární klíč (podle toho, která byla změněna). Služba potom získá nejnovější klíč, šifruje klíčů a odešle zašifrovaný klíč do zařízení.

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>Chcete-li synchronizovat klíče pro přihlašovací údaje účtu úložiště ve stejném předplatném jako služba (pouze Azure)
1. V okně cílové služby vyberte svoji službu, klikněte dvakrát na název služby a pak v **konfigurace** klikněte na tlačítko **přihlašovací údaje účtu úložiště**.
2. Na **přihlašovací údaje účtu úložiště** okno, v seznamu přihlašovacích údajů účtu úložiště, vyberte účet úložiště přihlašovacích údajů, které se mají synchronizovat klíči.
3. V **vlastnosti** okno přihlašovací údaje účtu úložiště, postupujte takto:
   
    1. Klikněte na tlačítko **Další**a potom klikněte na tlačítko **synchronizace přístupového klíče**.
   
    2. Po zobrazení výzvy k potvrzení, klikněte na tlačítko **synchronizovat klíč** na dokončení synchronizace.
    
4. Ve službě Správce zařízení StorSimple budete muset aktualizovat klíč, který byl dříve změněn ve službě Microsoft Azure Storage. V **synchronizovat klíč účtu úložiště** okno, pokud primární přístupový klíč se změnil (znovu vygenerovalo), klikněte na primární a potom klikněte na tlačítko **synchronizovat klíč**. Pokud byl změněn sekundární klíč, klikněte na tlačítko **sekundární**a potom klikněte na tlačítko **synchronizovat klíč**.
   
    ![Synchronizace přístupového klíče](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-acess-key.png)

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [spravovat StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

