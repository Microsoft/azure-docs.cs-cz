---
title: Webové aplikace, který se přihlásí uživatelé (registrace aplikace) – platforma identit Microsoft
description: Zjistěte, jak sestavit webovou aplikaci, který se přihlásí uživatelé (registrace aplikace)
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
ms.openlocfilehash: 0ae638f8cbef29c5d167a3ab59188169cbd934ef
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67150229"
---
# <a name="web-app-that-signs-in-users---app-registration"></a>Webové aplikace, který se přihlásí uživatelé – registrace aplikace

Tato stránka vysvětluje podrobnosti registrace aplikace pro webové aplikace, které uživatelé přihlásí.

Pokud chcete zaregistrovat aplikaci, můžete použít:

- [Webové aplikace, šablony rychlý Start](#register-an-app-using-the-quickstarts) – kromě toho, že skvělé první zkušenosti s vytvářením aplikace, šablony rychlý start na webu Azure Portal obsahovat tlačítko s názvem **tuto změnu pro mě**. Toto tlačítko slouží k nastavení vlastností, které potřebujete, dokonce i pro existující aplikace. Bude nutné upravit hodnoty těchto vlastností na vlastní písmena. Zejména webové adresy URL rozhraní API pro vaši aplikaci je pravděpodobně to se liší od navrhované výchozí, což také ovlivní odhlášení identifikátoru URI.
- Na webu Azure portal [ručně zaregistrovat aplikaci](#register-an-app-using-azure-portal)
- Prostředí PowerShell a nástroje příkazového řádku

## <a name="register-an-app-using-the-quickstarts"></a>Registrace aplikace pomocí postupů rychlý start

Pokud přejdete na tento odkaz, můžete vytvořit bootstrap vytvoření webové aplikace:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

### <a name="register-an-app-using-azure-portal"></a>Registrace aplikace pomocí webu Azure portal

> [!NOTE]
> portál na použití se liší v závislosti na Pokud je aplikace spuštěná ve veřejném cloudu Microsoft Azure nebo v jiném národní nebo suverénního cloudu. Další informace najdete v tématu [národních Cloudů](./authentication-national-cloud.md#app-registration-endpoints)

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft. Můžete také přihlásit k národních cloudů Azure portal podle výběru.
1. Pokud váš účet poskytuje přístup k více než jednoho tenanta, v pravém horním rohu vyberte svůj účet a nastavení portálu relace k požadované službě Azure AD tenanta.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby a pak vyberte **registrace aplikací** > **registrace nové**.
1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:
   1. Vyberte typy podporovaných účtů pro vaši aplikaci (naleznete v tématu [účtu podporované typy](./v2-supported-account-types.md))
   1. V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `AspNetCore-WebApp`.
   1. V **identifikátor URI pro přesměrování**typu aplikace a přidejte cílový identifikátor URI, který bude přijímat vrátil odpovědí na tokeny po úspěšném ověření. Například, `https://localhost:44321/`.  Vyberte **Zaregistrovat**.
1. Vyberte **ověřování** nabídky a potom přidejte následující informace:
   1. V **adresy URL odpovědi**, přidejte `https://localhost:44321/signin-oidc`.
   1. V **upřesňující nastavení** nastavte **odhlašovací adresa URL** k `https://localhost:44321/signout-oidc`.
   1. V části **implicitní grant**, zkontrolujte **tokeny typu ID**.
   1. Vyberte **Uložit**.

### <a name="register-an-app-using-powershell"></a>Registrace aplikace pomocí Powershellu

> [!NOTE]
> Azure AD PowerShell aktuálně vytvoří jenom aplikace s následujícími typy podporovaných účet:
>
> - MyOrg (účty v tomto adresáři organizace jenom)
> - AnyOrg (účty v libovolném adresáři organizace).
>
> Pokud chcete vytvořit aplikaci, které uživatelé přihlásí s jejich osobní Accounts Microsoft (třeba Skype, XBox, Outlook.com), můžete vytvořit první aplikaci s více tenanty (podporovaných typů účtu = účty v libovolném adresáři organizace) a potom změňte `signInAudience` vlastnost v manifestu aplikace z portálu Azure portal. To je vysvětleno v podrobnostech v kroku [1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) kurzu ASP.NET Core (a může být obecné, aby webové aplikace v jakémkoli jazyce).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Konfigurace kódu aplikace](scenario-web-app-sign-user-app-configuration.md)
