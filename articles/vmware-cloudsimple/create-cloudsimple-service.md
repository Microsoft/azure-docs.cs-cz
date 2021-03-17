---
title: Řešení Azure VMware podle CloudSimple – vytvoření služby CloudSimple
description: Naučte se vytvořit službu CloudSimple v Azure Portal. Než začnete, zkontrolujte požadovanou konfiguraci.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 53f2d0fc9f73985bd70792c8c3b7607eb4c560fa
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97896290"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Vytvoření řešení Azure VMware pomocí služby CloudSimple

Pokud chcete začít s řešením Azure VMware od CloudSimple, vytvořte v Azure Portal službu řešení Azure VMware podle CloudSimple.

## <a name="before-you-begin"></a>Než začnete

Přidělte bloku CIDR/28 pro podsíť brány. Pro každou službu CloudSimple se vyžaduje podsíť brány a je jedinečná pro oblast, ve které je vytvořená. Podsíť brány se používá pro služby hraniční sítě a vyžaduje blok CIDR/28. Adresní prostor podsítě brány musí být jedinečný. Nesmí se překrývat s žádnou sítí, která komunikuje s prostředím CloudSimple. Sítě, které komunikují s CloudSimple, zahrnují místní sítě a virtuální sítě Azure.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na web [Azure Portal](https://portal.azure.com).

## <a name="create-the-service"></a>Vytvoření služby

1. Vyberte **Všechny služby**.
2. Vyhledejte **služby CloudSimple Services**.
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

## <a name="next-steps"></a>Další kroky

* Naučte se [zřizovat uzly](create-nodes.md) .
* Informace o tom, jak [vytvořit privátní cloud](create-private-cloud.md)
* Informace o tom, jak [nakonfigurovat prostředí privátního cloudu](quickstart-create-private-cloud.md)
