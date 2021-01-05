---
title: Rozšíření řešení Azure VMware podle CloudSimple privátního cloudu
description: Popisuje postup rozšíření existujícího privátního cloudu CloudSimple pro přidání kapacity v existujícím nebo novém clusteru.
author: Ajayan1008
ms.author: v-hborys
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c469ea3f104e71eb80010d4a5cd421eab6184fcf
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895851"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>Rozšíření privátního cloudu CloudSimple

CloudSimple poskytuje flexibilitu pro dynamické rozšíření privátního cloudu. Můžete začít s menší konfigurací a pak je rozšířit podle potřeby větší kapacity. Nebo můžete vytvořit privátní cloud na základě aktuálních potřeb a pak rozbalit jako spotřebu.

Privátní cloud se skládá z jednoho nebo více clusterů vSphere. Každý cluster může mít 3 až 16 uzlů.  Při rozšiřování privátního cloudu přidáte uzly do stávajícího clusteru nebo vytvoříte nový cluster. Chcete-li rozšířit existující cluster, musí být další uzly stejného typu (SKU) jako stávající uzly. Pro vytvoření nového clusteru může být uzly jiného typu. Další informace o omezeních privátního cloudu najdete v části omezení v článku [Přehled privátního](cloudsimple-private-cloud.md) cloudu CloudSimple.

Ve výchozím **datacentru** na vCenter se vytvoří privátní cloud.  Každé datové centrum slouží jako entita správy na nejvyšší úrovni.  V případě nového clusteru nabízí CloudSimple možnost přidání do stávajícího datového centra nebo vytvoření nového datacentra.

V rámci nové konfigurace clusteru CloudSimple konfiguruje infrastrukturu VMware.  Mezi tato nastavení patří nastavení úložiště pro síti vSAN diskové skupiny, VMware high availability a Distributed Resource Scheduler (DRS).

Privátní cloud se dá několikrát rozšířit. Rozšíření se dá udělat jenom v případě, že zůstanete v rámci celkových omezení uzlu. Pokaždé, když rozbalíte privátní cloud, který přidáte do existujícího clusteru, nebo vytvořte nový.

## <a name="before-you-begin"></a>Než začnete

Uzly musí být zřízené, aby bylo možné rozšířit svůj privátní cloud.  Další informace o zřizovacích uzlech najdete v článku [zřízení uzlů pro řešení VMware podle CloudSimple – Azure](create-nodes.md) .  Pro vytvoření nového clusteru musíte mít aspoň tři dostupné uzly stejné SKU.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="expand-a-private-cloud"></a>Rozšíření privátního cloudu

1. [Přístup k portálu CloudSimple](access-cloudsimple-portal.md).

2. Otevřete stránku **prostředky** a vyberte privátní cloud, pro který chcete rozšíření rozšířit.

3. V části Souhrn klikněte na **Rozbalit**.

    ![Rozšíření privátního cloudu](media/resources-expand-private-cloud.png)

4. Vyberte, jestli chcete rozšířit svůj existující cluster, nebo vytvořte nový cluster vSphere. Při provádění změn se aktualizovaly souhrnné informace na stránce.

    * Pokud chcete rozšířit svůj existující cluster, klikněte na **Rozbalit existující cluster**. Vyberte cluster, který chcete rozšířit, a zadejte počet uzlů, které chcete přidat. Každý cluster může mít maximálně 16 uzlů.
    * Chcete-li přidat nový cluster, klikněte na tlačítko **vytvořit nový cluster**. Zadejte název clusteru. Vyberte existující datové centrum nebo zadejte název pro vytvoření nového datacentra. Vyberte typ uzlu. Při vytváření nového clusteru vSphere můžete zvolit jiný typ uzlu, ale ne při rozšiřování stávajícího clusteru vSphere. Vyberte počet uzlů. Každý nový cluster musí mít alespoň tři uzly.

    ![Rozbalení privátního cloudu – přidání uzlů](media/resources-expand-private-cloud-add-nodes.png)

5. Kliknutím na **Odeslat** rozbalte privátní cloud.

## <a name="next-steps"></a>Další kroky

* [Využívání virtuálních počítačů VMware v Azure](quickstart-create-vmware-virtual-machine.md)
* Další informace o [privátních cloudech](cloudsimple-private-cloud.md)