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
ms.openlocfilehash: 8af5cd4f961a2138f09bb3e56fccf7aa8f726dd9
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89653578"
---
# <a name="choose-a-tool-for-moving-azure-resources"></a>Volba nástroje pro přesunutí prostředků Azure

Prostředky můžete v rámci Azure přesunout následujícím způsobem:

- **Přesunutí prostředků mezi oblasti**: přesunout prostředek z centra pro zpracování prostředků, nebo v rámci skupiny prostředků. 
- **Přesunutí prostředků mezi skupinami prostředků/předplatnými**: přesun ze skupiny prostředků. 
- **Přesunutí prostředků mezi cloudy Azure**: službu Azure Site Recovery můžete použít k přesunu prostředků mezi veřejnými a státními cloudy.
- **Přesunutí prostředků mezi zónami dostupnosti ve stejné oblasti**: pomocí služby Azure Site Recovery přesunete prostředky mezi zónami dostupnosti ve stejné oblasti Azure.


## <a name="compare-move-tools"></a>Porovnat nástroje pro přesun

**Nástroj** | **Kdy je použít**
--- | --- 
**Přesunout do skupiny prostředků** | Přesuňte prostředky do jiné skupiny prostředků nebo předplatného nebo napříč oblastmi.<br/><br/> Pokud přecházíte mezi oblastmi, ve skupině prostředků vyberte prostředky, které chcete přesunout, a pak se přesunete do centra pro podávání prostředků, abyste ověřili závislosti a přesunuli prostředky do cílové oblasti.
**Přesun z centra pro postěhovací prostředky** | Přesunutí prostředků mezi oblasti. <br/><br/> Můžete přejít do cílové oblasti nebo do konkrétní zóny dostupnosti nebo do skupiny dostupnosti v rámci cílové oblasti.
**Přesunutí virtuálních počítačů pomocí Site Recovery** | Slouží k přesunu virtuálních počítačů Azure mezi státní správou a privátními cloudy.<br/><br/> Použijte, pokud chcete přesunout virtuální počítače mezi zónami dostupnosti ve stejné oblasti.

## <a name="next-steps"></a>Další kroky

[Přečtěte si další informace](about-move-process.md) o komponentách Resource stěhovací a procesu přesunutí.
