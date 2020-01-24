---
title: Diagnostika chyb pomocí služby připojené k Azure AD (Visual Studio)
description: Služba Active Directory Connect zjistila nekompatibilní typ ověřování.
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
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76699967"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Diagnostikování chyb pomocí Azure Active Directory připojené služby

Při zjišťování dřívějšího ověřovacího kódu zjistila Azure Active Directory připojená služba nekompatibilní typ ověřování.

Aby bylo možné správně zjistit předchozí ověřovací kód v projektu, je nutné projekt znovu sestavit. Pokud se zobrazí tato chyba a v projektu nemáte předchozí ověřovací kód, znovu ho Sestavte a zkuste to znovu.

## <a name="project-types"></a>Typy projektů

Připojená služba kontroluje typ projektu, který vyvíjíte, aby mohl vložit správnou logiku ověřování do projektu. Pokud existuje nějaký kontroler, který je odvozen od `ApiController` v projektu, projekt se považuje za projekt WebAPI. Pokud jsou v projektu pouze řadiče odvozené z `MVC.Controller`, projekt se považuje za projekt MVC. Připojená služba nepodporuje žádný jiný typ projektu.

## <a name="compatible-authentication-code"></a>Kompatibilní ověřovací kód

Připojená služba také kontroluje nastavení ověřování, která byla dříve nakonfigurovaná nebo jsou kompatibilní se službou. Pokud jsou všechna nastavení přítomná, považuje se za případ, že se to bude opakovat a připojená služba otevře zobrazení nastavení.  Pokud jsou k dispozici pouze některá z těchto nastavení, je považována za případ chyby.

V projektu MVC připojená služba kontroluje všechna následující nastavení, která jsou výsledkem předchozího použití služby:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Připojená služba také kontroluje jakékoli z následujících nastavení v projektu webového rozhraní API, které je výsledkem předchozího použití služby:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>Nekompatibilní ověřovací kód

Nakonec se připojená služba pokusí zjistit verze ověřovacího kódu, který byl nakonfigurován s předchozími verzemi sady Visual Studio. Pokud se tato chyba zobrazila, znamená to, že váš projekt obsahuje nekompatibilní typ ověřování. Připojená služba detekuje následující typy ověřování z předchozích verzí sady Visual Studio:

* Ověřování systému Windows
* Jednotlivé uživatelské účty
* Účty organizace

K detekci ověřování systému Windows v projektu MVC je připojení vyhledáno elementu `authentication` v souboru `web.config`.

```xml
<configuration>
    <system.web>
        <authentication mode="Windows" />
    </system.web>
</configuration>
```

K detekci ověřování systému Windows v projektu webového rozhraní API je připojená služba Hledat element `IISExpressWindowsAuthentication` v souboru `.csproj` projektu:

```xml
<Project>
    <PropertyGroup>
        <IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication>
    </PropertyGroup>
</Project>
```

Aby bylo možné detekovat individuální ověřování uživatelských účtů, bude připojená služba hledat prvek balíčku v souboru `packages.config`.

```xml
<packages>
    <package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" />
</packages>
```

Pokud chcete zjistit starou formu ověřování účtu organizace, bude připojená služba Hledat následující prvek v`web.config`:

```xml
<configuration>
    <appSettings>
        <add key="ida:Realm" value="***" />
    </appSettings>
</configuration>
```

Chcete-li změnit typ ověřování, odeberte nekompatibilní typ ověřování a pokuste se přidat připojenou službu znovu.

Další informace najdete v tématu [scénáře ověřování pro Azure AD](authentication-scenarios.md).
