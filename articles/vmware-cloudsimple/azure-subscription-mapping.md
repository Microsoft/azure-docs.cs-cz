---
title: Mapování předplatného Azure na fondy zdrojů v řešení Azure VMware podle CloudSimple
description: Popisuje, jak namapovat fond zdrojů v řešení Azure VMware tím, že se CloudSimple k vašemu předplatnému Azure.
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1bf721f35500d2ff1344996e7750c5e574f40f31
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816260"
---
# <a name="map-resource-pools-from-your-private-cloud-to-your-azure-subscription"></a>Mapování fondů prostředků z vašeho privátního cloudu na předplatné Azure

Mapování předplatného Azure umožňuje mapovat fondy zdrojů z vašeho privátního cloudu vCenter na vaše předplatné Azure. Můžete mapovat jenom předplatné, ve kterém jste službu CloudSimple vytvořili.  Vytvořením virtuálního počítače VMware z Azure Portal nasadíte virtuální počítač do mapovaného fondu zdrojů.  Na portálu CloudSimple můžete zobrazit a spravovat předplatné Azure pro privátní cloudy.

Předplatné je možné namapovat na více fondů zdrojů vCenter privátního cloudu.  Je nutné mapovat fondy zdrojů pro každý privátní cloud.  Pro vytvoření virtuálního počítače VMware z Azure Portal budou k dispozici pouze mapované fondy zdrojů.

> [!IMPORTANT]
> Mapování fondu zdrojů také mapuje všechny podřízené fondy zdrojů. Nadřazený fond zdrojů nejde namapovat, pokud už jsou namapované nějaké podřízené fondy zdrojů.

## <a name="before-you-begin"></a>Před zahájením

V tomto článku se předpokládá, že máte ve svém předplatném službu CloudSimple a privátní cloud.  Pokud chcete vytvořit službu CloudSimple, přečtěte si téma [rychlý Start – vytvoření služby](quickstart-create-cloudsimple-service.md).  Pokud potřebujete vytvořit privátní cloud, přečtěte si téma [rychlý Start – konfigurace prostředí privátního cloudu](quickstart-create-private-cloud.md).

Cluster vCenter (kořenový fond zdrojů) můžete namapovat na své předplatné.  Pokud chcete vytvořit nový fond zdrojů, přečtěte si článek o [Vytvoření fondu zdrojů](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-0F6C6709-A5DA-4D38-BE08-6CB1002DD13D.html) na webu dokumentace VMware.

## <a name="default-resource-group"></a>Výchozí skupina prostředků

Při vytvoření nového virtuálního počítače s CloudSimple z Azure Portal můžete vybrat skupinu prostředků.  Na Azure Portal se zobrazí virtuální počítač vytvořený v privátním cloudu vCenter v mapovaném fondu zdrojů.  Zjištěný virtuální počítač se umístí do výchozí skupiny prostředků Azure.  Můžete změnit název výchozí skupiny prostředků.

## <a name="map-azure-subscription"></a>Mapování předplatného Azure

1. Přístup k [portálu CloudSimple](access-cloudsimple-portal.md).

2. Otevřete stránku **prostředky** a vyberte privátní cloud, který chcete namapovat.

3. Vyberte **mapování předplatných Azure**.

4. Klikněte na **Upravit mapování předplatného Azure**.

5. Chcete-li namapovat dostupné fondy zdrojů, vyberte je na levé straně a klikněte na šipku vpravo.

6. Mapování odstraníte tak, že je vyberete vpravo a kliknete na šipku vlevo.

    ![Předplatná Azure](media/resources-azure-mapping.png)

7. Klikněte na **OK**.

## <a name="change-default-resource-group-name"></a>Změnit výchozí název skupiny prostředků

1. Přístup k [portálu CloudSimple](access-cloudsimple-portal.md).

2. Otevřete stránku **prostředky** a vyberte privátní cloud, který chcete namapovat.

3. Vyberte **mapování předplatných Azure**.

4. V části název skupiny prostředků Azure klikněte na **Upravit** .

    ![Upravit název skupiny prostředků](media/resources-edit-resource-group-name.png)

5. Zadejte nový název pro skupinu prostředků a klikněte na **Odeslat**.

    ![Zadejte název nové skupiny prostředků.](media/resources-new-resource-group-name.png)

## <a name="next-steps"></a>Další postup

* [Využití virtuálních počítačů VMware v Azure](quickstart-create-vmware-virtual-machine.md)
* Další informace o [virtuálních počítačích s CloudSimple](cloudsimple-virtual-machines.md)