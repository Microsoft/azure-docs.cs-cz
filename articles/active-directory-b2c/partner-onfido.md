---
title: Kurz konfigurace Azure Active Directory B2C pomocí Onfido
titleSuffix: Azure AD B2C
description: Naučte se integrovat Azure AD B2C ověřování pomocí Onfido pro ID dokumentu a biometrika ověřování obličeje.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/03/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 46c6eac80ddbff73d99e05c070e66aa1700da174
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96928626"
---
# <a name="tutorial-for-configuring-onfido-with-azure-active-directory-b2c"></a>Kurz pro konfiguraci Onfido s využitím Azure Active Directory B2C

V tomto ukázkovém kurzu poskytujeme pokyny pro integraci Azure AD B2C s [Onfido](https://onfido.com/). Onfido je ID dokumentu a ověřovací aplikace biometrika obličeje. Umožňuje společnostem uspokojit požadavky na *zákazníky* a identitu v reálném čase. Onfido používá propracované ověřování identity založené na AI, které nejdřív ověří ID fotografie a pak odpovídá na své biometrika obličeje. Toto řešení přiřadí digitální identitu ke svému reálnému člověku a poskytuje bezpečné prostředí pro zprovoznění při omezování podvodů.

V této ukázce připojíme k ověření identity službu Onfido v procesu registrace nebo přihlašování. Informovaná rozhodnutí o tom, ke kterému produktu a službě může uživatel přistupovat, se provádí na základě výsledků Onfido.

## <a name="prerequisites"></a>Předpoklady

Abyste mohli začít, budete potřebovat:

- Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).

- [Tenant Azure AD B2C](./tutorial-create-tenant.md) , který je propojený s vaším předplatným Azure.

