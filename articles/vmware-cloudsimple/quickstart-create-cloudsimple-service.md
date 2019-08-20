---
title: Rychlé zprovoznění řešení Azure VMware podle CloudSimple – vytvoření služby
description: Naučte se vytvářet služby CloudSimple, koupit uzly a rezervovat uzly.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b20ff261939dd97a74d27f5ec7f21eae2665f474
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69574532"
---
# <a name="quickstart---create-cloudsimple-service"></a>Rychlý Start – vytvoření služby CloudSimple

Pokud chcete začít, vytvořte řešení Azure VMware podle CloudSimple v Azure Portal.

Služba CloudSimple umožňuje využívat řešení Azure VMware od CloudSimple.  Vytvořením služby umožníte nákup uzlů, rezervace uzlů a vytváření privátních cloudů.  Službu CloudSimple přidáte v každé oblasti Azure, kde je dostupná služba CloudSimple.  Služba definuje hraniční síť řešení Azure VMware od CloudSimple.  Tato hraniční síť se používá pro služby, které zahrnují připojení VPN, ExpressRoute a Internet k privátním cloudům.

Pokud chcete přidat službu CloudSimple, musíte vytvořit podsíť brány. Podsíť brány se používá při vytváření hraniční sítě a vyžaduje blok CIDR/28. Adresní prostor podsítě brány musí být jedinečný. Nemůže se překrývat s žádnými místními adresními prostory vaší sítě ani adresním prostorem virtuální sítě Azure.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-microsoftvmwarecloudsimple-resource-provider"></a>Povolit poskytovatele prostředků Microsoft. VMwareCloudSimple

Pomocí následujících kroků povolte poskytovatele prostředků pro službu CloudSimple.

1. Vyberte **Všechny služby**.
2. Vyhledejte a vyberte **odběry**.

    ![Vybrat předplatná](media/cloudsimple-service-select-subscriptions.png)

3. Vyberte předplatné, u kterého chcete povolit službu CloudSimple.
4. Klikněte na **poskytovatelé prostředků** pro předplatné.
5. K filtrování poskytovatele prostředků použijte **Microsoft. VMwareCloudSimple** .
6. Vyberte poskytovatele prostředků **Microsoft. VMwareCloudSimple** a klikněte na **zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/cloudsimple-service-enable-resource-provider.png)

## <a name="create-the-service"></a>Vytvoření služby

1. Vyberte **Všechny služby**.
2. Vyhledejte **službu CloudSimple**.

    ![Hledat službu CloudSimple](media/create-cloudsimple-service-search.png)

3. Vyberte **CloudSimple Services**.
4. Kliknutím na **Přidat** vytvořte novou službu.

    ![Přidat službu CloudSimple](media/create-cloudsimple-service-add.png)

5. Vyberte předplatné, ve kterém chcete vytvořit službu CloudSimple.
6. Vyberte skupinu prostředků pro službu. Pokud chcete přidat novou skupinu prostředků, klikněte na **vytvořit novou**.
7. Zadejte název pro identifikaci služby.
8. Zadejte CIDR pro bránu služby. Zadejte podsíť/28, která se nepřekrývá s žádnou z vašich místních podsítí, podsítí Azure ani s plánovanými podsítěmi CloudSimple. Po vytvoření služby nemůžete změnit CIDR.

    ![Vytvoření služby CloudSimple](media/create-cloudsimple-service.png)

9. Klikněte na **OK**.

Služba je vytvořena a přidána do seznamu služeb.

## <a name="purchase-nodes"></a>Nákup uzlů

Pokud chcete nastavit kapacitu s průběžnými platbami pro prostředí privátního cloudu CloudSimple, nejdřív zřiďte uzly v Azure Portal.

1. Vyberte **Všechny služby**.
2. Vyhledejte **uzly CloudSimple**.

    ![Hledat uzly CloudSimple](media/create-cloudsimple-node-search.png)

3. Vyberte **uzly CloudSimple**.
4. Kliknutím na **Přidat** vytvořte uzly.

    ![Přidat uzly CloudSimple](media/create-cloudsimple-node-add.png)

5. Vyberte předplatné, ve kterém chcete koupit CloudSimple uzly.
6. Vyberte skupinu prostředků pro uzly. Pokud chcete přidat novou skupinu prostředků, klikněte na **vytvořit novou**.
7. Zadejte předponu pro identifikaci uzlů.
8. Vyberte umístění pro prostředky uzlu.
9. Vyberte vyhrazené umístění pro hostování prostředků uzlu.
10. Vyberte typ uzlu. Můžete zvolit [možnost CS28 nebo CS36](cloudsimple-node.md). Tato možnost zahrnuje maximální kapacitu výpočetní kapacity a paměti.
11. Vyberte počet uzlů, které se mají zřídit.
12. Vyberte **zkontrolovat + vytvořit**.
13. Zkontrolujte nastavení. Chcete-li změnit nastavení, klikněte na tlačítko **Předchozí**.
14. Vyberte **Vytvořit**.

## <a name="next-steps"></a>Další postup

* [Vytvoření privátního cloudu a konfigurace prostředí](quickstart-create-private-cloud.md)
* Další informace o [službě CloudSimple](cloudsimple-service.md)
