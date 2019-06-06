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
ms.openlocfilehash: cacd5510147ce997efec922f4b4656956a098d88
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676935"
---
# <a name="register-the-microsoftvmwarecloudsimple-resource-provider-on-your-azure-subscription"></a>Registrace poskytovatele prostředků Microsoft.VMwareCloudSimple v rámci předplatného Azure

Služba CloudSimple umožňuje využívat řešení VMware Azure podle CloudSimple. Pokud chcete používat službu CloudSimple, musí nejprve být povolená na vaše předplatné Azure. Pak je možné zaregistrovat službu Microsoft.VMwareCloudSimple jako poskytovatele prostředků.

## <a name="enable-the-cloudsimple-service"></a>Povolit službu CloudSimple

Chcete-li povolit službu CloudSimple na vaše předplatné Azure, otevřete žádost o podporu s [podpory Microsoftu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Vyberte následující možnosti, když odešlete žádost.

* Typ problému: **Technické**
* Předplatné: **ID vašeho předplatného**
* Typ služby: **Řešení VMware podle CloudSimple**
* Typ problému: **Kvóta vyhrazené uzly**
* Podtyp problému: **Navyšte kvótu vyhrazené uzly**
* Předmět: **Povolit službu CloudSimple**

Taky se můžete obrátit na obchodního zástupce společnosti Microsoft [ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com). Zadejte ID svého předplatného Azure v e-mailu.  

Po povolení služby CloudSimple pro vaše předplatné můžete povolit zprostředkovatele prostředků v rámci předplatného.

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