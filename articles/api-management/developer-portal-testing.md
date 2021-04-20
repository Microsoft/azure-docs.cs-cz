---
title: Otestování samoobslužného portálu pro vývojáře
titleSuffix: Azure API Management
description: Naučte se, jak nastavit testy jednotek a ucelené testy pro váš samoobslužný API Management portál.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: ab6a7aa8fc4f11c34126415379294ef1e48fa286
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741628"
---
# <a name="test-the-self-hosted-developer-portal"></a>Otestování samoobslužného portálu pro vývojáře

Tento článek vysvětluje, jak nastavit testy jednotek a ucelené testy pro váš [Samoobslužný portál](developer-portal-self-host.md).

## <a name="unit-tests"></a>Testování částí

Test jednotek je přístup k ověření malých částí funkčnosti. Provádí se v izolaci od ostatních částí aplikace.

### <a name="example-scenario"></a>Ukázkový scénář

V tomto scénáři testujete ovládací prvek zadávání hesla. Akceptuje jenom hesla, která obsahují aspoň tento čas:

- Jedno písmeno

- Jedno číslo

- Jeden speciální znak
 
Proto test, který ověří tyto požadavky, vypadá takto:

```typescript
const passwordInput = new PasswordInput();

passwordInput.value = "";
expect(passwordInput.isValid).to.equal(false);

passwordInput.value = "password";
expect(passwordInput.isValid).to.equal(false);

passwordInput.value = "p@ssw0rd";
expect(passwordInput.isValid.to.equal(true);
```
 
### <a name="project-structure"></a>Struktura projektu

Je běžné udržet test jednotek vedle komponenty, kterou má ověřit.

```console
component.ts
component.spec.ts
```

### <a name="mock-http-requests"></a>Požadavky HTTP na modely

Existují případy, kdy očekáváte, že komponenta provede požadavky HTTP. Komponenta by měla reagovat správně na různé druhy odpovědí. Chcete-li simulovat konkrétní odpovědi HTTP, použijte `MockHttpClient` . Implementuje `HttpClient` rozhraní používané mnoha dalšími komponentami projektu.

```typescript
const httpClient = new MockHttpClient();

httpClient.mock()
    .get("/users/jane")
    .reply(200, {
        firstName: "Jane",
        lastName: "Doe"
    });
```

## <a name="end-to-end-tests"></a>Komplexní testy

Ucelený test provede konkrétní scénář uživatele s přesnými kroky, které očekáváte od uživatele. Ve webové aplikaci, jako je například portál Azure API Management Developer Portal, se uživatel posune prostřednictvím obsahu a vybere možnosti pro dosažení určitých výsledků. 

