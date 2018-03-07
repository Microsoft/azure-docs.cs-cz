---
title: "Příprava cílového (VMware do Azure) | Microsoft Docs"
description: "Tento článek popisuje postup přípravy prostředí Azure pro zahájení replikace virtuálních počítačů VMware do Azure."
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhemraj
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 02/22/2018
ms.author: bsiva
ms.openlocfilehash: ec1322e95431d5fd38d8e05a66322239d3184ac0
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2018
---
# <a name="prepare-target-vmware-to-azure"></a>Příprava cílového (VMware do Azure)
> [!div class="op_single_selector"]
> * [Z VMware do Azure](./site-recovery-prepare-target-vmware-to-azure.md)
> * [Fyzické do Azure](./site-recovery-prepare-target-physical-to-azure.md)

Tento článek popisuje postup přípravy prostředí Azure pro zahájení replikace virtuálních počítačů VMware do Azure.

## <a name="prerequisites"></a>Požadavky

Článek předpokládá:
- Jste vytvořili trezor služeb zotavení k ochraně virtuálních počítačů VMware. Můžete vytvořit trezor služeb zotavení z [portál Azure](http://portal.azure.com "portál Azure").
- Máte [instalaci prostředí místní](./site-recovery-set-up-vmware-to-azure.md) k replikaci virtuálních počítačů VMware do Azure.

## <a name="prepare-target"></a>Příprava cílového

Po dokončení **cíl ochrany krok 1: vyberte** a **krok 2: Příprava zdroj**, přejdete na **krok 3: cíl**

![Příprava cílového](./media/site-recovery-prepare-target-vmware-to-azure/prepare-target-vmware-to-azure.png)

1. **Předplatné:** z rozevírací nabídky vyberte předplatné, které chcete replikovat virtuální počítače.
2. **Model nasazení:** vyberte model nasazení (Classic nebo Resource Manager)

Založená na modelu vybrané nasazení, je zajistit, že máte alespoň jeden účet kompatibilní úložiště a virtuální sítě v cílové předplatné pro replikaci a převzetí služeb při selhání virtuálního počítače do spusťte ověřování.

Jakmile ověřovací úspěšně dokončit, klikněte na tlačítko OK přejdete k dalšímu kroku.

Pokud nemáte účet úložiště kompatibilní Resource Manager nebo virtuální sítě, můžete vytvořit na kliknutím **+ účet úložiště** nebo **+ síť** tlačítka v horní části stránky.

## <a name="next-steps"></a>Další postup
[Konfigurace nastavení replikace](./site-recovery-setup-replication-settings-vmware.md).
