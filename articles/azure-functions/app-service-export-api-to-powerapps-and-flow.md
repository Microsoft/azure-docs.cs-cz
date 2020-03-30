---
title: Export rozhraní API hostovaného v Azure do PowerApps a Microsoft Flow
description: Přehled, jak zpřístupnit rozhraní API hostované ve službě App Service powerapps a Microsoft Flow
ms.topic: conceptual
ms.date: 12/15/2017
ms.reviewer: sunayv
ms.openlocfilehash: 632818bf82e41e6be0a96d30cc1c4fa631718a3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74233078"
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Export rozhraní API hostovaného v Azure do PowerApps a Microsoft Flow

[PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) je služba pro vytváření a používání vlastních obchodních aplikací, které se připojují k vašim datům a fungují napříč platformami. [Microsoft Flow](/learn/modules/get-started-with-flow/index) usnadňuje automatizaci pracovních postupů a obchodních procesů mezi oblíbenými aplikacemi a službami. PowerApps i Microsoft Flow jsou dodávány s různými integrovanými konektory ke zdrojům dat, jako jsou Office 365, Dynamics 365, Salesforce a další. V některých případech se tvůrci aplikací a toků také chtějí připojit ke zdrojům dat a prostředím API vytvořeným jejich organizací.

Podobně vývojáři, kteří chtějí vystavit jejich rozhraní API šířeji v rámci organizace můžete zpřístupnit jejich rozhraní API pro tvůrce aplikací a toku. Toto téma ukazuje, jak exportovat rozhraní API vytvořené pomocí [Azure Functions](../azure-functions/functions-overview.md) nebo [Azure App Service](../app-service/overview.md). Exportované rozhraní API se stane *vlastním konektorem*, který se používá v PowerApps a Microsoft Flow stejně jako integrovaný konektor.

