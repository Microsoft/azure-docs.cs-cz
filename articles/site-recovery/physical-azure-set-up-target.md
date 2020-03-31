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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953905"
---
# <a name="prepare-target-vmware-to-azure"></a>Příprava cíle (VMware do Azure)

Tento článek popisuje, jak připravit prostředí Azure tak, aby začalo replikovat fyzické servery (x64) se systémem Windows nebo Linux do Azure.

## <a name="prerequisites"></a>Požadavky

Článek předpokládá:
- Vytvořili jste trezor služby Recovery Services, který chrání fyzické servery. Trezor služby Recovery Services můžete vytvořit z [webu Azure Portal](https://portal.azure.com "portál Azure").
- Máte [nastavení místního prostředí](physical-azure-disaster-recovery.md) pro replikaci fyzických serverů do Azure.

## <a name="prepare-target"></a>Připravit cíl

Po dokončení **kroku 1: Vyberte cíl ochrany** a krok **2: Připravit zdroj**, budete přejit krok **3: Cíl**

![Připravit cíl](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **Předplatné:** V rozevírací nabídce vyberte předplatné, do kterého chcete replikovat fyzické servery.
2. **Model nasazení:** Vyberte model nasazení (Klasický nebo Správce prostředků)

Na základě zvoleného modelu nasazení je spuštěno ověření, aby bylo zajištěno, že máte v cílovém předplatném alespoň jeden kompatibilní účet úložiště a virtuální síť, na které můžete replikovat fyzické servery a převzetí služeb při selhání.

Po úspěšném dokončení ověření přejděte klepnutím na tlačítko OK k dalšímu kroku.

Pokud nemáte kompatibilní účet úložiště Správce prostředků nebo virtuální síť, můžete jej vytvořit kliknutím na tlačítka **+ účet úložiště** nebo + **síť** v horní části stránky.

## <a name="next-steps"></a>Další kroky
[Konfigurace nastavení replikace](vmware-azure-set-up-replication.md).
