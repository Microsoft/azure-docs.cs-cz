---
title: Připravit cílové prostředí (VMware/fyzických prostředků do Azure) | Dokumentace Microsoftu
description: Tento článek popisuje, jak připravit cílové prostředí Azure pro replikaci virtuálních počítačů VMware a replikací fyzických serverů do Azure.
services: site-recovery
author: bsiva
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 09/28/2018
ms.author: bsiva
ms.openlocfilehash: 948812f05697362978ad041566d22977efec92a1
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434632"
---
# <a name="prepare-the-target-environment-vmwarephysical-to-azure"></a>Připravit cílové prostředí (VMware/fyzických prostředků do Azure)

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
