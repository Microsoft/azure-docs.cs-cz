---
title: Nastavení procesového serveru VMware/fyzické navrácení služeb po obnovení v Azure Site Recovery
description: Tento článek popisuje, jak nastavit procesový Server v Azure pro navrácení služeb po obnovení virtuálních počítačů Azure do VMware.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: ramamill
ms.openlocfilehash: 16214fa07b14c8db2f32fa2c69739b7fa3dc1907
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74083958"
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>Nastavení procesového serveru v Azure pro účely navrácení služeb po obnovení

Po převzetí služeb při selhání virtuálních počítačů VMware nebo fyzických serverů do Azure pomocí [Site Recovery](site-recovery-overview.md)můžete navrátit služby po obnovení do místní lokality, když se znovu spustí. Aby bylo možné navrácení služeb po obnovení, musíte v Azure nastavit dočasný procesový Server, abyste mohli zpracovávat replikaci z Azure do místního prostředí. Po dokončení navrácení služeb po obnovení můžete tento virtuální počítač odstranit.

## <a name="before-you-start"></a>Než začnete

Přečtěte [si další](vmware-azure-reprotect.md) informace o procesu [překlopení a navrácení služeb po obnovení](vmware-azure-failback.md) .

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]


## <a name="deploy-a-process-server-in-azure"></a>Nasazení procesového serveru v Azure

1. V trezoru > **Site Recovery infrastruktura** >  **Spravovat**  >  **konfigurační servery**, vyberte konfigurační server.
2. Na stránce Server klikněte na **+ procesový Server** .
3. Na stránce **Přidat procesový Server** a vyberte možnost nasazení procesového serveru v Azure.
4. Zadejte nastavení Azure, včetně předplatného používaného pro převzetí služeb při selhání, skupiny prostředků, oblasti Azure, která se používá pro převzetí služeb při selhání, a virtuální sítě, ve které jsou umístěné virtuální počítače Azure. Pokud jste použili více sítí Azure, budete potřebovat procesový Server v každém z nich.

   ![Přidat položku galerie procesového serveru](./media/vmware-azure-set-up-process-server-azure/add-ps-page-1.png)

4. Do pole **název serveru**, **uživatelské jméno**a **heslo**zadejte název procesového serveru a přihlašovací údaje, které budou přiřazeny oprávnění správce na serveru.
5. Zadejte účet úložiště, který se má použít pro disky virtuálních počítačů serveru, podsíť, ve které se virtuální počítač procesového serveru nachází, a IP adresu serveru, která se přiřadí při spuštění virtuálního počítače.
6. Kliknutím na tlačítko **OK** začněte NASAZOVAT virtuální počítač procesového serveru. Procesový Server se nasadí na Standard_A8_v2 SKU. Ujistěte se, že je tato skladová položka virtuálního počítače k dispozici pro vaše předplatné.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Registrace procesového serveru (běžícího v Azure) na konfiguračním serveru (běžící místně)

Až bude virtuální počítač procesového serveru v provozu, musíte ho zaregistrovat na místním konfiguračním serveru následujícím způsobem:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


