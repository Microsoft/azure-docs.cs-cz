---
title: Změna, trénovat na základě modelu aplikace Node.js
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu pro Node.js přidáte příklady promluv do aplikace domácí automatizace a budete aplikaci trénovat.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 048579aa6d22144b9de8129042ab9fd2a36d4d00
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53092684"
---
# <a name="quickstart-change-model-using-nodejs"></a>Rychlý start: Změna modelu pomocí Node.js

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* Nejnovější verze [**Node.js**](https://nodejs.org/en/download/) s NPM.
* Závislosti NPM pro tento článek: [**request**](https://www.npmjs.com/package/request), [**request-promise**](https://www.npmjs.com/package/request-promise), [**fs-extra**](https://www.npmjs.com/package/fs-extra).  
* [Visual Studio Code](https://code.visualstudio.com/).

[!INCLUDE [Code is available in LUIS-Samples Github repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>Soubor JSON s ukázkovými promluvami

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>Vytvoření kódu rychlého startu 

Přidejte do souboru s názvem `add-utterances.js` závislosti NPM.

   [!code-javascript[NPM Dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=8-11 "NPM Dependencies")]

Přidejte do souboru konstanty LUIS. Zkopírujte následující kód a použijte vlastní klíč pro vytváření, ID aplikace a ID verze.

   [!code-javascript[LUIS key and IDs](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=13-22 "LUIS key and IDs")]

Přidejte název a umístění nahraného souboru obsahujícího promluvy. 

   [!code-javascript[Add upload file](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=24-26 "Add upload file")]

Přidejte metodu a objekt pro funkci `addUtterance`.

   [!code-javascript[Add configuration information for adding utterance](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=28-67 "Add configuration information for adding utterance")]

Přidejte metodu a objekt pro funkci `train`.

   [!code-javascript[Add configuration information for training LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=69-101 "Add configuration information for training LUIS")]

Přidejte funkci `sendUtteranceToApi` pro odesílání a příjem volání HTTP. 

   [!code-javascript[Send the HTTP request](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=103-119 "Send the HTTP request")]

Přidejte **hlavní** kód, který vybere akci.

   [!code-javascript[Main function](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=121-143 "Main function")]

### <a name="install-dependencies"></a>Instalace závislostí

Vytvořte soubor `package.json` s následujícím textem:

   [!code-json[Package.json](~/samples-luis/documentation-samples/quickstarts/change-model/node/package.json "Package.json")]

V příkazovém řádku v adresáři, který obsahuje soubor package.json, nainstalujte závislosti s NPM: `npm install`.

## <a name="run-code"></a>Spuštění kódu

Spusťte aplikaci z příkazového řádku s Node.js.

Volání `npm start` přidá promluvy, trénuje a získá stav školení.

```console
> npm start 
```

Tento příkazový řádek ukazuje výsledky volání rozhraní API pro přidávání promluv. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]


## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile tento rychlý start dokončíte, odeberte všechny soubory, které jste v něm vytvořili. 

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"] 
> [Sestavení aplikace LUIS prostřednictvím kódu programu](luis-tutorial-node-import-utterances-csv.md)