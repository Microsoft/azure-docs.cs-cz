---
title: Arkose Labs s Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Naučte se integrovat Azure AD B2C ověřování pomocí Arkose Labs, abyste chránili proti útokům na roboty, útokům na převzetí účtů a podvodným otvírám účtů.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2c7eea87101a36edb0d77026489ea351b601158b
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584591"
---
# <a name="tutorial-for-configuring-arkose-labs-with-azure-active-directory-b2c"></a>Kurz pro konfiguraci Arkose Labs s Azure Active Directory B2C

V tomto kurzu se dozvíte, jak integrovat Azure AD B2C ověřování pomocí Arkose Labs. Arkose Labs můžou organizacím pomáhat s útoky na roboty, útoky při převzetí účtů a podvodnými otevřenými účty.  

## <a name="prerequisites"></a>Předpoklady

Abyste mohli začít, budete potřebovat:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* [Tenant Azure AD B2C](tutorial-create-tenant.md) , který je propojený s vaším předplatným Azure.

## <a name="scenario-description"></a>Popis scénáře

Následující diagram popisuje, jak se Arkose Labs integrují s Azure AD B2C.

![Diagram architektury Arkose Labs](media/partner-arkose-labs/arkose-architecture-diagram.png)

| Krok  | Popis |
|---|---|
|1     | Uživatel se přihlásí pomocí dříve vytvořeného účtu. Když uživatel vybere odeslat, zobrazí se výzva k vynucení Arkose Labs. Jakmile uživatel tuto výzvu dokončí, pošle se do Arkose Labs stav, ve kterém se vygeneruje token.        |
|2     |  Arkose Labs pošle token zpátky do Azure AD B2C.       |
|3     |  Před odesláním formuláře pro přihlášení se token pošle do Arkose Labs za účelem ověření.       |
|4     |  Arkose odešle zpět nebo neúspěšný výsledek z výzvy.       |
|5     |  Pokud se výzva úspěšně dokončí, odešle se formulář pro přihlášení do Azure AD B2C a Azure AD B2C dokončí ověřování.       |
|   |   |

## <a name="onboard-with-arkose-labs"></a>Zprovoznění s Arkose Labs