> [!IMPORTANT]
> Funkce definice rozhraní API zobrazená v tomto článku je podporovaná jenom pro [verzi 1.x aplikací Azure Functions runtime](functions-versions.md#creating-1x-apps) a App Services. Verze 2.x funkcí se integruje se správou rozhraní API pro vytváření a údržbu definic OpenAPI. Další informace najdete [v tématu Vytvoření definice OpenAPI pro funkci se správou rozhraní Azure API](functions-openapi-definition.md). 

## <a name="create-and-export-an-api-definition"></a>Vytvoření a export definice rozhraní API
Před exportem rozhraní API musíte popisovat rozhraní API pomocí definice OpenAPI (dříve známé jako soubor [Swagger).](https://swagger.io/) Tato definice obsahuje informace o tom, jaké operace jsou v rozhraní API dostupné a jakou strukturu by měla mít data požadavku a odpovědi pro toto rozhraní API. PowerApps a Microsoft Flow můžou vytvářet vlastní konektory pro libovolnou definici OpenAPI 2.0. Azure Functions a Azure App Service mají integrovanou podporu pro vytváření, hostování a správu definic OpenAPI. Další informace najdete [v tématu Hostování rozhraní API RESTful s CORS ve službě Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

> [!NOTE]
> Můžete také vytvářet vlastní konektory v PowerApps a Microsoft Flow UI, bez použití definice OpenAPI. Další informace najdete v [tématech Registrace a používání vlastního konektoru (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/) a [Registrace a použití vlastního konektoru (Microsoft Flow).](/power-automate/developer/register-custom-api)

Chcete-li exportovat definici rozhraní API, postupujte takto:

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na azure funkce nebo jinou aplikaci služby App Service.

    Pokud používáte Azure Functions, vyberte aplikaci funkcí, zvolte **funkce platformy**a pak **definici rozhraní API**.

    ![Definice rozhraní API Azure Functions](media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png)

    Pokud používáte Azure App Service, vyberte **definici rozhraní API** ze seznamu nastavení.

    ![Definice rozhraní API služby Aplikace](media/app-service-export-api-to-powerapps-and-flow/api-definition-app.png)

2. Tlačítko **Exportovat do PowerApps + Microsoft Flow** by mělo být k dispozici (pokud ne, musíte nejprve vytvořit definici OpenAPI). Klepnutím na toto tlačítko zahájíte proces exportu.

    ![Tlačítko Exportovat do PowerApps + Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. Vyberte **režim exportu**:

    **Express** umožňuje vytvořit vlastní konektor z portálu Azure. Vyžaduje, abyste byli přihlášeni k PowerApps nebo Microsoft Flow a měli oprávnění k vytváření konektorů v cílovém prostředí. Toto je doporučený přístup, pokud lze tyto dva požadavky splnit. Pokud používáte tento režim, postupujte podle níže uvedených pokynů [pro expresní export.](#express)

    **Příručka** umožňuje exportovat definici rozhraní API, kterou pak importujete pomocí portálů PowerApps nebo Microsoft Flow. Toto je doporučený přístup, pokud uživatel Azure a uživatel s oprávněním k vytváření konektorů jsou různí lidé nebo pokud konektor musí být vytvořen v jiném tenantovi Azure. Pokud používáte tento režim, postupujte podle níže uvedených pokynů [pro ruční export.](#manual)

    ![Režim exportu](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> Vlastní konektor používá *kopii* definice rozhraní API, takže PowerApps a Microsoft Flow nebudou okamžitě vědět, pokud provedete změny v aplikaci a její definici rozhraní API. Pokud provedete změny, opakujte kroky exportu pro novou verzi.

<a name="express"></a>
## <a name="use-express-export"></a>Použít expresní export

Chcete-li export dokončit v **expresním** režimu, postupujte takto:

1. Ujistěte se, že jste přihlášení k tenantovi PowerApps nebo Microsoft Flow, do kterého chcete exportovat. 

2. Použijte nastavení uvedená v tabulce.

    |Nastavení|Popis|
    |--------|------------|
    |**Prostředí**|Vyberte prostředí, do kterého má být vlastní spojnice uložena. Další informace najdete v tématu [Prostředí – přehled](https://powerapps.microsoft.com/tutorials/environments-overview/).|
    |**Vlastní název rozhraní API**|Zadejte název, který powerapps a microsoft flow stavitelé uvidí v jejich seznamu konektorů.|
    |**Příprava konfigurace zabezpečení**|V případě potřeby zadejte podrobnosti konfigurace zabezpečení potřebné k udělení přístupu uživatelům k rozhraní API. Tento příklad ukazuje klíč rozhraní API. Další informace naleznete [v tématu Určení typu ověřování](#auth) níže.|
 
    ![Expresní export do PowerApps a Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. Klikněte na tlačítko **OK**. Vlastní spojnice je nyní vytvořena a přidána do prostředí, které jste zadali.

<a name="manual"></a>
## <a name="use-manual-export"></a>Použití ručního exportu

Chcete-li export dokončit v **ručním** režimu, postupujte takto:

1. Klikněte na **Stáhnout** a uložit soubor nebo klikněte na tlačítko kopírovat a uložte adresu URL. Během importu použijete soubor pro stahování nebo adresu URL.
 
    ![Ruční export do PowerApps a Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. Pokud definice rozhraní API obsahuje všechny definice zabezpečení, jsou volány v kroku #2. Během importu powerapps a Microsoft Flow zjistí tyto a vyzve k zadání informací o zabezpečení. Shromážděte pověření související s každou definicí pro použití v další části. Další informace naleznete [v tématu Určení typu ověřování](#auth) níže.

    ![Zabezpečení pro ruční export](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    Tento příklad ukazuje definici zabezpečení klíče rozhraní API, která byla zahrnuta v definici OpenAPI.

Teď, když jste exportovali definici rozhraní API, importujete ji a vytvoříte vlastní konektor v PowerApps a Microsoft Flow. Vlastní konektory jsou sdíleny mezi těmito dvěma službami, takže je potřeba importovat definici pouze jednou.

Pokud chcete importovat definici rozhraní API do PowerApps a Microsoft Flow, postupujte takto:

1. Přejděte na adresu [powerapps.com](https://web.powerapps.com) nebo [flow.microsoft.com](https://flow.microsoft.com).

2. V pravém horním rohu klikněte na ikonu ozubeného kola a potom klikněte na **Vlastní konektory**.

   ![Ikona ozubeného kola ve službě](media/app-service-export-api-to-powerapps-and-flow/icon-gear.png)

3. Klepněte na **tlačítko Vytvořit vlastní konektor**a potom klepněte na **položku Importovat definici rozhraní OpenAPI**.

   ![Vytvoření vlastního konektoru](media/app-service-export-api-to-powerapps-and-flow/flow-apps-create-connector.png)

4. Zadejte název vlastního konektoru, přejděte na exportovnou definici OpenAPI a klepněte na tlačítko **Pokračovat**.

   ![Nahrát definici OpenAPI](media/app-service-export-api-to-powerapps-and-flow/flow-apps-upload-definition.png)

4. Na kartě **Obecné** zkontrolujte informace, které pocházejí z definice OpenAPI.

5. Pokud budete na kartě **Zabezpečení** vyzváni k zadání podrobností o ověření, zadejte hodnoty odpovídající typu ověřování. Klikněte na **Pokračovat**.

    ![Karta Zabezpečení](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    Tento příklad ukazuje požadovaná pole pro ověřování pomocí klíče rozhraní API. Pole se liší v závislosti na typu ověřování.

6. Na kartě Definice jsou automaticky **vyplněny** všechny operace definované v souboru OpenAPI. Pokud jsou definovány všechny požadované operace, můžete přejít k dalšímu kroku. Pokud ne, můžete zde přidat a upravit operace.

    ![Karta Definice](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    Tento příklad má jednu operaci s názvem `CalculateCosts`. Metadata, jako **je Popis**, vše pochází ze souboru OpenAPI.

7. V horní části stránky klikněte na **Vytvořit spojnici.**

Teď se můžete připojit k vlastnímu konektoru v PowerApps a Microsoft Flow. Další informace o vytváření konektorů na portálech PowerApps a Microsoft Flow najdete v [tématu Registrace vlastního konektoru (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) a [Registrace vlastního konektoru (Microsoft Flow).](/power-automate/get-started-flow-dev#create-a-custom-connector)

<a name="auth"></a>
## <a name="specify-authentication-type"></a>Zadání typu ověřování

PowerApps a Microsoft Flow podporují kolekci poskytovatelů identity, kteří poskytují ověřování pro vlastní konektory. Pokud vaše rozhraní API vyžaduje ověření, ujistěte se, že je zachyceno jako _definice zabezpečení_ v dokumentu OpenAPI, jako v následujícím příkladu:

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
Během exportu zadáte konfigurační hodnoty, které umožňují PowerApps a Microsoft Flow ověřovat uživatele.

Tato část popisuje typy ověřování, které jsou podporovány v **expresním** režimu: klíč rozhraní API, azure active directory a obecný OAuth 2.0. PowerApps a Microsoft Flow také podporují základní ověřování a OAuth 2.0 pro konkrétní služby, jako je Dropbox, Facebook a SalesForce.

### <a name="api-key"></a>Klíč rozhraní API
Při použití klíče rozhraní API jsou uživatelé konektoru vyzváni k poskytnutí klíče při vytváření připojení. Zadáte název klíče rozhraní API, který jim pomůže pochopit, který klíč je potřeba. V předchozím příkladu používáme `API Key (contact meganb@contoso.com)` název, aby lidé věděli, kde získat informace o klíči rozhraní API. Pro funkce Azure je klíč obvykle jedním z klíčů hostitele, pokrývající několik funkcí v rámci aplikace funkce.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
Při použití Azure AD, budete potřebovat dvě registrace aplikací Azure AD: jeden pro samotné rozhraní API a jeden pro vlastní konektor:

- Chcete-li nakonfigurovat registraci rozhraní API, použijte funkci [Ověřování/autorizace služby App Service.](../app-service/configure-authentication-provider-aad.md)

- Chcete-li nakonfigurovat registraci konektoru, postupujte podle pokynů v [části Přidání aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Registrace musí mít delegovaný přístup k rozhraní `https://msmanaged-na.consent.azure-apim.net/redirect`API a adresu URL odpovědi aplikace . 

Další informace najdete v příkladech registrace Azure AD pro [PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) a [Microsoft Flow](https://docs.microsoft.com/connectors/custom-connectors/azure-active-directory-authentication). Tyto příklady používají Azure Resource Manager jako rozhraní API; nahraďte rozhraní API, pokud budete postupovat podle pokynů.

Jsou vyžadovány následující hodnoty konfigurace:
- **ID klienta** – ID klienta registrace azure ad
- **Tajný klíč klienta** – tajný klíč klienta registrace azure ad
- **Přihlašovací adresa URL** – základní adresa URL pro Azure AD. V Azure je to `https://login.windows.net`obvykle .
- **ID klienta** – ID klienta, který se má použít pro přihlášení. To by mělo být "společné" nebo ID klienta, ve kterém je vytvořen konektor.
- **Adresa URL prostředku** – adresa URL prostředku registrace azure ad pro vaše rozhraní API

> [!IMPORTANT]
> Pokud někdo jiný importuje definici rozhraní API do PowerApps a Microsoft Flow jako součást ručního toku, musíte mu poskytnout ID klienta a tajný klíč klienta *registrace konektoru*a adresu URL prostředku vašeho rozhraní API. Ujistěte se, že tyto tajné klíče jsou spravovány bezpečně. **Nesdílejte pověření zabezpečení samotného rozhraní API.**

### <a name="generic-oauth-20"></a>Obecné OAuth 2.0
Při použití obecného OAuth 2.0, můžete integrovat s libovolným poskytovatelem OAuth 2.0. To umožňuje pracovat s vlastními zprostředkovateli, kteří nejsou nativně podporováni.

Jsou vyžadovány následující hodnoty konfigurace:
- **ID klienta** - ID klienta OAuth 2.0
- **Tajný klíč klienta** - tajný klíč klienta OAuth 2.0
- **Autorizační adresa URL** - autorizační adresa OAuth 2.0
- **Adresa URL tokenu** – adresa URL tokenu OAuth 2.0
- **Obnovit URL** - OAuth 2.0 obnovit URL


