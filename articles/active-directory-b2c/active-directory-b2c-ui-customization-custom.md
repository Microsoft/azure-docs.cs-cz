---
title: Přizpůsobení uživatelského rozhraní aplikace pomocí vlastní zásady v Azure Active Directory B2C | Microsoft Docs
description: Přečtěte si o přizpůsobení uživatelského rozhraní pomocí vlastních zásad v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e79d7a4b97f010b035f5c864682b4d3882a21393
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70171923"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Přizpůsobení uživatelského rozhraní aplikace pomocí vlastní zásady v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Po dokončení tohoto článku budete mít vlastní zásadu registrace a přihlašování s vaší značkou a vzhledem. Pomocí Azure Active Directory B2C (Azure AD B2C) získáte téměř úplné řízení obsahu HTML a CSS, který je prezentován uživatelům. Když použijete vlastní zásadu, nakonfigurujete přizpůsobení uživatelského rozhraní v XML namísto použití ovládacích prvků v Azure Portal. 

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v části Začínáme [s vlastními zásadami](active-directory-b2c-get-started-custom.md). Měli byste mít pracovní vlastní zásady pro registraci a přihlašování pomocí místních účtů.

## <a name="page-ui-customization"></a>Přizpůsobení uživatelského rozhraní stránky

Pomocí funkce přizpůsobení uživatelského rozhraní stránky můžete přizpůsobit vzhled a chování libovolné vlastní zásady. Můžete také udržovat konzistenci značky a vizuální konzistenci mezi vaší aplikací a službou Azure AD B2C.

Funguje to následovně: Azure AD B2C spouští kód v prohlížeči zákazníka a používá moderní přístup nazvaný [sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/). Nejdřív zadáte adresu URL do vlastních zásad s přizpůsobeným obsahem HTML. Azure AD B2C sloučí elementy uživatelského rozhraní s obsahem HTML, který je načtený z vaší adresy URL, a pak zobrazí stránku zákazníkovi.

## <a name="create-your-html5-content"></a>Vytvoření obsahu HTML5

V nadpisu Vytvořte obsah HTML s názvem značky vašeho produktu.

1. Zkopírujte následující fragment kódu HTML. Je ve správném formátu HTML5 s prázdným prvkem nazvaným  *\<div ID = "API"\>\<\> /div* umístěný v rámci *\<značek těla\>* . Tento prvek indikuje, kam se má vložit Azure AD B2C obsah.

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

2. Vložte zkopírovaný fragment do textového editoru a pak soubor uložte jako *Customize-UI. html*.

> [!NOTE]
> Prvky formuláře HTML budou odebrány z důvodu omezení zabezpečení, pokud použijete login.microsoftonline.com. Použijte b2clogin.com, pokud chcete použít prvky formuláře HTML ve vlastním obsahu HTML. Další výhody najdete v tématu [použití b2clogin.com](b2clogin.md) .

## <a name="create-an-azure-blob-storage-account"></a>Vytvoření účtu úložiště objektů Blob v Azure

