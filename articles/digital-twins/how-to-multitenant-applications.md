---
title: Povolení víceklientské aplikace s Azure digitální dvojče | Dokumentace Microsoftu
description: Postup konfigurace víceklientské aplikace Azure Active Directory pro digitální dvojče Azure.
author: mavoge
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2019
ms.author: mavoge
ms.openlocfilehash: 2b4f9bf87122f047e496dca1dbd425db8ad7c16c
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119964"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Povolení víceklientské aplikace s Azure digitální dvojče

Řešení pro vývojáře, kteří vytvářejí na digitální dvojče Azure může se stát, že chcete podporovat víc zákazníků s jednou službou nebo řešení. Ve skutečnosti *víceklientské* aplikace jsou mezi nejběžnější konfigurace digitální dvojče Azure.

Tento dokument popisuje, jak nakonfigurovat aplikaci digitální dvojče Azure pro podporu více tenantů Azure Active Directory a zákazníky.

## <a name="multitenancy"></a>Víceklientské architektury

A *multitenant* prostředků je zřízená jednu instanci, která podporuje více zákazníků. Každý zákazník má své vlastní nezávislý datový a oprávnění. Prostředí pro každého zákazníka je izolovaná od druhé strany, aby jejich "Zobrazit" aplikace se liší.

Další informace o víceklientskou architekturu, [víceklientské aplikace v Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications).

## <a name="problem-scenario"></a>Scénář

V tomto scénáři, zvažte vývojář sestavování řešení s Azure digitální dvojče (**DEVELOPER**) a zákazníky, kteří toto řešení využívá (**zákazníka**):

- **Pro vývojáře** má předplatné Azure s tenantem Azure Active Directory.
- **Pro vývojáře** nasadí instanci digitální dvojče Azure do svého předplatného Azure. Azure Active Directory automaticky vytvoří služba instanční objekt v **DEVELOPER**na tenanta Azure Active Directory.
- Uživatelé v rámci **DEVELOPER**v tenantovi Azure Active Directory, můžete [získání tokenů OAuth 2.0](./security-authenticating-apis.md) od služby digitální dvojče Azure.
- **Pro vývojáře** nyní vytvoří mobilní aplikace, která se integruje přímo s Azure digitální dvojče Management API.
- **Pro vývojáře** umožňuje **zákazníka** použití mobilních aplikací.
- **Zákazník** musí být autorizovaný k použití Azure digitální dvojče rozhraní API pro správu v rámci **DEVELOPER**vaší aplikace.

Problém:

- Při **zákazníka** přihlásí k **pro vývojáře**vaší aplikace, aplikace nemůže získat tokeny pro **zákazníka**od uživatele bude možné ověřit pomocí rozhraní API Správce Dvojčat digitální Azure.
- Výjimky vydané v Azure Active Directory označující, že digitální dvojče Azure nebyl rozpoznán v rámci **zákazníka**na adresář.

## <a name="problem-solution"></a>Řešení potíží

V předchozím scénáři problém vyřešíte takto budete potřebovat pro vytvoření digitální dvojče Azure instanční objekt v rámci **zákazníka**na tenanta Azure Active Directory:

- Pokud **zákazníka** ještě nemá předplatné Azure s tenantem Azure Active Directory:

  - **Zákazník**společnosti musíte získat správce tenanta Azure Active Directory [předplatné Azure s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0003p/).
  - **Zákazník**uživatele správce tenanta Azure Active Directory pak musí [propojte svého tenanta pomocí nového předplatného](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity).

- Na [webu Azure portal](https://portal.azure.com), **zákazníka**od správce tenanta Azure Active Directory je potřeba provést následující kroky:

  1. Otevřít **předplatná**.
  1. Vyberte předplatné, který má klienta Azure Active Directory pro použití v **DEVELOPER**vaší aplikace.

     ![Předplatná Azure Active Directory][1]

  1. Vyberte **poskytovatelů prostředků**.
  1. Vyhledejte **Microsoft.IoTSpaces**.
  1. Vyberte **Zaregistrovat**.

     ![Poskytovatelé prostředků Azure Active Directory][2]
  
## <a name="next-steps"></a>Další postup

- Další informace o tom, jak pomocí Azure digitální dvojče uživatelsky definovaných funkcí, [vytvoření uživatelem definovaných funkcích Azure digitální dvojče](./how-to-user-defined-functions.md).

- Další informace o použití řízení přístupu na základě rolí pro dalšího zabezpečení aplikace pomocí přiřazení rolí, přečtěte si téma [vytvoření a správa řízení přístupu na základě rolí Azure digitální dvojče](./security-create-manage-role-assignments.md).

<!-- Images -->
[1]: media/multitenant/ad-subscriptions.png
[2]: media/multitenant/ad-resource-providers.png
