---
title: 'Kurz: škálování privátního cloudu'
description: V tomto kurzu použijete Azure Portal ke škálování privátního cloudu řešení Azure VMware (AVS).
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 70d8c1c555badd6102f4b2547492bdea54e55783
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740287"
---
# <a name="tutorial-scale-an-azure-vmware-solution-avs-preview-private-cloud"></a>Kurz: škálování cloudového řešení Azure VMware (AVS) ve verzi Preview

Aby bylo možné využít možnosti služby AVS Preview v privátním cloudu, škálovat clustery a hostitele tak, aby odrážely to, co potřebujete pro plánované úlohy. Vzhledem k tomu, že aplikace AVS v rámci verze Preview nepodporuje vaši místní verzi vCenter, budete muset použít, co jste už vytvořili prostřednictvím Azure Portal.

Počet clusterů a počet hostitelů v privátním cloudu můžete škálovat podle potřeby pro zatížení vaší aplikace. Omezení výkonu a dostupnosti pro konkrétní služby se musí řešit na základě případu v cloudovém prostředí služby AVS Preview. Omezení clusteru a hostitele v privátním cloudu jsou uvedená v [článku konceptu privátního cloudu](concepts-private-clouds-clusters.md).

V tomto kurzu použijete Azure Portal k těmto akcím:

> [!div class="checklist"]
> * Přidání clusteru do existujícího privátního cloudu
> * Přidání hostitelů do existujícího clusteru

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete privátní cloud. Pokud jste ještě nevytvořili privátní cloud, můžete pomocí [kurzu Vytvoření privátního cloudu](tutorial-create-private-cloud.md) vytvořit privátní cloud a nakonfigurovat síť pro privátní cloud VMware v Azure, aby se nastavila požadovaná virtuální síť.

## <a name="add-a-new-cluster"></a>Přidat nový cluster

Na stránce Přehled stávajícího privátního cloudu v části **Spravovat**vyberte **škálovat privátní cloud**. V dalším kroku vyberte **+ Přidat cluster**.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="Vyberte Přidat cluster." border="true":::

Na stránce **Přidat cluster** pomocí posuvníku vyberte počet hostitelů. Vyberte **Uložit**.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Konfigurace nového clusteru privátního cloudu" border="true":::

Spustí se nasazení nového clusteru.

## <a name="scale-a-cluster"></a>Škálování clusteru 

Na stránce Přehled existujícího privátního cloudu vyberte možnost **škálovat privátní cloud** a vyberte ikonu tužky pro úpravu clusteru.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Výběr možností škálování privátního cloudu v přehledu" border="true":::

Na stránce **Upravit cluster** pomocí posuvníku vyberte počet hostitelů. Vyberte **Uložit**.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="Konfigurace nového clusteru privátního cloudu" border="true":::

Zahájí se přidání hostitelů do clusteru.

## <a name="next-steps"></a>Další kroky

Pokud vyžadujete jiný privátní cloud služby AVS, [vytvořte další privátní cloud](tutorial-create-private-cloud.md)za stejné požadavky na síť, cluster a omezení hostitele...

<!-- LINKS - external-->

<!-- LINKS - internal -->
