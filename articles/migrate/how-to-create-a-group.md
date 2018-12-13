---
title: Seskupit počítače pro posouzení pomocí Azure Migrate | Dokumentace Microsoftu
description: Popisuje, jak seskupit počítače před spuštěním posouzení ve službě Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/28/2018
ms.author: raynew
ms.openlocfilehash: c11d2f22fa08417107b0eecdd902b4521410b358
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53252418"
---
# <a name="group-machines-for-assessment"></a>Seskupit počítače pro posouzení

Tento článek popisuje, jak vytvořit skupinu počítačů pro posouzení podle [Azure Migrate](migrate-overview.md). Azure Migrate posuzuje počítačů ve skupině ke kontrole, jestli jsou vhodné k migraci do Azure a poskytuje odhady velikostí a nákladů pro spuštění počítače v Azure. Pokud víte, počítače, které potřebujete migrovat společně, můžete ručně vytvořit skupinu ve službě Azure Migrate pomocí následující metody. Pokud si nejste jisti velmi o počítačích, které musí být seskupené pohromadě, můžete použít funkce vizualizace závislostí ve službě Azure Migrate k vytváření skupin. [Další informace](how-to-create-group-machine-dependencies.md)

> [!NOTE]
> Funkce vizualizace závislostí není k dispozici ve službě Azure Government.

## <a name="create-a-group"></a>Vytvoření skupiny

1. V **přehled** projekt Azure Migrate v části Správa, klikněte na tlačítko **skupiny** > **+ skupina**a zadejte název skupiny.
2. Přidejte jeden nebo více počítačů do skupiny a klikněte na tlačítko **vytvořit**.
3. Volitelně můžete vybrat spustit nové posouzení pro skupinu.

    ![Vytvoření skupiny](./media/how-to-create-a-group/create-group.png)

Po vytvoření skupiny, můžete ho upravit tak, že vyberete skupinu na **skupiny** stránky a pak přidají nebo odeberou počítače.

## <a name="next-steps"></a>Další postup

- Další informace o použití [mapování závislosti počítačů](how-to-create-group-machine-dependencies.md) k vytvoření skupiny s vysokou spolehlivostí.
- [Přečtěte si další informace](concepts-assessment-calculation.md) o tom, jak se v rámci posouzení počítají náklady.
