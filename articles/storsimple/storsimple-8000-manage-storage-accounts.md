---
title: Spravovat vaše zařízení StorSimple přihlašovací údaje pro řadu zařízení Microsoft Azure StorSimple 8000 | Microsoft Docs
description: Vysvětluje, jak můžete použít stránku konfigurace Správce zařízení StorSimple můžete přidávat, upravovat, odstraňovat nebo otočit zabezpečení klíče pro účet úložiště.
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
ms.openlocfilehash: 36058ad69ea670998b50cf9038741c294a5b79ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875127"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>Použít službu StorSimple Manager zařízení ke správě přihlašovací údaje účtu úložiště

## <a name="overview"></a>Přehled

**Konfigurace** část v okně service Manager zařízení StorSimple představuje všechny parametry globální služby, které lze vytvořit ve službě StorSimple Manager zařízení. Tyto parametry lze použít pro všechna zařízení připojená k službu a zahrnují:

* Přihlašovací údaje účtu úložiště
* Šablony šířky pásma 
* Záznamy řízení přístupu 

Tento kurz vysvětluje, jak přidat, upravit, nebo odstranit přihlašovací údaje pro účet úložiště nebo otočit zabezpečení klíče pro účet úložiště.

 ![Seznam přihlašovacích údajů účtu úložiště](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

Účty úložiště obsahovat přihlašovací údaje, které zařízení StorSimple se používá pro přístup k účtu úložiště pomocí poskytovatele cloudových služeb. Pro účty úložiště Microsoft Azure jsou tyto přihlašovací údaje, jako je například název účtu a primární přístupový klíč. 

Na **přihlašovacích údajů účtu úložiště** okno, všechny účty úložiště, které jsou vytvořené pro fakturace předplatného se zobrazí v tabulkovém formátu, který obsahuje následující informace:

* **Název** – jedinečný název přiřazen k účtu, pokud byla vytvořena.
* **Povoleno zabezpečení SSL** – jestli SSL je povolená a komunikace zařízení cloud je přes zabezpečený kanál.
* **Používá** – počet svazků pomocí účtu úložiště.

Nejběžnější úlohy související s účty úložiště, které lze provést jsou:

* Přidání účtu úložiště 
* Upravit účet úložiště 
* Odstranění účtu úložiště 
* Střídání klíče účtů úložiště 

## <a name="types-of-storage-accounts"></a>Typy účtů úložiště

Existují tři typy účtů úložiště, které lze použít s zařízení StorSimple.

* **Účty úložiště automaticky generovaný** – jak již název naznačuje, tento typ účtu úložiště se automaticky generuje při prvním vytvoření služby. Další informace o tom, jak vytvořit účet úložiště, najdete v části [krok 1: vytvoření nové služby](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service) v [nasazení místního zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md). 
* **Účty úložiště v předplatném služby** – Toto jsou účty úložiště Azure, které jsou přidruženy ke stejnému předplatnému jako službu. Další informace o tom, jak tyto úložiště vytvoření účtů, najdete v části [o účtech úložiště Azure](../storage/common/storage-create-storage-account.md). 
* **Účty úložiště mimo předplatné služby** – Toto jsou účty úložiště Azure, které nejsou přidružené k vaší službě a pravděpodobně existovala předtím, než byla služba vytvořena.

## <a name="add-a-storage-account"></a>Přidání účtu úložiště

Můžete přidat účet úložiště tím, že poskytuje jedinečný popisný název a přihlašovací údaje přístup, které jsou propojeny s účet úložiště (s poskytovateli zadané cloudové služby). Máte také možnost povolení režimu zabezpečené sockets layer (SSL) vytvořte zabezpečený kanál pro síťovou komunikaci mezi vaším zařízením a cloudem.

Můžete vytvořit více účtů pro daný cloud poskytovatele služeb. Upozorňujeme ale, že po vytvoření účtu úložiště nemůžete změnit poskytovatele cloudové služby.

Během ukládání je účet úložiště, služba se pokouší komunikovat s poskytovatele cloudových služeb. V tuto chvíli se ověřit přihlašovací údaje a řádného materiálu přístupu, kterou jste zadali. Účet úložiště se vytvoří pouze v případě, že je ověřování úspěšné. Pokud se ověřování nezdaří, se zobrazí příslušná chybová zpráva.

Chcete-li přidat přihlašovací údaje účtu úložiště Azure pomocí následujících postupů:

* Chcete-li přidat přihlašovací údaje účtu úložiště, který má stejné předplatné jako služba Správce zařízení
* Chcete-li přidat pověření účtu úložiště Azure, který je mimo předplatné služby Správce zařízení

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>Chcete-li přidat pověření účtu úložiště Azure mimo předplatné služby StorSimple Manager zařízení

1. Přejděte do služby StorSimple Manager zařízení, vyberte a dvakrát na ni klikněte. Tím se otevře **přehled** okno.
2. Vyberte **přihlašovacích údajů účtu úložiště** v rámci **konfigurace** části. Rutina Vypíše seznam existující pověření účtu úložiště přidružený k službě StorSimple Manager zařízení.
3. Klikněte na tlačítko **Přidat**.
4. V **přidat přihlašovací údaje účtu úložiště** okno, postupujte takto:
   
    1. Pro **předplatné**, vyberte **jiných**.
   
    2. Zadejte název svoje přihlašovací údaje účtu úložiště Azure.
   
    3. V **přístupový klíč účtu úložiště** textové pole, zadejte primární přístupový klíč pro svoje přihlašovací údaje účtu úložiště Azure. Tento klíč, přejděte ke službě Azure Storage vyberte svoje přihlašovací údaje účtu úložiště a klikněte na **spravovat klíče účtu**. Nyní můžete zkopírovat primární přístupový klíč.
   
    4. Chcete-li povolit protokol SSL, klikněte na tlačítko **povolit** tlačítko vytvořte zabezpečený kanál pro síťovou komunikaci mezi služby StorSimple Manager zařízení a cloudem. Klikněte **zakázat** tlačítko pouze v případě, že pracujete v privátním cloudu.
   
    5. Klikněte na tlačítko **Přidat**. Upozornění se zobrazí po přihlašovací údaje účtu úložiště se úspěšně vytvořil.

5. Přihlašovací údaje účtu nově vytvořené úložiště se zobrazí v okně služby Správce konfigurace zařízení StorSimple v části **přihlašovacích údajů účtu úložiště**.
   


## <a name="edit-a-storage-account"></a>Upravit účet úložiště

Můžete upravit účet úložiště, který je používán kontejner svazků. Pokud chcete upravit účet úložiště, který se právě používá, je možné upravit pouze pole přístupový klíč pro účet úložiště. Můžete zadat nový přístupový klíč úložiště a uložte aktualizovaná nastavení.

#### <a name="to-edit-a-storage-account"></a>Chcete-li upravit účet úložiště

1. Přejděte do služby Správce zařízení StorSimple. V části **Konfigurace** klikněte na **Přihlašovací údaje účtu úložiště**.

    ![Přihlašovací údaje účtu úložiště](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. V **přihlašovacích údajů účtu úložiště** okno ze seznamu přihlašovacích údajů účtu úložiště, vyberte a klikněte na jeden, který chcete upravit. 

3. Můžete upravit **povolit šifrování SSL** výběr. Můžete také kliknout na **více...**  a pak vyberte **synchronizace přístupový klíč pro otočit** klíče pro přístup k účtu úložiště. Přejděte na [klíče otočení účtů úložiště](#key-rotation-of-storage-accounts) Další informace o tom, jak provést střídání klíče. Jakmile změníte nastavení, klikněte na tlačítko **Uložit**. 

    ![Uložte upravený úložiště pověření](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. Po zobrazení výzvy k potvrzení klikněte na **Ano**. 

    ![Potvrďte změny](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

Nastavení budou aktualizovány a uloženy pro váš účet úložiště. 

## <a name="delete-a-storage-account"></a>Odstranění účtu úložiště

> [!IMPORTANT]
> Účet úložiště můžete odstranit pouze v případě, že není používán kontejner svazků. Pokud účet úložiště je používán kontejner svazků, nejprve odstranit kontejner svazků a pak odstraňte přidruženého účtu úložiště.

#### <a name="to-delete-a-storage-account"></a>Chcete odstranit účet úložiště

1. Přejděte do služby Správce zařízení StorSimple. V části **Konfigurace** klikněte na **Přihlašovací údaje účtu úložiště**.

2. V tabulkovém seznam účtů úložiště najeďte myší na účet, který chcete odstranit. Klikněte pravým tlačítkem a vyvolání v místní nabídce klikněte na tlačítko **odstranit**.

    ![Odstranit přihlašovací údaje pro účet úložiště](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. Po zobrazení výzvy k potvrzení, klikněte na tlačítko **Ano** Chcete-li pokračovat v odstraňování. Tabulkové výpis budou aktualizovány tak, aby odrážely změny.

    ![Potvrzení odstranění](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>Střídání klíče účtů úložiště

Z bezpečnostních důvodů střídání klíčů je často požadavek v datových centrech. Každé předplatné Microsoft Azure může mít jeden nebo více účtů úložiště. Přístup do těchto účtů řídí předplatného a přístupových klíčů pro každý účet úložiště. 

Při vytváření účtu úložiště vygeneruje Microsoft Azure dva 512bitové přístupové klíče k úložišti, které se používají pro ověření při přístupu k účtu úložiště. Máte dva přístupové klíče k úložišti, můžete vygenerovat klíče bez přerušení poskytování vaší služby úložiště nebo přístupu k této službě. Klíč, který se právě používá, je *primární* a zálohování klíč se označuje jako *sekundární* klíč. Jeden z těchto dvou klíčů musí zadat, když zařízení s Microsoft Azure StorSimple přistupuje k poskytovatele cloudových služeb úložiště.

## <a name="what-is-key-rotation"></a>Co je střídání klíčů?

Obvykle se aplikace používají pouze jeden z klíčů přistupovat k datům. Po určité době dobu může mít vaše aplikace přepnout na používání druhý klíč. Poté, co jste přepnuli aplikace k sekundární klíč, můžete vyřadit z provozu první klíč a pak vygenerovat nový klíč. Pomocí dvou klíčů tímto způsobem umožňuje vaší aplikace přístup k datům, aniž by docházelo k výpadkům.

Klíče účtu úložiště jsou vždy uloženy ve službě v šifrovaném formátu. To však mohou obnovit přes službu StorSimple Manager zařízení. Služby mohou získat primární klíč a sekundární klíč pro všechny účty úložiště ve stejném předplatném, včetně účtů vytvořených v rámci služby úložiště a také výchozí účty úložiště vygeneruje, když služba service Manager zařízení StorSimple nejprve vytvořit. Služby StorSimple Manager zařízení bude vždy získat tyto klíče z portálu Azure classic a uložit je do šifrované způsobem.

## <a name="rotation-workflow"></a>Otočení pracovního postupu

Microsoft Azure správce můžete znovu vygenerovat nebo změnit primární nebo sekundární klíč tak, že přímý přístup k účtu úložiště (prostřednictvím služby Microsoft Azure Storage). Služby StorSimple Manager zařízení automaticky nezná tuto změnu.

K informování služby StorSimple Manager zařízení změny, budete potřebovat přístup ke službě StorSimple Manager zařízení přístup k účtu úložiště a potom synchronizovat primární nebo sekundární klíč (podle toho, která byla změněna). Tato služba pak získá nejnovější klíč, zašifruje klíče a odešle zašifrovaný klíč do zařízení.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>K synchronizaci klíčů pro účty úložiště ve stejném předplatném jako služba 
1. Přejděte do služby Správce zařízení StorSimple. V části **Konfigurace** klikněte na **Přihlašovací údaje účtu úložiště**.
2. V tabulkovém seznam účtů úložiště, klikněte na ten, který chcete upravit. 

    ![Synchronizovat klíče](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. Klikněte na tlačítko **... Další** a pak vyberte **synchronizace přístupový klíč pro otočit**.   

    ![Synchronizovat klíče](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. Ve službě Správce zařízení StorSimple budete muset aktualizovat klíč, který byl dříve změněn ve službě Microsoft Azure Storage. Pokud primární přístupový klíč byl změněn (znova vygeneroval), vyberte **primární** klíč. Pokud se sekundární klíč byl změněn, vyberte **sekundární** klíč. Klikněte na tlačítko **klíč synchronizace**.
      
      ![Synchronizovat klíče](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

Po úspěšně sycnhronized klíč, budete upozorněni.

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>K synchronizaci klíčů pro účty úložiště mimo předplatné služby
1. Na **služby** klikněte na tlačítko **konfigurace** kartě.
2. Klikněte na tlačítko **přidat či upravit účty úložiště**.
3. V dialogovém okně postupujte takto:
   
   1. Vyberte účet úložiště s přístupový klíč, který chcete aktualizovat.
   2. Budete muset aktualizovat přístupový klíč úložiště ve službě StorSimple Manager zařízení. V takovém případě se zobrazí přístupový klíč úložiště. Zadejte nový klíč v **přístupový klíč účtu úložiště** pole. 
   3. Uložte provedené změny. Nyní je třeba aktualizovat přístupový klíč účtu úložiště.

## <a name="next-steps"></a>Další kroky
* Další informace o [zabezpečení zařízení StorSimple](storsimple-8000-security.md).
* Další informace o [pomocí služby StorSimple Manager zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

