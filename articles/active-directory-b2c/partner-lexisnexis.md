---
title: Kurz konfigurace Azure Active Directory B2C pomocí LexisNexis
titleSuffix: Azure AD B2C
description: Naučte se integrovat Azure AD B2C ověřování pomocí LexisNexis, což je profilace a služba ověřování identity, která se používá k ověření identifikace uživatelů a zajištění komplexního vyhodnocení rizik na základě zařízení uživatele.
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 5f408699cae9580188a3780fc8f8654eaa97c26b
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108429"
---
# <a name="tutorial-for-configuring-lexisnexis-with-azure-active-directory-b2c"></a>Kurz pro konfiguraci LexisNexis s využitím Azure Active Directory B2C

V tomto ukázkovém kurzu poskytujeme pokyny pro integraci Azure AD B2C s [LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd). LexisNexis poskytuje celou řadu řešení, která můžete najít [tady](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd). V tomto ukázkovém kurzu pokryjeme řešení **ThreatMetrix** z LexisNexis. ThreatMetrix je profilace a služba ověřování identity. Slouží k ověření identifikace uživatele a k zajištění komplexního vyhodnocení rizik na základě zařízení uživatele.

Tato integrace provádí profilaci na základě několika informací o uživateli, které poskytuje uživatel během procesu registrace. ThreatMetrix určuje, zda má uživatel povoleno pokračovat v přihlašování. Následující atributy jsou zváženy v ThreatMetrix analýze rizik:

- E-mail
- Telefonní číslo
- Informace o profilaci shromážděné z počítače uživatele

## <a name="prerequisites"></a>Předpoklady

Abyste mohli začít, budete potřebovat:

- Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).

- [Tenant Azure AD B2C](./tutorial-create-tenant.md) , který je propojený s vaším předplatným Azure.

## <a name="scenario-description"></a>Popis scénáře

Integrace ThreatMetrix zahrnuje tyto komponenty:

- Azure AD B2C – autorizační Server zodpovědný za ověření přihlašovacích údajů uživatele, označovaný také jako zprostředkovatel identity

- ThreatMetrix – služba ThreatMetrix přebírá vstupy poskytované uživatelem a kombinuje je s informacemi o profilech shromážděnými z počítače uživatele, aby bylo možné ověřit zabezpečení interakce uživatele.

- Vlastní rozhraní REST API – toto rozhraní API implementuje integraci mezi Azure AD B2C a službou ThreatMetrix.

V následujícím diagramu architektury se zobrazuje implementace.

![snímek obrazovky pro LexisNexis – architektura – diagram](media/partner-lexisnexis/lexisnexis-architecture-diagram.png)

|Krok | Popis |
|:--------------|:-------------|
|1. | Uživatel dorazí na přihlašovací stránku. Uživatel vybere registraci a vytvoří nový účet a do stránky zadá informace. Azure AD B2C shromažďuje atributy uživatele.
| 2. | Azure AD B2C volá rozhraní API střední vrstvy a předá ho atributům uživatele.
| 3. | Rozhraní API prostřední vrstvy shromažďuje atributy uživatelů a transformuje je do formátu, který může LexisNexis API spotřebovat. Pak ho pošle do LexisNexis.  
| 4. | LexisNexis využívá informace a zpracovává je k ověření identifikace uživatele na základě analýzy rizik. Pak vrátí výsledek do prostřední vrstvy API.
| 5. | Rozhraní API střední vrstvy zpracovává tyto informace a odesílá zpátky relevantní informace pro Azure AD B2C.
| 6. | Azure AD B2C přijímá informace zpět z rozhraní API střední vrstvy. Pokud se zobrazí reakce na selhání, zobrazí se uživateli chybová zpráva. Pokud se zobrazí odpověď úspěšnosti, je uživatel ověřený a udělený přístup.

## <a name="onboard-with-lexisnexis"></a>Zprovoznění s LexisNexis

1. Pokud chcete vytvořit účet LexisNexis, kontaktujte [LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd) .

2. Vytvořte zásady LexisNexis, které vyhovují vašim požadavkům. Použijte dokumentaci k [dispozici.](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)

>[!NOTE]
> Název zásady se použije později.

Po vytvoření účtu obdržíte informace, které potřebujete pro konfiguraci rozhraní API. Následující části popisují proces.

## <a name="configure-azure-ad-b2c-with-lexisnexis"></a>Konfigurace Azure AD B2C pomocí LexisNexis

### <a name="part-1---deploy-the-api"></a>Část 1 – nasazení rozhraní API

