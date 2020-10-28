---
title: Kurz – škálování privátního cloudu
description: V tomto kurzu použijete Azure Portal ke škálování privátního cloudu řešení Azure VMware.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: d49d973cc6d97280dc0c7ea6681f2602b871e1ba
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791235"
---
# <a name="tutorial-scale-an-azure-vmware-solution-private-cloud"></a>Kurz: škálování privátního cloudu řešení Azure VMware

Aby bylo možné využít privátní cloudové prostředí Azure VMware, škálovat clustery a hostitele tak, aby odrážely to, co potřebujete pro plánované úlohy. Clustery a hostitele v privátním cloudu můžete škálovat podle potřeby pro úlohy vaší aplikace. Omezení výkonu a dostupnosti pro konkrétní služby by se měla řešit na základě případu. Omezení clusteru a hostitele jsou uvedená v článku [konceptu privátního cloudu](concepts-private-clouds-clusters.md) .

V tomto kurzu použijete Azure Portal k těmto akcím:

> [!div class="checklist"]
> * Přidání clusteru do existujícího privátního cloudu
> * Přidání hostitelů do existujícího clusteru

## <a name="prerequisites"></a>Předpoklady

Privátní cloud pro dokončení tohoto kurzu. Pokud jste ještě nevytvořili privátní cloud, vytvořte ho pomocí [kurzu Vytvoření privátního cloudu](tutorial-create-private-cloud.md) . Nakonfigurujte síť pro privátní cloud VMware v Azure a nastavte požadovanou virtuální síť.

## <a name="add-a-new-cluster"></a>Přidat nový cluster

1. Na stránce Přehled stávajícího privátního cloudu v části **Spravovat** vyberte **škálovat privátní cloud** . V dalším kroku vyberte **+ Přidat cluster** .

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="Vyberte Přidat cluster." border="true":::

1. Na stránce **Přidat cluster** pomocí posuvníku vyberte počet hostitelů. Vyberte **Uložit** .

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Vyberte Přidat cluster." border="true":::

   Spustí se nasazení nového clusteru.

## <a name="scale-a-cluster"></a>Škálování clusteru 

1. Na stránce Přehled existujícího privátního cloudu vyberte možnost **škálovat privátní cloud** a vyberte ikonu tužky pro úpravu clusteru.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Vyberte Přidat cluster." border="true":::

1. Na stránce **Upravit cluster** pomocí posuvníku vyberte počet hostitelů. Vyberte **Uložit** .

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="Vyberte Přidat cluster." border="true":::

   Zahájí se přidání hostitelů do clusteru.

## <a name="next-steps"></a>Další kroky

Pokud požadujete jiný privátní cloud řešení Azure VMware, [vytvořte další privátní cloud](tutorial-create-private-cloud.md)za stejné požadavky na síť, cluster a omezení hostitelů.

<!-- LINKS - external-->

<!-- LINKS - internal -->