K replikaci navigace uživatelů můžete použít pomocné knihovny pro manipulaci s prohlížečem, jako je [Puppeteer](https://github.com/puppeteer/puppeteer). Umožňuje simulaci uživatelských akcí a automatizaci předpokládaných scénářů. Puppeteer také automaticky přebírá snímky stránek nebo komponent v jakékoli fázi testu. Porovnejte je později s předchozími výsledky a zachyťte odchylky a potenciální regrese.

### <a name="example-scenario"></a>Ukázkový scénář

V tomto scénáři je nutné ověřit tok přihlašování uživatele. Tento scénář by vyžadoval následující kroky:

1. Otevřete prohlížeč a přejděte na přihlašovací stránku.

1. Zadejte e-mailovou adresu.

1. Zadejte heslo.

1. Vyberte **Přihlásit se**.

1. Ověřte, že uživatel byl přesměrován na domovskou stránku.

1. Ověřte, zda stránka obsahuje položku nabídky **profil** . Je to jeden z možných ukazatelů, které jste úspěšně přihlásili.

Chcete-li spustit test automaticky, vytvořte skript s naprosto stejnými kroky:

```typescript
// 1. Open browser and navigate to the sign-in page.
const page = await browser.newPage();
await page.goto("https://contoso.com/signin");

// 2. Enter email.
await this.page.type("#email", "john.doe@contoso.com");

// 3. Enter password.
await this.page.type("#password", "p@s$w0rd");

// 4. Click Sign-in.
await this.page.click("#signin");

// 5. Verify that user got redirected to Home page.
expect(page.url()).to.equal("https://contoso.com");

// 6. Verify that the page includes the Profile menu item.
const profileMenuItem = await this.page.$("#profile");
expect(profileMenuItem).not.equals(null);
```

> [!NOTE]
> Řetězce jako "#email", "#password" a "#signin" jsou selektory typu CSS, které identifikují prvky jazyka HTML na stránce. Další informace najdete v části [Selektory úrovně 3](https://www.w3.org/TR/selectors-3/) specifikace W3C.

### <a name="ui-component-maps"></a>Mapy součástí uživatelského rozhraní

Tok uživatelů často projde stejné stránky nebo komponenty. Dobrým příkladem je hlavní nabídka webu, která se nachází na každé stránce. 

Vytvořte mapu komponenty uživatelského rozhraní, abyste se vyhnuli konfiguraci a aktualizaci stejných selektorů pro každý test. V předchozím příkladu byste například mohli nahradit kroky 2 až 6, a to pouze dvěma řádky:

```typescript
const signInWidget = new SigninBasicWidget(page);
await signInWidget.signInWithBasic({ email: "...", password: "..." });
```

### <a name="test-configuration"></a>Konfigurace testu

Některé scénáře mohou vyžadovat předem vytvořená data nebo konfiguraci. Například může být nutné automatizovat přihlašování uživatelů pomocí účtů sociálních médií. Je těžké vytvořit tato data rychle nebo snadno.

Pro účely tohoto účelu byste mohli přidat speciální konfigurační soubor do testovacího scénáře. Testovací skripty mohou z tohoto souboru vybírat požadovaná data. V závislosti na kanálu sestavení a testování mohou testy získat tajné údaje z pojmenovaného zabezpečeného úložiště.

Tady je příklad `validate.config.json` , který by byl uložený ve `src` složce projektu.

```json
{
    "environment": "validation",
    "urls": {
        "home": "https://contoso.com",
        "signin": "https://contoso.com/signin",
        "signup": "https://contoso.com/signup/"
    },
    "signin": {
        "firstName": "John",
        "lastName": "Doe",
        "credentials": {
            "basic": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            },
            "aadB2C": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            }
        }
    },
    "signup": {
        "firstName": "John",
        "lastName": "Doe",
        "credentials": {
            "basic": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            }
        }
    }
}

```

### <a name="headless-vs-normal-tests"></a>Normální testy bez periferních a běžných testů

Moderní prohlížeče, jako je Chrome nebo Microsoft Edge, umožňují spouštět automatizaci v režimu bez periferních procesů i v normálním režimu. Prohlížeč funguje bez grafického uživatelského rozhraní v bezobslužném režimu. Stále provádí stejnou stránku a manipulaci model DOM (Document Object Model) (DOM). Uživatelské rozhraní prohlížeče většinou není potřeba v kanálech pro doručování. V takovém případě je spuštění testů v režimu bezobslužné volby Skvělé.

Při vývoji testovacího skriptu je vhodné zjistit, co se přesně děje v prohlížeči. To je dobrý čas na použití normálního režimu.

Chcete-li přepínat mezi režimy, změňte možnost možnosti `headless` v `constants.ts` souboru. Je ve `tests` složce projektu:

```typescript
export const LaunchOptions = {
    headless: false
};
```

Další užitečnou možností je `slowMo` . Pozastaví provádění testu mezi každou akcí:

```typescript
export const LaunchOptions = {
    slowMo: 200 // milliseconds
};
```

## <a name="run-tests"></a>Spouštění testů

Existují dva předdefinované způsoby, jak spustit testy v tomto projektu:

**NPM – příkaz**

```console
npm run test
```

**Průzkumník testů**

Rozšíření Průzkumníka testů pro VS Code (například [Mocha Test Explorer](https://marketplace.visualstudio.com/items?itemName=hbenl.vscode-mocha-test-adapter)) má vhodné uživatelské rozhraní a možnost spouštět testy automaticky při každé změně zdrojového kódu:

:::image type="content" source="media/developer-portal-testing/visual-studio-code-test-explorer.png" alt-text="Snímek obrazovky Visual Studio Code Průzkumníku testů":::

## <a name="next-steps"></a>Další kroky

Další informace o portálu pro vývojáře:

- [Přehled vývojářského portálu pro službu Azure API Management](api-management-howto-developer-portal.md)

- [Samoobslužný hostování portálu pro vývojáře](developer-portal-self-host.md)
