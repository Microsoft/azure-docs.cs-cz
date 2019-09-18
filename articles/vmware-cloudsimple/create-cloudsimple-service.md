---
title: Řešení Azure VMware podle CloudSimple – vytvoření služby CloudSimple
description: Popisuje, jak vytvořit službu CloudSimple v Azure Portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d6d4af28d79b2375e774da98d4fdb1ad9dc22063
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71035726"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Vytvoření řešení Azure VMware pomocí služby CloudSimple

Pokud chcete začít s řešením Azure VMware od CloudSimple, vytvořte v Azure Portal službu řešení Azure VMware podle CloudSimple.

> [!IMPORTANT]
> Před vytvořením služby CloudSimple musíte zaregistrovat poskytovatele prostředků Microsoft. VMwareCloudSimple ve vašem předplatném Azure. Postupujte podle kroků v části [povolení poskytovatele prostředků Microsoft. VMwareCloudSimple ve vašem předplatném Azure](enable-cloudsimple-service.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

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

## <a name="next-steps"></a>Další postup

* Informace o tom, jak [vytvořit privátní cloud](create-private-cloud.md)
* Informace o tom, jak [nakonfigurovat prostředí privátního cloudu](quickstart-create-private-cloud.md)
