---
title: Kurz konfigurace Azure Active Directory B2C pomocí Experian
titleSuffix: Azure AD B2C
description: Naučte se integrovat Azure AD B2C ověřování pomocí Experian pro ověřování totožnosti a kontrolu na základě atributů uživatele, abyste zabránili podvodům.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: a88894bb7462e9ac3afd16d69ae820dd98543a5f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91259369"
---
# <a name="tutorial-for-configuring-experian-with-azure-active-directory-b2c"></a>Kurz pro konfiguraci Experian s využitím Azure Active Directory B2C

V tomto ukázkovém kurzu poskytujeme pokyny pro integraci Azure AD B2C s [Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration). Experian poskytuje celou řadu řešení, která můžete najít [tady](https://www.experian.com/).

V této ukázce se používá integrovaná Digitální identita Experian a riziková platforma **Crosscore** pro podvod. CrossCore je ověřovací služba ID, která se používá k ověření identifikace uživatele. Analýza rizik vychází z několika informací poskytnutých uživatelem během procesu registrace. CrossCore se používá k určení, zda by měl uživatel mít povoleno pokračovat v přihlašování. V CrossCore analýze rizik lze použít následující atributy:

- E-mail
- IP adresa
- jméno
- Druhé jméno
- příjmení
- Adresa
- City (Město)
- Stát/kraj
- PSČ
- Země/oblast
- Telefonní číslo

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, budete potřebovat:

- Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).

- [Tenant Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) , který je propojený s vaším předplatným Azure.

## <a name="scenario-description"></a>Popis scénáře

Integrace Experian zahrnuje tyto komponenty:

- Azure AD B2C – autorizační Server zodpovědný za ověření přihlašovacích údajů uživatele, označovaný také jako zprostředkovatel identity

- Experian – služba Experian přebírá vstupy poskytované uživatelem a ověřuje identitu uživatele.

- Vlastní rozhraní REST API – toto rozhraní API implementuje integraci mezi Azure AD B2C a službou Experian.

V následujícím diagramu architektury se zobrazuje implementace.

![snímek obrazovky pro Experian – architektura – diagram](media/partner-experian/experian-architecture-diagram.png)

|Krok | Popis |
|:-----| :-----------|
| 1. | Uživatel dorazí na přihlašovací stránku. Uživatel vybere registraci a vytvoří nový účet a zadá informace na stránku. Azure AD B2C shromažďuje atributy uživatele.
| 2. | Azure AD B2C volá rozhraní API střední vrstvy a předá ho atributům uživatele.
| 3. | Rozhraní API prostřední vrstvy shromažďuje atributy uživatelů a transformuje je do formátu, který může Experian API spotřebovat. Pak ho pošle do Experian.
| 4. | Experian využívá informace a zpracovává je k ověření identifikace uživatele na základě analýzy rizik. Pak vrátí výsledek do prostřední vrstvy API.
| 5. | Rozhraní API střední vrstvy zpracovává informace a pošle zpátky relevantní informace ve správném formátu JSON pro Azure AD B2C.
| 6. | Azure AD B2C přijímá informace zpět z rozhraní API střední vrstvy. Pokud se zobrazí reakce na selhání, zobrazí se uživateli chybová zpráva. Pokud se zobrazí odpověď úspěšnosti, je uživatel ověřen a zapsán do adresáře.

## <a name="onboard-with-experian"></a>Zprovoznění s Experian

1. Pokud chcete vytvořit účet Experian, kontaktujte [Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration) .

2. Po vytvoření účtu obdržíte informace, které potřebujete pro konfiguraci rozhraní API. Následující části popisují proces.

## <a name="configure-azure-ad-b2c-with-experian"></a>Konfigurace Azure AD B2C pomocí Experian

### <a name="part-1---deploy-the-api"></a>Část 1 – nasazení rozhraní API

Nasaďte poskytnutý [kód rozhraní API](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Experian/CrossCoreIntegrationApi/CrossCoreIntegrationApi.sln) do služby Azure. Kód lze publikovat ze sady Visual Studio, a to podle těchto [pokynů](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019).

>[!NOTE]
>Abyste mohli Azure AD nakonfigurovat pomocí požadovaných nastavení, budete potřebovat adresu URL nasazené služby.

### <a name="part-2---deploy-the-client-certificate"></a>Část 2 – nasazení klientského certifikátu

