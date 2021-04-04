---
title: Volba nástroje pro přesunutí prostředků Azure napříč oblastmi
description: Projděte si možnosti a nástroje pro přesun prostředků Azure napříč oblastmi.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 1b233028b52175842c73660ff116ac592d8296e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90603352"
---
# <a name="choose-a-tool-for-moving-azure-resources"></a>Volba nástroje pro přesunutí prostředků Azure

Prostředky můžete v rámci Azure přesunout následujícím způsobem:

- **Přesunutí prostředků mezi oblasti**: přesunout prostředek z centra pro zpracování prostředků, nebo v rámci skupiny prostředků. 
- **Přesunutí prostředků mezi skupinami prostředků/předplatnými**: přesun ze skupiny prostředků. 
- **Přesunutí prostředků mezi cloudy Azure**: službu Azure Site Recovery můžete použít k přesunu prostředků mezi veřejnými a státními cloudy.
- **Přesunutí prostředků mezi zónami dostupnosti ve stejné oblasti**: pomocí služby Azure Site Recovery přesunete prostředky mezi zónami dostupnosti ve stejné oblasti Azure.


## <a name="compare-move-tools"></a>Porovnat nástroje pro přesun

**Nástroj** | **Kdy je použít** | **Další informace**
--- | --- | ---
**Přesunout do skupiny prostředků** | Přesuňte prostředky do jiné skupiny prostředků nebo předplatného nebo napříč oblastmi.<br/><br/> Pokud přecházíte mezi oblastmi, ve skupině prostředků vyberte prostředky, které chcete přesunout, a pak se přesunete do centra pro podávání prostředků, abyste ověřili závislosti a přesunuli prostředky do cílové oblasti. | [Přesuňte prostředky do jiné skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md).<br/><br/> [Přesune prostředky do jiné oblasti ze skupiny prostředků](move-region-within-resource-group.md).
**Přesun z centra pro postěhovací prostředky** | Přesunutí prostředků mezi oblasti. <br/><br/> Můžete přejít do cílové oblasti nebo do konkrétní zóny dostupnosti nebo do skupiny dostupnosti v rámci cílové oblasti. | [Přesuňte prostředky mezi oblasti v centru]()pro zpracování prostředků.
**Přesunutí virtuálních počítačů pomocí Site Recovery** | Slouží k přesunu virtuálních počítačů Azure mezi státní správou a veřejnými cloudy.<br/><br/> Použijte, pokud chcete přesunout virtuální počítače mezi zónami dostupnosti ve stejné oblasti. |[Přesuňte prostředky mezi státními a veřejnými cloudy](../site-recovery/region-move-cross-geos.md), [přesuňte prostředky do zón dostupnosti ve stejné oblasti](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

## <a name="next-steps"></a>Další kroky

[Přečtěte si další informace](about-move-process.md) o komponentách Resource stěhovací a procesu přesunutí.
