---
title: Připravit cílové prostředí pro replikaci VMware do Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jak připravit cílové prostředí Azure pro replikaci virtuálních počítačů VMware do Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/03/2019
ms.author: mayg
ms.openlocfilehash: e75d4b1701944e206fcf6ded2dcb6d1e1fbc77cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723779"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>Připravit cílové prostředí pro zotavení po havárii virtuálních počítačů VMware nebo fyzických serverů do Azure

Tento článek popisuje, jak připravit cílové prostředí Azure ke spuštění replikace virtuálních počítačů VMware nebo fyzických serverů do Azure.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá:
- Vytvoříte trezor služby Recovery Services na [webu Azure portal](https://portal.azure.com "webu Azure portal") chránit zdrojové počítače
- Máte nastavený v místním prostředí replikace zdroje [virtuálních počítačů VMware](vmware-azure-set-up-source.md) nebo [fyzických serverů](physical-azure-set-up-source.md) do Azure.

## <a name="prepare-target"></a>Připravit cíl

Po dokončení **krok 1: Vyberte cíl ochrany** a **krok 2: Příprava zdrojového**, budete přesměrováni na **krok 3: Cíl**

![Připravit cíl](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Předplatné:** Z rozevírací nabídky vyberte předplatné, které chcete replikovat virtuální počítače nebo fyzické servery do.
2. **Model nasazení:** Vyberte model nasazení (Classic nebo Resource Manager)

Na základě zvolené nasazení modelu, k zajištění, že máte alespoň jedné virtuální sítě v cílovém předplatném replikace a převzetí služeb při selhání virtuálního počítače nebo fyzického serveru do spuštění ověřování.

Po ověření úspěšně dokončit, klikněte na tlačítko OK přejdete k dalšímu kroku.

Pokud nemáte virtuální síť, můžete vytvořit kliknutím **+ síť** tlačítko v horní části stránky.

## <a name="next-steps"></a>Další postup
[Konfigurace nastavení replikace](vmware-azure-set-up-replication.md).