Nasaďte poskytnutý [kód rozhraní API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/Api) do služby Azure. Kód lze publikovat ze sady Visual Studio, a to podle těchto [pokynů](/visualstudio/deployment/quickstart-deploy-to-azure).

>[!NOTE]
>Abyste mohli Azure AD nakonfigurovat pomocí požadovaných nastavení, budete potřebovat adresu URL nasazené služby.

### <a name="part-2---configure-the-api"></a>Část 2 – konfigurace rozhraní API

Nastavení aplikace je možné [nakonfigurovat ve službě App Service v Azure](../app-service/configure-common.md#configure-app-settings).  S touto metodou se dají nastavení bezpečně nakonfigurovat bez jejich kontroly do úložiště. Pro rozhraní REST API musíte zadat následující nastavení:

| Nastavení aplikace | Zdroj | Poznámky |
| :-------- | :------------| :-----------|
|ThreatMetrix: URL | Konfigurace účtu ThreatMetrix |     |
|ThreatMetrix: OrgId | Konfigurace účtu ThreatMetrix |     |
|ThreatMetrix:ApiKey |Konfigurace účtu ThreatMetrix|  |
|ThreatMetrix: zásady | Název zásady vytvořené v ThreatMetrix | |
| BasicAuth:ApiUsername |Definování uživatelského jména pro rozhraní API| Uživatelské jméno se použije v konfiguraci Azure AD B2C.
| BasicAuth:ApiPassword | Definování hesla pro rozhraní API | Heslo bude použito v konfiguraci Azure AD B2C

### <a name="part-3---deploy-the-ui"></a>Část 3 – nasazení uživatelského rozhraní

Toto řešení používá vlastní šablony uživatelského rozhraní, které jsou načteny Azure AD B2C. Tyto šablony uživatelského rozhraní provádí profilování, které se odesílá přímo do služby ThreatMetrix.

Informace o nasazení zahrnutých [souborů uživatelského rozhraní](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template) do účtu služby Blob Storage najdete v těchto [pokynech](./customize-ui-with-html.md#custom-page-content-walkthrough) . Pokyny zahrnují nastavení účtu úložiště BLOB, konfigurace CORS a povolení veřejného přístupu.

Uživatelské rozhraní je založeno na [modré šabloně oceánu](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template/ocean_blue). Všechny odkazy v uživatelském rozhraní by se měly aktualizovat tak, aby odkazovaly na nasazené umístění. Ve složce uživatelského rozhraní vyhledejte a nahraďte https://yourblobstorage/blobcontainer nasazeným umístěním.

### <a name="part-4---create-api-policy-keys"></a>Část 4 – vytvoření klíčů zásad rozhraní API

Přečtěte si tento [dokument](./secure-rest-api.md#add-rest-api-username-and-password-policy-keys) a vytvořte dva klíče zásad – jednu pro uživatelské jméno rozhraní API a jednu pro heslo rozhraní API, které jste definovali výše.

Vzorová zásada používá tyto názvy klíčů:

- B2C_1A_RestApiUsername

- B2C_1A_RestApiPassword

### <a name="part-5---update-the-api-url"></a>Část 5 – aktualizace adresy URL rozhraní API

V zadaných [zásadách TrustFrameworkExtensions](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml)Najděte technický profil s názvem `Rest-LexisNexus-SessionQuery` a aktualizujte `ServiceUrl` položku metadat pomocí umístění rozhraní API nasazeného výše.

### <a name="part-6---update-ui-url"></a>Část 6 – aktualizace adresy URL uživatelského rozhraní

V poskytnutých [zásadách TrustFrameworkExtensions](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml)proveďte hledání a nahrazení https://yourblobstorage/blobcontainer/ pomocí umístění, do kterého jsou soubory uživatelského rozhraní nasazeny.

>[!NOTE]
> Jako osvědčený postup doporučujeme, aby zákazníci přidávají oznámení o souhlasu na stránce kolekce atributů. Upozorněte uživatele, že informace budou odeslány službám třetích stran pro ověření identity.

### <a name="part-7---configure-the-azure-ad-b2c-policy"></a>Část 7 – konfigurace zásad Azure AD B2C

V tomto [dokumentu](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) si stáhněte [úvodní Pack pro místní účty](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) a nakonfigurujte [zásady](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/policy) pro klienta Azure AD B2C.

>[!NOTE]
>Aktualizujte zadané zásady tak, aby se vztahovaly k vašemu konkrétnímu tenantovi.

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

7. Po zadání **pokračování** se zobrazí ThreatMetrix skládanky.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

- [Vlastní zásady v Azure AD B2C](./custom-policy-overview.md)

- [Začínáme s vlastními zásadami v Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
