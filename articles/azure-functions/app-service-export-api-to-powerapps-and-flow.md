---
title: Exportování rozhraní API hostovaného v Azure do PowerApps a Microsoft Flow
description: Přehled postupu vystavení rozhraní API hostovaného v App Service PowerApps a Microsoft Flow
ms.topic: conceptual
ms.date: 04/28/2020
ms.reviewer: sunayv
ms.openlocfilehash: 7d968c62afbfc92952f747e1e7627c98fe07436d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87015081"
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Exportování rozhraní API hostovaného v Azure do PowerApps a Microsoft Flow

[PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) je služba pro sestavování a používání vlastních obchodních aplikací, které se připojují k vašim datům a pracují na různých platformách. [Microsoft Flow](/learn/modules/get-started-with-flow/index) usnadňuje automatizaci pracovních postupů a obchodních procesů mezi vašimi oblíbenými aplikacemi a službami. PowerApps i Microsoft Flow přináší řadu integrovaných konektorů do zdrojů dat, jako jsou například Office 365, Dynamics 365, Salesforce a další. V některých případech se tvůrci aplikací a toků chtějí připojit ke zdrojům dat a rozhraním API vytvořeným jejich organizací.

Podobně vývojáři, kteří chtějí své rozhraní API lépe vystavit v rámci organizace, mohou zpřístupnit svá rozhraní API pro sestavování aplikací a toků. V tomto článku se dozvíte, jak exportovat rozhraní API vytvořené pomocí [Azure Functions](../azure-functions/functions-overview.md) nebo [Azure App Service](../app-service/overview.md). Exportované rozhraní API se stal *vlastním konektorem*, který se používá v PowerApps a Microsoft Flow stejně jako vestavěný konektor.

