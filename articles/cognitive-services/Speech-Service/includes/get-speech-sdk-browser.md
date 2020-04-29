---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 5545b10a4228448d49849e7cd52728febf14ce2d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399943"
---
:::row:::
    :::column span="3":::
        Sada JavaScript Speech SDK je k dispozici jako balíček NPM, viz <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Microsoft-cognitiveservices Account-Speech- <span class="docon docon-navigate-external x-hidden-focus"></span> SDK</a> a je to doprovodné služba pro rozpoznávání úložiště GitHub <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">-Services-Speech- <span class="docon docon-navigate-external x-hidden-focus"> </span>SDK-js </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> I když je sada JavaScript Speech SDK k dispozici jako balíček NPM, může to být tím, že oba klientské prohlížeče i node. js mohou spotřebovat IT – zvažte různé důsledky architektury každého prostředí. Například <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">model objektu dokumentu (DOM) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> není k dispozici pro serverové aplikace, stejně jako <a href="https://nodejs.org/api/fs.html" target="_blank">systém <span class="docon docon-navigate-external x-hidden-focus"></span> souborů</a> není k dispozici pro klientské aplikace.

### <a name="nodejs-package-manager-npm"></a>Správce balíčků Node. js (NPM)

Chcete-li nainstalovat sadu JavaScript Speech SDK, spusťte `npm install` následující příkaz.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>Značka skriptu HTML

Alternativně můžete přímo do `<script>` `<head>` elementu HTML přidat značku, která se spoléhá na <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank"> **JSDelivr** npm syndikát <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@1.10.1/distrib/lib/microsoft.cognitiveservices.speech.sdk.min.js">
</script>
```

Další informace najdete v tématu <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">rychlý Start <span class="docon docon-navigate-external x-hidden-focus"> </span>pro sadu Speech pro webový prohlížeč </a>.
