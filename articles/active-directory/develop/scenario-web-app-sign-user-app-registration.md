---
title: Webová aplikace, která přihlašuje uživatele (registrace aplikací) – platforma Microsoftu identity
description: Informace o tom, jak vytvořit webovou aplikaci, která přihlašuje uživatele (registrace aplikace)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bdf04014d7b0382913c0a4094f7474686658441
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086706"
---
# <a name="web-app-that-signs-in-users---app-registration"></a>Webová aplikace, která podepisuje uživatele – registrace aplikace

Tato stránka vysvětluje konkrétní registraci aplikace pro webovou aplikaci, která přihlašuje uživatele.

K registraci aplikace můžete použít:

- V případě, že se tato [Webová aplikace](#register-an-app-using-the-quickstarts) bude při vytváření aplikace nacházet jako skvělé, rychlé starty v Azure Portal obsahují tlačítko s názvem **udělat tuto změnu pro mě**. Pomocí tohoto tlačítka můžete nastavit vlastnosti, které potřebujete, i pro existující aplikaci. Hodnoty těchto vlastností budete muset přizpůsobit na vlastní případ. Konkrétně adresa URL webového rozhraní API vaší aplikace se pravděpodobně liší od navrhované výchozí, což ovlivní také identifikátor URI pro odhlášení.
- Azure Portal k [ruční registraci aplikace](#register-an-app-using-azure-portal)
- PowerShell a nástroje příkazového řádku

## <a name="register-an-app-using-the-quickstarts"></a>Registrace aplikace pomocí rychlých startů

Pokud přejdete na tento odkaz, můžete vytvořit Bootstrap vytváření webové aplikace:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

### <a name="register-an-app-using-azure-portal"></a>Registrace aplikace pomocí Azure Portal

> [!NOTE]
> portál, který se má použít, se liší v závislosti na tom, jestli je vaše aplikace spuštěná ve veřejném cloudu Microsoft Azure nebo v národním nebo svrchovaném cloudu. Další informace najdete v tématu [národní cloudy](./authentication-national-cloud.md#app-registration-endpoints) .

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft. Případně se přihlaste k národnímu cloudu Azure Portal podle vlastního výběru.
1. Pokud vám váš účet poskytne přístup k více než jednomu klientovi, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** a pak vyberte **Registrace aplikací** > **Nová registrace**.
1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:
   1. Vyberte podporované typy účtů pro vaši aplikaci (viz [podporované typy účtů](./v2-supported-account-types.md)).
   1. V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `AspNetCore-WebApp`.
   1. Do pole **identifikátor URI pro přesměrování**přidejte typ aplikace a cíl identifikátoru URI, který bude po úspěšném ověření akceptovat vrácené odpovědi na tokeny. Například, `https://localhost:44321/`.  Vyberte **Zaregistrovat**.
1. Vyberte nabídku **ověřování** a přidejte následující informace:
   1. Do **adresy URL odpovědi**přidejte `https://localhost:44321/signin-oidc`.
   1. V části **Upřesnit nastavení** nastavte **adresu URL pro odhlášení** na `https://localhost:44321/signout-oidc`.
   1. V části **implicitní udělení**ověřte **tokeny ID**.
   1. Vyberte **Uložit**.

### <a name="register-an-app-using-powershell"></a>Registrace aplikace pomocí PowerShellu

> [!NOTE]
> V současné době Azure AD PowerShell vytvoří pouze aplikace s následujícími podporovanými typy účtů:
>
> - MyOrg (jenom účty v této organizační složce)
> - AnyOrg (účty v jakékoli organizační složce).
>
> Pokud chcete vytvořit aplikaci, která přihlašuje uživatele pomocí svých osobních účtů Microsoft (např. Skype, XBox, Outlook.com), můžete nejdřív vytvořit víceklientské aplikace (podporované typy účtů = účty v jakémkoli adresáři organizace) a pak změnit `signInAudience` vlastnost v manifestu aplikace z Azure Portal. To je vysvětleno v podrobnostech v kroku [1,3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) kurzu ASP.NET Core (a lze je zobecnit pro webové aplikace v jakémkoli jazyce).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Konfigurace kódu aplikace](scenario-web-app-sign-user-app-configuration.md)
