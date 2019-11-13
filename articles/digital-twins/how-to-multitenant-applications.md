---
title: Povolit víceklientské aplikace – digitální vlákna Azure | Microsoft Docs
description: Jak nakonfigurovat víceklientské Azure Active Directory aplikace pro digitální vlákna Azure
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: 6394d519b93b55358ef9d528f89978d5a3cf3007
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74005917"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Povolení víceklientské aplikací s využitím digitálních vláken Azure

Vývojáři řešení, kteří vytvářejí na digitálních Vlákenách Azure, můžou najít, že chtějí podporovat více zákazníků s jednou službou nebo řešením. Ve skutečnosti jsou *víceklientské* aplikace z nejběžnějších konfigurací digitálních vláken Azure.

Tento dokument popisuje, jak nakonfigurovat aplikaci Azure Digital revlákens pro podporu několika Azure Active Directory klientů a zákazníků.

## <a name="multitenancy"></a>Víceklientskou architekturu

Prostředek s více *klienty* je jediná zřízená instance, která podporuje víc zákazníků. Každý zákazník má svá nezávislá data a oprávnění. Činnost každého zákazníka je izolovaná od sebe, takže jejich "zobrazení" aplikace je jedinečné.

Pokud chcete získat další informace o víceklientské architektuře, přečtěte si víc [tenantů aplikací v Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications).

## <a name="problem-scenario"></a>Scénář problému

V tomto scénáři zvažte vývojáře, který sestaví řešení digitálních vláken Azure (**Developer**) a zákazníka, který používá toto řešení (**Zákazník**):

- **Vývojář** má předplatné Azure s klientem Azure Active Directory.
- **Vývojář** nasadí instanci digitálního vlákna Azure do svého předplatného Azure. Azure Active Directory automaticky vytvořil instanční objekt v tenantovi Azure Active Directory **vývojáře**.
- Uživatelé v rámci tenanta Azure Active Directory **vývojářů**potom můžou [získat tokeny OAuth 2,0](./security-authenticating-apis.md) ze služby digitálního vlákna Azure.
- **Vývojář** teď vytvoří mobilní aplikaci, která se přímo integruje s rozhraními API pro správu digitálních vláken Azure.
- **Vývojář** umožňuje **zákazníkům** používat mobilní aplikace.
- **Zákazník** musí mít autorizaci používat rozhraní API pro správu digitálních vláken Azure v rámci aplikace **vývojáře**.

Problém:

- Když se **Zákazník** do aplikace pro **vývojáře**přihlásí, aplikace nemůže získat tokeny pro uživatele **zákazníka**k ověření pomocí rozhraní API pro správu digitálních vláken Azure.
- V Azure Active Directory je vyvolána výjimka, která znamená, že v adresáři **zákazníka**nejsou rozpoznány digitální vlákna Azure.

## <a name="problem-solution"></a>Řešení problému

K vyřešení předchozího scénáře problému je potřeba k vytvoření instančního objektu služby digitálních vláken Azure v rámci klienta Azure Active Directory **zákazníka**použít následující akce:

- Pokud **Zákazník** ještě nemá předplatné Azure s Azure Active Directory tenant:

  - Správce tenanta Azure Active Directory **zákazníka**musí získat [předplatné Azure s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0003p/).
  - Správce tenanta Azure Active Directory **zákazníka**pak musí [propojit svého tenanta s novým předplatným](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity).

- Na [Azure Portal](https://portal.azure.com)Azure Active Directory správce tenanta u **zákazníka**provede následující kroky:

  1. Otevřete **odběry**.
  1. Vyberte předplatné, které má klienta Azure Active Directory použít v aplikaci **vývojáře**.

     [předplatná ![Azure Active Directory](media/multitenant/ad-subscriptions.png)](media/multitenant/ad-subscriptions.png#lightbox)

  1. Vyberte **poskytovatelé prostředků**.
  1. Vyhledejte **Microsoft. IoTSpaces**.
  1. Vyberte **Zaregistrovat**.

     [poskytovatelé prostředků ![Azure Active Directory](media/multitenant/ad-resource-providers.png)](media/multitenant/ad-resource-providers.png#lightbox)
  
## <a name="next-steps"></a>Další kroky

- Pokud se chcete dozvědět víc o tom, jak používat uživatelsky definované funkce s digitálními úkoly Azure, přečtěte si téma [jak vytvořit Azure Digital vlákna – uživatelsky definované funkce](./how-to-user-defined-functions.md).

- Informace o tom, jak pomocí řízení přístupu na základě role dále zabezpečit aplikaci pomocí přiřazení rolí, najdete v tématu [Vytvoření a Správa řízení přístupu na základě role v rámci služby Azure Digital](./security-create-manage-role-assignments.md)forming.
