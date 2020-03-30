---
title: Příprava cíle replikace virtuálních počítačů VMware v azure site recovery
description: Tento článek popisuje, jak připravit cílové prostředí Azure pro replikaci virtuálních zařízení VMware do Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/03/2019
ms.author: mayg
ms.openlocfilehash: c721234f2e1d806d51d31f3466e441bf8360f6b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73693167"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>Příprava cílového prostředí pro zotavení po havárii virtuálních počítačích VMware nebo fyzických serverů do Azure

Tento článek popisuje, jak připravit cílové prostředí Azure pro zahájení replikace virtuálních počítačů VMware nebo fyzických serverů do Azure.

## <a name="prerequisites"></a>Požadavky

Článek předpokládá:
- Vytvořili jste trezor služby Recovery Services na [webu Azure Portal,](https://portal.azure.com "portál Azure") který chrání vaše zdrojové počítače.
- Máte nastavení místního prostředí pro replikaci zdrojových [virtuálních počítačů VMware](vmware-azure-set-up-source.md) nebo [fyzických serverů](physical-azure-set-up-source.md) do Azure.

## <a name="prepare-target"></a>Připravit cíl

Po dokončení **kroku 1: Vyberte cíl ochrany** a krok **2: Připravit zdroj**, budete přejit krok **3: Cíl**

![Připravit cíl](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Předplatné:** V rozevírací nabídce vyberte předplatné, do kterého chcete replikovat virtuální počítače nebo fyzické servery.
2. **Model nasazení:** Vyberte model nasazení (Klasický nebo Správce prostředků)

Na základě zvoleného modelu nasazení se spustí ověření, aby bylo zajištěno, že máte v cílovém předplatném alespoň jednu virtuální síť, na kterou můžete replikovat a přepojit na váš virtuální počítač nebo fyzický server.

Po úspěšném dokončení ověření přejděte klepnutím na tlačítko OK k dalšímu kroku.

Pokud virtuální síť nemáte, můžete ji vytvořit kliknutím na tlačítko **+ síť** v horní části stránky.

## <a name="next-steps"></a>Další kroky
[Konfigurace nastavení replikace](vmware-azure-set-up-replication.md).
