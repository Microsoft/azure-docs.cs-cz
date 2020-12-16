---
title: Přizpůsobení uživatelského rozhraní
titleSuffix: Azure AD B2C
description: Naučte se, jak přizpůsobit uživatelské rozhraní pro aplikace, které používají Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 520b2eff91faf2e333ab0a5df7bcc85e6a47c80a
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585186"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Přizpůsobení uživatelského rozhraní v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Branding a přizpůsobení uživatelského rozhraní, které Azure Active Directory B2C (Azure AD B2C) zobrazuje vašim zákazníkům, pomáhá zajistit bezproblémové uživatelské prostředí ve vaší aplikaci. Mezi tyto možnosti patří registrace, přihlašování, úpravy profilu a resetování hesla. Tento článek představuje metody přizpůsobení uživatelského rozhraní. 

> [!TIP]
> Chcete-li upravit pouze logo banner, obrázek pozadí a barvu pozadí stránek toku uživatele, můžete vyzkoušet funkci [Branding společnosti](company-branding.md) .

## <a name="custom-html-and-css-overview"></a>Vlastní HTML a CSS – přehled

Azure AD B2C spouští kód v prohlížeči zákazníka pomocí [sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/). V době běhu se obsah načte z adresy URL, kterou zadáte v toku uživatele nebo vlastní zásady. Každá stránka v uživatelském prostředí načte svůj obsah z adresy URL, kterou zadáte pro tuto stránku. Po načtení obsahu z vaší adresy URL se sloučí s fragmentem kódu HTML vloženým pomocí Azure AD B2C a potom se stránka zobrazí vašemu zákazníkovi.

![Okraj obsahu vlastní stránky](./media/customize-ui-with-html/html-content-merging.png)

### <a name="custom-html-page-content"></a>Vlastní obsah stránky HTML

Vytvořte stránku HTML s vlastní značkou, která bude sloužit k vlastnímu obsahu stránky. Tato stránka může být statickou `*.html` stránkou nebo dynamickou stránkou, jako je například .NET, Node.js nebo php.

Vlastní obsah stránky může obsahovat libovolné prvky HTML, včetně šablon stylů CSS a JavaScript, ale nemůže zahrnovat nezabezpečené prvky jako prvky IFrame. Jediným vyžadovaným prvkem je element div s nastavením na, jako je například ten `id` , který je `api` `<div id="api"></div>` v rámci stránky HTML.

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Product Brand Name</title>
</head>
<body>
    <div id="api"></div>
