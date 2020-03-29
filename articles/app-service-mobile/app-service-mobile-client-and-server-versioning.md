---
title: Správa verzí sady SDK klienta a serveru
description: Seznam sad SDK klienta a kompatibilita s verzemi sady SDK serveru pro mobilní služby a mobilní aplikace Azure.
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: a9ba442c00ec2498139ee34a1ff7497c98f17ede
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293488"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Správa verzí klienta a serveru v mobilních aplikacích a mobilních službách

Nejnovější verze mobilních služeb Azure je funkce **Mobilní aplikace** služby Azure App Service.

Sady SDK pro klienty a servery Mobile Apps jsou původně založeny na klientských sadách Mobile Services a serverech SDK, které jsou původně založeny na klientských službách a serverech Mobile Services, ale *nejsou* vzájemně kompatibilní.
To znamená, že je nutné použít sadu SDK klienta *mobilních aplikací* se sadou SDK serveru *mobilních aplikací* a podobně pro *mobilní služby*. Tato smlouva je vynucena prostřednictvím speciální hodnoty záhlaví používané klientem a serverem Sady SDK . `ZUMO-API-VERSION`

Poznámka: Vždy, když tento dokument odkazuje na back-end *mobilních služeb,* nemusí být nutně hostován v mobilních službách. Nyní je možné migrovat mobilní službu spustit na App Service bez jakýchkoli změn kódu, ale služba by stále používat *mobilní služby* SDK verze.

## <a name="header-specification"></a>Specifikace záhlaví
Klíč `ZUMO-API-VERSION` může být zadán v hlavičce PROTOKOLU HTTP nebo v řetězci dotazu. Hodnota je řetězec verze ve formuláři **x.y.z**.

Například:

`GET https://service.azurewebsites.net/tables/TodoItem`

HLAVIČKY: ZUMO-API-VERZE: 2.0.0

`POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0`

## <a name="opting-out-of-version-checking"></a>Odhlášení z kontroly verze
Z kontroly verzí se můžete odhlásit nastavením hodnoty **true** pro nastavení aplikace **MS_SkipVersionCheck**. Zadejte to buď ve vašem web.config nebo v části Nastavení aplikace na webu Azure Portal.

> [!NOTE]
> Mezi mobilními službami a mobilními aplikacemi dochází k řadě změn chování, zejména v oblastech offline synchronizace, ověřování a nabízených oznámení. Po dokončení testování byste se měli odhlásit z kontroly verzí, abyste zajistili, že tyto změny chování neporuší funkčnost aplikace.

## <a name="azure-mobile-apps-client-and-server"></a><a name="2.0.0"></a>Klient a server mobilních aplikací Azure
### <a name="mobile-apps-client-sdks"></a><a name="MobileAppsClients"></a>Sady SDK klienta mobilních *aplikací*
Kontrola verzí byla zavedena počínaje následujícími verzemi sady SDK klienta pro **mobilní aplikace Azure**:

| Platforma klienta | Version | Hodnota záhlaví verze |
| --- | --- | --- |
| Spravovaný klient (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

### <a name="mobile-apps-server-sdks"></a>Sady SDK serveru *mobilních aplikací*
Kontrola verzí je součástí následujících verzí sady SDK serveru:

| Serverová platforma | Sada SDK | Hlavička přijaté verze |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azurové mobilní aplikace)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Chování back-endů mobilních aplikací
| ZUMO-API-VERZE | Hodnota MS_SkipVersionCheck | Odpověď |
| --- | --- | --- |
| x.y.z nebo Null |True |200 - OK |
| Null |False/Není zadáno |400 – Chybný požadavek |
| 1.x.y |False/Není zadáno |400 – Chybný požadavek |
| 2.0.0-2.x.y |False/Není zadáno |200 - OK |
| 3.0.0-3.x.y |False/Není zadáno |400 – Chybný požadavek |

[Mobile Services clients]: #MobileServicesClients
[Mobile Apps clients]: #MobileAppsClients
[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
