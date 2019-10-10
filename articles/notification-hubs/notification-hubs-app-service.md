---
title: Integrace s App Service Mobile Apps
description: Přečtěte si, jak Azure Notification Hubs pracuje s Mobile Appsem Azure App Service.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: ''
ms.assetid: 83132dff-a01d-4b31-a426-b57496852b81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 8c3bc90b282092ede0e924d32b50b67e5c4e22b8
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244505"
---
# <a name="integration-with-app-service-mobile-apps"></a>Integrace s App Service Mobile Apps

Pro usnadnění bezproblémového a sjednocení prostředí napříč službami Azure [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) obsahuje integrovanou podporu pro nabízená oznámení pomocí Notification Hubs. [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) nabízí vysoce škálovatelnou a globálně dostupnou platformu pro vývoj mobilních aplikací pro podnikové vývojáře a systémové integrátory, která přináší bohatou sadu funkcí pro mobilní vývojáře.

Visual Studio App Center přináší dnes moderní mobilní řešení pro vývojáře. Podporuje vývoj koncových a integrovaných služeb z centrálního vývoje mobilních aplikací. Vývojáři **mohou pomocí sestavování**, **testování** a **distribuce** služeb nastavit kanál průběžné integrace a doručování. Po nasazení aplikace mohou vývojáři sledovat stav a využití své aplikace pomocí **analytických** a **diagnostických** služeb a spolupracovat s uživateli pomocí služby **push** . Vývojáři můžou také využít **ověřování** k ověřování uživatelů a **datových** služeb, aby zachovaly a synchronizovaly data aplikací v cloudu.

> [!NOTE]
> Pokud chcete v mobilní aplikaci integrovat cloudové služby, zaregistrujte se [App Center](https://appcenter.ms/signup?utm_source=NotificationHubs&utm_medium=Azure&utm_campaign=docs) dnes.

Mobile Apps vývojáři mohou využít Notification Hubs s následujícím pracovním postupem:

1. Načíst popisovač PNS zařízení
2. Registrace zařízení pomocí Notification Hubs přes praktické rozhraní API pro registraci Mobile Apps klientské sady SDK

    > [!NOTE]
    > Všimněte si, že Mobile Apps odříznout všechny značky v registrech z hlediska zabezpečení. Spolupracujte se Notification Hubs z back-endu přímo k přidružení značek k zařízením.

3. Odesílání oznámení z back-endu vaší aplikace pomocí Notification Hubs

Tady je několik výhod, které vývojářům poskytuje tato integrace:

- **Mobile Apps SDK klienta**: tyto sady SDK pro více platforem poskytují jednoduchá rozhraní API pro registraci a komunikují s centrem oznámení propojenými s mobilní aplikací automaticky. Vývojáři nemusí dig prostřednictvím přihlašovacích údajů Notification Hubs a pracovat s další službou.
  - *Odeslat uživateli*: sady SDK automaticky označí dané zařízení pomocí Mobile apps ověřeného ID uživatele, aby bylo možné povolit scénář vložení do uživatele.
  - *Odeslat do zařízení*: sady SDK automaticky používají Mobile Apps ID instalace jako identifikátor GUID k registraci v Notification Hubs a ukládají vývojářům problémy s údržbou více identifikátorů GUID služby.
- **Model instalace**: Mobile Apps pracuje s Notification Hubs "nejnovějším modelem nabízených oznámení", který představuje všechny vlastnosti push přidružené k zařízení v instalaci JSON, které odpovídají službám nabízených oznámení a jsou snadno použitelné.
- **Flexibilita**: vývojáři se můžou vždycky rozhodnout pracovat s Notification Hubs přímo i s integrací na místě.
- **Integrované prostředí v [Azure Portal](https://portal.azure.com)** : nabízení jako funkce je znázorněné vizuálně v Mobile Apps a vývojáři můžou snadno pracovat s přidruženým centrem oznámení prostřednictvím Mobile Apps.
