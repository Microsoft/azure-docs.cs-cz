---
title: "Příprava cílového (fyzické do Azure) | Microsoft Docs"
description: "Tento článek popisuje postup přípravy prostředí Azure pro zahájení replikace fyzických serverů s Windows nebo Linuxem do Azure."
services: site-recovery
author: bsiva
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: bsiva
ms.openlocfilehash: a2465bb3397a175b6ad8b8be0de933dfae1dee5b
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="prepare-target-vmware-to-azure"></a>Příprava cílového (VMware do Azure)

Tento článek popisuje postup přípravy prostředí Azure pro zahájení replikace fyzických serverů (x 64) se systémem Windows nebo Linux do Azure.

## <a name="prerequisites"></a>Požadavky

Článek předpokládá:
- Jste vytvořili trezor služeb zotavení k ochraně fyzických serverů. Můžete vytvořit trezor služeb zotavení z [portál Azure](http://portal.azure.com "portál Azure").
- Máte [instalaci prostředí místní](physical-azure-disaster-recovery.md) replikace fyzických serverů do Azure.

## <a name="prepare-target"></a>Příprava cílového

Po dokončení **cíl ochrany krok 1: vyberte** a **krok 2: Příprava zdroj**, přejdete na **krok 3: cíl**

![Příprava cílového](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **Předplatné:** z rozevírací nabídky vyberte předplatné, které chcete replikovat fyzických serverů.
2. **Model nasazení:** vyberte model nasazení (Classic nebo Resource Manager)

Podle modelu vybrané nasazení, je zajistit, že máte alespoň jeden účet kompatibilní úložiště a virtuální sítě v cílové předplatné pro replikaci a převzetí služeb při selhání vaší fyzických serverů na spusťte ověřování.

Jakmile ověřovací úspěšně dokončit, klikněte na tlačítko OK přejdete k dalšímu kroku.

Pokud nemáte účet úložiště kompatibilní Resource Manager nebo virtuální sítě, můžete vytvořit kliknutím **+ účet úložiště** nebo **+ síť** tlačítka v horní části stránky.

## <a name="next-steps"></a>Další postup
[Konfigurace nastavení replikace](vmware-azure-set-up-replication.md).
