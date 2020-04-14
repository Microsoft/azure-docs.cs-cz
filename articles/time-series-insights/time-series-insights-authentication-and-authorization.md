---
title: Ověřování a autorizace rozhraní API – Přehledy Azure Time Series | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak nakonfigurovat ověřování a autorizaci pro vlastní aplikaci, která volá rozhraní API Azure Time Series Insights.
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
ms.openlocfilehash: 373fc2829e599d0989b59c6386757c8f5f3e1d66
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81251720"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Ověřování a autorizace pro rozhraní API služby Azure Time Series Insights

Tento dokument popisuje, jak zaregistrovat aplikaci ve službě Azure Active Directory pomocí nového rozhraní Služby Azure Active Directory. Aplikace registrované ve službě Azure Active Directory umožňují uživatelům ověřovat a mít oprávnění používat rozhraní Azure Time Series Insight API přidružené k prostředí Time Series Insights.

> [!IMPORTANT]
> Azure Time Series Insights podporuje obě následující ověřovací knihovny:
> * Novější [knihovna ověřování společnosti Microsoft (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * [Knihovna ověřování služby Azure Active Directory (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="service-principal"></a>Instanční objekt

Následující části popisují, jak nakonfigurovat aplikaci pro přístup k rozhraní API Time Series Insights jménem aplikace. Aplikace pak může dotazovat nebo publikovat referenční data v prostředí Time Series Insights pomocí vlastních přihlašovacích údajů aplikace prostřednictvím služby Azure Active Directory.

## <a name="summary-and-best-practices"></a>Shrnutí a doporučené postupy

Tok registrace aplikace Azure Active Directory zahrnuje tři hlavní kroky.

1. [Zaregistrujte aplikaci](#azure-active-directory-app-registration) ve službě Azure Active Directory.
1. Autorizujte aplikaci tak, aby měla [přístup k datům v prostředí Time Series Insights](#granting-data-access).
1. Pomocí **ID aplikace** a **tajného klíče klienta** získáte token z `https://api.timeseries.azure.com/` [klientské aplikace](#client-app-initialization). Token pak lze použít k volání rozhraní API Time Series Insights.

V **kroku 3**můžete oddělit pověření aplikace a uživatele:

* Přiřaďte identitě aplikace oprávnění, která se liší od vašich vlastních oprávnění. Tato oprávnění jsou obvykle omezena pouze na to, co aplikace vyžaduje. Můžete například povolit aplikaci číst data pouze z určitého prostředí Time Series Insights.
* Izolujte zabezpečení aplikace od přihlašovacích údajů uživatele, který vytváří, pomocí **tajného klíče klienta** nebo certifikátu zabezpečení. V důsledku toho pověření aplikace nejsou závislé na pověření konkrétního uživatele. Pokud se role uživatele změní, aplikace nemusí nutně vyžadovat nová pověření nebo další konfiguraci. Pokud uživatel změní své heslo, veškerý přístup k aplikaci nevyžaduje nová pověření nebo klíče.
* Spusťte bezobslužný skript pomocí **tajného klíče klienta** nebo certifikátu zabezpečení, nikoli pověření konkrétního uživatele (vyžadující, aby byli přítomni).
* K zabezpečení přístupu k rozhraní API Azure Time Series Insights používejte spíše certifikát zabezpečení než heslo.

> [!IMPORTANT]
> Při konfiguraci **zásady oddělení obav** (popsané pro tento scénář výše) postupujte podle zásad y Azure Time Series Insights.

> [!NOTE]
> * Článek se zaměřuje na aplikaci s jedním tenantem, kde je aplikace určena ke spuštění pouze v jedné organizaci.
> * Obvykle budete používat aplikace s jedním tenantem pro obchodní aplikace, které běží ve vaší organizaci.

## <a name="detailed-setup"></a>Podrobné nastavení

### <a name="azure-active-directory-app-registration"></a>Registrace aplikace Azure Active Directory

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Udělení přístupu k datům

1. V prostředí Time Series Insights vyberte **Zásady přístupu k datům** a vyberte **Přidat**.

   [![Přidání nových zásad přístupu k datům do prostředí Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. V dialogovém okně **Vybrat uživatele** vložte **název aplikace** nebo **ID aplikace** z části registrace aplikace Azure Active Directory.

   [![Vyhledání aplikace v dialogovém okně Vybrat uživatele](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Vyberte roli. Vyberte **Reader,** chcete-li dotazovat data nebo **přispěvatele,** chcete-li dotazovat data a měnit referenční data. Vyberte **OK**.

   [![V dialogovém okně Vybrat roli uživatele vyberte čtečku nebo přispěvatele.](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Uložte zásadu výběrem **možnosti OK**.

   > [!TIP]
   > U rozšířených možností přístupu k datům můžete číst [článek Uděluje přístup k datům](./time-series-insights-data-access.md).

### <a name="client-app-initialization"></a>Inicializace klientské aplikace

* Vývojáři mohou k ověřování pomocí Azure Time Series Insights používat [knihovnu Ověřování Microsoft (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) nebo [Azure Active Directory Authentication Library (ADAL).](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

* Například k ověření pomocí ADAL:

   1. Pomocí **ID aplikace** a **tajného klíče klienta** (aplikační klíč) z oddílu registrace aplikace Azure Active Directory získáte token jménem aplikace.

   1. V C# následující kód může získat token jménem aplikace. Kompletní ukázka načtete [data dotazu pomocí jazyka C#](time-series-insights-query-data-csharp.md).

        [!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs?range=170-199)]

   1. Token pak může být `Authorization` předán v záhlaví, když aplikace volá rozhraní API Time Series Insights.

* Alternativně vývojáři mohou zvolit ověření pomocí MSAL. Přečtěte si o [migraci na MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration) a podívejte se na naše spravovat ga referenční data pro prostředí Azure Time Series Insights pomocí článku [C#,](time-series-insights-manage-reference-data-csharp.md) kde se dozvíte další informace. 

## <a name="common-headers-and-parameters"></a>Běžné záhlaví a parametry

Tato část popisuje běžné hlavičky a parametry požadavků HTTP, které se používají k dotazování na rozhraní API Time Series Insights GA a Preview. Požadavky specifické pro rozhraní API jsou podrobněji popsány v [referenční dokumentaci rozhraní API TIME Series Insights REST](https://docs.microsoft.com/rest/api/time-series-insights/).

> [!TIP]
> Přečtěte si [odkaz na rozhraní API Azure REST,](https://docs.microsoft.com/rest/api/azure/) kde se dozvíte další informace o tom, jak využívat rozhraní REST API, provádět požadavky HTTP a zpracovávat odpovědi HTTP.

### <a name="authentication"></a>Authentication

Chcete-li provádět ověřené dotazy proti [api maty REST Časové řady Insights](https://docs.microsoft.com/rest/api/time-series-insights/), musí být platný nosný token OAuth 2.0 předán v [hlavičce autorizace](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate) pomocí klienta REST podle vašeho výběru (Postman, JavaScript, C#). 

> [!TIP]
> Přečtěte si hostovizované Azure Time Series Insights [klienta SDK ukázkové vizualizace](https://tsiclientsample.azurewebsites.net/) se dozvíte, jak ověřit pomocí časové řady Insights API programově pomocí [JavaScript Client SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) spolu s grafy a grafy.

### <a name="http-headers"></a>Hlavičky PROTOKOLU HTTP

Povinné hlavičky požadavku jsou popsány níže.

| Hlavička požadovaného požadavku | Popis |
| --- | --- |
| Autorizace | Chcete-li ověřit pomocí Time Series Insights, platný OAuth 2.0 Bearer token musí být předán v záhlaví **autorizace.** | 

> [!IMPORTANT]
> Token musí být vydán přesně `https://api.timeseries.azure.com/` prostředku (označované také jako "publikum" tokenu).
> * Váš [pošťák](https://www.getpostman.com/) **AuthURL** bude tedy:`https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`
> * `https://api.timeseries.azure.com/`je platný, ale `https://api.timeseries.azure.com` není.

Volitelné hlavičky požadavku jsou popsány níže.

| Nepovinná hlavička požadavku | Popis |
| --- | --- |
| Typ obsahu | pouze `application/json` je podporována. |
| x-ms-client-request-id | ID požadavku klienta. Služba zaznamenává tuto hodnotu. Umožňuje službě sledovat operace napříč službami. |
| x-ms-client-session-id | ID relace klienta. Služba zaznamenává tuto hodnotu. Umožňuje službě sledovat skupinu souvisejících operací napříč službami. |
| x-ms-client-application-name | Název aplikace, která vygenerovala tento požadavek. Služba zaznamenává tuto hodnotu. |

Volitelné, ale doporučené hlavičky odpovědí jsou popsány níže.

| Hlavička odpovědi | Popis |
| --- | --- |
| Typ obsahu | Je `application/json` podporována pouze. |
| x-ms-request-id | ID požadavku generovaného serverem. Lze kontaktovat společnost Microsoft a prošetřit požadavek. |
| x-ms-property-not-found-behavior | Volitelná hlavička odpovědi ROZHRANÍ GA API. Možné hodnoty `ThrowError` jsou (výchozí) nebo `UseNull`. |

### <a name="http-parameters"></a>Parametry HTTP

> [!TIP]
> Další informace o povinných a volitelných informacích o dotazu naleznete v [referenční dokumentaci](https://docs.microsoft.com/rest/api/time-series-insights/).

Požadované parametry řetězce dotazu URL závisí na verzi rozhraní API.

| Vydat | Možné hodnoty verze rozhraní API |
| --- |  --- |
| Obecná dostupnost | `api-version=2016-12-12`|
| Preview | `api-version=2018-11-01-preview` |
| Preview | `api-version=2018-08-15-preview` |

Volitelné parametry řetězce dotazu URL zahrnují nastavení časového limitu pro časy spuštění požadavku HTTP.

| Volitelný parametr dotazu | Popis | Version |
| --- |  --- | --- |
| `timeout=<timeout>` | Časový výtek na straně serveru pro spuštění požadavku HTTP. Platí pouze pro [získání událostí prostředí](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) a získat [agregace prostředí](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api) API. Hodnota časového času by měla být například `"PT20S"` ve formátu doby trvání `1-30 s`ISO 8601 a měla by být v rozsahu . Výchozí hodnota `30 s`je . | GA |
| `storeType=<storeType>` | Pro prostředí náhledu s povoleným aktivním úložištěm `WarmStore` může `ColdStore`být dotaz proveden buď na nebo . Tento parametr v dotazu definuje, ve kterém úložišti by měl být dotaz spuštěn. Pokud není definována, dotaz bude proveden v úložišti chladiva. Chcete-li dotaz teplé úložiště, **storeType** musí být nastavena na `WarmStore`. Pokud není definována, dotaz bude proveden proti úložiště chladiva. | Preview |

## <a name="next-steps"></a>Další kroky

- Ukázkový kód, který volá rozhraní GA Time Series Insights API, přečtěte [si data dotazu pomocí jazyka C#](./time-series-insights-query-data-csharp.md).

- Ukázky kódu rozhraní API náhledu Časové řady Přehledy najdete [v kódu náhledu dotazu pomocí jazyka C#](./time-series-insights-update-query-data-csharp.md).

- Informace o referencích rozhraní API naleznete v dokumentaci [k referenčnímu rozhraní API dotazu.](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)

- Přečtěte si, jak [vytvořit instanční objekt](../active-directory/develop/howto-create-service-principal-portal.md).