Volání rozhraní API Experian je chráněno klientským certifikátem. Tento klientský certifikát poskytne Experian. Podle pokynů uvedených v tomto [dokumentu](https://docs.microsoft.com/azure/app-service/environment/certificates#private-client-certificate)se musí certifikát nahrát do služby Azure App Service. Vzorová zásada používá tyto klíče kroků v procesu:

- Odeslání certifikátu

- Nastavte `WEBSITE_LOAD_ROOT_CERTIFICATES` klíč s kryptografickým otiskem certifikátu.

### <a name="part-3---configure-the-api"></a>Část 3 – konfigurace rozhraní API

Nastavení aplikace je možné [nakonfigurovat ve službě App Service v Azure](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings). S touto metodou se dají nastavení bezpečně nakonfigurovat bez jejich kontroly do úložiště. Pro rozhraní REST API musíte zadat následující nastavení:

| Nastavení aplikace | Zdroj | Poznámky |
| :-------- | :------------| :-----------|
|CrossCoreConfig: TenantId | Konfigurace účtu Experian |     |
|CrossCoreConfig:OrgCode | Konfigurace účtu Experian |     |
|CrossCore:ApiEndpoint |Konfigurace účtu Experian|  |
|CrossCore:ClientReference | Konfigurace účtu Experian | |
| CrossCore:ModelCode |Konfigurace účtu Experian|
| CrossCore:OrgCode | Konfigurace účtu Experian |
| CrossCore:SignatureKey  | Konfigurace účtu Experian |
| CrossCore: TenantId  | Konfigurace účtu Experian |
| CrossCore: CertificateThumbprint | Experian certifikát |
| BasicAuth:ApiUsername | Definování uživatelského jména pro rozhraní API | Použito v konfiguraci ExtId |
| BasicAuth:ApiPassword | Definování hesla pro rozhraní API | Použito v konfiguraci ExtId

### <a name="part-4---create-api-policy-keys"></a>Část 4 – vytvoření klíčů zásad rozhraní API

Přečtěte si tento [dokument](https://docs.microsoft.com/azure/active-directory-b2c/secure-rest-api#add-rest-api-username-and-password-policy-keys) a vytvořte dva klíče zásad – jeden pro uživatelské jméno rozhraní API a druhý pro heslo rozhraní API, které jste definovali pro základní ověřování HTTP.

>[!NOTE]
>Budete potřebovat klíče pro konfiguraci zásad později.

### <a name="part-5---replace-the-configuration-values"></a>Část 5 – nahrazení hodnot konfigurace

V poskytnutých [vlastních zásadách](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Experian/policy)vyhledejte následující zástupné symboly a nahraďte je odpovídajícími hodnotami z vaší instance.

|                      Zástupný symbol                       |                                   Nahradit hodnotou                                 |                   Příklad                    |
| ------------------------------------------------------ | -------------------------------------------------------------------------------- | -------------------------------------------- |
| {your_tenant_name}                                     | Krátký název tenanta                                                           | "yourtenant" z yourtenant.onmicrosoft.com |
| {your_trustframeworkbase_policy}                       | Azure AD B2C název zásad TrustFrameworkBase                  | B2C_1A_experian_TrustFrameworkBase           |
| {your_tenant_IdentityExperienceFramework_appid}        | ID aplikace IdentityExperienceFramework nakonfigurované ve vašem tenantovi Azure AD B2C      | 01234567-89AB-CDEF-0123-456789ABCDEF         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | ID aplikace ProxyIdentityExperienceFramework nakonfigurované ve vašem tenantovi Azure AD B2C | 01234567-89AB-CDEF-0123-456789ABCDEF         |
| {your_tenant_extensions_appid}                         | ID aplikace pro aplikaci úložiště vašeho tenanta                                      | 01234567-89AB-CDEF-0123-456789ABCDEF         |
| {your_tenant_extensions_app_objectid}                  | ID objektu aplikace úložiště vašeho tenanta                                   | 01234567-89AB-CDEF-0123-456789ABCDEF         |
| {your_api_username_key_name}                           | Název klíče uživatelského jména, který jste [tady](#part-4---create-api-policy-keys) vytvořili             | B2C \_ 1a \_ RestApiUsername                     |
| {your_api_password_key_name}                           | Název klíče hesla, který jste [tady](#part-4---create-api-policy-keys) vytvořili             | B2C \_ 1a \_ RestApiPassword                     |
| {your_app_service_URL}                                 | Adresa URL služby App Service, kterou jste nastavili                                             | `https://yourapp.azurewebsites.net`          |

### <a name="part-6---configure-the-azure-ad-b2c-policy"></a>Část 6 – konfigurace zásad Azure AD B2C

Pokyny, jak nastavit tenanta Azure AD B2C a nakonfigurovat zásady, najdete v tomto [dokumentu](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack) .

>[!NOTE]
>Tato ukázková zásada vychází z [Úvodní sady místních účtů](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts).

>[!NOTE]
> Jako osvědčený postup doporučujeme, aby zákazníci přidávají oznámení o souhlasu na stránce kolekce atributů. Upozorněte uživatele, že informace budou odeslány službám třetích stran pro ověření identity.

## <a name="test-the-user-flow"></a>Testování toku uživatele

1. Otevřete Azure AD B2C tenant a v části zásady vyberte **toky uživatelů**.

2. Vyberte dříve vytvořený **tok uživatele**.

3. Vyberte **Spustit tok uživatele** a vyberte nastavení:

   a. **Aplikace**: vyberte registrovanou aplikaci (ukázka je JWT).

   b. **Adresa URL odpovědi**: vyberte **adresu URL pro přesměrování** .

   c. Vyberte **Spustit tok uživatele**.

4. Projděte si registrační tok a vytvořte účet.

5. Odhlášení

6. Projděte si tok přihlášení.  

7. Po zadání **pokračování**se zobrazí Crosscore skládanky.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

- [Vlastní zásady v Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Začínáme s vlastními zásadami v Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
