---
title: Diagnostika chyb pomocí připojené služby Azure AD (Visual Studio)
description: Služba připojená k aktivnímu zpoza služby zjistila nekompatibilní typ ověřování
author: ghogen
manager: jillfra
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: a6ec31f0d60c7f6e3737dc4042b05a6d8bf3dd5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76699967"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Diagnostika chyb pomocí připojené služby Azure Active Directory

Při zjišťování předchozího ověřovacího kódu zjistila připojená služba Azure Active Directory nekompatibilní typ ověřování.

Chcete-li správně zjistit předchozí ověřovací kód v projektu, projekt musí být znovu sestaven. Pokud se zobrazí tato chyba a nemáte předchozí ověřovací kód v projektu, znovu sestavit a zkuste to znovu.

## <a name="project-types"></a>Typy projektů

Připojená služba kontroluje typ projektu, který vyvíjíte, aby mohla do projektu vložit správnou logiku ověřování. Pokud existuje řadič, který je `ApiController` odvozen z v projektu, projekt je považován za projekt WebAPI. Pokud existují pouze řadiče, `MVC.Controller` které jsou odvozeny z v projektu, projekt je považován za projekt MVC. Připojená služba nepodporuje žádný jiný typ projektu.

## <a name="compatible-authentication-code"></a>Kompatibilní ověřovací kód

Připojená služba také kontroluje nastavení ověřování, která byla dříve nakonfigurována nebo jsou kompatibilní se službou. Pokud jsou k dispozici všechna nastavení, je to považováno za případ opětovného účastníka a připojená služba se otevře zobrazí nastavení.  Pokud jsou k dispozici pouze některá nastavení, považuje se za chybový případ.

V projektu MVC související služba zkontroluje libovolné z následujících nastavení, které vyplývají z předchozího použití služby:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Připojená služba také kontroluje některá z následujících nastavení v projektu webového rozhraní API, která jsou výsledkem předchozího použití služby:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>Nekompatibilní ověřovací kód

Nakonec se připojená služba pokusí zjistit verze ověřovacího kódu, které byly nakonfigurovány s předchozími verzemi sady Visual Studio. Pokud jste obdrželi tuto chybu, znamená to, že projekt obsahuje nekompatibilní typ ověřování. Připojená služba detekuje následující typy ověřování z předchozích verzí sady Visual Studio:

* Ověřování systému Windows
* Jednotlivé uživatelské účty
* Organizační účty

Chcete-li zjistit ověřování systému Windows v projektu `authentication` MVC, připojené hledá prvek v `web.config` souboru.

```xml
<configuration>
    <system.web>
        <authentication mode="Windows" />
    </system.web>
</configuration>
```

Chcete-li zjistit ověřování systému Windows v projektu `IISExpressWindowsAuthentication` webového rozhraní `.csproj` API, propojená služba hledá prvek v souboru projektu:

```xml
<Project>
    <PropertyGroup>
        <IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication>
    </PropertyGroup>
</Project>
```

Chcete-li zjistit ověřování jednotlivých uživatelských účtů, `packages.config` hledá připojená služba prvek balíčku v souboru.

```xml
<packages>
    <package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" />
</packages>
```

Chcete-li zjistit starou formu ověřování účtu organizace, propojená`web.config`služba hledá následující prvek v aplikaci :

```xml
<configuration>
    <appSettings>
        <add key="ida:Realm" value="***" />
    </appSettings>
</configuration>
```

Chcete-li změnit typ ověřování, odeberte nekompatibilní typ ověřování a zkuste přidat připojenou službu znovu.

Další informace naleznete v [tématu Scénáře ověřování pro Azure AD](authentication-scenarios.md).
