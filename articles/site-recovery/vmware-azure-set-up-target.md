---
title: Příprava cíle replikace virtuálních počítačů VMware v Azure Site Recovery
description: Tento článek popisuje, jak připravit cílové prostředí Azure pro replikaci virtuálních počítačů VMware do Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/03/2019
ms.author: mayg
ms.openlocfilehash: c721234f2e1d806d51d31f3466e441bf8360f6b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "73693167"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>Příprava cílového prostředí na zotavení po havárii virtuálních počítačů VMware nebo fyzických serverů do Azure

Tento článek popisuje, jak připravit cílové prostředí Azure na zahájení replikace virtuálních počítačů VMware nebo fyzických serverů do Azure.

## <a name="prerequisites"></a>Požadavky

Článek předpokládá:
- Vytvořili jste trezor Recovery Services v [Azure Portal](https://portal.azure.com "portál Azure") k ochraně vašich zdrojových počítačů
- Nastavili jste místní prostředí pro replikaci zdrojových [virtuálních počítačů VMware](vmware-azure-set-up-source.md) nebo [fyzických serverů](physical-azure-set-up-source.md) do Azure.

## <a name="prepare-target"></a>Připravit cíl

Po dokončení **kroku 1: Vyberte cíl ochrany** a **Krok 2: připravit zdroj**. provedete to **krokem 3: Target**

![Připravit cíl](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Předplatné:** V rozevírací nabídce vyberte předplatné, do kterého chcete replikovat virtuální počítače nebo fyzické servery.
2. **Model nasazení:** Vyberte model nasazení (Classic nebo Správce prostředků).

Na základě zvoleného modelu nasazení se spustí ověřování, aby se zajistilo, že máte v cílovém předplatném alespoň jednu virtuální síť pro replikaci a převzetí služeb při selhání virtuálního počítače nebo fyzického serveru na.

Po úspěšném ověření se kliknutím na tlačítko OK přejděte k dalšímu kroku.

Pokud nemáte virtuální síť, můžete ji vytvořit kliknutím na tlačítko **+ síť** v horní části stránky.

## <a name="next-steps"></a>Další kroky
[Nakonfigurujte nastavení replikace](vmware-azure-set-up-replication.md).
