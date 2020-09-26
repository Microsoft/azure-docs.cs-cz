---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/19/2020
ms.author: mimart
ms.openlocfilehash: 9b660bf20c90a84780175e70573c96a0ce1b0b7d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377271"
---
## <a name="use-custom-page-content"></a>Použít vlastní obsah stránky

Pomocí funkce přizpůsobení uživatelského rozhraní stránky můžete přizpůsobit vzhled a chování libovolné vlastní zásady. Můžete také udržovat konzistenci značky a vizuální konzistenci mezi vaší aplikací a službou Azure AD B2C.

### <a name="how-it-works"></a>Jak to funguje

Azure AD B2C spouští kód v prohlížeči zákazníka pomocí [sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/). V době běhu se obsah načte z adresy URL, kterou zadáte v toku uživatele nebo vlastní zásady. Každá stránka v uživatelském prostředí načte svůj obsah z adresy URL, kterou zadáte pro tuto stránku. Po načtení obsahu z vaší adresy URL se sloučí s fragmentem kódu HTML vloženým pomocí Azure AD B2C a potom se stránka zobrazí vašemu zákazníkovi.

![Okraj obsahu vlastní stránky](./media/active-directory-b2c-html-templates/html-content-merging.png)

## <a name="custom-html-page-content"></a>Vlastní obsah stránky HTML

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

### <a name="customize-the-default-azure-ad-b2c-pages"></a>Přizpůsobení výchozích Azure AD B2C stránek

Místo vytvoření vlastního obsahu stránky od začátku můžete přizpůsobit obsah výchozí stránky Azure AD B2C's.

Následující tabulka uvádí výchozí obsah stránky, který poskytuje Azure AD B2C. Stáhněte si soubory a používejte je jako výchozí bod pro vytváření vlastních stránek.

| Výchozí stránka | Popis | ID definice obsahu<br/>(jenom vlastní zásady) |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Chybová stránka**. Tato stránka se zobrazí, pokud dojde k výjimce nebo chybě. | *rozhraní API. Chyba* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Stránka s vlastním kontrolním**výrazem. Tento soubor použijte jako obsah vlastní stránky pro přihlašovací stránku účtu sociální sítě, přihlašovací stránku místního účtu, přihlašovací stránku místního účtu, resetování hesla a další. Formulář může obsahovat různé vstupní ovládací prvky, jako je například textové pole pro zadání hesla, pole pro zadávání hesla, přepínač, rozevírací seznamy s jedním výběrem a zaškrtávací políčka vícenásobného výběru. | *API. localaccountsignin*, *API. localaccountsignup*, *API. localaccountpasswordreset*, *API. selfasserted* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Stránku Multi-Factor Authentication**. Na této stránce můžou uživatelé při registraci nebo přihlašování ověřit svoje telefonní čísla (pomocí textu nebo hlasu). | *API. PhoneFactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Stránka aktualizace profilu**. Tato stránka obsahuje formulář, ke kterému můžou uživatelé získat přístup, aby mohli aktualizovat svůj profil. Tato stránka se podobá stránce pro registraci účtu sociální sítě, s výjimkou polí zadání hesla. | *API. selfasserted. profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Jednotná registrace nebo přihlašovací stránka**. Tato stránka zpracovává proces registrace a přihlášení uživatele. Uživatelé můžou používat podnikové zprostředkovatele identity, poskytovatele sociálních identit, jako je Facebook nebo Google +, nebo místní účty. | *API. signuporsignin* |

## <a name="hosting-the-page-content"></a>Hostování obsahu stránky

Při použití vlastních souborů HTML a CSS k přizpůsobení uživatelského rozhraní hostovat obsah svého uživatelského rozhraní na jakémkoli veřejně dostupném koncovém bodu HTTPS, který podporuje CORS. Například [Azure Blob Storage](../articles/storage/blobs/storage-blobs-introduction.md), [Azure App Services](/azure/app-service/), webové servery, sítě CDN, AWS S3 nebo systémy pro sdílení souborů.

## <a name="guidelines-for-using-custom-page-content"></a>Pokyny pro používání vlastního obsahu stránky

