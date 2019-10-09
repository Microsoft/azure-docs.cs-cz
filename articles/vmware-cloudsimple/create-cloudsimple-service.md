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
ms.openlocfilehash: 99194c42dbc6ef07301be517021bf0fb4b4e7c23
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72173511"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Vytvoření řešení Azure VMware pomocí služby CloudSimple

Pokud chcete začít s řešením Azure VMware od CloudSimple, vytvořte v Azure Portal službu řešení Azure VMware podle CloudSimple.

> [!IMPORTANT]
> Před vytvořením služby CloudSimple musíte zaregistrovat poskytovatele prostředků Microsoft. VMwareCloudSimple ve vašem předplatném Azure. Postupujte podle kroků v části [povolení poskytovatele prostředků Microsoft. VMwareCloudSimple ve vašem předplatném Azure](enable-cloudsimple-service.md).

## <a name="sign-in-to-azure"></a>Přihlaste se k Azure

Přihlaste se k [Azure Portal](https://portal.azure.com).

## <a name="create-the-service"></a>Vytvoření služby

1. Vyberte **všechny služby**.
2. Vyhledejte **služby CloudSimple Services**.
    Služba @no__t 0Search CloudSimple @ no__t-1
3. Vyberte **CloudSimple Services**.
4. Kliknutím na **Přidat** vytvořte novou službu.
    Služba @no__t 0Add CloudSimple @ no__t-1
5. Vyberte předplatné, ve kterém chcete vytvořit službu CloudSimple.
6. Vyberte skupinu prostředků pro službu. Pokud chcete přidat novou skupinu prostředků, klikněte na **vytvořit novou**.
7. Zadejte název pro identifikaci služby.
8. Zadejte CIDR pro bránu služby. Zadejte podsíť/28, která se nepřekrývá s žádnou z vašich místních podsítí, podsítí Azure ani s plánovanými podsítěmi CloudSimple. Po vytvoření služby nemůžete změnit CIDR.

    ![Vytvoření služby CloudSimple](media/create-cloudsimple-service.png)
9. Klikněte na tlačítko **OK**.

Služba je vytvořena a přidána do seznamu služeb.

## <a name="next-steps"></a>Další kroky

* Naučte se [zřizovat uzly](create-nodes.md) .
* Informace o tom, jak [vytvořit privátní cloud](create-private-cloud.md)
* Informace o tom, jak [nakonfigurovat prostředí privátního cloudu](quickstart-create-private-cloud.md)
