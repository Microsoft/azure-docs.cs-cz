---
title: Připravit cílové prostředí pro replikaci VMware do Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jak připravit cílové prostředí Azure pro replikaci virtuálních počítačů VMware do Azure.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: a6f983b08415659b9a989ebed824cddd210396e1
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233425"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>Připravit cílové prostředí pro zotavení po havárii virtuálních počítačů VMware nebo fyzických serverů do Azure

Tento článek popisuje, jak připravit cílové prostředí Azure ke spuštění replikace virtuálních počítačů VMware nebo fyzických serverů do Azure.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá:
- Vytvoříte trezor služby Recovery Services na [webu Azure portal](http://portal.azure.com "webu Azure portal") chránit zdrojové počítače
- Máte nastavený v místním prostředí replikace zdroje [virtuálních počítačů VMware](vmware-azure-set-up-source.md) nebo [fyzických serverů](physical-azure-set-up-source.md) do Azure.

## <a name="prepare-target"></a>Připravit cíl

Po dokončení **krok 1: cíl ochrany vybrat** a **krok 2: Příprava zdroje**, budete přesměrováni na **krok 3: cíl**

![Připravit cíl](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Předplatné:** z rozevírací nabídky vyberte předplatné, které chcete replikovat virtuální počítače nebo fyzické servery do.
2. **Model nasazení:** vyberte model nasazení (Classic nebo Resource Manager)

Na základě zvolené nasazení modelu, k zajištění, že máte aspoň jeden kompatibilní účet úložiště a virtuální sítě v cílovém předplatném pro replikaci a převzetí služeb při selhání virtuálního počítače nebo fyzického serveru do spuštění ověřování.

Po ověření úspěšně dokončit, klikněte na tlačítko OK přejdete k dalšímu kroku.

Pokud nemáte k dispozici kompatibilní účet úložiště Resource Manageru nebo virtuální sítě, můžete vytvořit kliknutím **+ účet úložiště** nebo **+ síť** tlačítka v horní části stránky.

## <a name="next-steps"></a>Další postup
[Konfigurace nastavení replikace](vmware-azure-set-up-replication.md).
