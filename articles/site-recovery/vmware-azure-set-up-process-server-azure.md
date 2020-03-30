---
title: Nastavení procesního serveru VMware/fyzické navrácení služeb po obnovení v azure site recovery
description: Tento článek popisuje, jak nastavit procesní server v Azure, aby navrácení služeb po službě Azure virtuální počítače na VMware.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: ramamill
ms.openlocfilehash: 16214fa07b14c8db2f32fa2c69739b7fa3dc1907
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083958"
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>Nastavení procesového serveru v Azure pro účely navrácení služeb po obnovení

Po převzetí služeb při selhání virtuálních počítačů VMware nebo fyzických serverů do Azure pomocí [site recovery](site-recovery-overview.md), můžete je vypovědět zpět na místní web, když je znovu spuštěna. Aby bylo možné navrácení služeb po službě, musíte nastavit dočasný proces server v Azure, pro zpracování replikace z Azure do místního. Tento virtuální virtuální ms můžete odstranit po dokončení navrácení služeb po selhání.

## <a name="before-you-start"></a>Než začnete

Další informace o [procesu opětovnéochrany](vmware-azure-reprotect.md) a [navrácení služeb po obnovení.](vmware-azure-failback.md)

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]


## <a name="deploy-a-process-server-in-azure"></a>Nasazení procesního serveru v Azure

1. V úschovně > **infrastruktury**> obnovení sítě**Spravujte** > **konfigurační servery**vyberte konfigurační server.
2. Na stránce serveru klikněte na **+ Procesní server**
3. Na stránce **Přidat procesní server** a vyberte nasazení procesního serveru v Azure.
4. Zadejte nastavení Azure, včetně předplatného používaného pro převzetí služeb při selhání, skupiny prostředků, oblasti Azure používané pro převzetí služeb při selhání a virtuální sítě, ve které jsou virtuální počítače Azure umístěny. Pokud jste použili více sítí Azure, budete potřebovat proces server v každé z nich.

   ![Přidání položky galerie procesního serveru](./media/vmware-azure-set-up-process-server-azure/add-ps-page-1.png)

4. V **poli Název serveru**, Uživatelské **jméno**a **Heslo**zadejte název procesu serveru a pověření, kterým budou na serveru přiřazena oprávnění správce.
5. Zadejte účet úložiště, který se má použít pro disky virtuálních počítačů serveru, podsíť, ve které bude virtuální modul procesního serveru umístěn, a adresu IP serveru, která bude přiřazena při spuštění virtuálního počítače.
6. Klepnutím na tlačítko **OK** spusťte nasazení virtuálního virtuálního soudu procesního serveru. Procesový server bude nasazen na Standard_A8_v2 skladové položky. Ujistěte se, že tato stoka virtuálních zařízení je k dispozici pro vaše předplatné.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Registrace procesního serveru (spuštěného v Azure) na konfigurační server (spuštěný místně)

Po spuštění virtuálního počítače procesního serveru je potřeba jej zaregistrovat na místním konfiguračním serveru takto:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


