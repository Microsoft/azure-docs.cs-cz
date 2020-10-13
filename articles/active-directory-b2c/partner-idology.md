---
title: Integrace IDology s Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Naučte se integrovat ukázkovou online platební aplikaci do Azure AD B2C pomocí IDology. IDology je ověření identity a poskytovatel kontroly pravopisu s více řešeními.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cfce5b42d37908d0ba89cff9c4831cb25b968524
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91259318"
---
# <a name="tutorial-for-configuring-idology-with-azure-active-directory-b2c"></a>Kurz pro konfiguraci IDology s využitím Azure Active Directory B2C 

V tomto ukázkovém kurzu poskytujeme pokyny pro integraci Azure AD B2C s [IDology](https://www.idology.com/solutions/). IDology je ověření identity a poskytovatel kontroly pravopisu s více řešeními. V této ukázce budeme pokrývat řešení ExpectID podle IDology.

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, budete potřebovat:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* [Tenant Azure AD B2C](tutorial-create-tenant.md) , který je propojený s vaším předplatným Azure.

## <a name="scenario-description"></a>Popis scénáře

Integrace IDology zahrnuje tyto komponenty:

- Azure AD B2C – autorizační Server zodpovědný za ověření přihlašovacích údajů uživatele. Označuje se také jako zprostředkovatel identity.
- IDology – služba IDology přebírá vstup poskytnutý uživatelem a ověřuje identitu uživatele.
- Vlastní rozhraní REST API – toto rozhraní API implementuje integraci mezi Azure AD a službou IDology.

V následujícím diagramu architektury se zobrazuje implementace.

![Diagram architektury IDology](media/partner-idology/idology-architecture-diagram.png)

| Krok | Description |
|------|------|
|1     | Uživatel přijde na přihlašovací stránku. |
|2     | Uživatel vybere možnost registrace pro vytvoření nového účtu a zadání informací do stránky. Azure AD B2C shromažďuje atributy uživatele. |
|3     | Azure AD B2C volá rozhraní API střední vrstvy a předá ho atributům uživatele. |
|4     | Rozhraní API střední vrstvy shromažďuje atributy uživatelů a transformuje je do formátu, který může IDOlogy API spotřebovat. Pak pošle informace do IDology. |
|5     | IDology spotřebovává informace a zpracovává je a potom vrátí výsledek do rozhraní API střední vrstvy. |
|6     | Rozhraní API střední vrstvy tyto informace zpracovává a odesílá příslušné informace zpět do Azure AD B2C. |
|7     | Azure AD B2C přijímá informace zpět z rozhraní API střední vrstvy. Pokud se zobrazí reakce na **selhání** , zobrazí se uživateli chybová zpráva. Pokud se zobrazí odpověď **úspěšnosti** , je uživatel ověřen a zapsán do adresáře. |
|      |      |

> [!NOTE]
> Azure AD B2C může také požádat zákazníka o provedení podrobného ověřování, ale tento scénář je mimo rozsah tohoto kurzu.

## <a name="onboard-with-idology"></a>Zprovoznění s IDology

1. IDology poskytuje celou řadu řešení, která můžete najít [tady](https://www.idology.com/solutions/). V této ukázce používáme ExpectID.

2. Pokud chcete vytvořit účet IDology, kontaktujte [IDology](https://www.idology.com/request-a-demo/microsoft-integration-signup/).

3. Po vytvoření účtu obdržíte informace, které potřebujete pro konfiguraci rozhraní API. Následující části popisují proces.

## <a name="integrate-with-azure-ad-b2c"></a>Integrace s Azure AD B2C

### <a name="part-1---deploy-the-api"></a>Část 1 – nasazení rozhraní API

Nasaďte poskytnutý [kód rozhraní API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/Api) do služby Azure. Kód lze publikovat ze sady Visual Studio, a to podle těchto [pokynů](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019).

Abyste mohli Azure AD nakonfigurovat pomocí požadovaných nastavení, budete potřebovat adresu URL nasazené služby.

### <a name="part-2---configure-the-api"></a>Část 2 – konfigurace rozhraní API 

Nastavení aplikace je možné [nakonfigurovat v App Service v Azure](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings). S touto metodou se dají nastavení bezpečně nakonfigurovat bez jejich kontroly do úložiště. Pro rozhraní REST API musíte zadat následující nastavení:

| Nastavení aplikace | Zdroj | Poznámky |
| :-------- | :------------| :-----------|
|IdologySettings:ApiUsername | Konfigurace účtu IDology |     |
|IdologySettings:ApiPassword | Konfigurace účtu IDology |     |
|WebApiSettings:ApiUsername |Definování uživatelského jména pro rozhraní API| Použito v konfiguraci ExtId |
|WebApiSettings:ApiPassword | Definování hesla pro rozhraní API | Použito v konfiguraci ExtId

### <a name="part-3---create-api-policy-keys"></a>Část 3 – vytvoření klíčů zásad rozhraní API

Podle tohoto [dokumentu](secure-rest-api.md#add-rest-api-username-and-password-policy-keys) vytvořte dva klíče zásad: jednu pro uživatelské jméno rozhraní API a jednu pro heslo rozhraní API, které jste definovali výše.

Vzorová zásada používá tyto názvy klíčů:

* B2C_1A_RestApiUsername
* B2C_1A_RestApiPassword

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>Část 4 – Konfigurace zásad Azure AD B2C

1. Pomocí tohoto [dokumentu](custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) stáhněte [LocalAccounts Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) a nakonfigurujte zásady pro klienta Azure AD B2C. Postupujte podle pokynů, dokud nedokončíte oddíl **test pro vlastní zásady** .

2. [Zde](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/policy)si stáhněte dvě vzorové zásady.

3. Aktualizujte dvě ukázkové zásady:

   1. Otevřete obě zásady:

      1. V části `Idology-ExpectId-API` aktualizujte `ServiceUrl` položku metadata pomocí umístění rozhraní API nasazeného výše.

      1. Nahraďte `yourtenant` názvem vašeho tenanta Azure AD B2C.
      Například pokud je název vašeho tenanta Azure AD B2C  `contosotenant` , nahraďte všechny výskyty  `yourtenant.onmicrosoft.com`   pomocí `contosotenant.onmicrosoft.com` .

   1. Otevřete soubor TrustFrameworkExtensions.xml:

      1. Vyhledejte element  `<TechnicalProfile Id="login-NonInteractive">` . Nahraďte obě instance  `IdentityExperienceFrameworkAppId`   s ID aplikace aplikace IdentityExperienceFramework, kterou jste vytvořili dříve.

      1. Nahraďte obě instance  `ProxyIdentityExperienceFrameworkAppId`   s ID aplikace aplikace ProxyIdentityExperienceFramework, kterou jste vytvořili dříve.

4. Nahraďte SignInorSignUp.xml a TrustFrameworkExtensions.xml dříve nahraný do Azure AD B2C v kroku 1 se dvěma aktualizovanými ukázkovými zásadami.

> [!NOTE]
> Jako osvědčený postup doporučujeme, aby zákazníci přidávají oznámení o souhlasu na stránce kolekce atributů. Upozorněte uživatele, že informace budou zaslány službám třetích stran pro ověření identity.

## <a name="test-the-user-flow"></a>Testování toku uživatele

1. Otevřete Azure AD B2C tenant a v části **zásady**vyberte **toky uživatelů**.

2. Vyberte dříve vytvořený **tok uživatele**.

3. Vyberte **Spustit tok uživatele** a vyberte nastavení:

   1. **Aplikace** – vyberte registrovanou aplikaci (ukázka je JWT).

   1. **Adresa URL odpovědi** – vyberte **adresu URL pro přesměrování**.

   1. Vyberte **Spustit tok uživatele**.

4. Projděte si tok registrace a vytvořte účet.

5. Odhlaste se.

6. Projděte si tok přihlášení.

7. Po zadání **pokračování**se zobrazí IDology skládanky.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

- [Vlastní zásady v Azure AD B2C](custom-policy-overview.md)

- [Začínáme s vlastními zásadami v Azure AD B2C](custom-policy-get-started.md?tabs=applications) 