1. Začněte kontaktováním [Arkose Labs](https://www.arkoselabs.com/book-a-demo/) a vytvořením účtu.

2. Po vytvoření účtu přejděte na https://dashboard.arkoselabs.com/login .

3. V řídicím panelu přejděte na nastavení lokality a vyhledejte svůj veřejný klíč a privátní klíč. Tyto informace budou potřeba později ke konfiguraci Azure AD B2C.

## <a name="integrate-with-azure-ad-b2c"></a>Integrace s Azure AD B2C

### <a name="part-1--create-blob-storage-to-store-the-custom-html"></a>Část 1 – Vytvoření úložiště objektů BLOB pro uložení vlastního HTML

Pokud chcete vytvořit účet úložiště, použijte následující postup:  

1. Přihlaste se k Azure Portal.

2. Ujistěte se, že používáte adresář, který obsahuje vaše předplatné Azure. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vaše předplatné. Tento adresář je jiný než ten, který obsahuje vašeho tenanta Azure B2C.

3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte  **účty úložiště**.

4. Vyberte **Přidat**.

5. V části  **Skupina prostředků** vyberte  **vytvořit novou**, zadejte název nové skupiny prostředků a pak vyberte **OK**.

6. Zadejte název účtu úložiště. Zvolený název musí být jedinečný v rámci Azure, mít délku 3 až 24 znaků a může obsahovat jen číslice a malá písmena.

7. Vyberte umístění účtu úložiště nebo přijměte výchozí umístění.

8. Přijměte všechny ostatní výchozí hodnoty, vyberte   **zkontrolovat & vytvořit**  >  **vytvořit**.

9. Po vytvoření účtu úložiště vyberte  **Přejít k prostředku**.

#### <a name="create-a-container"></a>Vytvoření kontejneru

1. Na stránce Přehled účtu úložiště vyberte  **objekty blob**.

2. Vyberte   **kontejner**, zadejte název kontejneru, zvolte   **objekt BLOB** (anonymní přístup pro čtení jenom pro objekty BLOB) a pak vyberte **OK**.

#### <a name="enable-cross-origin-resource-sharing-cors"></a>Povolit sdílení prostředků mezi zdroji (CORS)

Azure AD B2C kód v prohlížeči používá moderní a standardní přístup k načtení vlastního obsahu z adresy URL, kterou zadáte v toku uživatele. CORS povoluje vyžádání omezených prostředků na webové stránce z jiných domén.

1. V nabídce vyberte  **CORS**.

2. V případě  **povolených zdrojů** zadejte  `https://your-tenant-name.b2clogin.com` . Nahraďte název-tenanta názvem vašeho tenanta Azure AD B2C. Příklad:  `https://fabrikam.b2clogin.com`. Při zadávání názvu tenanta použijte všechna malá písmena.

3. U  **povolených metod** vyberte  **získat**, **Vložit** a  **Možnosti**.

4. U **povolených hlaviček** zadejte hvězdičku (*).

5. U **zveřejněných hlaviček** zadejte hvězdičku (*).

6. Do **maximálního stáří** zadejte 200.

   ![Registrace a přihlášení Arkose Labs](media/partner-arkose-labs/signup-signin-arkose.png)

7. Vyberte **Uložit**.

### <a name="part-2--set-up-a-back-end-server"></a>Část 2 – nastavení serveru back-end

Stáhněte si Git bash a postupujte podle následujících kroků:

1. Postupujte podle pokynů k [Vytvoření webové aplikace](../app-service/quickstart-php.md)až do zprávy "Blahopřejeme!Nasadili jste svoji první aplikaci v PHP do App Service "zobrazí se.

2. Otevřete místní složku a přejmenujte soubor **index. php** na **verify-token. php**.

3. Otevřete nově přejmenovaný soubor Verify-token. php a proveďte následující:

   a. Nahraďte obsah obsahem ze souboru Verify-token. php, který najdete v [úložišti GitHub](https://github.com/ArkoseLabs/Azure-AD-B2C).

   b. Nahraďte <private_key> na řádku 3 pomocí privátního klíče získaného na řídicím panelu Arkose Labs.

4. V okně místního terminálu potvrďte změny v Gitu a potom odešlete změny kódu do Azure, a to tak, že v Gitu bash zadáte následující:

   ``git commit -am "updated output"``

   ``git push azure main``  

### <a name="part-3---final-setup"></a>Část 3 – finální instalace

#### <a name="store-the-custom-html"></a>Uložení vlastního HTML

1. Otevřete soubor index.html uložený v [úložišti GitHub](https://github.com/ArkoseLabs/Azure-AD-B2C).

2. Nahraďte všechny instance `<tenantname>` s názvem vašeho tenanta b2C (jinými slovy `<tenantname>.b2clogin.com` ). Měly by existovat čtyři instance.

3. Nahraďte `<appname>` názvem aplikace, který jste vytvořili v části 2, kroku 1.

   ![Snímek obrazovky s rozhraním příkazového řádku Azure Arkose Labs](media/partner-arkose-labs/arkose-azure-cli.png)

4. `<public_key>`Na řádku 64 nahraďte veřejným klíčem, který jste získali z řídicího panelu Arkose Labs.

5. Nahrajte soubor index.html do úložiště objektů BLOB vytvořené výše.

6. Přejít na   >    >  **nahrávání** kontejneru úložiště.

#### <a name="set-up-azure-ad-b2c"></a>Nastavit Azure AD B2C

> [!NOTE]
> Pokud ho ještě nemáte, [Vytvořte klienta Azure AD B2C](tutorial-create-tenant.md) , který je propojený s vaším předplatným Azure.

1. Na základě informací [zde](tutorial-create-user-flows.md)vytvořte tok uživatele. Zastavte se, až se dostanete k části **Testování toku uživatele**.

2. Povolte JavaScript v [toku uživatele](javascript-and-page-layout.md).

3. Na stránce stejný tok uživatelů Povolit adresu URL vlastní stránky: Přejít na rozložení **stránky toku uživatele**  >    >  **použít vlastní obsah stránky**  =  **Ano**  >  **Vložit vlastní adresu URL stránky**.
Tato vlastní adresa URL stránky se získává z umístění souboru index.html v úložišti objektů BLOB.  

   ![Snímek obrazovky s adresou URL úložiště Arkose Labs](media/partner-arkose-labs/arkose-storage-url.png)

## <a name="test-the-user-flow"></a>Testování toku uživatele

1. Otevřete Azure AD B2C tenant a v části **zásady** vyberte **toky uživatelů**.

2. Vyberte dříve vytvořený tok uživatele.

3. Vyberte **Spustit tok uživatele** a vyberte nastavení:

   a. **Aplikace** – vyberte registrovanou aplikaci (ukázka je JWT).

   b. **Adresa URL odpovědi** – vyberte adresu URL pro přesměrování.

   c. Vyberte **Spustit tok uživatele**.

4. Projděte si tok registrace a vytvořte účet.

5. Odhlaste se.

6. Projděte si tok přihlášení.

7. Po výběru **pokračovat** se zobrazí skládanka Arkose Labs.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

- [Vlastní zásady v Azure AD B2C](custom-policy-overview.md)

- [Začínáme s vlastními zásadami v Azure AD B2C](custom-policy-get-started.md?tabs=applications)