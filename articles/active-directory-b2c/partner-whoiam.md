---
title: Kurz konfigurace Azure Active Directory B2C pomocí WhoIAM
titleSuffix: Azure AD B2C
description: V tomto kurzu se dozvíte, jak integrovat Azure AD B2C ověřování pomocí WhoIAM pro ověřování uživatelů.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 718ccbaa57ffe9f4ebaf4e8df448b602ba8cc3fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89293145"
---
# <a name="tutorial-for-configuring-whoiam-with-azure-active-directory-b2c"></a>Kurz pro konfiguraci WhoIAM s využitím Azure Active Directory B2C

V tomto ukázkovém kurzu poskytujeme pokyny k tomu, jak ve svém prostředí nakonfigurovat [WhoIAM](https://www.whoiam.ai/brims/) (BRIMSed identity System) a jak ho integrovat s Active Directory B2C (Azure AD B2C).

BRIMS je sada aplikací a služeb, které jsou nasazené ve vašem prostředí. Poskytuje hlasová, SMS a ověřování e-mailů vaší uživatelské základny. BRIMS funguje ve spojení s vaším stávajícím řešením pro správu identit a přístupu a je nezávislá platformy.

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, budete potřebovat:

- Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).

- [Klienta Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) , který je propojený s vaším předplatným Azure.

- [Zkušební účet](https://www.whoiam.ai/contact-us/)WhoIAM.

## <a name="scenario-description"></a>Popis scénáře

Integrace WhoIAM zahrnuje tyto komponenty:

- Tenant Azure AD B2C. Je to autorizační Server, který ověřuje přihlašovací údaje uživatele na základě vlastních zásad, které jsou v něm definované. Označuje se také jako zprostředkovatel identity.

- Portál pro správu pro správu klientů a jejich konfigurací.

- Služba API, která zpřístupňuje různé funkce prostřednictvím koncových bodů.  

- Azure Cosmos DB, která funguje jako back-end pro portál pro správu BRIMS i službu API.

V následujícím diagramu architektury se zobrazuje implementace.

![Diagram architektury Azure AD B2C integrace s WhoIAM](media/partner-whoiam/whoiam-architecture-diagram.png)

|Krok | Description |
|:-----| :-----------|
| 1. | Uživatel dorazí na stránku a spustí žádost o registraci nebo přihlášení do aplikace, která jako poskytovatele identity používá Azure AD B2C.
| 2. | V rámci ověřování si uživatel vyžádá buď, aby ověřil vlastnictví e-mailu nebo telefonu, nebo použil svůj hlas jako faktor biometrického ověřování.  
| 3. | Azure AD B2C provede volání služby API BRIMS a předá e-mailovou adresu uživatele, telefonní číslo a záznam hlasu.
| 4. | BRIMS používá předdefinované konfigurace, jako jsou plně přizpůsobitelné e-maily a šablony SMS pro interakci s uživatelem v příslušném jazyce způsobem, který je konzistentní se stylem aplikace.
| 5. | Po dokončení ověření identity uživatele BRIMS vrátí token, který Azure AD B2C k indikaci výsledku ověření. Azure AD B2C pak buď udělí uživateli přístup k aplikaci, nebo se nezdaří pokus o ověření.  

## <a name="sign-up-with-whoiam"></a>Zaregistrujte se pomocí WhoIAM

1. Kontaktujte [WhoIAM](https://www.whoiam.ai/contact-us/) a vytvořte účet BRIMS.

2. Použijte pokyny k registraci, které máte k dispozici, a nakonfigurujte následující služby Azure:

    - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): používá se k zabezpečenému ukládání hesel, jako je například heslo poštovní služby.

    - [Azure App Service](https://azure.microsoft.com/services/app-service/): používá se pro hostování služeb API BRIMS a portálu pro správu.

    - [Azure Active Directory](https://azure.microsoft.com/services/active-directory/): slouží k ověřování administrativních uživatelů portálu pro správu.

    - [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/): používá se k ukládání a načítání nastavení.

    - [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview#:~:text=Application%20Insights%2C%20a%20feature%20of%20Azure%20Monitor%2C%20is,professionals.%20Use%20it%20to%20monitor%20your%20live%20applications) (volitelné): slouží k přihlášení k rozhraní API i k portálu pro správu.

3. Nasaďte rozhraní API BRIMS a portál pro správu BRIMS v prostředí Azure.

4. Ukázky vlastních zásad Azure AD B2C jsou k dispozici v dokumentaci k registraci BRIMS. Postupujte podle dokumentace a nakonfigurujte svou aplikaci a použijte platformu BRIMS pro ověření identity uživatele.  

Další informace o BRIMS pro WhoIAM najdete v [dokumentaci k produktu](https://www.whoiam.ai/brims/).

## <a name="test-the-user-flow"></a>Testování toku uživatele

1. Otevřete klienta Azure AD B2C. V části **zásady**vyberte **Architektura prostředí identity**.

2. Vyberte dříve vytvořenou **SignUpSignIn**.

3. Vyberte **Spustit tok uživatele** a potom:

   a. V poli **aplikace**vyberte registrovanou aplikaci (ukázka je JWT).

   b. V poli **Adresa URL odpovědi**vyberte **adresu URL pro přesměrování**.

   c. Vyberte **Spustit tok uživatele**.

4. Projděte si tok registrace a vytvořte účet.

5. Po vytvoření atributu uživatele bude služba BRIMS volána během toku. Pokud tok není úplný, ověřte, že uživatel není uložen v adresáři.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

- [Vlastní zásady v Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Začínáme s vlastními zásadami v Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
