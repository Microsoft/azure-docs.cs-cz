---
title: Správa přihlašovacích údajů účtu úložiště, zařízení StorSimple 8000 series
description: Vysvětluje, jak můžete pomocí stránky StorSimple Device Manager nakonfigurovat přidat, upravit, odstranit nebo otočit klíče zabezpečení pro účet úložiště.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 0df0c12d5942179e2492bfdc932fa25d126a2426
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94951725"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>Použijte službu StorSimple Device Manager ke správě přihlašovacích údajů účtu úložiště.

## <a name="overview"></a>Přehled

**Konfigurační** oddíl v okně StorSimple Device Manager Service obsahuje všechny parametry globálních služeb, které se dají vytvořit ve službě StorSimple Device Manager. Tyto parametry se dají použít pro všechna zařízení připojená ke službě a zahrnují:

* Přihlašovací údaje účtu úložiště
* Šablony šířky pásma 
* Záznamy řízení přístupu 

V tomto kurzu se dozvíte, jak přidat, upravit nebo odstranit přihlašovací údaje k účtu úložiště nebo jak vystřídat klíče zabezpečení pro účet úložiště.

 ![Seznam přihlašovacích údajů účtu úložiště 1](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

Účty úložiště obsahují přihlašovací údaje, které zařízení StorSimple používá pro přístup k vašemu účtu úložiště ve vašem poskytovateli cloudové služby. U Microsoft Azure účtů úložiště se jedná o přihlašovací údaje, jako je název účtu a primární přístupový klíč. 

V okně **pověření účtu úložiště** se všechny účty úložiště, které jsou vytvořené pro fakturační předplatné, zobrazují v tabulkovém formátu, který obsahuje následující informace:

* **Název** – jedinečný název přiřazený k účtu při jeho vytvoření.
* **SSL povoleno** – zda je povolen protokol TLS a komunikace mezi zařízeními a cloudem je přes zabezpečený kanál.
* **Používá** se – počet svazků s použitím účtu úložiště.

Nejběžnější úlohy týkající se účtů úložiště, které je možné provádět, jsou tyto:

* Přidat účet úložiště 
* Úprava účtu úložiště 
* Odstranění účtu úložiště 
* Střídání klíčů účtů úložiště 

## <a name="types-of-storage-accounts"></a>Typy účtů úložiště

Existují tři typy účtů úložiště, které se dají používat se zařízením StorSimple.

* **Automaticky generované účty úložiště** – jak název navrhuje, tento typ účtu úložiště se automaticky vygeneruje při prvním vytvoření služby. Další informace o tom, jak se tento účet úložiště vytváří, najdete v části [Krok 1: vytvoření nové služby](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service) v tématu [nasazení místního zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md). 
* **Účty úložiště v předplatném služby** – jedná se o účty úložiště Azure, které jsou přidružené ke stejnému předplatnému jako služba. Další informace o tom, jak se tyto účty úložiště vytvářejí, najdete v tématu [informace o Azure Storagech účtech](../storage/common/storage-account-create.md). 
* **Účty úložiště mimo předplatné služby** – jedná se o účty úložiště Azure, které nejsou přidružené k vaší službě a nejspíš existovaly před vytvořením služby.

## <a name="add-a-storage-account"></a>Přidat účet úložiště

Účet úložiště můžete přidat zadáním jedinečného popisného názvu a přístupových přihlašovacích údajů, které jsou propojené s účtem úložiště (se zadaným poskytovatelem cloudových služeb). Máte také možnost povolit režim TLS (Transport Layer Security), dříve označovaný jako SSL (Secure Sockets Layer) (SSL), a vytvořit zabezpečený kanál pro síťovou komunikaci mezi vaším zařízením a cloudem.

Pro daného poskytovatele cloudové služby můžete vytvořit několik účtů. Mějte ale na paměti, že po vytvoření účtu úložiště nemůžete změnit poskytovatele cloudových služeb.

Při ukládání účtu úložiště se služba pokusí o komunikaci s vaším poskytovatelem cloudových služeb. Přihlašovací údaje a přístupový materiál, které jste zadali, se v tuto chvíli ověřují. Účet úložiště se vytvoří jenom v případě, že je ověřování úspěšné. Pokud se ověření nezdaří, zobrazí se příslušná chybová zpráva.

K přidání přihlašovacích údajů k účtu úložiště Azure použijte následující postupy:

* Přidání přihlašovacích údajů účtu úložiště, které mají stejné předplatné Azure jako služba Device Manager
* Přidání přihlašovacích údajů účtu Azure Storage mimo předplatné služby Device Manager

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>Přidání přihlašovacích údajů účtu Azure Storage mimo předplatné služby StorSimple Device Manager

1. Přejděte ke službě StorSimple Device Manager, vyberte ji a dvakrát na ni klikněte. Tím se otevře okno **Přehled** .
2. V části **Konfigurace** vyberte **přihlašovací údaje účtu úložiště** . Tato seznam obsahuje všechna existující pověření účtu úložiště přidružená ke službě StorSimple Device Manager.
3. Klikněte na **Přidat**.
4. V okně **Přidat přihlašovací údaje účtu úložiště** udělejte toto:
   
    1. U **předplatného** vyberte **jiné**.
   
    2. Zadejte název přihlašovacích údajů k účtu úložiště Azure.
   
    3. Do textového pole **přístupový klíč účtu úložiště** zadejte primární přístupový klíč pro přihlašovací údaje k účtu úložiště Azure. Tento klíč získáte tak, že přejdete do služby Azure Storage, vyberete přihlašovací údaje účtu úložiště a kliknete na **spravovat klíče účtu**. Nyní můžete zkopírovat primární přístupový klíč.
   
    4. Pokud chcete povolit protokol TLS, klikněte na tlačítko **Povolit** a vytvořte zabezpečený kanál pro síťovou komunikaci mezi službou StorSimple Device Manager a cloudem. Klikněte na tlačítko **Zakázat** jenom v případě, že pracujete v privátním cloudu.
   
    5. Klikněte na **Přidat**. Po úspěšném vytvoření přihlašovacích údajů účtu úložiště se zobrazí oznámení.

5. Nově vytvořené přihlašovací údaje účtu úložiště se zobrazí v okně StorSimple konfigurace služby Device Manager v části **přihlašovací údaje účtu úložiště**.
   


## <a name="edit-a-storage-account"></a>Úprava účtu úložiště

Můžete upravit účet úložiště, který je používán kontejnerem svazků. Pokud upravíte účet úložiště, který se právě používá, je k dispozici pouze přístupové klávesy pro účet úložiště. Můžete dodat nový přístupový klíč k úložišti a uložit aktualizované nastavení.

#### <a name="to-edit-a-storage-account"></a>Úprava účtu úložiště

1. Přejděte do služby Správce zařízení StorSimple. V části **Konfigurace** klikněte na **Přihlašovací údaje účtu úložiště**.

    ![Přihlašovací údaje účtu úložiště 2](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. V okně **přihlašovací údaje účtu úložiště** vyberte v seznamu přihlašovací údaje účtu úložiště možnost a klikněte na tu, kterou chcete upravit. 

3. Můžete upravit výběr **Povolit protokol SSL** . Můžete také kliknout na **Další...** a pak vybrat **synchronizační přístupový klíč pro otočení** přístupových klíčů k účtu úložiště. Další informace o tom, jak provést střídání klíčů, najdete v tématu [střídání klíčů účtů úložiště](#key-rotation-of-storage-accounts) . Po úpravě nastavení klikněte na **Uložit**. 

    ![Uložit upravené přihlašovací údaje účtu úložiště](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. Po zobrazení výzvy k potvrzení klikněte na **Ano**. 

    ![Potvrdit změny](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

Nastavení budou aktualizována a uložena pro váš účet úložiště. 

## <a name="delete-a-storage-account"></a>Odstranění účtu úložiště

> [!IMPORTANT]
> Účet úložiště můžete odstranit jenom v případě, že ho nepoužívá kontejner svazků. Pokud se v kontejneru svazků používá účet úložiště, odstraňte nejprve kontejner svazků a pak odstraňte přidružený účet úložiště.

#### <a name="to-delete-a-storage-account"></a>Odstranění účtu úložiště

1. Přejděte do služby Správce zařízení StorSimple. V části **Konfigurace** klikněte na **Přihlašovací údaje účtu úložiště**.

2. V tabulkovém seznamu účtů úložiště najeďte myší na účet, který chcete odstranit. Kliknutím pravým tlačítkem myši zavolejte kontextovou nabídku a kliknete na **Odstranit**.

    ![Odstranit přihlašovací údaje účtu úložiště](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. Po zobrazení výzvy k potvrzení klikněte na **Ano** , aby se odstraňování pokračovalo. Tabulkový výpis bude aktualizován tak, aby odrážel změny.

    ![Potvrzení odstranění](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>Střídání klíčů účtů úložiště

Z bezpečnostních důvodů je střídání klíčů často v datových centrech potřeba. Každé předplatné Microsoft Azure může mít jeden nebo více přidružených účtů úložiště. Přístup k těmto účtům je řízen předplatným a přístupovými klíči pro každý účet úložiště. 

Když vytváříte účet úložiště, Microsoft Azure generuje 2 512 klíče pro přístup k úložišti, které se používají k ověřování při přístupu k účtu úložiště. Pomocí dvou přístupových klíčů k úložišti můžete znovu vygenerovat klíče bez přerušení služby úložiště nebo přístupu k této službě. Klíč, který se právě používá, je *primární* klíč a záložní klíč se nazývá *sekundární* klíč. Pokud vaše zařízení Microsoft Azure StorSimple přistupuje k poskytovateli služby cloudového úložiště, musí se zadat jeden z těchto dvou klíčů.

## <a name="what-is-key-rotation"></a>Co je střídání klíčů?

Aplikace obvykle používají pro přístup k datům pouze jeden z klíčů. Po určité době můžete své aplikace přepnout na použití druhého klíče. Po přepnutí aplikací na sekundární klíč můžete vyřadit první klíč a potom vygenerovat nový klíč. Pomocí těchto dvou klíčů tímto způsobem umožníte aplikacím přístup k datům, aniž by docházelo k výpadkům.

Klíče účtu úložiště se ve službě vždycky ukládají v zašifrované podobě. Můžete je ale resetovat prostřednictvím služby StorSimple Device Manager. Služba může získat primární klíč a sekundární klíč pro všechny účty úložiště ve stejném předplatném, včetně účtů vytvořených ve službě úložiště a výchozích účtů úložiště generovaných při prvním vytvoření služby StorSimple Device Manager. Služba StorSimple Device Manager bude tyto klíče vždycky získávat z portálu Azure Classic a pak je ukládat šifrovaným způsobem.

## <a name="rotation-workflow"></a>Pracovní postup rotace

Správce Microsoft Azure může znovu vygenerovat nebo změnit primární nebo sekundární klíč přímým přístupem k účtu úložiště (prostřednictvím služby Microsoft Azure Storage). Služba StorSimple Device Manager nevidí tuto změnu automaticky.

Abyste informovali službu StorSimple Device Manager o změně, budete potřebovat přístup ke službě StorSimple Device Manager, přístupu k účtu úložiště a pak primárnímu nebo sekundárnímu klíči synchronizovat (podle toho, která z nich se změnila). Služba pak získá nejnovější klíč, zašifruje klíče a odešle šifrovaný klíč do zařízení.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>Synchronizace klíčů pro účty úložiště ve stejném předplatném jako služba 
1. Přejděte do služby Správce zařízení StorSimple. V části **Konfigurace** klikněte na **Přihlašovací údaje účtu úložiště**.
2. V tabulkovém výpisu účtů úložiště klikněte na tu, kterou chcete upravit. 

    ![synchronizovat klíče](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. Klikněte na **... A pak** vyberte možnost **synchronizovat přístupový klíč k otočení**.   

    ![synchronizovat klíče 2](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. Ve službě StorSimple Device Manager musíte aktualizovat klíč, který se dříve změnil ve službě Microsoft Azure Storage. Pokud se primární přístupový klíč změnil (znovu vygeneroval), vyberte **primární** klíč. Pokud se sekundární klíč změnil, vyberte **sekundární** klíč. Klikněte na **synchronizovat klíč**.
      
      ![synchronizace klíčů 3](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

Po úspěšné synchronizaci klíče budete upozorněni.

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Synchronizace klíčů pro účty úložiště mimo předplatné služby
1. Na stránce **služby** klikněte na kartu **Konfigurovat** .
2. Klikněte na **Přidat/upravit účty úložiště**.
3. V dialogovém okně udělejte toto:
   
   1. Vyberte účet úložiště s přístupovým klíčem, který chcete aktualizovat.
   2. Je potřeba aktualizovat přístupový klíč úložiště ve službě StorSimple Device Manager. V takovém případě můžete zobrazit přístupový klíč k úložišti. Do pole **přístupový klíč účtu úložiště** zadejte nový klíč. 
   3. Uložte provedené změny. Váš přístupový klíč účtu úložiště by se teď měl aktualizovat.

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [zabezpečení StorSimple](storsimple-8000-security.md).
* Přečtěte si další informace o [používání služby StorSimple Device Manager ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).