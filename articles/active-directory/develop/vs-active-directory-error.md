---
title: Diagnostika chyb pomocí služby připojené k Azure AD (Visual Studio)
description: Služba Active Directory Connect zjistila nekompatibilní typ ověřování.
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 7b437e3117540719c8c0adc5701ac1a5e934340b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88114468"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Diagnostikování chyb pomocí Azure Active Directory připojené služby

Při zjišťování dřívějšího ověřovacího kódu zjistila Azure Active Directory připojená služba nekompatibilní typ ověřování.

Aby bylo možné správně zjistit předchozí ověřovací kód v projektu, je nutné projekt znovu sestavit. Pokud se zobrazí tato chyba a v projektu nemáte předchozí ověřovací kód, znovu ho Sestavte a zkuste to znovu.

## <a name="project-types"></a>Typy projektů

Připojená služba kontroluje typ projektu, který vyvíjíte, aby mohl vložit správnou logiku ověřování do projektu. Pokud existuje nějaký kontroler, který je odvozen z `ApiController` projektu, projekt je považován za projekt WebAPI. Pokud jsou pouze řadiče, které jsou odvozeny z `MVC.Controller` projektu, projekt je považován za projekt MVC. Připojená služba nepodporuje žádný jiný typ projektu.

## <a name="compatible-authentication-code"></a>Kompatibilní ověřovací kód

Připojená služba také kontroluje nastavení ověřování, která byla dříve nakonfigurovaná nebo jsou kompatibilní se službou. Pokud jsou všechna nastavení přítomná, považuje se za případ, že se to bude opakovat a připojená služba otevře zobrazení nastavení.  Pokud jsou k dispozici pouze některá z těchto nastavení, je považována za případ chyby.

V projektu MVC připojená služba kontroluje všechna následující nastavení, která jsou výsledkem předchozího použití služby:

```xml
<add key="ida:ClientId" value="" />
<add key="ida:Tenant" value="" />
<add key="ida:AADInstance" value="" />
<add key="ida:PostLogoutRedirectUri" value="" />
```

Připojená služba také kontroluje jakékoli z následujících nastavení v projektu webového rozhraní API, které je výsledkem předchozího použití služby:

```xml
<add key="ida:ClientId" value="" />
<add key="ida:Tenant" value="" />
<add key="ida:Audience" value="" />
```

## <a name="incompatible-authentication-code"></a>Nekompatibilní ověřovací kód

Nakonec se připojená služba pokusí zjistit verze ověřovacího kódu, který byl nakonfigurován s předchozími verzemi sady Visual Studio. Pokud se tato chyba zobrazila, znamená to, že váš projekt obsahuje nekompatibilní typ ověřování. Připojená služba detekuje následující typy ověřování z předchozích verzí sady Visual Studio:

* Ověřování systému Windows
* Jednotlivé uživatelské účty
* Účty organizace

K detekci ověřování systému Windows v projektu MVC je připojen vzhled `authentication` prvku v `web.config` souboru.

```xml
<configuration>
    <system.web>
        <authentication mode="Windows" />
    </system.web>
</configuration>
```

K detekci ověřování systému Windows v projektu webového rozhraní API je připojená služba hledat `IISExpressWindowsAuthentication` prvek v `.csproj` souboru projektu:

```xml
<Project>
    <PropertyGroup>
        <IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication>
    </PropertyGroup>
</Project>
```

Aby bylo možné detekovat individuální ověřování uživatelských účtů, bude připojená služba hledat prvek balíčku v `packages.config` souboru.

```xml
<packages>
    <package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" />
</packages>
```

Pokud chcete zjistit starou formu ověřování účtu organizace, bude připojená služba Hledat následující prvek v `web.config` :

```xml
<configuration>
    <appSettings>
        <add key="ida:Realm" value="***" />
    </appSettings>
</configuration>
```

Chcete-li změnit typ ověřování, odeberte nekompatibilní typ ověřování a pokuste se přidat připojenou službu znovu.

Další informace najdete v tématu [scénáře ověřování pro Azure AD](./authentication-vs-authorization.md).