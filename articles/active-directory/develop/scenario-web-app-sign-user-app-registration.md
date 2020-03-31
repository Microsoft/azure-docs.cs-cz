---
title: Registrace webové aplikace, která se připisuje k uživatelům – platforma microsoft identit | Azure
description: Přečtěte si, jak zaregistrovat webovou aplikaci, která se připisuje uživatelům.
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f2513b1fee022199f040ba0dcf83110c0b3b1365
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701565"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>Webová aplikace, která se připisuje uživatelům: Registrace aplikace

Tento článek vysvětluje podrobnosti o registraci aplikace pro webovou aplikaci, která se připisuje uživatelům.

Chcete-li zaregistrovat přihlášku, můžete použít:

- Webová [aplikace se rychle spustí](#register-an-app-by-using-the-quickstarts). Kromě toho, že skvělé první zkušenosti s vytvářením aplikace, rychlé starty na portálu Azure obsahují tlačítko s názvem **Provést tuto změnu pro mě**. Pomocí tohoto tlačítka můžete nastavit vlastnosti, které potřebujete, a to i pro existující aplikaci. Hodnoty těchto vlastností budete muset přizpůsobit vlastnímu případu. Zejména adresa URL webového rozhraní API pro vaši aplikaci se pravděpodobně bude lišit od navrhovaného výchozího nastavení, což bude mít vliv také na identifikátor URI pro odhlášení.
- Portál Azure k [ruční registraci aplikace](#register-an-app-by-using-the-azure-portal).
- Nástroje PowerShellu a příkazového řádku.

## <a name="register-an-app-by-using-the-quickstarts"></a>Registrace aplikace pomocí rychlých startů

Tyto odkazy můžete použít k zavádění vytvoření webové aplikace:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>Registrace aplikace pomocí portálu Azure

> [!NOTE]
> Portál, který se má použít, se liší v závislosti na tom, jestli vaše aplikace běží ve veřejném cloudu Microsoft Azure nebo v národním nebo suverénním cloudu. Další informace naleznete v tématu [Národní cloudy](./authentication-national-cloud.md#app-registration-endpoints).


1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft. Případně se přihlaste k portálu Azure, který si vyberete pro národní cloud.
1. Pokud váš účet umožňuje přístup k více než jednomu tenantovi, vyberte svůj účet v pravém horním rohu. Potom nastavte relaci portálu na požadovaný klient Azure Active Directory (Azure AD).
1. V levém podokně vyberte službu **Azure Active Directory** a pak vyberte Registrace >  **aplikací****Nová registrace**.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:
   1. Zvolte podporované typy účtů pro vaši aplikaci. (Viz [Podporované typy účtů](./v2-supported-account-types.md).)
   1. V části **Název** zadejte smysluplný název aplikace, který se uživatelům aplikace zobrazí. Zadejte například **aspNetCore-WebApp**.
   1. Do **identifikátoru URI přesměrování**přidejte typ aplikace a cíl identifikátoru URI, který bude po úspěšném ověřování přijímat vrácené odpovědi tokenů. Zadejte například . **https://localhost:44321** Potom vyberte **Registrovat**.
1. Vyberte nabídku **Ověřování** a přidejte následující informace:
   1. Do pole Adresa **https://localhost:44321/signin-oidc** URL **odpovědi**přidejte typ **Web**.
   1. V části **Upřesnit nastavení** nastavte adresu **https://localhost:44321/signout-oidc**URL **odhlášení** na adresu .
   1. V části **Implicitní udělení** vyberte **Tokeny ID**.
   1. Vyberte **Uložit**.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

1. Po zobrazení **stránky Registrovat přihlášku** zadejte registrační údaje vaší přihlášky:
   1. Zvolte podporované typy účtů pro vaši aplikaci. (Viz [Podporované typy účtů](./v2-supported-account-types.md).)
   1. V části **Název** zadejte smysluplný název aplikace, který se uživatelům aplikace zobrazí. Zadejte například **MailApp-openidconnect-v2**.
   1. V části **Přesměrování identifikátoru URI (volitelné)** vyberte v poli se seznamem **možnost Web** a zadejte následující identifikátor URI přesměrování: **https://localhost:44326/**.
1. Výběrem možnosti **Registrovat** aplikaci vytvořte.
1. Vyberte nabídku **Ověřování.**
1. V části **Upřesnit nastavení** | **implicitní udělení** vyberte **Tokeny ID**. Tato ukázka vyžaduje [implicitní tok grantu,](v2-oauth2-implicit-grant-flow.md) který má být povolen pro přihlášení uživatele.
1. Vyberte **Uložit**.

# <a name="java"></a>[Java](#tab/java)

1. Po zobrazení **stránky Registrovat aplikaci** zadejte zobrazovaný název aplikace. Zadejte například **java-webapp**.
1. Vyberte **Účty v libovolném organizačním adresáři a osobních účtech Microsoft (např. skype, xbox, Outlook.com)** a pak vyberte **Webová aplikace / ROZHRANÍ API** pro typ **aplikace**.
1. Chcete-li aplikaci zaregistrovat, vyberte **možnost Registrovat.**
1. V levé nabídce vyberte **Možnost Ověřování**. V části **Přesměrovat identifikátory URI**vyberte **možnost Web**.

1. Zadejte dvě identifikátory URI přesměrování: jednu pro přihlašovací stránku a jednu pro stránku grafu. Pro obě použijte stejný hostitel a číslo portu, následovaný **/msal4jsample/secure/aad** pro přihlašovací stránku a **msal4jsample/graph/me** pro stránku s informacemi o uživateli.

   Ve výchozím nastavení ukázka používá:

   - **http://localhost:8080/msal4jsample/secure/aad**
   - **http://localhost:8080/msal4jsample/graph/me**

  Potom vyberte **Uložit**.

1. V nabídce **vyberte & tajné kódy.**
1. V části **Tajné klíče klienta** vyberte **Nový tajný klíč klienta**a potom:

   1. Zadejte popis klíče.
   1. Vyberte dobu trvání klíče **Do 1 roku**.
   1. Vyberte **Přidat**.
   1. Když se zobrazí hodnota klíče, zkopírujte ji na později. Tato hodnota nebude znovu zobrazena ani nebude moci být načíst jiným způsobem.

# <a name="python"></a>[Python](#tab/python)

1. Po zobrazení **stránky Registrovat přihlášku** zadejte registrační údaje vaší přihlášky:
   1. V části **Název** zadejte smysluplný název aplikace, který se uživatelům aplikace zobrazí. Zadejte například **python-webapp**.
   1. Změňte **podporované typy účtů** na účty v **libovolném organizačním adresáři a osobních účtech Microsoftu (např Outlook.com.**
   1. V části **Přesměrování identifikátoru URI (volitelné)** vyberte v poli se seznamem **možnost Web** a zadejte následující identifikátor URI přesměrování: **http://localhost:5000/getAToken**.
1. Výběrem možnosti **Registrovat** aplikaci vytvořte.
1. Na stránce **Přehled** aplikace najděte hodnotu **ID aplikace (klienta)** a zaznamenejte ji na později. Budete ji potřebovat ke konfiguraci konfiguračního souboru sady Visual Studio pro tento projekt.
1. V levé nabídce vyberte **možnost Certifikáty & tajných kódů**.
1. V části **Tajné klíče klienta** vyberte **Nový tajný klíč klienta**a potom:

   1. Zadejte popis klíče.
   1. Vyberte klíčovou dobu trvání **Do 1 roku**.
   1. Vyberte **Přidat**.
   1. Když se zobrazí hodnota klíče, zkopírujte ji. Budete ho potřebovat později.
---

## <a name="register-an-app-by-using-powershell"></a>Registrace aplikace pomocí PowerShellu

> [!NOTE]
> V současné době Azure AD PowerShell vytváří aplikace s pouze následující podporované typy účtů:
>
> - MyOrg (pouze účty v tomto organizačním adresáři)
> - AnyOrg (účty v libovolném organizačním adresáři)
>
> Můžete vytvořit aplikaci, která se uživatelům přihlašuje k jejich osobním účtům Microsoft (například Skype, Xbox nebo Outlook.com). Nejprve vytvořte víceklientské aplikace. Podporované typy účtů jsou účty v libovolném organizačním adresáři. Potom změňte `signInAudience` vlastnost v manifestu aplikace z portálu Azure. Další informace naleznete v [kroku 1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) v kurzu ASP.NET Core. Tento krok můžete zobecnit na webové aplikace v libovolném jazyce.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Konfigurace kódu aplikace](scenario-web-app-sign-user-app-configuration.md)
