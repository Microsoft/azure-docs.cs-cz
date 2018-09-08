---
title: Export rozhraní API hostovaných v Azure do PowerApps a Microsoft Flow | Dokumentace Microsoftu
description: Přehled o tom, jak vystavit rozhraní API hostované ve službě App Service do PowerApps a Microsoft Flow
services: app-service
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: app-service
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/15/2017
ms.author: glenga
ms.reviewer: sunayv
ms.openlocfilehash: ceb0b1ce0d04c15a5b949519caad65d2c33b40ed
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092442"
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Export rozhraní API hostovaných v Azure do PowerApps a Microsoft Flow

[PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) je služba pro sestavování a využívání vlastních obchodních aplikací, které připojují k vašim datům a fungují napříč platformami. [Microsoft Flow](https://flow.microsoft.com/guided-learning/learning-introducing-flow/) usnadňuje automatizaci pracovních postupů a obchodních procesů mezi vašimi oblíbenými aplikacemi a službami. PowerApps a Microsoft Flow součástí širokou škálu integrovaných konektorů pro zdroje dat, jako je Office 365, Dynamics 365, Salesforce a další. V některých případech se pro připojení ke zdrojům dat a rozhraní API vytvořené ve své organizaci také vhodné tvůrci aplikací a toků.

Podobně vývojáře, které chcete je zveřejnit své rozhraní API šířeji v rámci organizace můžete zpřístupnit svoje rozhraní API na tvůrce aplikací a toků. V tomto tématu se dozvíte, jak exportovat rozhraní API vytvořené pomocí [Azure Functions](../azure-functions/functions-overview.md) nebo [služby Azure App Service](../app-service/app-service-web-overview.md). Exportovaná rozhraní API se stane *vlastního konektoru*, který se používá v PowerApps a Microsoft Flow, stejně jako integrovaného konektoru.

## <a name="create-and-export-an-api-definition"></a>Vytvoření a export definice rozhraní API
Před exportem rozhraní API, musíte popsat rozhraní API pomocí definice OpenAPI (dříve označované jako [Swagger](http://swagger.io/) souboru). Tato definice obsahuje informace o tom, jaké operace jsou v rozhraní API dostupné a jakou strukturu by měla mít data požadavku a odpovědi pro toto rozhraní API. PowerApps a Microsoft Flow můžete vytvořit vlastní konektory pro všechny definice OpenAPI 2.0. Azure Functions a Azure App Service mají integrovanou podporu pro vytváření, hostování a správu definice OpenAPI. Další informace najdete v tématu [hostovat rozhraní RESTful API s CORS v Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

> [!NOTE]
> Můžete také vytvořit vlastní konektory v PowerApps a Microsoft Flow uživatelského rozhraní, bez použití definice OpenAPI. Další informace najdete v tématu [registrace a použití vlastního konektoru (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/) a [registrace a použití vlastního konektoru (Microsoft Flow)](https://flow.microsoft.com/documentation/register-custom-api/).

Pokud chcete exportovat definice rozhraní API, postupujte takto:

1. V [webu Azure portal](https://portal.azure.com), přejděte na Azure Functions nebo jiná aplikace služby App Service.

    Pokud používáte Azure Functions, vyberte vaši aplikaci function app, zvolte **funkce platformy**a potom **definice rozhraní API**.

    ![Azure definice rozhraní API funkce](media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png)

    Při použití služby Azure App Service, vybrat **definice rozhraní API** ze seznamu nastavení.

    ![Definice rozhraní API služby App Service](media/app-service-export-api-to-powerapps-and-flow/api-definition-app.png)

2. **Vyexportovat do PowerApps a Microsoft Flow** tlačítko by mělo být k dispozici (Pokud ne, je nutné nejprve vytvořit definici OpenAPI). Kliknutím na toto tlačítko k zahájení procesu exportu.

    ![Export do PowerApps a Microsoft Flow tlačítko](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. Vyberte **režim exportu**:

    **Express** umožňuje vytváření vlastního konektoru z webu Azure portal. Vyžaduje se přihlášení k PowerApps nebo Microsoft Flow a nemáte oprávnění k vytvoření konektorů v cílovém prostředí. Toto je doporučený postup, pokud tyto dva požadavky můžete splnit. Používáte-li tento režim, postupujte [používání expresní exportu](#express) níže uvedených pokynů.

    **Ruční** umožňuje exportovat definice rozhraní API, které pak importovat pomocí portály PowerApps nebo Microsoft Flow. Toto je doporučený postup, pokud uživatele Azure a uživatel s oprávněním vytvářet konektory jsou různým lidem nebo pokud konektoru je potřeba vytvořit v jiném tenantovi Azure. Pokud používáte tento režim, postupujte [použijte ruční export](#manual) níže uvedených pokynů.

    ![Režim exportu](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> Vlastní konektor používá *kopírování* definice rozhraní API, aby PowerApps a Microsoft Flow nepoznáte okamžitě pokud provedete změny aplikace a jeho definice rozhraní API. Pokud provedete změny, opakujte kroky exportu pro novou verzi.

<a name="express"></a>
## <a name="use-express-export"></a>Použít expresní exportu

K dokončení exportu **Express** režimu, postupujte podle těchto kroků:

1. Ujistěte se, že jste přihlášení k tenantovi PowerApps nebo Microsoft Flow, do které chcete exportovat. 

2. Použijte nastavení uvedená v tabulce.

    |Nastavení|Popis|
    |--------|------------|
    |**Prostředí**|Vyberte prostředí, které by měl být uložen vlastního konektoru. Další informace najdete v tématu [prostředí – přehled](https://powerapps.microsoft.com/tutorials/environments-overview/).|
    |**Název vlastního rozhraní API**|Zadejte název, který PowerApps a Microsoft Flow tvůrci uvidí ve svém seznamu konektoru.|
    |**Připravit konfiguraci zabezpečení**|V případě potřeby zadejte podrobnosti o konfiguraci zabezpečení potřebné k uživatelům udělit přístup k rozhraní API. Tento příklad ukazuje, klíč rozhraní API. Další informace najdete v tématu [určete typ ověřování](#auth) níže.|
 
    ![Express export do PowerApps a Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. Klikněte na **OK**. Vlastní konektor je teď vytvořená a přidány do prostředí, které jste zadali.

Příklady použití **Express** režimu s využitím Azure Functions najdete v článku [volání funkce z PowerApps](functions-powerapps-scenario.md) a [volání funkce z Microsoft Flow](functions-flow-scenario.md).

<a name="manual"></a>
## <a name="use-manual-export"></a>Použijte ruční export

K dokončení exportu **ruční** režimu, postupujte podle těchto kroků:

1. Klikněte na tlačítko **Stáhnout** a uložte soubor, nebo klikněte na tlačítko Kopírovat a uložit adresu URL. Soubor ke stažení nebo adresu URL použije při importu.
 
    ![Ruční export do PowerApps a Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. Pokud vaše definice rozhraní API obsahuje všechny definice security, ty jsou uvedeny v kroku #2. Při importu PowerApps a Microsoft Flow tyto zjistí a zobrazí výzvu k zadání informací o zabezpečení. Získat přihlašovací údaje související s každou definici pro použití v další části. Další informace najdete v tématu [určete typ ověřování](#auth) níže.

    ![Zabezpečení pro ruční export](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    Tento příklad ukazuje definici zabezpečení klíče rozhraní API, která byla součástí definice OpenAPI.

Teď, když jste exportovali definice rozhraní API, importujete ho k vytvoření vlastního konektoru v PowerApps a Microsoft Flow. Vlastní konektory jsou sdíleny mezi těmito dvěma službami, proto budete muset importovat definici jednou.

Chcete-li importovat definice rozhraní API do PowerApps a Microsoft Flow, postupujte takto:

1. Přejděte na adresu [powerapps.com](https://web.powerapps.com) nebo [flow.microsoft.com](https://flow.microsoft.com).

2. V pravém horním rohu klikněte na ikonu ozubeného kola a pak klikněte na **vlastní konektory**.

   ![Ikona ozubeného kola ve službě](media/app-service-export-api-to-powerapps-and-flow/icon-gear.png)

3. Klikněte na tlačítko **vytvořit vlastní konektor**, pak klikněte na tlačítko **Import definice OpenAPI**.

   ![Vytvoření vlastního konektoru](media/app-service-export-api-to-powerapps-and-flow/flow-apps-create-connector.png)

4. Zadejte název vlastního konektoru, pak přejděte k definici OpenAPI, který jste exportovali a klikněte na tlačítko **pokračovat**.

   ![Nahrát definice OpenAPI](media/app-service-export-api-to-powerapps-and-flow/flow-apps-upload-definition.png)

4. Na **Obecné** kartu, projděte si informace, které pocházejí z definice OpenAPI.

5. Na **zabezpečení** kartu, pokud se zobrazí výzva k zadání podrobností o ověřování, zadejte hodnoty, které jsou vhodné pro typ ověřování. Klikněte na tlačítko **pokračovat**.

    ![Karta zabezpečení](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    Tento příklad ukazuje povinná pole pro ověřování pomocí klíče rozhraní API. Pole se liší v závislosti na typu ověřování.

6. Na **definice** kartu, všechny operace definované v souboru OpenAPI vyplní automaticky. Pokud jsou definované všechny vaše požadované operace, můžete přejít k dalšímu kroku. Pokud ne, můžete přidat a upravit operace tady.

    ![Karta definice](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    V tomto příkladu má jednu operaci s názvem `CalculateCosts`. Metadata, třeba **popis**, všechny pocházejí ze souboru OpenAPI.

7. Klikněte na tlačítko **vytvořit konektor** v horní části stránky.

Teď můžete připojit k vlastním konektorem v PowerApps a Microsoft Flow. Další informace o vytváření konektorů v PowerApps a Microsoft Flow portálech najdete v tématu [zaregistrování vlastního konektoru (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) a [zaregistrování vlastního konektoru (Microsoft Flow)](https://flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector).

<a name="auth"></a>
## <a name="specify-authentication-type"></a>Zadání typu ověřování

PowerApps a Microsoft Flow podporují kolekci zprostředkovatelů identity, které poskytují ověřování pro vlastní konektory. Pokud vaše rozhraní API vyžaduje ověření, ujistěte se, že jsou zachyceny jako _definice security_ v dokumentu OpenAPI, jako v následujícím příkladu:

```json
"securityDefinitions": {
    "AAD": {
    "type": "oauth2",
    "flow": "accessCode",
    "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
    "scopes": {}
    }
}
``` 
Během exportu zadejte hodnoty konfigurace, které umožňují PowerApps a Microsoft Flow k ověřování uživatelů.

Tato část popisuje typy ověřování, které jsou podporovány v **Express** režimu: klíč rozhraní API, Azure Active Directory a obecné OAuth 2.0. PowerApps a Microsoft Flow také podporují základní ověřování nebo OAuth 2.0 pro konkrétní služby jako Dropbox, Facebook nebo SalesForce.

### <a name="api-key"></a>Klíč rozhraní API
Pokud použijete klíč rozhraní API, uživatelé vašeho konektoru se výzva k zadání klíče při vytváření připojení. Zadáte klíče název rozhraní API, aby to pomohl ostatním pochopit, které je zapotřebí. V předchozím příkladu vytvoříme s využitím názvu `API Key (contact meganb@contoso.com)` aby uživatelé věděli, kde lze získat informace o klíči rozhraní API. Pro službu Azure Functions klíčem je obvykle jeden z klíčů hostitele, pokrývající několik funkcí v rámci aplikace function app.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
Při použití služby Azure AD, budete potřebovat dva registrace aplikace Azure AD: jeden pro samotné rozhraní API a jeden pro vlastní konektor:

- Ke konfiguraci registrace pro rozhraní API, použijte [ověřování/autorizace služby App Service](../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md) funkce.

- Ke konfiguraci registrace pro konektor, postupujte podle kroků v [přidáním aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application). Registrace musí mít Delegovaný přístup k rozhraní API a adresu URL odpovědi `https://msmanaged-na.consent.azure-apim.net/redirect`. 

Další informace najdete v tématu příkladů registrace Azure AD pro [PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) a [Microsoft Flow](https://flow.microsoft.com/documentation/customapi-azure-resource-manager-tutorial/). Tyto příklady používají jako rozhraní API Azure Resource Manageru Pokud budete postupovat podle kroků, nahraďte vaše rozhraní API.

Následující konfigurační hodnoty jsou povinné:
- **ID klienta** – ID klienta vašeho konektoru registrace služby Azure AD
- **Tajný kód klienta** -tajný kód klienta vašeho konektoru registrace služby Azure AD
- **Adresa URL pro přihlášení** – základní adresu URL pro službu Azure AD. V Azure, to je obvykle `https://login.windows.net`.
- **ID tenanta** – ID tenanta se použije pro přihlášení. To by měl být "common" nebo ID tenanta, ve kterém se vytvoří konektor.
- **Adresa URL prostředku** – adresa URL prostředku registrace Azure AD pro vaše rozhraní API

> [!IMPORTANT]
> Pokud někdo jiný bude import definice rozhraní API do PowerApps a Microsoft Flow jako součást ruční toku, je potřeba je zadat pomocí ID klienta a tajný kód klienta *registrace konektoru*, a také adresu URL prostředku rozhraní API. Ujistěte se, že se bezpečně spravují těchto tajných kódů. **Nesdílejte zabezpečovací přihlašovací údaje o samotné rozhraní API.**

### <a name="generic-oauth-20"></a>Obecné OAuth 2.0
Jestli používáte obecný OAuth 2.0, můžete integrovat s všech poskytovatelů OAuth 2.0. To umožňuje pracovat s vlastní poskytovatele, které nativně nepodporují.

Následující konfigurační hodnoty jsou povinné:
- **ID klienta** – ID klienta OAuth 2.0
- **Tajný kód klienta** -tajný klíč klienta OAuth 2.0
- **Autorizace adresy URL** – adresa URL autorizace OAuth 2.0
- **Token URL** – adresa URL tokenu OAuth 2.0
- **Aktualizovat adresu URL** -adresu URL pro obnovení OAuth 2.0


