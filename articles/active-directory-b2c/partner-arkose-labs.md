---
title: Kurz konfigurace Azure Active Directory B2C s využitím Arkose Labs
titleSuffix: Azure AD B2C
description: Kurz konfigurace Azure Active Directory B2C s využitím Arkose Labs pro identifikaci rizikových a podvodných uživatelů
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/18/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 46f117b13909c2d9624b88e9f5d9a62c4c646e51
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500288"
---
# <a name="tutorial-configure-arkose-labs-with-azure-active-directory-b2c"></a>Kurz: Konfigurace Arkose Labs pomocí Azure Active Directory B2C

V tomto ukázkovém kurzu se dozvíte, jak integrovat Azure Active Directory (AD) B2C ověřování pomocí [Arkose Labs](https://www.arkoselabs.com/). Arkose Labs můžou organizacím pomáhat s útoky na roboty, útoky při převzetí účtů a podvodnými otevřenými účty.  

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, budete potřebovat:

- Předplatné Azure. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).

- [Tenant Azure AD B2C](tutorial-create-tenant.md) , který je propojený s vaším předplatným Azure.

- Účet [Arkose Labs](https://www.arkoselabs.com/book-a-demo/) .

## <a name="scenario-description"></a>Popis scénáře

Integrace Arkose Labs zahrnuje tyto komponenty:

- **Arkose Labs** – podvod a služba zneužití pro ochranu před roboty a jiným automatizovaným zneužitím.

- **Azure AD B2C uživatelských toků přihlášení** – prostředí pro registraci, které bude používat službu Arkose Labs. Bude používat vlastní konektory HTML a JavaScript a rozhraní API pro integraci se službou Arkose Labs.

- Služba **Azure Functions** – koncový bod rozhraní API hostovaný vámi, který pracuje s funkcí KONEKTORy API Toto rozhraní API zodpovídá za ověřování na straně serveru pro token relace Arkose Labs.

Následující diagram popisuje, jak se Arkose Labs integrují s Azure AD B2C.

![Obrázek znázorňuje diagram architektury Arkose Labs.](media/partner-arkose-labs/arkose-labs-architecture-diagram.png)

| Krok  | Description |
|---|---|
|1     | Uživatel se přihlásí a vytvoří účet. Když uživatel vybere odeslat, zobrazí se výzva k vynucení Arkose Labs.         |
|2     |  Jakmile uživatel tuto výzvu dokončí, Azure AD B2C odešle stav do Arkose Labs, aby vygeneroval token. |
|3     |  Arkose Labs vygeneruje token a pošle ho zpátky do Azure AD B2C.   |
|4     |  Azure AD B2C volá zprostředkující webové rozhraní API, aby se předal formulář pro registraci.      |
|5     |  Zprostředkující webové rozhraní API pošle registrační formulář Arkose testovacímu prostředí pro ověření tokenu.    |
|6     | Arkose testovací procesy a odesílá výsledky ověření zpátky do zprostředkujícího webového rozhraní API.|
|7     | Zprostředkující webové rozhraní API pošle výsledek úspěch nebo neúspěchu z výzvy k Azure AD B2C. |
|8     | Pokud se výzva úspěšně dokončí, odešle se do Azure AD B2C formulář pro registraci a Azure AD B2C dokončí ověřování.|

## <a name="onboard-with-arkose-labs"></a>Zprovoznění s Arkose Labs

1. Kontaktujte [Arkose](https://www.arkoselabs.com/book-a-demo/) a vytvořte účet.

2. Po vytvoření účtu přejděte na https://dashboard.arkoselabs.com/login  

3. V řídicím panelu přejděte na nastavení lokality a vyhledejte svůj veřejný klíč a privátní klíč. Tyto informace budou potřeba později ke konfiguraci Azure AD B2C. Hodnoty veřejných a privátních klíčů jsou označovány jako `ARKOSE_PUBLIC_KEY` a `ARKOSE_PRIVATE_KEY` v [ukázkovém kódu](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose).

## <a name="integrate-with-azure-ad-b2c"></a>Integrace s Azure AD B2C

### <a name="part-1--create-a-arkosesessiontoken-custom-attribute"></a>Část 1 – Vytvoření vlastního atributu ArkoseSessionToken

Chcete-li vytvořit vlastní atribut, použijte následující postup:  

1. Přejít na **Azure Portal**  >  **Azure AD B2C**

2. Vybrat **atributy uživatele**

3. Vyberte **Přidat**.

4. Jako název atributu zadejte **ArkoseSessionToken** .

5. Vyberte **Vytvořit**.

Přečtěte si další informace o [vlastních atributech](https://docs.microsoft.com/azure/active-directory-b2c/user-flow-custom-attributes?pivots=b2c-user-flow).

### <a name="part-2---create-a-user-flow"></a>Část 2 – Vytvoření toku uživatele

Tok uživatele může být buď pro **registraci** , **přihlašování, nebo jen** k **registraci**. Tok uživatele Arkose Labs se zobrazí jenom během registrace.

1. Přečtěte si [pokyny](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) k vytvoření toku uživatele. Pokud používáte existující tok uživatele, musí se jednat o **doporučený typ verze (Náhled nové generace)** .

2. V nastavení toku uživatele přejít na **atributy uživatele** a vyberte deklaraci identity **ArkoseSessionToken** .

![Obrázek ukazuje, jak vybrat vlastní atributy](media/partner-arkose-labs/select-custom-attribute.png)

### <a name="part-3---configure-custom-html-javascript-and-page-layouts"></a>Část 3 – Konfigurace vlastních rozložení HTML, JavaScriptu a stránek

Přejít na poskytnutý [skript HTML](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html). Soubor obsahuje šablonu HTML s `<script>` značkami jazyka JavaScript, která provede tři věci:

1. Načtěte skript Arkose Labs, který vykresluje widget Arkose Labs a provede ověření Arkose Labs na straně klienta.

2. Skryjte `extension_ArkoseSessionToken` vstupní element a popisek odpovídající `ArkoseSessionToken` vlastnímu atributu z uživatelského rozhraní zobrazeného uživateli.

3. Když uživatel dokončí výzvu Arkose Labs, Arkose Labs ověří odpověď uživatele a vygeneruje token. Zpětné volání `arkoseCallback` ve vlastním JavaScriptu nastaví hodnotu `extension_ArkoseSessionToken` na hodnotu vygenerovaného tokenu. Tato hodnota se odešle do koncového bodu rozhraní API, jak je popsáno v následující části.

    V [tomto článku](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/214176229/Standard+Setup) se dozvíte o ověřování na straně klienta Arkose Labs.

Postupujte podle kroků uvedených v části použití vlastního HTML a JavaScriptu pro tok uživatele.

1. Upravte soubor [selfAsserted.html](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html) tak, aby `<ARKOSE_PUBLIC_KEY>` odpovídal hodnotě, kterou jste vygenerovali pro ověřování na straně klienta, a použili jste k načtení skriptu Arkose Labs pro svůj účet.

2. Hostovat stránku HTML na webovém koncovém bodu s povoleným sdílením prostředků mezi zdroji (CORS). [Vytvořte účet úložiště objektů BLOB v Azure](https://docs.microsoft.com/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal) a [nakonfigurujte CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services).

  >[!NOTE]
  >Máte-li vlastní kód HTML, zkopírujte a vložte `<script>` prvky do stránky HTML.

3. Pomocí těchto kroků nakonfigurujete rozložení stránky.

   a. Z Azure Portal, přejít na **Azure AD B2C**

   b. Přejděte na **toky uživatelů** a vyberte tok uživatele.

   c. Výběr **rozložení stránky**

   d. Výběr **rozložení přihlašovací stránky místního účtu**

   e. Přepnout **použití vlastního obsahu stránky** na **Ano**

   f. Vložte identifikátor URI, ve kterém se používá vlastní kód HTML. **vlastní obsah stránky**

   například Pokud používáte zprostředkovatele sociální identity, opakujte **Krok e** a **f** pro rozložení **stránky pro přihlášení k účtu sociální** sítě.

   ![Obrázek znázorňující rozložení stránky](media/partner-arkose-labs/page-layouts.png)

4. V toku uživatele, přejít na **vlastnosti** a vyberte **Povolit JavaScript** – vynutit rozložení stránky (Preview). Další informace najdete v tomto [článku](https://docs.microsoft.com/azure/active-directory-b2c/javascript-and-page-layout?pivots=b2c-user-flow) .

### <a name="part-4---create-and-deploy-your-api"></a>Část 4 – Vytvoření a nasazení rozhraní API

Nainstalujte [Azure Functions rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pro Visual Studio Code.

>[!Note]
>Postup uvedený v této části předpokládá, že používáte Visual Studio Code k nasazení funkce Azure Functions. K nasazení můžete také použít Azure Portal, terminál nebo příkazový řádek nebo jakýkoli jiný Editor kódu.

#### <a name="run-the-api-locally"></a>Místní spuštění rozhraní API

1. Přejděte na rozšíření Azure ve Visual Studio Code na levém navigačním panelu. Vyberte **místní složku projektu** reprezentující místní funkci Azure Functions.

2. Stiskněte klávesu **F5** nebo   >  spusťte ladicí program pomocí nabídky **Spustit ladění** a připojte se k Azure Functionsmu hostiteli. Tento příkaz automaticky používá jedinou konfiguraci ladění, kterou vytvořila funkce Azure.

3. Rozšíření funkce Azure automaticky vygeneruje několik souborů pro místní vývoj, závislosti instalace a nainstaluje základní nástroje Functions, pokud ještě nejsou přítomné. Tyto nástroje vám pomůžou s laděním.

4. Výstup z nástroje Core Function se zobrazí na panelu **terminálu** Visual Studio Code. Po spuštění hostitele **Alt + kliknutí na** místní adresu URL zobrazenou ve výstupu otevřete prohlížeč a spusťte funkci. V Průzkumníku Azure Functions klikněte pravým tlačítkem myši na funkci, aby se zobrazila adresa URL místně hostované funkce.

K opětovnému nasazení místní instance během testování zopakujte kroky 1 až 4.

#### <a name="add-environment-variables"></a>Přidat proměnné prostředí

Tato ukázka chrání koncový bod webového rozhraní API pomocí [ověřování HTTP Basic](https://tools.ietf.org/html/rfc7617).

Uživatelské jméno a heslo se ukládají jako proměnné prostředí a ne jako součást úložiště. Další informace najdete v tématu [local.settings.jsv](https://docs.microsoft.com/azure/azure-functions/functions-run-local?tabs=macos%2Ccsharp%2Cbash#local-settings-file) souboru.

1. Vytvořit local.settings.jsv souboru v kořenové složce

2. Zkopírujte následující kód a vložte ho do souboru:

```
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "BASIC_AUTH_USERNAME": "<USERNAME>",
    "BASIC_AUTH_PASSWORD": "<PASSWORD>",
    "ARKOSE_PRIVATE_KEY": "<ARKOSE_PRIVATE_KEY>",
    "B2C_EXTENSIONS_APP_ID": "<B2C_EXTENSIONS_APP_ID>"
  }
}
```
Hodnoty **BASIC_AUTH_USERNAME** a **BASIC_AUTH_PASSWORD** budou přihlašovací údaje, které se použijí k ověření volání rozhraní API vaší funkce Azure Functions. Vyberte požadované hodnoty.

`<ARKOSE_PRIVATE_KEY>`Je tajný kód na straně serveru, který jste vygenerovali ve službě Arkose Labs. Slouží k volání [rozhraní API ověřování na straně serveru Arkose Labs](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/266214758/Server-Side+Instructions) za účelem ověření hodnoty `ArkoseSessionToken` generované front-endu.

`<B2C_EXTENSIONS_APP_ID>`Je ID aplikace aplikace, kterou používá Azure AD B2C k ukládání vlastních atributů v adresáři. Toto ID aplikace můžete najít tak, že přejdete na Registrace aplikací, vyhledáte B2C-Extensions-App a zkopírujete ID aplikace (klienta) z podokna **přehledu** . Odeberte `-` znaky.

![Obrázek zobrazuje hledání podle ID aplikace](media/partner-arkose-labs/search-app-id.png)

#### <a name="deploy-the-application-to-the-web"></a>Nasazení aplikace na web

1. Postupujte podle kroků uvedených v [tomto](https://docs.microsoft.com/azure/javascript/tutorial-vscode-serverless-node-04) průvodci a nasaďte funkci Azure Functions do cloudu. Zkopírujte adresu URL webu koncového bodu vaší funkce Azure Functions.

2. Po nasazení vyberte možnost **nahrávání nastavení** . Vaše proměnné prostředí nahraje do [nastavení aplikace](https://docs.microsoft.com/azure/azure-functions/functions-develop-vs-code?tabs=csharp#application-settings-in-azure) služby App Service. Tato nastavení aplikace lze také konfigurovat nebo [spravovat prostřednictvím Azure Portal.](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings)

Další informace o Visual Studio Code vývoji pro Azure Functions najdete v [tomto článku](https://docs.microsoft.com/azure/azure-functions/functions-develop-vs-code?tabs=csharp#republish-project-files) .

#### <a name="configure-and-enable-the-api-connector"></a>Konfigurace a povolení konektoru API

[Vytvořte konektor API](https://docs.microsoft.com/azure/active-directory-b2c/add-api-connector) a povolte ho pro tok uživatele. Vaše konfigurace konektoru API by měla vypadat takto:

![Obrázek ukazuje, jak nakonfigurovat konektor API.](media/partner-arkose-labs/configure-api-connector.png)

- **Adresa URL koncového bodu** – je adresa URL funkce, kterou jste zkopírovali dříve během nasazování funkce Azure Functions.

- **Uživatelské jméno a heslo** – jedná se o uživatelské jméno a heslo, které jste dříve definovali jako proměnné prostředí.

Pokud chcete povolit konektor API, v nastavení **konektoru rozhraní API** pro tok uživatele vyberte konektor rozhraní API, který se má vyvolávat při **vytváření kroku uživatele** . Tím dojde k vyvolání rozhraní API, když uživatel vybere **vytvořit** v toku registrace. Rozhraní API provede ověřování na straně serveru `ArkoseSessionToken` hodnoty, které bylo nastaveno pomocí zpětného volání widgetu Arkose `arkoseCallback` .

![Obrázek ukazuje povolení konektoru rozhraní API](media/partner-arkose-labs/enable-api-connector.png)

## <a name="test-the-user-flow"></a>Testování toku uživatele

1. Otevřete Azure AD B2C tenant a v části zásady vyberte **toky uživatelů**.

2. Vyberte dříve vytvořený tok uživatele.

3. Vyberte **Spustit tok uživatele** a vyberte nastavení:

   a. Aplikace: vyberte registrovanou aplikaci (ukázka je JWT).

   b. Adresa URL odpovědi: vyberte adresu URL pro přesměrování.

   c. Vyberte **Spustit tok uživatele**.

4. Projděte si tok registrace a vytvořte účet.

5. Odhlásit se

6. Projděte si tok přihlášení  

7. Po výběru **pokračovat** se zobrazí skládanka Arkose Labs.

## <a name="additional-resources"></a>Další zdroje informací

- [Ukázkové kódy](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose) Azure AD B2C toku uživatelů při registraci

- [Vlastní zásady v Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Začínáme s vlastními zásadami v Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
