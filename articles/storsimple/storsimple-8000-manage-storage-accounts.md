---
title: Spravovat přihlašovací údaje účtu úložiště StorSimple pro zařízení s Microsoft Azure StorSimple 8000 series | Dokumentace Microsoftu
description: Vysvětluje, jak můžete na stránce konfigurace Správce zařízení StorSimple můžete přidat, upravit, odstranit nebo obměna klíčů zabezpečení pro účet úložiště.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38688724"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>Použití služby Správce zařízení StorSimple pro správu přihlašovacích údajů účtu úložiště

## <a name="overview"></a>Přehled

**Konfigurace** část v okně služby Správce zařízení StorSimple představuje všechny parametry globální služby, které lze vytvořit ve službě Správce zařízení StorSimple. Tyto parametry můžete použít pro všechna zařízení připojená ke službě a zahrnují:

* Přihlašovací údaje účtu úložiště
* Šablony šířky pásma 
* Záznamy řízení přístupu 

Tento kurz vysvětluje, jak přidat, upravit, odstranit přihlašovací údaje účtu úložiště nebo obměna klíčů zabezpečení pro účet úložiště.

 ![Seznam přihlašovacích údajů účtu úložiště](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

Účty úložiště obsahovat přihlašovací údaje, které zařízení StorSimple používá pro přístup k vašemu účtu úložiště pomocí poskytovatele cloudových služeb. Pro účty úložiště Microsoft Azure jsou tyto přihlašovací údaje, jako je například název účtu a primární přístupový klíč. 

Na **přihlašovací údaje účtu úložiště** okno, všechny účty úložiště, které jsou vytvořené pro předplatné pro fakturaci se zobrazí v tabulkovém formátu, který obsahuje následující informace:

* **Název** – jedinečný název přiřazený k účtu při vytvoření rovnou uložil.
* **Protokol SSL povolený** – SSL, zda je povolen a je komunikace typu zařízení cloud přes zabezpečený kanál.
* **Používá** – počet svazky s využitím účtu úložiště.

Jsou zvládnout běžné úkoly související s účty úložiště, které lze provést:

* Přidání účtu úložiště 
* Upravit účet úložiště 
* Odstranění účtu úložiště 
* Obměna klíčů účtů úložiště 

## <a name="types-of-storage-accounts"></a>Typy účtů úložiště

Existují tři typy účtů úložiště, které je možné se zařízením StorSimple.

* **Účty úložiště pro automaticky generované** – jak název napovídá, tento typ účtu úložiště se automaticky generuje při prvním vytvoření služby. Další informace o vytvoření tohoto účtu úložiště najdete v tématu [krok 1: vytvoření nové služby](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service) v [nasazení zařízení StorSimple v místním](storsimple-8000-deployment-walkthrough-u2.md). 
* **Účty úložiště v předplatném služby** – jedná se účty úložiště Azure, které jsou přidruženy stejném předplatném jako služby. Další informace o tom, jak tyto úložiště se vytvoří účty, najdete v článku [o účtech Azure Storage](../storage/common/storage-create-storage-account.md). 
* **Účty úložiště mimo předplatné služby** – jedná se účty úložiště Azure, které nejsou přidružené k vaší službě a pravděpodobně existovala předtím, než služba byla vytvořena.

## <a name="add-a-storage-account"></a>Přidání účtu úložiště

Přidání účtu úložiště tím, že poskytuje jedinečný popisný název a přihlašovacích údajů pro přístup, které jsou propojeny do účtu úložiště (s poskytovateli zadané cloudové služby). Máte také možnost zapnutí režimu secure Sockets Layer (SSL) layer vytvořit zabezpečený kanál pro síťovou komunikaci mezi zařízením a cloudem.

Můžete vytvořit více účtů pro poskytovatele služeb daného cloudu. Mějte na paměti, ale, že po vytvoření účtu úložiště nejde změnit poskytovatele cloudových služeb.

Při uložení účtu úložiště se služba pokusí komunikovat se svým poskytovatelem cloudových služeb. V tuto chvíli se ověřit přihlašovací údaje a přístup materiál, který jste zadali. Účet úložiště se vytvoří pouze v případě, že bude ověřování úspěšné. Pokud se ověření nezdaří, se zobrazí příslušná chybová zpráva.

Pomocí následujících postupů můžete přidat přihlašovací údaje účtu úložiště Azure:

* Chcete-li přidat přihlašovací údaje účtu úložiště, který má stejné předplatné Azure jako službu Správce zařízení
* Přidání Azure přihlašovací údaje účtu úložiště, které je mimo předplatné služby Správce zařízení

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>Chcete-li přidat přihlašovacích údajů účtu úložiště Azure mimo předplatné služby Správce zařízení StorSimple

1. Přejděte k vaší službě Správce zařízení StorSimple, vyberte a dvojím kliknutím ho. Tím se otevře **přehled** okno.
2. Vyberte **přihlašovací údaje účtu úložiště** v rámci **konfigurace** oddílu. Vypíšou se všechny existující přihlašovací údaje účtu úložiště přidruženého ke službě Správce zařízení StorSimple.
3. Klikněte na tlačítko **Add** (Přidat).
4. V **přidat přihlašovací údaj účtu úložiště** okno, postupujte takto:
   
    1. Pro **předplatné**vyberte **jiných**.
   
    2. Zadejte název své přihlašovací údaje účtu úložiště Azure.
   
    3. V **přístupový klíč účtu úložiště** textové pole, zadat primární přístupový klíč pro vaše přihlašovací údaje účtu úložiště Azure. Chcete-li tento klíč získat, přejděte do služby Azure Storage, vyberte vaše přihlašovací údaje účtu úložiště a klikněte na **spravovat klíče účtu**. Nyní můžete zkopírovat primární přístupový klíč.
   
    4. Chcete-li povolit protokol SSL, klikněte na tlačítko **povolit** tlačítko vytvořit zabezpečený kanál pro síťovou komunikaci mezi službou Správce zařízení StorSimple a cloudu. Klikněte na tlačítko **zakázat** tlačítko pouze v případě, že pracujete v privátním cloudu.
   
    5. Klikněte na tlačítko **Add** (Přidat). Po úspěšném vytvoření přihlašovacích údajů účtu úložiště, se zobrazí oznámení.

5. Zobrazí se přihlašovací údaje účtu úložiště vytvořeného v okně služby Správce zařízení StorSimple nakonfigurujte podle **přihlašovací údaje účtu úložiště**.
   


## <a name="edit-a-storage-account"></a>Upravit účet úložiště

Můžete upravit účet úložiště, který je používán kontejner svazků. Pokud upravíte účet úložiště, který je aktuálně používán, lze upravit pouze pole se přístupový klíč pro účet úložiště. Můžete zadat nový přístupový klíč úložiště a uložte aktualizované nastavení.

#### <a name="to-edit-a-storage-account"></a>Chcete-li upravit účet úložiště

1. Přejděte do služby Správce zařízení StorSimple. V části **Konfigurace** klikněte na **Přihlašovací údaje účtu úložiště**.

    ![Přihlašovací údaje účtu úložiště](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. V **přihlašovací údaje účtu úložiště** okno, ze seznamu přihlašovací údaje účtu úložiště, vyberte a klikněte na ta, kterou chcete upravit. 

3. Můžete upravit **povolit šifrování SSL** výběru. Můžete také kliknout na **více...**  a pak vyberte **synchronizace přístupového klíče k otočení** přístupové klíče účtu úložiště. Přejděte na [klíče otočení účtů úložiště](#key-rotation-of-storage-accounts) Další informace o tom, jak provádět obměny klíče. Po úpravě nastavení, klikněte na tlačítko **Uložit**. 

    ![Uložení přihlašovacích údajů účtu úložiště upravené](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. Po zobrazení výzvy k potvrzení klikněte na **Ano**. 

    ![Potvrzení změn](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

Nastavení se aktualizuje a uloží pro váš účet úložiště. 

## <a name="delete-a-storage-account"></a>Odstranění účtu úložiště

> [!IMPORTANT]
> Účet úložiště můžete odstranit pouze v případě, že není používán kontejner svazků. Pokud účet úložiště používá kontejner svazků, nejprve odstranit kontejner svazků a potom odstraňte přidružený účet úložiště.

#### <a name="to-delete-a-storage-account"></a>Pokud chcete odstranit účet úložiště

1. Přejděte do služby Správce zařízení StorSimple. V části **Konfigurace** klikněte na **Přihlašovací údaje účtu úložiště**.

2. V tabulkovém seznam účtů úložiště najeďte myší na účet, který chcete odstranit. Kliknutí pravým tlačítkem myši vyvolejte místní nabídku a klikněte na tlačítko **odstranit**.

    ![Odstranit přihlašovací údaje účtu úložiště](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. Po zobrazení výzvy k potvrzení, klikněte na tlačítko **Ano** pro pokračování v odstranění. Tabulkovém výpisu se aktualizují tak, aby odrážely změny.

    ![Potvrzení odstranění](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>Obměna klíčů účtů úložiště

Z bezpečnostních důvodů obměna klíčů je často požadavek v datových centrech. Každé předplatné Microsoft Azure může mít jeden nebo více účtů přidružené úložiště. Přístup do těchto účtů je řízen předplatného a přístupových klíčů pro každý účet úložiště. 

Při vytváření účtu úložiště vygeneruje Microsoft Azure dva 512bitové přístupových klíčů k úložišti, které se používají k ověření při přístupu k účtu úložiště. Máte dva přístupové klíče k úložišti, můžete znovu vygenerovat klíče bez přerušení poskytování vaší služby úložiště nebo přístupu k této službě. Klíč, který je aktuálně používána *primární* klíč a záložní klíč se označuje jako *sekundární* klíč. Jeden z těchto dvou klíčů je nutné zadat, když zařízení Microsoft Azure StorSimple přistupuje k poskytovatele cloudových služeb úložiště.

## <a name="what-is-key-rotation"></a>Co je obměna klíčů?

Obvykle aplikace použít pouze jeden z klíčů pro přístup k datům. Po určité době času můžete mít vaše aplikace přepnout na druhý klíč. Poté, co jste přešli svoje aplikace na sekundární klíč, můžete vyřadit první klíč a potom vygenerujte nový klíč. Vaše aplikace přístup k datům pomocí dvou klíčů tímto způsobem umožňuje nenastane přitom žádný výpadek.

Klíče účtu úložiště jsou vždy uloženy ve službě v šifrovaném tvaru. Však toto nastavení lze změnit prostřednictvím služby Správce zařízení StorSimple. Službu můžete získat primární klíč a sekundární klíč pro všechny účty úložiště ve stejném předplatném, včetně účty vytvořené ve službě Storage i výchozí účty úložiště generovány, pokud byla služba služby Správce zařízení StorSimple prvním vytvoření. Služba Správce zařízení StorSimple bude vždy získat tyto klíče z portálu Azure classic a uložit je do zašifrované.

## <a name="rotation-workflow"></a>Střídání pracovních postupů

Správce Microsoft Azure můžete znovu nebo změnit primární nebo sekundární klíč přímý přístup k účtu úložiště (prostřednictvím služby Microsoft Azure Storage). Služba Správce zařízení StorSimple nezobrazují tato změna automaticky.

Informovat službu Správce zařízení StorSimple změny, můžete bude potřebovat přístup ke službě Správce zařízení StorSimple přístup k účtu úložiště a pak synchronizovat primární nebo sekundární klíč (podle toho, která byla změněna). Služba potom získá nejnovější klíč, šifruje klíčů a odešle zašifrovaný klíč do zařízení.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>K synchronizaci klíčů pro účty úložiště ve stejném předplatném jako služba 
1. Přejděte do služby Správce zařízení StorSimple. V části **Konfigurace** klikněte na **Přihlašovací údaje účtu úložiště**.
2. V tabulkovém výpisu účtů úložiště, klikněte na ten, který chcete upravit. 

    ![Synchronizovat klíče](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. Klikněte na tlačítko **... Další** a pak vyberte **synchronizace přístupového klíče k otočení**.   

    ![Synchronizovat klíče](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. Ve službě Správce zařízení StorSimple budete muset aktualizovat klíč, který byl dříve změněn ve službě Microsoft Azure Storage. Pokud primární přístupový klíč se změnil (znovu vygenerovalo), vyberte **primární** klíč. Pokud byl změněn sekundární klíč, vyberte **sekundární** klíč. Klikněte na tlačítko **synchronizovat klíč**.
      
      ![Synchronizovat klíče](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

Po úspěšném sycnhronized klíč, budete upozorněni.

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>K synchronizaci klíčů pro účty úložiště mimo předplatné služby
1. Na **služby** stránky, klikněte na tlačítko **konfigurovat** kartu.
2. Klikněte na tlačítko **přidat/upravit účty úložiště**.
3. V dialogovém okně postupujte takto:
   
   1. Vyberte účet úložiště se přístupový klíč, který chcete aktualizovat.
   2. Je potřeba aktualizovat přístupový klíč úložiště ve službě Správce zařízení StorSimple. V takovém případě se zobrazí přístupový klíč úložiště. Zadejte nový klíč v **přístupový klíč účtu úložiště** pole. 
   3. Uložte provedené změny. Nyní je třeba aktualizovat přístupový klíč účtu úložiště.

## <a name="next-steps"></a>Další postup
* Další informace o [zabezpečení zařízení StorSimple](storsimple-8000-security.md).
* Další informace o [použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

