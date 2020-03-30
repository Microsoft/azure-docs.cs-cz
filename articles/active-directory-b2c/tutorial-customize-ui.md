---
title: 'Kurz: Přizpůsobení uživatelského rozhraní'
titleSuffix: Azure AD B2C
description: Zjistěte, jak přizpůsobit uživatelské rozhraní (UI) vašich aplikací ve službě Azure Active Directory B2C pomocí portálu Azure.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b2b2bc8dd4e60348553228b8b418df252a8c426a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186246"
---
# <a name="tutorial-customize-the-interface-of-user-experiences-in-azure-active-directory-b2c"></a>Kurz: Přizpůsobení rozhraní uživatelských prostředí ve službě Azure Active Directory B2C

Pro běžnější uživatelské prostředí, jako je registrace, přihlášení a úpravy profilu, můžete použít [toky uživatelů](user-flow-overview.md) ve službě Azure Active Directory B2C (Azure AD B2C). Informace v tomto kurzu vám pomůže naučit se [přizpůsobit uživatelské rozhraní (UI)](customize-ui-overview.md) těchto prostředí pomocí vlastní chrup u souborů HTML a CSS.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření souborů vlastního nastavení uživatelského rozhraní
> * Aktualizace toku uživatele pro použití souborů
> * Testování přizpůsobeného ui

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

[Vytvořte tok uživatelů,](tutorial-create-user-flows.md) který uživatelům umožní přihlásit se a přihlásit se k vaší aplikaci.

## <a name="create-customization-files"></a>Vytvoření souborů vlastního nastavení

Vytvoříte účet úložiště Azure a kontejner a pak umístit základní SOUBORY HTML a CSS v kontejneru.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

I když můžete soubory ukládat mnoha způsoby, v tomto kurzu je uložíte v [úložišti objektů Blob Azure](../storage/blobs/storage-blobs-introduction.md).

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje vaše předplatné Azure. V horní nabídce vyberte filtr **Directory + odběr** a zvolte adresář, který obsahuje vaše předplatné. Tento adresář se liší od adresáře, který obsahuje vašeho klienta Azure B2C.
3. V levém horním rohu portálu Azure vyberte Všechny služby, vyhledejte a vyberte **účty úložiště**.
4. Vyberte **Přidat**.
5. V části **Skupina prostředků**vyberte **Vytvořit nový**, zadejte název nové skupiny prostředků a klepněte na tlačítko **OK**.
6. Zadejte název účtu úložiště. Zvolený název musí být jedinečný v rámci Azure, mít délku 3 až 24 znaků a může obsahovat jen číslice a malá písmena.
7. Vyberte umístění účtu úložiště nebo přijměte výchozí umístění.
8. Přijměte všechny ostatní výchozí hodnoty, vyberte **Zkontrolovat + vytvořit**a klepněte na tlačítko **Vytvořit**.
9. Po vytvoření účtu úložiště vyberte **Přejít na prostředek**.

### <a name="create-a-container"></a>Vytvoření kontejneru

1. Na stránce s přehledem účtu úložiště vyberte **Objekty blob .**
2. Vyberte **Kontejner**, zadejte název kontejneru, zvolte **Objekt blob (anonymní přístup ke čtení pouze pro objekty BLOB)** a klepněte na tlačítko **OK**.

### <a name="enable-cors"></a>Povolení CORS

 Kód Azure AD B2C v prohlížeči používá moderní a standardní přístup k načtení vlastního obsahu z adresy URL, kterou zadáte v toku uživatele. Sdílení prostředků mezi zdroji (CORS) umožňuje omezené prostředky na webové stránce, které mají být požadovány z jiných domén.

1. V nabídce vyberte **CORS**.
2. V **případě povoleného původu**zadejte `https://your-tenant-name.b2clogin.com`. Nahraďte `your-tenant-name` se názvem vašeho klienta Azure AD B2C. Například, `https://fabrikam.b2clogin.com`. Při zadávání jména klienta je třeba použít všechna malá písmena.
3. V **případě povolených metod**vyberte `GET`položky ,`PUT`a `OPTIONS`.
4. Do **pole Povolené hlavičky**zadejte hvězdičku (*).
5. Do **pole Exponovaná záhlaví**zadejte hvězdičku (*).
6. Maximální **věk**zadejte hodnotu 200.

    ![Konfigurační stránka CORS v úložišti objektů Blob Azure na webu Azure Portal](./media/tutorial-customize-ui/enable-cors.png)

