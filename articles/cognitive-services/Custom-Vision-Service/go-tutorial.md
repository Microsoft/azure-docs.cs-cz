---
title: 'Rychlý start: Vytvoření projektu klasifikace Image pomocí sady Custom Vision SDK for přejít'
titleSuffix: Azure Cognitive Services
description: Vytvořte projekt, přidejte značky, nahrajte obrázky, výukujte projekt a vytvořte předpovědi pomocí sady SDK.
services: cognitive-services
author: areddish
manager: daauld
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 07/15/2019
ms.author: areddish
ms.openlocfilehash: f21ab53b3beeead8cbd4ba781cd54b23420661f0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561006"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-go-sdk"></a>Rychlý start: Vytvoření projektu klasifikace obrázků pomocí sady Custom Vision jít SDK

Tento článek poskytuje informace a ukázkový kód, který vám může pomoci začít používat sadu Custom Vision SDK s nástrojem přejít k sestavení modelu klasifikace imagí. Po vytvoření můžete přidat značky, nahrát obrázky, naučit projekt, získat adresu URL koncového bodu předpovědi projektu a použít koncový bod k programovému testování obrázku. Tento příklad použijte jako šablonu pro vytvoření vlastní aplikace v cestách. Pokud chcete procesem vytvoření a používání modelu klasifikace projít _bez_ kódu, přečtěte si místo toho [pokyny s využitím prohlížeče](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Požadavky

- [Přejít 1.8 +](https://golang.org/doc/install)

## <a name="install-the-custom-vision-sdk"></a>Instalace sady Custom Vision SDK

Pokud chcete nainstalovat sadu Custom Vision Service SDK pro přejít, spusťte v PowerShellu následující příkaz:

```shell
go get -u github.com/Azure/azure-sdk-for-go/...
```

nebo pokud `dep`používáte, v rámci vašeho spuštění úložiště:
```shell
dep ensure -add github.com/Azure/azure-sdk-for-go
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]

## <a name="add-the-code"></a>Přidání kódu

Vytvořte nový soubor s názvem *Sample. přejít* do preferovaného adresáře projektu.

### <a name="create-the-custom-vision-service-project"></a>Vytvoření projektu služby Custom Vision

Přidáním následujícího kódu do svého skriptu vytvořte nový projekt služby Custom Vision. Do odpovídajících definic vložte své klíče předplatného.

```go
import(
    "context"
    "bytes"
    "fmt"
    "io/ioutil"
    "path"
    "log"
    "time"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v3.0/customvision/training"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v3.0/customvision/prediction"
)

var (
    training_key string = "<your training key>"
    prediction_key string = "<your prediction key>"
    prediction_resource_id = "<your prediction resource id>"
    endpoint string = "https://southcentralus.api.cognitive.microsoft.com"
    project_name string = "Go Sample Project"
    iteration_publish_name = "classifyModel"
    sampleDataDirectory = "<path to sample images>"
)

func main() {
    fmt.Println("Creating project...")

    ctx = context.Background()

    trainer := training.New(training_key, endpoint)

    project, err := trainer.CreateProject(ctx, project_name, "sample project", nil, string(training.Multilabel))
    if (err != nil) {
        log.Fatal(err)
    }
```

### <a name="create-tags-in-the-project"></a>Vytvoření značek v projektu

Chcete-li vytvořit klasifikační značky pro projekt, přidejte následující kód na konec *Sample. přejít*:

```go
// Make two tags in the new project
hemlockTag, _ := trainer.CreateTag(ctx, *project.ID, "Hemlock", "Hemlock tree tag", string(training.Regular))
cherryTag, _ := trainer.CreateTag(ctx, *project.ID, "Japanese Cherry", "Japanese cherry tree tag", string(training.Regular))
```

### <a name="upload-and-tag-images"></a>Nahrání a označení obrázků

Ukázkové obrázky do projektu přidáte tak, že po vytvoření značky vložíte následující kód. Tento kód nahraje jednotlivé obrázky s odpovídající značkou. Na základě toho, kam jste stáhli projekt ukázek sady Cognitive Services jít na sadu SDK, budete muset zadat cestu k adrese URL základního obrázku.

> [!NOTE]
> Budete muset změnit cestu k obrázkům na základě toho, kam jste dříve stáhli projekt ukázek sady Cognitive Services jít na sadu SDK.

```go
fmt.Println("Adding images...")
japaneseCherryImages, err := ioutil.ReadDir(path.Join(sampleDataDirectory, "Japanese Cherry"))
if err != nil {
    fmt.Println("Error finding Sample images")
}

hemLockImages, err := ioutil.ReadDir(path.Join(sampleDataDirectory, "Hemlock"))
if err != nil {
    fmt.Println("Error finding Sample images")
}

for _, file := range hemLockImages {
    imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Hemlock", file.Name()))
    imageData := ioutil.NopCloser(bytes.NewReader(imageFile))

    trainer.CreateImagesFromData(ctx, *project.ID, imageData, []string{ hemlockTag.ID.String() })
}

for _, file := range japaneseCherryImages {
    imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Japanese Cherry", file.Name()))
    imageData := ioutil.NopCloser(bytes.NewReader(imageFile))
    trainer.CreateImagesFromData(ctx, *project.ID, imageData, []string{ cherryTag.ID.String() })
}
```

### <a name="train-the-classifier-and-publish"></a>Výuka třídění a publikování

Tento kód vytvoří první iteraci v projektu a pak tuto iteraci publikuje do koncového bodu předpovědi. Název zadaný pro publikovanou iteraci lze použít k odeslání požadavků předpovědi. Iterace není v koncovém bodu předpovědi k dispozici, dokud není publikována.

```go
fmt.Println("Training...")
iteration, _ := trainer.TrainProject(ctx, *project.ID)
for {
    if *iteration.Status != "Training" {
        break
    }
    fmt.Println("Training status: " + *iteration.Status)
    time.Sleep(1 * time.Second)
    iteration, _ = trainer.GetIteration(ctx, *project.ID, *iteration.ID)
}
fmt.Println("Training status: " + *iteration.Status)

trainer.PublishIteration(ctx, *project.ID, *iteration.ID, iteration_publish_name, prediction_resource_id))
```

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>Získání a použití publikované iterace na koncovém bodu předpovědi

Pokud chcete odeslat obrázek do koncového bodu předpovědi a načíst předpověď, přidejte na konec souboru následující kód:

```go
    fmt.Println("Predicting...")
    predictor := prediction.New(prediction_key, endpoint)

    testImageData, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Test", "test_image.jpg"))
    results, _ := predictor.ClassifyImage(ctx, *project.ID, iteration_publish_name, ioutil.NopCloser(bytes.NewReader(testImageData)), "")

    for _, prediction := range *results.Predictions {
        fmt.Printf("\t%s: %.2f%%", *prediction.TagName, *prediction.Probability * 100)
        fmt.Println("")
    }
}
```

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte *Sample. přejít*.

```shell
go run sample.go
```

Výstup aplikace by se měl podobat následujícímu textu:

```console
Creating project...
Adding images...
Training...
Training status: Training
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Pak můžete ověřit správné označení testovacího obrázku (ve složce v **<adresa_URL_základního_obrázku>/Images/Test/** ). Můžete se také vrátit na [web služby Custom Vision](https://customvision.ai) a zobrazit aktuální stav nově vytvořeného projektu.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Další postup

Právě jste viděli, jak se dají jednotlivé kroky procesu klasifikace obrázků provádět v kódu. Tato ukázka provede jednu iteraci trénování, ale často je potřeba model trénovat a testovat vícekrát, aby byl přesnější.

> [!div class="nextstepaction"]
> [Testování a přetrénování modelu](test-your-model.md)