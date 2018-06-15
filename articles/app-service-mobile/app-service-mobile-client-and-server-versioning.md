---
title: Klient a server správy verzí sady SDK v Mobile Apps a Mobile Services | Microsoft Docs
description: Seznam klientskou sadu SDK a kompatibilitu s verzí serveru SDK pro Mobile Services a Azure Mobile Apps
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
ms.openlocfilehash: 37bf36af535eb9b5c8b0ba38434b71f1a6686811
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
ms.locfileid: "27593370"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Správa verzí klientských a serverových v Mobile Apps a Mobile Services
Nejnovější verzi Azure Mobile Services je **Mobile Apps** funkce Azure App Service.

Sady SDK pro klienta a serveru Mobile Apps jsou původně založené na těch, které v Mobile Services, ale jsou *není* vzájemně kompatibilní.
To znamená, že musí použít *Mobile Apps* klienta SDK s *Mobile Apps* serveru SDK a podobně pro *Mobile Services*. Tato smlouva se vynucuje prostřednictvím hodnotu speciální hlavičky používané klientem a serverem sady SDK, `ZUMO-API-VERSION`.

Poznámka: když tento dokument odkazuje *Mobile Services* back-end, nemusí nutně pro hostování v Mobile Services. Nyní je možné migrovat mobilních služeb ke spuštění v App Service beze změn kódu, ale stále používat službu *Mobile Services* verze sady SDK.

Další informace o migraci do služby App Service beze změn kódu, najdete v článku [migrace mobilní služby Azure App Service].

## <a name="header-specification"></a>Specifikace záhlaví
Klíč `ZUMO-API-VERSION` může být určen v hlavičce HTTP nebo řetězec dotazu. Hodnota je řetězec verze ve formě **x.y.z**.

Příklad:

GET https://service.azurewebsites.net/tables/TodoItem

HEADERS: ZUMO-API-VERSION: 2.0.0

POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Zrušení kontrolu verzí
Můžete vyjádření výslovného nesouhlasu kontroly nastavením hodnotu verze **true** pro nastavení aplikace **MS_SkipVersionCheck**. Tuto verzi uveďte buď do souboru web.config, nebo v části Nastavení aplikace z portálu Azure.

> [!NOTE]
> Existuje několik změn v chování mezi Mobile Services a mobilní aplikace, zejména v oblasti ověřování, offline synchronizace a nabízených oznámení. Má jenom vyjádření výslovného nesouhlasu verze kontrola po dokončení testování pro zajištění, že tyto změny chování není rozdělit funkce vaší aplikace.
>
>

## <a name="summary-of-compatibility-for-all-versions"></a>Souhrn kompatibility pro všechny verze
Následující graf zobrazuje kompatibilitu mezi všechny typy klienta a serveru. Back-end je jsou klasifikovány jako buď Mobile **služby** nebo Mobile **aplikace** založené na serveru SDK, která používá.

|  | **Mobilní služby** Node.js, nebo .NET | **Mobilní aplikace** Node.js, nebo .NET |
| --- | --- | --- |
| [Klienti Mobile Services] |OK |Chyba\* |
| [Klienti Mobile Apps] |Chyba\* |OK |

\*To se dá nastavit podle určení **MS_SkipVersionCheck**.

<!-- IMPORTANT!  The anchors for Mobile Services and Mobile Apps MUST be 1.0.0 and 2.0.0 respectively, since there is an exception error message that uses those anchors. -->

<!-- NOTE: the fwlink to this document is http://go.microsoft.com/fwlink/?LinkID=690568 -->

## <a name="1.0.0"></a>Klientem Mobile Services a serveru
Klientské sady SDK v následující tabulce jsou kompatibilní s **Mobile Services**.

Poznámka: Mobile Services klientskou sadu SDK *nepodporují* odeslání hodnotu hlavičky pro `ZUMO-API-VERSION`. Pokud služba přijme této hlavičky nebo hodnotu řetězce dotazu, bude vrácena chyba, pokud jste explicitně zvolili odhlašování, jak je popsáno výše.

### <a name="MobileServicesClients"></a> Mobilní *služby* klientskou sadu SDK
| Klientské platformy | Verze | Hodnota záhlaví verze |
| --- | --- | --- |
| Spravované klientské (Windows, Xamarin) |[1.3.2](https://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.2) |neuvedeno |
| iOS |[2.2.2](http://aka.ms/gc6fex) |neuvedeno |
| Android |[2.0.3](https://go.microsoft.com/fwLink/?LinkID=280126) |neuvedeno |
| HTML |[1.2.7](http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js) |neuvedeno |

### <a name="mobile-services-server-sdks"></a>Mobilní *služby* server sady SDK
| Platforma serveru | Verze | Přijatá verze hlavičky |
| --- | --- | --- |
| .NET |[WindowsAzure.MobileServices.Backend.* Version 1.0.x](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend/) |** Žádné záhlaví verze ** |
| Node.js |(už brzy) |**Žádná hlavička verze** |

<!-- TODO: add Node npm version -->

### <a name="behavior-of-mobile-services-backends"></a>Chování back-EndY mobilní služby
| ZUMO-API-VERSION | Hodnota MS_SkipVersionCheck | Odpověď |
| --- | --- | --- |
| Neuvedeno |Všechny |200 - OK |
| Libovolná hodnota |True |200 - OK |
| Libovolná hodnota |False nebo nebyla zadána |400 – Chybný požadavek |

## <a name="2.0.0"></a>Azure Mobile Apps klienta a serveru
### <a name="MobileAppsClients"></a> Mobilní *aplikace* klientskou sadu SDK
Kontrola verze byla zavedena spuštění v následujících verzích klienta SDK pro **Azure Mobile Apps**:

| Klientské platformy | Verze | Hodnota záhlaví verze |
| --- | --- | --- |
| Spravované klientské (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](http://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](http://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

<!-- TODO: add HTML version when released -->

### <a name="mobile-apps-server-sdks"></a>Mobilní *aplikace* server sady SDK
Kontrola verze je součástí následující verze sady SDK serveru:

| Platforma serveru | Sada SDK | Přijatá verze hlavičky |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Chování back-EndY mobilní aplikace
| ZUMO-API-VERSION | Hodnota MS_SkipVersionCheck | Odpověď |
| --- | --- | --- |
| x.y.z nebo hodnota Null. |True |200 - OK |
| Null |False nebo nebyla zadána |400 – Chybný požadavek |
| 1.x.y |False nebo nebyla zadána |400 – Chybný požadavek |
| 2.0.0-2.x.y |False nebo nebyla zadána |200 - OK |
| 3.0.0-3.x.y |False nebo nebyla zadána |400 – Chybný požadavek |

## <a name="next-steps"></a>Další kroky
* [migrace mobilní služby Azure App Service]

[Klienti Mobile Services]: #MobileServicesClients
[Klienti Mobile Apps]: #MobileAppsClients


[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[migrace mobilní služby Azure App Service]: app-service-mobile-migrating-from-mobile-services.md
