---
title: Dynamické přizpůsobení Azure Active Directory B2C uživatelského rozhraní (UI) pomocí vlastních zásad | Microsoft Docs
description: Podpora více funkcí brandingu pomocí obsahu HTML5/CSS, který se dynamicky mění za běhu.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 43c0da3ca8fa4b2f74d48b0e202cc56bc8b9406c
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227222"
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-by-using-custom-policies"></a>Azure Active Directory B2C: Konfigurace uživatelského rozhraní s dynamickým obsahem pomocí vlastních zásad

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Pomocí vlastních zásad Azure Active Directory B2C (Azure AD B2C) můžete odeslat parametr v řetězci dotazu. Předáním parametru do vašeho koncového bodu HTML můžete dynamicky měnit obsah stránky. Můžete například změnit obrázek pozadí na registrační nebo přihlašovací stránce Azure AD B2C na základě parametru, který předáte z vašeho webu nebo mobilní aplikace.

## <a name="prerequisites"></a>Požadavky
Tento článek se zaměřuje na přizpůsobení Azure AD B2C uživatelského rozhraní s *dynamickým obsahem* pomocí vlastních zásad. Informace o tom, jak začít, najdete v tématu [přizpůsobení uživatelského rozhraní ve vlastních zásadách](active-directory-b2c-ui-customization-custom.md).

>[!NOTE]
>Článek Azure AD B2C, [Konfigurace přizpůsobení uživatelského rozhraní ve vlastních zásadách](active-directory-b2c-ui-customization-custom.md), popisuje následující základy:
> * Funkce přizpůsobení uživatelského rozhraní stránky (UI).
> * Základní nástroje pro testování funkce přizpůsobení uživatelského rozhraní stránky pomocí našeho ukázkového obsahu.
> * Základní prvky uživatelského rozhraní každého typu stránky.
> * Osvědčené postupy pro použití této funkce.

## <a name="add-a-link-to-html5css-templates-to-your-user-journey"></a>Přidání odkazu na vaši cestu uživatele do šablon HTML5/CSS

V vlastní zásadě definice obsahu definuje identifikátor URI stránky HTML5, který se používá pro zadaný krok uživatelského rozhraní (například přihlašovací stránky nebo přihlašovací stránky). Základní zásady definují výchozí vzhled a chování tak, že odkazují na identifikátor URI souborů HTML5 (v šabloně stylů CSS). V zásadách rozšíření můžete upravit vzhled a chování přepsáním LoadUri pro soubor HTML5. Definice obsahu obsahují adresy URL externího obsahu, které jsou definovány podle potřeby pro vytváření souborů HTML5/CSS.

Oddíl obsahuje řadu prvků `ContentDefinition`XML. `ContentDefinitions` Atribut `ContentDefinition` ID elementu určuje typ stránky, která se vztahuje k definici obsahu. To znamená, že element definuje kontext, který bude použita vlastní šablona HTML5/CSS. Následující tabulka popisuje sadu ID definic obsahu rozpoznávaných modulem IEF a typy stránek, které se na ně vztahují.

