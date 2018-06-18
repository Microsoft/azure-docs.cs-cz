---
title: Přizpůsobit Azure Active Directory B2C uživatelské rozhraní (UI) dynamicky pomocí vlastních zásad | Microsoft Docs
description: Podpora více značky prostředí s obsahem HTML5 nebo šablon stylů CSS, který změní dynamicky za běhu.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 09/20/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: d01f7b884e03bb311350c508dc46f699cd6717c0
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34709338"
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-by-using-custom-policies"></a>Azure Active Directory B2C: Konfigurace uživatelského rozhraní s dynamickým obsahem pomocí vlastních zásad

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Pomocí Azure Active Directory B2C (Azure AD B2C) vlastní zásady, můžete odeslat parametr v řetězci dotazu. Pomocí předání parametru váš koncový bod HTML, můžete dynamicky měnit obsah stránky. Například můžete změnit obrázek pozadí na Azure AD B2C registrace nebo přihlášení stránky, na základě parametr, který můžete předat z webu nebo mobilních aplikací. 

## <a name="prerequisites"></a>Požadavky
Tento článek zaměřuje na tom, jak přizpůsobit uživatelské rozhraní Azure AD B2C s *dynamický obsah* pomocí vlastních zásad. Abyste mohli začít, najdete v části [přizpůsobení uživatelského rozhraní ve vlastních zásadách pro](active-directory-b2c-ui-customization-custom.md). 

>[!NOTE]
>V článku Azure AD B2C [přizpůsobení konfigurace uživatelského rozhraní ve vlastních zásadách pro](active-directory-b2c-ui-customization-custom.md), popisuje základní následující informace:
> * Funkce přizpůsobení stránce uživatelské rozhraní (UI).
> * Základní nástroje pro testování funkce přizpůsobení uživatelského rozhraní stránky pomocí našich ukázkový obsah.
> * Zadejte základní prvky uživatelského rozhraní každé stránky.
> * Osvědčené postupy pro použití této funkce.

## <a name="add-a-link-to-html5css-templates-to-your-user-journey"></a>Přidat odkaz na HTML5 nebo šablon stylů CSS šablony vám dobře slouží uživatele

Ve vlastních zásadách definuje definici obsahu stránce HTML5 identifikátor URI, který slouží ke konkrétnímu kroku uživatelského rozhraní (například stránky přihlášení nebo registraci). Základní zásada definuje výchozí vzhledu a chování tak, že odkazuje na URI HTML5 souborů (v CSS). V případě rozšíření zásad můžete upravit vzhled a chování přepsáním LoadUri souboru HTML5. Obsahu definice obsahovat adresy URL na externí obsah, který je definován tím, že vytvoří soubory HTML5 nebo šablon stylů CSS, podle potřeby. 

`ContentDefinitions` Část obsahuje řadu `ContentDefinition` elementů XML. Atribut ID `ContentDefinition` element určuje typ stránky, která má vztah k definici obsahu. To znamená že element definují kontext, který se bude použít vlastní šablonu HTML5 nebo šablon stylů CSS. Následující tabulka popisuje sadu obsahu definice ID, které jsou rozpoznány modul IEF a typy stránek, které se vztahují k nim.

