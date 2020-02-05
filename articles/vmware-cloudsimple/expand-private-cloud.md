---
title: Rozšíření privátního cloudu řešení Azure VMware Solutions (AVS)
description: Popisuje postup rozšíření existujícího privátního cloudu služby AVS za účelem přidání kapacity v existujícím nebo novém clusteru.
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3286b7537056a6c2f282533aa629ebbe47612690
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025295"
---
# <a name="expand-an-avs-private-cloud"></a>Rozšíření privátního cloudu služby AVS

AVS nabízí flexibilitu pro dynamické rozšíření privátního cloudu služby AVS. Můžete začít s menší konfigurací a pak je rozšířit podle potřeby větší kapacity. Nebo můžete vytvořit privátní cloud služby AVS na základě aktuálních potřeb a pak rozbalovat růst spotřeby.

Privátní cloud služby AVS se skládá z jednoho nebo více clusterů vSphere. Každý cluster může mít 3 až 16 uzlů. Při rozšiřování privátního cloudu služby AVS můžete přidat uzly do stávajícího clusteru nebo vytvořit nový cluster. Chcete-li rozšířit existující cluster, musí být další uzly stejného typu (SKU) jako stávající uzly. Pro vytvoření nového clusteru může být uzly jiného typu. Další informace o omezeních privátního cloudu služby AVS najdete v části omezení v článku [Přehled privátního](cloudsimple-private-cloud.md) cloudu služby AVS.

Privátní cloud služby AVS se vytvoří **s výchozím** datacentrem na vCenter. Každé datové centrum slouží jako entita správy na nejvyšší úrovni. V případě nového clusteru funkce AVS nabízí možnost přidání do stávajícího datového centra nebo vytvoření nového datacentra.

V rámci nové konfigurace clusteru služba AVS konfiguruje infrastrukturu VMware. Mezi tato nastavení patří nastavení úložiště pro síti vSAN diskové skupiny, VMware high availability a Distributed Resource Scheduler (DRS).

Privátní cloud služby AVS lze několikrát rozšířit. Rozšíření se dá udělat jenom v případě, že zůstanete v rámci celkových omezení uzlu. Pokaždé, když rozbalíte privátní cloud služby AVS, přidáte ho do existujícího clusteru nebo vytvoříte nový.

## <a name="before-you-begin"></a>Než začnete

Aby bylo možné rozšířit svůj privátní cloud služby AVS, musí být uzly zřízené. Další informace o zřizovacích uzlech najdete v článku [zřízení uzlů pro řešení VMware pomocí služby AVS-Azure](create-nodes.md) . Pro vytvoření nového clusteru musíte mít aspoň tři dostupné uzly stejné SKU.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="expand-an-avs-private-cloud"></a>Rozšíření privátního cloudu služby AVS

1. [Přístup k portálu AVS](access-cloudsimple-portal.md).

2. Otevřete stránku **prostředky** a vyberte privátní cloud služby AVS, pro který chcete rozšíření rozšířit.

3. V části Souhrn klikněte na **Rozbalit**.

    ![Rozbalit privátní cloud AVS](media/resources-expand-private-cloud.png)

4. Vyberte, jestli chcete rozšířit svůj existující cluster, nebo vytvořte nový cluster vSphere. Při provádění změn se aktualizovaly souhrnné informace na stránce.

    * Pokud chcete rozšířit svůj existující cluster, klikněte na **Rozbalit existující cluster**. Vyberte cluster, který chcete rozšířit, a zadejte počet uzlů, které chcete přidat. Každý cluster může mít maximálně 16 uzlů.
    * Chcete-li přidat nový cluster, klikněte na tlačítko **vytvořit nový cluster**. Zadejte název clusteru. Vyberte existující datové centrum nebo zadejte název pro vytvoření nového datacentra. Vyberte typ uzlu. Při vytváření nového clusteru vSphere můžete zvolit jiný typ uzlu, ale ne při rozšiřování stávajícího clusteru vSphere. Vyberte počet uzlů. Každý nový cluster musí mít alespoň tři uzly.

    ![Rozbalte privátní cloud AVS – přidat uzly](media/resources-expand-private-cloud-add-nodes.png)

5. Kliknutím na **Odeslat** rozbalte privátní cloud AVS.

## <a name="next-steps"></a>Další kroky

* [Využití virtuálních počítačů VMware v Azure](quickstart-create-vmware-virtual-machine.md)
* Další informace o [privátních cloudech](cloudsimple-private-cloud.md) pro funkci AVS