---
title: Diagnostika chyb s Azure Active Directory připojení služby
description: Služby active directory připojené zjistil typu nekompatibilní ověřování
services: active-directory
author: ghogen
manager: douge
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev
ms.openlocfilehash: 8cd15c59bbe536dba9adb6f44c07844eaf030b55
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Diagnostikování chyb pomocí služby Azure Active Directory připojené Service

Při zjišťování předchozí kód ověřování, Azure Active Directory připojit server zjistil typu nekompatibilní ověřování.

Ke zjištění správně předchozí ověřovacího kódu v projektu, musí být vytvořená projektu.  Pokud došlo k této chybě a nemáte předchozí ověřovacího kódu v projektu, znovu sestavte a zkuste to znovu.

## <a name="project-types"></a>Typy projektů

Připojená služba zkontroluje typu projektu, které vyvíjíte, takže ho můžete vložit správné ověřování do projektu. Pokud je každý kontroler, který je odvozen od `ApiController` v projektu projekt se považuje za WebAPI projektu. Pokud jsou pouze řadiče, které jsou odvozeny od `MVC.Controller` v projektu projekt se považuje za projektu MVC. Připojená služba nepodporuje žádným jiným typem projektu.

## <a name="compatible-authentication-code"></a>Kompatibilní ověřovací kód

Připojené služby také zkontroluje nastavení ověřování, které byly dříve nakonfigurovány nebo jsou kompatibilní se službou. Pokud jsou v něm všechna nastavení, bude považován za vícenásobně případu a otevře připojené služby zobrazit nastavení.  Pokud jenom některá nastavení jsou v něm, bude považován za případ k chybě.

V projektu MVC připojená služba zkontroluje pro žádné z následujících nastavení, které jsou výsledkem předchozí pomocí služby:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Kromě toho připojená služba zkontroluje pro žádné z následujících nastavení v projektu webového rozhraní API, které jsou výsledkem předchozí pomocí služby:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>Nekompatibilní ověřovací kód

Ke zjištění verzích ověřovací kód, které byly nakonfigurovány s předchozími verzemi sady Visual Studio se pokusí připojené služby. Pokud se tato chyba, znamená to, že váš projekt obsahuje typu nekompatibilní ověřování. Připojené služby rozpozná následující typy ověřování z předchozích verzí sady Visual Studio:

* Ověřování systému Windows
* Jednotlivých uživatelských účtů
* Účty organizace

Ke zjištění ověřování systému Windows v projektu aplikace MVC, připojených hledá `authentication` element ve vaší `web.config` souboru.

```xml
<configuration>
    <system.web>
        <span style="background-color: yellow"><authentication mode="Windows" /></span>
    </system.web>
</configuration>
```

Ke zjištění ověřování systému Windows v projektu webového rozhraní API, hledá připojená služba `IISExpressWindowsAuthentication` element ve vašem projektu `.csproj` souboru:

```xml
<Project>
    <PropertyGroup>
        <span style="background-color: yellow"><IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication></span>
    </PropertyGroup>
</Project>
```

Ke zjištění ověřování jednotlivých uživatelských účtů, připojené služby hledá v elementu balíčku vaší `packages.config` souboru.

```xml
<packages>
    <span style="background-color: yellow"><package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /></span>
</packages>
```

Ke zjištění staré formu ověřování účtu organizace, hledá připojená služba následující element v`web.config`:

```xml
<configuration>
    <appSettings>
        <span style="background-color: yellow"><add key="ida:Realm" value="***" /></span>
    </appSettings>
</configuration>
```

Chcete-li změnit typ ověřování, odeberte typ nekompatibilní ověřování a znovu zkuste přidat službu připojené.

Další informace najdete v tématu [scénáře ověřování pro Azure AD](active-directory-authentication-scenarios.md).