---
title: Rychlé zprovoznění řešení Azure VMware (AVS) – vytvoření služby
description: Naučte se vytvořit službu AVS, koupit uzly a rezervovat uzly.
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e7eb414e51ca38f524ab83bfb51f80f771524287
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024428"
---
# <a name="quickstart---create-azure-vmware-solutions-avs-service"></a>Rychlý Start – vytvoření služby pro Azure VMware Solutions (AVS)

Začněte tím, že v Azure Portal vytvoříte řešení Azure VMware (AVS).

## <a name="vmware-solutions-avs---service-overview"></a>Řešení VMware (AVS) – Přehled služby

Služba AVS umožňuje využívat řešení Azure VMware pomocí služby AVS. Vytvoření služby umožňuje zřídit uzly, rezervovat uzly a vytvořit privátní cloudy služby AVS. Do každé oblasti Azure, ve které je služba AVS dostupná, přidáte službu AVS. Služba prostřednictvím služby AVS definuje hraniční síť řešení Azure VMware. Tato hraniční síť se používá pro služby, které zahrnují připojení VPN, ExpressRoute a Internet k privátním cloudům služby AVS.

Chcete-li přidat službu AVS, je nutné vytvořit podsíť brány. Podsíť brány se používá při vytváření hraniční sítě a vyžaduje blok CIDR/28. Adresní prostor podsítě brány musí být jedinečný. Nemůže se překrývat s žádnými místními adresními prostory vaší sítě ani adresním prostorem virtuální sítě Azure.

## <a name="before-you-begin"></a>Než začnete

Přidělte bloku CIDR/28 pro podsíť brány. Pro každou službu AVS se vyžaduje podsíť brány a je jedinečná pro oblast, ve které je vytvořená. Podsíť brány se používá pro řešení Azure VMware pomocí služby AVS hraniční sítě a vyžaduje blok CIDR/28. Adresní prostor podsítě brány musí být jedinečný. Nesmí se překrývat s žádnou sítí, která komunikuje s prostředím AVS. Sítě, které komunikují se službou AVS, zahrnují místní sítě a virtuální sítě Azure.

Kontrola [požadavků na síť](cloudsimple-network-checklist.md). 

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Vytvoření služby

1. Vyberte **Všechny služby**.
2. Vyhledejte **službu AVS**.

    ![Hledat ve službě AVS](media/create-cloudsimple-service-search.png)

3. Vyberte **služby AVS**.
4. Kliknutím na **Přidat** vytvořte novou službu.

    ![Přidat službu AVS](media/create-cloudsimple-service-add.png)

5. Vyberte předplatné, ve kterém chcete vytvořit službu AVS.
6. Vyberte skupinu prostředků pro službu. Pokud chcete přidat novou skupinu prostředků, klikněte na **vytvořit novou**.
7. Zadejte název pro identifikaci služby.
8. Zadejte CIDR pro bránu služby. Zadejte podsíť/28, která se nepřekrývá s žádnou z vašich místních podsítí, podsítí Azure ani s plánovanými podsítěmi služby AVS. Po vytvoření služby nemůžete změnit CIDR.

    ![Vytvoření služby AVS](media/create-cloudsimple-service.png)

9. Klikněte na **OK**.

Služba je vytvořena a přidána do seznamu služeb.

## <a name="provision-nodes"></a>Zřizování uzlů

Pokud chcete nastavit kapacitu s průběžnými platbami pro prostředí privátního cloudu služby AVS, nejdřív zřiďte uzly v Azure Portal.

1. Vyberte **Všechny služby**.
2. Vyhledejte **uzly služby AVS**.

    ![Hledat v uzlech AVS](media/create-cloudsimple-node-search.png)

3. Vyberte **uzly**pro funkci AVS.
4. Kliknutím na **Přidat** vytvořte uzly.

    ![Přidat uzly pro funkci AVS](media/create-cloudsimple-node-add.png)

5. Vyberte předplatné, ve kterém chcete vytvořit uzly pro funkci AVS.
6. Vyberte skupinu prostředků pro uzly. Pokud chcete přidat novou skupinu prostředků, klikněte na **vytvořit novou**.
7. Zadejte předponu pro identifikaci uzlů.
8. Vyberte umístění pro prostředky uzlu.
9. Vyberte vyhrazené umístění pro hostování prostředků uzlu.
10. Vyberte [typ uzlu](cloudsimple-node.md).
11. Vyberte počet uzlů, které se mají zřídit.
12. Vyberte **zkontrolovat + vytvořit**.
13. Zkontrolujte nastavení. Chcete-li změnit nastavení, klikněte na tlačítko **Předchozí**.
14. Vyberte **Vytvořit**.

## <a name="next-steps"></a>Další kroky

* [Vytvoření privátního cloudu pro funkci AVS a konfigurace prostředí](quickstart-create-private-cloud.md)
* Další informace o [službě AVS](https://docs.azure.cloudsimple.com/cloudsimple-service)
