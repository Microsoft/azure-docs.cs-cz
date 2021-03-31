---
title: Registrace webové aplikace, která podepisuje uživatele | Azure
titleSuffix: Microsoft identity platform
description: Zjistěte, jak zaregistrovat webovou aplikaci, která se přihlásí uživatelům.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 920249aa252469c3db2be284fc010d775d04c921
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578273"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>Webová aplikace, která přihlašuje uživatele: registrace aplikace

Tento článek vysvětluje kroky registrace aplikací pro webovou aplikaci, která se přihlašuje uživatelům.

K registraci aplikace můžete použít:

- [Webové aplikace jsou rychlé starty](#register-an-app-by-using-the-quickstarts). Kromě skvělého prvního prostředí při vytváření aplikace budou rychlé starty v Azure Portal obsahovat tlačítko s názvem **udělat tuto změnu pro mě**. Pomocí tohoto tlačítka můžete nastavit vlastnosti, které potřebujete, i pro existující aplikaci. Hodnoty těchto vlastností můžete přizpůsobit na vlastní případ. Konkrétně adresa URL webového rozhraní API vaší aplikace se pravděpodobně liší od navrhované výchozí, což ovlivní také identifikátor URI pro odhlášení.
- Azure Portal k [registraci aplikace ručně](#register-an-app-by-using-the-azure-portal).
- PowerShell a nástroje příkazového řádku.

## <a name="register-an-app-by-using-the-quickstarts"></a>Registrace aplikace pomocí rychlých startů

Pomocí těchto odkazů můžete spustit vytvoření webové aplikace:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>Registrace aplikace pomocí Azure Portal

> [!NOTE]
> Portál, který se má použít, se liší v závislosti na tom, jestli vaše aplikace běží ve veřejném cloudu Microsoft Azure nebo v národním nebo svrchovaném cloudu. Další informace najdete v tématu [národní cloudy](./authentication-national-cloud.md#app-registration-endpoints).


1. Přihlaste se na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>. 
1. Máte-li přístup k více klientům, použijte filtr **adresář + odběr** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: v horní nabídce a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.
1. Vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Registrace aplikací**  >  **Nová registrace**.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:
   1. Zadejte **název** vaší aplikace, například `AspNetCore-WebApp` . Uživatel vaší aplikace může tento název zobrazit a později ho můžete změnit.
   1. Vyberte podporované typy účtů pro vaši aplikaci. (Viz [podporované typy účtů](./v2-supported-account-types.md).)
   1. Pro **identifikátor URI přesměrování** přidejte typ aplikace a cíl identifikátoru URI, který bude po úspěšném ověření akceptovat vrácené odpovědi na tokeny. Zadejte například `https://localhost:44321`.
   1. Vyberte **Zaregistrovat**.
1. V části **Spravovat** vyberte **ověřování** a přidejte následující informace:
   1. V části **Web** přidejte `https://localhost:44321/signin-oidc` jako **identifikátor URI přesměrování**.
   1. V **části Adresa URL pro odhlášení front-Channel** zadejte `https://localhost:44321/signout-oidc` .
   1. V části **implicitní udělení a hybridní toky** vyberte **tokeny ID**.
   1. Vyberte **Uložit**.
   
# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

1. Jakmile se zobrazí **Stránka Registrovat aplikaci** , zadejte informace o registraci vaší aplikace:
   1. Zadejte **název** vaší aplikace, například `MailApp-openidconnect-v2` . Uživatel vaší aplikace může tento název zobrazit a později ho můžete změnit.
   1. Vyberte podporované typy účtů pro vaši aplikaci. (Viz [podporované typy účtů](./v2-supported-account-types.md).)
   1. V části **identifikátor URI přesměrování (volitelné)** vyberte možnost **Web** v poli se seznamem a zadejte **identifikátor URI přesměrování** `https://localhost:44326/` .
   1. Výběrem možnosti **Registrovat** aplikaci vytvořte.
1. V části **Spravovat** vyberte **ověřování**.
1. V části **implicitní udělení a hybridní toky** vyberte **tokeny ID**. Tato ukázka vyžaduje, aby byl [tok implicitního udělení](v2-oauth2-implicit-grant-flow.md) povolen pro přihlášení uživatele.
1. Vyberte **Uložit**.

# <a name="java"></a>[Java](#tab/java)

1. Jakmile se zobrazí **Stránka Registrovat aplikaci** , zadejte informace o registraci vaší aplikace: 
    1. Zadejte **název** vaší aplikace, například `java-webapp` . Uživatel vaší aplikace může tento název zobrazit a později ho můžete změnit. 
    1. Vyberte **účty v libovolném organizačním adresáři a osobní účty Microsoft (např. Skype, Xbox, Outlook.com)**.
    1. Vyberte **Registrovat** pro registraci aplikace.
1. V části **Spravovat** vyberte **ověřování**  >  **Přidat platformu**.
1. Vyberte **Web**.
1. Pro **identifikátor URI přesměrování** zadejte stejný hostitel a číslo portu následovaný `/msal4jsample/secure/aad` přihlašovací stránkou. 
1. Vyberte **Konfigurovat**.
1. V části **Web** použijte hostitele a číslo portu následované `/msal4jsample/graph/me` jako **identifikátor URI přesměrování** pro stránku informace o uživateli.
Ve výchozím nastavení ukázka používá:
   - `http://localhost:8080/msal4jsample/secure/aad`
   - `http://localhost:8080/msal4jsample/graph/me`

1. Vyberte **Uložit**.
1. V části **Správa** vyberte **Certifikáty a tajné kódy**.
1. V části **tajné klíče klienta** vyberte **nový tajný klíč klienta** a potom:

   1. Zadejte popis klíče.
   1. Vyberte dobu trvání klíče **v 1 roce**.
   1. Vyberte **Přidat**.
   1. Jakmile se zobrazí hodnota klíče, zkopírujte ji pro pozdější verzi. Tato hodnota se znovu nezobrazí nebo ji nebude možné získat jakýmkoli jiným způsobem.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

1. Jakmile se zobrazí **Stránka Registrovat aplikaci** , zadejte informace o registraci vaší aplikace:
   1. Zadejte **název** vaší aplikace, například `node-webapp` . Uživatel vaší aplikace může tento název zobrazit a později ho můžete změnit.
   1. Změňte **podporované typy účtů** na **účty v jakémkoli adresáři organizace a na osobních účtech Microsoft (např. Skype, Xbox, Outlook.com)**.
   1. V části **identifikátor URI přesměrování (volitelné)** vyberte v poli se seznamem možnost **Web** a zadejte následující identifikátor URI pro přesměrování: `http://localhost:3000/redirect` .
   1. Výběrem možnosti **Registrovat** aplikaci vytvořte.
1. Na stránce **Přehled** aplikace vyhledejte hodnotu **ID aplikace (klienta)** a zaznamenejte ji pro pozdější použití. Budete ho potřebovat ke konfiguraci konfiguračního souboru pro tento projekt.
1. V části **Správa** vyberte **Certifikáty a tajné kódy**.
1. V části **tajné klíče klienta** vyberte **nový tajný klíč klienta** a potom:
   1. Zadejte popis klíče.
   1. Vyberte dobu trvání klíče **v intervalu 1 roku**.
   1. Vyberte **Přidat**.
   1. Když se zobrazí hodnota klíče, zkopírujte ji. Budete ho potřebovat později.

# <a name="python"></a>[Python](#tab/python)

1. Jakmile se zobrazí **Stránka Registrovat aplikaci** , zadejte informace o registraci vaší aplikace:
   1. Zadejte **název** vaší aplikace, například `python-webapp` . Uživatel vaší aplikace může tento název zobrazit a později ho můžete změnit.
   1. Změňte **podporované typy účtů** na **účty v jakémkoli adresáři organizace a na osobních účtech Microsoft (např. Skype, Xbox, Outlook.com)**.
   1. V části **identifikátor URI přesměrování (volitelné)** vyberte v poli se seznamem možnost **Web** a zadejte následující identifikátor URI pro přesměrování: `http://localhost:5000/getAToken` .
   1. Výběrem možnosti **Registrovat** aplikaci vytvořte.
1. Na stránce **Přehled** aplikace vyhledejte hodnotu **ID aplikace (klienta)** a zaznamenejte ji pro pozdější použití. Budete ho potřebovat ke konfiguraci konfiguračního souboru sady Visual Studio pro tento projekt.
1. V části **Správa** vyberte **Certifikáty a tajné kódy**.
1. V části **tajné klíče klienta** vyberte **nový tajný klíč klienta** a potom:
   1. Zadejte popis klíče.
   1. Vyberte dobu trvání klíče **v intervalu 1 roku**.
   1. Vyberte **Přidat**.
   1. Když se zobrazí hodnota klíče, zkopírujte ji. Budete ho potřebovat později.
---

## <a name="register-an-app-by-using-powershell"></a>Registrace aplikace pomocí PowerShellu

> [!NOTE]
> V současné době Azure AD PowerShell vytváří aplikace s pouze těmito podporovanými typy účtů:
>
> - MyOrg (jenom účty v této organizační složce)
> - AnyOrg (účty v jakékoli organizační složce)
>
> Můžete vytvořit aplikaci, která se přihlásí uživatelům pomocí svých osobních účtů Microsoft (například Skype, Xbox nebo Outlook.com). Nejdřív vytvořte víceklientské aplikace. Podporované typy účtů jsou účty v jakémkoli organizačním adresáři. Poté změňte [`accessTokenAcceptedVersion`](./reference-app-manifest.md#accesstokenacceptedversion-attribute) vlastnost na hodnotu **2** a [`signInAudience`](./reference-app-manifest.md#signinaudience-attribute) vlastnost na `AzureADandPersonalMicrosoftAccount` v [manifestu aplikace](./reference-app-manifest.md) z Azure Portal. Další informace najdete v tématu [krok 1,3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) v kurzu ASP.NET Core. Tento krok můžete zobecnit do Web Apps v libovolném jazyce.

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku v tomto scénáři [Konfigurace kódu aplikace](scenario-web-app-sign-user-app-configuration.md).
