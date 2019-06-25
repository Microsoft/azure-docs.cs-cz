---
title: Povolení řešení VMware Azure službou CloudSimple
description: Popisuje, jak povolit službu CloudSimple na předplatné Azure a pak zaregistrujte poskytovatele prostředků CLoudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2553aa95d5028c510b4e1a1b7f51a9f410bcea51
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154860"
---
# <a name="register-the-microsoftvmwarecloudsimple-resource-provider-on-your-azure-subscription"></a>Registrace poskytovatele prostředků Microsoft.VMwareCloudSimple v rámci předplatného Azure

Služba CloudSimple umožňuje využívat řešení VMware Azure podle CloudSimple. Služba Microsoft.VMwareCloudSimple můžete zaregistrovat poskytovatele prostředků.

## <a name="register-the-resource-provider"></a>Registrace poskytovatele prostředků

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyberte **Všechny služby**.

3. Vyhledejte a vyberte **předplatná**.

    ![Vybrat předplatná](media/cloudsimple-service-select-subscriptions.png)

4. Vyberte předplatné, ve kterém chcete povolit službu CloudSimple.

5. Klikněte na tlačítko **poskytovatelů prostředků** pro předplatné.

6. Použití **Microsoft.VMwareCloudSimple** k filtrování poskytovatele prostředků.

7. Vyberte poskytovatele prostředků a klikněte na tlačítko **zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/cloudsimple-service-enable-resource-provider.png)

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [vytvoření CloudSimple služby](create-cloudsimple-service.md)
* Zjistěte, jak [konfigurace prostředí privátního cloudu](quickstart-create-private-cloud.md)
