---
title: Kurz – přizpůsobení uživatelského rozhraní svých aplikací v Azure Active Directory B2C | Dokumentace Microsoftu
description: Zjistěte, jak přizpůsobit uživatelské rozhraní aplikace v Azure Active Directory B2C na webu Azure portal.
services: B2C
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9c206ac7a13ea222a01cac78c447c0764f753517
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50633788"
---
# <a name="tutorial-customize-the-user-interface-of-your-applications-in-azure-active-directory-b2c"></a>Kurz: Přizpůsobení uživatelského rozhraní aplikací v Azure Active Directory B2C

Pro běžné uživatelské prostředí jako například registrace, přihlašování a úpravy profilu, můžete použít [integrované zásady](active-directory-b2c-reference-policies.md) v Azure Active Directory (Azure AD) B2C. Informace v tomto kurzu vám umožní získat informace tom, jak [přizpůsobení uživatelského rozhraní (UI)](customize-ui-overview.md) z těchto prostředí s využitím vlastní soubory HTML a CSS.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření souborů přizpůsobení uživatelského rozhraní
> * Vytvořte zásadu registrace a přihlašování, která používá soubory
> * Testování vlastní uživatelské rozhraní

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Pokud jste ještě nevytvořili vlastní [Tenanta Azure AD B2C](tutorial-create-tenant.md), založte si ho teď. Existujícího tenanta můžete použít, pokud jste vytvořili v předchozím kurzu.

## <a name="create-customization-files"></a>Vytvořit soubory vlastního nastavení

Vytvoření účtu služby Azure storage a kontejner a pak umístit základní soubory HTML a CSS v kontejneru.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

I když můžete ukládat soubory v mnoha směrech pro účely tohoto kurzu, jsou uloženy v [úložiště objektů Blob v Azure](../storage/blobs/storage-blobs-introduction.md).

1. Ujistěte se, že používáte adresáře, který obsahuje předplatné Azure. Vyberte **filtr adresářů a předplatných** v horní nabídce a vyberte adresář, který obsahuje vaše předplatné. Tento adresář je jiný než ten, který obsahuje váš tenant Azure B2C.

    ![Přepnout na adresář předplatného](./media/tutorial-customize-ui/switch-directories.png)

2. Vyberte všechny služby v levém horním rohu webu Azure portal, vyhledejte a vyberte **účty úložiště**. 
3. Vyberte **Přidat**.
4. V části **skupiny prostředků**vyberte **vytvořit nový**, zadejte název pro novou skupinu prostředků a pak klikněte na tlačítko **OK**.
5. Zadejte název účtu úložiště. Zvolený název musí být jedinečný v rámci Azure, mít délku 3 až 24 znaků a může obsahovat jen číslice a malá písmena.
6. Vyberte umístění účtu úložiště, nebo přijměte výchozí umístění. 
7. Přijměte všechny výchozí hodnoty, vyberte **revize + vytvořit**a potom klikněte na tlačítko **vytvořit**.
8. Po vytvoření účtu úložiště vyberte **přejít k prostředku**.

### <a name="create-a-container"></a>Vytvoření kontejneru

1. Na stránce Přehled účtu úložiště vyberte **objekty BLOB**.
2. Vyberte **kontejneru**, zadejte název kontejneru, zvolte **objektů Blob (anonymní přístup pro čtení pro objekty BLOB pouze)** a potom klikněte na tlačítko **OK**.

### <a name="enable-cors"></a>Povolení CORS

 Azure AD B2C kód v prohlížeči používá moderní a standardní přístup k načtení vlastního obsahu z adresy URL, který zadáte v zásadách. Prostředků mezi zdroji (CORS) pro sdílení obsahu s omezeným přístupem prostředků umožňuje na webové stránce vyžadované z jiných domén.

1. V nabídce vyberte **CORS**.
2. Pro **povolené zdroje**, **povolené hlavičky**, a **zveřejněné hlavičky**, zadejte `your-tenant-name.b2clogin.com`. Nahraďte `your-tenant-name` s názvem vašeho tenanta Azure AD B2C. Například, `fabrikam.b2clogin.com`.
3. Pro **povolených operací**, vyberte oba `GET` a `OPTIONS`.
4. Pro **maximální stáří**, zadejte 200.

    ![Povolení CORS](./media/tutorial-customize-ui/enable-cors.png)

5. Klikněte na **Uložit**.

### <a name="create-the-customization-files"></a>Vytvořit soubory vlastního nastavení

Přizpůsobení uživatelského rozhraní přihlašovacího prostředí, začnete vytvořením jednoduchého souboru HTML a CSS. Můžete nakonfigurovat libovolným způsobem, ale musí mít kódu HTML **div** element s identifikátorem `api`. Například, `<div id="api"></div>`. Vloží prvky do Azure AD B2C `api` stránka se zobrazí v kontejneru.

