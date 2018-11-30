---
title: Dynamické přizpůsobení uživatelského rozhraní Azure Active Directory B2C (UI) pomocí vlastních zásad | Dokumentace Microsoftu
description: Podpora více značky prostředí směřovat HTML5 a CSS, která se dynamicky mění v době běhu.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 11a14bc8b593e5e7d81e9bdbd4ac4ee3b2bbecaa
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582883"
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-by-using-custom-policies"></a>Azure Active Directory B2C: Konfigurace uživatelského rozhraní s dynamickým obsahem pomocí vlastních zásad

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Pomocí Azure Active Directory B2C (Azure AD B2C) vlastní zásady, můžete poslat parametr v řetězci dotazu. Předáním parametru do vašeho koncového bodu HTML můžete dynamicky měnit obsah stránky. Můžete například změnit obrázek pozadí na registrační nebo přihlašovací stránce Azure AD B2C na základě parametru, který předáte z vašeho webu nebo mobilní aplikace. 

## <a name="prerequisites"></a>Požadavky
Tento článek se zaměřuje na tom, jak přizpůsobit uživatelské rozhraní Azure AD B2C s *dynamický obsah* pomocí vlastních zásad. Abyste mohli začít, najdete v článku [přizpůsobení uživatelského rozhraní ve vlastních zásadách](active-directory-b2c-ui-customization-custom.md). 

>[!NOTE]
>Azure AD B2C článku [přizpůsobení konfigurace uživatelského rozhraní ve vlastních zásadách](active-directory-b2c-ui-customization-custom.md), popisuje následující základní informace:
> * Funkce přizpůsobení stránky uživatelského rozhraní (UI).
> * Základní nástroje pro testování funkce přizpůsobení uživatelského rozhraní stránky s využitím naší ukázkový obsah.
> * Zadejte základní prvky uživatelského rozhraní každé stránky.
> * Osvědčené postupy pro použití této funkce.

## <a name="add-a-link-to-html5css-templates-to-your-user-journey"></a>Přidat odkaz na HTML5 a CSS šablony do vaší cesty uživatele

Ve vlastních zásadách definuje definici obsahu stránky HTML5 identifikátor URI, který slouží ke konkrétnímu kroku uživatelského rozhraní (například, přihlaste se nebo zaregistrujte stránky). Základní zásada definuje výchozí vzhled a chování najetím myší na identifikátor URI HTML5 souborů (v jazyce CSS). V případě rozšíření zásad můžete upravit vzhled a chování tak, že přepíšete LoadUri pro soubor HTML5. Definice obsahu obsahuje adresy URL na externí obsah, který je definován vytváření souborů HTML5 a CSS, podle potřeby. 

`ContentDefinitions` Oddíl obsahuje řadu `ContentDefinition` elementů XML. Atribut ID `ContentDefinition` prvek určuje typ stránky, která má vztah k definici obsahu. To znamená element definuje kontext, na který budete použít vlastní šablonu HTML5 a CSS. Následující tabulka popisuje sadu obsahu definice ID, které jsou rozpoznány modulem pro modul IEF a typy stránek, které se vztahují k nim.

