---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d6bad1ec584b28fee77bc654eba8a2d0c7b5df30
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889260"
---
#### <a name="to-add-a-storage-account-credential-in-the-same-azure-subscription-as-the-storsimple-device-manager-service"></a>Přidání přihlašovacích údajů účtu úložiště ve stejném předplatném Azure, jako je služba Správce zařízení StorSimple

1. Přejděte do služby Správce zařízení StorSimple. V části **Konfigurace** klikněte na **Přihlašovací údaje účtu úložiště**.

    ![Přihlašovací údaje účtu úložiště](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct1.png)

2. V okně **Přihlašovací údaje účtu úložiště** klikněte na **+ Přidat**.

    ![Přidání přihlašovacích údajů účtu úložiště](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct2.png)

3. V okně **Přidat přihlašovací údaje účtu úložiště** proveďte následující kroky:

    1. Protože přidáváte přihlašovací údaje účtu úložiště ve stejném předplatném Azure, jako je vaše služba, zkontrolujte, že je vybrána možnost **Aktuální**.

    2. V rozevíracím seznamu **Účet úložiště** vyberte existující účet úložiště.

    3. V závislosti na vybraném účtu úložiště se zobrazí **umístění** (bude zobrazeno šedě a tady ho nelze změnit).

    4. Výběrem možnosti **Povolit režim SSL** vytvořte zabezpečený kanál pro síťovou komunikaci mezi vaším zařízením a cloudem. Výběr možnosti **Povolit protokol SSL** zrušte jenom v případě, že pracujete v privátním cloudu.

        ![Okno přidání přihlašovacích údajů účtu úložiště](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct3.png)

    5. Kliknutím na **Přidat** spusťte vytváření úlohy pro přihlašovací údaje účtu úložiště. Po úspěšném vytvoření přihlašovacích údajů účtu úložiště se zobrazí oznámení.

        ![Oznámení o úspěšném vytvoření přihlašovacích údajů účtu úložiště](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct5.png)

Nově vytvořené přihlašovací údaje účtu úložiště se zobrazí v seznamu **Přihlašovací údaje účtu úložiště**.

![Seznam přihlašovacích údajů účtu úložiště](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct6.png)

