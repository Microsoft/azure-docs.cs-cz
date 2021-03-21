---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 252f2e6275b01522b83e846201d190b39d2bbf21
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434449"
---
:::row:::
    :::column span="3":::
        Sada Speech SDK pro JavaScript je k dispozici jako balíček NPM, viz <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Microsoft-cognitiveservices Account-Speech-SDK </a> a její doprovodné úložiště GitHubu <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">– služby – řeč-sada SDK-js </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> I když je sada Speech SDK pro JavaScript k dispozici jako balíček NPM, mohou si klientské webové prohlížeče i Node.js spotřebovávat IT – zvažte různé důsledky architektury každého prostředí. Například <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">model objektu dokumentu (DOM) </a> není k dispozici pro serverové aplikace, stejně jako <a href="https://nodejs.org/api/fs.html" target="_blank">systém souborů </a> není k dispozici pro klientské aplikace.

### <a name="nodejs-package-manager-npm"></a>Správce balíčků Node.js (NPM)

Sadu Speech SDK pro JavaScript nainstalujete spuštěním následujícího `npm install` příkazu.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>Značka skriptu HTML

Alternativně můžete přímo do `<script>` elementu HTML přidat značku, která se `<head>` spoléhá na <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank"> **JSDelivr** npm syndikát</a>.

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@latest/distrib/browser/microsoft.cognitiveservices.speech.sdk.bundle-min.js">
</script>
```

Další informace najdete v tématu <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">rychlý Start pro sadu Speech pro webový prohlížeč </a>.
