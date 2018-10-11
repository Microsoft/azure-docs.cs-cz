---
title: Nastavit procesový server v Azure pro virtuální počítač VMware a fyzických serverů navrácení služeb po obnovení pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje, jak nastavit procesový server v Azure a navrátit služby po obnovení virtuálních počítačů Azure do VMware.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 10/10/2018
ms.author: raynew
ms.openlocfilehash: a54fe3099854e6e5200a50ec6ef5db1401a223bf
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077608"
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>Nastavit procesový server v Azure pro navrácení služeb po obnovení

Po převzetí služeb při selhání virtuálních počítačů VMware nebo fyzických serverů do Azure s využitím [Site Recovery](site-recovery-overview.md), které může selhat zpět do místní lokality a až bude znovu spuštěn a pracuje. Aby bylo možné po navrácení služeb po obnovení, budete muset nastavit dočasný procesní server v Azure, pro zpracování replikace z Azure do místní. Po dokončení navrácení služeb po obnovení můžete odstranit tento virtuální počítač.

## <a name="before-you-start"></a>Než začnete

Další informace o [opětovného nastavování ochrany](vmware-azure-reprotect.md) a [navrácení služeb po obnovení](vmware-azure-failback.md) procesu.

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-in-azure"></a>Nasazení procesového serveru v Azure

1. V trezoru > **infrastruktura Site Recovery**> **spravovat** > **konfigurační servery**, vyberte konfigurační server.
2. Na stránce serveru, klikněte na tlačítko **+ procesového serveru**
3. V **přidat procesový server** stránku a vybrat, zda chcete nasadit procesový server v Azure.
4. Zadejte nastavení Azure, včetně předplatného, které použijete pro převzetí služeb při selhání, skupinu prostředků, oblast Azure používá pro převzetí služeb při selhání a virtuální síť, ve kterém jsou umístěné virtuální počítače Azure. Pokud jste použili více sítí Azure, je třeba procesový server v každé z nich.

  ![Přidat položku Galerie procesového serveru](./media/vmware-azure-set-up-process-server-azure/add-ps-page-1.png)

4. V **název serveru**, **uživatelské jméno**, a **heslo**, zadejte název k procesového serveru a přihlašovací údaje, které se dají přiřadit oprávnění správce na serveru.
5. Zadejte účet úložiště pro disky virtuálních počítačů serveru, podsíť, ve kterém bude umístěn na procesovém serveru virtuálního počítače a IP adresu serveru, která bude přiřazena při spuštění virtuálního počítače.
6. Klikněte na tlačítko **OK** tlačítko Zahájit nasazení procesového serveru virtuálního počítače.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Registrace procesového serveru (běžícího v Azure) na konfiguračním serveru (v místním)

Po vytvoření a spuštění je procesový server VM, budete muset zaregistrovat ji pomocí místní konfigurační server, následujícím způsobem:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