| ID obsahu definice | Výchozí šablona HTML5| Popis | 
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Chybová stránka**. Na této stránce se zobrazí, když došlo k výjimce nebo došlo k chybě. |
| *API.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Stránka výběru zprostředkovatele identit**. Tato stránka obsahuje seznam zprostředkovatelů identity, které uživatelé můžou vybírat při přihlašování. Možnosti jsou obvykle enterprise zprostředkovatelů identity, zprostředkovatelé sociálních identit, jako je Facebook a Google + nebo místním účtům. |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Výběr zprostředkovatele identity pro registraci**. Tato stránka obsahuje seznam zprostředkovatelů identity, které uživatelé můžou vybírat při registraci. Možnosti jsou organizace zprostředkovatelů identity, zprostředkovatelé sociálních identit, jako je Facebook nebo Google + nebo místním účtům. |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Stránka zapomenuté heslo**. Tato stránka obsahuje formulář, který uživatelé musí dokončit k zahájení resetování hesla.  |
| *API.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Místní účet přihlašovací stránku**. Tato stránka obsahuje formulář pro přihlašování pomocí místní účet, který je založen na e-mailovou adresu nebo jméno uživatele. Formulář může obsahovat pole textového zadání a pole pro zadání hesla. |
| *API.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Místní účet registrační stránku**. Tato stránka obsahuje formulář pro registraci místního účtu, který je založen na e-mailovou adresu nebo jméno uživatele. Formulář může obsahovat různé vstupní ovládací prvky, jako například: textový vstup pole, pole pro zadání hesla, přepínací tlačítko, rozevírací seznamy výběru jednoho a víc zaškrtněte políčka. |
| *api.phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Stránka služby Multi-Factor authentication**. Na této stránce si uživatelé mohli ověřit jejich telefonní čísla (pomocí textových nebo hlasových) během registrace nebo přihlášení. |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Stránka registrace sociálního účtu**. Tato stránka obsahuje formulář, který uživatelé musí dokončit při registraci pomocí existující účet od poskytovatele identity v sociálních sítích. Tato stránka je podobný předchozí stránka registrace sociálního účtu, s výjimkou pole pro zadání hesla. |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Stránka pro aktualizaci profilu**. Tato stránka obsahuje formulář, který mohou uživatelé aktualizovat svůj profil. Tato stránka je podobná stránka registrace sociálního účtu s výjimkou pole pro zadání hesla. |
| *api.signuporsignin* | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Jednotná stránka registrace nebo přihlašování**. Na této stránce zpracovává proces registrace a přihlášení uživatele. Uživatelé můžou používat podnikové zprostředkovatelů identity, zprostředkovatelé sociálních identit, jako je Facebook nebo Google + nebo místním účtům.  |

## <a name="serving-dynamic-content"></a>Poskytování obsahu dynamického obsahu
V [přizpůsobení konfigurace uživatelského rozhraní ve vlastních zásadách](active-directory-b2c-ui-customization-custom.md) článku, nahrání HTML5 souborů do úložiště objektů Blob v Azure. Tyto soubory HTML5 jsou statické a vykreslení obsahu pro každý požadavek stejný kód HTML. 

V tomto článku použijte k webové aplikaci ASP.NET, které můžou přijímat parametry řetězce dotazu a odpovídajícím způsobem reagovat. 

V tomto podrobném návodu můžete:
* Vytvoření webové aplikace ASP.NET Core, který je hostitelem vaší šablony HTML5. 
* Přidat vlastní šablonu HTML5 _unified.cshtml_. 
* Publikujte webovou aplikaci do služby Azure App Service. 
* Nastavení prostředků mezi zdroji (CORS) pro sdílení obsahu pro vaši webovou aplikaci.
* Přepsat `LoadUri` prvky tak, aby odkazovala na váš soubor HTML5.

## <a name="step-1-create-an-aspnet-web-app"></a>Krok 1: Vytvoření webové aplikace ASP.NET

1. V sadě Visual Studio vytvořte projekt tak, že vyberete **souboru** > **nový** > **projektu**.

2. V **nový projekt** okně **Visual C#** > **webové** > **webová aplikace ASP.NET Core (.NET Core)**.

