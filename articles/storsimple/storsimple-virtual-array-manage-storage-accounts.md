---
title: Správa přihlašovacích údajů účtu úložiště pole virtuální zařízení StorSimple | Microsoft Docs
description: Vysvětluje, jak můžete použít stránku konfigurace Správce zařízení StorSimple Pokud chcete přidat, upravit, odstranit nebo otočit klíče zabezpečení pro přihlašovací údaje účtu úložiště, která je spojená s polem virtuální zařízení StorSimple.
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
ms.openlocfilehash: a4ce2d329d0e1399cffaf886adf2b95e34b9cd7b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876982"
---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>Pomocí Správce zařízení StorSimple ke správě přihlašovacích údajů účtu úložiště pro pole virtuální zařízení StorSimple

## <a name="overview"></a>Přehled
**Konfigurace** části okně služby StorSimple Manager zařízení pole virtuální zařízení StorSimple uvede parametry globální služby, které lze vytvořit ve službě StorSimple Manager. Tyto parametry lze použít pro všechna zařízení připojená k službu a zahrnují:

* Přihlašovací údaje účtu úložiště
* Záznamy řízení přístupu
  
  ![Řídicí panel služby Správce zařízení](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

Tento kurz vysvětluje, jak můžete přidat, upravit nebo odstranit přihlašovací údaje účtu úložiště pro pole virtuální zařízení StorSimple. Informace v tomto kurzu se vztahují pouze na pole virtuální zařízení StorSimple. Informace o tom, jak spravovat účty úložiště ve řady 8000 najdete v tématu [použít službu StorSimple Manager ke správě vašeho účtu úložiště](storsimple-manage-storage-accounts.md).

Přihlašovací údaje účtu úložiště obsahovat přihlašovací údaje, které zařízení používá pro přístup k účtu úložiště pomocí poskytovatele cloudových služeb. Pro účty úložiště Microsoft Azure jsou tyto přihlašovací údaje, jako je například název účtu a primární přístupový klíč.

Na **přihlašovacích údajů účtu úložiště** okno, všechny pověření účtu úložiště, které jsou vytvořené pro fakturace předplatného se zobrazí v tabulkovém formátu, který obsahuje následující informace:

* **Název** – jedinečný název přiřazen k účtu, pokud byla vytvořena.
* **Povoleno zabezpečení SSL** – jestli SSL je povolená a komunikace zařízení cloud je přes zabezpečený kanál.
  
  ![Konfigurační oddíl](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

Běžné úkoly související s přihlašovacích údajů účtu úložiště, které lze provést na **přihlašovacích údajů účtu úložiště** okna jsou:

* Přidání přihlašovacích údajů účtu úložiště
* Upravit přihlašovací údaje účtu úložiště
* Odstranit přihlašovací údaje účtu úložiště

## <a name="types-of-storage-account-credentials"></a>Typy přihlašovacích údajů účtu úložiště
Existují tři typy přihlašovacích údajů účtu úložiště, které lze použít s zařízení StorSimple.

* **Přihlašovací údaje účtu úložiště automaticky generovaný** – jak již název naznačuje, tento typ přihlašovacích údajů účtu úložiště se automaticky generuje při prvním vytvoření služby. Další informace o tom, jak vytvořit toto pověření pro účet úložiště, najdete v části [vytvoření nové služby](storsimple-virtual-array-manage-service.md#create-a-service).
* **přihlašovací údaje účtu úložiště v předplatném služby** – Toto jsou přihlašovací údaje účtu úložiště Azure, které jsou přidruženy ke stejnému předplatnému jako službu. Další informace o tom, jak tyto úložiště jsou vytvořené pověření účtu, najdete v části [o účtech úložiště Azure](../storage/common/storage-create-storage-account.md).
* **přihlašovací údaje účtu úložiště mimo předplatné služby** – Toto jsou přihlašovací údaje účtu úložiště Azure, které nejsou přidružené k vaší službě a pravděpodobně existovala předtím, než byla služba vytvořena.

## <a name="add-a-storage-account-credential"></a>Přidání přihlašovacích údajů účtu úložiště
Konfigurace služby StorSimple Manager zařízení můžete přidat přihlašovací údaje účtu úložiště tím, že poskytuje jedinečný popisný název a přihlašovací údaje přístup, které jsou propojeny s účet úložiště. Máte také možnost povolení režimu zabezpečené sockets layer (SSL) vytvořte zabezpečený kanál pro síťovou komunikaci mezi vaším zařízením a cloudem.

Můžete vytvořit více účtů pro daný cloud poskytovatele služeb. Během je ukládání přihlašovacích údajů účtu úložiště, služba se pokouší komunikovat s poskytovatele cloudových služeb. V tuto chvíli se ověřují přihlašovací údaje a řádného materiálu přístupu, kterou jste zadali. Pověření účtu úložiště je vytvořen jen v případě, že je ověřování úspěšné. Pokud se ověřování nezdaří, se zobrazí příslušná chybová zpráva.

Chcete-li přidat přihlašovací údaje účtu úložiště Azure pomocí následujících postupů:

* Chcete-li přidat přihlašovací údaje účtu úložiště, který má stejné předplatné jako služba Správce zařízení
* Chcete-li přidat pověření účtu úložiště Azure, který je mimo předplatné služby Správce zařízení

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Chcete-li přidat přihlašovací údaje účtu úložiště, který má stejné předplatné jako služba Správce zařízení

1. Přejděte do vaší služby Správce zařízení, vyberte a dvakrát na ni klikněte. Tím se otevře **přehled** okno.
2. Vyberte **přihlašovacích údajů účtu úložiště** v rámci **konfigurace** části.
3. Klikněte na tlačítko **Přidat**.
4. V **přidání účtu úložiště** okno, postupujte takto:
   
    1. Pro **předplatné**, vyberte **aktuální**.
    2. Zadejte název účtu úložiště Azure.
    3. Vyberte **povolit** vytvořit zabezpečený kanál pro síťovou komunikaci mezi zařízení StorSimple a cloudem. Vyberte **zakázat** pouze v případě, že pracujete v privátním cloudu.
    4. Klikněte na tlačítko **Přidat**. Upozornění se zobrazí po úspěšném vytvoření účtu úložiště.<br></br>
   
        ![Přidat existující přihlašovací údaje účtu úložiště](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>Chcete-li přidat pověření účtu úložiště Azure, který je mimo předplatné služby Správce zařízení

1. Přejděte do vaší služby Správce zařízení, vyberte a dvakrát na ni klikněte. Tím se otevře **přehled** okno.
2. Vyberte **přihlašovacích údajů účtu úložiště** v rámci **konfigurace** části. Rutina Vypíše seznam existující pověření účtu úložiště přidružený k službě StorSimple Manager zařízení.
3. Klikněte na tlačítko **Přidat**.
4. V **přidání účtu úložiště** okno, postupujte takto:
   
    1. Pro **předplatné**, vyberte **jiných**.
   
    2. Zadejte název svoje přihlašovací údaje účtu úložiště Azure.
   
    3. V **přístupový klíč účtu úložiště** textové pole, zadejte primární přístupový klíč pro svoje přihlašovací údaje účtu úložiště Azure. Tento klíč, přejděte ke službě Azure Storage vyberte svoje přihlašovací údaje účtu úložiště a klikněte na **spravovat klíče účtu**. Nyní můžete zkopírovat primární přístupový klíč.
   
    4. Chcete-li povolit protokol SSL, klikněte na tlačítko **povolit** tlačítko vytvořte zabezpečený kanál pro síťovou komunikaci mezi služby StorSimple Manager zařízení a cloudem. Klikněte **zakázat** tlačítko pouze v případě, že pracujete v privátním cloudu.
   
    5. Klikněte na tlačítko **Přidat**. Upozornění se zobrazí po přihlašovací údaje účtu úložiště se úspěšně vytvořil.

5. Přihlašovací údaje účtu nově vytvořené úložiště se zobrazí v okně služby Správce konfigurace zařízení StorSimple v části **přihlašovacích údajů účtu úložiště**.
   
    ![Přidat přihlašovací údaje účtu úložiště mimo předplatné služby Správce zařízení](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>Upravit přihlašovací údaje účtu úložiště
Můžete upravit přihlašovací údaje účtu úložiště používané zařízení. Pokud chcete upravit přihlašovací údaje účtu úložiště, který se právě používá, jsou pole lze upravit přístupový klíč a režimu protokolu SSL pro přihlašovací údaje účtu úložiště. Můžete zadat nový přístupový klíč úložiště nebo upravit **režim povolit šifrování SSL** výběr a uložte aktualizovaná nastavení.

#### <a name="to-edit-a-storage-account-credential"></a>Chcete-li upravit pověření účtu úložiště
1. Přejděte do vaší služby Správce zařízení, vyberte a dvakrát na ni klikněte. Tím se otevře **přehled** okno.
2. Vyberte **přihlašovacích údajů účtu úložiště** v rámci **konfigurace** části. Rutina Vypíše seznam existující pověření účtu úložiště přidružený k službě StorSimple Manager zařízení.
3. V tabulkovém seznam přihlašovacích údajů účtu úložiště vyberte a dvakrát klikněte na účet, který chcete upravit.
4. V přihlašovací údaje účtu úložiště **vlastnosti** okno, postupujte takto:
   
   1. Pokud potřeby můžete změnit **povolit šifrování SSL** režim výběru.
   2. Můžete znovu vygenerovat klíče pro přístup k pověření účtu úložiště. Další informace najdete v tématu [obnovit klíče účtu úložiště](../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys). Zadejte nový klíč přihlašovacích údajů účtu úložiště. U účtu úložiště Azure je to primární přístupový klíč.
   3. Klikněte na tlačítko **Uložit** v horní části **vlastnosti** okno a uložte nastavení. Nastavení jsou aktualizovány na **přihlašovacích údajů účtu úložiště** okno.
      
      ![Upravit přihlašovací údaje účtu úložiště](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>Odstranit přihlašovací údaje účtu úložiště
> [!IMPORTANT]
> Pověření účtu úložiště můžete odstranit pouze v případě, že není používán. Pokud pověření účtu úložiště se používá, budete upozorněni.
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>Chcete-li odstranit přihlašovací údaje účtu úložiště
1. Přejděte do vaší služby Správce zařízení, vyberte a dvakrát na ni klikněte. Tím se otevře **přehled** okno.
2. Vyberte **přihlašovacích údajů účtu úložiště** v rámci **konfigurace** části. Rutina Vypíše seznam existující pověření účtu úložiště přidružený k službě StorSimple Manager zařízení.
3. V tabulkovém seznam přihlašovacích údajů účtu úložiště vyberte a dvakrát klikněte na účet, který chcete odstranit.
4. V přihlašovací údaje účtu úložiště **vlastnosti** okno, postupujte takto:
   
   1. Klikněte na tlačítko **odstranit** odstranit přihlašovací údaje.
   2. Po zobrazení výzvy k potvrzení, klikněte na tlačítko **Ano** Chcete-li pokračovat v odstraňování. Tabulkový výčet je aktualizována tak, aby odrážely změny.
      
      ![Odstranit přihlašovací údaje účtu úložiště](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>Synchronizace přihlašovacích údajů klíče účtu úložiště
Z bezpečnostních důvodů střídání klíčů je často požadavek v datových centrech. Microsoft Azure správce můžete znovu vygenerovat nebo změnit primární nebo sekundární klíč tak, že přímý přístup k pověřením účtu úložiště (prostřednictvím služby Microsoft Azure Storage). Služby StorSimple Manager zařízení automaticky nezná tuto změnu.

Informovat službu StorSimple Manager zařízení změny, budete potřebovat přístup ke službě StorSimple Manager zařízení přístup k pověřením účtu úložiště a potom synchronizovat primární nebo sekundární klíč (podle toho, která byla změněna). Tato služba pak získá nejnovější klíč, zašifruje klíče a odešle zašifrovaný klíč do zařízení.

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>K synchronizaci klíče pro přihlašovací údaje účtu úložiště ve stejném předplatném jako služba (pouze Azure)
1. V okně Cílová služba vyberte svoji službu, dvakrát klikněte na název služby a potom v **konfigurace** klikněte na tlačítko **přihlašovacích údajů účtu úložiště**.
2. Na **přihlašovacích údajů účtu úložiště** okno, v seznamu přihlašovacích údajů účtu úložiště, vyberte účet úložiště pověření, jehož klíče, které chcete synchronizovat.
3. V **vlastnosti** okno pro přihlašovací údaje účtu vybrané úložiště, postupujte takto:
   
    1. Klikněte na tlačítko **Další**a potom klikněte na **synchronizace přístupový klíč**.
   
    2. Po zobrazení výzvy k potvrzení, klikněte na tlačítko **klíč synchronizace** k provedení synchronizace.
    
4. Ve službě Správce zařízení StorSimple budete muset aktualizovat klíč, který byl dříve změněn ve službě Microsoft Azure Storage. V **klíč účtu úložiště synchronizovat** okno, pokud byl změněn primární přístupový klíč (znova vygeneroval), klikněte na primární a pak klikněte na **klíč synchronizace**. Pokud se sekundární klíč byl změněn, klikněte na tlačítko **sekundární**a potom klikněte na **klíč synchronizace**.
   
    ![Synchronizace přístupového klíče](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-acess-key.png)

## <a name="next-steps"></a>Další kroky
* Zjistěte, jak [spravovat vaše pole virtuální zařízení StorSimple](storsimple-ova-web-ui-admin.md).

