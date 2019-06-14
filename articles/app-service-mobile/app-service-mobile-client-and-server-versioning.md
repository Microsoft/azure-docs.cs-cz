---
title: Klient a server správy verzí sady SDK v Mobile Apps a Mobile Services | Dokumentace Microsoftu
description: Seznam klientských sad SDK a kompatibilitu s verzí sady SDK serveru pro Mobile Services a Azure Mobile Apps
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: cfa6a363725c35083b32d6de1dd1371777f91907
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240305"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Správa verzí klienta a serveru v Mobile Apps a Mobile Services
Nejnovější verze Azure Mobile Services je **Mobile Apps** funkce služby Azure App Service.

Sady SDK pro klienta a serveru Mobile Apps jsou původně podle toho, které v Mobile Services, ale jsou *není* navzájem kompatibilní.
To znamená, že musí použít *Mobile Apps* Klientská sada SDK s *Mobile Apps* sada SDK pro server a podobně pro *Mobile Services*. Tato smlouva se vynucuje prostřednictvím hodnotu speciální hlavičky používané klientem a serverem sady SDK, `ZUMO-API-VERSION`.

Poznámka: vždy, když tento dokument odkazuje *Mobile Services* back-endu, nemusí být nutně zajistit také jejich hostování v Mobile Services. Nyní je možné migrovat mobilní služby ke spuštění ve službě App Service bez jakýchkoli změn kódu, ale stále používat službu *Mobile Services* verze sady SDK.

Další informace o migraci do služby App Service bez jakýchkoli změn kódu, najdete v článku [migrace mobilní službu Azure App Service].

## <a name="header-specification"></a>Specifikace záhlaví
Klíč `ZUMO-API-VERSION` lze zadat v hlavičce protokolu HTTP nebo řetězec dotazu. Hodnota je řetězec verze ve formátu **x.y.z**.

Příklad:

ZÍSKAT https://service.azurewebsites.net/tables/TodoItem

ZÁHLAVÍ: ZUMO-API-VERSION: 2.0.0

PŘÍSPĚVEK https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Výslovné odhlášení z kontroly verzí
Kontroly tak, že nastavíte hodnotu verze, můžete zrušit **true** pro nastavení aplikace, které **MS_SkipVersionCheck**. Tuto verzi uveďte v souboru web.config nebo v části Nastavení aplikace na webu Azure portal.

> [!NOTE]
> Existuje mnoho změn chování mezi Mobile Services a Mobile Apps, zejména v oblasti offline synchronizaci, ověřování a nabízená oznámení. Zakážete by měl pouze verze kontrola po dokončení testování pro zajištění, že tyto chování změny nenaruší funkce vaší aplikace.

## <a name="2.0.0"></a>Azure Mobile Apps klienta a serveru
### <a name="MobileAppsClients"></a> Mobilní *aplikace* klientské sady SDK
Kontrola verze se zavedl až od s následujícími verzemi klientskou sadou SDK pro **Azure Mobile Apps**:

| Klientskou platformu | Version | Hodnota záhlaví verze |
| --- | --- | --- |
| Spravované klientské (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

### <a name="mobile-apps-server-sdks"></a>Mobilní *aplikace* sadami SDK serveru
Kontrola verze je součástí následující verze sady SDK serveru:

| Serverová platforma | Sada SDK | Přijatá verze hlavičky |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Chování back-EndY mobilních aplikací
| ZUMO-API-VERSION | Hodnota MS_SkipVersionCheck | Odpověď |
| --- | --- | --- |
| x.y.z nebo hodnota Null |True |200 – OK |
| Null |False nebo není zadaný |400 - Chybný požadavek |
| 1.x.y |False nebo není zadaný |400 - Chybný požadavek |
| 2.0.0-2.x.y |False nebo není zadaný |200 – OK |
| 3.0.0-3.x.y |False nebo není zadaný |400 - Chybný požadavek |

[Mobile Services clients]: #MobileServicesClients
[Mobile Apps clients]: #MobileAppsClients
[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
