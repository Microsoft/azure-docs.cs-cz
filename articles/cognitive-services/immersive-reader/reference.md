---
title: Odkaz na sadu SDK atraktivní čtečky
titleSuffix: Azure Cognitive Services
description: Referenční informace pro atraktivní čtečky SDK
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 485e8626af4266492e02d4f9fbe4af486e10c082
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718387"
---
# <a name="immersive-reader-sdk-reference"></a>Odkaz na sadu SDK atraktivní čtečky

Atraktivní čtečky SDK je knihovna JavaScript, která umožňuje integrovat atraktivní čtečky do webové aplikace.

## <a name="functions"></a>Funkce

Sada SDK poskytuje jedinou funkci `ImmersiveReader.launchAsync(token, resourceName, content, options)`.

### <a name="launchasync"></a>launchAsync

Spustí atraktivní čtecí modul v rámci `iframe` ve webové aplikaci.

```typescript
launchAsync(token: string, resourceName: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

#### <a name="parameters"></a>Parametry

| Name | Typ | Popis |
| ---- | ---- |------------ |
| `token` | řetězec | Přístupový token získaných z volání `issueToken` koncového bodu. |
| `resourceName` | řetězec | Vyhrazená. Musí být nastaveno na `null`. |
| `content` | [Obsah](#content) | Objekt, který obsahuje obsah zobrazený v moderním čtečky. |
| `options` | [Možnosti](#options) | Možnosti pro konfiguraci určitého chování, atraktivní čtecí zařízení. Volitelné. |

#### <a name="returns"></a>Vrací

Vrátí `Promise<HTMLDivElement>` který řeší při načtení atraktivní čtečky. `Promise` Přeloží na `div` element, jehož jediným podřízeným je `iframe` element, který obsahuje stránku atraktivní čtečky.

#### <a name="exceptions"></a>Výjimky

Vrácený `Promise` budou odmítnuty [ `Error` ](#error) objektu, pokud se nepodaří načíst atraktivní čtečky. Další informace najdete v tématu [kódy chyb](#error-codes).

## <a name="types"></a>Typy

### <a name="content"></a>Obsah

Obsahuje obsah zobrazený v moderním čtečky.

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
| text/plain | Prostý text. |
| aplikace/mathml + xml | Matematické Markup Language (MathML). [Další informace](https://developer.mozilla.org/en-US/docs/Web/MathML).

### <a name="options"></a>Možnosti

Obsahuje vlastnosti, které konfigurují určitého chování, atraktivní čtecí zařízení.

```typescript
{
    uiLang?: string;   // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;  // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number; // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean; // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
}
```

### <a name="error"></a>Chyba

Obsahuje informace o této chybě.

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>Kódy chyb

| Kód | Popis |
| ---- | ----------- |
| BadArgument | Zadaný argument je neplatný, naleznete v tématu `message` podrobnosti. |
| časový limit | Dokonalé čtecí modul se nepodařilo načíst v rámci zadaného časového limitu. |
| TokenExpired| Platnost zadaného tokenu. |

## <a name="launching-the-immersive-reader"></a>Spouští se atraktivní čtečky

Sada SDK poskytuje výchozí styl tlačítka pro spouštění atraktivní čtečky. Použití `immersive-reader-button` atribut třídy, které chcete povolit tento styl.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Volitelné atributy

Konfigurace vzhledu a chování tlačítka použijte následující atributy.

| Atribut | Popis |
| --------- | ----------- |
| `data-button-style` | Nastaví styl tlačítka. Může být `icon`, `text`, nebo `iconAndText`. Výchozí hodnota je `icon`. |
| `data-locale` | Nastaví národní prostředí, například `en-US`, `fr-FR`. Výchozí hodnota je angličtina. |
| `data-icon-px-size` | Nastaví velikost ikony v pixelech. Výchozí hodnota je {20px. |

## <a name="browser-support"></a>Podpora prohlížeče

Pro dosažení co nejlepších výsledků atraktivní čtečkou pomocí nejnovější verze následujících prohlížečů.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Další postup

* Prozkoumejte [atraktivní čtečky SDK na Githubu](https://github.com/Microsoft/immersive-reader-sdk)
* [Rychlé zprovoznění: Vytvoření webové aplikace, které spouští atraktivní Reader (C#)](./quickstart.md)