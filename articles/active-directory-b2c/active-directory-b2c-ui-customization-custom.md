---
title: Přizpůsobení uživatelského rozhraní aplikace pomocí vlastních zásad v Azure Active Directory B2C | Dokumentace Microsoftu
description: Další informace o přizpůsobení uživatelského rozhraní pomocí vlastních zásad v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 3c3b7d73de9a0e7e72351687af16b98b11f9581b
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55188487"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Přizpůsobení uživatelského rozhraní aplikace pomocí vlastních zásad v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Po dokončení tohoto článku, budete mít vlastní zásady registrace a přihlášení pomocí značky a vzhled. S Azure Active Directory B2C (Azure AD B2C), získáte téměř úplnou kontrolu nad obsah HTML a CSS, která se zobrazí uživatelům. Při použití vlastních zásad konfigurace přizpůsobení uživatelského rozhraní v XML namísto použití ovládacích prvků na webu Azure Portal. 

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v [začít pracovat s vlastními zásadami](active-directory-b2c-get-started-custom.md). Měli byste mít funkční vlastní zásady pro registraci a přihlaste se pomocí místních účtů.

## <a name="page-ui-customization"></a>Přizpůsobení uživatelského rozhraní stránky

Pomocí stránky funkce přizpůsobení uživatelského rozhraní můžete přizpůsobit vzhled a chování všech vlastních zásad. Můžete také udržovat konzistenci značky a vizuální konzistenci mezi vaší aplikací a službou Azure AD B2C.

Zde je, jak to funguje: Azure AD B2C kód v prohlížeči vašeho zákazníka a využívá moderní přístup a volá [sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/). Nejprve zadejte adresu URL ve vlastních zásadách přizpůsobený obsah ve formátu HTML. Azure AD B2C sloučí elementy uživatelského rozhraní s obsahem HTML, který je načtený z vaší adresy URL, a pak zobrazí stránku zákazníkovi.

## <a name="create-your-html5-content"></a>Vytvoření obsahu vaší HTML5

Vytvoření obsahu s názvem vašeho produktu značky HTML v názvu.

1. Zkopírujte následující fragment kódu HTML. Je ve správném formátu HTML5 se prázdný element volá *\<div id = "rozhraní api"\>\</div\>* v rámci *\<tělo\>* značky. Tento prvek určuje, kde má být vložen obsah Azure AD B2C.

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

2. Vložte zkopírovaný fragment kódu v textovém editoru a uložte soubor jako *přizpůsobit ui.html*.

## <a name="create-an-azure-blob-storage-account"></a>Vytvoření účtu úložiště objektů Blob v Azure