> [!IMPORTANT]
> Funkce definice rozhraní API uvedená v tomto článku je podporovaná jenom pro [verzi 1. x Azure Functions runtime](functions-versions.md#creating-1x-apps) a App Servicesch aplikací. Verze 2. x funkcí se integruje s API Management k vytváření a údržbě definic OpenAPI. Další informace najdete v tématu [Vytvoření definice openapi pro funkci pomocí Azure API Management](functions-openapi-definition.md). 

## <a name="create-and-export-an-api-definition"></a>Vytvoření a export definice rozhraní API
Před exportem rozhraní API musíte popsat rozhraní API pomocí definice OpenAPI (dříve označované jako soubor [Swagger](https://swagger.io/) ). Tato definice obsahuje informace o tom, jaké operace jsou v rozhraní API dostupné a jakou strukturu by měla mít data požadavku a odpovědi pro toto rozhraní API. PowerApps a Microsoft Flow můžou vytvářet vlastní konektory pro všechny definice OpenAPI 2,0. Azure Functions a Azure App Service mají integrovanou podporu pro vytváření, hostování a správu definic OpenAPI. Další informace najdete v tématu [hostování rozhraní RESTful API s CORS v Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

> [!NOTE]
> Vlastní konektory můžete vytvářet také v uživatelském rozhraní PowerApps a Microsoft Flow bez použití definice OpenAPI. Další informace najdete v tématu [registrace a použití vlastního konektoru (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/) a [registrace a použití vlastního konektoru (Microsoft flow)](/power-automate/developer/register-custom-api).

Pokud chcete exportovat definici rozhraní API, postupujte podle těchto kroků:

1. V [Azure Portal](https://portal.azure.com)přejděte do aplikace Function app nebo App Service aplikace.

    V nabídce vlevo v části **rozhraní API**vyberte **definice rozhraní API**.

    :::image type="content" source="media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png" alt-text="Azure Functions definice rozhraní API":::

2. Tlačítko **exportovat do PowerApps + Microsoft Flow** by mělo být dostupné (Pokud ne, musíte nejdřív vytvořit definici openapi). Kliknutím na toto tlačítko zahájíte proces exportu.

    ![Tlačítko Exportovat do PowerApps + Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. Vyberte **režim exportu**:

    **Express** umožňuje vytvořit vlastní konektor v rámci Azure Portal. Vyžaduje, abyste byli přihlášeni k PowerApps nebo Microsoft Flow a máte oprávnění k vytváření konektorů v cílovém prostředí. Tento přístup se doporučuje, pokud tyto dva požadavky můžou být splněné. Pokud používáte tento režim, postupujte podle pokynů k [Použití expresního exportu](#express) níže.

    **Ruční** umožňuje exportovat definici rozhraní API, kterou pak importujete pomocí portálů PowerApps nebo Microsoft Flow. Tento přístup se doporučuje, pokud uživatel Azure a uživatel s oprávněním k vytváření konektorů jsou různí lidé nebo pokud je potřeba konektor vytvořit v jiném tenantovi Azure. Pokud používáte tento režim, postupujte podle pokynů k [ručnímu exportu](#manual) níže.

    ![Režim exportu](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> Vlastní konektor používá *kopii* definice rozhraní API, takže PowerApps a Microsoft Flow okamžitě nevědí, jestli provedete změny aplikace a její definice rozhraní API. Pokud provedete změny, opakujte kroky exportu pro novou verzi.

<a name="express"></a>
## <a name="use-express-export"></a>Použití expresního exportu

K dokončení exportu v **expresním** režimu použijte následující postup:

1. Ujistěte se, že jste přihlášeni k tenantovi PowerApps nebo Microsoft Flow, do kterého chcete exportovat. 

2. Použijte nastavení uvedená v tabulce.

    |Nastavení|Popis|
    |--------|------------|
    |**Prostředí**|Vyberte prostředí, do kterého se má vlastní konektor Uložit. Další informace najdete v tématu [Prostředí – přehled](https://powerapps.microsoft.com/tutorials/environments-overview/).|
    |**Název vlastního rozhraní API**|Zadejte název, který PowerApps a tvůrci Microsoft Flow uvidí v seznamu konektorů.|
    |**Připravit konfiguraci zabezpečení**|V případě potřeby zadejte podrobnosti konfigurace zabezpečení potřebné k udělení přístupu uživatelům k vašemu rozhraní API. Tento příklad ukazuje klíč rozhraní API. Další informace najdete v tématu [určení typu ověřování](#auth) níže.|
 
    ![Expresní export do PowerApps a Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. Klikněte na **OK**. Vlastní konektor je teď sestavený a přidaný do zadaného prostředí.

<a name="manual"></a>
## <a name="use-manual-export"></a>Použití ručního exportu

Pokud chcete export dokončit v **ručním** režimu, postupujte podle těchto kroků:

1. Klikněte na **Stáhnout** a uložte soubor, nebo klikněte na tlačítko Kopírovat a uložte adresu URL. Během importu použijete soubor ke stažení nebo adresu URL.
 
    ![Ruční export do PowerApps a Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. Pokud definice rozhraní API obsahuje nějaké definice zabezpečení, tyto definice se zavolají v kroku #2. Během importu PowerApps a Microsoft Flow detekuje tyto definice a vyzývá informace o zabezpečení. Shromážděte přihlašovací údaje týkající se každé definice pro použití v další části. Další informace najdete v tématu [určení typu ověřování](#auth) níže.

    ![Zabezpečení ručního exportu](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    Tento příklad ukazuje definici zabezpečení klíčů rozhraní API, která byla obsažena v definici OpenAPI.

Teď, když jste exportovali definici rozhraní API, naimportujete ho a vytvoříte vlastní konektor v PowerApps a Microsoft Flow. Vlastní konektory se sdílejí mezi těmito dvěma službami, takže je potřeba nejdřív importovat jenom jednu definici.

Pokud chcete naimportovat definici rozhraní API do PowerApps a Microsoft Flow, postupujte podle následujících kroků:

1. Přejděte na adresu [powerapps.com](https://web.powerapps.com) nebo [flow.microsoft.com](https://flow.microsoft.com).

2. V pravém horním rohu klikněte na ikonu ozubeného kolečka a pak klikněte na **vlastní konektory**.

   ![Ikona ozubeného kola ve službě](media/app-service-export-api-to-powerapps-and-flow/icon-gear.png)

3. Klikněte na **vytvořit vlastní konektor**a pak klikněte na **importovat definici openapi**.

   ![Vytvoření vlastního konektoru](media/app-service-export-api-to-powerapps-and-flow/flow-apps-create-connector.png)

4. Zadejte název vlastního konektoru, přejděte do definice OpenAPI, kterou jste exportovali, a klikněte na **pokračovat**.

   ![Odeslat definici OpenAPI](media/app-service-export-api-to-powerapps-and-flow/flow-apps-upload-definition.png)

4. Na kartě **Obecné** zkontrolujte informace, které pocházejí z definice openapi.

5. Pokud se zobrazí výzva k zadání podrobností ověření, na kartě **zabezpečení** zadejte hodnoty odpovídající typu ověřování. Klikněte na **Pokračovat**.

    ![Karta zabezpečení](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    Tento příklad zobrazuje povinná pole pro ověřování klíčů rozhraní API. Pole se liší v závislosti na typu ověřování.

6. Na kartě **definice** se automaticky vyplní všechny operace definované v souboru openapi. Pokud jsou definované všechny požadované operace, můžete přejít k dalšímu kroku. Pokud ne, můžete sem přidat a upravit operace.

    ![Karta definice](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    Tento příklad obsahuje jednu operaci s názvem `CalculateCosts` . Metadata, jako je **Popis**, pocházejí ze souboru openapi.

7. V horní části stránky klikněte na **vytvořit konektor** .

Nyní se můžete připojit k vlastnímu konektoru v PowerApps a Microsoft Flow. Další informace o vytváření konektorů na portálech PowerApps a Microsoft Flow najdete v tématu [registrace vlastního konektoru (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) a [registrace vlastního konektoru (Microsoft flow)](/power-automate/get-started-flow-dev#create-a-custom-connector).

<a name="auth"></a>
## <a name="specify-authentication-type"></a>Zadání typu ověřování

PowerApps a Microsoft Flow podporují kolekci zprostředkovatelů identity, která poskytuje ověřování pro vlastní konektory. Pokud vaše rozhraní API vyžaduje ověření, ujistěte se, že je v dokumentu OpenAPI zachycen jako _Definice zabezpečení_ , jako v následujícím příkladu:

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
Během exportu zadáte konfigurační hodnoty, které umožní PowerApps a Microsoft Flow ověřovat uživatele.

Tato část se zabývá typy ověřování, které jsou podporované v **expresním** režimu: klíč rozhraní API, Azure Active Directory a obecné OAuth 2,0. PowerApps a Microsoft Flow také podporují základní ověřování a OAuth 2,0 pro konkrétní služby, jako jsou Dropbox, Facebook nebo SalesForce.

### <a name="api-key"></a>Klíč rozhraní API
Při použití klíče rozhraní API se uživatelům konektoru zobrazí výzva k zadání klíče při vytváření připojení. Zadejte název klíče rozhraní API, který jim pomůže pochopit, který klíč je potřeba. V předchozím příkladu používáme název, `API Key (contact meganb@contoso.com)` aby lidé věděli, kde získat informace o klíči rozhraní API. Pro Azure Functions je klíč obvykle jedním z klíčů hostitele, který pokryje několik funkcí v rámci aplikace Function App.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
Při použití Azure AD potřebujete dva registrace aplikací Azure AD: jedno pro samotné rozhraní API a jeden pro vlastní konektor:

- Ke konfiguraci registrace pro rozhraní API použijte funkci [App Service ověřování/autorizace](../app-service/configure-authentication-provider-aad.md) .

- Pokud chcete nakonfigurovat registraci konektoru, postupujte podle kroků v části [Přidání aplikace Azure AD](../active-directory/develop/quickstart-register-app.md). Registrace musí mít delegovaný přístup k vašemu rozhraní API a adresu URL odpovědi `https://msmanaged-na.consent.azure-apim.net/redirect` . 

Další informace najdete v tématu Příklady registrace Azure AD pro [PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) a [Microsoft Flow](/connectors/custom-connectors/azure-active-directory-authentication). Tyto příklady používají jako rozhraní API Azure Resource Manager; Pokud budete postupovat podle pokynů, nahraďte své rozhraní API.

Jsou vyžadovány následující hodnoty konfigurace:
- **ID klienta** – ID klienta vašeho konektoru registrace Azure AD
- **Tajný klíč klienta** – tajný klíč klienta registrace Azure AD vašeho konektoru
- **Adresa URL pro přihlášení** – základní adresa URL služby Azure AD. V Azure obvykle `https://login.windows.net` .
- **ID tenanta** – ID tenanta, který se má použít pro přihlášení. Toto ID by mělo být "Common" nebo ID tenanta, ve kterém je konektor vytvořen.
- **Adresa URL prostředku** – adresa URL prostředku registrace Azure AD pro vaše rozhraní API

> [!IMPORTANT]
> Pokud někdo jiný naimportuje definici rozhraní API do PowerApps a Microsoft Flow jako součást ručního toku, musíte jim poskytnout ID klienta a tajný klíč klienta *registrace konektoru*a také adresu URL prostředku vašeho rozhraní API. Ujistěte se, že jsou tyto tajné klíče spravované bezpečně. **Nesdílejte bezpečnostní pověření samotného rozhraní API.**

### <a name="generic-oauth-20"></a>Obecné OAuth 2.0
Při použití obecného OAuth 2,0 můžete integrovat s jakýmkoli poskytovatelem OAuth 2,0. Díky tomu můžete pracovat s vlastními poskytovateli, kteří nejsou nativně podporováni.

Jsou vyžadovány následující hodnoty konfigurace:
- **ID klienta** – ID klienta OAuth 2,0
- **Tajný kód klienta** – tajný kód klienta OAuth 2,0
- **Autorizační adresa URL** – autorizační adresa url OAuth 2,0
- **Adresa URL tokenu** – adresa URL tokenu OAuth 2,0
- **Aktualizovat adresu URL** – adresa URL pro aktualizaci OAuth 2,0
