---
title: 'Rychlý Start: vytvoření služby VMware CloudSimple'
titleSuffix: Azure VMware Solution by CloudSimple
description: Naučte se vytvářet služby CloudSimple, koupit uzly a rezervovat uzly.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 14df0f131aaef8a4c24e2d1eb242a9b440e7c7b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86507586"
---
# <a name="quickstart---create-azure-vmware-solution-by-cloudsimple-service"></a>Rychlý Start – vytvoření řešení Azure VMware pomocí služby CloudSimple

Pokud chcete začít, vytvořte řešení Azure VMware podle CloudSimple v Azure Portal.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>Řešení VMware podle CloudSimple – přehled služby

Služba CloudSimple umožňuje využívat řešení Azure VMware od CloudSimple.  Vytvoření služby umožňuje zřídit uzly, rezervovat uzly a vytvořit privátní cloudy.  Službu CloudSimple přidáte v každé oblasti Azure, kde je dostupná služba CloudSimple.  Služba definuje hraniční síť řešení Azure VMware od CloudSimple.  Tato hraniční síť se používá pro služby, které zahrnují připojení VPN, ExpressRoute a Internet k privátním cloudům.

Pokud chcete přidat službu CloudSimple, musíte vytvořit podsíť brány. Podsíť brány se používá při vytváření hraniční sítě a vyžaduje blok CIDR/28. Adresní prostor podsítě brány musí být jedinečný. Nemůže se překrývat s žádnými místními adresními prostory vaší sítě ani adresním prostorem virtuální sítě Azure.

## <a name="before-you-begin"></a>Než začnete

Přidělte bloku CIDR/28 pro podsíť brány.  Pro každou službu CloudSimple se vyžaduje podsíť brány a je jedinečná pro oblast, ve které je vytvořená. Podsíť brány se používá pro řešení Azure VMware pomocí služeb CloudSimple Edge Network a vyžaduje blok CIDR/28. Adresní prostor podsítě brány musí být jedinečný. Nesmí se překrývat s žádnou sítí, která komunikuje s prostředím CloudSimple.  Sítě, které komunikují s CloudSimple, zahrnují místní sítě a virtuální sítě Azure.

Kontrola [požadavků na síť](cloudsimple-network-checklist.md). 

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

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

## <a name="provision-nodes"></a>Zřizování uzlů

Pokud chcete nastavit kapacitu s průběžnými platbami pro prostředí privátního cloudu CloudSimple, nejdřív zřiďte uzly v Azure Portal.

1. Vyberte **Všechny služby**.
2. Vyhledejte **uzly CloudSimple**.

    ![Hledat uzly CloudSimple](media/create-cloudsimple-node-search.png)

3. Vyberte **uzly CloudSimple**.
4. Kliknutím na **Přidat** vytvořte uzly.

    ![Přidat uzly CloudSimple](media/create-cloudsimple-node-add.png)

5. Vyberte předplatné, ve kterém chcete zřídit CloudSimple uzly.
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

* [Vytvoření privátního cloudu a konfigurace prostředí](quickstart-create-private-cloud.md)
* Další informace o [službě CloudSimple](./cloudsimple-service.md)
