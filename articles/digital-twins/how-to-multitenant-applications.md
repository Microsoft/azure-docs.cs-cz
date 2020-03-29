---
title: Povolení víceklientských aplikací – digitální dvojčata Azure | Dokumenty společnosti Microsoft
description: Jak nakonfigurovat víceklientské aplikace Azure Active Directory pro digitální dvojče Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 6e1321e01d8d12974a2704f4478b02a26c14142f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264929"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Povolení víceklientských aplikací pomocí digitálních dvojčat Azure

Vývojáři řešení, kteří staví na Azure Digital Twins, mohou zjistit, že chtějí podporovat více zákazníků pomocí jediné služby nebo řešení. Ve skutečnosti *víceklientské* aplikace patří mezi nejběžnější konfigurace Azure Digital Twins.

Tento dokument popisuje, jak nakonfigurovat aplikaci Azure Digital Twins pro podporu několika klientů a zákazníků služby Azure Active Directory.

## <a name="multitenancy"></a>Vícenásobná nancy

*Víceklientské* prostředky je jedna zřízená instance, která podporuje více zákazníků. Každý zákazník má svá vlastní nezávislá data a oprávnění. Zkušenosti každého zákazníka jsou izolovány od zkušeností ostatních, takže jejich "zobrazení" aplikace je odlišné.

Další informace o multitenancy najdete v článek [Víceklientské aplikace v Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications).

## <a name="problem-scenario"></a>Problémový scénář

V tomto scénáři zvažte vývojářvytváření řešení Azure Digital Twins (**DEVELOPER**) a zákazník, který používá toto řešení (**ZÁKAZNÍK**):

- **VÝVOJÁŘ** má předplatné Azure s tenantem Azure Active Directory.
- **DEVELOPER** nasadí instanci Azure Digital Twins do svého předplatného Azure. Služba Azure Active Directory automaticky vytvořila objekt zabezpečení služby v tenantovi azure služby **AZURE.**
- Uživatelé v **tenantovi Azure**Active Directory vývojáře pak můžou ze služby Azure Digital Twins [získat tokeny OAuth 2.0.](./security-authenticating-apis.md)
- **DEVELOPER** teď vytvoří mobilní aplikaci, která se přímo integruje s virtuálními zařízeními pro správu digitálních dvojčat Azure.
- **DEVELOPER** umožňuje **zákazníkovi** používat mobilní aplikaci.
- **Zákazník** musí mít oprávnění používat rozhraní Azure Digital Twins Management API v rámci aplikace **DEVELOPER.**

Problém:

- Když **se zákazník** přihlásí do aplikace **developera,** aplikace nemůže získat tokeny pro uživatele **zákazníka**k ověření pomocí azure digital twins management API.
- Ve službě Azure Active Directory se vydává výjimka, která označuje, že azure digital twins není rozpoznána v adresáři **ZÁKAZNÍKA.**

## <a name="problem-solution"></a>Řešení problému

Chcete-li vyřešit předchozí scénář problému, jsou potřeba následující akce k vytvoření hlavního mocnina služby Azure Digital Twins v **tenantovi**Azure Active Directory zákazníka:

- Pokud **zákazník** ještě nemá předplatné Azure s tenantem Azure Active Directory:

  - Správce klienta Azure Active Directory **zákazníka**musí získat předplatné Azure s [průběžným platbou](https://azure.microsoft.com/offers/ms-azr-0003p/).
  - Správce klienta Azure Active Directory **zákazníka**pak musí [propojit svého klienta s novým předplatným](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity).

- Na [webu Azure Portal](https://portal.azure.com)provede správce klienta Azure Active Directory **zákazníka**následující kroky:

  1. Vyhledejte **předplatná** v hlavním vyhledávacím poli Azure. Vyberte **Předplatná**.
  1. Vyberte předplatné, které má tenanta Azure Active Directory, který se má použít v aplikaci **DEVELOPER.**

     [![Předplatná Služby Azure Active Directory](media/multitenant/ad-subscriptions.png)](media/multitenant/ad-subscriptions.png#lightbox)

  1. Vyberte **zprostředkovatele prostředků**.
  1. Vyhledejte **soubor Microsoft.IoTSpaces**.
  1. Vyberte **Zaregistrovat**.

     [![Poskytovatelé prostředků služby Azure Active Directory](media/multitenant/ad-resource-providers.png)](media/multitenant/ad-resource-providers.png#lightbox)
  
## <a name="next-steps"></a>Další kroky

- Další informace o tom, jak používat uživatelem definované funkce s Azure Digital Twins, najdete v článku [Jak vytvořit uživatelem definované funkce Azure Digital Twins](./how-to-user-defined-functions.md).

- Informace o tom, jak pomocí řízení přístupu na základě rolí dále zabezpečit aplikaci pomocí přiřazení rolí, najdete v článek [Jak vytvořit a spravovat řízení přístupu na základě rolí Azure Digital Twins](./security-create-manage-role-assignments.md).
