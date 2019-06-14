---
title: Vytvoření řešení VMware Azure podle CloudSimple – služby
description: Popisuje, jak vytvořit službu CloudSimple na webu Azure Portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fc08ce48b3b5a4b2cd28cb66e6dd4d9eae3f3885
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66676950"
---
# <a name="create-azure-vmware-solution-by-cloudsimple---service"></a>Vytvoření řešení Azure VMware podle CloudSimple – služby

Abyste mohli začít s řešením Azure VMware CloudSimple, vytvořte řešení VMware Azure CloudSimple služby na webu Azure Portal.

> [!NOTE]
> Než vytvoříte službu CloudSimple, můžete v rámci předplatného Azure, zaregistrujte poskytovatele prostředků Microsoft.VMwareCloudSimple. Postupujte podle kroků v [povolit Microsoft.VMwareCloudSimple poskytovatele prostředků v předplatném Azure](enable-cloudsimple-service.md).

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Vytvoření služby

1. Vyberte **Všechny služby**.

2. Vyhledejte **CloudSimple služby**.

    ![Search CloudSimple Service](media/create-cloudsimple-service-search.png)

3. Vyberte **CloudSimple služby**.

4. Klikněte na tlačítko **přidat** k vytvoření nové služby.

    ![Přidat službu CloudSimple](media/create-cloudsimple-service-add.png)

5. Vyberte předplatné, ve kterém chcete vytvořit službu CloudSimple.

6. Vyberte skupinu prostředků pro službu. Chcete-li přidat novou skupinu prostředků, klikněte na tlačítko **vytvořit nový**.

7. Zadejte název k identifikaci služby.

8. Zadejte CIDR služby brány. Zadejte o velikosti/28 podsítě, který se nepřekrývá s žádným z existující podsítě.  Patří mezi ně místních podsítích, Azure podsítě, nebo všechny plánované CloudSimple podsítě. Po vytvoření služby nelze změnit CIDR.

    ![Vytvoření služby CloudSimple](media/create-cloudsimple-service.png)

9. Klikněte na **OK**.

Služba je vytvořen a přidán do seznamu služeb.

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [vytvoření privátního cloudu](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Zjistěte, jak [konfigurace prostředí privátního cloudu](quickstart-create-private-cloud.md)
