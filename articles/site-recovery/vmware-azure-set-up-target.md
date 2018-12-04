---
title: Připravit cílové prostředí pro replikaci VMware do Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jak připravit cílové prostředí Azure pro replikaci virtuálních počítačů VMware do Azure.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: cbf9c1a49481de49fb8d9c6599acec7bc3600df3
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52837507"
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
