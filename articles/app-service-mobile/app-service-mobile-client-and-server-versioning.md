---
title: Správa verzí pro klientské a serverové sady SDK v Mobile Apps a Mobile Services | Microsoft Docs
description: Seznam klientských sad SDK a kompatibilita s verzemi serveru SDK pro Mobile Services a Azure Mobile Apps
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
ms.openlocfilehash: 7133e8bc7d04b3653b6b788347b7bc5176087f4c
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883470"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Správa verzí klienta a serveru v Mobile Apps a Mobile Services
Nejnovější verze služby Azure Mobile Services je **Mobile Apps** funkce Azure App Service.

Sady SDK pro Mobile Apps klienta a serveru jsou původně založené na těch, které jsou v Mobile Services , ale nejsou vzájemně kompatibilní.
To znamená, že musíte použít sadu SDK klienta *Mobile Apps* se sadou sdk serveru *Mobile Apps* a podobně pro *Mobile Services*. Tato smlouva se vynutila prostřednictvím speciální hodnoty hlavičky, `ZUMO-API-VERSION`kterou používají klientské a serverové sady SDK.

Poznámka: pokaždé, když tento dokument odkazuje na *Mobile Services* back-end, nemusí být nutně hostovaný na Mobile Services. Nyní je možné migrovat mobilní službu, aby běžela na App Service bez jakýchkoli změn kódu, ale služba stále používá *Mobile Services* verze SDK.

## <a name="header-specification"></a>Specifikace záhlaví
Klíč `ZUMO-API-VERSION` lze zadat buď v hlavičce protokolu HTTP, nebo v řetězci dotazu. Hodnota je řetězec verze ve formátu **x. y. z**.

Příklad:

ČTĚTE https://service.azurewebsites.net/tables/TodoItem

ZÁHLAVÍ ZUMO-API-VERSION: 2.0.0

SPUŠTĚNÍ https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Zrušení kontroly verze
Kontrolu verzí můžete odhlásit nastavením hodnoty **true** pro nastavení aplikace **MS_SkipVersionCheck**. Zadejte to buď v souboru Web. config, nebo v části nastavení aplikace Azure Portal.

> [!NOTE]
> Mezi Mobile Services a Mobile Apps se mění množství chování, zejména v oblasti offline synchronizace, ověřování a nabízených oznámení. Po kompletním testování byste měli odhlásit jenom kontrolu verzí, aby se zajistilo, že tyto změny chování nebudou přerušovat funkčnost vaší aplikace.

## <a name="2.0.0"></a>Klient a server Azure Mobile Apps
### <a name="MobileAppsClients"></a>Klientské sady SDK pro mobilní *aplikace*
Kontrola verze byla představena počínaje následujícími verzemi klientské sady SDK pro **Azure Mobile Apps**:

| Klientská platforma | Version | Hodnota hlavičky verze |
| --- | --- | --- |
| Spravovaný klient (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

### <a name="mobile-apps-server-sdks"></a>Sady SDK serveru Mobile *Apps*
Kontrola verze je zahrnutá v následujících verzích sady SDK serveru:

| Serverová platforma | Sada SDK | Hlavička přijaté verze |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Chování Mobile Apps back-endy
| ZUMO-API-VERSION | Hodnota MS_SkipVersionCheck | Odpověď |
| --- | --- | --- |
| x. y. z nebo null |Pravda |200 – OK |
| Null |Hodnota false/není zadána |400 – Chybný požadavek |
| 1. x. y |Hodnota false/není zadána |400 – Chybný požadavek |
| 2.0.0-2.x.y |Hodnota false/není zadána |200 – OK |
| 3.0.0-3.x.y |Hodnota false/není zadána |400 – Chybný požadavek |

[Mobile Services clients]: #MobileServicesClients
[Mobile Apps clients]: #MobileAppsClients
[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