- [Zkušební účet](https://onfido.com/signup/)Onfido.

## <a name="scenario-description"></a>Popis scénáře

Integrace Onfido zahrnuje tyto komponenty:

- Azure AD B2C tenant – autorizační Server, který zodpovídá za ověření přihlašovacích údajů uživatele na základě vlastních zásad definovaných v tenantovi. Označuje se také jako zprostředkovatel identity. Je hostitelem klientské aplikace Onfido, která shromažďuje dokumenty uživatelů a odesílá je do služby Onfido API.

- Onfido Client – konfigurovatelný nástroj pro shromažďování dokumentů klienta JavaScript nasazený v rámci jiných webových stránek. Shromáždí dokumenty a provede předběžné kontroly, jako je velikost dokumentu a kvalita.

- Mezilehlé rozhraní REST API – poskytuje koncovým bodům pro klienta Azure AD B2C komunikovat se službou Onfido API, zpracovává zpracování dat a dodržuje požadavky na zabezpečení obou.

- Onfido API Service – back-end služba poskytovaná nástrojem Onfido, která ukládá a ověřuje dokumenty poskytnuté uživatelem.

V následujícím diagramu architektury se zobrazuje implementace.

![snímek obrazovky pro onfido – architektura – diagram](media/partner-onfido/onfido-architecture-diagram.png)

|Krok | Description |
|:-----| :-----------|
| 1. | Uživatel dorazí na přihlašovací stránku. Uživatel se přihlásí k vytvoření nového účtu a zadá informace na stránku. Azure AD B2C shromažďuje atributy uživatele. Klientská aplikace Onfido hostovaná v Azure AD B2C provede předběžné kontroly informací o uživateli.
| 2. | Azure AD B2C volá rozhraní API střední vrstvy a předá ho atributům uživatele.
| 3. | Rozhraní API prostřední vrstvy shromažďuje atributy uživatelů a transformuje je do formátu, který může Onfido API spotřebovat. Pak ho pošle do Onfido.
| 4. | Onfido využívá informace a zpracovává je k ověření identifikace uživatele. Pak vrátí výsledek do prostřední vrstvy API.
| 5. | Rozhraní API střední vrstvy zpracovává informace a pošle zpátky relevantní informace ve správném formátu JSON pro Azure AD B2C.
| 6. | Azure AD B2C přijímá informace zpět z rozhraní API střední vrstvy. Pokud se zobrazí reakce na selhání, zobrazí se uživateli chybová zpráva. Pokud se zobrazí odpověď úspěšnosti, je uživatel ověřen a zapsán do adresáře.

## <a name="onboard-with-onfido"></a>Zprovoznění s Onfido

1. Pokud chcete vytvořit účet Onfido, kontaktujte [Onfido](https://onfido.com/signup/).

2. Po vytvoření účtu vytvořte [klíč rozhraní API](https://documentation.onfido.com/). Živé klíče jsou Fakturovatelné, ale k testování řešení můžete použít [klíče izolovaného prostoru (sandbox)](https://documentation.onfido.com/?javascript#sandbox-and-live-differences) . Klíče izolovaného prostoru vytvoří stejnou strukturu výsledků jako živé klíče, ale výsledky jsou vždy předem určené. Dokumenty nejsou zpracovávány nebo uloženy.

>[!NOTE]
> Klíč budete potřebovat později.

Další informace o Onfido najdete v tématu [dokumentace k rozhraní Onfido API](https://documentation.onfido.com) a [Centrum pro vývojáře Onfido](https://developers.onfido.com).  

## <a name="configure-azure-ad-b2c-with-onfido"></a>Konfigurace Azure AD B2C pomocí Onfido

### <a name="part-1---deploy-the-api"></a>Část 1 – nasazení rozhraní API

- Nasaďte poskytnutý [kód rozhraní API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/API/Onfido.Api) do služby Azure. Kód lze publikovat ze sady Visual Studio, a to podle těchto [pokynů](/visualstudio/deployment/quickstart-deploy-to-azure).
- Nastavte CORS, přidejte **povolený původ** jako https://{your_tenant_name}. b2clogin. com.

>[!NOTE]
>Abyste mohli Azure AD nakonfigurovat pomocí požadovaných nastavení, budete potřebovat adresu URL nasazené služby.

#### <a name="adding-sensitive-configuration-settings"></a>Přidávání nastavení citlivých konfigurací

Nastavení aplikace je možné nakonfigurovat ve [službě App Service v Azure](../app-service/configure-common.md#configure-app-settings). App Service umožňuje nakonfigurovat nastavení tak, aby se správně nakonfigurovala bez jejich kontroly do úložiště. Rozhraní REST API potřebuje následující nastavení:

| Název nastavení aplikace | Zdroj | Poznámky |
|:-------------------------|:-------|:-------|
|OnfidoSettings: AuthToken| Účet Onfido |

### <a name="part-2---deploy-the-ui"></a>Část 2 – nasazení uživatelského rozhraní

#### <a name="configure-your-storage-location"></a>Konfigurace umístění úložiště

1. Nastavení [kontejneru úložiště objektů BLOB v účtu úložiště](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)

2. Soubory uživatelského rozhraní uložte ze [složky uživatelského rozhraní](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI) do kontejneru objektů BLOB.

3. Pomocí těchto pokynů povolte přístup CORS k kontejneru úložiště, který jste vytvořili:

   a. Přejděte na **Nastavení**  > **povolený počátek** a zadejte `https://{your_tenant_name}.b2clogin.com` . Nahraďte název-tenanta názvem vašeho tenanta Azure AD B2C. Například https://fabrikam.b2clogin.com . Při zadávání názvu tenanta použijte všechna malá písmena.

   b. U **povolených metod** vyberte `GET` a `PUT` .

   c. Vyberte **Uložit**.

#### <a name="update-ui-files"></a>Aktualizovat soubory uživatelského rozhraní

1. V souborech uživatelského rozhraní přejdete do složky [ **ocean_blue**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI/ocean_blue)

2. Otevřete každý soubor HTML.

3. Vyhledejte a nahraďte {Your-UI-BLOB-Container-URL} adresou URL místa, kde se nachází vaše uživatelské rozhraní **ocean_blue**, **DIST** a **assety** složky.

4. Vyhledejte a nahraďte {The-Intermediate-API-URL} adresou URL služby zprostředkující API App Service.

#### <a name="upload-your-files"></a>Nahrání souborů

1. Soubory uživatelského rozhraní uložte ze složky uživatelského rozhraní do kontejneru objektů BLOB.

2. Ke správě souborů a přístupových oprávnění použijte [Průzkumník služby Azure Storage](../virtual-machines/disks-use-storage-explorer-managed-disks.md) .

### <a name="part-3---configure-azure-ad-b2c"></a>Část 3 – konfigurace Azure AD B2C

#### <a name="replace-the-configuration-values"></a>Nahraďte konfigurační hodnoty.

V části poskytnuté [vlastní zásady](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/Policies)vyhledejte následující zástupné symboly a nahraďte je odpovídajícími hodnotami z vaší instance.

| Zástupný symbol | Nahradit hodnotou | Příklad  |
|:---------------|:----------------|:-------------------|
| {your_tenant_name}  | Krátký název tenanta |  "yourtenant" z yourtenant.onmicrosoft.com |
| {your_tenantID} | TenantID vašeho tenanta Azure AD B2C | 01234567-89AB-CDEF-0123-456789ABCDEF           |
| {your_tenant_IdentityExperienceFramework_appid}        | ID aplikace IdentityExperienceFramework nakonfigurované ve vašem tenantovi Azure AD B2C      | 01234567-89AB-CDEF-0123-456789ABCDEF         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | ID aplikace ProxyIdentityExperienceFramework nakonfigurované ve vašem tenantovi Azure AD B2C | 01234567-89AB-CDEF-0123-456789ABCDEF         |
| {your_tenant_extensions_appid}                         | ID aplikace pro aplikaci úložiště vašeho tenanta                                      | 01234567-89AB-CDEF-0123-456789ABCDEF         |
| {your_tenant_extensions_app_objectid}                  | ID objektu aplikace úložiště vašeho tenanta                                   | 01234567-89AB-CDEF-0123-456789ABCDEF         |
| {your_app_insights_instrumentation_key} | Klíč instrumentace instance App Insights *| 01234567-89AB-CDEF-0123-456789ABCDEF|
|{your_ui_file_base_url}| Adresa URL místa, kde se nachází vaše uživatelské rozhraní **ocean_blue**, **DIST** a složky **assetů** | https://yourstorage.blob.core.windows.net/UI/|
| {your_app_service_URL}                                 | Adresa URL služby App Service, kterou jste nastavili                                             | `https://yourapp.azurewebsites.net`          |

* App Insights může být v jiném tenantovi. Tento krok je volitelný. Odeberte odpovídající TechnicalProfiles a OrchestrationSteps, pokud není potřeba.

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>Část 4 – Konfigurace zásad Azure AD B2C

Pokyny, jak nastavit tenanta Azure AD B2C a nakonfigurovat zásady, najdete v tomto [dokumentu](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) .

>[!NOTE]
> Jako osvědčený postup doporučujeme, aby zákazníci přidávají oznámení o souhlasu na stránce kolekce atributů. Upozorněte uživatele, že informace budou odeslány službám třetích stran pro ověření identity.

## <a name="test-the-user-flow"></a>Testování toku uživatele

1. Otevřete Azure AD B2C tenant a v části zásady vyberte **Architektura prostředí identity**.

2. Vyberte dříve vytvořenou **SignUpSignIn**.

3. Vyberte **Spustit tok uživatele** a vyberte nastavení:

   a. **Aplikace**: vyberte registrovanou aplikaci (ukázka je JWT).

   b. **Adresa URL odpovědi**: vyberte **adresu URL pro přesměrování** .

   c. Vyberte **Spustit tok uživatele**.

4. Projděte si registrační tok a vytvořte účet.

5. Služba Onfido bude volána během toku po vytvoření atributu uživatele. Pokud tok není úplný, ověřte, že uživatel není uložen v adresáři.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

- [Vlastní zásady v Azure AD B2C](./custom-policy-overview.md)

- [Začínáme s vlastními zásadami v Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
