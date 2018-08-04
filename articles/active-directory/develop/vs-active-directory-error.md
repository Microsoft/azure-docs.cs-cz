---
title: Jak diagnostikovat chyby v Azure Active Directory připojenou službu
description: Připojené služby active directory zjistila typu nekompatibilní ověřování
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
ms.openlocfilehash: f4fc7d11516d2ebf5e091d519333b3cf1fd09c47
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495151"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Diagnostikování chyb pomocí Azure Active Directory připojenou službu

Při zjišťování předchozí kód ověřování Azure Active Directory připojit server zjistil typ nekompatibilní ověřování.

Správně detekovat předchozí ověřovacího kódu v projektu, musí být sestaveny jako projekt.  Pokud k této chybě došlo ve vašem projektu nemáte předchozí ověřovacího kódu, sestavení a zkuste to znovu.

## <a name="project-types"></a>Typy projektů

Připojená služba zkontroluje typ projektu, kterou vyvíjíte, takže ji můžete vložit správné ověřovací logiku do projektu. Pokud je každý kontroler, který je odvozen od `ApiController` v projektu, projektu se považuje za projektem WebAPI. Pokud existují jenom řadiče, které jsou odvozeny z `MVC.Controller` v projektu, projektu se považuje za projektu aplikace MVC. Připojená služba nepodporuje jakýkoli jiný typ projektu.

## <a name="compatible-authentication-code"></a>Kompatibilní ověřovacího kódu

Připojenou službu také kontroluje nastavení ověřování, které byly dříve nakonfigurovány nebo jsou kompatibilní se službou. Pokud všechna nastavení jsou k dispozici, bude považován za vícenásobně případ a otevře připojená služba zobrazí nastavení.  Pokud pouze některá nastavení jsou k dispozici, bude považován za případ k chybě.

V projektu aplikace MVC kontroluje připojenou službu pro některý z následujících nastavení, které jsou výsledkem předchozího používání služby:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Kromě toho připojené služby kontroluje pro některý z následujících nastavení v projektu webového rozhraní API, které jsou výsledkem předchozího používání služby:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>Nekompatibilní ověřovacího kódu

Verze ověřovacího kódu, které byly nakonfigurovány s předchozími verzemi sady Visual Studio se pokusí připojenou službu. Pokud se zobrazí tato chyba, znamená to, že váš projekt obsahuje typ nekompatibilní ověřování. Připojená služba rozpoznává následující typy ověřování z předchozích verzí sady Visual Studio:

* Ověřování systému Windows
* Individuální uživatelské účty
* Účty organizací

Ke zjištění ověřování Windows v projektu aplikace MVC, bude připojená hledá `authentication` prvek v vaše `web.config` souboru.

```xml
<configuration>
    <system.web>
        <span style="background-color: yellow"><authentication mode="Windows" /></span>
    </system.web>
</configuration>
```

Ke zjištění ověřování Windows v projektu webového rozhraní API, hledá připojená služba `IISExpressWindowsAuthentication` element ve vašem projektu `.csproj` souboru:

```xml
<Project>
    <PropertyGroup>
        <span style="background-color: yellow"><IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication></span>
    </PropertyGroup>
</Project>
```

Ke zjištění ověřování individuálních uživatelských účtů, hledá připojená služba elementu balíčku v vaše `packages.config` souboru.

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

Chcete-li změnit typ ověřování, odebrání nekompatibilních ověřování typu a zkuste přidat připojenou službu znovu.

Další informace najdete v tématu [scénáře ověřování pro službu Azure AD](authentication-scenarios.md).