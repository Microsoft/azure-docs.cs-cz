---
title: "Nastavit procesní server v Azure pro virtuální počítač VMware a fyzické server navrácení služeb po obnovení s Azure Site Recovery | Microsoft Docs"
description: "Tento článek popisuje, jak nastavit procesní server v Azure, pro navrácení služeb po obnovení virtuálních počítačů Azure k VMware."
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 9d9270d8c6d2ffc5e42dfc6f94818fdace89bfb5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>Nastavit procesní server v Azure pro navrácení služeb po obnovení

Po selhání virtuální počítače VMware nebo fyzických serverů do Azure pomocí [Site Recovery](site-recovery-overview.md), vám může selhat zpět do místního serveru a když je znovu spuštěn a. Chcete-li navrácení služeb po obnovení, budete muset nastavit dočasný procesní server v Azure, pro zpracování replikace z Azure do místní. Tento virtuální počítač můžete odstranit po dokončení navrácení služeb po obnovení.

## <a name="before-you-start"></a>Než začnete

Další informace o [vytvoření](vmware-azure-reprotect.md) a [navrácení služeb po obnovení](vmware-azure-failback.md) procesu.

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-in-azure"></a>Nasadit procesový server v Azure

1. V trezoru > **infrastruktura Site Recovery**> **Mnaage** > **konfigurační servery**, vyberte konfigurační server.
2. Na stránce serveru klikněte na tlačítko **+ procesového serveru**
3. V **přidat procesový server** stránku a vyberte, které chcete nasadit procesový server v Azure.
4. Zadejte nastavení Azure, včetně předplatné použité pro převzetí služeb při selhání, skupinu prostředků, oblast Azure pro převzetí služeb při selhání a virtuální síť, ve které se nacházejí virtuální počítače Azure. Pokud jste použili více sítí Azure, je třeba procesový server v každé z nich.
5. V **název serveru**, **uživatelské jméno**, a **heslo**, zadejte název pro proces serveru a přihlašovací údaje, které budou přiřazeny oprávnění správce na serveru.
6. Zadejte účet úložiště, který se má použít pro server disky virtuálních počítačů, podsítě, ve kterém budou umístěné na procesní server virtuálního počítače a IP adresu serveru, který bude přiřazen při spuštění virtuálního počítače.
7. Klikněte na tlačítko **OK** tlačítko začít s nasazením serveru procesu virtuálního počítače.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Registrace serveru procesu (spuštění v Azure) konfigurace serveru (s místní)

Po procesový server virtuálního počítače je spuštěná, je třeba zaregistrovat ji pomocí místní konfigurační server, následujícím způsobem:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