| ID definice obsahu | Výchozí šablona HTML5| Popis |
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Chybová stránka**. Tato stránka se zobrazí, pokud dojde k výjimce nebo chybě. |
| *api.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Stránka výběru zprostředkovatele identity** Tato stránka obsahuje seznam zprostředkovatelů identity, ze kterých si uživatelé můžou vybírat během přihlašování. Tyto možnosti jsou obvykle poskytovatelé podnikových identit, poskytovatelé sociálních identit, jako je Facebook, Google + nebo místní účty. |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Výběr zprostředkovatele identity pro registraci**. Tato stránka obsahuje seznam zprostředkovatelů identity, ze kterých si uživatelé můžou vybírat během registrace. Možnosti jsou buď poskytovatelé podnikových identit, zprostředkovatelé sociálních identit, jako je Facebook, Google + nebo místní účty. |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Stránka zapomenuté heslo** Tato stránka obsahuje formulář, který musí uživatelé dokončit pro zahájení resetování hesla.  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Přihlašovací stránka místního účtu** Tato stránka obsahuje formulář pro přihlášení pomocí místního účtu, který je založen na e-mailové adrese nebo uživatelském jménu. Formulář může obsahovat textové pole pro zadání textu a heslo. |
| *api.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Přihlašovací stránka místního účtu** Tato stránka obsahuje formulář pro registraci místního účtu, který vychází z e-mailové adresy nebo uživatelského jména. Formulář může obsahovat různé vstupní ovládací prvky, jako je například textové pole pro zadání hesla, pole pro zadávání hesla, přepínač, rozevírací seznamy s jedním výběrem a zaškrtávací políčka vícenásobného výběru. |
| *api.phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Stránku Multi-Factor Authentication**. Na této stránce můžou uživatelé při registraci nebo přihlašování ověřit svoje telefonní čísla (pomocí textu nebo hlasu). |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Stránka pro registraci účtu sociální**sítě Tato stránka obsahuje formulář, který uživatelé musí dokončit při registraci pomocí existujícího účtu od poskytovatele sociálních identit. Tato stránka se podobá na předchozí stránce pro registraci účtu sociální sítě, s výjimkou polí zadání hesla. |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Stránka aktualizace profilu**. Tato stránka obsahuje formulář, ke kterému můžou uživatelé získat přístup, aby mohli aktualizovat svůj profil. Tato stránka se podobá stránce pro registraci účtu sociální sítě, s výjimkou polí zadání hesla. |
| *api.signuporsignin* | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Jednotná registrace nebo přihlašovací stránka**. Tato stránka zpracovává proces registrace a přihlášení uživatele. Uživatelé můžou používat podnikové zprostředkovatele identity, poskytovatele sociálních identit, jako je Facebook nebo Google +, nebo místní účty.  |

## <a name="serving-dynamic-content"></a>Obsluha dynamického obsahu
V článku [Konfigurace přizpůsobení uživatelského rozhraní ve vlastní zásadě](active-directory-b2c-ui-customization-custom.md) nahrajete soubory HTML5 do úložiště objektů BLOB v Azure. Tyto soubory HTML5 jsou statické a vykreslují pro každý požadavek stejný obsah HTML.

V tomto článku použijete webovou aplikaci ASP.NET, která může přijmout parametry řetězce dotazu a odpovídajícím způsobem reagovat.

V tomto návodu:
* Vytvořte ASP.NET Core webovou aplikaci, která je hostitelem šablon HTML5.
* Přidejte vlastní šablonu HTML5, _Unified. cshtml_.
* Publikujte webovou aplikaci na Azure App Service.
* Nastavte sdílení prostředků mezi zdroji (CORS) pro vaši webovou aplikaci.
* Přepište `LoadUri` prvky tak, aby odkazovaly na váš soubor HTML5.

## <a name="step-1-create-an-aspnet-web-app"></a>Krok 1: Vytvoření webové aplikace ASP.NET

1. V aplikaci Visual Studio vytvořte projekt tak, že vyberete **soubor** > **Nový** > **projekt**.

2. V okně **Nový projekt**  > vyberte **Visual C#**  **Web** > **ASP.NET Core Web Application (.NET Core)** .

