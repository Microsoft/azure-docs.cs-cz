---
title: Kurz – škálování privátního cloudu
description: V tomto kurzu použijete Azure Portal ke škálování privátního cloudu řešení Azure VMware.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 2129a3f5d04311883369b7b708689a13f07ec118
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463586"
---
# <a name="tutorial-scale-an-azure-vmware-solution-private-cloud"></a>Kurz: škálování privátního cloudu řešení Azure VMware

Aby bylo možné využít privátní cloudové prostředí Azure VMware, škálovat clustery a hostitele tak, aby odrážely to, co potřebujete pro plánované úlohy. Clustery a hostitele v privátním cloudu můžete škálovat podle potřeby pro úlohy vaší aplikace. Omezení výkonu a dostupnosti pro konkrétní služby by se měla řešit na základě případu. Omezení clusteru a hostitele jsou uvedená v článku [konceptu privátního cloudu](concepts-private-clouds-clusters.md) .

V tomto kurzu použijete Azure Portal k těmto akcím:

> [!div class="checklist"]
> * Přidání clusteru do existujícího privátního cloudu
> * Přidání hostitelů do existujícího clusteru

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu budete potřebovat existující privátní cloud. Pokud jste ještě nevytvořili privátní cloud, vytvořte ho pomocí [kurzu Vytvoření privátního cloudu](tutorial-create-private-cloud.md) . 

## <a name="add-a-new-cluster"></a>Přidat nový cluster

1. Na stránce Přehled stávajícího privátního cloudu v části **Spravovat** vyberte **škálovat privátní cloud**. V dalším kroku vyberte **+ Přidat cluster**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="Vyberte Přidat cluster." border="true":::

1. Na stránce **Přidat cluster** pomocí posuvníku vyberte počet hostitelů. Vyberte **Uložit**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Na stránce Přidat cluster pomocí posuvníku vyberte počet hostitelů. Vyberte Uložit." border="true":::

   Spustí se nasazení nového clusteru.

## <a name="scale-a-cluster"></a>Škálování clusteru 

1. Na stránce Přehled existujícího privátního cloudu vyberte možnost **škálovat privátní cloud** a vyberte ikonu tužky pro úpravu clusteru.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Výběr možností škálování privátního cloudu v přehledu" border="true":::

1. Na stránce **Upravit cluster** pomocí posuvníku vyberte počet hostitelů. Vyberte **Uložit**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="Na stránce Upravit cluster pomocí posuvníku vyberte počet hostitelů. Vyberte Uložit." border="true":::

   Začíná přidávání hostitelů do clusteru.

## <a name="next-steps"></a>Další kroky

Pokud požadujete jiný privátní cloud řešení Azure VMware, [vytvořte další privátní cloud](tutorial-create-private-cloud.md)za stejné požadavky na síť, cluster a omezení hostitelů.

<!-- LINKS - external-->

<!-- LINKS - internal -->
