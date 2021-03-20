---
title: Nastavení cílového prostředí pro fyzické servery v Azure Site Recovery
description: Tento článek popisuje, jak nastavit cílové prostředí Azure pro zotavení po havárii fyzických serverů pomocí Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 685f04b27cd315b04ef5c45e155e825eebe2747b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "73953905"
---
# <a name="prepare-target-vmware-to-azure"></a>Připravit cíl (z VMware do Azure)

Tento článek popisuje, jak připravit prostředí Azure tak, aby se spustila replikace fyzických serverů (x64) se systémem Windows nebo Linux do Azure.

## <a name="prerequisites"></a>Předpoklady

Článek předpokládá:
- Vytvořili jste Recovery Services trezor pro ochranu fyzických serverů. Z [Azure Portal](https://portal.azure.com "portál Azure")můžete vytvořit trezor Recovery Services.
- Máte [nastavené místní prostředí](physical-azure-disaster-recovery.md) pro replikaci fyzických serverů do Azure.

## <a name="prepare-target"></a>Připravit cíl

Po dokončení **kroku 1: Vyberte cíl ochrany** a **Krok 2: připravit zdroj**. provedete to **krokem 3: Target**

![Připravit cíl](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **Předplatné:** V rozevírací nabídce vyberte předplatné, do kterého chcete replikovat fyzické servery.
2. **Model nasazení:** Vyberte model nasazení (Classic nebo Správce prostředků).

Na základě zvoleného modelu nasazení se spustí ověřování, aby se zajistilo, že budete mít aspoň jeden kompatibilní účet úložiště a virtuální síť v cílovém předplatném pro replikaci a převzetí služeb při selhání fyzických serverů na.

Po úspěšném ověření se kliknutím na tlačítko OK přejděte k dalšímu kroku.

Pokud nemáte kompatibilní účet úložiště Správce prostředků nebo virtuální síť, můžete ho vytvořit kliknutím na tlačítko **+ účet úložiště** nebo **+ síť** v horní části stránky.

## <a name="next-steps"></a>Další kroky
[Nakonfigurujte nastavení replikace](vmware-azure-set-up-replication.md).
