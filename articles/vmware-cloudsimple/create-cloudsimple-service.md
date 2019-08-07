---
title: Vytvoření řešení Azure VMware pomocí CloudSimple – Service
description: Popisuje, jak vytvořit službu CloudSimple v Azure Portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6986e0a7e6eee6dbbd43c72a415b01df7da7da51
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812438"
---
# <a name="create-azure-vmware-solution-by-cloudsimple---service"></a>Vytvoření řešení Azure VMware pomocí CloudSimple – Service

Pokud chcete začít s řešením Azure VMware od CloudSimple, vytvořte v Azure Portal službu řešení Azure VMware podle CloudSimple.

## <a name="before-you-begin"></a>Před zahájením

Přidělte bloku CIDR/28 pro podsíť brány.  Pro každou službu CloudSimple se vyžaduje podsíť brány a je jedinečná pro oblast, ve které je vytvořená. Podsíť brány se používá pro služby hraniční sítě a vyžaduje blok CIDR/28. Adresní prostor podsítě brány musí být jedinečný. Nesmí se překrývat s žádnou sítí, která komunikuje s prostředím CloudSimple.  Sítě, které komunikují s CloudSimple, zahrnují místní sítě a virtuální sítě Azure.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

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

8. Zadejte CIDR pro bránu služby. Zadejte podsíť/28, která se nepřekrývá s žádnou z vašich stávajících podsítí.  Patří mezi ně místní podsítě, podsítě Azure nebo jakékoliv plánované podsítě CloudSimple. Po vytvoření služby nemůžete změnit CIDR.

    ![Vytvoření služby CloudSimple](media/create-cloudsimple-service.png)

9. Klikněte na **OK**.

Služba je vytvořena a přidána do seznamu služeb.

## <a name="next-steps"></a>Další postup

* Informace o tom, jak [vytvořit privátní cloud](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Informace o tom, jak [nakonfigurovat prostředí privátního cloudu](quickstart-create-private-cloud.md)