</body>
</html>
```

#### <a name="customize-the-default-azure-ad-b2c-pages"></a>Přizpůsobení výchozích Azure AD B2C stránek

Místo vytvoření vlastního obsahu stránky od začátku můžete přizpůsobit obsah výchozí stránky Azure AD B2C's.

Následující tabulka uvádí výchozí obsah stránky, který poskytuje Azure AD B2C. Stáhněte si soubory a používejte je jako výchozí bod pro vytváření vlastních stránek.

| Výchozí stránka | Popis | ID definice obsahu<br/>(jenom vlastní zásady) |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Chybová stránka**. Tato stránka se zobrazí, pokud dojde k výjimce nebo chybě. | *rozhraní API. Chyba* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Stránka s vlastním kontrolním** výrazem. Tento soubor použijte jako obsah vlastní stránky pro přihlašovací stránku účtu sociální sítě, přihlašovací stránku místního účtu, přihlašovací stránku místního účtu, resetování hesla a další. Formulář může obsahovat různé vstupní ovládací prvky, jako je například textové pole pro zadání hesla, pole pro zadávání hesla, přepínač, rozevírací seznamy s jedním výběrem a zaškrtávací políčka vícenásobného výběru. | *API. localaccountsignin*, *API. localaccountsignup*, *API. localaccountpasswordreset*, *API. selfasserted* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Stránku Multi-Factor Authentication**. Na této stránce můžou uživatelé při registraci nebo přihlašování ověřit svoje telefonní čísla (pomocí textu nebo hlasu). | *API. PhoneFactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Stránka aktualizace profilu**. Tato stránka obsahuje formulář, ke kterému můžou uživatelé získat přístup, aby mohli aktualizovat svůj profil. Tato stránka se podobá stránce pro registraci účtu sociální sítě, s výjimkou polí zadání hesla. | *API. selfasserted. profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Jednotná registrace nebo přihlašovací stránka**. Tato stránka zpracovává proces registrace a přihlášení uživatele. Uživatelé můžou používat podnikové zprostředkovatele identity, poskytovatele sociálních identit, jako je Facebook nebo Google +, nebo místní účty. | *API. signuporsignin* |

## <a name="hosting-the-page-content"></a>Hostování obsahu stránky

Při použití vlastních souborů HTML a CSS k přizpůsobení uživatelského rozhraní hostovat obsah svého uživatelského rozhraní na jakémkoli veřejně dostupném koncovém bodu HTTPS, který podporuje CORS. Například [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md), [Azure App Services](../app-service/index.yml), webové servery, sítě CDN, AWS S3 nebo systémy pro sdílení souborů.

## <a name="guidelines-for-using-custom-page-content"></a>Pokyny pro používání vlastního obsahu stránky

- Použijte absolutní adresu URL při zahrnutí externích prostředků, jako jsou multimédia, CSS a JavaScriptové soubory v souboru HTML.
- Pomocí [rozložení stránky verze](page-layout.md) 1.2.0 a vyšší můžete přidat `data-preload="true"` atribut do značek HTML pro řízení pořadí načítání pro šablony stylů CSS a JavaScript. Pomocí nástroje `data-preload=true` je stránka vytvořena před zobrazením uživateli. Tento atribut pomáhá zabránit tomu, aby se stránka vyblikat tím, že předvede soubor CSS, aniž by byl uživatel zobrazen bez stylu HTML. Následující fragment kódu HTML ukazuje použití `data-preload` značky.
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- Doporučujeme začít s výchozím obsahem stránky a sestavit nad ním.
- JavaScript můžete [Zahrnout](javascript-and-page-layout.md) do vlastního obsahu.
- Podporované verze prohlížeče:
  - Internet Explorer 11, 10 a Microsoft Edge
  - Omezená podpora pro Internet Explorer 9 a 8
  - Google Chrome 42,0 a novější
  - Mozilla Firefox 38,0 a novější
  - Safari pro iOS a macOS, verze 12 a vyšší
- Z důvodu omezení zabezpečení Azure AD B2C nepodporují `frame` , `iframe` nebo `form` prvky HTML.

## <a name="localize-content"></a>Lokalizace obsahu

Obsah HTML můžete lokalizovat tím, že ve svém tenantovi Azure AD B2C povolíte [vlastní nastavení jazyka](language-customization.md) . Povolením této funkce umožníte Azure AD B2C přeposlání parametru OpenID Connect `ui_locales` do svého koncového bodu. Server obsahu může tento parametr použít k poskytování HTML stránek specifických pro jazyk.

Obsah lze z různých míst načíst na základě používaného národního prostředí. V rámci koncového bodu s povoleným CORS nastavíte strukturu složek na hostování obsahu pro konkrétní jazyky. Pokud použijete zástupnou hodnotu, zavoláte tu správnou `{Culture:RFC5646}` .

Identifikátor URI vlastní stránky může vypadat například takto:

```http
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

Stránku v francouzštině můžete načíst tak, že nahrajete obsah z:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="custom-page-content-walkthrough"></a>Návod k obsahu vlastní stránky

Tady je přehled tohoto procesu:

1. Příprava umístění pro hostování obsahu vlastní stránky (veřejně přístupného koncového bodu HTTPS s povoleným CORS).
1. Stažení a přizpůsobení výchozího souboru obsahu stránky, například `unified.html` .
1. Publikujte vlastní obsah stránky, který je veřejně dostupným koncovým bodem HTTPS.
1. Nastavte sdílení prostředků mezi zdroji (CORS) pro vaši webovou aplikaci.
1. Nasměrujte zásady na svůj vlastní identifikátor URI obsahu zásad.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

### <a name="1-create-your-html-content"></a>1. vytvoření obsahu HTML

V nadpisu vytvořte vlastní obsah stránky s názvem značky vašeho produktu.

