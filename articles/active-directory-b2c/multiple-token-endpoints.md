---
title: Migrace webových api založených na OWIN do b2clogin.com
titleSuffix: Azure AD B2C
description: Zjistěte, jak povolit webové rozhraní .NET pro podporu tokenů vydaných více vydavateli tokenů při migraci aplikací do b2clogin.com.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5daf88e746ea803f345c79bd31d656f2615b6754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184090"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom"></a>Migrace webového rozhraní API založeného na OWIN do b2clogin.com

Tento článek popisuje techniku povolení podpory pro více vydavatelů tokenů ve webových rozhraních API, která implementují [otevřené webové rozhraní pro .NET (OWIN)](http://owin.org/). Podpora více koncových bodů tokenu je užitečná, když migrujete rozhraní API Služby Azure Active Directory B2C (Azure AD B2C) a jejich aplikace z *login.microsoftonline.com* na *b2clogin.com*.

Přidáním podpory v rozhraní API pro přijímání tokenů vydaných b2clogin.com i login.microsoftonline.com můžete migrovat webové aplikace fázovaným způsobem před odebráním podpory pro tokeny vydané login.microsoftonline.com z rozhraní API.

Následující části představují příklad povolení více vystavitelů ve webovém rozhraní API, které používá součásti Middleware [Společnosti Microsoft OWIN][katana] (Katana). Přestože příklady kódu jsou specifické pro middleware Microsoft OWIN, obecná technika by měla být použitelná pro jiné knihovny OWIN.

> [!NOTE]
> Tento článek je určený pro zákazníky Azure AD B2C s `login.microsoftonline.com` aktuálně nasazenými api `b2clogin.com` a aplikacemi, které odkazují a které chtějí migrovat do doporučeného koncového bodu. Pokud nastavujete novou aplikaci, použijte [b2clogin.com](b2clogin.md) podle pokynů.

## <a name="prerequisites"></a>Požadavky

Před pokračováním v krocích v tomto článku potřebujete následující prostředky Azure AD B2C na místě:

* [Toky uživatelů](tutorial-create-user-flows.md) nebo [vlastní zásady](custom-policy-get-started.md) vytvořené ve vašem tenantovi

## <a name="get-token-issuer-endpoints"></a>Získat koncové body vystavitetokenu

Nejprve musíte získat identifikátory URI koncového bodu vystavitenu tokenu pro každého vystavitna, který chcete ve vašem rozhraní API podporovat. Pokud chcete získat *b2clogin.com* a *login.microsoftonline.com* koncových bodů podporovaných vaším klientem Azure AD B2C, použijte na webu Azure Portal následující postup.

Začněte výběrem jednoho z vašich stávajících uživatelských toků:

1. Přejděte do klienta Azure AD B2C na [webu Azure Portal](https://portal.azure.com)
1. V části **Zásady**vyberte **Toky uživatelů (zásady).**
1. Vyberte existující zásadu, například *B2C_1_signupsignin1*, a pak vyberte **Spustit tok uživatele.**
1. Pod nadpisem **Spustit tok uživatele** v horní části stránky vyberte hypertextový odkaz a přejděte na koncový bod zjišťování OpenID Connect pro tento tok uživatele.

    ![Známý hypertextový odkaz URI na stránce Spustit nyní na webu Azure Portal](media/multi-token-endpoints/portal-01-policy-link.png)

1. Na stránce, která se otevře `issuer` v prohlížeči, zaznamenejte hodnotu, například:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. Pomocí rozevíracího přehledu **Vybrat doménu** vyberte druhou doménu, pak `issuer` znovu proveďte předchozí dva kroky a zaznamenejte její hodnotu.

Nyní byste měli mít dva identifikátory URI, které jsou podobné:

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>Vlastní zásady

Pokud máte vlastní zásady namísto toků uživatelů, můžete použít podobný proces k získání identifikátorů URI vystavitho.

1. Přechod na klienta Azure AD B2C
1. Vybrat **rozhraní pro prostředí identity**
1. Vyberte jednu ze zásad předávající strany, například *B2C_1A_signup_signin*
1. Pomocí rozevíracího políčka **Vybrat doménu** vyberte doménu, například *yourtenant.b2clogin.com*
1. Výběr hypertextového odkazu zobrazeného v **koncovém bodě zjišťování OpenID Connect**
1. Zaznamenat `issuer` hodnotu
1. Proveďte kroky 4-6 pro druhou doménu, například *login.microsoftonline.com*

## <a name="get-the-sample-code"></a>Získání ukázkového kódu

Teď, když máte oba identifikátory URI koncového bodu tokenu, je třeba aktualizovat kód a určit, že oba koncové body jsou platnými vystaviteli. Chcete-li projít příklad, stáhněte nebo klonovat ukázkovou aplikaci a pak aktualizujte ukázku tak, aby podporovala oba koncové body jako platné vystavitele.

Stáhněte si archiv: [active-directory-b2c-dotnet-webapp-and-webapi-master.zip][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>Povolení více vydavatelů ve webovém rozhraní API

V této části aktualizovat kód určit, že oba koncové body vystavitetokenu jsou platné.

1. Otevření řešení **B2C-WebAPI-DotNet.sln** v sadě Visual Studio
1. V projektu **TaskService** otevřete soubor *TaskService\\App_Start\\**Startup.Auth.cs*** v editoru.
1. Do horní `using` části souboru přidejte následující direktivu:

    `using System.Collections.Generic;`
1. Přidejte [`ValidIssuers`][validissuers] vlastnost [`TokenValidationParameters`][tokenvalidationparameters] do definice a zadejte obě identifikátory URI, které jste zaznamenali v předchozí části:

    ```csharp
    TokenValidationParameters tvps = new TokenValidationParameters
    {
        // Accept only those tokens where the audience of the token is equal to the client ID of this app
        ValidAudience = ClientId,
        AuthenticationType = Startup.DefaultPolicy,
        ValidIssuers = new List<string> {
            "https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/",
            "https://{your-b2c-tenant}.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"
        }
    };
    ```

`TokenValidationParameters`je poskytována MSAL.NET a je spotřebována middleware OWIN v další části kódu v *Startup.Auth.cs*. S více platnými vystaviteli zadaný kanál aplikace OWIN je upozorněn, že oba koncové body tokenu jsou platné vystavitele.

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

Jak již bylo zmíněno dříve, jiné knihovny OWIN obvykle poskytují podobné zařízení pro podporu více emitentů. Přestože poskytuje příklady pro každou knihovnu je mimo rozsah tohoto článku, můžete použít podobnou techniku pro většinu knihoven.

## <a name="switch-endpoints-in-web-app"></a>Přepnutí koncových bodů ve webové aplikaci

S oběma identifikátory URI, které nyní podporuje vaše webové rozhraní API, nyní potřebujete aktualizovat webovou aplikaci tak, aby načítaná tokeny z koncového bodu b2clogin.com.

Ukázkovou webovou aplikaci můžete například nakonfigurovat tak, `ida:AadInstance` aby používala nový koncový bod, úpravou hodnoty v souboru *TaskWebApp\\**Web.config**** projektu **TaskWebApp.**

Změňte `ida:AadInstance` hodnotu v *souboru Web.config* aplikace `{your-b2c-tenant-name}.b2clogin.com` TaskWebApp tak, aby odkazovala na položku `login.microsoftonline.com`.

Před:

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

Po (nahraďte `{your-b2c-tenant}` se názvem vašeho klienta B2C):

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

Když jsou řetězce koncového bodu vytvořeny během provádění webové aplikace, b2clogin.com koncové body se používají při požadavcích na tokeny.

## <a name="next-steps"></a>Další kroky

Tento článek představil metodu konfigurace webového rozhraní API implementující middleware Microsoft OWIN (Katana) pro přijímání tokenů z více koncových bodů vystavite. Jak jste si možná všimli, existuje několik dalších řetězců v *souborech Web.Config* taskservice a TaskWebApp projekty, které by bylo nutné změnit, pokud chcete vytvořit a spustit tyto projekty proti vlastnímu tenantovi. Můžete upravit projekty odpovídajícím způsobem, pokud chcete vidět v akci, ale úplný návod k tomu je mimo rozsah tohoto článku.

Další informace o různých typech tokenů zabezpečení vysílaných službou Azure AD B2C najdete [v tématu Přehled tokenů ve službě Azure Active Directory B2C](tokens-overview.md).

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/
[validissuers]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters
