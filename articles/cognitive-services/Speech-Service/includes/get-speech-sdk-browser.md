---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 1f1512fbd766faed3922e4bdf8a47dba0de69864
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656495"
---
:::row:::
    :::column span="3":::
        Sada JavaScript Speech SDK je k dispozici jako balíček npm, viz <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">microsoft-cognitiveservices-speech-sdk <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a je to doprovodné úložiště GitHub <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitive-services-speech-sdk-js <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Přestože javascriptová sada Speech SDK je k dispozici jako balíček npm, tak jak klientské webové prohlížeče, tak Node.js jej mohou využívat - zvažte různé architektonické důsledky každého prostředí. Například <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">objektový model dokumentu <span class="docon docon-navigate-external x-hidden-focus"></span> (DOM)</a> není k dispozici pro aplikace na straně serveru, stejně jako systém <a href="https://nodejs.org/api/fs.html" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> souborů</a> není k dispozici pro aplikace na straně klienta.

### <a name="nodejs-package-manager-npm"></a>Správce balíčků node.js (NPM)

Chcete-li nainstalovat sadu JavaScript Speech `npm install` SDK, spusťte následující příkaz níže.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>Značka skriptu HTML

Případně můžete přímo zahrnout `<script>` značku do prvku HTMS `<head>` a spoléhat se na syndikát <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank"> **JSDelivr** <span class="docon docon-navigate-external x-hidden-focus"> </span>JSDelivr NPM </a>.

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@1.10.1/distrib/lib/microsoft.cognitiveservices.speech.sdk.min.js">
</script>
```

Další informace naleznete v <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">rychlém spuštění <span class="docon docon-navigate-external x-hidden-focus"> </span>sady Web Browser Speech SDK </a>.
