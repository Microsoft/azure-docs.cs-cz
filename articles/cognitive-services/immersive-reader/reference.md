---
title: Referenční dokumentace sady pro moderní čtečku
titleSuffix: Azure Cognitive Services
description: Moderní čtečka SDK je knihovna JavaScriptu, která umožňuje integrovat moderní čtečku do vaší webové aplikace.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 09244b634fa2603a7dc92af3c78d171f8d6bd9df
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903111"
---
# <a name="immersive-reader-sdk-reference-guide"></a>Referenční příručka k sadě pro moderní čtečku SDK

Moderní čtečka SDK je knihovna JavaScriptu, která umožňuje integrovat moderní čtečku do vaší webové aplikace.

## <a name="functions"></a>Funkce

Sada SDK zpřístupňuje funkce:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>launchAsync

Spustí moderní čtečku v rámci `iframe` ve vaší webové aplikaci.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

### <a name="parameters"></a>Parametry

| Název | Typ | Popis |
| ---- | ---- |------------ |
| `token` | řetězec | Ověřovací token Azure AD. Podívejte se na [postupy ověřování Azure AD](./azure-active-directory-authentication.md). |
| `subdomain` | řetězec | Vlastní subdoména prostředku pro moderní čtečku v Azure. Podívejte se na [postupy ověřování Azure AD](./azure-active-directory-authentication.md). |
| `content` | [Obsah](#content) | Objekt obsahující obsah, který se má zobrazit v moderní čtečce. |
| `options` | [Možnosti](#options) | Možnosti pro konfiguraci určitého chování moderního čtecího zařízení. Volitelné. |

### <a name="returns"></a>Vrátí

Vrátí `Promise<HTMLDivElement>`, který se vyřeší, když se nahraje moderní čtečka. `Promise` se přeloží na `div` element, jehož jediným podřízeným prvkem je prvek `iframe`, který obsahuje stránku s atraktivním čtečkou.

### <a name="exceptions"></a>Výjimky

Vrácený `Promise` budou odmítnuty s objektem [`Error`](#error) v případě, že se moderní čtečka nepovede načíst. Další informace najdete v tématu [kódy chyb](#error-codes).

## <a name="close"></a>zavřít

Zavře moderní čtečku.

Příkladem případu použití této funkce je, že je tlačítko Ukončit skryté nastavením ```hideExitButton: true``` v [Možnosti](#options). Pak jiné tlačítko (například šipka back-šipky pro mobilní hlavičku) může tuto funkci ```close``` vyvolat, když na ni kliknete.

```typescript
close(): void;
```

## <a name="renderbuttons"></a>renderButtons

Tato funkce styly a aktualizuje prvky tlačítka pro moderní čtečku dokumentu. Pokud je k dispozici ```options.elements```, tato funkce vykreslí tlačítka v rámci ```options.elements```. V opačném případě se tlačítka vykreslí v rámci prvků dokumentu, které mají třídu ```immersive-reader-button```.

Tato funkce je automaticky volána sadou SDK při načtení okna.

Další možnosti vykreslování naleznete v tématu [volitelné atributy](#optional-attributes) .

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>Parametry

| Název | Typ | Popis |
| ---- | ---- |------------ |
| `options` | [RenderButtonsOptions](#renderbuttonsoptions) | Možnosti pro konfiguraci určitého chování funkce renderButtons Volitelné. |

## <a name="types"></a>Typy

### <a name="content"></a>Obsah

Obsahuje obsah, který se zobrazí v moderní čtečce.

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>Indikátor

Jeden blok dat, který se předává do obsahu moderního čtecího zařízení.

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

#### <a name="supported-mime-types"></a>Podporované typy MIME

| Typ MIME | Popis |
| --------- | ----------- |
| Text/prostý | Prostý text. |
| text/HTML | Obsah HTML. [Další informace](#html-support)|
| Application/MathML + XML | Jazyk MathML (Matematická Markup Language). [Další informace](https://developer.mozilla.org/en-US/docs/Web/MathML)
| application/vnd. openxmlformats-officedocument. WordprocessingML. Document | Dokument formátu Microsoft Word. docx.

### <a name="html-support"></a>Podpora HTML
| HTML | Podporovaný obsah |
| --------- | ----------- |
| Styly písma | Tučné, kurzíva, podtržení, kód, přeškrtnutí, horní index, dolní index |
| Neuspořádané seznamy | Disk, kruh, čtverec |
| Seřazené seznamy | Decimal, Upper-Alpha, nižší-alfa, horní – Roman, nižší – Roman |
| Vytváření | Připravuje se |

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

| Kód | Popis |
| ---- | ----------- |
| BadArgument | Zadaný argument je neplatný, podrobnosti najdete v `message`. |
| Prodlev | V rámci zadaného časového limitu se nepovedlo načíst moderní čtečku. |
| TokenExpired | Platnost zadaného tokenu vypršela. |
| Omezené | Překročilo se omezení četnosti volání. |

## <a name="launching-the-immersive-reader"></a>Spuštění moderního čtecího zařízení

Sada SDK poskytuje výchozí styl pro tlačítko pro spuštění moderního čtecího zařízení. Pro povolení tohoto stylu použijte atribut třídy `immersive-reader-button`.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Volitelné atributy

Pomocí následujících atributů můžete nakonfigurovat vzhled a chování tlačítka.

| Atribut | Popis |
| --------- | ----------- |
| `data-button-style` | Nastaví styl tlačítka. Může být `icon`, `text`nebo `iconAndText`. Výchozí hodnota je `icon`. |
| `data-locale` | Nastaví národní prostředí. Například `en-US` nebo `fr-FR`. Výchozí hodnota je `en`angličtiny. |
| `data-icon-px-size` | Nastaví velikost ikony v pixelech. Výchozí hodnota je 20px. |

## <a name="browser-support"></a>Podpora prohlížeče

K dosažení nejlepšího prostředí pro moderní čtečku použijte nejnovější verze následujících prohlížečů.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Další kroky

* Prozkoumejte [sadu moderních čtenářů na GitHubu](https://github.com/microsoft/immersive-reader-sdk)
* [Rychlý Start: Vytvoření webové aplikace, která spustí moderní čtečku (C#)](./quickstart.md)
