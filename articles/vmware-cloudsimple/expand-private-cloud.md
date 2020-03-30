---
title: Rozšíření řešení Azure VMware pomocí cloudového privátního cloudu
description: Popisuje, jak rozšířit existující CloudSimple Private Cloud přidat kapacitu v existujícínebo nový cluster
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a82ba1b433e62ed1c4b72b8e942d4ade29f26c4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025295"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>Rozšíření cloudového jednoduchého privátního cloudu

CloudSimple poskytuje flexibilitu dynamicky rozbalovat privátní cloud. Můžete začít s menší konfigurací a potom rozbalit, jak budete potřebovat vyšší kapacitu. Nebo můžete vytvořit privátní cloud na základě aktuálních potřeb a pak rozšířit s rostoucí spotřebou.

Privátní cloud se skládá z jednoho nebo více clusterů vSphere. Každý cluster může mít 3 až 16 uzlů.  Při rozbalení privátního cloudu přidáte uzly do existujícího clusteru nebo vytvoříte nový cluster. Chcete-li rozbalit existující cluster, musí být další uzly stejného typu (SKU) jako existující uzly. Pro vytvoření nového clusteru mohou být uzly jiného typu. Další informace o limitech privátního cloudu najdete v části Limity v článku [přehled privátního cloudu CloudSimple.](cloudsimple-private-cloud.md)

Privátní cloud se vytvoří s výchozím **datovým centrem** v centru.  Každé datové centrum slouží jako entita pro správu nejvyšší úrovně.  Pro nový cluster CloudSimple poskytuje možnost přidání do existujícího datového centra nebo vytvoření nového datového centra.

CloudSimple v rámci nové konfigurace clusteru konfiguruje infrastrukturu VMware.  Nastavení zahrnují nastavení úložiště pro skupiny disků vSAN, vysokou dostupnost vmware a plánovač distribuovaných prostředků (DRS).

Privátní cloud lze rozšířit vícekrát. Rozšíření lze provést pouze v případě, že zůstanete v rámci celkových limitů uzlů. Pokaždé, když rozbalíte privátní cloud, přidáte do existujícího clusteru nebo vytvoříte nový.

## <a name="before-you-begin"></a>Než začnete

Před rozšířením privátního cloudu je nutné zřídit uzly.  Další informace o zřizování uzlů najdete v článku [Zřízení uzlů pro řešení VMware podle CloudSimple – Azure.](create-nodes.md)  Pro vytvoření nového clusteru musíte mít alespoň tři dostupné uzly stejné skladové položky.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .

## <a name="expand-a-private-cloud"></a>Rozšíření privátního cloudu

1. [Přístup k portálu CloudSimple](access-cloudsimple-portal.md).

2. Otevřete stránku **Prostředky** a vyberte privátní cloud, pro který chcete rozbalit.

3. V souhrnné části klikněte na **Rozbalit**.

    ![Rozšíření privátního cloudu](media/resources-expand-private-cloud.png)

4. Zvolte, zda chcete rozbalit stávající cluster nebo vytvořit nový cluster vSphere. Při provádění změn se aktualizují souhrnné informace na stránce.

    * Chcete-li rozšířit existující cluster, klepněte na tlačítko **Rozbalit existující cluster**. Vyberte cluster, který chcete rozbalit, a zadejte počet uzlů, které chcete přidat. Každý cluster může mít maximálně 16 uzlů.
    * Chcete-li přidat nový cluster, klepněte na tlačítko **Vytvořit nový cluster**. Zadejte název clusteru. Vyberte existující datové centrum nebo zadejte název pro vytvoření nového datového centra. Zvolte typ uzlu. Při vytváření nového clusteru vSphere můžete zvolit jiný typ uzlu, ale ne při rozbalení existujícího clusteru vSphere. Vyberte počet uzlů. Každý nový cluster musí mít alespoň tři uzly.

    ![Rozšíření privátního cloudu – přidání uzlů](media/resources-expand-private-cloud-add-nodes.png)

5. Kliknutím na **Odeslat** rozbalte privátní cloud.

## <a name="next-steps"></a>Další kroky

* [Využívání virtuálních počítačů VMware v Azure](quickstart-create-vmware-virtual-machine.md)
* Další informace o [privátních cloudech](cloudsimple-private-cloud.md)