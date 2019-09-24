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
ms.openlocfilehash: d6747193b8c82119e45a24e3e4bffc065db14e51
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212157"
---
# <a name="integration-with-app-service-mobile-apps"></a>Integrace s App Service Mobile Apps

> [!NOTE]
> Společnost Microsoft se zavazuje plně podporovat Azure App Service Mobile Apps, včetně podpory pro nejnovější verzi operačního systému, opravy chyb, vylepšení dokumentace a recenze pro komunity. Uvědomte si prosím, že produktový tým není v současnosti investovat do žádné nové funkce pro Azure Mobile Apps. Vysoce oceňujete komunitní příspěvky pro všechny oblasti Azure Mobile Apps.

Pro zajištění plynulého a sjednocujícího prostředí napříč službami Azure nabízí [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) integrovanou podporu pro nabízená oznámení prostřednictvím služby Notification Hubs. Služba [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) nabízí vysoce škálovatelnou a globálně dostupnou platformu pro vývoj mobilních aplikací určenou pro vývojáře a integrátory systémů ve velkých firmách. Přináší bohatou sadu funkcí pro vývojáře pro mobilní zařízení.

Vývojáři v Mobile Apps mohou službu Notification Hubs využívat v rámci následujícího pracovního postupu:

1. Načtení popisovače systému PNS zařízení
2. Registrace zařízení ve službě Notification Hubs se provádí pohodlně v registračním rozhraní API sady Mobile Apps Client SDK.

    > [!NOTE]
    > Mějte na paměti, že služba Mobile Apps odstraní z bezpečnostních důvodů při registraci všechny značky. Služba Notification Hubs vám umožní přiřadit značky k zařízením přímo z back-endu.

3. Odesílání oznámení z back-endu aplikace pomocí Notification Hubs

Zde jsou některé výhody, které vývojáři získají díky této integraci:

- **Mobile Apps sady SDK klienta**: Tyto multiplatformní sady SDK poskytují jednoduchá rozhraní API pro registraci a automaticky komunikují s centrem oznámení propojeným s touto mobilní aplikací. Vývojáři se nemusí zabývat přihlašovacími údaji pro Notification Hubs a pracovat s další službou.
  - *Odeslat uživateli*: Tyto sady SDK automaticky označí dané zařízení ověřeným ID uživatele Mobile Apps, díky čemuž je možné uskutečnit scénář nabízených oznámení pro uživatele.
  - *Vložit do zařízení*: Sady SDK automaticky používají instalační ID Mobile Apps jako identifikátor GUID pro registraci v Notification Hubs, což vývojáře zbavuje nutnosti starat se o identifikátory GUID pro několik služeb.
- **Model instalace**: Služba Mobile Apps pracuje s nejnovějším modelem nabízených oznámení v Notification Hubs, který reprezentuje všechny vlastnosti nabízených oznámení související se zařízením v instalaci JSON, což vyhovuje Službě nabízených oznámení a nabízí snadné použití.
- **Flexibilita**: Vývojáři se vždy mohou rozhodnout pracovat přímo s Notification Hubs, a to i když je tato služba integrována.
- **Integrované prostředí v [Azure Portal](https://portal.azure.com)** : Nabízená oznámení jsou v Mobile Apps vizuálně reprezentována jako funkce a vývojáři mohou přes Mobile Apps snadno pracovat s přidruženým centrem oznámení.
