---
title: Ověřování a autorizace rozhraní API – Azure Time Series Insights | Microsoft Docs
description: Tento článek popisuje, jak nakonfigurovat ověřování a autorizaci pro vlastní aplikaci, která volá rozhraní Azure Time Series Insights API.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: ff5f7a80e2dcedb1795bae14ee9140c2842303a5
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76984530"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Ověřování a autorizace pro rozhraní Azure Time Series Insights API

Tento dokument popisuje, jak zaregistrovat aplikaci v Azure Active Directory pomocí nového okna Azure Active Directory. Aplikace zaregistrované v Azure Active Directory umožňují uživatelům ověřování a autorizaci používat rozhraní API služby Azure Time Series Insights přidružené k prostředí Time Series Insights.

> [!IMPORTANT]
> Azure Time Series Insights podporuje obě následující knihovny ověřování:
> * Novější [Knihovna Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * [Knihovna Azure Active Directory Authentication Library (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="service-principal"></a>Instanční objekt

Následující části popisují, jak nakonfigurovat aplikaci pro přístup k rozhraní Time Series Insights API jménem aplikace. Aplikace pak může dotazovat nebo publikovat referenční data v prostředí Time Series Insights pomocí vlastních přihlašovacích údajů aplikace prostřednictvím Azure Active Directory.

## <a name="summary-and-best-practices"></a>Souhrn a osvědčené postupy

Registrační tok aplikace Azure Active Directory zahrnuje tři hlavní kroky.

1. [Registrace aplikace](#azure-active-directory-app-registration) v Azure Active Directory.
1. Autorizujte aplikaci tak, aby měla [přístup k datům Time Series Insights prostředí](#granting-data-access).
1. Použijte **ID aplikace** a **tajný klíč klienta** k získání tokenu z `https://api.timeseries.azure.com/` v [klientské aplikaci](#client-app-initialization). Token se pak dá použít k volání rozhraní Time Series Insights API.

V rámci **kroku 3**oddělení aplikace a přihlašovací údaje uživatele vám umožní:

* Přiřaďte oprávnění identitě aplikace, která se liší od vašich vlastních oprávnění. Tato oprávnění se většinou omezují jenom na to, co aplikace vyžaduje. Aplikaci můžete například dovolit číst data pouze z konkrétního Time Series Insightsho prostředí.
* Izolujte zabezpečení aplikace od přihlašovacích údajů pro ověření uživatele pomocí **tajného klíče klienta** nebo certifikátu zabezpečení. V důsledku toho přihlašovací údaje aplikace nejsou závislé na přihlašovacích údajích konkrétního uživatele. Pokud se změní role uživatele, aplikace nemusí nutně vyžadovat nové přihlašovací údaje ani další konfiguraci. Pokud uživatel změní heslo, veškerý přístup k aplikaci nevyžaduje nové přihlašovací údaje ani klíče.
* Spusťte bezobslužný skript pomocí **tajného klíče klienta** nebo certifikátu zabezpečení, nikoli přihlašovací údaje konkrétního uživatele (vyžaduje, aby byly přítomny).
* Pro zabezpečení přístupu k rozhraní Azure Time Series Insights API použijte certifikát zabezpečení, nikoli heslo.

> [!IMPORTANT]
> Při konfiguraci zásad zabezpečení Azure Time Series Insights postupujte podle principu **oddělení** potíží (popsané v tomto scénáři výše).

> [!NOTE]
> * Článek se zaměřuje na aplikaci s jedním tenantů, kde má aplikace běžet jenom v jedné organizaci.
> * Pro obchodní aplikace, které běží ve vaší organizaci, obvykle používáte aplikace pro jednoho tenanta.

## <a name="detailed-setup"></a>Podrobné nastavení

### <a name="azure-active-directory-app-registration"></a>Registrace aplikace Azure Active Directory

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Udělení přístupu k datům

1. V prostředí Time Series Insights vyberte **zásady přístupu k datům** a vyberte **Přidat**.

   [![přidat nové zásady přístupu k datům do prostředí Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. V dialogovém okně **Vybrat uživatele** vložte **název aplikace** nebo **ID aplikace** z části registrace aplikace Azure Active Directory.

   [![najít aplikaci v dialogovém okně Vybrat uživatele](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Vyberte roli. Výběr **čtecího zařízení** pro dotazování dat nebo **přispěvatele** k dotazování na data a změně referenčních dat. Vyberte **OK**.

   [![výběru čtecího modulu nebo přispěvatele v dialogovém okně Vybrat roli uživatele](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Uložte zásadu výběrem **OK**.

   > [!TIP]
   > V případě pokročilých možností přístupu k datům si přečtěte téma [udělení přístupu k datům](./time-series-insights-data-access.md).

### <a name="client-app-initialization"></a>Inicializace klientské aplikace

* Vývojáři můžou k ověřování pomocí Azure Time Series Insights použít [knihovnu Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) nebo [knihovnu ověřování v knihovně ADAL (Azure Active Directory Authentication Library)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) .

* Například pro ověřování pomocí ADAL:

   1. Použijte **ID aplikace** a **tajný klíč klienta** (aplikační klíč) z části registrace aplikace Azure Active Directory k získání tokenu jménem aplikace.

   1. V C#nástroji může následující kód získat token jménem aplikace. Úplnou ukázku najdete v článku [data dotazů pomocí C# ](time-series-insights-query-data-csharp.md).

        [!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs?range=170-199)]

   1. Token se pak může předat v hlavičce `Authorization`, když aplikace volá rozhraní API Time Series Insights.

* Alternativně se mohou vývojáři rozhodnout k ověřování pomocí MSAL. Přečtěte si o [migraci na MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration) a podívejte se na naše [referenční data GA pro Azure Time Series Insights C# prostředí](time-series-insights-manage-reference-data-csharp.md) . v článku se dozvíte víc. 

## <a name="common-headers-and-parameters"></a>Společné hlavičky a parametry

Tato část popisuje společné hlavičky a parametry požadavků protokolu HTTP, které slouží k vytváření dotazů proti Time Series Insights rozhraní API GA a Preview. Požadavky na konkrétní rozhraní API jsou podrobněji popsány v [referenční dokumentaci Time Series Insights REST API](https://docs.microsoft.com/rest/api/time-series-insights/).

> [!TIP]
> Další informace o tom, jak využívat rozhraní REST API, dělat požadavky HTTP a zpracovávat odpovědi HTTP, najdete v referenčních informacích k [Azure REST API](https://docs.microsoft.com/rest/api/azure/) .

### <a name="authentication"></a>Ověření

Aby bylo možné provádět ověřené dotazy proti [Time Series Insights rozhraní REST API](https://docs.microsoft.com/rest/api/time-series-insights/), musí se v [autorizační hlavičce](/rest/api/apimanagement/2019-01-01/authorizationserver/createorupdate) předávat platný token OAuth 2,0 s použitím klienta REST podle vašeho výběru (post, JavaScript, C#). 

> [!TIP]
> Přečtěte si [ukázkovou vizualizaci hostované Azure Time Series Insights klientské sady SDK](https://tsiclientsample.azurewebsites.net/) a Naučte se ověřovat pomocí rozhraní api pro Time Series Insights programově pomocí [klientské sady SDK pro JavaScript](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) spolu s grafy a grafy.

### <a name="http-headers"></a>Hlavičky protokolu HTTP

Požadované hlavičky požadavku jsou popsány níže.

| Požadovaná hlavička žádosti | Popis |
| --- | --- |
| Autorizace | Chcete-li provést ověření pomocí Time Series Insights, musí být do **autorizační** hlavičky předána platný nosný token OAuth 2,0. | 

> [!IMPORTANT]
> Token se musí vystavit přesně pro prostředek `https://api.timeseries.azure.com/` (označuje se také jako "cílová skupina" tokenu).
> * Vaše [dodatečná](https://www.getpostman.com/) **AuthURLa** bude tedy: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`
> * `https://api.timeseries.azure.com/` je platný, ale `https://api.timeseries.azure.com` není.

Volitelné hlavičky požadavku jsou popsány níže.

| Nepovinná hlavička požadavku | Popis |
| --- | --- |
| Typ obsahu | podporuje se jenom `application/json`. |
| x-ms-client-request-id | ID žádosti klienta. Služba zaznamená tuto hodnotu. Umožňuje službě sledovat operace napříč službami. |
| x-MS-Client-Session-ID | ID klientské relace. Služba zaznamená tuto hodnotu. Umožňuje službě trasovat skupinu souvisejících operací napříč službami. |
| x-MS-Client-Application-Name | Název aplikace, která vygenerovala tento požadavek. Služba zaznamená tuto hodnotu. |

Volitelné, ale Doporučené hlavičky odpovědí jsou popsány níže.

| Hlavička odpovědi | Popis |
| --- | --- |
| Typ obsahu | podporuje se jenom `application/json`. |
| x-MS-Request-ID | ID žádosti generované serverem Dá se použít ke kontaktování žádosti Microsoftu o vyšetření žádosti. |
| x-MS-Property-Nenalezeno – chování | Volitelná hlavička odpovědi pro rozhraní API GA Možné hodnoty jsou `ThrowError` (výchozí) nebo `UseNull`. |

### <a name="http-parameters"></a>Parametry HTTP

> [!TIP]
> Další informace o požadovaných a volitelných informacích o dotazech najdete v [referenční dokumentaci](https://docs.microsoft.com/rest/api/time-series-insights/).

Požadované parametry řetězce dotazu adresy URL závisí na verzi rozhraní API.

| Vydání | Možné hodnoty verze rozhraní API |
| --- |  --- |
| Všeobecná dostupnost | `api-version=2016-12-12`|
| Preview | `api-version=2018-11-01-preview` |
| Preview | `api-version=2018-08-15-preview` |

Volitelné parametry řetězce dotazu adresy URL zahrnují nastavení časového limitu pro dobu provádění požadavku HTTP.

| Volitelný parametr dotazu | Popis | Version |
| --- |  --- | --- |
| `timeout=<timeout>` | Časový limit pro provedení požadavku HTTP na straně serveru. Dá se použít jenom pro [události Get prostředí](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) a rozhraní API pro [agregace prostředí](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api) . Hodnota časového limitu by měla být ve formátu doby trvání ISO 8601, například `"PT20S"` a měla by být v rozsahu `1-30 s`. Výchozí hodnota je `30 s`. | GA |
| `storeType=<storeType>` | Pro prostředí ve verzi Preview s povoleným teplým úložištěm je možné dotaz spustit buď na `WarmStore` nebo `ColdStore`. Tento parametr v dotazu definuje, na kterém úložišti se má dotaz spustit. Pokud není definován, dotaz se spustí v chladírenském skladu. Pro dotazování na záložní úložiště musí být **storeType** nastavené na `WarmStore`. Pokud není definován, dotaz se spustí pro chladírenský sklad. | Preview |

## <a name="next-steps"></a>Další kroky

- Pro vzorový kód, který volá rozhraní API GA Time Series Insights, si přečtěte [data dotazu pomocí C# ](./time-series-insights-query-data-csharp.md).

- Pro náhled Time Series Insights ukázek kódu rozhraní API čtěte [data ve verzi Preview C#dotazu pomocí ](./time-series-insights-update-query-data-csharp.md).

- Informace o referenčních informacích k rozhraní API najdete v referenční dokumentaci k [rozhraní API pro dotazy](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api) .

- Naučte se [vytvořit instanční objekt](../active-directory/develop/howto-create-service-principal-portal.md).
