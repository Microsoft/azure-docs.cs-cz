---
title: Ověřování a autorizace rozhraní API – Azure Time Series Insights | Microsoft Docs
description: Tento článek popisuje, jak nakonfigurovat ověřování a autorizaci pro vlastní aplikaci, která volá rozhraní Azure Time Series Insights API.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: shresha
manager: dpalled
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 7408e3fb279536f61dd2e5cf1858476da57219d4
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91665802"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Ověřování a autorizace pro rozhraní API služby Azure Time Series Insights

Tento dokument popisuje, jak zaregistrovat aplikaci v Azure Active Directory pomocí nového okna Azure Active Directory. Aplikace zaregistrované v Azure Active Directory umožňují uživatelům ověřování a autorizaci používat rozhraní API služby Azure Time Series Insights přidružené k prostředí Azure Time Series Insights.

## <a name="service-principal"></a>Instanční objekt

Následující části popisují, jak nakonfigurovat aplikaci pro přístup k rozhraní Azure Time Series Insights API jménem aplikace. Aplikace pak může dotazovat nebo publikovat referenční data v prostředí Azure Time Series Insights pomocí vlastních přihlašovacích údajů aplikace prostřednictvím Azure Active Directory.

## <a name="summary-and-best-practices"></a>Souhrn a osvědčené postupy

Registrační tok aplikace Azure Active Directory zahrnuje tři hlavní kroky.