5. Klikněte na **Uložit**.

### <a name="create-the-customization-files"></a>Vytvoření souborů vlastního nastavení

Chcete-li přizpůsobit uživatelské prostředí prostředí pro registraci, začněte vytvořením jednoduchého souboru HTML a CSS. Html můžete nakonfigurovat libovolným způsobem, ale **div** musí mít prvek `api`div s identifikátorem aplikace . Například, `<div id="api"></div>`. Azure AD B2C vloží `api` prvky do kontejneru při zobrazení stránky.

1. V místní složce vytvořte následující soubor a `your-storage-account` ujistěte se, že `your-container` změníte název účtu úložiště a název kontejneru, který jste vytvořili. Například, `https://store1.blob.core.windows.net/b2c/style.css`.

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

    Stránka může být navržena libovolným způsobem, ale prvek **API** div je vyžadován pro libovolný soubor vlastního nastavení HTML, který vytvoříte.

3. Uložte soubor jako *custom-ui.html*.
4. Vytvořte následující jednoduchý CSS, který vycentruje všechny prvky na přihlašovací nebo přihlašovací stránce včetně prvků, které vloží Azure AD B2C.

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

5. Uložte soubor jako *styl.css*.

### <a name="upload-the-customization-files"></a>Nahrání souborů vlastního nastavení

V tomto kurzu uložíte soubory, které jste vytvořili v účtu úložiště tak, aby k nim měl přístup Azure AD B2C.

1. V levém horním rohu portálu Azure vyberte **Všechny služby,** vyhledejte a vyberte **účty úložiště**.
2. Vyberte účet úložiště, který jste vytvořili, vyberte **Objekty blob**a pak vyberte kontejner, který jste vytvořili.
3. Vyberte **Nahrát**, přejděte na *soubor custom-ui.html* a vyberte jej a klepněte na tlačítko **Nahrát**.

    ![Nahrání stránky objektu blob na portál se zvýrazněným tlačítkem Nahrát a zvýrazněnými soubory](./media/tutorial-customize-ui/upload-blob.png)

4. Zkopírujte adresu URL souboru, který jste nahráli, abyste ji použili později v kurzu.
5. Opakujte krok 3 a 4 pro soubor *style.css.*

## <a name="update-the-user-flow"></a>Aktualizace toku uživatelů

1. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
2. Vyberte **Toky uživatelů (zásady)** a pak vyberte *B2C_1_signupsignin1* toku uživatele.
3. Vyberte **Rozložení stránky**a potom v části **Sjednocené registrace nebo přihlašovací stránka**klikněte na **Ano** pro použití vlastního **obsahu stránky**.
4. V **identifikátoru URI vlastní stránky**zadejte identifikátor URI pro soubor *custom-ui.html,* který jste zaznamenali dříve.
5. V horní části stránky vyberte **Uložit**.

## <a name="test-the-user-flow"></a>Testování toku uživatele

1. Ve vašem tenantovi Azure AD B2C vyberte **Toky uživatelů** a vyberte *B2C_1_signupsignin1* tok uživatele.
2. V horní části stránky klikněte na **Spustit tok uživatele**.
3. Klepněte na tlačítko **Spustit tok uživatele.**

    ![Spuštění stránky toku uživatele pro tok registrace nebo přihlášení uživatele](./media/tutorial-customize-ui/run-user-flow.png)

    Měli byste vidět stránku podobnou následujícímu příkladu s prvky vystředěnými na základě vytvořeného souboru CSS:

    ![Webový prohlížeč zobrazující zaregistrovanou nebo přihlašovací stránku s vlastními prvky uživatelského rozhraní](./media/tutorial-customize-ui/run-now.png)

## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili:

> [!div class="checklist"]
> * Vytvoření souborů vlastního nastavení uživatelského rozhraní
> * Aktualizace toku uživatele pro použití souborů
> * Testování přizpůsobeného ui

> [!div class="nextstepaction"]
> [Přizpůsobení jazyka ve službě Azure Active Directory B2C](user-flow-language-customization.md)
