---
title: Připravit cílové prostředí pro replikaci VMware do Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jak připravit cílové prostředí Azure pro replikaci virtuálních počítačů VMware do Azure.
services: site-recovery
author: bsiva
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: bsiva
ms.openlocfilehash: 4b428dff1cebf353cc081696649494e6e4ec9b92
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37921106"
---
# <a name="prepare-the-target-environment-for-vmware-replication-to-azure"></a>Připravit cílové prostředí pro replikaci VMware do Azure

Tento článek popisuje, jak připravit cílové prostředí Azure ke spuštění replikace virtuálních počítačů VMware do Azure.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá:
- Vytvoříte trezor služby Recovery Services k ochraně virtuálních počítačů VMware. Můžete vytvořit z trezoru služby Recovery Services [webu Azure portal](http://portal.azure.com "webu Azure portal").
- Máte [nastavení v místním prostředí](vmware-azure-set-up-source.md) pro replikaci virtuálních počítačů VMware do Azure.

## <a name="prepare-target"></a>Připravit cíl

Po dokončení **cíl ochrany krok 1: vyberte** a **krok 2: Příprava zdroje**, budete přesměrováni na **krok 3: cíl**

![Připravit cíl](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Předplatné:** z rozevírací nabídky vyberte předplatné, které chcete replikovat virtuální počítače.
2. **Model nasazení:** vyberte model nasazení (Classic nebo Resource Manager)

Na základě zvolené nasazení modelu, abyste měli jistotu, že máte aspoň jeden kompatibilní účet úložiště a virtuální sítě v cílovém předplatném pro replikaci a převzetí služeb při selhání vašeho virtuálního počítače na spuštění ověřování.

Po ověření úspěšně dokončit, klikněte na tlačítko OK přejdete k dalšímu kroku.

Pokud nemáte kompatibilní účet úložiště Resource Manageru nebo virtuální sítě, můžete vytvořit na kliknutím **+ účet úložiště** nebo **+ síť** tlačítka v horní části stránky.

## <a name="next-steps"></a>Další postup
[Konfigurace nastavení replikace](vmware-azure-set-up-replication.md).
