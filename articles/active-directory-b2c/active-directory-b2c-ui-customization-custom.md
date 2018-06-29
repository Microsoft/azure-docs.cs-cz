---
title: Přizpůsobení uživatelského rozhraní pomocí vlastních zásad v Azure Active Directory B2C | Microsoft Docs
description: Další informace o přizpůsobení uživatelského rozhraní (UI), zatímco pomocí vlastních zásad v Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 3a8d797793b0e54df112ec44cdb37cbb1b40ce86
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "34710137"
---
# <a name="azure-active-directory-b2c-configure-ui-customization-in-a-custom-policy"></a>Azure Active Directory B2C: Konfigurace ve vlastních zásadách pro přizpůsobení uživatelského rozhraní

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Po dokončení tohoto článku, budete mít vlastní zásady registrace a přihlášení pomocí značky a vzhled. S Azure Active Directory B2C (Azure AD B2C), můžete získat téměř plnou kontrolu nad obsah HTML a CSS, který se zobrazí uživatelům. Pokud používáte vlastní zásadu, nakonfigurujete přizpůsobení uživatelského rozhraní v kódu XML místo použití ovládacích prvků na portálu Azure. 

## <a name="prerequisites"></a>Požadavky

Než začnete, dokončení [Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md). Měli byste mít pracovní vlastní zásadu pro registraci a přihlašování s místní účty.

## <a name="page-ui-customization"></a>Přizpůsobení uživatelského rozhraní stránky

Pomocí funkce přizpůsobení uživatelského rozhraní stránky můžete přizpůsobit vzhled a chování žádné vlastní zásady. Můžete také udržovat značky a visual konzistenci mezi aplikací a Azure AD B2C.

