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
ms.openlocfilehash: 7ab7c3699abdd5c094b1b14cd53f76023fa8c1ac
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309600"
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

## <a name="register-an-app-using-azure-portal"></a>Registrace aplikace pomocí Azure Portal

> [!NOTE]
> portál, který se má použít, se liší v závislosti na tom, jestli je vaše aplikace spuštěná ve veřejném cloudu Microsoft Azure nebo v národním nebo svrchovaném cloudu. Další informace najdete v tématu [národní cloudy](./authentication-national-cloud.md#app-registration-endpoints) .


1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft. Případně se přihlaste k národnímu cloudu Azure Portal podle vlastního výběru.
1. Pokud vám váš účet poskytne přístup k více než jednomu klientovi, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** a pak vyberte **Registrace aplikací** > **Nová registrace**.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

4. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:
   1. Vyberte podporované typy účtů pro vaši aplikaci (viz [podporované typy účtů](./v2-supported-account-types.md)).
   1. V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `AspNetCore-WebApp`.
   1. Do pole **identifikátor URI pro přesměrování**přidejte typ aplikace a cíl identifikátoru URI, který bude po úspěšném ověření akceptovat vrácené odpovědi na tokeny. Například, `https://localhost:44321/`.  Vyberte **Zaregistrovat**.
1. Vyberte nabídku **ověřování** a přidejte následující informace:
   1. V části **Adresa URL odpovědi**přidejte `https://localhost:44321/signin-oidc` typ Web.
   1. V části **Upřesnit nastavení** nastavte **adresu URL pro odhlášení** na `https://localhost:44321/signout-oidc`.
   1. V části **implicitní udělení**ověřte **tokeny ID**.
   1. Vyberte **Uložit**.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

4. Když se zobrazí **stránka Registrace aplikace**, zadejte registrační informace vaší aplikace:
   1. Vyberte podporované typy účtů pro vaši aplikaci (viz [podporované typy účtů](./v2-supported-account-types.md)).
   - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `MailApp-openidconnect-v2`.
   - V části identifikátor URI přesměrování (volitelné) vyberte v poli se seznamem možnost **Web** a zadejte následující identifikátory URI pro přesměrování: `https://localhost:44326/`.
1. Výběrem možnosti **Registrovat** aplikaci vytvořte.
1. Vyberte nabídku **ověřování** a přidejte následující informace:
   - V části **Upřesnit nastavení** | **implicitní udělení** **ID ověřte tokeny ID** , protože tato ukázka vyžaduje, aby byl [tok implicitního udělení](v2-oauth2-implicit-grant-flow.md) povolen pro přihlášení uživatele.
1. Vyberte **Uložit**.

# <a name="javatabjava"></a>[Java](#tab/java)

4. Jakmile se zobrazí **Stránka Registrovat aplikaci** , zadejte popisný název aplikace, například "Java-WebApp", vyberte možnost "účty v jakémkoli adresáři organizace a osobní účty Microsoft (např. Skype, Xbox, Outlook.com)" a vyberte možnost Webová aplikace /Rozhraní API jako *Typ aplikace*.
1. Kliknutím na **Registrovat** zaregistrujete aplikaci.
1. V nabídce na levé straně klikněte na **ověřování**a v části *identifikátory URI pro přesměrování*vyberte web. Budete muset zadat dva různé identifikátory URI pro přesměrování: jednu pro stránku přihlášení a jednu pro stránku Uživatelé grafu. V obou případech byste měli použít stejný hostitel a číslo portu, potom za ním "/msal4jsample/Secure/AAD" pro přihlašovací stránku a "msal4jsample/Graph/uživatelé" pro stránku Uživatelé.
 Ve výchozím nastavení ukázka používá: 

    - `http://localhost:8080/msal4jsample/secure/aad`. 
    - `http://localhost:8080/msal4jsample/graph/users`

Klikněte na **Uložit**.

# <a name="pythontabpython"></a>[Python](#tab/python)

4. Když se zobrazí **stránka Registrace aplikace**, zadejte registrační informace vaší aplikace:
   - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `python-webapp`.
   - Změňte **podporované typy účtů** na **účty v jakémkoli adresáři organizace a na osobních účtech Microsoft (např. Skype, Xbox, Outlook.com)** .
   - V části identifikátor URI přesměrování (volitelné) vyberte v poli se seznamem možnost **Web** a zadejte následující identifikátory URI pro přesměrování: `http://localhost:5000/getAToken`.
1. Výběrem možnosti **Registrovat** aplikaci vytvořte.
1. Na stránce **Přehled** aplikace vyhledejte hodnotu **ID aplikace (klienta)** a zaznamenejte ji pro pozdější použití. Budete ho potřebovat ke konfiguraci konfiguračního souboru sady Visual Studio pro tento projekt.
1. Na stránce Přehled aplikace vyberte část **ověřování** .
   - V části **Upřesnit nastavení** nastavte **adresu URL pro odhlášení** na`http://localhost:5000/logout`
1. Vyberte **Uložit**.

---

## <a name="register-an-app-using-powershell"></a>Registrace aplikace pomocí PowerShellu

> [!NOTE]
> V současné době Azure AD PowerShell vytvoří pouze aplikace s následujícími podporovanými typy účtů:
>
> - MyOrg (jenom účty v této organizační složce)
> - AnyOrg (účty v jakékoli organizační složce).
>
> Pokud chcete vytvořit aplikaci, která přihlašuje uživatele pomocí svých osobních účtů Microsoft (např. Skype, XBox, Outlook.com), můžete nejdřív vytvořit víceklientské aplikace (podporované typy účtů = účty v jakémkoli adresáři organizace) a pak změnit `signInAudience` vlastnost v manifestu aplikace z Azure Portal. To je vysvětleno v podrobnostech v kroku [1,3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) kurzu ASP.NET Core (a lze je zobecnit pro webové aplikace v jakémkoli jazyce).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Konfigurace kódu aplikace](scenario-web-app-sign-user-app-configuration.md)