1. V místní složce, vytvořte následující soubor a ujistěte se, že změníte `your-storage-account` k názvu účtu úložiště a `your-container` název kontejneru, který jste vytvořili. Například, `https://store1.blob.core.windows.net/b2c/style.css`.

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>My B2C Application</title>
        <link rel="stylesheet" href="https://your-storage-account.blob.core.windows.net/your-container/style.css">
      </head>
      <body>  
        <h1>My B2C Application</h1>
        <div id="api"></div>
      </body>
    </html>
    ```

    Na stránce nelze navrhovat způsobem, který chcete, ale **api** div element se vyžaduje pro libovolný soubor vlastního nastavení HTML, který vytvoříte. 

3. Uložte soubor jako *vlastní ui.html*.
4. Vytvořte následující jednoduchý šablon stylů CSS, která centra pro všechny elementy na stránce registrace / přihlášení, včetně prvků, které Azure AD B2C vkládá.

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center; 
    }
    .entry {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center; 
    }
    .create {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

5. Uložte soubor jako *style.css*.

### <a name="upload-the-customization-files"></a>Nahrát soubory vlastního nastavení

V tomto kurzu ukládáte soubory, které jste vytvořili v účtu úložiště tak, aby Azure AD B2C k nim přistupovat.

1. Zvolte **všechny služby** v levém horním rohu webu Azure portal, vyhledejte a vyberte **účty úložiště**.
2. Vyberte účet úložiště, který jste vytvořili, vyberte **objekty BLOB**a potom vyberte kontejner, který jste vytvořili.
3. Vyberte **nahrát**, najděte a vyberte *vlastní ui.html* souboru a pak klikněte na tlačítko **nahrát**.

    ![Nahrát soubory vlastního nastavení](./media/tutorial-customize-ui/upload-blob.png)

4. Zkopírujte adresu URL souboru, který jste nahráli do použít později v tomto kurzu.
5. Zopakujte kroky 3 a 4 *style.css* souboru.

## <a name="create-a-sign-up-and-sign-in-policy"></a>Vytvoření zásad registrace a přihlášení

K dokončení kroků v tomto kurzu, budete muset vytvořit testovací aplikace a zásady registrace nebo přihlašování v Azure AD B2C. Můžete použít zásady popsané v tomto kurzu k jiné uživatelské prostředí, jako je například upravování profilu.

### <a name="create-an-azure-ad-b2c-application"></a>Vytvoření aplikace Azure AD B2C

Probíhá komunikace s Azure AD B2C prostřednictvím aplikace, kterou vytvoříte ve vašem tenantovi. Následující kroky slouží k vytvoření aplikace, který přesměruje autorizační token, který je vrácen [ https://jwt.ms ](https://jwt.ms).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **aplikací**a pak vyberte **přidat**.
5. Zadejte název aplikace, například *testapp1*.
6. Pro **webová aplikace / webové rozhraní API**vyberte `Yes`a pak zadejte `https://jwt.ms` pro **adresy URL odpovědi**.
7. Klikněte na možnost **Vytvořit**.

### <a name="create-the-policy"></a>Vytvoření zásad

K otestování soubory vlastního nastavení, vytvoření integrované zásady registrace nebo přihlášení, která používá aplikaci, kterou jste vytvořili.

1. Ve vašem tenantovi Azure AD B2C vyberte **zásady registrace nebo přihlášení**a potom klikněte na tlačítko **přidat**.
2. Zadejte název zásady. Například *signup_signin*. Předpona, která *B2C_1* se automaticky přidá název při vytváření zásady.
3. Vyberte **zprostředkovatelé Identity**, nastavte **e-mailová registrace** pro místní účet a pak klikněte na tlačítko **OK**.
4. Vyberte **atributy registrace**, zvolte atributy, které chcete během registrace shromažďovat od zákazníka. Například nastavte **země/oblast**, **zobrazovaný název**, a **PSČ**a potom klikněte na tlačítko **OK**.
5. Vyberte **deklarace identit aplikace**, zvolte deklarace identit, které se mají vracet v autorizačních tokenech odesílaných zpět do aplikace po úspěšné registraci nebo přihlašování prostředí. Vyberte například **zobrazovaný název**, **zprostředkovatele Identity**, **PSČ**, **uživatel je nový** a **ID objektu uživatele** a potom klikněte na tlačítko **OK**.
6. Vyberte **přizpůsobení uživatelského rozhraní stránky**vyberte **stránku registrace nebo přihlášení Unified**a potom na **Ano** pro **použít vlastní stránku**.
7. V **vlastní identifikátor URI stránky**, zadejte adresu URL *vlastní ui.html* souboru, že jste si poznamenali dříve a potom klikněte na **OK**.
8. Klikněte na možnost **Vytvořit**.

## <a name="test-the-policy"></a>Testování zásad

1. Ve vašem tenantovi Azure AD B2C vyberte **zásady registrace nebo přihlášení**a potom vyberte zásadu, kterou jste vytvořili. Například *B2C_1_signup_signin*.
2. Ujistěte se, že je vybraná aplikace, kterou jste vytvořili v **vyberte aplikaci**a potom klikněte na tlačítko **Run Now**.

    ![Spuštění zásady registrace / přihlášení](./media/tutorial-customize-ui/signup-signin.png)

    Měli byste vidět stránku podobně jako v následujícím příkladu s elementy na střed na základě souboru šablon stylů CSS, který jste vytvořili:

    ![Výsledky zásad](./media/tutorial-customize-ui/run-now.png) 

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Vytvoření souborů přizpůsobení uživatelského rozhraní
> * Vytvořte zásadu registrace a přihlašování, která používá soubory
> * Testování vlastní uživatelské rozhraní

> [!div class="nextstepaction"]
> [Přizpůsobení jazyka v Azure Active Directory B2C](active-directory-b2c-reference-language-customization.md)