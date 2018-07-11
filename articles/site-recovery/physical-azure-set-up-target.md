---
title: Připravit cíl (fyzické prostředky do Azure) | Dokumentace Microsoftu
description: Tento článek popisuje, jak připravit prostředí Azure ke spuštění replikace fyzických serverů s Windows nebo Linuxem do Azure.
services: site-recovery
author: bsiva
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: bsiva
ms.openlocfilehash: 370d245e39b848acade18d0e73f60a3246737629
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37915516"
---
# <a name="prepare-target-vmware-to-azure"></a>Připravit cíl (z VMware do Azure)

Tento článek popisuje, jak připravit prostředí Azure ke spuštění replikace fyzických serverů (x 64) s Windows nebo Linuxem do Azure.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá:
- Vytvoříte trezor služby Recovery Services pro ochranu fyzických serverů. Můžete vytvořit z trezoru služby Recovery Services [webu Azure portal](http://portal.azure.com "webu Azure portal").
- Máte [nastavení v místním prostředí](physical-azure-disaster-recovery.md) pro replikaci fyzických serverů do Azure.

## <a name="prepare-target"></a>Připravit cíl

Po dokončení **cíl ochrany krok 1: vyberte** a **krok 2: Příprava zdroje**, budete přesměrováni na **krok 3: cíl**

![Připravit cíl](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **Předplatné:** z rozevírací nabídky vyberte předplatné, které chcete replikovat fyzické servery.
2. **Model nasazení:** vyberte model nasazení (Classic nebo Resource Manager)

Na základě zvolené nasazení modelu, abyste měli jistotu, že máte aspoň jeden kompatibilní účet úložiště a virtuální sítě v cílovém předplatném pro replikaci a převzetí služeb při selhání vaší fyzické servery do spuštění ověřování.

Po ověření úspěšně dokončit, klikněte na tlačítko OK přejdete k dalšímu kroku.

Pokud nemáte k dispozici kompatibilní účet úložiště Resource Manageru nebo virtuální sítě, můžete vytvořit kliknutím **+ účet úložiště** nebo **+ síť** tlačítka v horní části stránky.

## <a name="next-steps"></a>Další postup
[Konfigurace nastavení replikace](vmware-azure-set-up-replication.md).
