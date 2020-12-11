---
title: 'Kurz: přizpůsobení uživatelského rozhraní'
titleSuffix: Azure AD B2C
description: V tomto kurzu se dozvíte, jak přizpůsobit uživatelské rozhraní (UI) vašich aplikací v Azure Active Directory B2C pomocí Azure Portal.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 036e9e6bf58d837c698a6092770ef581f77bc20c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108242"
---
# <a name="tutorial-customize-the-interface-of-user-experiences-in-azure-active-directory-b2c"></a>Kurz: přizpůsobení rozhraní uživatelského prostředí v Azure Active Directory B2C

Pro běžné uživatelské prostředí, jako je registrace, přihlašování a úpravy profilu, můžete použít [toky uživatelů](user-flow-overview.md) v Azure Active Directory B2C (Azure AD B2C). Informace v tomto kurzu vám pomůžou s postupem [přizpůsobení uživatelského rozhraní (UI)](customize-ui-with-html.md) těchto prostředí pomocí vlastních souborů HTML a CSS.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořit vlastní soubory uživatelského rozhraní
> * Aktualizace toku uživatele pro použití souborů
> * Testování přizpůsobeného uživatelského rozhraní

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Předpoklady

[Vytvořte uživatelský tok](tutorial-create-user-flows.md), který uživatelům umožní registraci a přihlášení do vaší aplikace.

## <a name="create-customization-files"></a>Vytvořit soubory vlastního nastavení

Vytvoříte účet a kontejner úložiště Azure a potom do kontejneru umístíte základní soubory HTML a CSS.

### <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

I když můžete soubory ukládat mnoha různými způsoby, můžete je v tomto kurzu ukládat do [úložiště objektů BLOB v Azure](../storage/blobs/storage-blobs-introduction.md).

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje vaše předplatné Azure. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vaše předplatné. Tento adresář je jiný než ten, který obsahuje vašeho tenanta Azure B2C.
3. V levém horním rohu Azure Portal vyberte všechny služby, vyhledejte a vyberte **účty úložiště**.
4. Vyberte **Přidat**.
5. V části **Skupina prostředků** vyberte **vytvořit novou**, zadejte název nové skupiny prostředků a pak klikněte na **OK**.
6. Zadejte název účtu úložiště. Zvolený název musí být jedinečný v rámci Azure, mít délku 3 až 24 znaků a může obsahovat jen číslice a malá písmena.
7. Vyberte umístění účtu úložiště nebo přijměte výchozí umístění.
8. Přijměte všechny ostatní výchozí hodnoty, vyberte **zkontrolovat + vytvořit** a pak klikněte na **vytvořit**.
9. Po vytvoření účtu úložiště vyberte **Přejít k prostředku**.

### <a name="create-a-container"></a>Vytvoření kontejneru

1. Na stránce Přehled účtu úložiště vyberte **objekty blob**.
2. Vyberte **kontejner**, zadejte název kontejneru, zvolte **objekt BLOB (anonymní přístup pro čtení jenom pro objekty BLOB)** a pak klikněte na **OK**.

### <a name="enable-cors"></a>Povolení CORS

 Azure AD B2C kód v prohlížeči používá moderní a standardní přístup k načtení vlastního obsahu z adresy URL, kterou zadáte v toku uživatele. Sdílení prostředků mezi zdroji (CORS) umožňuje omezit prostředky na webové stránce, které se mají požadovat z jiných domén.

1. V nabídce vyberte **CORS**.
2. V případě **povolených zdrojů** zadejte `https://your-tenant-name.b2clogin.com` . Nahraďte `your-tenant-name` názvem vašeho tenanta Azure AD B2C. Například `https://fabrikam.b2clogin.com`. Při zadávání názvu tenanta je potřeba použít všechna malá písmena.
3. U **povolených metod** vyberte `GET` , `PUT` a `OPTIONS` .
4. U **povolených hlaviček** zadejte hvězdičku (*).
5. U **zveřejněných hlaviček** zadejte hvězdičku (*).
6. Do **maximálního stáří** zadejte 200.

    ![Konfigurační stránka CORS ve službě Azure Blob Storage v Azure Portal](./media/tutorial-customize-ui/enable-cors.png)

