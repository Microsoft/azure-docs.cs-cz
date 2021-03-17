---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 99be483f67bf5e3f9b27c63a2318df8761c16eff
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434459"
---
:::row:::
    :::column span="3":::
        Sada Speech SDK pro JavaScript je k dispozici jako balíček NPM, viz <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Microsoft-cognitiveservices Account-Speech-SDK </a> a její doprovodné úložiště GitHubu <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">– služby – řeč-sada SDK-js </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Node.js" src="https://docs.microsoft.com/media/logos/logo_nodejs.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> I když je sada Speech SDK pro JavaScript k dispozici jako balíček NPM, může to být tím, že oba Node.js i klientské webové prohlížeče mají za to, že budou mít různé vlivy na architekturu každého prostředí. Například <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">model objektu dokumentu (DOM) </a> není k dispozici pro serverové aplikace, stejně jako <a href="https://nodejs.org/api/fs.html" target="_blank">systém souborů </a> není k dispozici pro klientské aplikace.

### <a name="nodejs-package-manager-npm"></a>Správce balíčků Node.js (NPM)

Sadu Speech SDK pro JavaScript nainstalujete spuštěním následujícího `npm install` příkazu.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

Další informace najdete v tématu <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node" target="_blank"> rychlý Start k saděNode.js Speech SDK </a>.
