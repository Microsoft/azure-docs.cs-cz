---
title: Migrace webových rozhraní API založených na OWIN do b2clogin.com-Azure Active Directory B2C
description: Naučte se, jak povolit rozhraní Web API .NET pro podporu tokenů vydaných více vystaviteli tokenů při migraci aplikací do b2clogin.com.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a8a6b4f90fe3f1e60341cc59e7d81870c82e843b
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533758"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom"></a>Migrace webového rozhraní API založeného na OWIN na b2clogin.com

Tento článek popisuje techniku pro povolení podpory více vystavitelů tokenů ve webových rozhraních API, které implementují rozhraní [Open Web Interface for .NET (Owin)](http://owin.org/). Podpora více koncových bodů tokenu je užitečná v případě, že migrujete rozhraní API Azure Active Directory B2C (Azure AD B2C) a jejich aplikace z *Login.microsoftonline.com* do *b2clogin.com*.

Přidáním podpory do svého rozhraní API pro přijetí tokenů vydaných b2clogin.com i login.microsoftonline.com můžete migrovat webové aplikace tak, aby se odstranily podpory pro tokeny vydané login.microsoftonline.com z rozhraní API.

V následujících částech najdete příklad povolení více vystavitelů ve webovém rozhraní API, které používá součásti middlewaru [Microsoft Owin][katana] (Katana). I když jsou příklady kódu specifické pro middleware Microsoft OWIN, měla by být obecná technika platná pro jiné knihovny OWIN.

> [!NOTE]
> Tento článek je určený pro Azure AD B2C zákazníky s aktuálně nasazenými rozhraními API a `login.microsoftonline.com` aplikacemi, na které odkazují a kteří chtějí `b2clogin.com` migrovat na doporučený koncový bod. Pokud nastavujete novou aplikaci, použijte [b2clogin.com](b2clogin.md) jako směrovaný.

## <a name="prerequisites"></a>Požadavky

Než budete pokračovat v krocích v tomto článku, budete potřebovat následující Azure AD B2C prostředky:

* [Uživatelské toky](tutorial-create-user-flows.md) nebo [vlastní zásady](active-directory-b2c-get-started-custom.md) vytvořené ve vašem tenantovi

## <a name="get-token-issuer-endpoints"></a>Získat koncové body vystavitele tokenů

Nejdřív musíte získat identifikátory URI koncového bodu vystavitele tokenu pro každého vystavitele, který chcete v rozhraní API podporovat. Pokud chcete získat koncové body *b2clogin.com* a *Login.microsoftonline.com* podporované vaším klientem Azure AD B2C, použijte následující postup v Azure Portal.

Začněte tím, že vyberete jeden z vašich stávajících uživatelských toků:

1. V [Azure Portal](https://portal.azure.com) přejděte do svého tenanta Azure AD B2C.
1. V části **zásady**vyberte **toky uživatelů (zásady)** .
1. Vyberte existující zásadu, třeba *B2C_1_signupsignin1*, a pak vyberte **Spustit tok uživatele** .
1. Pod záhlavím **Spustit uživatele** v horní části stránky vyberte hypertextový odkaz, který umožňuje přejít na koncový bod zjišťování OpenID Connect pro daný tok uživatele.

    ![Dobře známý hypertextový odkaz URI na stránce spustit nyní na Azure Portal](media/multi-token-endpoints/portal-01-policy-link.png)

1. Na stránce, která se otevře v prohlížeči, zaznamenejte `issuer` hodnotu například:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. Pomocí rozevíracího seznamu **Vybrat doménu** vyberte druhou doménu a potom znovu proveďte předchozí dva kroky a zaznamenejte její `issuer` hodnotu.

Nyní byste měli mít dva zaznamenané identifikátory URI, které jsou podobné:

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>Vlastní zásady

Pokud místo uživatelských toků máte vlastní zásady, můžete k získání identifikátorů URI vystavitele použít podobný postup.

1. Přejděte do svého tenanta Azure AD B2C.
1. Vybrat **architekturu prostředí identity**
1. Vyberte jednu ze zásad předávající strany, například *B2C_1A_signup_signin* .
1. Pomocí rozevíracího seznamu **Vybrat doménu** vyberte doménu, například *yourtenant.b2clogin.com* .
1. Vyberte hypertextový odkaz zobrazený v části **OpenID Connect Discovery Endpoint** .
1. Poznamenejte `issuer` si hodnotu
1. Proveďte kroky 4-6 pro druhou doménu, například *Login.microsoftonline.com*

## <a name="get-the-sample-code"></a>Získání ukázkového kódu

Teď, když máte identifikátory URI koncového bodu tokenu, musíte aktualizovat kód, abyste určili, že oba koncové body jsou platnými vystaviteli. Pokud chcete projít příklad, Stáhněte nebo naklonujte ukázkovou aplikaci a pak aktualizujte ukázku tak, aby podporovalo oba koncové body jako platné vystavitele.

Stažení archivu: [Active-Directory-B2C-dotnet-WebApp-and-WebApi-Master. zip][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>Povolení více vystavitelů ve webovém rozhraní API

V této části aktualizujete kód a určíte, že oba koncové body vystavitele tokenu jsou platné.

1. Otevřete řešení **B2C-WebApi-dotnet. sln** v aplikaci Visual Studio
1. V projektu **TaskService** otevřete soubor *TaskService\\app_start\\**Startup.auth.cs*** v editoru.
1. Do horní části `using` souboru přidejte následující direktivu:

    `using System.Collections.Generic;`
1. [`ValidIssuers`][validissuers] Přidejte [dodefinicevlastnostazadejteobaidentifikátoryURI,kteréjstesipoznamenali`TokenValidationParameters`][tokenvalidationparameters] v předchozí části:

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

`TokenValidationParameters`je poskytována nástrojem MSAL.NET a je využíván middlewarem OWIN v další části kódu v *Startup.auth.cs*. Je-li zadáno více platných vystavitelů, kanál aplikace OWIN ví, že jsou oba koncové body tokenu platnými vystaviteli.

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

Jak už bylo zmíněno dříve, jiné knihovny OWIN obvykle poskytují podobnou funkci pro podporu více vystavitelů. I když poskytuje příklady pro každou knihovnu mimo rozsah tohoto článku, můžete pro většinu knihoven použít podobnou techniku.

## <a name="switch-endpoints-in-web-app"></a>Přepínání koncových bodů ve webové aplikaci

U obou identifikátorů URI, které teď podporuje vaše webové rozhraní API, teď potřebujete aktualizovat webovou aplikaci tak, aby se z koncového bodu b2clogin.com načítat tokeny.

Například můžete nakonfigurovat ukázkovou webovou aplikaci tak, aby používala nový koncový bod úpravou `ida:AadInstance` hodnoty v souboru *TaskWebApp\\**Web. config** v* projektu **TaskWebApp** .

Změňte hodnotu v *souboru Web. config* pro TaskWebApp tak, aby odkazovala `{your-b2c-tenant-name}.b2clogin.com` místo `login.microsoftonline.com`na. `ida:AadInstance`

Před:

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

Po (nahraďte `{your-b2c-tenant}` názvem vašeho tenanta B2C):

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

Když jsou během provádění webové aplikace vytvořeny řetězce koncového bodu, použijí se koncové body založené na b2clogin.com při žádosti o tokeny.

## <a name="next-steps"></a>Další postup

V tomto článku jsme si předvedli metodu konfigurace webového rozhraní API implementující middleware Microsoft OWIN (Katana), který přijímá tokeny z více koncových bodů vystavitelů. Jak si můžete všimnout, existuje několik dalších řetězců v souborech *Web. config* obou projektů TaskService a TaskWebApp, které by bylo potřeba změnit, pokud chcete sestavit a spustit tyto projekty pro vašeho vlastního tenanta. Vítá vás, abyste mohli projekty upravovat, pokud je chcete vidět v akci, ale Úplný návod k tomu, že se to stane, je nad rámec tohoto článku.

Další informace o různých typech tokenů zabezpečení emitovaných Azure AD B2C najdete v tématu [Přehled tokenů v Azure Active Directory B2C](active-directory-b2c-reference-tokens.md).

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/
[validissuers]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters
