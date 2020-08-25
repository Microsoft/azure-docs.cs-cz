---
title: 'Kurz: škálování privátního cloudu'
description: V tomto kurzu použijete Azure Portal ke škálování privátního cloudu řešení Azure VMware ve verzi Preview.
ms.topic: tutorial
ms.date: 08/21/2020
ms.openlocfilehash: dddfbddd57f3ad6b541d11c360aeadea4383044a
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750430"
---
# <a name="tutorial-scale-an-azure-vmware-solution-preview-private-cloud"></a>Kurz: škálování privátního cloudu řešení Azure VMware Preview

Abyste mohli využít Azure VMware ve verzi Preview cloudového prostředí ve verzi Preview, můžete škálovat clustery a hostitele tak, aby odrážely to, co potřebujete pro plánované úlohy. Vzhledem k tomu, že řešení Azure VMware v rámci verze Preview nepodporuje vaše místní vCenter, budete muset použít, co jste už vytvořili prostřednictvím Azure Portal.

Počet clusterů a počet hostitelů v privátním cloudu můžete škálovat podle potřeby pro zatížení vaší aplikace. Omezení výkonu a dostupnosti pro konkrétní služby se musí řešit na základě případu v cloudovém prostředí Azure VMware Preview. Omezení clusteru a hostitele v privátním cloudu jsou uvedená v [článku konceptu privátního cloudu](concepts-private-clouds-clusters.md).

V tomto kurzu použijete Azure Portal k těmto akcím:

> [!div class="checklist"]
> * Přidání clusteru do existujícího privátního cloudu
> * Přidání hostitelů do existujícího clusteru

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete privátní cloud. Pokud jste ještě nevytvořili privátní cloud, můžete pomocí [kurzu Vytvoření privátního cloudu](tutorial-create-private-cloud.md) vytvořit privátní cloud a nakonfigurovat síť pro privátní cloud VMware v Azure, aby se nastavila požadovaná virtuální síť.

## <a name="add-a-new-cluster"></a>Přidat nový cluster

1. Na stránce Přehled stávajícího privátního cloudu v části **Spravovat**vyberte **škálovat privátní cloud**. V dalším kroku vyberte **+ Přidat cluster**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="Vyberte Přidat cluster." border="true":::

1. Na stránce **Přidat cluster** pomocí posuvníku vyberte počet hostitelů. Vyberte **Uložit**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Na stránce Přidat cluster pomocí posuvníku vyberte počet hostitelů. Vyberte Uložit." border="true":::

   Spustí se nasazení nového clusteru.

## <a name="scale-a-cluster"></a>Škálování clusteru 

1. Na stránce Přehled existujícího privátního cloudu vyberte možnost **škálovat privátní cloud** a vyberte ikonu tužky pro úpravu clusteru.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Výběr možností škálování privátního cloudu v přehledu" border="true":::

1. Na stránce **Upravit cluster** pomocí posuvníku vyberte počet hostitelů. Vyberte **Uložit**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="Na stránce Upravit cluster pomocí posuvníku vyberte počet hostitelů. Vyberte Uložit." border="true":::

   Zahájí se přidání hostitelů do clusteru.

## <a name="next-steps"></a>Další kroky

Pokud požadujete jiný privátní cloud řešení Azure VMware, [vytvořte další privátní cloud](tutorial-create-private-cloud.md)za stejné požadavky na síť, clustery a hostitele.

<!-- LINKS - external-->

<!-- LINKS - internal -->
