---
title: Integrace s App Service Mobile Apps
description: Zjistěte, jak funguje Azure Notification Hubs s Azure App Service Mobile Apps.
author: jwargo
manager: patniko
editor: spelluru
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
ms.author: jowargo
ms.openlocfilehash: bb39949bc21ece819b7ac6e279390f8bdb0ee00a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158522"
---
# <a name="integration-with-app-service-mobile-apps"></a>Integrace s App Service Mobile Apps

> [!NOTE]
> Společnost Microsoft se zavazuje plné podpoře Azure App Service Mobile Apps, včetně podpory pro nejnovější verzi operačního systému, opravy chyb, vylepšení dokumentaci a komunity žádosti o přijetí změn zkontroluje. Mějte prosím na paměti, že produktový tým není aktuálně investic do jakékoli nové funkce práce pro Azure Mobile Apps. Děkujeme za vysoce komunitní příspěvky do všech oblastí Azure Mobile Apps.

Pro zajištění plynulého a sjednocujícího prostředí napříč službami Azure nabízí [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) integrovanou podporu pro nabízená oznámení prostřednictvím služby Notification Hubs. Služba [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) nabízí vysoce škálovatelnou a globálně dostupnou platformu pro vývoj mobilních aplikací určenou pro vývojáře a integrátory systémů ve velkých firmách. Přináší bohatou sadu funkcí pro vývojáře pro mobilní zařízení.

Vývojáři v Mobile Apps mohou službu Notification Hubs využívat v rámci následujícího pracovního postupu:

1. Načtení popisovače systému PNS zařízení
2. Registrace zařízení ve službě Notification Hubs se provádí pohodlně v registračním rozhraní API sady Mobile Apps Client SDK.

    > [!NOTE]
    > Mějte na paměti, že služba Mobile Apps odstraní z bezpečnostních důvodů při registraci všechny značky. Služba Notification Hubs vám umožní přiřadit značky k zařízením přímo z back-endu.

3. Odesílání oznámení z back-endu aplikace pomocí Notification Hubs

Zde jsou některé výhody, které vývojáři získají díky této integraci:

- **Sady Mobile Apps Client SDK**: Tyto multiplatformní sady SDK poskytují jednoduchá rozhraní API pro registraci a automaticky komunikují s centrem oznámení propojeným s touto mobilní aplikací. Vývojáři se nemusí zabývat přihlašovacími údaji pro Notification Hubs a pracovat s další službou.
  - *Push uživateli*: Tyto sady SDK automaticky označí dané zařízení ověřeným ID uživatele Mobile Apps, díky čemuž je možné uskutečnit scénář nabízených oznámení pro uživatele.
  - *Odeslat do zařízení*: Sady SDK automaticky používají instalační ID Mobile Apps jako identifikátor GUID pro registraci v Notification Hubs, což vývojáře zbavuje nutnosti starat se o identifikátory GUID pro několik služeb.
- **Instalační model**: Služba Mobile Apps pracuje s nejnovějším modelem nabízených oznámení v Notification Hubs, který reprezentuje všechny vlastnosti nabízených oznámení související se zařízením v instalaci JSON, což vyhovuje Službě nabízených oznámení a nabízí snadné použití.
- **Flexibilita**: Vývojáři se vždy mohou rozhodnout pracovat přímo s Notification Hubs, a to i když je tato služba integrována.
- **Integrované prostředí na [webu Azure portal](https://portal.azure.com)**: Nabízená oznámení jsou v Mobile Apps vizuálně reprezentována jako funkce a vývojáři mohou přes Mobile Apps snadno pracovat s přidruženým centrem oznámení.