5. Klikněte na **Uložit**.

### <a name="create-the-customization-files"></a>Vytvoření souborů vlastního nastavení

Chcete-li přizpůsobit uživatelské rozhraní prostředí pro registraci, začněte tím, že vytvoříte jednoduchý soubor HTML a CSS. HTML můžete nakonfigurovat způsobem, který chcete, ale musí mít element **div** s identifikátorem `api` . Například `<div id="api"></div>`. Azure AD B2C vloží prvky do `api` kontejneru při zobrazení stránky.

1. V místní složce vytvořte následující soubor a ujistěte se, že jste změnili `your-storage-account` název účtu úložiště a `your-container` název kontejneru, který jste vytvořili. Například `https://store1.blob.core.windows.net/b2c/style.css`.

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

    Stránku lze navrhnout jakýmkoli způsobem, ale element **rozhraní API** div je vyžadován pro libovolný soubor vlastního nastavení HTML, který vytvoříte.

3. Uložte soubor jako *custom-ui.html*.
4. Vytvořte následující jednoduchou šablonu stylů CSS, která bude nacentrovat všechny prvky na stránce pro registraci nebo přihlášení včetně prvků, které Azure AD B2C vkládání.

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

5. Uložte soubor jako *style. CSS*.

### <a name="upload-the-customization-files"></a>Odeslat soubory vlastního nastavení

V tomto kurzu ukládáte soubory, které jste vytvořili v účtu úložiště tak, aby k nim Azure AD B2C přistupovat.

1. V levém horním rohu Azure Portal vyberte **všechny služby** , vyhledejte a vyberte **účty úložiště**.
2. Vyberte účet úložiště, který jste vytvořili, vyberte **objekty blob** a potom vyberte kontejner, který jste vytvořili.
3. Vyberte **Odeslat**, přejděte na a vyberte soubor *custom-ui.html* a pak klikněte na **nahrát**.

    ![Stránka nahrát objekt blob na portálu se zvýrazněným tlačítkem Odeslat a soubory](./media/tutorial-customize-ui/upload-blob.png)

4. Zkopírujte adresu URL souboru, který jste nahráli pro pozdější použití v tomto kurzu.
5. Zopakujte kroky 3 a 4 pro soubor *style. CSS* .

## <a name="update-the-user-flow"></a>Aktualizace toku uživatele

1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
2. Vyberte **toky uživatelů** a potom vyberte tok uživatele *B2C_1_signupsignin1* .
3. Vyberte **rozložení stránky** a potom v části **jednotná registrace nebo přihlášení** klikněte na **Ano** , aby se **použil vlastní obsah stránky**.
4. Do pole **URI vlastní stránky** zadejte identifikátor URI pro soubor *custom-ui.html* , který jste si poznamenali dříve.
5. V horní části stránky vyberte **Save (Uložit**).

## <a name="test-the-user-flow"></a>Testování toku uživatele

1. Ve vašem tenantovi Azure AD B2C vyberte **toky uživatelů** a vyberte *B2C_1_signupsignin1* tok uživatele.
2. V horní části stránky klikněte na **Spustit tok uživatele**.
3. Klikněte na tlačítko **Spustit tok uživatele** .

    ![Spustit stránku Flow uživatele pro uživatelský tok registrace nebo přihlašování](./media/tutorial-customize-ui/run-user-flow.png)

    Měla by se zobrazit stránka podobná následujícímu příkladu s prvky na středovém souboru CSS, který jste vytvořili:

    ![Webový prohlížeč zobrazující přihlašovací stránku pro registraci nebo přihlášení s vlastními prvky uživatelského rozhraní](./media/tutorial-customize-ui/run-now.png)

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Vytvořit vlastní soubory uživatelského rozhraní
> * Aktualizace toku uživatele pro použití souborů
> * Testování přizpůsobeného uživatelského rozhraní

> [!div class="nextstepaction"]
> [Přizpůsobení uživatelského rozhraní v Azure Active Directory B2C](customize-ui-with-html.md)