3. Pojmenujte aplikaci (například *contoso. AADB2C. UI*) a pak vyberte **OK**.

    ![Vytvořit nový projekt sady Visual Studio](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4. Vyberte šablonu **webové aplikace** .

5. Nastavte ověřování na **bez ověřování**.

    ![Vybrat šablonu webové aplikace](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6. Vyberte **OK** pro vytvoření projektu.

## <a name="step-2-create-mvc-view"></a>Krok 2: Vytvoření zobrazení MVC
### <a name="step-21-download-the-b2c-built-in-html5-template"></a>Krok 2,1: Stažení předdefinované šablony HTML5 B2C
Vaše vlastní šablona HTML5 je založena na Azure AD B2C předdefinované šabloně HTML5. Můžete si stáhnout [soubor Unified. html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) nebo stáhnout šablonu z [Úvodní sady](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip). Tento soubor HTML5 použijete k vytvoření sjednocené přihlašovací stránky nebo přihlašovací stránky.

### <a name="step-22-add-the-mvc-view"></a>Krok 2,2: Přidat zobrazení MVC
1. Klikněte pravým tlačítkem na zobrazení/domovskou složku a pak **přidejte** > **novou položku**.

    ![Přidat položku nabídky nová položka v aplikaci Visual Studio](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)

2. V okně **Přidat novou položku – contoso. AADB2C. UI** vyberte **Web > ASP.NET**.

3. Vyberte **stránku zobrazení MVC**.

4. V poli **název** změňte název na **Unified. cshtml**.

5. Vyberte **Přidat**.

    ![Dialogové okno Přidat novou položku v aplikaci Visual Studio se zvýrazněnou stránkou zobrazení MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)

6. Pokud soubor *Unified. cshtml* ještě není otevřený, otevřete ho tak, že na něj dvakrát kliknete a pak jeho obsah vymažete.

7. Pro tento návod odebereme odkaz na stránku rozložení. Do _sjednoceného. cshtml_přidejte následující fragment kódu:

    ```csharp
    @{
        Layout = null;
    }
    ```

8. Otevřete soubor _Unified. cshtml_ , který jste stáhli z Azure AD B2C integrovanou šablonu HTML5.

9. Nahraďte znak @ u znaku (@) dvojitým znaménkem (@ @).

10. Zkopírujte obsah souboru a vložte jej pod definici rozložení. Váš kód by měl vypadat takto:

    ![Sjednocený soubor. cshtml po přidání jazyka HTML5](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)

### <a name="step-23-change-the-background-image"></a>Krok 2,3: Změna obrázku pozadí

`ID` `src` Vyhledejte prvek, který obsahuje hodnotu *background_background_image* **https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1** , a pak hodnotu nahraďte nebo jinou imagí pozadí, kterou chcete použít. `<img>`

![IMG – element s vlastní hodnotou background_background_image src](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)

### <a name="step-24-add-your-view-to-the-mvc-controller"></a>Krok 2,4: Přidat zobrazení do kontroleru MVC

1. Otevřete **souboru controllers\homecontroller.cs**a přidejte následující metodu:

    ```C
    public IActionResult unified()
    {
        return View();
    }
    ```
    Tento kód určuje, že metoda má použít soubor šablony *zobrazení* k vykreslení odpovědi do prohlížeče. Vzhledem k tomu, že explicitně neurčíte název souboru šablony *zobrazení* , MVC použije výchozí hodnotu pro použití sjednoceného souboru zobrazení _. cshtml_ ve složce */views/Home* .

    Když přidáte sjednocenou  metodu, váš kód by měl vypadat takto:

    ![Změna kontroleru pro vykreslení zobrazení](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

2. Proveďte ladění webové aplikace a ujistěte se, že je _sjednocená_ stránka přístupná (například `http://localhost:<Port number>/Home/unified`).

### <a name="step-25-publish-to-azure"></a>Krok 2,5: Publikování do Azure
1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt **contoso. AADB2C. UI** a pak vyberte **publikovat**.

    ![Publikování do Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2. Vyberte dlaždici **Microsoft Azure App Service** a pak vyberte **publikovat**.

    ![Vytvořit novou Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    Otevře se okno **vytvořit App Service** . V takovém případě můžete začít vytvářet všechny potřebné prostředky Azure ke spuštění webové aplikace v ASP.NET v Azure.

    > [!NOTE]
    > Další informace o publikování najdete v tématu [Vytvoření webové aplikace v ASP.NET v Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet).

3. Do pole **název webové aplikace** zadejte jedinečný název aplikace (platné znaky jsou a-z, a-z, 0-9 a spojovník (-). Adresa URL webové aplikace je `http://<app_name>.azurewebsites.NET`, kde `<app_name>` je název vaší webové aplikace. Můžete přijmout automaticky vygenerovaný název, který je jedinečný.

4. Výběrem možnosti **Vytvořit** spustíte vytváření prostředků Azure.

    ![Zadat App Service vlastnosti](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

    Po dokončení procesu vytváření publikuje Průvodce webovou aplikaci ASP.NET do Azure a pak aplikaci spustí ve výchozím prohlížeči.

5. Zkopírujte adresu URL _sjednocené_ stránky (například _https://< APP_NAME >. NET/Home/Unified_).

## <a name="step-3-configure-cors-in-azure-app-service"></a>Krok 3: Konfigurace CORS v Azure App Service
1. V [Azure Portal](https://portal.azure.com/)vyberte **App Services**a pak vyberte název vaší aplikace API.

    ![V Azure Portal vyberte aplikace API.](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

2. V části **Nastavení** v části **rozhraní API** vyberte **CORS**.

    ![Položka nabídky CORS zvýrazněná v nabídce App Service v Azure Portal](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

3. V okně **CORS** v poli **Povolené zdroje** proveďte jednu z následujících akcí:

    * Zadejte adresu URL nebo adresy URL, ze kterých mají být povolena volání JavaScriptu. V adresách URL, které zadáte, je třeba použít všechna malá písmena.
    * Zadejte hvězdičku (*), chcete-li určit, zda jsou všechny zdrojové domény přijímány.

4. Vyberte **Uložit**.

    ![Stránka nastavení CORS se zvýrazněnou hvězdičkou v povolených zdrojích](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

    Po výběru **Uložit**aplikace API přijme volání JavaScriptu ze zadaných adres URL.

## <a name="step-4-html5-template-validation"></a>Krok 4: Ověření šablony HTML5
Vaše šablona HTML5 je připravena k použití. V `ContentDefinition` kódu ale není k dispozici. Než budete moct přidat `ContentDefinition` vlastní zásady, ujistěte se, že:
* Váš obsah je kompatibilní s HTML5 a je přístupný.
* Pro CORS je povolený váš server obsahu.

    >[!NOTE]
    >Pokud chcete ověřit, jestli lokalita, na které hostuje váš obsah, má povolený CORS a může testovat žádosti CORS, navštivte web [test-CORS.org](https://test-cors.org/) .

* Poskytovaný obsah je zabezpečený přes **protokol HTTPS**.
* Používáte *absolutní adresy URL*, například `https://yourdomain/content`, pro všechny odkazy, obsah CSS a obrázky.

## <a name="step-5-configure-your-content-definition"></a>Krok 5: Konfigurace definice obsahu
Pro konfiguraci `ContentDefinition`proveďte následující kroky:
1. Otevřete základní soubor zásad (například *TrustFrameworkBase. XML*).

2. Vyhledejte prvek a potom zkopírujte celý obsah `<ContentDefinitions>` uzlu. `<ContentDefinitions>`

3. Otevřete soubor rozšíření (například *TrustFrameworkExtensions. XML*) a vyhledejte `<BuildingBlocks>` element. Pokud element neexistuje, přidejte jej.

4. Vložte celý obsah `<ContentDefinitions>` uzlu, který jste zkopírovali jako podřízený `<BuildingBlocks>` prvek elementu.

5. Vyhledejte uzel, který obsahuje `Id="api.signuporsignin"` soubor ve formátu XML, který jste zkopírovali. `<ContentDefinition>`

6. Změňte hodnotu `LoadUri` z _~/tenant/default/Unified_ na _https://< APP_NAME >. azurewebsites. NET/Home/Unified_.
    Vaše vlastní zásada by měla vypadat takto:

    ![Příklad fragmentu XML s zvýrazněným elementem LoadUri](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Krok 6: Nahrajte zásady do svého tenanta.
1. V [Azure Portal](https://portal.azure.com)přepněte do [kontextu vašeho tenanta Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)a pak vyberte **Azure AD B2C**.

2. Vyberte **architekturu prostředí identity**.

3. Vyberte **všechny zásady**.

4. Vyberte **Odeslat zásadu**.

5. Zaškrtněte políčko **přepsat zásadu, pokud existuje** .

6. Nahrajte soubor *TrustFrameworkExtensions. XML* a ujistěte se, že projde ověřením.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Krok 7: Otestujte vlastní zásady pomocí rutiny spustit hned
1. Vyberte **nastavení Azure AD B2C**a pak vyberte **Architektura prostředí identity**.

    >[!NOTE]
    >Spustit teď vyžaduje, aby se v tenantovi předem zaregistrovala aspoň jedna aplikace. Informace o tom, jak zaregistrovat aplikace, najdete v článku Azure AD B2C [Začínáme](active-directory-b2c-get-started.md) nebo v článku věnovaném [registraci aplikace](active-directory-b2c-app-registration.md) .

2. Otevřete **B2C_1A_signup_signin**, vlastní zásady předávající strany (RP), které jste nahráli, a pak vyberte **Spustit nyní**.
    Měli byste být schopni zobrazit vlastní HTML5 pomocí pozadí, které jste vytvořili dříve.

    ![Vaše zásady registrace nebo přihlašování](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-add-dynamic-content"></a>Krok 8: Přidat dynamický obsah
Změňte pozadí na základě parametru řetězce dotazu s názvem _campaignId_. Vaše aplikace RP (webové a mobilní aplikace) odešle parametr do Azure AD B2C. Vaše zásada přečte parametr a pošle jeho hodnotu do šablony HTML5.

### <a name="step-81-add-a-content-definition-parameter"></a>Krok 8,1: Přidat parametr definice obsahu

`ContentDefinitionParameters` Přidejte element pomocí následujícího postupu:
1. Otevřete soubor *SignUpOrSignin* vaší zásady (například *SignUpOrSignin. XML*).

2. Pod uzlem přidejte `UserJourneyBehaviors`uzel: `<DefaultUserJourney>`

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <UserJourneyBehaviors>
        <ContentDefinitionParameters>
          <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        </ContentDefinitionParameters>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```

### <a name="step-82-change-your-code-to-accept-a-query-string-parameter-and-replace-the-background-image"></a>Krok 8,2: Změňte kód tak, aby přijímal parametr řetězce dotazu a nahradil obrázek pozadí.
Úpravou metody `unified` HomeController přijměte parametr campaignId. Metoda pak zkontroluje hodnotu parametru a odpovídajícím způsobem nastaví `ViewData["background"]` proměnnou.

1. Otevřete soubor *souboru controllers\homecontroller.cs* a pak změňte `unified` metodu přidáním následujícího fragmentu kódu:

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

2. Vyhledejte element s ID `background_background_image` `src` a nahraďte hodnotu hodnotou. `@ViewData["background"]` `<img>`

    ![IMG – element s zvýrazněnou hodnotou src ](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>8,3: Nahrání změn a publikování zásad
1. Publikujte projekt sady Visual Studio na Azure App Service.

2. Nahrajte do Azure AD B2C zásadu *SignUpOrSignin. XML* .

3. Otevřete **B2C_1A_signup_signin**, vlastní zásady RP, které jste nahráli, a pak vyberte **Spustit nyní**.
    Měli byste vidět stejný obrázek na pozadí, který se dřív zobrazil.

4. Zkopírujte adresu URL z panelu Adresa v prohlížeči.

5. Přidejte parametr řetězce dotazu _campaignId_ k identifikátoru URI. Například přidejte `&campaignId=hawaii`, jak je znázorněno na následujícím obrázku:

    ![Identifikátor URI s vybraným parametrem řetězce dotazu campaignId](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)

6. Pokud chcete zobrazit obrázek na pozadí Havajské ostrovy, vyberte **ENTER** .

    ![Přihlašovací stránka pro přihlášení s vlastním pozadím ve havajské imagi](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)

7. Změňte hodnotu na *Tokio*a pak vyberte **ENTER**.
    V prohlížeči se zobrazí pozadí v Brně.

    ![Přihlašovací stránka pro přihlášení s vlastním pozadím v neřízeném obrázku](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>Krok 9: Změna zbývající části cesty uživatele
Pokud vyberete odkaz **zaregistrovat se nyní** na přihlašovací stránce, prohlížeč zobrazí výchozí obrázek pozadí, nikoli obrázek, který jste definovali. K tomuto chování dojde, protože jste změnili pouze přihlašovací stránku nebo přihlašovací stránku. Změna zbytku definic obsahu s vlastním kontrolním výrazem:
1. Vraťte se zpět na krok 2 a udělejte toto:

    a. Stáhněte si soubor *selfasserted* .

    b. Zkopírujte obsah souboru.

    c. Vytvořte nové zobrazení *selfasserted*.

    d. Přidejte *selfasserted* do **domovského** kontroleru.

2. Vraťte se zpět na krok 4 a postupujte takto:

    a. V zásadách rozšíření `<ContentDefinition>` Najděte uzel, který obsahuje `Id="api.selfasserted"`, `Id="api.localaccountsignup"`a `Id="api.localaccountpasswordreset"`.

    b. Nastavte atribut na identifikátor URI *selfasserted.* `LoadUri`

3. Vraťte se zpět na krok "Step 8,2" a změňte kód tak, aby přijímal parametry řetězce dotazu, ale tentokrát do funkce *selfasserted* .

4. Nahrajte zásady *TrustFrameworkExtensions. XML* a zajistěte, aby prošly ověřením.

5. Spusťte test zásad a kliknutím na tlačítko **zaregistrovat** se zobrazí výsledek.

## <a name="optional-download-the-complete-policy-files-and-code"></a>Volitelné Stažení úplných souborů a kódu zásad
* Po dokončení návodu [Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md) doporučujeme sestavit svůj scénář pomocí vlastních souborů zásad. Pro váš odkaz jsme zadali [ukázkové soubory zásad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).
* Můžete si stáhnout kompletní kód z [ukázkového řešení sady Visual Studio pro referenci](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).