>[!NOTE]
> V tomto článku používáme pro hostování našeho obsahu službu Azure Blob Storage. Svůj obsah můžete hostovat na webovém serveru, ale musíte [na svém webovém serveru povolit CORS](https://enable-cors.org/server.html).

Chcete-li tento obsah HTML hostovat ve službě BLOB Storage, postupujte následovně:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce **centra** vyberte **Nový** > **úložiště** > **účet**úložiště.
3. Vyberte **předplatné** pro účet úložiště.
4. Vytvořte **skupinu prostředků** nebo vyberte existující.
5. Zadejte jedinečný **název** účtu úložiště.
6. Vyberte **zeměpisnou polohu** svého účtu úložiště. 
7. **Model nasazení** může zůstat **Správce prostředků**.
8. **Výkon** může zůstat v **úrovni Standard**.
9. Změňte **druh účtu** na **úložiště objektů BLOB**.
10. **Replikace** může zůstat v **RA-GRS**.
11. **Úroveň přístupu** může zůstatv chodu. 
12. Kliknutím na tlačítko **zkontrolovat + vytvořit** vytvořte účet úložiště.  
    Po dokončení nasazení se automaticky otevře okno **účet úložiště** .

## <a name="create-a-container"></a>Vytvoření kontejneru

Pokud chcete vytvořit veřejný kontejner ve službě BLOB Storage, udělejte toto:

1. V části **BLOB Service** v nabídce na levé straně vyberte **objekty blob**.
2. Klikněte na **+ kontejner**.
3. Jako **název**zadejte *root*. Může to být název vašeho výběru, například *wingtiptoys*, ale v tomto příkladu používáme pro jednoduchost v tomto příkladu *kořen* .
4. Jako **úroveň veřejného přístupu**vyberte **objekt BLOB**a pak klikněte na **OK**.
5. Kliknutím na **kořen** otevřete nový kontejner.
6. Klikněte na **Odeslat**.
7. Klikněte na ikonu složky vedle **výběru souboru**.
8. Přejděte na a vyberte **Customize-UI. html** , který jste vytvořili dříve v oddílu přizpůsobení uživatelského rozhraní stránky.
9. Pokud chcete nahrávat do podsložky, rozbalte **Upřesnit** a zadejte název složky do **složky nahrát do složky**.
10. Vyberte **Nahrát**.
11. Vyberte objekt BLOB **Customize-UI. html** , který jste nahráli.
12. Napravo od textového pole **Adresa URL** výběrem ikony **Kopírovat do schránky** zkopírujte adresu URL do schránky.
13. Ve webovém prohlížeči přejděte na adresu URL, kterou jste zkopírovali, a ověřte přístup k uloženému objektu BLOB. Pokud je nepřístupná, například pokud dojde k `ResourceNotFound` chybě, ujistěte se, že je typ přístupu kontejneru nastavený na **BLOB**.

## <a name="configure-cors"></a>Konfigurace CORS

Pomocí následujícího postupu nakonfigurujte úložiště objektů BLOB pro sdílení prostředků mezi zdroji.

1. V nabídce vyberte **CORS**.
2. V případě **povolených zdrojů**zadejte `https://your-tenant-name.b2clogin.com`. Nahraďte `your-tenant-name` s názvem vašeho tenanta Azure AD B2C. Například, `https://fabrikam.b2clogin.com`. Při zadávání názvu tenanta je potřeba použít všechna malá písmena.
3. U **povolených metod**vyberte obojí `GET` a `OPTIONS`.
4. U **povolených hlaviček**zadejte hvězdičku (*).
5. U **zveřejněných hlaviček**zadejte hvězdičku (*).
6. Do **maximálního stáří**zadejte 200.
7. Klikněte na **Uložit**.

## <a name="test-cors"></a>Test CORS

Ověřte, že jste připraveni, pomocí následujícího postupu:

1. Přejít na web [www.test-CORS.org](https://www.test-cors.org/) a vložte adresu URL do pole **Adresa URL pro vzdálenou adresu** .
2. Klikněte na **Odeslat žádost**.  
    Pokud se zobrazí chyba, ujistěte se, že je [Nastavení CORS](#configure-cors) správné. Je také možné, že budete muset vymazat mezipaměť prohlížeče nebo otevřít soukromou relaci procházení stisknutím kombinace kláves CTRL + SHIFT + P.

## <a name="modify-the-extensions-file"></a>Úprava souboru rozšíření

Chcete-li nakonfigurovat přizpůsobení uživatelského rozhraní, zkopírujte **ContentDefinition** a jeho podřízené prvky ze základního souboru do souboru rozšíření.

1. Otevřete základní soubor zásad. Například *TrustFrameworkBase. XML*.
2. Vyhledejte a zkopírujte celý obsah elementu **ContentDefinitions** .
3. Otevřete soubor rozšíření. Například *TrustFrameworkExtensions. XML*. Vyhledejte element **BuildingBlocks** . Pokud element neexistuje, přidejte jej.
4. Vložte celý obsah elementu **ContentDefinitions** , který jste zkopírovali jako podřízený prvek **BuildingBlocks** elementu. 
5. Vyhledejte element **ContentDefinition** , který obsahuje `Id="api.signuporsignin"` soubor ve formátu XML, který jste zkopírovali.
6. Změňte hodnotu **LoadUri** na adresu URL souboru HTML, který jste nahráli do úložiště. Například, `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.
    
    Vaše vlastní zásada by měla vypadat takto:

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

7. Uložte soubor rozšíření.

## <a name="upload-your-updated-custom-policy"></a>Nahrajte aktualizované vlastní zásady.

1. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **architekturu prostředí identity**.
2. Klikněte na **všechny zásady**.
3. Klikněte na **nahrát zásadu**.
4. Nahrajte soubor rozšíření, který jste předtím změnili.

## <a name="test-the-custom-policy-by-using-run-now"></a>Otestujte vlastní zásady pomocí rutiny **spustit hned**

1. V okně **Azure AD B2C** přejít na **všechny zásady**.
2. Vyberte vlastní zásady, které jste nahráli, a klikněte na tlačítko **Spustit** .
3. Měli byste být schopni se zaregistrovat pomocí e-mailové adresy.

## <a name="reference"></a>Reference

### <a name="sample-templates"></a>Ukázkové šablony
Ukázkové šablony pro přizpůsobení uživatelského rozhraní můžete najít tady:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Složka sample_templates/Wingtip obsahuje následující soubory HTML:

| Šablona HTML5 | Popis |
|----------------|-------------|
| *phonefactor.html* | Tento soubor použijte jako šablonu pro stránku služby Multi-Factor Authentication. |
| *resetpassword.html* | Tento soubor použijte jako šablonu pro stránku zapomenuté heslo. |
| *selfasserted.html* | Tento soubor použijte jako šablonu pro přihlašovací stránku účtu sociální sítě, přihlašovací stránku místního účtu nebo přihlašovací stránku místního účtu. |
| *unified.html* | Tento soubor použijte jako šablonu pro sjednocenou registraci nebo přihlašovací stránku. |
| *updateprofile.html* | Tento soubor použijte jako šablonu pro stránku Aktualizace profilu. |

Tady je postup, jak použít ukázku. 
1. Naklonujte úložiště na místním počítači. V části sample_templates vyberte složku šablony. Můžete použít `wingtip` nebo `contoso`.
2. Nahrajte všechny soubory ve `css`složkách, `fonts`a `images` do úložiště objektů blob, jak je popsáno v předchozích částech. 
3. Pak otevřete každý \*soubor. HTML v kořenovém adresáři `wingtip` nebo `contoso` (podle toho, co jste vybrali v prvním kroku), a nahraďte všechny výskyty "http://localhost" adresami URL souborů CSS, obrázků a písem, které jste nahráli v kroku 2.
4. Uložte soubory \*. html a nahrajte je do úložiště objektů BLOB.
5. Nyní upravte soubor rozšíření tak, jak bylo uvedeno dříve v [části Úprava souboru rozšíření](#modify-the-extensions-file).
6. Pokud se zobrazí chybějící písma, obrázky nebo šablony stylů CSS, zkontrolujte odkazy v zásadách rozšíření a v \*souborech. html.

### <a name="content-defintion-ids"></a>ID definice obsahu

V části Upravit vlastní zásady registrace nebo přihlašování jste nakonfigurovali definici obsahu pro `api.idpselections`. Úplná sada ID definice obsahu, která je rozpoznána rozhraním Azure AD B2C identity Experience Framework, a jejich popisy jsou uvedeny v následující tabulce:

| ID definice obsahu | Popis | 
|-----------------------|-------------|
| *api.error* | **Chybová stránka**. Tato stránka se zobrazí, pokud dojde k výjimce nebo chybě. |
| *api.idpselections* | **Stránka výběru zprostředkovatele identity** Tato stránka obsahuje seznam zprostředkovatelů identity, ze kterých si může uživatel vybrat během přihlašování. Tyto možnosti jsou buď poskytovatelé podnikových identit, poskytovatelé sociálních identit, jako je Facebook, Google + nebo místní účty. |
| *api.idpselections.signup* | **Výběr zprostředkovatele identity pro registraci**. Tato stránka obsahuje seznam zprostředkovatelů identity, ze kterých si může uživatel vybrat během registrace. Tyto možnosti jsou buď poskytovatelé podnikových identit, poskytovatelé sociálních identit, jako je Facebook, Google + nebo místní účty. |
| *api.localaccountpasswordreset* | **Stránka zapomenuté heslo** Tato stránka obsahuje formulář, který musí uživatel dokončit, aby bylo možné resetování hesla inicializovat.  |
| *api.localaccountsignin* | **Přihlašovací stránka místního účtu** Tato stránka obsahuje přihlašovací formulář pro přihlašování pomocí místního účtu, který je založený na e-mailové adrese nebo uživatelském jménu. Formulář může obsahovat textové pole pro zadání textu a heslo. |
| *api.localaccountsignup* | **Přihlašovací stránka místního účtu**. Tato stránka obsahuje registrační formulář pro registraci k místnímu účtu na základě e-mailové adresy nebo uživatelského jména. Formulář může obsahovat různé vstupní ovládací prvky, jako je textové pole, zadání hesla, přepínač, rozevírací seznamy s jedním výběrem a zaškrtávací políčka vícenásobného výběru. |
| *api.phonefactor* | **Stránku Multi-Factor Authentication**. Na této stránce můžou uživatelé při registraci nebo přihlašování ověřit svoje telefonní čísla (pomocí textu nebo hlasu). |
| *api.selfasserted* | **Stránka pro registraci účtu sociální**sítě Tato stránka obsahuje registrační formulář, který musí uživatelé dokončit při registraci pomocí stávajícího účtu od poskytovatele sociálních identit, jako je Facebook nebo Google +. Tato stránka se podobá na předchozí stránce pro registraci účtu sociální sítě, s výjimkou polí zadání hesla. |
| *api.selfasserted.profileupdate* | **Stránka aktualizace profilu**. Tato stránka obsahuje formulář, pomocí kterého můžou uživatelé aktualizovat svůj profil. Tato stránka se podobá stránce pro registraci účtu sociální sítě, s výjimkou polí zadání hesla. |
| *api.signuporsignin* | **Jednotná registrace nebo přihlašovací stránka**. Tato stránka zpracovává registraci i přihlašování uživatelů, kteří můžou používat podnikové zprostředkovatele identity, zprostředkovatele sociálních identit, jako je Facebook nebo Google + nebo místní účty.  |

## <a name="next-steps"></a>Další postup

Další informace o prvcích uživatelského rozhraní, které je možné přizpůsobit, najdete v [Referenční příručce pro přizpůsobení uživatelského rozhraní pro předdefinované zásady](active-directory-b2c-reference-ui-customization.md).
