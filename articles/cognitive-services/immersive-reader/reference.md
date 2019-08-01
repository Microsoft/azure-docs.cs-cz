---
title: Referenční dokumentace sady pro moderní čtečku
titleSuffix: Azure Cognitive Services
description: Referenční dokumentace k sadě pro moderní čtečku
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 1a616bce8c161825853b1966769d9505595d95de
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688320"
---
# <a name="immersive-reader-sdk-reference"></a>Referenční dokumentace sady pro moderní čtečku

Moderní čtečka SDK je knihovna JavaScriptu, která umožňuje integrovat moderní čtečku do vaší webové aplikace.

## <a name="functions"></a>Funkce

Sada SDK zpřístupňuje jednu funkci, `ImmersiveReader.launchAsync(token, subdomain, content, options)`.

### <a name="launchasync"></a>launchAsync

Spustí moderní čtečku v rámci `iframe` ve vaší webové aplikaci.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

#### <a name="parameters"></a>Parametry

| Name | Typ | Popis |
| ---- | ---- |------------ |
| `token` | řetězec | Ověřovací token Azure AD. Podívejte se na [postupy ověřování Azure AD](./azure-active-directory-authentication.md). |
| `subdomain` | řetězec | Vlastní subdoména prostředku pro moderní čtečku v Azure. Podívejte se na [postupy ověřování Azure AD](./azure-active-directory-authentication.md). |
| `content` | [Obsah](#content) | Objekt obsahující obsah, který se má zobrazit v moderní čtečce. |
| `options` | [Možnosti](#options) | Možnosti pro konfiguraci určitého chování moderního čtecího zařízení. Volitelné. |

#### <a name="returns"></a>Vrací

Vrátí, `Promise<HTMLDivElement>` který se vyřeší, když se nahraje moderní čtečka. Překládá na prvek, jehož jediným podřízeným prvkem `iframe` je prvek, který obsahuje stránku s moderní čtečkou. `div` `Promise`

#### <a name="exceptions"></a>Výjimky

Vrácený `Promise` [`Error`](#error) objekt se odmítne s objektem, pokud se nepovede načíst moderní čtecí zařízení. Další informace najdete v tématu [kódy chyb](#error-codes).

## <a name="types"></a>Typy

### <a name="content"></a>Obsah

Obsahuje obsah, který se zobrazí v moderní čtečce.

```typescript
{
    title?: string;      // Title text shown at the top of the Immersive Reader (optional)
    chunks: [ {          // Array of chunks
        content: string; // Plain text string
        lang?: string;   // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
        mimeType?: string; // MIME type of the content (optional). Defaults to 'text/plain' if not specified.
    } ];
}
```

#### <a name="supported-mime-types"></a>Podporované typy MIME

| Typ MIME | Popis |
| --------- | ----------- |
| Text/prostý | Prostý text. |
| Application/MathML + XML | Jazyk MathML (Matematická Markup Language). [Další informace](https://developer.mozilla.org/en-US/docs/Web/MathML).

### <a name="options"></a>Možnosti

Obsahuje vlastnosti, které konfigurují určité chování moderního čtecího zařízení.

```typescript
{
    uiLang?: string;   // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;  // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number; // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean; // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
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
| BadArgument | Zadaný argument je neplatný. Podrobnosti `message` naleznete v tématu. |
| časový limit | V rámci zadaného časového limitu se nepovedlo načíst moderní čtečku. |
| TokenExpired| Platnost zadaného tokenu vypršela. |

## <a name="launching-the-immersive-reader"></a>Spuštění moderního čtecího zařízení

Sada SDK poskytuje výchozí styl pro tlačítko pro spuštění moderního čtecího zařízení. Pro povolení tohoto stylu použijte atribut Class.`immersive-reader-button`

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Volitelné atributy

Pomocí následujících atributů můžete nakonfigurovat vzhled a chování tlačítka.

| Atribut | Popis |
| --------- | ----------- |
| `data-button-style` | Nastaví styl tlačítka. Může být `icon`, `text`, nebo `iconAndText`. Výchozí hodnota je `icon`. |
| `data-locale` | Nastaví národní prostředí, např. `en-US`. `fr-FR` Výchozí hodnota je angličtina. |
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
* [Rychlé zprovoznění: Vytvoření webové aplikace, která spustí moderní čtečku (C#)](./quickstart.md)