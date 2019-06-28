---
title: Namapovat fondy zdrojů na řešení VMware Azure podle CloudSimple předplatného Azure
description: Popisuje, jak mapovat fond zdrojů na řešení VMware Azure podle CloudSimple ke svému předplatnému Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: efda996e03d46a2f97d19558f7c2930b623a639e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332975"
---
# <a name="map-resource-pools-from-your-private-cloud-to-your-azure-subscription"></a>Mapování fondů zdrojů z privátního cloudu do vašeho předplatného Azure

Mapování předplatného Azure můžete namapovat fondy zdrojů z vCenter privátního cloudu do vašeho předplatného Azure. Můžete namapovat pouze předplatné, ve které jste vytvořili službu CloudSimple.  Vytvoření virtuálního počítače VMware na webu Azure Portal se nasadí virtuální počítač ve fondu zdrojů pro mapovanou.  Na portálu CloudSimple můžete zobrazit a spravovat předplatné Azure pro privátních Cloudů.

Přihlášení k odběru lze mapovat na více fondů prostředků vCenter privátního cloudu.  Budete muset namapovat fondy zdrojů každého privátního cloudu.  Pouze fondy namapovaný prostředek bude k dispozici pro vytvoření virtuálního počítače VMware na webu Azure Portal.

> [!IMPORTANT]
> Mapování fond zdrojů také mapuje všechny fondy zdrojů podřízených. Fond zdrojů nadřazené nelze mapovat již mapováno každého fondu zdrojů podřízených.

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte CloudSimple služby a privátního cloudu v rámci vašeho předplatného.  Vytvoření služby CloudSimple najdete v tématu [rychlý start – vytvoření služby](quickstart-create-cloudsimple-service.md).  Pokud potřebujete k vytvoření privátního cloudu, přečtěte si [rychlý start – konfigurace prostředí privátního cloudu](quickstart-create-private-cloud.md).

Můžete namapovat vCenter clusteru (kořenový fond zdrojů) ke svému předplatnému.  Pokud chcete vytvořit nový fond prostředků, přečtěte si téma [vytvořit fond zdrojů](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-0F6C6709-A5DA-4D38-BE08-6CB1002DD13D.html) článku na webu Dokumentace ke službě VMware.

## <a name="default-resource-group"></a>Výchozí skupiny prostředků

Vytvoření nového virtuálního počítače CloudSimple z webu Azure portal můžete vybrat skupinu prostředků.  Virtuální počítač vytvořený na serveru vCenter privátního cloudu ve fondu zdrojů pro mapovanou se nebude zobrazovat na portálu Azure portal.  Zjištěné virtuální počítač se umístí do výchozí skupiny prostředků Azure.  Můžete změnit název výchozí skupiny prostředků.

## <a name="map-azure-subscription"></a>Mapování předplatného Azure

1. Přístup [CloudSimple portál](access-cloudsimple-portal.md).

2. Otevřít **prostředky** stránky a vyberte privátní cloud, který chcete propojit.

3. Vyberte **předplatná Azure mapování**.

4. Klikněte na tlačítko **mapování předplatného Azure upravit**.

5. Pokud chcete namapovat fondy zdrojů k dispozici, vyberte na levé straně a klikněte na šipku doprava.

6. Pokud chcete odebrat mapování, vyberte na pravé straně a klikněte na šipku doleva.

    ![Předplatná Azure](media/resources-azure-mapping.png)

7. Klikněte na **OK**.

## <a name="change-default-resource-group-name"></a>Změnit výchozí název skupiny prostředků

1. Přístup [CloudSimple portál](access-cloudsimple-portal.md).

2. Otevřít **prostředky** stránky a vyberte privátní cloud, který chcete propojit.

3. Vyberte **předplatná Azure mapování**.

4. Klikněte na tlačítko **upravit** pod názvem skupiny prostředků Azure.

    ![Upravit název skupiny prostředků](media/resources-edit-resource-group-name.png)

5. Zadejte nový název pro skupinu prostředků a klikněte na tlačítko **odeslat**.

    ![Zadejte název nové skupiny prostředků](media/resources-new-resource-group-name.png)

## <a name="next-steps"></a>Další postup

* [Využívání virtuálních počítačů VMware v Azure](quickstart-create-vmware-virtual-machine.md)
* Další informace o [CloudSimple virtuálních počítačů](cloudsimple-virtual-machines.md)