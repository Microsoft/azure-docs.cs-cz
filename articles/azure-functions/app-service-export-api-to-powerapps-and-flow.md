---
title: Export rozhraní API Azure hostovaná PowerApps a Microsoft toku | Microsoft Docs
description: Přehled o tom, jak vystavit rozhraní API hostované ve službě App Service PowerApps a Flow Microsoft
services: app-service
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 12/15/2017
ms.author: glenga
ms.reviewer: sunayv
ms.openlocfilehash: ef3fe5002a28c66478a10909a7e9556449cd9712
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234763"
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Export rozhraní API Azure hostovaná PowerApps a Microsoft toku

[PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) je služba pro vytváření a používání vlastní obchodní aplikace, které se připojují ke svým datům a fungovat na všech platformách. [Microsoft Flow](https://flow.microsoft.com/guided-learning/learning-introducing-flow/) usnadňuje automatizovat pracovní postupy a obchodní procesy mezi vaše oblíbené aplikace a služby. Jak PowerApps a Flow Microsoft obsahují celou řadu předdefinovaných konektory ke zdrojům dat, jako je například Office 365, Dynamics 365, Salesforce a další. V některých případech aplikace a toku počítačů také chcete se připojit k rozhraní API vytvořené své organizace a zdroje dat.

Podobně vývojáře, která chcete vystavit příslušných rozhraní API více široce v rámci organizace, můžete zpřístupnit příslušných rozhraní API na aplikaci a toku Tvůrce. Toto téma ukazuje, jak exportovat rozhraní API vytvořené s [Azure Functions](../azure-functions/functions-overview.md) nebo [Azure App Service](../app-service/app-service-web-overview.md). Bude exportovaný rozhraní API *vlastní konektor*, která je použita v PowerApps a Microsoft Flow stejně jako integrovaného konektoru.

## <a name="create-and-export-an-api-definition"></a>Vytvořit a exportovat definice rozhraní API.
Před exportem rozhraní API, musí popisovat, rozhraní API pomocí definici OpenAPI (dříve označovaný jako [Swagger](http://swagger.io/) souboru). Tato definice obsahuje informace o tom, jaké operace jsou v rozhraní API dostupné a jakou strukturu by měla mít data požadavku a odpovědi pro toto rozhraní API. PowerApps a Flow Microsoft můžete vytvořit vlastní konektory pro všechny definice OpenAPI 2.0. Azure funkce a služby Azure App Service mít integrovanou podporu pro vytváření, hostování a správě OpenAPI definice. Další informace najdete v tématu [hostování rozhraní RESTful API s CORS v Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

> [!NOTE]
> Můžete také vytvořit vlastní konektory v PowerApps a Microsoft toku uživatelského rozhraní, bez použití OpenAPI definice. Další informace najdete v tématu [registrace a použít vlastní konektor (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/) a [registrace a použít vlastní konektor (Microsoft Flow)](https://flow.microsoft.com/documentation/register-custom-api/).

Pokud chcete exportovat definice rozhraní API, postupujte takto:

1. V [portál Azure](https://portal.azure.com), přejděte na Azure Functions nebo jiná aplikace služby App Service.

    Pokud používáte Azure Functions, vyberte svou aplikaci funkce, vyberte **funkce**a potom **definice rozhraní API**.

    ![Azure definice rozhraní API funkce](media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png)

    Pokud používáte Azure App Service, vyberte **definice rozhraní API** ze seznamu nastavení.

    ![Definice rozhraní API služby App Service](media/app-service-export-api-to-powerapps-and-flow/api-definition-app.png)

2. **Exportovat do PowerApps + Microsoft Flow** tlačítko by měla být k dispozici (Pokud ne, musíte nejdřív vytvořit definici OpenAPI). Kliknutím na toto tlačítko k zahájení procesu exportu.

    ![Exportovat do PowerApps + Microsoft Flow tlačítko](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. Vyberte **exportovat režimu**:

    **Express** se dá vytvořit vlastní konektor z portálu Azure. Vyžaduje se přihlášení k PowerApps nebo Flow Microsoft a mají oprávnění vytvářet konektory v cílovém prostředí. Toto je doporučený postup, pokud tyto dva požadavky lze splnit. Pokud při použití tohoto režimu, postupujte podle kroků [použít expresní exportu](#express) podle následujících pokynů.

    **Ruční** umožňuje exportovat definice rozhraní API, které pak importovat používání portálů PowerApps nebo Flow společnosti Microsoft. Toto je doporučený postup, pokud uživatel Azure a uživatel s oprávněním k vytvoření konektory jsou jiné osoby nebo pokud konektor musí být vytvořen v jiné klientovi Azure. Pokud při použití tohoto režimu, postupujte podle kroků [použít ruční export](#manual) podle následujících pokynů.

    ![Export režimu](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> Vlastní konektor používá *kopie* definice rozhraní API, takže PowerApps a Flow Microsoft nebude vědět okamžitě pokud provedete změny aplikace a jeho definice rozhraní API. Pokud provedete změny, opakujte kroky exportu pro novou verzi.

<a name="express"></a>
## <a name="use-express-export"></a>Použít expresní exportu

Chcete-li dokončit export v **Express** režimu, postupujte takto:

1. Ujistěte se, že jste přihlášeni PowerApps nebo Microsoft Flow klienta, do kterého chcete provést export. 

2. Použijte nastavení zadané v tabulce.

    |Nastavení|Popis|
    |--------|------------|
    |**Prostředí**|Vyberte prostředí, ve kterém by měl být uložen vlastní konektor. Další informace najdete v tématu [Přehled prostředí](https://powerapps.microsoft.com/tutorials/environments-overview/).|
    |**Název vlastního rozhraní API**|Zadejte název, který PowerApps a Microsoft Flow počítačů se zobrazí v jejich seznamu konektor.|
    |**Příprava konfigurace zabezpečení**|V případě potřeby zadejte podrobnosti konfigurace zabezpečení potřebné pro uživatelům uděluje přístup k vašemu rozhraní API. Tento příklad ukazuje klíč rozhraní API. Další informace najdete v tématu [zadejte typ ověřování](#auth) níže.|
 
    ![Export do PowerApps a Microsoft Flow Express](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. Klikněte na **OK**. Vlastní konektor je teď vytvořené a přidat do prostředí, které zadáte.

Příklady použití **Express** režimu s Azure Functions najdete v části [volat funkci z PowerApps](functions-powerapps-scenario.md) a [volání funkce z Microsoft Flow](functions-flow-scenario.md).

<a name="manual"></a>
## <a name="use-manual-export"></a>Použít ruční export

Chcete-li dokončit export v **ruční** režimu, postupujte takto:

1. Klikněte na tlačítko **Stáhnout** a uložte soubor, nebo klikněte na tlačítko Kopírovat a uložte adresu URL. Soubor ke stažení nebo adresu URL použije při importu.
 
    ![Ruční export na PowerApps a Flow Microsoft](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. Pokud vaše definice rozhraní API obsahuje všechny definice zabezpečení, toto nastavení se nazývá v kroku #2. Během importu PowerApps a Flow Microsoft tyto zjistí a vyzve k zadání informací o zabezpečení. Získat přihlašovací údaje související s každou definici pro použití v další části. Další informace najdete v tématu [zadejte typ ověřování](#auth) níže.

    ![Zabezpečení pro ruční export](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    Tento příklad ukazuje definici klíče zabezpečení rozhraní API, která byla součástí definice OpenAPI.

Teď, když jste exportovali definice rozhraní API, importujete ho a vytvořte vlastní konektor v PowerApps a Flow společnosti Microsoft. Vlastní konektory jsou sdílené mezi dvě služby, takže potřebujete importovat definici jednou.

Import definice rozhraní API do PowerApps a Flow Microsoft, postupujte takto:

1. Přejděte na adresu [powerapps.com](https://web.powerapps.com) nebo [flow.microsoft.com](https://flow.microsoft.com).

2. V pravém horním rohu klikněte na ikonu zařízení a pak klikněte na **vlastní konektory**.

   ![Ikona ozubeného kola ve službě](media/app-service-export-api-to-powerapps-and-flow/icon-gear.png)

3. Klikněte na tlačítko **vytvořte vlastní konektor**, pak klikněte na tlačítko **importovat definici OpenAPI**.

   ![Vytvoření vlastního konektoru](media/app-service-export-api-to-powerapps-and-flow/flow-apps-create-connector.png)

4. Zadejte název pro vlastní konektor, pak přejděte k definici OpenAPI, který jste exportovali a klikněte na tlačítko **pokračovat**.

   ![Nahrát OpenAPI definice](media/app-service-export-api-to-powerapps-and-flow/flow-apps-upload-definition.png)

4. Na **Obecné** zkontrolujte informace, které pochází z definice OpenAPI.

5. Na **zabezpečení** kartě, pokud se zobrazí výzva k zadejte podrobnosti o ověřování, zadejte hodnoty, které jsou vhodné pro typ ověřování. Klikněte na tlačítko **pokračovat**.

    ![Karta zabezpečení](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    Tento příklad ukazuje povinná pole pro ověření pomocí klíče rozhraní API. Pole se liší v závislosti na typu ověřování.

6. Na **definice** , všechny operace, které jsou definované v souboru OpenAPI jsou automaticky vyplněna. Pokud jsou definovány všechny požadované operace, můžete přejít k dalšímu kroku. Pokud ne, můžete přidat a upravit operations sem.

    ![Karta definice](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    V tomto příkladu má jednu operaci, s názvem `CalculateCosts`. Metadata, například **popis**, všechny pochází z OpenAPI souboru.

7. Klikněte na tlačítko **vytvořit konektor** v horní části stránky.

Nyní můžete připojit k vlastní konektor v PowerApps a Flow společnosti Microsoft. Další informace týkající se vytváření konektory portálů PowerApps a Flow Microsoft najdete v tématu [zaregistrovat vlastní spojnici (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) a [zaregistrovat vlastní spojnici (Microsoft Flow)](https://flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector).

<a name="auth"></a>
## <a name="specify-authentication-type"></a>Zadání typu ověřování

PowerApps a Flow Microsoft podporují kolekci zprostředkovatelů identity, které zajišťují ověřování pro vlastní konektory. Pokud vaše rozhraní API vyžaduje ověření, ujistěte se, že se zaznamená jako _zabezpečení definice_ v dokumentu OpenAPI, jako v následujícím příkladu:

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
Během exportu zadejte hodnoty konfigurace, které umožňují PowerApps a Flow Microsoft k ověřování uživatelů.

Tato část popisuje typy ověřování, které jsou podporovány v **Express** režim: klíč rozhraní API, Azure Active Directory a obecné OAuth 2.0. PowerApps a Flow Microsoft také podporují základní ověřování a OAuth 2.0 pro konkrétní služby, jako je Dropbox, Facebook či služby SalesForce.

### <a name="api-key"></a>Klíč rozhraní API
Pokud používáte klíč rozhraní API, uživatelé konektoru se výzva k zadání klíč při vytváření připojení. Zadáte název klíče pomáhá jim pochopit, které je zapotřebí rozhraní API. V předchozím příkladu používáme název `API Key (contact meganb@contoso.com)` aby uživatelé věděli, kde chcete získat informace o klíč rozhraní API. Pro Azure Functions klíč je obvykle jeden z klíčů hostitele, pokrývajících několik funkcí v rámci funkce aplikace.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
Při používání služby Azure AD, budete potřebovat dva registrace aplikace Azure AD: jeden pro rozhraní API, sám a jeden pro vlastní konektor:

- Ke konfiguraci registrace pro rozhraní API, použijte [ověřování/autorizace služby App Service](../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md) funkce.

- Ke konfiguraci registrace pro konektor, postupujte podle kroků v [přidání aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application). Registrace musí mít delegovaná přístup k rozhraní API a adresa URL odpovědi z `https://msmanaged-na.consent.azure-apim.net/redirect`. 

Další informace najdete v tématu příklady registrace Azure AD pro [PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) a [Microsoft Flow](https://flow.microsoft.com/documentation/customapi-azure-resource-manager-tutorial/). Tyto příklady použití Azure Resource Manager jako rozhraní API; Pokud budete postupovat podle kroků, nahraďte vaše rozhraní API.

Jsou tyto hodnoty konfigurace vyžaduje:
- **ID klienta** -ID klienta Azure AD registrace konektoru
- **Tajný klíč klienta** -tajný klíč klienta Azure AD registrace konektoru
- **Adresa URL pro přihlášení** – základní adresu URL pro Azure AD. V Azure, je to obvykle `https://login.windows.net`.
- **ID klienta** -ID klienta, který se má použít pro přihlášení. Měl by být "běžné" nebo ID klienta, ve kterém je vytvořen konektor.
- **Adresa URL prostředku** – prostředek Adresa URL registrace Azure AD pro vaše rozhraní API

> [!IMPORTANT]
> Pokud někdo bude import definice rozhraní API do PowerApps a Flow Microsoft jako součást ruční toku, je potřeba je zadat pomocí ID klienta a tajný klíč klienta z *registrace konektoru*, a také na adresu URL zdroje rozhraní API. Ujistěte se, že těchto tajných klíčů spravuje bezpečně. **Nesdílí pověření zabezpečení rozhraní API, sám sebe.**

### <a name="generic-oauth-20"></a>Obecné OAuth 2.0
Při použití obecné OAuth 2.0, můžete integrovat s kteréhokoli poskytovatele služeb, OAuth 2.0. Můžete tak pracovat s vlastní zprostředkovatelé, kteří nejsou nativně podporovány.

Jsou tyto hodnoty konfigurace vyžaduje:
- **ID klienta** -ID klienta OAuth 2.0
- **Tajný klíč klienta** -tajný klíč klienta OAuth 2.0
- **URL pro autorizaci** -URL pro autorizaci OAuth 2.0
- **Token URL** -adresy URL tokenu OAuth 2.0
- **Aktualizujte adresu URL** – adresa URL aktualizace OAuth 2.0