- Použijte absolutní adresu URL při zahrnutí externích prostředků, jako jsou multimédia, CSS a JavaScriptové soubory v souboru HTML.
- Pomocí [rozložení stránky verze](../articles/active-directory-b2c/page-layout.md) 1.2.0 a vyšší můžete přidat `data-preload="true"` atribut do značek HTML pro řízení pořadí načítání pro šablony stylů CSS a JavaScript. Pomocí nástroje `data-preload=true` je stránka vytvořena před zobrazením uživateli. Tento atribut pomáhá zabránit tomu, aby se stránka vyblikat tím, že předvede soubor CSS, aniž by byl uživatel zobrazen bez stylu HTML. Následující fragment kódu HTML ukazuje použití `data-preload` značky.
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- Doporučujeme začít s výchozím obsahem stránky a sestavit nad ním.
- JavaScript můžete zahrnout do vlastního obsahu pro [uživatelské toky](../articles/active-directory-b2c/user-flow-javascript-overview.md) i pro [vlastní zásady](../articles/active-directory-b2c/javascript-samples.md).
- Podporované verze prohlížeče:
  - Internet Explorer 11, 10 a Microsoft Edge
  - Omezená podpora pro Internet Explorer 9 a 8
  - Google Chrome 42,0 a novější
  - Mozilla Firefox 38,0 a novější
  - Safari pro iOS a macOS, verze 12 a vyšší
- Z důvodu omezení zabezpečení Azure AD B2C nepodporují `frame` , `iframe` nebo `form` prvky HTML.

## <a name="custom-page-content-walkthrough"></a>Návod k obsahu vlastní stránky

Tady je přehled tohoto procesu:

1. Příprava umístění pro hostování obsahu vlastní stránky (veřejně přístupného koncového bodu HTTPS s povoleným CORS).
1. Stažení a přizpůsobení výchozího souboru obsahu stránky, například `unified.html` .
1. Publikujte vlastní obsah stránky, který je veřejně dostupným koncovým bodem HTTPS.
1. Nastavte sdílení prostředků mezi zdroji (CORS) pro vaši webovou aplikaci.
1. Nasměrujte zásady na svůj vlastní identifikátor URI obsahu zásad.

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

1. Vložte zkopírovaný fragment do textového editoru a pak soubor uložte jako *customize-ui.html*.

> [!NOTE]
> Prvky formuláře HTML budou odebrány z důvodu omezení zabezpečení, pokud použijete login.microsoftonline.com. Pokud chcete použít prvky formuláře HTML ve vlastním obsahu HTML, [použijte b2clogin.com](../articles/active-directory-b2c/b2clogin.md).

### <a name="2-create-an-azure-blob-storage-account"></a>2. vytvoření účtu služby Azure Blob Storage

V tomto článku používáme pro hostování našeho obsahu službu Azure Blob Storage. Svůj obsah můžete hostovat na webovém serveru, ale musíte [na svém webovém serveru povolit CORS](https://enable-cors.org/server.html).

Chcete-li hostovat obsah HTML v úložišti objektů blob, proveďte následující kroky:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. V nabídce **centra** vyberte **Nový**  >  **úložiště**  >  **účet**úložiště.
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
1. Jako **název**zadejte *root*. Název může být název, který si zvolíte, například *Contoso*, ale v tomto příkladu používáme pro jednoduchost v tomto příkladu *kořen* .
1. Jako **úroveň veřejného přístupu**vyberte **objekt BLOB**a pak klikněte na **OK**.
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
1. V případě **povolených zdrojů**zadejte `https://your-tenant-name.b2clogin.com` . Nahraďte `your-tenant-name` názvem vašeho tenanta Azure AD B2C. Například, `https://fabrikam.b2clogin.com`. Při zadávání názvu tenanta použijte všechna malá písmena.
1. U **povolených metod**vyberte obojí `GET` a `OPTIONS` .
1. U **povolených hlaviček**zadejte hvězdičku (*).
1. U **zveřejněných hlaviček**zadejte hvězdičku (*).
1. Do **maximálního stáří**zadejte 200.
1. Vyberte **Uložit**.

#### <a name="31-test-cors"></a>3,1 Test CORS

Ověřte, že jste připraveni, provedením následujících kroků:

1. Opakujte krok konfigurace CORS. U **povolených zdrojů**zadejte `https://www.test-cors.org`
1. Přejít na [www.test-CORS.org](https://www.test-cors.org/) 
1. Do pole **Vzdálená adresa URL** vložte adresu URL souboru HTML. Například `https://your-account.blob.core.windows.net/root/azure-ad-b2c/unified.html`.
1. Vyberte **Odeslat žádost**.
    Výsledek by měl být `XHR status: 200` . 
    Pokud se zobrazí chyba, ujistěte se, že je nastavení CORS správné. Je také možné, že budete muset vymazat mezipaměť prohlížeče nebo otevřít soukromou relaci procházení stisknutím kombinace kláves CTRL + SHIFT + P.
