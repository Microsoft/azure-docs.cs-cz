---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/19/2020
ms.author: mimart
ms.openlocfilehash: af11283f9e9dbd925ec994dcb1d96393332b90fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117167"
---
## <a name="use-custom-page-content"></a>Použití vlastního obsahu stránky

Pomocí funkce přizpůsobení uživatelského rozhraní stránky můžete přizpůsobit vzhled a chování všech vlastních zásad. Můžete také udržovat konzistenci značky a vizuální konzistenci mezi vaší aplikací a službou Azure AD B2C.

### <a name="how-it-works"></a>Jak to funguje

Azure AD B2C spustí kód v prohlížeči zákazníka pomocí [sdílení prostředků mezi zdroji (CORS).](https://www.w3.org/TR/cors/) Za běhu se obsah načte z adresy URL, kterou zadáte v toku uživatele nebo vlastní chod zásad. Každá stránka v uživatelském prostředí načte svůj obsah z adresy URL, kterou pro danou stránku zadáte. Po načtení obsahu z adresy URL se sloučí s fragmentem HTML vloženým službou Azure AD B2C a stránka se zobrazí zákazníkovi.

![Vlastní okraj obsahu stránky](./media/active-directory-b2c-html-templates/html-content-merging.png)

## <a name="custom-html-page-content"></a>Vlastní obsah stránky HTML

Vytvořte stránku HTML s vlastní značkou, která bude zobrazovat vlastní obsah stránky. Tato stránka může `*.html` být statická stránka nebo dynamická stránka jako .NET, Node.js nebo PHP.

Vlastní obsah stránky může obsahovat libovolné prvky HTML, včetně CSS a JavaScriptu, ale nemůže obsahovat nezabezpečené prvky, jako jsou iframe. Jediným povinným prvkem je `id` prvek `api`div s nastaveným na , například `<div id="api"></div>` tento na stránce HTML.

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

### <a name="customize-the-default-azure-ad-b2c-pages"></a>Přizpůsobení výchozích stránek Azure AD B2C

Místo vytváření vlastního obsahu stránky od začátku můžete přizpůsobit výchozí obsah stránky Azure AD B2C.

V následující tabulce je uveden výchozí obsah stránky poskytovaný službou Azure AD B2C. Stáhněte si soubory a použijte je jako výchozí bod pro vytváření vlastních stránek.

| Výchozí stránka | Popis | ID definice obsahu<br/>(pouze vlastní zásady) |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Chybová stránka**. Tato stránka se zobrazí, když dojde k výjimce nebo chybě. | *api.chyba* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Self-tvrdil stránku**. Tento soubor použijte jako vlastní obsah stránky pro stránku registrace účtu na sociální síti, stránku registrace místního účtu, přihlašovací stránku místního účtu, resetování hesla a další. Formulář může obsahovat různé vstupní ovládací prvky, například: textové vstupní pole, pole pro zadání hesla, přepínací tlačítko, rozevírací políčka s jedním výběrem a vícenásobná zaškrtávací políčka. | *api.localaccountsignin*, *api.localaccountsignup*, *api.localaccountpasswordreset*, *api.selfasserted* |
| [multifaktor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Vícefaktorová stránka ověřování**. Na této stránce mohou uživatelé ověřit svá telefonní čísla (pomocí textu nebo hlasu) během registrace nebo přihlášení. | *api.phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Stránka aktualizace profilu**. Tato stránka obsahuje formulář, ke kterému mohou uživatelé přistupovat k aktualizaci svého profilu. Tato stránka je podobná stránce registrace účtu na sociální síti, s výjimkou polí pro zadání hesla. | *api.selfasserted.profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Sjednocená přihlašovací nebo přihlašovací stránka**. Tato stránka zpracovává proces registrace a přihlášení uživatele. Uživatelé mohou používat poskytovatele podnikových identit, poskytovatele sociálníidentity, jako je Facebook nebo Google+, nebo místní účty. | *api.signuporsignin* |

## <a name="hosting-the-page-content"></a>Hostování obsahu stránky

Při použití vlastních souborů HTML a CSS k přizpůsobení ui hostujte obsah ui na libovolném veřejně dostupném koncovém bodu HTTPS, který podporuje CORS. Například [azure blob úložiště](../articles/storage/blobs/storage-blobs-introduction.md), [Azure App Services](/azure/app-service/), webové servery, CDNs, AWS S3 nebo systémy pro sdílení souborů.

## <a name="guidelines-for-using-custom-page-content"></a>Pokyny pro použití vlastního obsahu stránky

- Absolutní adresa URL použijte, pokud do souboru HTML zahrnete externí zdroje, jako jsou média, CSS a soubory JavaScriptu.
- Pomocí [rozložení stránky verze](../articles/active-directory-b2c/page-layout.md) 1.2.0 a `data-preload="true"` vyšší můžete přidat atribut do značek HTML a řídit pořadí načítání pro CSS a JavaScript. Pomocí `data-preload=true`aplikace je stránka vytvořena před zobrazením uživateli. Tento atribut pomáhá zabránit "blikání" stránky přednačtením souboru CSS, aniž by se uživateli zobrazoval nestylizovaný kód HTML. Následující fragment kódu HTML ukazuje použití značky. `data-preload`
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- Doporučujeme začít s výchozím obsahem stránky a stavět na něm.
- JavaScript můžete zahrnout do vlastního obsahu jak pro [toky uživatelů,](../articles/active-directory-b2c/user-flow-javascript-overview.md) tak pro [vlastní zásady](../articles/active-directory-b2c/javascript-samples.md).
- Podporované verze prohlížeče jsou:
  - Internet Explorer 11, 10 a Microsoft Edge
  - Omezená podpora pro internet explorer 9 a 8
  - Google Chrome 42.0 a vyšší
  - Mozilla Firefox 38.0 a vyšší
  - Safari pro iOS a macOS, verze 12 a vyšší
- Z důvodu omezení zabezpečení Azure AD B2C nepodporuje `frame`prvky `iframe`, nebo `form` HTML.

## <a name="custom-page-content-walkthrough"></a>Návod k vlastnímu obsahu stránky

Zde je přehled procesu:

1. Připravte umístění pro hostování vlastního obsahu stránky (veřejně přístupný koncový bod HTTPS s podporou CORS).
1. Stáhněte a přizpůsobte například `unified.html`výchozí soubor obsahu stránky .
1. Publikujte vlastní obsah stránky, váš veřejně dostupný koncový bod HTTPS.
1. Nastavte sdílení prostředků mezi zdroji (CORS) pro webovou aplikaci.
1. Nasuďte zásadu na identifikátor URI obsahu vlastní zásady.

### <a name="1-create-your-html-content"></a>1. Vytvořte obsah HTML

Vytvořte vlastní obsah stránky se značkou produktu v názvu.

1. Zkopírujte následující úryvek HTML. Je to dobře formátovaný HTML5 s prázdným prvkem zvaným * \<div id="api"\>\</div\> * umístěný v * \<značkách těla.\> * Tento prvek označuje, kde má být vložen obsah Azure AD B2C.

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

1. Vložte zkopírovaný úryvek do textového editoru a uložte soubor jako *customize-ui.html*.

> [!NOTE]
> Prvky formuláře HTML budou odebrány z důvodu bezpečnostních omezení, pokud použijete login.microsoftonline.com. Pokud chcete ve vlastním obsahu HTML používat prvky formuláře HTML, [použijte b2clogin.com](../articles/active-directory-b2c/b2clogin.md).

### <a name="2-create-an-azure-blob-storage-account"></a>2. Vytvoření účtu úložiště objektů Blob Azure

V tomto článku používáme úložiště objektů blob Azure k hostování našeho obsahu. Můžete se rozhodnout hostovat obsah na webovém serveru, ale musíte [povolit cors na webovém serveru](https://enable-cors.org/server.html).

Chcete-li hostovat obsah HTML v úložišti objektů Blob, proveďte následující kroky:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V nabídce **Hub** vyberte **Nový** > **účet úložiště****úložiště** > .
1. Vyberte **předplatné** pro svůj účet úložiště.
1. Vytvořte **skupinu prostředků** nebo vyberte existující.
1. Zadejte jedinečný **název** svého účtu úložiště.
1. Vyberte **geografickou polohu** pro svůj účet úložiště.
1. **Model nasazení** může zůstat **Správce prostředků**.
1. **Výkon** může zůstat **standardní**.
1. Změnit **typ účtu** na úložiště objektů **Blob**.
1. **Replikace** může zůstat **RA-GRS**.
1. **Úroveň přístupu** může zůstat **hot**.
1. Vyberte **Zkontrolovat + vytvořit** a vytvořte účet úložiště.
    Po dokončení nasazení se automaticky otevře stránka **účtu úložiště.**

#### <a name="21-create-a-container"></a>2.1 Vytvoření kontejneru

Pokud chcete vytvořit veřejný kontejner v úložišti objektů Blob, proveďte následující kroky:

1. V části **Služba objektů blob** v levé nabídce vyberte **objekty blob .**
1. Vyberte **možnost +Kontejner**.
1. Do **pole Název**zadejte kořen *.* Název může být název podle vašeho výběru, například *contoso*, ale používáme *root* v tomto příkladu pro jednoduchost.
1. V **případě Úrovně veřejného přístupu**vyberte **objekt Blob**a potom **OK**.
1. Vyberte **kořen,** chcete-li otevřít nový kontejner.

#### <a name="22-upload-your-custom-page-content-files"></a>2.2 Nahrání vlastních souborů obsahu stránky

1. Vyberte **Nahrát**.
1. Vyberte ikonu složky vedle **položky Vybrat soubor**.
1. Přejděte na a vyberte **customize-ui.html**, který jste vytvořili dříve v části přizpůsobení uživatelského rozhraní stránky.
1. Pokud chcete nahrát do podsložky, rozbalte **upřesnit** a zadejte název složky **Do složky Upload**.
1. Vyberte **Nahrát**.
1. Vyberte objekt blob **customize-ui.html,** který jste nahráli.
1. Vpravo od textového pole **URL** vyberte ikonu **Kopírovat do schránky, chcete-li** adresu URL zkopírovat do schránky.
1. Ve webovém prohlížeči přejděte na zkopírovanou adresu URL a ověřte, zda je nahraný objekt blob přístupný. Pokud je nepřístupný, například pokud `ResourceNotFound` narazíte na chybu, ujistěte se, že typ přístupu kontejneru je nastavena na **objekt blob**.

### <a name="3-configure-cors"></a>3. Konfigurace CORS

Nakonfigurujte úložiště objektů Blob pro sdílení prostředků mezi zdroji provedením následujících kroků:

1. V nabídce vyberte **CORS**.
1. V **případě povoleného původu**zadejte `https://your-tenant-name.b2clogin.com`. Nahraďte `your-tenant-name` se názvem vašeho klienta Azure AD B2C. Například, `https://fabrikam.b2clogin.com`. Při zadávání jména klienta použijte všechna malá písmena.
1. V **případě povolených metod**vyberte obě `GET` položky a `OPTIONS`.
1. Do **pole Povolené hlavičky**zadejte hvězdičku (*).
1. Do **pole Exponovaná záhlaví**zadejte hvězdičku (*).
1. Maximální **věk**zadejte hodnotu 200.
1. Vyberte **Uložit**.

#### <a name="31-test-cors"></a>3.1 Zkušební CORS

Ověřte, zda jste připraveni, provedením následujících kroků:

1. Opakujte krok konfigurace CORS. Pro **povolený původ**uveďte`https://www.test-cors.org`
1. Přejděte na [www.test-cors.org](https://www.test-cors.org/) 
1. Do pole **Vzdálená adresa URL** vložte adresu URL souboru HTML. Například `https://your-account.blob.core.windows.net/azure-ad-b2c/unified.html`.
1. Vyberte **Odeslat požadavek**.
    Výsledkem by `XHR status: 200`měla být . 
    Pokud se zobrazí chyba, ujistěte se, že vaše nastavení CORS jsou správné. Možná také budete muset vymazat mezipaměť prohlížeče nebo otevřít relaci procházení v soukromí stisknutím kombinace kláves Ctrl+Shift+P.
