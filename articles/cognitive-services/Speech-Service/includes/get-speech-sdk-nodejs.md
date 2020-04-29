---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 6dd4dfd0edd334005cc2af51a46f9ca2e634272f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399865"
---
:::row:::
    :::column span="3":::
        Sada JavaScript Speech SDK je k dispozici jako balíček NPM, viz <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Microsoft-cognitiveservices Account-Speech- <span class="docon docon-navigate-external x-hidden-focus"></span> SDK</a> a je to doprovodné služba pro rozpoznávání úložiště GitHub <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">-Services-Speech- <span class="docon docon-navigate-external x-hidden-focus"> </span>SDK-js </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Node.js" src="https://docs.microsoft.com/media/logos/logo_nodejs.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> I když je sada JavaScript Speech SDK k dispozici jako balíček NPM, může to být, že oba uzly v Node. js i klientské webové prohlížeče mají za to, že mají různé vlivy na architekturu každého prostředí. Například <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">model objektu dokumentu (DOM) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> není k dispozici pro serverové aplikace, stejně jako <a href="https://nodejs.org/api/fs.html" target="_blank">systém <span class="docon docon-navigate-external x-hidden-focus"></span> souborů</a> není k dispozici pro klientské aplikace.

### <a name="nodejs-package-manager-npm"></a>Správce balíčků Node. js (NPM)

Chcete-li nainstalovat sadu JavaScript Speech SDK, spusťte `npm install` následující příkaz.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

Další informace najdete v tématu <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node" target="_blank">rychlý Start <span class="docon docon-navigate-external x-hidden-focus"> </span>pro sadu SDK pro rozpoznávání řeči Node. js </a>.
