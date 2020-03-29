---
title: Odkaz na sypkou imersive readeru
titleSuffix: Azure Cognitive Services
description: Sada Immersive Reader SDK obsahuje knihovnu JavaScriptu, která umožňuje integrovat immersive Reader do vaší aplikace.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: b20a3e6dd3b32b183bbf34dbefd76f0e4cd56b99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156399"
---
# <a name="immersive-reader-sdk-reference-guide"></a>Referenční příručka sady Immersive Reader SDK

Sada Immersive Reader SDK obsahuje knihovnu JavaScriptu, která umožňuje integrovat immersive Reader do vaší aplikace.

## <a name="functions"></a>Funkce

Sada SDK zveřejňuje funkce:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>launchAsync

Spustí immersive Reader v `iframe` rámci ve vaší webové aplikaci.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

### <a name="parameters"></a>Parametry

| Name (Název) | Typ | Popis |
| ---- | ---- |------------ |
| `token` | řetězec | Ověřovací token Azure AD. |
| `subdomain` | řetězec | Vlastní subdoména vašeho prostředku Immersive Reader v Azure. |
| `content` | [Obsah](#content) | Objekt obsahující obsah, který má být zobrazen v immersive reader. |
| `options` | [Možnosti](#options) | Možnosti konfigurace určitých chování immersive Reader. Nepovinný parametr. |

### <a name="returns"></a>Vrací

Vrátí `Promise<LaunchResponse>`, který řeší při načítání immersive Reader. Překládá `Promise` na [`LaunchResponse`](#launchresponse) objekt.

### <a name="exceptions"></a>Výjimky

Vrácené `Promise` bude odmítnut [`Error`](#error) s objektem, pokud immersive Reader nepodaří načíst. Další informace naleznete v [kódech chyb](#error-codes).

## <a name="close"></a>close

Zavře immersive Reader.

Příkladem případu použití této funkce je, pokud je ```hideExitButton: true``` tlačítko ukončení skryto nastavením v [možnostech](#options). Poté jiné tlačítko (například šipka zpět záhlaví mobilního ```close``` telefonu) může tuto funkci volat po klepnutí.

```typescript
close(): void;
```

## <a name="renderbuttons"></a>vykreslovat tlačítka

Tato funkce stylizuje a aktualizuje prvky tlačítka Immersive Reader dokumentu. Pokud ```options.elements``` je k dispozici, pak tato ```options.elements```funkce vykreslí tlačítka v rámci . V opačném případě budou tlačítka vykreslena v rámci prvků ```immersive-reader-button```dokumentu, které mají třídu .

Tato funkce je automaticky volána sadou SDK při načtení okna.

Další možnosti vykreslování najdete v [tématu Volitelné atributy.](#optional-attributes)

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>Parametry

| Name (Název) | Typ | Popis |
| ---- | ---- |------------ |
| `options` | [Možnosti renderů](#renderbuttonsoptions) | Možnosti konfigurace určitých chování funkce renderButtons. Nepovinný parametr. |

## <a name="types"></a>Typy

### <a name="content"></a>Obsah

Obsahuje obsah, který má být zobrazen v immersive Reader.

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>Blok dat

Jeden blok dat, který bude předán do obsahu immersive Reader.

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

### <a name="launchresponse"></a>LaunchResponse

Obsahuje odpověď z volání `ImmersiveReader.launchAsync`do .

```typescript
{
    container: HTMLDivElement;    // HTML element which contains the Immersive Reader iframe
    sessionId: string;            // Globally unique identifier for this session, used for debugging
}
```

### <a name="cookiepolicy-enum"></a>Výčtu CookiePolicy

Výčtu slouží k nastavení zásad pro immersive reader je cookie použití. Viz [možnosti](#options).

```typescript
enum CookiePolicy { Disable, Enable }
```

#### <a name="supported-mime-types"></a>Podporované typy MIME

| Typ MIME | Popis |
| --------- | ----------- |
| text/prostý | Prostý text. |
| text/html | obsah HTML. [Další informace](#html-support)|
| aplikace/mathml+xml | Matematický značkovací jazyk (MathML). [Další informace](./how-to/display-math.md).
| aplikace/vnd.openxmlformats-officedocument.wordprocessingml.document | Dokument ve formátu Docx aplikace Microsoft Word.

### <a name="html-support"></a>Podpora HTML

| HTML | Podporovaný obsah |
| --------- | ----------- |
| Styly písma | Tučné písmo, kurzíva, podtržení, kód, přeškrtávací, horní index, dolní index |
| Neuspořádané seznamy | Disk, Kruh, Čtverec |
| Seřazené seznamy | Desetinné, Horní Alfa, Dolní Alfa, Horní-Roman, Dolní-Roman |
| Hypertextové odkazy | Připravuje se |

Nepodporované značky budou vykresleny srovnatelně. Obrázky a tabulky nejsou aktuálně podporovány.

### <a name="options"></a>Možnosti

Obsahuje vlastnosti, které konfigurují určité chování immersive Reader.

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

### <a name="renderbuttonsoptions"></a>Možnosti renderů

Možnosti pro vykreslení tlačítek immersive reader.

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
| Chybný argument | Zadaný argument je `message` neplatný, podrobnosti naleznete v části Podrobnosti. |
| Časový limit | Immersive Reader se nepodařilo načíst v rámci zadaného časového času. |
| TokenExpired | Platnost zadaného tokenu vypršela. |
| Škrtil | Byl překročen limit rychlosti volání. |

## <a name="launching-the-immersive-reader"></a>Spuštění pohlcující čtečky

Sada SDK poskytuje výchozí styl pro tlačítko pro spuštění immersive reader. Pomocí `immersive-reader-button` atributu třídy povolte tento styl. Další podrobnosti naleznete v [tomto článku.](./how-to-customize-launch-button.md)

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Volitelné atributy

Následující atributy slouží ke konfiguraci vzhledu a chování tlačítka.

| Atribut | Popis |
| --------- | ----------- |
| `data-button-style` | Nastaví styl tlačítka. Může `icon`být `text`, `iconAndText`, nebo . Výchozí hodnota `icon`je na . |
| `data-locale` | Nastaví národní prostředí. Příkladem je `en-US` nebo `fr-FR`. Výchozí nastavení `en`je angličtina . |
| `data-icon-px-size` | Nastaví velikost ikony v obrazových bodech. Výchozí hodnota je 20 px. |

## <a name="browser-support"></a>Podpora prohlížečů

Pro nejlepší zážitek z immersive Readeru použijte nejnovější verze následujících prohlížečů.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Další kroky

* Prozkoumejte sadku [Immersive Reader SDK na GitHubu](https://github.com/microsoft/immersive-reader-sdk)
* [Úvodní příručka: Vytvoření webové aplikace, která spustí immersive Reader (C#)](./quickstart.md)
