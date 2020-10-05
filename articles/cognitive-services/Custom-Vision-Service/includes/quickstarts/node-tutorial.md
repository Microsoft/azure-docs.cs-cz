---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 09/15/2020
ms.custom: devx-track-js
ms.openlocfilehash: 90927109a78d387ed3a535128e98ae7910c222dc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91321049"
---
Tato příručka poskytuje pokyny a ukázkový kód, který vám pomůže začít používat Custom Vision klientské knihovny pro Node.js k sestavení modelu klasifikace imagí. Vytvoříte projekt, přidáte značky, provedete projekt a použijete adresu URL koncového bodu předpovědi projektu pro programové testování. Tento příklad použijte jako šablonu pro vytvoření vlastní aplikace pro rozpoznávání imagí.

> [!NOTE]
> Pokud chcete sestavit model klasifikace _bez_ psaní kódu, prostudujte si místo toho doprovodné materiály pro [prohlížeč](../../getting-started-build-a-classifier.md) .

## <a name="prerequisites"></a>Předpoklady

- Je nainstalovaná [Node.js 8](https://www.nodejs.org/en/download/) nebo novější.
- [npm](https://www.npmjs.com/) je nainstalovaný.
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="install-the-custom-vision-client-library"></a>Instalace klientské knihovny Custom Vision

K napsání aplikace pro analýzu obrázků pomocí Custom Vision pro Node.js budete potřebovat balíčky Custom Vision NPM. Pokud je chcete nainstalovat, spusťte v PowerShellu následující příkaz:

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

[!INCLUDE [get-keys](../../includes/get-keys.md)]

[!INCLUDE [node-get-images](../../includes/node-get-images.md)]

## <a name="add-the-code"></a>Přidání kódu

V upřednostňovaném adresáři projektu vytvořte nový soubor s názvem *sample.js* .

## <a name="create-the-custom-vision-project"></a>Vytvoření projektu Custom Vision

Přidáním následujícího kódu do svého skriptu vytvořte nový projekt služby Custom Vision. Do příslušných definic vložte klíče předplatného a nastavte hodnotu cesty sampleDataRoot na cestu ke složce imagí. Ujistěte se, že hodnota koncového bodu odpovídá koncovým bodům školení a předpovědi, které jste vytvořili v [Customvision.AI](https://www.customvision.ai/). Všimněte si, že rozdíl mezi vytvořením projektu detekce objektu a klasifikace obrázků je doména zadaná ve volání **createProject** .

```javascript
const util = require('util');
const fs = require('fs');
const TrainingApi = require("@azure/cognitiveservices-customvision-training");
const PredictionApi = require("@azure/cognitiveservices-customvision-prediction");
const msRest = require("@azure/ms-rest-js");

const setTimeoutPromise = util.promisify(setTimeout);

const trainingKey = "<your training key>";
const predictionKey = "<your prediction key>";
const predictionResourceId = "<your prediction resource id>";
const sampleDataRoot = "<path to image files>";

const endPoint = "https://<my-resource-name>.cognitiveservices.azure.com/"

const publishIterationName = "classifyModel";

const credentials = new msRest.ApiKeyCredentials({ inHeader: { "Training-key": trainingKey } });
const trainer = new TrainingApi.TrainingAPIClient(credentials, endPoint);

(async () => {
    console.log("Creating project...");
    const sampleProject = await trainer.createProject("Sample Project");
```

## <a name="create-tags-in-the-project"></a>Vytvoření značek v projektu

Chcete-li vytvořit klasifikační značky pro projekt, přidejte následující kód na konec *sample.js*:

```javascript
    const hemlockTag = await trainer.createTag(sampleProject.id, "Hemlock");
    const cherryTag = await trainer.createTag(sampleProject.id, "Japanese Cherry");
```

## <a name="upload-and-tag-images"></a>Nahrávání a označování obrázků

Ukázkové obrázky do projektu přidáte tak, že po vytvoření značky vložíte následující kód. Tento kód nahraje jednotlivé obrázky s odpovídající značkou. Do jedné dávky můžete nahrát až 64 imagí.

> [!NOTE]
> Budete muset změnit *sampleDataRoot* na cestu k obrázkům na základě toho, kam jste dříve stáhli projekt ukázek sady SDK Cognitive Services Node.js.

```javascript
    console.log("Adding images...");
    let fileUploadPromises = [];
    
    const hemlockDir = `${sampleDataRoot}/Hemlock`;
    const hemlockFiles = fs.readdirSync(hemlockDir);
    hemlockFiles.forEach(file => {
        fileUploadPromises.push(trainer.createImagesFromData(sampleProject.id, fs.readFileSync(`${hemlockDir}/${file}`), { tagIds: [hemlockTag.id] }));
    });
    
    const cherryDir = `${sampleDataRoot}/Japanese Cherry`;
    const japaneseCherryFiles = fs.readdirSync(cherryDir);
    japaneseCherryFiles.forEach(file => {
        fileUploadPromises.push(trainer.createImagesFromData(sampleProject.id, fs.readFileSync(`${cherryDir}/${file}`), { tagIds: [cherryTag.id] }));
    });
    
    await Promise.all(fileUploadPromises);
```

## <a name="train-and-publish-the-classifier"></a>Výuka a publikování klasifikátoru

Tento kód vytvoří první iteraci modelu předpovědi a pak tuto iteraci publikuje do koncového bodu předpovědi. Název zadaný pro publikovanou iteraci lze použít k odeslání požadavků předpovědi. Iterace není v koncovém bodu předpovědi k dispozici, dokud není publikována.

```javascript
    console.log("Training...");
    let trainingIteration = await trainer.trainProject(sampleProject.id);
    
    // Wait for training to complete
    console.log("Training started...");
    while (trainingIteration.status == "Training") {
        console.log("Training status: " + trainingIteration.status);
        await setTimeoutPromise(1000, null);
        trainingIteration = await trainer.getIteration(sampleProject.id, trainingIteration.id)
    }
    console.log("Training status: " + trainingIteration.status);
    
    // Publish the iteration to the end point
    await trainer.publishIteration(sampleProject.id, trainingIteration.id, publishIterationName, predictionResourceId);
```

## <a name="use-the-prediction-endpoint"></a>Použití koncového bodu předpovědi

Pokud chcete odeslat obrázek do koncového bodu předpovědi a načíst předpověď, přidejte na konec souboru následující kód:

```javascript
    const predictor_credentials = new msRest.ApiKeyCredentials({ inHeader: { "Prediction-key": predictionKey } });
    const predictor = new PredictionApi.PredictionAPIClient(predictor_credentials, endPoint);
    const testFile = fs.readFileSync(`${sampleDataRoot}/Test/test_image.jpg`);

    const results = await predictor.classifyImage(sampleProject.id, publishIterationName, testFile);

    // Step 6. Show results
    console.log("Results:");
    results.predictions.forEach(predictedResult => {
        console.log(`\t ${predictedResult.tagName}: ${(predictedResult.probability * 100.0).toFixed(2)}%`);
    });
})()
```

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte *sample.js*.

```shell
node sample.js
```

Výstup aplikace by se měl podobat následujícímu textu:

```console
Creating project...
Adding images...
Training...
Training started...
Training status: Training
Training status: Training
Training status: Training
Training status: Completed
Results:
         Hemlock: 94.97%
         Japanese Cherry: 0.01%
```

Pak můžete ověřit správné označení testovacího obrázku (ve složce v **<adresa_URL_základního_obrázku>/Images/Test/**). Můžete se také vrátit na [web služby Custom Vision](https://customvision.ai) a zobrazit aktuální stav nově vytvořeného projektu.

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Další kroky

Nyní jste viděli, jak se každý krok procesu detekce objektů dá provést v kódu. Tato ukázka provádí jednu výukovou iteraci, ale často je potřeba, abyste model provedli a otestovali několikrát, aby bylo přesnější.

> [!div class="nextstepaction"]
> [Testování a přetrénování modelu](../../test-your-model.md)

* [Co je Custom Vision?](../../overview.md)
* [Referenční dokumentace k sadě SDK (školení)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-customvision-training/?view=azure-node-latest)
* [Referenční dokumentace k sadě SDK (předpovědi)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-customvision-prediction/?view=azure-node-latest)