---
title: Ověřování a autorizace rozhraní API – Azure Time Series Insights | Microsoft Docs
description: Tento článek popisuje, jak nakonfigurovat ověřování a autorizaci pro vlastní aplikaci, která volá rozhraní Azure Time Series Insights API.
ms.service: time-series-insights
author: deepakpalled
ms.author: shresha
manager: dpalled
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/23/2021
ms.custom: seodec18, has-adal-ref, devx-track-azurecli
ms.openlocfilehash: c174504ea48330821d7734def5b6a5904d3ec2b8
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107883917"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Ověřování a autorizace pro rozhraní API služby Azure Time Series Insights

V závislosti na vašich obchodních potřebách může vaše řešení zahrnovat jednu nebo víc klientských aplikací, které používáte k interakci s [rozhraními api](/rest/api/time-series-insights/reference-data-access-overview)Azure Time Series Insightsho prostředí. Azure Time Series Insights provádí ověřování pomocí [tokenů zabezpečení Azure AD na základě OAUTH 2,0](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims). Pokud chcete ověřit klienty, budete muset získat token nosiče se správnými oprávněními a předat ho spolu s vaším voláním rozhraní API. Tento dokument popisuje několik metod získání přihlašovacích údajů, které můžete použít k získání nosného tokenu a k ověření, včetně použití spravované identity a Azure Active Directory registrace aplikací.

## <a name="managed-identities"></a>Spravované identity

Následující části popisují, jak pomocí spravované identity z Azure Active Directory (Azure AD) získat přístup k rozhraní Azure Time Series Insights API. V Azure spravované identity eliminují nutnost vývojářů spravovat přihlašovací údaje tím, že poskytuje identitu prostředku Azure ve službě Azure AD a použije ho k získání tokenů Azure Active Directory (Azure AD). Zde jsou některé výhody použití spravovaných identit:

- Nemusíte spravovat přihlašovací údaje. Přihlašovací údaje nejsou k dispozici ani pro vás.
- Spravované identity můžete použít k ověření pro libovolnou službu Azure, která podporuje ověřování Azure AD, včetně Azure Key Vault.
- Spravované identity lze použít bez jakýchkoli dalších nákladů.

Další informace o těchto dvou typech spravovaných identit najdete v tématu [co jsou spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md)

Spravované identity můžete použít z těchto:

- Virtuální počítače Azure
- Azure App Services
- Azure Functions
- Azure Container Instances
- a další...

Úplný seznam najdete v tématu [služby Azure, které podporují spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) .

## <a name="azure-active-directory-app-registration"></a>Registrace aplikace Azure Active Directory