1. [Registrace aplikace](#azure-active-directory-app-registration) v Azure Active Directory.
1. Autorizujte aplikaci tak, aby měla [přístup k datům Azure Time Series Insights prostředí](#granting-data-access).
1. Použijte **ID aplikace** a **tajný klíč klienta** k získání tokenu z `https://api.timeseries.azure.com/` [klientské aplikace](#client-app-initialization). Token se pak dá použít k volání rozhraní Azure Time Series Insights API.

V rámci **kroku 3**oddělení aplikace a přihlašovací údaje uživatele vám umožní:

* Přiřaďte oprávnění identitě aplikace, která se liší od vašich vlastních oprávnění. Tato oprávnění se většinou omezují jenom na to, co aplikace vyžaduje. Aplikaci můžete například dovolit číst data pouze z konkrétního Azure Time Series Insightsho prostředí.
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

1. V prostředí Azure Time Series Insights vyberte **zásady přístupu k datům** a vyberte **Přidat**.

   [![Přidání nových zásad přístupu k datům do prostředí Azure Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. V dialogovém okně **Vybrat uživatele** vložte **název aplikace** nebo **ID aplikace** z části registrace aplikace Azure Active Directory.

   [![Vyhledání aplikace v dialogovém okně Vybrat uživatele](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Vyberte roli. Výběr **čtecího zařízení** pro dotazování dat nebo **přispěvatele** k dotazování na data a změně referenčních dat. Vyberte **OK**.

   [![Výběr čtecího modulu nebo přispěvatele v dialogovém okně Vybrat roli uživatele](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Uložte zásadu výběrem **OK**.

   > [!TIP]
   > V případě pokročilých možností přístupu k datům si přečtěte téma [udělení přístupu k datům](./time-series-insights-data-access.md).

### <a name="client-app-initialization"></a>Inicializace klientské aplikace

* Vývojáři můžou k ověřování pomocí Azure Time Series Insights použít [Microsoft Authentication Library (MSAL).

* Ověření pomocí MSAL:

   1. Použijte **ID aplikace** a **tajný klíč klienta** (aplikační klíč) z části registrace aplikace Azure Active Directory k získání tokenu jménem aplikace.

   1. V jazyce C# může následující kód získat token jménem aplikace. Úplný příklad pro dotazování dat z prostředí Gen1 najdete v článku o [dotazování dat pomocí jazyka C#](time-series-insights-query-data-csharp.md).

        Chcete-li získat přístup k kódu jazyka C#, Prohlédněte si [Azure Time Series Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen1-sample/csharp-tsi-gen1-sample/Program.cs)] úložiště.

   1. Token se pak může předat v hlavičce, `Authorization` když aplikace volá rozhraní Azure Time Series Insights API.

> [!IMPORTANT]
> Pokud Azure Active Directory používáte MSAL [Authentication Library (ADAL)](https://docs.microsoft.com/azure/active-directory/azuread-dev/active-directory-authentication-libraries) , přečtěte si o [migraci do](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration).

## <a name="common-headers-and-parameters"></a>Společné hlavičky a parametry

Tato část popisuje společné hlavičky a parametry požadavků protokolu HTTP, které slouží k vytváření dotazů proti Azure Time Series Insights rozhraní API Gen1 a Gen2. Požadavky na konkrétní rozhraní API jsou podrobněji popsány v [referenční dokumentaci Azure Time Series Insights REST API](https://docs.microsoft.com/rest/api/time-series-insights/).

> [!TIP]
> Další informace o tom, jak využívat rozhraní REST API, dělat požadavky HTTP a zpracovávat odpovědi HTTP, najdete v referenčních informacích k [Azure REST API](https://docs.microsoft.com/rest/api/azure/) .

### <a name="authentication"></a>Authentication

Aby bylo možné provádět ověřené dotazy proti [Azure Time Series Insights rozhraní REST API](https://docs.microsoft.com/rest/api/time-series-insights/), musí se v [autorizační hlavičce](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate) předávat platný token OAuth 2,0 s použitím klienta REST podle vašeho výběru (post, JavaScript, C#).

> [!TIP]
> Přečtěte si [ukázkovou vizualizaci hostované Azure Time Series Insights klientské sady SDK](https://tsiclientsample.azurewebsites.net/) a Naučte se ověřovat pomocí rozhraní api pro Azure Time Series Insights programově pomocí [klientské sady SDK pro JavaScript](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) spolu s grafy a grafy.

### <a name="http-headers"></a>Hlavičky protokolu HTTP

Požadované hlavičky požadavku jsou popsány níže.

| Požadovaná hlavička žádosti | Description |
| --- | --- |
| Autorizace | Chcete-li provést ověření pomocí Azure Time Series Insights, musí být do **autorizační** hlavičky předána platný nosný token OAuth 2,0. |

> [!IMPORTANT]
> Token se musí vystavit přesně `https://api.timeseries.azure.com/` prostředku (označuje se také jako "cílová skupina" tokenu).

> * Váš [Postman](https://www.getpostman.com/) **AuthURL** bude mít tedy následující:`https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com//.default`
> * `https://api.timeseries.azure.com/` je platný, ale není `https://api.timeseries.azure.com` .

Volitelné hlavičky požadavku jsou popsány níže.

| Nepovinná hlavička požadavku | Description |
| --- | --- |
| Typ obsahu | `application/json`podporuje se jenom. |
| x-MS-Client-Request-ID | ID žádosti klienta. Služba zaznamená tuto hodnotu. Umožňuje službě sledovat operace napříč službami. |
| x-MS-Client-Session-ID | ID klientské relace. Služba zaznamená tuto hodnotu. Umožňuje službě trasovat skupinu souvisejících operací napříč službami. |
| x-MS-Client-Application-Name | Název aplikace, která vygenerovala tento požadavek. Služba zaznamená tuto hodnotu. |

Volitelné, ale Doporučené hlavičky odpovědí jsou popsány níže.

| Hlavička odpovědi | Description |
| --- | --- |
| Typ obsahu | `application/json`Podporuje se jenom. |
| x-MS-Request-ID | ID žádosti generované serverem Dá se použít ke kontaktování žádosti Microsoftu o vyšetření žádosti. |
| x-MS-Property-Nenalezeno – chování | Volitelná hlavička odpovědi pro rozhraní API GA Možné hodnoty jsou `ThrowError` (výchozí) nebo `UseNull` . |

### <a name="http-parameters"></a>Parametry HTTP

> [!TIP]
> Další informace o požadovaných a volitelných informacích o dotazech najdete v [referenční dokumentaci](https://docs.microsoft.com/rest/api/time-series-insights/).

Požadované parametry řetězce dotazu adresy URL závisí na verzi rozhraní API.

| Vydat | Možné hodnoty verze rozhraní API |
| --- |  --- |
| Gen1 | `api-version=2016-12-12`|
| Gen2 | `api-version=2020-07-31` a `api-version=2018-11-01-preview`|

> [!IMPORTANT]
>
> `api-version=2018-11-01-preview`Verze bude brzy zastaralá. Doporučujeme, aby uživatelé přešli na novější verzi.

Volitelné parametry řetězce dotazu adresy URL zahrnují nastavení časového limitu pro dobu provádění požadavku HTTP.

| Volitelný parametr dotazu | Popis | Verze |
| --- |  --- | --- |
| `timeout=<timeout>` | Časový limit pro provedení požadavku HTTP na straně serveru. Dá se použít jenom pro [události Get prostředí](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) a rozhraní API pro [agregace prostředí](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api) . Hodnota časového limitu by měla být ve formátu trvání ISO 8601, například `"PT20S"` a měla by být v rozsahu `1-30 s` . Výchozí hodnota je `30 s` . | Gen1 |
| `storeType=<storeType>` | Pro prostředí Gen2 s povoleným teplým úložištěm se dotaz dá spustit buď na `WarmStore` nebo `ColdStore` . Tento parametr v dotazu definuje, na kterém úložišti se má dotaz spustit. Pokud není definován, dotaz se spustí v chladírenském skladu. Pro dotazování na záložní úložiště musí být **storeType** nastavené na `WarmStore` . Pokud není definován, dotaz se spustí pro chladírenský sklad. | Gen2 |

## <a name="next-steps"></a>Další kroky

* Pro vzorový kód, který volá rozhraní API Gen1 Azure Time Series Insights, přečtěte si [data Gen1 dotazů pomocí jazyka C#](./time-series-insights-query-data-csharp.md).

* Vzorový kód, který volá ukázky kódu rozhraní API Gen2 Azure Time Series Insights, čte [data Gen2 dotazů pomocí jazyka C#](./time-series-insights-update-query-data-csharp.md).

* Informace o referenčních informacích k rozhraní API najdete v referenční dokumentaci k [rozhraní API pro dotazy](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api) .

* Naučte se [vytvořit instanční objekt](../active-directory/develop/howto-create-service-principal-portal.md).
