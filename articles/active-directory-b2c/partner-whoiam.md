---
title: Kurz konfigurace Azure Active Directory B2C pomocí whoIam
titleSuffix: Azure AD B2C
description: Naučte se integrovat Azure AD B2C ověřování pomocí whoIam pro ověřování uživatelů.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 6545c5f40004dc50904618a8ea734eb73eeee933
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817430"
---
# <a name="tutorial-for-configuring-whoiam-with-azure-active-directory-b2c"></a>Kurz pro konfiguraci WhoIAM s využitím Azure Active Directory B2C

V tomto ukázkovém kurzu poskytujeme pokyny k tomu, jak ve svém prostředí nakonfigurovat [WhoIAM](https://www.whoiam.ai/brims/) (BRIMSed identity System) a jak ho integrovat s Azure AD B2C.

BRIMS WhoIAM je sada aplikací a služeb, které se ve vašem prostředí nasazují. Poskytuje hlasová, SMS a ověřování e-mailů vaší uživatelské základny. BRIMS funguje ve spojení s vaším stávajícím řešením pro správu identit a přístupu a je nezávislá platformy.

## <a name="prerequisites"></a>Předpoklady

Abyste mohli začít, budete potřebovat:

- Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).

- [Tenant Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) , který je propojený s vaším předplatným Azure.

- [Zkušební účet](https://www.whoiam.ai/contact-us/) WhoIAM

## <a name="scenario-description"></a>Popis scénáře

Integrace WhoIAM zahrnuje tyto komponenty:

- Azure AD B2C tenant – autorizační Server, který zodpovídá za ověření přihlašovacích údajů uživatele na základě vlastních zásad definovaných v tenantovi. Označuje se také jako zprostředkovatel identity.

- Portál pro správu pro správu klientů a jejich konfigurací

- Služba API, která zpřístupňuje různé funkce prostřednictvím koncových bodů  

- Azure Cosmos DB, která se používá jako back-end pro portál pro správu BRIMS i pro službu API

V následujícím diagramu architektury se zobrazuje implementace.

![snímek obrazovky pro whoiam – architektura – diagram](media/partner-whoiam/whoiam-architecture-diagram.png)

|Krok | Popis |
|:-----| :-----------|
| 1. | Uživatel dorazí na přihlašovací stránku. Uživatel spustí žádost o registraci nebo přihlášení do aplikace, která jako poskytovatele identity používá Azure AD B2C.
| 2. | V rámci ověřování si uživatel vyžádá buď, aby ověřil vlastnictví e-mailu nebo telefonu, nebo použil svůj hlas jako faktor biometrického ověřování.  
| 3. | Azure AD B2C zavolá službu API BRIMS, která předává přihlašovací e-mailovou adresu uživatele, telefonní číslo a záznam hlasu.
| 4. | BRIMS používá předdefinované konfigurace, jako jsou plně přizpůsobitelné e-maily a šablony SMS pro interakci s uživatelem v příslušném jazyce, který je konzistentní s pohledem a chováním aplikace.
| 5. | Po dokončení ověření identity uživatele BRIMS vrátí token pro Azure AD B2C, který indikuje výsledek ověření. Azure AD B2C pak buď udělí uživateli přístup k aplikaci, nebo se nezdaří pokus o ověření.  

## <a name="onboard-with-whoiam"></a>Zprovoznění s WhoIAM

1. Kontaktujte [WhoIAM](https://www.whoiam.ai/contact-us/) a vytvořte účet BRIMS.

2. Po vytvoření účtu použijte pokyny k registraci, které vám budou dostupné, a nakonfigurujte následující služby Azure:

    - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) – slouží k zabezpečenému ukládání hesla, jako jsou hesla poštovní služby.

    - [Azure App Service](https://azure.microsoft.com/services/app-service/) – slouží k hostování služeb rozhraní API BRIMS a portálu pro správu.

    - [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) – slouží k ověřování administrativních uživatelů portálu pro správu.

    - [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) – používá se k ukládání a načítání nastavení.

    - [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview#:~:text=Application%20Insights%2C%20a%20feature%20of%20Azure%20Monitor%2C%20is,professionals.%20Use%20it%20to%20monitor%20your%20live%20applications.) (volitelné) – používá se k přihlášení k rozhraní API i k portálu pro správu

3. Nasaďte rozhraní API BRIMS a portál pro správu BRIMS v prostředí Azure.

4. V dokumentaci k registraci BRIMS jsou k dispozici ukázky vlastních zásad Azure AD B2C. Postupujte podle dokumentu a nakonfigurujte svoji aplikaci a použijte platformu BRIMS pro ověření identity uživatele.  

Další informace o BRIMS pro WhoIAM najdete v [dokumentaci k produktu](https://www.whoiam.ai/brims/) .

## <a name="test-the-user-flow"></a>Testování toku uživatele

1. Otevřete Azure AD B2C tenant a v části zásady vyberte **Architektura prostředí identity**.

2. Vyberte dříve vytvořenou **SignUpSignIn**.

3. Vyberte **Spustit tok uživatele** a vyberte nastavení:

   a. **Aplikace**: vyberte registrovanou aplikaci (ukázka je JWT).

   b. **Adresa URL odpovědi**: vyberte **adresu URL pro přesměrování** .

   c. Vyberte **Spustit tok uživatele**.

4. Projděte si registrační tok a vytvořte účet.

5. Služba BRIMS bude volána během toku po vytvoření atributu uživatele. Pokud tok není úplný, ověřte, že uživatel není uložen v adresáři.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

- [Vlastní zásady v Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Začínáme s vlastními zásadami v Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
