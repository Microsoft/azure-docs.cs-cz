---
title: Kurz – škálování privátního cloudu
description: V tomto kurzu použijete Azure Portal ke škálování privátního cloudu řešení Azure VMware.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: e1226eb98607a34869bda1f998c7cecea2e50919
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91254410"
---
# <a name="tutorial-scale-an-azure-vmware-solution-private-cloud"></a>Kurz: škálování privátního cloudu řešení Azure VMware

Aby bylo možné využít privátní cloudové prostředí Azure VMware, škálovat clustery a hostitele tak, aby odrážely to, co potřebujete pro plánované úlohy. Počet clusterů a počet hostitelů v privátním cloudu můžete škálovat podle potřeby pro zatížení vaší aplikace. Omezení výkonu a dostupnosti pro konkrétní služby se musí řešit na základě případu v rámci vašeho privátního cloudového prostředí řešení Azure VMware. Omezení clusteru a hostitele jsou uvedená v článku [konceptu privátního cloudu](concepts-private-clouds-clusters.md) .

V tomto kurzu použijete Azure Portal k těmto akcím:

> [!div class="checklist"]
> * Přidání clusteru do existujícího privátního cloudu
> * Přidání hostitelů do existujícího clusteru

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete privátní cloud. Pokud jste ještě privátní cloud nevytvořili, vytvořte si ho pomocí [kurzu Vytvoření privátního cloudu](tutorial-create-private-cloud.md) a nakonfigurujte v Azure sítě pro privátní cloud VMware a nastavte požadovanou virtuální síť.

## <a name="add-a-new-cluster"></a>Přidat nový cluster

1. Na stránce Přehled stávajícího privátního cloudu v části **Spravovat**vyberte **škálovat privátní cloud**. V dalším kroku vyberte **+ Přidat cluster**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="Vyberte Přidat cluster." border="true":::

1. Na stránce **Přidat cluster** pomocí posuvníku vyberte počet hostitelů. Vyberte **Uložit**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Vyberte Přidat cluster." border="true":::

   Spustí se nasazení nového clusteru.

## <a name="scale-a-cluster"></a>Škálování clusteru 

1. Na stránce Přehled existujícího privátního cloudu vyberte možnost **škálovat privátní cloud** a vyberte ikonu tužky pro úpravu clusteru.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Vyberte Přidat cluster." border="true":::

1. Na stránce **Upravit cluster** pomocí posuvníku vyberte počet hostitelů. Vyberte **Uložit**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="Vyberte Přidat cluster." border="true":::

   Zahájí se přidání hostitelů do clusteru.

## <a name="next-steps"></a>Další kroky

Pokud požadujete jiný privátní cloud řešení Azure VMware, [vytvořte další privátní cloud](tutorial-create-private-cloud.md)za stejné požadavky na síť, clustery a hostitele.

<!-- LINKS - external-->

<!-- LINKS - internal -->