Zde je, jak to funguje: Azure AD B2C spuštěním kódu v prohlížeči vašeho zákazníka a používá moderní přístup názvem [sdílení prostředků různých původů (CORS)](http://www.w3.org/TR/cors/). Nejdřív zadejte adresu URL ve vlastních zásadách s přizpůsobený obsah HTML. Azure AD B2C sloučí elementy uživatelského rozhraní pomocí obsah HTML, který je načten z vaší adresy URL a potom zobrazí stránku zákazník.

## <a name="create-your-html5-content"></a>Vytvoření vaší HTML5 obsahu

Vytváření obsahu s názvem vašeho produktu značky HTML v názvu.

1. Zkopírujte následující fragment kódu HTML. Je ve správném formátu názvem HTML5 s prázdný element *\<div id = "api"\>\</div\>* umístěné v rámci *\<textu\>* značky. Tento element určuje, kde Azure AD B2C obsah má být vložen.

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

   >[!NOTE]
   >Z bezpečnostních důvodů je používání jazyka JavaScript aktuálně blokován pro přizpůsobení.

2. Vložte zkopírovaný fragment v textovém editoru a pak soubor uložte jako *přizpůsobit ui.html*.

## <a name="create-an-azure-blob-storage-account"></a>Vytvoření účtu úložiště objektů Blob v Azure

>[!NOTE]
> V tomto článku používáme úložiště objektů Blob v Azure k hostování náš obsah. Je možné hostovat obsah na webovém serveru, ale musíte [povolení CORS na vašem webovém serveru](https://enable-cors.org/server.html).

Chcete-li hostovat tento obsah HTML v úložišti objektů Blob, postupujte takto:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na **rozbočovače** nabídce vyberte možnost **nový** > **úložiště** > **účet úložiště**.
3. Zadejte jedinečný **název** pro váš účet úložiště.
4. **Model nasazení** může zůstat **Resource Manager**.
5. Změna **druh účtu** k **úložiště objektů Blob**.
6. **Výkon** může zůstat **standardní**.
7. **Replikace** může zůstat **RA-GRS**.
8. **Úroveň přístupu** může zůstat **horká**.
9. **Šifrování služby úložiště** může zůstat **zakázané**.
10. Vyberte **předplatné** pro váš účet úložiště.
11. Vytvoření **skupiny prostředků** nebo vyberte nějaký existující.
12. Vyberte **zeměpisné polohy** pro váš účet úložiště.
13. Vytvořte účet úložiště kliknutím na **Vytvořit**.  
    Po dokončení nasazení **účet úložiště** automaticky otevře se okno.

## <a name="create-a-container"></a>Vytvoření kontejneru

Pokud chcete vytvořit veřejném kontejneru v úložiště objektů Blob, postupujte takto:

1. Klikněte **přehled** kartě.
2. Klikněte na tlačítko **kontejneru**.
3. Pro **název**, typ **$root**.
4. Nastavit **přistupovat typu** k **Blob**.
5. Klikněte na tlačítko **$root** otevřete nový kontejner.
6. Klikněte na **Odeslat**.
7. Klikněte na ikonu složky vedle **vyberte soubor**.
8. Přejděte na **přizpůsobit ui.html**, který jste vytvořili předtím v [přizpůsobení uživatelského rozhraní stránky](#the-page-ui-customization-feature) části.
9. Klikněte na **Odeslat**.
10. Vyberte objekt blob přizpůsobit ui.html, který jste nahráli.
11. Vedle **URL**, klikněte na tlačítko **kopie**.
12. V prohlížeči vložte zkopírovanou adresu URL a přejděte na web. Pokud je lokalita nedostupné, ujistěte se, že typ přístupu kontejneru je nastaven na **blob**.

## <a name="configure-cors"></a>Konfigurace CORS

Konfigurace úložiště objektů Blob pro sdílení prostředků různého původu následujícím způsobem:

>[!NOTE]
>Chcete vyzkoušet funkce přizpůsobení uživatelského rozhraní pomocí naše ukázka HTML a CSS obsahu? Nabízíme [jednoduché pomocný nástroj](active-directory-b2c-reference-ui-customization-helper-tool.md) , odešle a nakonfiguruje naše ukázkový obsah na vašem účtu úložiště objektů Blob. Pokud použijete nástroj, přeskočit na [upravte registrace nebo přihlášení vlastní zásady](#modify-your-sign-up-or-sign-in-custom-policy).

1. Na **úložiště** okno, v části **nastavení**, otevřete **CORS**.
2. Klikněte na tlačítko **Add** (Přidat).
3. Pro **povolené zdroje**, zadejte hvězdičku (\*).
4. V **povolených operací** rozevíracího seznamu, vyberte **získat** a **možnosti**.
5. Pro **povolené hlavičky**, zadejte hvězdičku (\*).
6. Pro **zveřejněné hlavičky**, zadejte hvězdičku (\*).
7. Pro **maximální stáří (v sekundách)**, typ **200**.
8. Klikněte na tlačítko **Add** (Přidat).

## <a name="test-cors"></a>Test CORS

Ověřte, že jste připraveni následujícím způsobem:

1. Přejděte na [www.test-cors.org](http://www.test-cors.org/) webu a vložte adresu URL v **vzdálenou adresou URL** pole.
2. Klikněte na tlačítko **poslat žádost o**.  
    Pokud se zobrazí chyba, ujistěte se, že vaše [nastavení CORS](#configure-cors) jsou správné. Může se také muset vymazat mezipaměť prohlížeče nebo otevřete relaci procházení v privátní stisknutím kombinace kláves Ctrl + Shift + P.

## <a name="modify-your-sign-up-or-sign-in-custom-policy"></a>Upravit vlastní zásady registrace nebo přihlášení

V části nejvyšší úrovně *\<TrustFrameworkPolicy\>* značka, byste měli najít *\<BuildingBlocks\>* značky. V rámci *\<BuildingBlocks\>* přidat značky, *\<ContentDefinitions\>* značky tak, že zkopírujete následující příklad. Nahraďte *your_storage_account* s názvem účtu úložiště.

  ```xml
  <BuildingBlocks>
    <ContentDefinitions>
      <ContentDefinition Id="api.idpselections">
        <LoadUri>https://{your_storage_account}.blob.core.windows.net/customize-ui.html</LoadUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0</DataUri>
      </ContentDefinition>
    </ContentDefinitions>
  </BuildingBlocks>
  ```

## <a name="upload-your-updated-custom-policy"></a>Nahrát váš aktualizované vlastní zásady

1. V [portál Azure](https://portal.azure.com), [přepnout do kontextu klienta služby Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)a pak otevřete **Azure AD B2C** okno.
2. Klikněte na tlačítko **všechny zásady**.
3. Klikněte na tlačítko **nahrát zásady**.
4. Nahrát `SignUpOrSignin.xml` s *\<ContentDefinitions\>* značky, které jste přidali dříve.

## <a name="test-the-custom-policy-by-using-run-now"></a>Otestovat pomocí vlastních zásad **spustit nyní**

1. Na **Azure AD B2C** okno, přejděte na **všechny zásady**.
2. Vyberte vlastní zásady, který jste nahráli a klikněte na **spustit nyní** tlačítko.
3. Nyní byste měli mít přihlásit pomocí e-mailovou adresu.

## <a name="reference"></a>Referenční informace

Pro přizpůsobení uživatelského rozhraní Zde můžete najít ukázkové šablony:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Složka sample_templates/wingtip obsahuje následující soubory HTML:

| HTML5 šablony | Popis |
|----------------|-------------|
| *phonefactor.html* | Tento soubor jako šablonu použijte pro stránku služby Multi-Factor authentication. |
| *resetpassword.html* | Použít jako šablonu pro tento soubor zapomněli jste heslo?. |
| *selfasserted.html* | Tento soubor jako šablonu použijte pro stránku pro přihlášení sociálních účet, stránku pro přihlášení místní účet nebo na přihlašovací stránku místní účet. |
| *unified.html* | Tento soubor jako šablonu použijte pro jednotné stránku registrace nebo přihlášení. |
| *updateprofile.html* | Tento soubor jako šablonu použijte pro stránku aktualizace profilu. |

V [upravte část vaší vlastní zásady registrace nebo přihlášení](#modify-your-sign-up-or-sign-in-custom-policy), jste nakonfigurovali obsahu definice `api.idpselections`. Kompletní obsah definice ID, která rozpoznává rozhraní Azure AD B2C identity prostředí a jejich popisy jsou v následující tabulce:

| ID obsahu definice | Popis | 
|-----------------------|-------------|
| *api.error* | **Chybové stránky**. Tato stránka se zobrazí, když je došlo k výjimce nebo došlo k chybě. |
| *API.idpselections* | **Stránka Výběr zprostředkovatele identity**. Tato stránka obsahuje seznam zprostředkovatelů identity, které může uživatel vybírat během přihlášení. Tyto možnosti jsou poskytovatelů identit enterprise, poskytovatelů identit sociálních třeba Facebook a Google + nebo místním účtům. |
| *api.idpselections.signup* | **Výběr zprostředkovatele identity pro registraci**. Tato stránka obsahuje seznam poskytovatelů identity, které může uživatel vybírat během registrace. Tyto možnosti jsou poskytovatelů identit enterprise, poskytovatelů identit sociálních třeba Facebook a Google + nebo místním účtům. |
| *api.localaccountpasswordreset* | **Zapomněli jste heslo**. Tato stránka obsahuje formulář, který uživatel musí dokončit zahájíte resetování hesla.  |
| *API.localaccountsignin* | **Přihlašovací stránka místní účet**. Tato stránka obsahuje formulář přihlášení pro přihlášení pomocí místního účtu, který je založený na e-mailovou adresu nebo uživatelské jméno. Formulář může obsahovat vstupní textové pole a pole pro zadání hesla. |
| *API.localaccountsignup* | **Místní účet stránku**. Tato stránka obsahuje registrační formulář pro registraci pro místní účet, který je založený na e-mailovou adresu nebo uživatelské jméno. Formulář může obsahovat různé vstupní ovládací prvky, jako je například vstupní textové pole, pole pro zadání hesla, přepínače, pole rozevíracího seznamu vyberte jeden a vybrat víc zaškrtávací políčka. |
| *api.phonefactor* | **Stránka služby Multi-Factor authentication**. Na této stránce uživatelé mohli ověřit jejich telefonní čísla (pomocí textové nebo hlasové) během registrace nebo přihlášení. |
| *api.selfasserted* | **Stránku pro přihlášení sociálních účet**. Tato stránka obsahuje registrační formulář, který uživatelé musí dokončit při registraci pomocí stávající účet od poskytovatele identity sociálních třeba Facebook nebo Google +. Tato stránka je podobný na předchozí sociálních registrační stránku účtu, s výjimkou pole pro zadání hesla. |
| *api.selfasserted.profileupdate* | **Stránka pro aktualizaci profilu**. Tato stránka obsahuje formulář, který uživatelé lze použít k aktualizaci svůj profil. Tato stránka je podobná registrační stránku sociálních účtu, s výjimkou pole pro zadání hesla. |
| *api.signuporsignin* | **Jednotná stránku registrace nebo přihlášení**. Tato stránka zpracovává registrace i přihlášení uživatelů, kteří můžou využívat poskytovatelů identit enterprise, poskytovatelů identit sociálních třeba Facebook nebo Google + nebo místní účty.  |

## <a name="next-steps"></a>Další postup

Další informace o prvky uživatelského rozhraní, které se dají přizpůsobit najdete v tématu [referenční příručka pro přizpůsobení uživatelského rozhraní pro předdefinované zásady](active-directory-b2c-reference-ui-customization.md).