3. Pojmenujte aplikaci (například *Contoso.AADB2C.UI*) a pak vyberte **OK**.

    ![Vytvořit nový projekt sady Visual Studio](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4. Vyberte **webovou aplikaci** šablony.

5. Nastavte ověřování na **bez ověřování**.

    ![Výběr šablony webové aplikace](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6. Vyberte **OK** a vytvořte projekt.

## <a name="step-2-create-mvc-view"></a>Krok 2: Vytvoření zobrazení MVC
### <a name="step-21-download-the-b2c-built-in-html5-template"></a>Krok 2.1: Stáhněte šablonu B2C integrované HTML5
Vaše vlastní HTML5 šablona je založena na Azure AD B2C integrovanou šablonu HTML5. Můžete stáhnout [unified.html souboru](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) nebo stáhnout šablonu z [starter pack](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip). Tento soubor HTML5 použijete k vytvoření jednotná stránka registrace nebo přihlášení.

### <a name="step-22-add-the-mvc-view"></a>Krok 2.2: Přidat zobrazení MVC
1. Klikněte pravým tlačítkem na zobrazení/domovské složky a potom **přidat** > **nová položka**.

    ![Přidat novou položku MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)

2. V **přidat novou položku - Contoso.AADB2C.UI** okně **Web > ASP.NET**.

3. Vyberte **stránka zobrazení MVC**.

4. V **název** pole, změňte název na **unified.cshtml**.

5. Vyberte **Přidat**.

    ![Přidat zobrazení MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)

6. Pokud *unified.cshtml* soubor již není otevřen, poklikejte na soubor a otevřete ho a pak vymazat obsah souboru.

7. V tomto návodu budeme odeberte odkaz na stránku rozložení. Přidejte následující fragment kódu pro _unified.cshtml_:

    ```csharp
    @{
        Layout = null;
    }
    ```

8. Otevřít _unified.cshtml_ souborů, které jste stáhli ze služby Azure AD B2C integrovanou šablonu HTML5.

9. Nahradit jedním zavináče (@) s dvojitou zavináči (@@).

10. Obsah souboru zkopírujte a vložte ji pod definice rozložení. Váš kód by měl vypadat:

    ![Unified.cshtml souboru po přidání HTML5](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)

### <a name="step-23-change-the-background-image"></a>Krok 2.3: Změna obrázku pozadí

Vyhledejte `<img>` element, který obsahuje `ID` hodnotu *background_background_image*a potom nahraďte `src` hodnotu **https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1** nebo jakékoli jiné Obrázek pozadí, který chcete použít.

![Změnit na pozadí stránky](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)

### <a name="step-24-add-your-view-to-the-mvc-controller"></a>2.4 krok: Přidání zobrazení do kontroler MVC

1. Otevřít **Controllers\HomeController.cs**a přidejte následující metodu: 

    ```C
    public IActionResult unified()
    {
        return View();
    }
    ```
    Tento kód určuje, zda by měla používat metody *zobrazení* soubor šablony k vykreslení odezva do prohlížeče. Protože jsme explicitně nezadávejte název *zobrazení* soubor šablony MVC ve výchozím nastavení používá _unified.cshtml_ zobrazení souboru v */zobrazení Domů* složky.
    
    Po přidání _jednotné_ metoda, váš kód by měl vypadat:
    
    ![Změna kontroleru k vykreslení zobrazení](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

2. Ladění webových aplikací a ujistěte se, že _jednotné_ stránka je přístupná (například `http://localhost:<Port number>/Home/unified`).

### <a name="step-25-publish-to-azure"></a>Krok 2.5: Publikování aplikace do Azure
1. V **Průzkumníka řešení**, klikněte pravým tlačítkem myši **Contoso.AADB2C.UI** projektu a pak vyberte **publikovat**.

    ![Publikování do Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2. Vyberte **Microsoft Azure App Service** dlaždici a vyberte **publikovat**.

    ![Vytvořit novou službu Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    **Vytvořit službu App Service** otevře se okno. V něm můžete začít vytvářet všechny prostředky Azure potřebné ke spuštění webové aplikace ASP.NET v Azure.

    > [!NOTE]
    > Další informace o publikování najdete v tématu [vytvoření webové aplikace ASP.NET v Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure).

3. V **název webové aplikace** zadejte jedinečný název aplikace (platné znaky jsou a – z, A-Z, 0-9 a spojovník (-). Adresa URL webové aplikace je `http://<app_name>.azurewebsites.NET`, kde `<app_name>` je název vaší webové aplikace. Můžete přijmout automaticky vygenerovaný název, který je jedinečný.

4. Výběrem možnosti **Vytvořit** spustíte vytváření prostředků Azure.

    ![Zadat vlastnosti služby App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

    Po dokončení procesu vytváření průvodce publikuje webovou aplikaci ASP.NET do Azure a pak aplikaci spustí ve výchozím prohlížeči.

5. Zkopírujte adresu URL _jednotné_ stránky (například _https://<app_name>.azurewebsites.net/home/unified_).

## <a name="step-3-configure-cors-in-azure-app-service"></a>Krok 3: Konfigurace CORS v Azure App Service
1. V [webu Azure portal](https://portal.azure.com/)vyberte **App Services**a potom vyberte název vaší aplikace API.

    ![Výběr aplikace API na webu Azure Portal](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

2. V **nastavení** pod **API** vyberte **CORS**.

    ![Vyberte nastavení CORS](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

3. V **CORS** okno v **povolené zdroje** pole, proveďte jednu z následujících akcí:

    * Zadejte adresu URL nebo adresy URL, které chcete povolit předávat volání JavaScriptu. Budete muset použít jenom malá písmena. v adresách URL, které zadáte.
    * Zadejte hvězdičku (*) k určení, že se přijímají všechny zdrojové domény.

4. Vyberte **Uložit**.

    ![V okně CORS](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

    Po výběru **Uložit**, aplikace API přijímat volání JavaScriptu ze zadané adresy URL. 

## <a name="step-4-html5-template-validation"></a>Krok 4: Ověření šablony HTML5
Šablona HTML5 je připravené k použití. Ale není k dispozici v `ContentDefinition` kódu. Než budete moct přidat `ContentDefinition` na vlastní zásadu, ujistěte se, že:
* Váš obsah je HTML5 kompatibilní a přístupné.
* Vaše servery obsahu je povolený pro CORS.

    >[!NOTE]
    >K ověření, že lokality, kde hostuješ obsahu má povolenou CORS a budete moci otestovat požadavků CORS, přejděte na [testovací cors.org](http://test-cors.org/) webu. 

* Obsloužit obsah je zabezpečené přes **HTTPS**.
* Používáte *absolutní adresy URL*, jako například *https://yourdomain/content*, pro všechny odkazy, obsah šablony stylů CSS a obrázky.

## <a name="step-5-configure-your-content-definition"></a>Krok 5: Konfigurace vaší definice obsahu
Ke konfiguraci `ContentDefinition`, postupujte takto:
1. Otevřete soubor základní zásady (například *TrustFrameworkBase.xml*).

2. Hledat `<ContentDefinitions>` prvek a potom zkopírujte celý obsah `<ContentDefinitions>` uzlu.

3. Otevřete soubor rozšíření (například *TrustFrameworkExtensions.xml*) a pak vyhledejte oddíl týkající `<BuildingBlocks>` elementu. Pokud element neexistuje, přidejte ji.

4. Vložte celý obsah `<ContentDefinitions>` uzel, který jste zkopírovali jako podřízený objekt `<BuildingBlocks>` elementu. 

5. Hledat `<ContentDefinition>` uzel, který obsahuje `Id="api.signuporsignin"` ve formátu XML, který jste zkopírovali.

6. Změňte hodnotu vlastnosti `LoadUri` z _~/tenant/default/unified_ k _https://<app_name>.azurewebsites.net/home/unified_.  
    Vlastní zásady by měl vypadat nějak takto:
    
    ![Vaše definice obsahu](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Krok 6: Nahrajte zásady pro vašeho tenanta
1. V [webu Azure portal](https://portal.azure.com), přepněte [kontextu vašeho tenanta Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)a pak vyberte **Azure AD B2C**.

2. Vyberte **architekturu rozhraní identit**.

3. Vyberte **všechny zásady**.

4. Vyberte **nahrát zásady**.

5. Vyberte **přepsat zásady, pokud existuje** zaškrtávací políčko.

6. Nahrát *TrustFrameworkExtensions.xml* souboru a ujistěte se, že projde úspěšně ověřovacím.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Krok 7: Test spustit pomocí vlastních zásad
1. Vyberte **nastavení Azure AD B2C**a pak vyberte **architekturu rozhraní identit**.

    >[!NOTE]
    >Spustit nyní vyžaduje aspoň jednu aplikaci do být registrované u klienta. Informace o postupu registrace aplikací, najdete v tématu Azure AD B2C [Začínáme](active-directory-b2c-get-started.md) článku nebo [registrace aplikace](active-directory-b2c-app-registration.md) článku.

2. Otevřít **B2C_1A_signup_signin**, předávající stranu vlastní zásady, které jste nahráli a pak vyberte **spustit nyní**.  
    Byste měli vidět vaše vlastní HTML5 na pozadí, který jste vytvořili dříve.

    ![Zásady registrace / přihlášení](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-add-dynamic-content"></a>Krok 8: Přidat dynamický obsah
Změnit na pozadí podle parametru řetězce dotazu s názvem _campaignId_. Vaše aplikace předávající strany (webových a mobilních aplikací) odešle parametr do Azure AD B2C. Vaše zásady přečte parametr a jeho hodnotu do šablony HTML5. 

### <a name="step-81-add-a-content-definition-parameter"></a>Krok 8.1: Přidání parametru definici obsahu

Přidat `ContentDefinitionParameters` element následujícím způsobem:
1. Otevřít *SignUpOrSignin* soubor zásad (například *SignUpOrSignin.xml*).

2. Hledat `<DefaultUserJourney>` uzlu. 

3. V `<DefaultUserJourney>` uzel, přidejte následující fragment kódu XML:  

    ```XML
    <UserJourneyBehaviors>
        <ContentDefinitionParameters>
            <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ```

### <a name="step-82-change-your-code-to-accept-a-query-string-parameter-and-replace-the-background-image"></a>Krok 8.2: Změňte kód tak, aby přijímal parametru řetězce dotazu a nahraďte obrázek pozadí
Upravit HomeController `unified` metodu tak, aby přijímal campaignId parametru. Metoda pak ověří parametr na hodnotu a nastaví `ViewData["background"]` proměnné odpovídajícím způsobem.

1. Otevřít *Controllers\HomeController.cs* souboru a potom změňte `unified` metodu tak, že přidáte následující fragment kódu:

    ```csharp
    public IActionResult unified(string campaignId)
    {
        // If campaign ID is Hawaii, show Hawaii background
        if (campaignId != null && campaignId.ToLower() == "hawaii")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1";
        }
        // If campaign ID is Tokyo, show Tokyo background
        else if (campaignId != null && campaignId.ToLower() == "tokyo")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19666_en_1";
        }
        // Default background
        else
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/18983_en_1";
        }

        return View();
    }

    ```

2. Vyhledejte `<img>` element s ID `background_background_image`a nahraďte `src` hodnotu `@ViewData["background"]`.

    ![Změnit na pozadí stránky](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>8.3: odešlete změny a publikovat zásady
1. Publikování projektu sady Visual Studio do služby Azure App Service.

2. Nahrát *SignUpOrSignin.xml* zásady Azure AD B2C.

3. Otevřít **B2C_1A_signup_signin**, RP vlastní zásady, které jste nahráli a pak vyberte **spustit nyní**.  
    Měli byste vidět stejný obrázek pozadí, který byl dříve zobrazí.

4. Zkopírujte adresu URL z panelu Adresa prohlížeče.

5. Přidat _campaignId_ parametr řetězce k identifikátoru URI dotazu. Například přidejte `&campaignId=hawaii`, jak je znázorněno v následujícím obrázku:

    ![Změnit na pozadí stránky](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)

6. Vyberte **Enter** a zobrazte obrázek na pozadí Havajské ostrovy.

    ![Změnit na pozadí stránky](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)

7. Změňte hodnotu na *Tokio*a pak vyberte **Enter**.  
    Prohlížeč zobrazí Tokio pozadí.

    ![Změnit na pozadí stránky](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>Krok 9: Změna zbývající část cesty uživatele
Pokud vyberete **zaregistrujte se hned teď** odkaz na přihlašovací stránce v prohlížeči se zobrazí výchozí obrázek na pozadí, definice není obrázek. Toto chování mohou nastat, protože jste změnili pouze stránky registrace nebo přihlášení. Chcete-li změnit rest svým vyhodnocení obsahu definice:
1. Přejděte zpět do "Krok 2" a postupujte takto:

    a. Stáhněte si *selfasserted* souboru.

    b. Zkopírujte obsah souboru.

    c. Vytvoření nového zobrazení *selfasserted*.

    d. Přidat *selfasserted* k **Domů** kontroleru.

2. Přejděte zpět do "Kroku 4" a postupujte takto: 

    a. V rozšíření zásady, najdete `<ContentDefinition>` uzel, který obsahuje `Id="api.selfasserted"`, `Id="api.localaccountsignup"`, a `Id="api.localaccountpasswordreset"`.

    b. Nastavte `LoadUri` atribut vaše *selfasserted* identifikátoru URI.

3. Přejděte zpět do "Krok 8.2" a změňte kód tak, aby přijímal parametrů řetězce dotazu, ale tentokrát s *selfasserted* funkce. 

4. Nahrát *TrustFrameworkExtensions.xml* zásady a ujistěte se, že projde úspěšně ověřovacím.

5. Spustit test zásady a pak vyberte **zaregistrujte** k zobrazení výsledku.

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Volitelné) Stažení kompletní zásady souborů a kódu
* Po dokončení [začít pracovat s vlastními zásadami](active-directory-b2c-get-started-custom.md) návodu, doporučujeme vám vytvořit váš scénář s využitím vlastních zásad pro soubory. Pro srovnání si uvádíme [ukázkové soubory zásad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).
* Můžete stáhnout kompletní kód z [řešení sady Visual Studio ukázkový pro referenci](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).




