---
title: Řešení Azure VMware (AVS) – vytvoření služby AVS
description: V této části najdete popis postupu vytvoření služby AVS v Azure Portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 468ef8751438812422d7eee83d98acc9e3aedb82
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024819"
---
# <a name="create-the-azure-vmware-solutions-avs-service"></a>Vytvoření služby Azure VMware Solutions (AVS)

Pokud chcete začít s řešeními Azure VMware (AVS), vytvořte v Azure Portal službu Azure VMware Solutions (AVS).

## <a name="before-you-begin"></a>Než začnete

Přidělte bloku CIDR/28 pro podsíť brány. Pro každou službu AVS se vyžaduje podsíť brány a je jedinečná pro oblast, ve které je vytvořená. Podsíť brány se používá pro služby hraniční sítě a vyžaduje blok CIDR/28. Adresní prostor podsítě brány musí být jedinečný. Nesmí se překrývat s žádnou sítí, která komunikuje s prostředím AVS. Sítě, které komunikují se službou AVS, zahrnují místní sítě a virtuální sítě Azure.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [Portálu Azure](https://portal.azure.com).

## <a name="create-the-service"></a>Vytvoření služby

1. Vyberte **Všechny služby**.
2. Vyhledejte **služby AVS**.
    ![Hledat službu AVS](media/create-cloudsimple-service-search.png)
3. Vyberte **služby AVS**.
4. Kliknutím na **Přidat** vytvořte novou službu.
    ![přidat](media/create-cloudsimple-service-add.png) služby AVS
5. Vyberte předplatné, ve kterém chcete vytvořit službu AVS.
6. Vyberte skupinu prostředků pro službu. Pokud chcete přidat novou skupinu prostředků, klikněte na **vytvořit novou**.
7. Zadejte název pro identifikaci služby.
8. Zadejte CIDR pro bránu služby. Zadejte podsíť/28, která se nepřekrývá s žádnou z vašich místních podsítí, podsítí Azure ani s plánovanými podsítěmi služby AVS. Po vytvoření služby nemůžete změnit CIDR.

    ![Vytvoření služby AVS](media/create-cloudsimple-service.png)
9. Klikněte na **OK**.

Služba je vytvořena a přidána do seznamu služeb.

## <a name="next-steps"></a>Další kroky

* Naučte se [zřizovat uzly](create-nodes.md) .
* Informace o tom, jak [vytvořit privátní cloud služby AVS](create-private-cloud.md)
* Naučte se [Konfigurovat prostředí privátního cloudu služby AVS](quickstart-create-private-cloud.md)