1. Zkopírujte následující fragment kódu HTML. Je ve správném formátu HTML5 s prázdným elementem, který se *\<div id="api"\>\</div\>* nachází v rámci *\<body\>* značek. Tento prvek indikuje, kam se má vložit Azure AD B2C obsah.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. Vložení zkopírovaného fragmentu v textovém editoru
1. Pomocí CSS můžete naformátovat prvky uživatelského rozhraní, které Azure AD B2C vloží do stránky. Následující příklad ukazuje jednoduchý soubor CSS, který obsahuje také nastavení pro vložené prvky HTML:

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

1.  Uložte soubor jako *customize-ui.html*.

> [!NOTE]
> Prvky formuláře HTML budou odebrány z důvodu omezení zabezpečení, pokud použijete login.microsoftonline.com. Pokud chcete použít prvky formuláře HTML ve vlastním obsahu HTML, [použijte b2clogin.com](b2clogin.md).

### <a name="2-create-an-azure-blob-storage-account"></a>2. vytvoření účtu služby Azure Blob Storage

V tomto článku používáme pro hostování našeho obsahu službu Azure Blob Storage. Svůj obsah můžete hostovat na webovém serveru, ale musíte [na svém webovém serveru povolit CORS](https://enable-cors.org/server.html).

Chcete-li hostovat obsah HTML v úložišti objektů blob, proveďte následující kroky:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V nabídce **centra** vyberte **Nový**  >  **úložiště**  >  **účet** úložiště.
1. Vyberte **předplatné** pro účet úložiště.
1. Vytvořte **skupinu prostředků** nebo vyberte existující.
1. Zadejte jedinečný **název** účtu úložiště.
1. Vyberte **zeměpisnou polohu** svého účtu úložiště.
1. **Model nasazení** může zůstat **Správce prostředků**.
1. **Výkon** může zůstat v **úrovni Standard**.
1. Změňte **druh účtu** na **úložiště objektů BLOB**.
1. **Replikace** může zůstat v **RA-GRS**.
1. **Úroveň přístupu** může zůstat v **chodu**.
1. Vyberte **zkontrolovat + vytvořit** a vytvořte účet úložiště.
    Po dokončení nasazení se automaticky otevře stránka **účet úložiště** .

#### <a name="21-create-a-container"></a>2,1 vytvoření kontejneru

Pokud chcete vytvořit veřejný kontejner ve službě BLOB Storage, proveďte následující kroky:

1. V části **BLOB Service** v nabídce na levé straně vyberte **objekty blob**.
1. Vyberte **+ kontejner**.
1. Jako **název** zadejte *root*. Název může být název, který si zvolíte, například *Contoso*, ale v tomto příkladu používáme pro jednoduchost v tomto příkladu *kořen* .
1. Jako **úroveň veřejného přístupu** vyberte **objekt BLOB** a pak klikněte na **OK**.
1. Vyberte **kořen** a otevřete tak nový kontejner.

#### <a name="22-upload-your-custom-page-content-files"></a>2,2 nahrání vlastních souborů obsahu stránky

1. Vyberte **Nahrát**.
1. Vyberte ikonu složky vedle **výběru souboru**.
1. Přejděte do části a vyberte **customize-ui.html**, kterou jste vytvořili dříve v oddílu přizpůsobení uživatelského rozhraní stránky.
1. Pokud chcete nahrávat do podsložky, rozbalte **Upřesnit** a zadejte název složky do **složky nahrát do složky**.
1. Vyberte **Nahrát**.
1. Vyberte objekt BLOB **customize-ui.html** , který jste nahráli.
1. Napravo od textového pole **Adresa URL** výběrem ikony **Kopírovat do schránky** zkopírujte adresu URL do schránky.
1. Ve webovém prohlížeči přejděte na adresu URL, kterou jste zkopírovali, a ověřte přístup k uloženému objektu BLOB. Pokud je nepřístupná, například pokud dojde k chybě, ujistěte se, `ResourceNotFound` že je typ přístupu kontejneru nastavený na **BLOB**.

### <a name="3-configure-cors"></a>3. konfigurace CORS

Pomocí následujících kroků nakonfigurujte úložiště objektů BLOB pro sdílení prostředků mezi zdroji:

1. V nabídce vyberte **CORS**.
1. V případě **povolených zdrojů** zadejte `https://your-tenant-name.b2clogin.com` . Nahraďte `your-tenant-name` názvem vašeho tenanta Azure AD B2C. Například `https://fabrikam.b2clogin.com`. Při zadávání názvu tenanta použijte všechna malá písmena.
1. U **povolených metod** vyberte obojí `GET` a `OPTIONS` .
1. U **povolených hlaviček** zadejte hvězdičku (*).
1. U **zveřejněných hlaviček** zadejte hvězdičku (*).
1. Do **maximálního stáří** zadejte 200.
1. Vyberte **Uložit**.

#### <a name="31-test-cors"></a>3,1 Test CORS

Ověřte, že jste připraveni, provedením následujících kroků:

1. Opakujte krok konfigurace CORS. U **povolených zdrojů** zadejte `https://www.test-cors.org`
1. Přejít na [www.test-CORS.org](https://www.test-cors.org/) 
1. Do pole **Vzdálená adresa URL** vložte adresu URL souboru HTML. Například `https://your-account.blob.core.windows.net/root/azure-ad-b2c/unified.html`.
1. Vyberte **Odeslat žádost**.
    Výsledek by měl být `XHR status: 200` . 
    Pokud se zobrazí chyba, ujistěte se, že je nastavení CORS správné. Je také možné, že budete muset vymazat mezipaměť prohlížeče nebo otevřít soukromou relaci procházení stisknutím kombinace kláves CTRL + SHIFT + P.

::: zone pivot="b2c-user-flow"

### <a name="4-update-the-user-flow"></a>4. aktualizace toku uživatele

1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **toky uživatelů** a potom vyberte tok uživatele *B2C_1_signupsignin1* .
1. Vyberte **rozložení stránky** a potom v části **jednotná registrace nebo přihlášení** klikněte na **Ano** , aby se **použil vlastní obsah stránky**.
1. Do pole **URI vlastní stránky** zadejte identifikátor URI pro soubor *custom-ui.html* , který jste si poznamenali dříve.
1. V horní části stránky vyberte **Save (Uložit**).

### <a name="5-test-the-user-flow"></a>5. testování toku uživatele

1. Ve vašem tenantovi Azure AD B2C vyberte **toky uživatelů** a vyberte *B2C_1_signupsignin1* tok uživatele.
1. V horní části stránky klikněte na **Spustit tok uživatele**.
1. Klikněte na tlačítko **Spustit tok uživatele** .

Měla by se zobrazit stránka podobná následujícímu příkladu s prvky na středovém souboru CSS, který jste vytvořili:

![Webový prohlížeč zobrazující přihlašovací stránku pro registraci nebo přihlášení s vlastními prvky uživatelského rozhraní](./media/customize-ui-with-html/run-now.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="4-modify-the-extensions-file"></a>4. Upravte soubor rozšíření.

Chcete-li nakonfigurovat přizpůsobení uživatelského rozhraní, zkopírujte **ContentDefinition** a jeho podřízené prvky ze základního souboru do souboru rozšíření.

1. Otevřete základní soubor zásad. Například <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em> . Tento základní soubor je jedním ze souborů zásad, které jsou součástí počáteční sady Custom Policy Pack, které byste měli mít k dispozici v rámci svých požadavků. Začněte [s vlastními zásadami](./custom-policy-get-started.md).
1. Vyhledejte a zkopírujte celý obsah elementu **ContentDefinitions** .
1. Otevřete soubor rozšíření. Například *TrustFrameworkExtensions.xml*. Vyhledejte element **BuildingBlocks** . Pokud element neexistuje, přidejte jej.
1. Vložte celý obsah elementu **ContentDefinitions** , který jste zkopírovali jako podřízený prvek **BuildingBlocks** elementu.
1. Vyhledejte element **ContentDefinition** , který obsahuje `Id="api.signuporsignin"` soubor ve formátu XML, který jste zkopírovali.
1. Změňte hodnotu **LoadUri** na adresu URL souboru HTML, který jste nahráli do úložiště. Například `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Vaše vlastní zásada by měla vypadat jako následující fragment kódu:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Uložte soubor rozšíření.

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. nahrávání a testování aktualizovaných vlastních zásad

#### <a name="51-upload-the-custom-policy"></a>5,1 nahrajte vlastní zásadu.

1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
1. Vyhledejte a vyberte **Azure AD B2C**.
1. V části **zásady** vyberte **Architektura prostředí identity**.
1. Vyberte **Odeslat vlastní zásadu**.
1. Nahrajte soubor rozšíření, který jste předtím změnili.

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5,2 otestujte vlastní zásady pomocí rutiny **spustit hned**

1. Vyberte zásady, které jste nahráli, a pak vyberte **Spustit nyní**.
1. Měli byste být schopni se zaregistrovat pomocí e-mailové adresy.

## <a name="configure-dynamic-custom-page-content-uri"></a>Konfigurovat dynamický identifikátor URI obsahu vlastní stránky

Pomocí Azure AD B2C vlastních zásad můžete odeslat parametr v cestě URL nebo v řetězci dotazu. Předáním parametru do vašeho koncového bodu HTML můžete dynamicky měnit obsah stránky. Můžete například změnit obrázek pozadí na registrační nebo přihlašovací stránce Azure AD B2C na základě parametru, který předáte z vašeho webu nebo mobilní aplikace. Parametr může být jakýkoli [překladač deklarací identity](claim-resolver-overview.md), jako je ID aplikace, ID jazyka nebo vlastní parametr řetězce dotazu, jako je například `campaignId` .

### <a name="sending-query-string-parameters"></a>Odesílají se parametry řetězce dotazu.

K odeslání parametrů řetězce dotazu v [zásadách předávající strany](relyingparty.md)přidejte `ContentDefinitionParameters` element, jak je znázorněno níže.

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        <Parameter Name="lang">{Culture:LanguageName}</Parameter>
        <Parameter Name="appId">{OIDC:ClientId}</Parameter>
    </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ...
</RelyingParty>
```

V definici obsahu změňte hodnotu `LoadUri` na `https://<app_name>.azurewebsites.net/home/unified` . Vaše vlastní zásada `ContentDefinition` by měla vypadat jako následující fragment kódu:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Když Azure AD B2C načte stránku, provede volání na koncový bod webového serveru:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>Identifikátor URI dynamického obsahu stránky

Obsah lze z různých míst načíst na základě použitých parametrů. V rámci koncového bodu s povoleným CORS nastavte strukturu složek pro hostování obsahu. Můžete například uspořádat obsah v následující struktuře. Kořenová *Složka/složka pro jednotlivé jazyky/soubory HTML* Identifikátor URI vlastní stránky může vypadat například takto:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C pošle 2. kód ISO pro jazyk, `fr` pro francouzštinu:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

::: zone-end

## <a name="sample-templates"></a>Ukázkové šablony

Ukázkové šablony pro přizpůsobení uživatelského rozhraní můžete najít tady:

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

Tento projekt obsahuje následující šablony:
- [Mořská modrá](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [Břidlicová šedá](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

Postup použití ukázky:

1. Naklonujte úložiště na místním počítači. Vyberte složku šablony `/ocean_blue` nebo `/slate_gray` .
1. Nahrajte všechny soubory ve složce šablony a `/assets` složce do úložiště objektů blob, jak je popsáno v předchozích částech.
1. Potom otevřete každý `\*.html` soubor v kořenovém adresáři `/ocean_blue` nebo `/slate_gray` nahraďte všechny výskyty relativních adres URL adresami URL souborů CSS, obrázků a písem, které jste nahráli v kroku 2. Příklad:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    Záměr
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Uložte `\*.html` soubory a nahrajte je do úložiště objektů BLOB.
1. Nyní upravte zásady tak, aby odkazovaly na soubor HTML, jak bylo uvedeno dříve.
1. Pokud se zobrazí chybějící písma, obrázky nebo šablony stylů CSS, zkontrolujte odkazy v zásadách rozšíření a v \* souborech. html.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak povolit [Kód JavaScriptu na straně klienta](javascript-and-page-layout.md).

