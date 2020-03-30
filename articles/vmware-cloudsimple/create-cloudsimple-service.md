---
title: Řešení Azure VMware od CloudSimple – vytvoření služby CloudSimple
description: Popisuje, jak vytvořit službu CloudSimple na webu Azure Portal.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8648f2c9cc0175050d4b7642f5235d47159ecfaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024819"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Vytvoření řešení Azure VMware pomocí služby CloudSimple

Pokud chcete začít pracovat s řešením Azure VMware od CloudSimple, vytvořte řešení Azure VMware podle clouduSimple na webu Azure Portal.

## <a name="before-you-begin"></a>Než začnete

Přidělte blok CIDR /28 pro podsíť brány. Podsíť brány je vyžadována pro službu CloudSimple a je jedinečná pro oblast, ve které je vytvořena. Podsíť brány se používá pro hraniční síťové služby a vyžaduje blok CIDR /28. Adresní prostor podsítě brány musí být jedinečný. Nesmí se překrývat s žádnou sítí, která komunikuje s prostředím CloudSimple. Sítě, které komunikují s CloudSimple patří místní sítě a virtuální sítě Azure.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [portálu Azure](https://portal.azure.com).

## <a name="create-the-service"></a>Vytvoření služby

1. Vyberte **Všechny služby**.
2. Vyhledejte **cloudové jednoduché služby**.
    ![Hledat cloudovou jednoduchou službu](media/create-cloudsimple-service-search.png)
3. Vyberte **možnost CloudSimple Services**.
4. Chcete-li vytvořit novou službu, klepněte na tlačítko **Přidat.**
    ![Přidat cloudovou službu](media/create-cloudsimple-service-add.png)
5. Vyberte předplatné, ve kterém chcete vytvořit službu CloudSimple.
6. Vyberte skupinu prostředků pro službu. Chcete-li přidat novou skupinu prostředků, klepněte na tlačítko **Vytvořit nový**.
7. Zadejte název pro identifikaci služby.
8. Zadejte CIDR pro bránu služby. Zadejte podsíť /28, která se nepřekrývá s žádnou místní podsítí, podsítěmi Azure nebo plánovanými podsítěmi CloudSimple. Po vytvoření služby nelze změnit CIDR.

    ![Vytvoření služby CloudSimple](media/create-cloudsimple-service.png)
9. Klikněte na tlačítko **OK**.

Služba je vytvořena a přidána do seznamu služeb.

## <a name="next-steps"></a>Další kroky

* Naučte se [zřazovat uzly](create-nodes.md)
* Přečtěte si, jak [vytvořit privátní cloud](create-private-cloud.md)
* Přečtěte si, jak [nakonfigurovat prostředí privátního cloudu](quickstart-create-private-cloud.md)