| ID obsahu definice | Výchozí HTML5 šablony| Popis | 
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Chybové stránky**. Tato stránka se zobrazí, když je došlo k výjimce nebo došlo k chybě. |
| *API.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Stránka Výběr zprostředkovatele identity**. Tato stránka obsahuje seznam zprostředkovatelů identity, které uživatelé mohou vybírat z během přihlášení. Možnosti jsou obvykle poskytovatelů identit enterprise, poskytovatelů identit sociálních třeba Facebook a Google + nebo místní účty. |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Výběr zprostředkovatele identity pro registraci**. Tato stránka obsahuje seznam zprostředkovatelů identity, které uživatelé mohou vybírat z během registrace. Možnosti jsou poskytovatelů identit enterprise, poskytovatelů identit sociálních třeba Facebook a Google + nebo místním účtům. |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Zapomněli jste heslo**. Tato stránka obsahuje formulář, který uživatelé musí dokončit zahájíte resetování hesla.  |
| *API.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Přihlašovací stránka místní účet**. Tato stránka obsahuje formulář pro přihlašování pomocí místního účtu, který je založený na e-mailovou adresu nebo uživatelské jméno. Formulář může obsahovat vstupní textové pole a pole pro zadání hesla. |
| *API.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Místní účet registrační stránku**. Tato stránka obsahuje formulář pro registraci pro místní účet, který je založený na e-mailovou adresu nebo uživatelské jméno. Formulář může obsahovat různé vstupní ovládací prvky, jako například: textový vstupní pole, pole pro zadání hesla, přepínače, polí rozevíracího seznamu vyberte jeden a více zaškrtněte políčka. |
| *api.phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Stránka služby Multi-Factor authentication**. Na této stránce uživatelé mohli ověřit jejich telefonní čísla (pomocí textové nebo hlasové) během registrace nebo přihlášení. |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Stránku pro přihlášení sociálních účet**. Tato stránka obsahuje formulář, který uživatelé musí dokončit při registraci pomocí stávající účet ze sociálních identity zprostředkovatele. Tato stránka je podobný na předchozí sociálních registrační stránku účtu, s výjimkou pole pro zadání hesla. |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Stránka pro aktualizaci profilu**. Tato stránka obsahuje formulář, který mohou uživatelé aktualizovat svůj profil. Tato stránka je podobná registrační stránku sociálních účtu, s výjimkou pole pro zadání hesla. |
| *api.signuporsignin* | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Jednotná stránku registrace nebo přihlášení**. Tato stránka zpracovává proces registrace a přihlášení uživatele. Uživatelé mohou pomocí poskytovatelů identit enterprise, poskytovatelů identit sociálních třeba Facebook nebo Google + nebo místní účty.  |

## <a name="serving-dynamic-content"></a>Poskytování dynamického obsahu
V [přizpůsobení konfigurace uživatelského rozhraní ve vlastních zásadách pro](active-directory-b2c-ui-customization-custom.md) článek, odešle HTML5 soubory do úložiště objektů Blob v Azure. Tyto soubory HTML5 statické a vykreslení stejné HTML obsahu pro každý požadavek. 

V tomto článku použijte webové aplikace ASP.NET, který může přijmout parametrů řetězce dotazu a reagují odpovídajícím způsobem. 

V tomto návodu můžete:
* Vytvořte webovou aplikaci ASP.NET Core, který je hostitelem vaší šablony HTML5. 
* Přidat vlastní šablonu HTML5 _unified.cshtml_. 
* Publikování webové aplikace do služby Azure App Service. 
* Nastavit prostředků mezi zdroji (CORS) pro webovou aplikaci pro sdílení.
* Přepsání `LoadUri` elementy tak, aby odkazovaly do souboru HTML5.

## <a name="step-1-create-an-aspnet-web-app"></a>Krok 1: Vytvoření webové aplikace ASP.NET

1. V sadě Visual Studio vytvořte projekt tak, že vyberete **soubor** > **nový** > **projektu**.

2. V **nový projekt** vyberte **Visual C#** > **webové** > **webové aplikace ASP.NET Core (.NET Core)**.

