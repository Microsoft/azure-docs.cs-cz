---
title: Notification Hubs integrace s App Service Mobile Apps
description: Přečtěte si, jak Azure Notification Hubs pracuje s Mobile Appsem Azure App Service.
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 08/06/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: e7042be7e407e8e0827e142ba6878dfff812e1f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88004064"
---
# <a name="integration-with-app-service-mobile-apps"></a>Integrace s App Service Mobile Apps

Pro usnadnění bezproblémového a sjednocení prostředí napříč službami Azure [App Service Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) má integrovaná podpora pro oznámení pomocí Azure Notification Hubs. [App Service Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) nabízí vysoce škálovatelnou a globálně dostupnou platformu pro vývoj mobilních aplikací, která umožňuje vývojářům a systémům v podnicích získat bohatou sadu funkcí pro mobilní vývojáře.

Mobile Apps mohou vývojáři používat Notification Hubs s následujícím pracovním postupem:

1. Načtěte popisovač PNSa zařízení.
2. Zaregistrujte zařízení pomocí Notification Hubs pomocí rozhraní API pro registraci Mobile Apps klientské sady SDK.

    > [!NOTE]
    > Mějte na paměti, že služba Mobile Apps odstraní z bezpečnostních důvodů při registraci všechny značky. Služba Notification Hubs vám umožní přiřadit značky k zařízením přímo z back-endu.

3. Odesílání oznámení z back-endu vaší aplikace pomocí Notification Hubs.

Tato integrace přináší některé výhody:

- **Mobile Apps SDK klienta**: tyto sady SDK pro více platforem poskytují rozhraní API pro registraci a komunikují s centrem oznámení, které je propojeno s mobilní aplikací. Nepotřebujete Notification Hubs přihlašovací údaje nebo budete pracovat s další službou.
  - *Odeslat uživateli*: sady SDK automaticky označí zadané zařízení s ID ověřeného uživatele Mobile Apps, aby bylo možné povolit vložení do uživatelského scénáře.
  - *Odeslat do zařízení*: sady SDK automaticky používají Mobile Apps ID instalace jako identifikátor GUID k registraci v Notification Hubs, takže není nutné spravovat více identifikátorů GUID služby.
- **Model instalace**: Mobile Apps pracuje s Notification Hubs nejnovějším modelem nabízených oznámení, který představuje všechny vlastnosti push přidružené k zařízení v instalaci JSON, které odpovídají službám nabízených oznámení a jsou snadno použitelné.
- **Flexibilita:** Vývojáři se vždy mohou rozhodnout, že budou pracovat přímo se službou Notification Hubs, i když je tato služba integrovaná.
- **Integrované prostředí [Azure Portal](https://portal.azure.com)**: nabízení jako funkce je vizuálně znázorněné v Mobile Apps a vývojáři můžou snadno pracovat s přidruženým centrem oznámení prostřednictvím Mobile Apps.
