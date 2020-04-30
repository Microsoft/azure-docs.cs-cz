---
title: Referenční dokumentace sady pro moderní čtečku
titleSuffix: Azure Cognitive Services
description: Sada moderní čtečka SDK obsahuje knihovnu JavaScriptu, která umožňuje integrovat moderní čtečku do vaší aplikace.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: cb88fb24ceed943d4104da6914959e4b79c35571
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231913"
---
# <a name="immersive-reader-sdk-reference-guide"></a>Referenční příručka k sadě pro moderní čtečku SDK

Sada moderní čtečka SDK obsahuje knihovnu JavaScriptu, která umožňuje integrovat moderní čtečku do vaší aplikace.

## <a name="functions"></a>Functions

Sada SDK zpřístupňuje funkce:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>launchAsync

Spustí moderní čtečku v rámci `iframe` ve vaší webové aplikaci.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

### <a name="parameters"></a>Parametry

| Název | Typ | Popis |
| ---- | ---- |------------ |
| `token` | řetězec | Ověřovací token Azure AD. |
| `subdomain` | řetězec | Vlastní subdoména prostředku pro moderní čtečku v Azure. |
| `content` | [Obsah](#content) | Objekt obsahující obsah, který se má zobrazit v moderní čtečce. |
| `options` | [Možnosti](#options) | Možnosti pro konfiguraci určitého chování moderního čtecího zařízení. Nepovinný parametr. |

### <a name="returns"></a>Vrací

Vrátí `Promise<LaunchResponse>`, který se vyřeší, když se nahraje moderní čtečka. `Promise` Překládá na [`LaunchResponse`](#launchresponse) objekt.

### <a name="exceptions"></a>Výjimky

Vrácený `Promise` [`Error`](#error) objekt se odmítne s objektem, pokud se nepovede načíst moderní čtecí zařízení. Další informace najdete v tématu [kódy chyb](#error-codes).

## <a name="close"></a>close

Zavře moderní čtečku.

Příkladem případu použití této funkce je, že je tlačítko Ukončit skryto nastavením ```hideExitButton: true``` v [Možnosti](#options). Pak jiné tlačítko (například šipky zpět pro mobilní hlavičku) může zavolat tuto ```close``` funkci, když na ni kliknete.

```typescript
close(): void;
```

## <a name="renderbuttons"></a>renderButtons

Tato funkce styly a aktualizuje prvky tlačítka pro moderní čtečku dokumentu. Pokud ```options.elements``` je k dispozici, bude tato funkce vykreslovat ```options.elements```tlačítka v rámci. V opačném případě se tlačítka vykreslí v rámci prvků dokumentu, které mají třídu ```immersive-reader-button```.

Tato funkce je automaticky volána sadou SDK při načtení okna.

Další možnosti vykreslování naleznete v tématu [volitelné atributy](#optional-attributes) .

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>Parametry

| Název | Typ | Popis |
| ---- | ---- |------------ |
| `options` | [RenderButtonsOptions](#renderbuttonsoptions) | Možnosti pro konfiguraci určitého chování funkce renderButtons Nepovinný parametr. |

## <a name="types"></a>Typy

### <a name="content"></a>Obsah

Obsahuje obsah, který se zobrazí v moderní čtečce.

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>Blok dat

Jeden blok dat, který se předává do obsahu moderního čtecího zařízení.

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

### <a name="launchresponse"></a>LaunchResponse

Obsahuje odpověď od volání `ImmersiveReader.launchAsync`.

```typescript
{
    container: HTMLDivElement;    // HTML element which contains the Immersive Reader iframe
    sessionId: string;            // Globally unique identifier for this session, used for debugging
}
```

### <a name="cookiepolicy-enum"></a>Výčet CookiePolicy

Výčet, který slouží k nastavení zásad pro použití souboru cookie pro moderní čtečku. Viz [Možnosti](#options).

```typescript
enum CookiePolicy { Disable, Enable }
```

#### <a name="supported-mime-types"></a>Podporované typy MIME

| Typ MIME | Popis |
| --------- | ----------- |
| Text/prostý | Prostý text. |
| text/html | Obsah HTML. [Další informace](#html-support)|
| Application/MathML + XML | Jazyk MathML (Matematická Markup Language). [Další informace](./how-to/display-math.md).
| application/vnd. openxmlformats-officedocument. WordprocessingML. Document | Dokument formátu Microsoft Word. docx.

### <a name="html-support"></a>Podpora HTML

| HTML | Podporovaný obsah |
| --------- | ----------- |
| Styly písma | Tučné, kurzíva, podtržení, kód, přeškrtnutí, horní index, dolní index |
| Neuspořádané seznamy | Disk, kruh, čtverec |
| Seřazené seznamy | Decimal, Upper-Alpha, nižší-alfa, horní – Roman, nižší – Roman |

Nepodporované značky budou vykresleny srovnatelně. Obrázky a tabulky se aktuálně nepodporují.

### <a name="options"></a>Možnosti

Obsahuje vlastnosti, které konfigurují určité chování moderního čtecího zařízení.

```typescript
{
    uiLang?: string;           // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;          // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number;         // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean;      // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
    onExit?: () => any;        // Executes when the Immersive Reader exits
    customDomain?: string;     // Reserved for internal use. Custom domain where the Immersive Reader webapp is hosted (default is null).
    allowFullscreen?: boolean; // The ability to toggle fullscreen (default is true).
    hideExitButton?: boolean;  // Whether or not to hide the Immersive Reader's exit button arrow (default is false). This should only be true if there is an alternative mechanism provided to exit the Immersive Reader (e.g a mobile toolbar's back arrow).
    cookiePolicy?: CookiePolicy; // Setting for the Immersive Reader's cookie usage (default is CookiePolicy.Disable). It's the responsibility of the host application to obtain any necessary user consent in accordance with EU Cookie Compliance Policy.
}
```

### <a name="renderbuttonsoptions"></a>RenderButtonsOptions

Možnosti pro vykreslování tlačítek pro moderní čtečku

```typescript
{
    elements: HTMLDivElement[];    // Elements to render the Immersive Reader buttons in
}
```

### <a name="error"></a>Chyba

Obsahuje informace o chybě.

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>Kódy chyb

| kód | Popis |
| ---- | ----------- |
| BadArgument | Zadaný argument je neplatný. Podrobnosti `message` naleznete v tématu. |
| Časový limit | V rámci zadaného časového limitu se nepovedlo načíst moderní čtečku. |
| TokenExpired | Platnost zadaného tokenu vypršela. |
| Omezené | Překročilo se omezení četnosti volání. |

## <a name="launching-the-immersive-reader"></a>Spuštění moderního čtecího zařízení

Sada SDK poskytuje výchozí styl pro tlačítko pro spuštění moderního čtecího zařízení. Pro povolení `immersive-reader-button` tohoto stylu použijte atribut class. Další podrobnosti najdete v [tomto článku](./how-to-customize-launch-button.md) .

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Volitelné atributy

Pomocí následujících atributů můžete nakonfigurovat vzhled a chování tlačítka.

| Atribut | Popis |
| --------- | ----------- |
| `data-button-style` | Nastaví styl tlačítka. Může být `icon`, `text`, nebo `iconAndText`. Výchozí hodnota `icon`je. |
| `data-locale` | Nastaví národní prostředí. Příkladem je `en-US` nebo `fr-FR`. Výchozí hodnota je `en`angličtina. |
| `data-icon-px-size` | Nastaví velikost ikony v pixelech. Výchozí hodnota je 20px. |

## <a name="browser-support"></a>Podpora prohlížečů

K dosažení nejlepšího prostředí pro moderní čtečku použijte nejnovější verze následujících prohlížečů.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Další kroky

* Prozkoumejte [sadu moderních čtenářů na GitHubu](https://github.com/microsoft/immersive-reader-sdk)
* [Rychlý Start: Vytvoření webové aplikace, která spustí moderní čtečku (C#)](./quickstart.md)