>[!NOTE]
> V tomto článku používáme úložiště objektů Blob v Azure k hostování náš obsah. Je možné k hostování obsahu na webovém serveru, ale je potřeba [povolení CORS na vašem webovém serveru](https://enable-cors.org/server.html).

Chcete-li hostovat tento obsah ve formátu HTML v úložišti objektů Blob, postupujte takto:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na **centra** nabídce vyberte možnost **nový** > **úložiště** > **účtu úložiště**.
3. Zadejte jedinečný **název** pro váš účet úložiště.
4. **Model nasazení** může zůstat **Resource Manageru**.
5. Změna **druh účtu** k **úložiště objektů Blob**.
6. **Výkon** může zůstat **standardní**.
7. **Replikace** může zůstat **RA-GRS**.
8. **Úroveň přístupu** může zůstat **Hot**.
9. **Šifrování služby Storage** může zůstat **zakázané**.
10. Vyberte **předplatné** pro váš účet úložiště.
11. Vytvoření **skupiny prostředků** nebo vyberte existující.
12. Vyberte **zeměpisná poloha** pro váš účet úložiště.
13. Vytvořte účet úložiště kliknutím na **Vytvořit**.  
    Po dokončení nasazení **účtu úložiště** se automaticky otevře okno.

## <a name="create-a-container"></a>Vytvoření kontejneru

Vytvoření veřejného kontejneru v úložišti objektů Blob, postupujte takto:

1. Klikněte na tlačítko **přehled** kartu.
2. Klikněte na tlačítko **kontejneru**.
3. Pro **název**, typ **$root**.
4. Nastavte **získat přístup k typu** k **Blob**.
5. Klikněte na tlačítko **$root** otevřete nový kontejner.
6. Klikněte na **Odeslat**.
7. Klikněte na ikonu složky vedle **vyberte soubor**.
8. Přejděte na **přizpůsobit ui.html**, který jste vytvořili dříve v [přizpůsobení uživatelského rozhraní stránky](#the-page-ui-customization-feature) oddílu.
9. Klikněte na **Odeslat**.
10. Vyberte vlastní ui.html objekt blob, který jste nahráli.
11. Vedle položky **URL**, klikněte na tlačítko **kopírování**.
12. V prohlížeči vložte zkopírovanou adresu URL a přejděte na web. Pokud web není přístupný, ujistěte se, že typ přístupu kontejneru je nastaven na **blob**.

## <a name="configure-cors"></a>Konfigurace CORS

Konfigurace úložiště objektů Blob pro sdílení prostředků různého původu následujícím způsobem:

1. V nabídce vyberte **CORS**.
2. Pro **povolené zdroje**, zadejte `your-tenant-name.b2clogin.com`. Nahraďte `your-tenant-name` s názvem vašeho tenanta Azure AD B2C. Například, `fabrikam.b2clogin.com`. Budete muset použít jenom malá písmena. Pokud zadáte název vašeho klienta.
3. Pro **povolené metody**, vyberte oba `GET` a `OPTIONS`.
4. Pro **povolené hlavičky**, zadejte hvězdičku (*).
5. Pro **zveřejněné hlavičky**, zadejte hvězdičku (*).
6. Pro **maximální stáří**, zadejte 200.
7. Klikněte na **Uložit**.

## <a name="test-cors"></a>Test CORS

Ověřte, že budete připraveni, následujícím způsobem:

1. Přejděte [www.test-cors.org](https://www.test-cors.org/) webu a pak vložte adresu URL v **adresa URL vzdáleného úložiště** pole.
2. Klikněte na tlačítko **poslat žádost o**.  
    Pokud se zobrazí chybová zpráva, ujistěte se, že vaše [nastavení CORS](#configure-cors) jsou správné. Může také muset vymazat mezipaměť prohlížeče nebo otevřete relaci procházení v privátní stisknutím kombinace kláves Ctrl + Shift + P.

## <a name="modify-the-extensions-file"></a>Upravte soubor rozšíření

Konfigurace přizpůsobení uživatelského rozhraní, je zkopírovat **ContentDefinition** a jeho podřízené prvky ze základního souboru do souboru rozšíření.

1. Otevřete soubor základní zásady. Například *TrustFrameworkBase.xml*.
2. Vyhledejte a zkopírujte celý obsah **ContentDefinitions** elementu.
3. Otevřete soubor rozšíření. Například *TrustFrameworkExtensions.xml*. Hledat **BuildingBlocks** elementu. Pokud element neexistuje, přidejte ji.
4. Vložte celý obsah **ContentDefinitions** element, který jste zkopírovali jako podřízený objekt **BuildingBlocks** elementu. 
5. Hledat **ContentDefinition** element, který obsahuje `Id="api.signuporsignin"` ve formátu XML, který jste zkopírovali.
6. Změňte hodnotu vlastnosti **LoadUri** na adresu URL souboru HTML, který jste nahráli do úložiště. Například, `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.
    
    Vlastní zásady by měl vypadat nějak takto:

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

## <a name="upload-your-updated-custom-policy"></a>Nahrání aktualizované vlastní zásady

1. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **architekturu rozhraní identit**.
2. Klikněte na tlačítko **všechny zásady**.
3. Klikněte na tlačítko **nahrát zásady**.
4. Nahrajte soubor rozšíření, které jste dříve změnili.

## <a name="test-the-custom-policy-by-using-run-now"></a>Testování s použitím vlastní zásady **spustit nyní**

1. Na **Azure AD B2C** okno, přejděte na **všechny zásady**.
2. Vyberte vlastní zásady, které jste nahráli a klikněte na tlačítko **spustit nyní** tlačítko.
3. Byste měli být schopni zaregistrujte s použitím e-mailovou adresu.

## <a name="reference"></a>Referenční informace

Pro přizpůsobení uživatelského rozhraní tady najdete ukázkové šablony:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Složka sample_templates/wingtip obsahuje následující soubory HTML:

| Šablona HTML5 | Popis |
|----------------|-------------|
| *phonefactor.html* | Tento soubor můžete použijte jako šablonu pro stránku služby Multi-Factor authentication. |
| *resetpassword.html* | Použít jako šablonu pro tento soubor stránka zapomenuté heslo. |
| *selfasserted.html* | Tento soubor můžete použijte jako šablonu pro stránka registrace sociálního účtu, stránku pro přihlášení místním účtem nebo místní účet přihlašovací stránky. |
| *unified.html* | Tento soubor můžete použijte jako šablonu pro jednotné stránku registrace nebo přihlášení. |
| *updateprofile.html* | Tento soubor můžete použijte jako šablonu pro stránku aktualizace profilu. |

V [upravit vaše vlastní zásady registrace / přihlášení oddíl](#modify-your-sign-up-or-sign-in-custom-policy), nakonfigurovat definici obsahu pro `api.idpselections`. Kompletní obsah ID definice, které jsou rozpoznány modulem pro architekturu rozhraní identit Azure AD B2C a jejich popisy jsou v následující tabulce:

| ID obsahu definice | Popis | 
|-----------------------|-------------|
| *api.error* | **Chybová stránka**. Na této stránce se zobrazí, když došlo k výjimce nebo došlo k chybě. |
| *api.idpselections* | **Stránka výběru zprostředkovatele identit**. Tato stránka obsahuje seznam zprostředkovatelů identity, které může uživatel vybrat při přihlašování. Tyto možnosti jsou organizace zprostředkovatelů identity, zprostředkovatelé sociálních identit, jako je Facebook nebo Google + nebo místním účtům. |
| *api.idpselections.signup* | **Výběr zprostředkovatele identity pro registraci**. Tato stránka obsahuje seznam zprostředkovatelů identity, které může uživatel vybírat během registrace. Tyto možnosti jsou organizace zprostředkovatelů identity, zprostředkovatelé sociálních identit, jako je Facebook nebo Google + nebo místním účtům. |
| *api.localaccountpasswordreset* | **Stránka zapomenuté heslo**. Tato stránka obsahuje formulář, který uživatel musí dokončit k zahájení resetování hesla.  |
| *api.localaccountsignin* | **Místní účet přihlašovací stránku**. Tato stránka obsahuje přihlašovací formulář pro přihlašování pomocí místní účet, který je založen na e-mailovou adresu nebo jméno uživatele. Formulář může obsahovat pole textového zadání a pole pro zadání hesla. |
| *api.localaccountsignup* | **Stránku pro přihlášení místním účtem**. Tato stránka obsahuje formuláři pro registraci registrace pro místní účet, který je založen na e-mailovou adresu nebo jméno uživatele. Formulář může obsahovat různé vstupní ovládací prvky, jako je například pole textového zadání, pole pro zadání hesla, přepínač, jedním výběrem rozevírací seznamy a vyberte zaškrtávací políčka. |
| *api.phonefactor* | **Stránka služby Multi-Factor authentication**. Na této stránce si uživatelé mohli ověřit jejich telefonní čísla (pomocí textových nebo hlasových) během registrace nebo přihlášení. |
| *api.selfasserted* | **Stránka registrace sociálního účtu**. Tato stránka obsahuje registrace formulář, který uživatelé musí dokončit při registraci pomocí existujícího účtu ze zprostředkovatele sociální identity, jako je Facebook nebo Google +. Tato stránka je podobný předchozí stránka registrace sociálního účtu, s výjimkou pole pro zadání hesla. |
| *api.selfasserted.profileupdate* | **Stránka pro aktualizaci profilu**. Tato stránka obsahuje formulář, který mohou uživatelé aktualizovat svůj profil. Tato stránka je podobná stránka registrace sociálního účtu s výjimkou pole pro zadání hesla. |
| *api.signuporsignin* | **Jednotná stránka registrace nebo přihlašování**. Tato stránka zpracovává registrace i přihlášení uživatelů, kteří můžou využívat enterprise zprostředkovatelů identity, zprostředkovatelé sociálních identit, jako je Facebook nebo Google + nebo místním účtům.  |

## <a name="next-steps"></a>Další postup

Další informace o prvcích uživatelského rozhraní, které lze přizpůsobit, najdete v části [referenční příručka pro přizpůsobení uživatelského rozhraní pro předdefinované zásady](active-directory-b2c-reference-ui-customization.md).