Pokud je to možné, doporučujeme používat spravované identity, takže nemusíte spravovat přihlašovací údaje. Pokud vaše klientská aplikace není hostovaná ve službě Azure, která podporuje spravované identity, můžete svoji aplikaci zaregistrovat v tenantovi Azure AD. Při registraci aplikace ve službě Azure AD vytváříte konfiguraci identity pro vaši aplikaci, která umožňuje integraci se službou Azure AD. Při registraci aplikace v [Azure Portal](https://portal.azure.com/)zvolíte, zda se jedná o jediného tenanta (k dispozici pouze ve vašem tenantovi) nebo k víceklientskému přístupu (přístup v jiných klientech), a volitelně můžete nastavit identifikátor URI přesměrování (kam se přístupový token posílá).

Po dokončení registrace aplikace budete mít globálně jedinečnou instanci aplikace (objekt aplikace), která je umístěná v rámci vašeho domovského tenanta nebo adresáře. Máte také globálně jedinečné ID vaší aplikace (ID aplikace nebo klienta). Na portálu můžete přidat tajné klíče, certifikáty a obory, aby vaše aplikace fungovala, přizpůsobení značky vaší aplikace v dialogovém okně přihlášení a další.

Pokud zaregistrujete aplikaci na portálu, objekt aplikace a objekt instančního objektu se automaticky vytvoří v domovském tenantovi. Pokud zaregistrujete nebo vytvoříte aplikaci pomocí rozhraní Microsoft Graph API, vytvoří se objekt instančního objektu jako samostatný krok. Pro žádosti o tokeny je vyžadován objekt instančního objektu.

Nezapomeňte si projít kontrolní seznam [zabezpečení](../active-directory/develop/identity-platform-integration-checklist.md#security) pro vaši aplikaci. Osvědčeným postupem je použití [přihlašovacích údajů k certifikátu](../active-directory/develop/active-directory-certificate-credentials.md), nikoli přihlašovacích údajů pro heslo (Client tajných klíčů).

Další podrobnosti najdete [v tématu aplikace a objekty zabezpečení služby v Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md) .

## <a name="step-1-create-your-managed-identity-or-app-registration"></a>Krok 1: vytvoření spravované identity nebo registrace aplikace

Jakmile zjistíte, jestli budete používat spravovanou identitu nebo registraci aplikace, je dalším krokem zřídit ho.

### <a name="managed-identity"></a>Spravovaná identita

Kroky, které použijete k vytvoření spravované identity, se budou lišit v závislosti na tom, kde se nachází váš kód, a to bez ohledu na to, jestli vytváříte identitu přiřazenou systémem nebo uživatelem přiřazenou uživateli. Pro pochopení rozdílu si přečtěte [spravované typy identity](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) . Po výběru typu identity Najděte a postupujte podle správného kurzu v [dokumentaci k](../active-directory/managed-identities-azure-resources/index.yml)identitám spravovaným službou Azure AD. Najdete tady pokyny ke konfiguraci spravovaných identit pro:

- [Virtuální počítače Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-during-creation-of-a-vm)
- [App Service a Azure Functions](../app-service/overview-managed-identity.md)
- [Azure Container Instances](../container-instances/container-instances-managed-identity.md)
- a další...

### <a name="application-registration"></a>Registrace aplikací

Postupujte podle kroků uvedených v části [Registrace aplikace](../active-directory/develop/quickstart-register-app.md#register-an-application).

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="step-2-grant-access"></a>Krok 2: udělení přístupu

Když Azure Time Series Insights prostředí obdrží požadavek, nejdřív se ověří nosný token volajícího. Pokud ověření projde, volající se ověřil a pak se provede jiná kontrola, aby byl volající autorizovaný k provedení požadované akce. Pokud chcete autorizovat libovolného uživatele nebo instanční objekt, musíte jim nejdřív udělit přístup k prostředí tím, že jim přiřadíte roli Čtenář nebo Přispěvatel.

- Pokud chcete udělit přístup prostřednictvím uživatelského rozhraní [Azure Portal](https://portal.azure.com/) , postupujte podle pokynů uvedených v článku [udělení přístupu k datům do prostředí](concepts-access-policies.md) . Při výběru uživatele můžete vyhledat spravovanou identitu nebo registraci aplikace podle jejího názvu nebo podle ID.

- Pokud chcete udělit přístup pomocí Azure CLI, spusťte následující příkaz. Úplný seznam příkazů, které jsou k dispozici pro správu přístupu, najdete [v dokumentaci.](/cli/azure/tsi/access-policy)

   ```azurecli-interactive
   az tsi access-policy create --name "ap1" --environment-name "env1" --description "some description" --principal-object-id "aGuid" --roles Reader Contributor --resource-group "rg1"
   ```

> [!Note]
> Rozšíření timeseriesinsights pro rozhraní příkazového řádku Azure CLI vyžaduje verzi 2.11.0 nebo vyšší. Rozšíření se automaticky nainstaluje při prvním spuštění příkazu AZ TSI Access-Policy. [Přečtěte si další informace](/cli/azure/azure-cli-extensions-overview) o rozšířeních.

## <a name="step-3-requesting-tokens"></a>Krok 3: vyžadování tokenů

Jakmile se spravovaná identita nebo registrace aplikace zřídí a přiřadí roli, budete připraveni ji začít používat k vyžádání tokenů Bearer OAuth 2,0. Metoda, kterou použijete k získání tokenu, se liší v závislosti na tom, kde je váš kód hostovaný, a zvolený jazyk. Při zadávání prostředku (označuje se také jako "cílová skupina" v tokenu) můžete identifikovat Azure Time Series Insights podle jeho adresy URL nebo identifikátoru GUID:

* `https://api.timeseries.azure.com/`
* `120d688d-1518-4cf7-bd38-182f158850b6`

> [!IMPORTANT]
> Použijete-li adresu URL jako ID prostředku, musí být token vydán přesně na `https://api.timeseries.azure.com/` . Je požadováno koncové lomítko.

> * Pokud používáte [metodu post](https://www.getpostman.com/) , bude vaše **AuthURL** proto: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com//.default`
> * `https://api.timeseries.azure.com/` je platný, ale není `https://api.timeseries.azure.com` .

### <a name="managed-identities"></a>Spravované identity

Při přístupu z Azure App Service nebo funkcí postupujte podle pokynů v části [získání tokenů pro prostředky Azure](../app-service/overview-managed-identity.md).

Pro aplikace a funkce .NET je nejjednodušší způsob práce se spravovanou identitou prostřednictvím [klientské knihovny Azure identity](/dotnet/api/overview/azure/identity-readme) pro .NET. Tato Klientská knihovna je oblíbená z důvodu zjednodušení a výhod zabezpečení. Vývojáři mohou napsat kód jednou a povolit klientské knihovně, aby určili, jak ověřit na základě prostředí aplikace – ať už na pracovní stanici pro vývojáře pomocí účtu vývojáře nebo nasazeného v Azure pomocí identity spravované služby. Pokyny k migraci z předchůdce knihovny AppAuthentication najdete v článku [pokyny pro migraci AppAuthentication na Azure. identity](/dotnet/api/overview/azure/app-auth-migration).

Vyžádání tokenu pro Azure Time Series Insights pomocí jazyka C# a klientské knihovny Azure identity pro .NET:

   ```csharp
   using Azure.Identity;
   // ...
   var credential = new DefaultAzureCredential();
   var token = credential.GetToken(
   new Azure.Core.TokenRequestContext(
       new[] { "https://api.timeseries.azure.com/" }));
   var accessToken = token.Token;
   ```

### <a name="app-registration"></a>Registrace aplikace

* Vývojáři mohou pomocí [knihovny Microsoft Authentication Library](../active-directory/develop/msal-overview.md) (MSAL) získat tokeny pro registraci aplikací.

MSAL se dá použít v mnoha scénářích aplikací, včetně, ale ne omezení na:

* [Jednostránkové aplikace (JavaScript)](../active-directory/develop/scenario-spa-overview.md)
* [Webová aplikace, která přihlašuje uživatele a volá webové rozhraní API jménem uživatele](../active-directory/develop/scenario-web-app-call-api-overview.md)
* [Webové rozhraní API, které jménem přihlášeného uživatele volá jiné webové rozhraní API pro příjem dat](../active-directory/develop/scenario-web-api-call-api-overview.md)
* [Aplikace klasické pracovní plochy, která jménem přihlášeného uživatele volá webové rozhraní API](../active-directory/develop/scenario-desktop-overview.md)
* [Mobilní aplikace, která volá webové rozhraní API jménem uživatele, který je přihlášený interaktivně](../active-directory/develop/scenario-mobile-overview.md).
* [Aplikace démona plochy/služby – volání webového rozhraní API jménem sebe sama](../active-directory/develop/scenario-daemon-overview.md)

Vzorový kód jazyka C#, který ukazuje, jak získat token jako registraci aplikace a data dotazů z prostředí Gen2, najdete v ukázkové aplikaci na [GitHubu](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/Program.cs) .

> [!IMPORTANT]
> Pokud Azure Active Directory používáte MSAL [Authentication Library (ADAL)](../active-directory/azuread-dev/active-directory-authentication-libraries.md) , přečtěte si o [migraci do](../active-directory/develop/msal-net-migration.md).

## <a name="common-headers-and-parameters"></a>Společné hlavičky a parametry

Tato část popisuje společné hlavičky a parametry požadavků protokolu HTTP, které slouží k vytváření dotazů proti Azure Time Series Insights rozhraní API Gen1 a Gen2. Požadavky na konkrétní rozhraní API jsou podrobněji popsány v [referenční dokumentaci Azure Time Series Insights REST API](/rest/api/time-series-insights/).

> [!TIP]
> Další informace o tom, jak využívat rozhraní REST API, dělat požadavky HTTP a zpracovávat odpovědi HTTP, najdete v referenčních informacích k [Azure REST API](/rest/api/azure/) .

### <a name="http-headers"></a>Hlavičky protokolu HTTP

Požadované hlavičky požadavku jsou popsány níže.

| Požadovaná hlavička žádosti | Description |
| --- | --- |
| Autorizace | Chcete-li provést ověření pomocí Azure Time Series Insights, musí být do [autorizační hlavičky](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate)předána platný nosný token OAuth 2,0. |

> [!TIP]
> Přečtěte si [ukázkovou vizualizaci hostované Azure Time Series Insights klientské sady SDK](https://tsiclientsample.azurewebsites.net/) a Naučte se ověřovat pomocí rozhraní api pro Azure Time Series Insights programově pomocí [klientské sady SDK pro JavaScript](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) spolu s grafy a grafy.

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
> Další informace o požadovaných a volitelných informacích o dotazech najdete v [referenční dokumentaci](/rest/api/time-series-insights/).

Požadované parametry řetězce dotazu adresy URL závisí na verzi rozhraní API.

| Vydat | Hodnoty verze rozhraní API |
| --- |  --- |
| Gen1 | `api-version=2016-12-12`|
| Gen2 | `api-version=2020-07-31`|

Volitelné parametry řetězce dotazu adresy URL zahrnují nastavení časového limitu pro dobu provádění požadavku HTTP.

| Volitelný parametr dotazu | Popis | Verze |
| --- |  --- | --- |
| `timeout=<timeout>` | Časový limit pro provedení požadavku HTTP na straně serveru. Dá se použít jenom pro [události Get prostředí](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) a rozhraní API pro [agregace prostředí](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api) . Hodnota časového limitu by měla být ve formátu trvání ISO 8601, například `"PT20S"` a měla by být v rozsahu `1-30 s` . Výchozí hodnota je `30 s`. | Gen1 |
| `storeType=<storeType>` | Pro prostředí Gen2 s povoleným teplým úložištěm se dotaz dá spustit buď na `WarmStore` nebo `ColdStore` . Tento parametr v dotazu definuje, na kterém úložišti se má dotaz spustit. Pokud není definován, dotaz se spustí v chladírenském skladu. Pro dotazování na záložní úložiště musí být **storeType** nastavené na `WarmStore` . Pokud není definován, dotaz se spustí pro chladírenský sklad. | Gen2 |

## <a name="next-steps"></a>Další kroky

* Pro vzorový kód, který volá rozhraní API Gen1 Azure Time Series Insights, přečtěte si [data Gen1 dotazů pomocí jazyka C#](./time-series-insights-query-data-csharp.md).

* Vzorový kód, který volá ukázky kódu rozhraní API Gen2 Azure Time Series Insights, čte [data Gen2 dotazů pomocí jazyka C#](./time-series-insights-update-query-data-csharp.md).

* Informace o referenčních informacích k rozhraní API najdete v referenční dokumentaci k [rozhraní API pro dotazy](/rest/api/time-series-insights/reference-query-apis) .