3. Název aplikace (například *Contoso.AADB2C.UI*) a potom vyberte **OK**.

    ![Vytvoření nového projektu sady Visual Studio](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4. Vyberte **webové aplikace** šablony.

5. Nastavení ověřování **bez ověřování**.

    ![Vyberte šablonu webové aplikace](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6. Vyberte **OK** a vytvořte tak projekt.

## <a name="step-2-create-mvc-view"></a>Krok 2: Vytvoření zobrazení MVC
### <a name="step-21-download-the-b2c-built-in-html5-template"></a>Krok 2.1: Stáhněte si předdefinované šablony HTML5 B2C
Vlastní šablony HTML5 je založený na šabloně předdefinované HTML5 Azure AD B2C. Si můžete stáhnout [unified.html soubor](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) nebo stáhnout šablonu z [starter pack](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip). Tento soubor HTML5 použijete k vytvoření jednotné stránku registrace nebo přihlášení.

### <a name="step-22-add-the-mvc-view"></a>Krok 2.2: Přidejte zobrazení MVC
1. Klikněte pravým tlačítkem na zobrazení/domovskou složku a potom **přidat** > **novou položku**.

    ![Přidat novou položku MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)

2. V **přidat novou položku – Contoso.AADB2C.UI** vyberte **Web > ASP.NET**.

3. Vyberte **MVC zobrazení stránky**.

4. V **název** pole, změňte název **unified.cshtml**.

5. Vyberte **Přidat**.

    ![Přidání zobrazení MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)

6. Pokud *unified.cshtml* soubor již není otevřený, poklikejte na soubor a otevře se a pak vymazat obsah souboru.

7. V tomto návodu budeme odeberte odkaz na stránce rozložení. Přidejte následující fragment kódu do _unified.cshtml_:

    ```csharp
    @{
        Layout = null;
    }
    ```

8. Otevřete _unified.cshtml_ soubor stažený z Azure AD B2C předdefinované šablony HTML5.

9. Nahraďte jeden znak (@) s dvojitou znak @@.

10. Obsah souboru zkopírujte a vložte ji pod definici rozložení. Váš kód by měl vypadat podobně jako:

    ![Po přidání HTML5 Unified.cshtml soubor](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)

### <a name="step-23-change-the-background-image"></a>Krok 2.3: Změna rozložení obrázku na pozadí

Vyhledejte `<img>` elementu, který obsahuje `ID` hodnotu *background_background_image*a potom `src` hodnotu s **https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1** nebo libovolný jiný Obrázek pozadí, kterou chcete použít.

![Změní pozadí stránky](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)

### <a name="step-24-add-your-view-to-the-mvc-controller"></a>Krok 2.4: Přidáte zobrazení do řadiče MVC

1. Otevřete **Controllers\HomeController.cs**a přidejte následující metodu: 

    ```C
    public IActionResult unified()
    {
        return View();
    }
    ```
    Tento kód určuje, že by měl používat metodu *zobrazení* soubor šablony k vykreslení odpovědi do prohlížeče. Protože jsme explicitně nezadávejte název *zobrazení* soubor šablony MVC používá ve výchozím nastavení _unified.cshtml_ zobrazení souboru v *nebo zobrazení, domácí* složky.
    
    Po přidání _jednotná_ metoda, váš kód by měl vypadat jako:
    
    ![Změna řadiče k vykreslení zobrazení](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

2. Ladění webové aplikace a ujistěte se, že _jednotná_ stránka je přístupná (například `http://localhost:<Port number>/Home/unified`).

### <a name="step-25-publish-to-azure"></a>Krok 2.5: Publikování aplikace do Azure
1. V **Průzkumníku řešení**, klikněte pravým tlačítkem myši **Contoso.AADB2C.UI** projektu a potom vyberte **publikovat**.

    ![Publikování do služby Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2. Vyberte **Microsoft Azure App Service** dlaždici a potom vyberte **publikovat**.

    ![Vytvořit nový Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    **Vytvořit službu App Service** otevře se okno. V něm můžete začít vytvářet všechny potřebné prostředky Azure ke spouštění webové aplikace ASP.NET v Azure.

    > [!NOTE]
    > Další informace o publikování najdete v tématu [vytvoření webové aplikace ASP.NET v Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure).

3. V **název webové aplikace** zadejte jedinečným názvem aplikace (platné znaky jsou a – z, A-Z, 0 – 9 a pomlčku (-). Adresa URL webové aplikace je `http://<app_name>.azurewebsites.NET`, kde `<app_name>` je název vaší webové aplikace. Můžete přijmout automaticky vygenerovaný název, který je jedinečný.

4. Výběrem možnosti **Vytvořit** spustíte vytváření prostředků Azure.

    ![Zadejte vlastnosti služby App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

    Po dokončení procesu vytváření průvodce publikuje webové aplikace ASP.NET ve službě Azure a pak spustí aplikaci ve výchozím prohlížeči.

5. Zkopírujte adresu URL _jednotná_ stránky (například _https://<app_name>.azurewebsites.net/home/unified_).

## <a name="step-3-configure-cors-in-azure-app-service"></a>Krok 3: Konfigurace CORS v Azure App Service
1. V [portál Azure](https://portal.azure.com/), vyberte **App Services**a pak vyberte název aplikace API.

    ![Výběr aplikace API na portálu Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

2. V **nastavení** v oblasti **rozhraní API** vyberte **CORS**.

    ![Vyberte nastavení CORS](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

3. V **CORS** okno v **povolené zdroje** pole, proveďte jednu z následujících akcí:

    * Zadejte adresu nebo adresy URL, které chcete povolit volání JavaScriptu z.
    * Zadejte hvězdičku (*) Chcete-li povolit všechny zdrojové domény.

4. Vyberte **Uložit**.

    ![Okno CORS](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

    Po výběru **Uložit**, aplikace API přijímat volání JavaScriptu ze zadaných adres URL. 

## <a name="step-4-html5-template-validation"></a>Krok 4: Ověření šablony HTML5
Šablona HTML5 je připravený k použití. Však není k dispozici v `ContentDefinition` kódu. Chcete-li přidat `ContentDefinition` vlastní zásady, ověřte, že:
* Obsah je HTML5 kompatibilní a přístupné.
* Vaše servery obsahu je povolený pro CORS.

    >[!NOTE]
    >Ověřte, zda má povolenou CORS lokality, kde jste hostování obsahu a můžete otestovat požadavků CORS, přejděte na [test cors.org](http://test-cors.org/) webu. 

* Obsloužit obsah je zabezpečené přes **HTTPS**.
* Používáte *absolutní adresy URL*, jako například *https://yourdomain/content*, pro všechny odkazy, obsah šablon stylů CSS a obrázků.

## <a name="step-5-configure-your-content-definition"></a>Krok 5: Konfigurace vašeho obsahu definice
Ke konfiguraci `ContentDefinition`, postupujte takto:
1. Otevřete soubor základní zásad (například *TrustFrameworkBase.xml*).

2. Vyhledejte `<ContentDefinitions>` elementu a poté zkopírujte celý obsah `<ContentDefinitions>` uzlu.

3. Otevřete soubor rozšíření (například *TrustFrameworkExtensions.xml*) a poté vyhledejte `<BuildingBlocks>` elementu. Pokud element neexistuje, přidejte ji.

4. Vložte celý obsah `<ContentDefinitions>` uzlu, který jste zkopírovali jako podřízenou `<BuildingBlocks>` elementu. 

5. Vyhledejte `<ContentDefinition>` uzlu, který obsahuje `Id="api.signuporsignin"` v souboru XML, který jste zkopírovali.

6. Změňte hodnotu `LoadUri` z _~/tenant/default/unified_ k _https://<app_name>.azurewebsites.net/home/unified_.  
    Vaše vlastní zásada by měl vypadat asi takto:
    
    ![Vaše obsahu definice](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Krok 6: Nahrajte zásady klienta
1. V [portál Azure](https://portal.azure.com), přepnout [kontextu klienta služby Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)a potom vyberte **Azure AD B2C**.

2. Vyberte **Identity rozhraní Framework**.

3. Vyberte **všechny zásady**.

4. Vyberte **nahrát zásady**.

5. Vyberte **přepsat zásady, pokud existuje** zaškrtávací políčko.

6. Nahrát *TrustFrameworkExtensions.xml* souboru a ujistěte se, že předává ověření.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Krok 7: Testování spustit pomocí vlastních zásad
1. Vyberte **nastavení Azure AD B2C**a potom vyberte **Identity rozhraní Framework**.

    >[!NOTE]
    >Spustit nyní vyžaduje alespoň jedné aplikace do být preregistered u klienta. Další postup registrace aplikace najdete v tématu Azure AD B2C [Začínáme](active-directory-b2c-get-started.md) článek nebo [registrace aplikace](active-directory-b2c-app-registration.md) článku.

2. Otevřete **B2C_1A_signup_signin**, předávající stranu vlastních zásad, které jste nahráli a potom vyberte **spustit nyní**.  
    Nyní byste měli mít v tématu vaše vlastní HTML5 s na pozadí, kterou jste vytvořili dříve.

    ![Vaše zásady registrace nebo přihlášení](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-add-dynamic-content"></a>Krok 8: Přidejte dynamický obsah
Změní pozadí podle parametru řetězce dotazu s názvem _campaignId_. Vaše aplikace RP (webových a mobilních aplikací) odešle parametr do Azure AD B2C. Vaše zásady čte parametr a jeho hodnotu do šablony HTML5. 

### <a name="step-81-add-a-content-definition-parameter"></a>Krok 8.1: Parametr obsahu definice přidat

Přidat `ContentDefinitionParameters` element provedením následujících akcí:
1. Otevřete *SignUpOrSignin* soubor zásad (například *SignUpOrSignin.xml*).

2. Vyhledejte `<DefaultUserJourney>` uzlu. 

3. V `<DefaultUserJourney>` uzlu, přidejte následující fragment kódu XML:  

    ```XML
    <UserJourneyBehaviors>
        <ContentDefinitionParameters>
            <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ```

### <a name="step-82-change-your-code-to-accept-a-query-string-parameter-and-replace-the-background-image"></a>Krok 8.2: Změnit kód tak, aby přijímal parametr řetězce dotazu a nahraďte obrázku pozadí
Změnit HomeController `unified` metoda tak, aby přijímal parametr campaignId. Parametr poté zkontroluje tato metoda je hodnota a nastaví `ViewData["background"]` proměnná odpovídajícím způsobem.

1. Otevřete *Controllers\HomeController.cs* souboru a poté změňte `unified` metoda přidáním následující fragment kódu:

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

2. Vyhledejte `<img>` element s ID `background_background_image`a nahraďte `src` hodnotu s `@ViewData["background"]`.

    ![Změní pozadí stránky](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>8.3: odešlete změny a publikování zásady
1. Publikování projektu sady Visual Studio do služby Azure App Service.

2. Nahrát *SignUpOrSignin.xml* zásad do Azure AD B2C.

3. Otevřete **B2C_1A_signup_signin**, RP vlastních zásad, které jste nahráli a potom vyberte **spustit nyní**.  
    Měli byste vidět stejné obrázku pozadí, která byla dříve zobrazena.

4. Zkopírujte adresu URL z panelu Adresa prohlížeče.

5. Přidat _campaignId_ parametr řetězce k identifikátoru URI dotazu. Například přidejte `&campaignId=hawaii`, jak je znázorněno na následujícím obrázku:

    ![Změní pozadí stránky](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)

6. Vyberte **Enter** zobrazíte Havaj obrázku pozadí.

    ![Změní pozadí stránky](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)

7. Změňte hodnotu na *Tokio*a potom vyberte **Enter**.  
    Prohlížeč zobrazí Tokio pozadí.

    ![Změní pozadí stránky](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>Krok 9: Změňte zbytek cesty uživatele
Pokud vyberete **nyní** odkaz na přihlašovací stránce v prohlížeči zobrazí výchozí obrázek pozadí, definované není obrázek. Toto chování nastane, protože jste změnili pouze stránku registrace nebo přihlášení. Chcete-li změnit zbytek samoobslužné Assert obsahu definice:
1. Přejděte zpět na krok 2 a proveďte následující kroky:

    a. Stažení *selfasserted* souboru.

    b. Zkopírujte obsah souboru.

    c. Vytvořit nové zobrazení, *selfasserted*.

    d. Přidat *selfasserted* k **Domů** řadiče.

2. Vraťte se zpátky a "Krok 4" a proveďte následující kroky: 

    a. V zásadách rozšíření najít `<ContentDefinition>` uzlu, který obsahuje `Id="api.selfasserted"`, `Id="api.localaccountsignup"`, a `Id="api.localaccountpasswordreset"`.

    b. Nastavte `LoadUri` atribut vaše *selfasserted* identifikátor URI.

3. Vraťte se zpátky a "Krok 8.2" a změnit kód tak, aby přijímal parametrů řetězce dotazu, ale této doby *selfasserted* funkce. 

4. Nahrát *TrustFrameworkExtensions.xml* zásady a ujistěte se, že předává ověření.

5. Spuštění testu zásady a pak vyberte **nyní** zobrazíte výsledek.

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Volitelné) Stáhnout soubory dokončení zásad a kódu
* Po dokončení [začít pracovat s vlastními zásadami](active-directory-b2c-get-started-custom.md) návod, doporučujeme vám vytvořit váš scénář pomocí vlastních zásad pro soubory. Pro vaši informaci uvádíme [ukázkové soubory zásad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).
* Si můžete stáhnout kompletní kód z [řešení sady Visual Studio ukázkový pro referenci](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).




