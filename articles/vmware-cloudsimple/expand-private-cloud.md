---
title: Rozbalte Azure řešení VMware CloudSimple privátního cloudu
description: Popisuje, jak rozšířit existující CloudSimple privátní Cloud na přidat kapacitu do stávajícího nebo nového clusteru
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 293a09c57ca95e2774e44ff4bc9f9f2c31be2f49
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333251"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>Rozbalte CloudSimple privátního cloudu

CloudSimple poskytuje možnost flexibilního rozšiřování dynamicky privátního cloudu. Můžete začít s menší konfigurace a potom rozbalte potřebujete větší kapacitu. Nebo můžete vytvořit privátní Cloud podle aktuální potřeby a potom rozbalte nárůstu využití.

Privátní Cloud se skládá z jednoho nebo více clusterech vSphere. Každý cluster může mít 3 až 16 uzlů.  Při rozšiřování privátního cloudu, přidat uzly do existujícího clusteru nebo vytvoření nového clusteru. Pokud chcete rozšířit existující cluster, další uzly musí být stejného typu (SKU) jako stávající uzly. Pro vytvoření nového clusteru, může být uzly jiného typu. Další informace o omezení privátního cloudu, omezuje najdete v části [přehled privátního cloudu CloudSimple](cloudsimple-private-cloud.md) článku.

Privátní cloud je vytvořen s výchozím **Datacenter** na serveru vCenter.  Každé datové centrum slouží jako entity správu nejvyšší úrovně.  Pro nový cluster CloudSimple poskytuje možnost přidání existujícího datacentra a vytvářet nové datové centrum.

Jako součást novou konfiguraci clusteru CloudSimple konfiguruje infrastrukturu VMware.  Nastavení zahrnuje nastavení úložiště pro skupiny disků síť vSAN, vysokou dostupnost VMware a distribuované plánovač prostředků (DRS).

Privátní Cloud můžete rozbalit více než jednou. Rozšíření lze provést pouze v případě, že se vejdete do omezení celkové uzlu. Pokaždé, když rozšiřujete privátního cloudu, přidat do existujícího clusteru nebo vytvořte novou.

## <a name="before-you-begin"></a>Než začnete

Uzly je potřeba poskytovat, než budete moct rozšířit privátního cloudu.  Další informace o zřizování uzlů najdete v tématu [zřízení uzlů pro řešení VMware podle CloudSimple - Azure](create-nodes.md) článku.  Pro vytvoření nového clusteru, musí mít aspoň tři dostupné uzly se stejným SKU.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="expand-a-private-cloud"></a>Rozbalte privátního cloudu

1. [Přístup k portálu CloudSimple](access-cloudsimple-portal.md).

2. Otevřít **prostředky** stránky a vyberte privátní Cloud, pro který chcete rozšířit.

3. V části Souhrn klikněte na tlačítko **Rozbalit**.

    ![Rozbalte privátního cloudu](media/resources-expand-private-cloud.png)

4. Zvolte, jestli se má rozšířit existující cluster nebo vytvořit nový cluster vSphere. Při provádění změn, souhrnné informace na stránce se aktualizuje.

    * Rozšíření vašeho existujícího clusteru, klikněte na tlačítko **rozbalte existující cluster**. Vyberte cluster, který chcete rozšířit a zadejte počet uzlů, které chcete přidat. Každý cluster může mít maximálně 16 uzlů.
    * Chcete-li přidat nový cluster, klikněte na tlačítko **vytvořit nový cluster**. Zadejte název clusteru. Vyberte existující datové centrum nebo zadejte název pro vytvoření nového datového centra. Zvolte typ uzlu. Při vytváření nového clusteru vSphere, ale ne v případě, že rozšiřuje existující cluster vSphere můžete typu jiný uzel. Vyberte počet uzlů. Každý nový cluster musí mít alespoň tři uzly.

    ![Rozbalte privátního cloudu – přidání uzlů](media/resources-expand-private-cloud-add-nodes.png)

5. Klikněte na tlačítko **odeslat** rozbalte privátního cloudu.

## <a name="next-steps"></a>Další postup

* [Využívání virtuálních počítačů VMware v Azure](quickstart-create-vmware-virtual-machine.md)
* Další informace o [privátních Cloudů](cloudsimple-private-cloud.md)