---
title: 'Úvodní příručka: Vytvoření projektu klasifikace obrázků pomocí sady Custom Vision SDK for Go'
titleSuffix: Azure Cognitive Services
description: Vytvořte projekt, přidejte značky, nahrajte obrázky, trénujte projekt a předveďte pomocí sady Go SDK.
services: cognitive-services
author: areddish
manager: daauld
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: areddish
ms.openlocfilehash: 0c2e18146666de8d36eb462bf972ef2e3722216a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81403892"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-go-sdk"></a>Úvodní příručka: Vytvoření projektu klasifikace obrázků pomocí sady Custom Vision Go SDK

Tento článek obsahuje informace a ukázkový kód, který vám pomůže začít používat vlastní vision SDK s Přejít k vytvoření modelu klasifikace bitových obrázků. Po jeho vytvoření můžete přidat značky, nahrát obrázky, trénovat projekt, získat adresu URL koncového bodu publikované předpovědi projektu a použít koncový bod k programovému testování bitové kopie. Tento příklad použijte jako šablonu pro vytváření vlastní aplikace Go. Pokud chcete procesem vytvoření a používání modelu klasifikace projít _bez_ kódu, přečtěte si místo toho [pokyny s využitím prohlížeče](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Požadavky

- [Jdi 1.8+](https://golang.org/doc/install)
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="install-the-custom-vision-sdk"></a>Instalace sady Custom Vision SDK

Chcete-li nainstalovat vlastní službu Vize SDK for Go, spusťte v Prostředí PowerShell následující příkaz:

```shell
go get -u github.com/Azure/azure-sdk-for-go/...
```

nebo pokud `dep`používáte , v rámci repo běhu:
```shell
dep ensure -add github.com/Azure/azure-sdk-for-go
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]

## <a name="add-the-code"></a>Přidání kódu

Vytvořte nový soubor s názvem *sample.go* v preferovaném adresáři projektu.

### <a name="create-the-custom-vision-service-project"></a>Vytvoření projektu služby Custom Vision

Přidáním následujícího kódu do svého skriptu vytvořte nový projekt služby Custom Vision. Do odpovídajících definic vložte své klíče předplatného. Adresu URL koncového bodu si také můžete stáhnout ze stránky nastavení na webu Custom Vision.

Viz [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) metoda určit další [možnosti](getting-started-build-a-classifier.md) při vytváření projektu (vysvětleno v Sestavení třídění webový portál průvodce).

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
    endpoint string = "<your endpoint URL>"
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

Chcete-li do projektu vytvořit značky klasifikace, přidejte na konec *souboru sample.go*následující kód :

```go
// Make two tags in the new project
hemlockTag, _ := trainer.CreateTag(ctx, *project.ID, "Hemlock", "Hemlock tree tag", string(training.Regular))
cherryTag, _ := trainer.CreateTag(ctx, *project.ID, "Japanese Cherry", "Japanese cherry tree tag", string(training.Regular))
```

### <a name="upload-and-tag-images"></a>Nahrávání a označování obrázků

Ukázkové obrázky do projektu přidáte tak, že po vytvoření značky vložíte následující kód. Tento kód nahraje jednotlivé obrázky s odpovídající značkou. V jedné dávce můžete nahrát až 64 obrázků.

> [!NOTE]
> Budete muset změnit cestu k obrázkům podle toho, kde jste stáhli projekt cognitive services go sdk ukázky dříve.

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

### <a name="train-the-classifier-and-publish"></a>Trénování třídění a publikování

Tento kód vytvoří první iteraci modelu předpověď a potom publikuje tuto iteraci do koncového bodu předpověď. Název zadaný pro publikovanou iteraci lze použít k odeslání požadavků předpovědi. Iterace není k dispozici v koncovém bodu předpověď, dokud je publikován.

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

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>Získat a použít publikovanou iteraci v koncovém bodě předpověď

Pokud chcete odeslat obrázek do koncového bodu předpovědi a načíst předpověď, přidejte na konec souboru následující kód:

```go
    fmt.Println("Predicting...")
    predictor := prediction.New(prediction_key, endpoint)

    testImageData, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Test", "test_image.jpg"))
    results, _ := predictor.ClassifyImage(ctx, *project.ID, iteration_publish_name, ioutil.NopCloser(bytes.NewReader(testImageData)), "")

    for _, prediction := range *results.Predictions    {
        fmt.Printf("\t%s: %.2f%%", *prediction.TagName, *prediction.Probability * 100)
        fmt.Println("")
    }
}
```

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte *sample.go*.

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

Pak můžete ověřit správné označení testovacího obrázku (ve složce v **<adresa_URL_základního_obrázku>/Images/Test/**). Můžete se také vrátit na [web služby Custom Vision](https://customvision.ai) a zobrazit aktuální stav nově vytvořeného projektu.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Další kroky

Nyní jste viděli, jak lze provést každý krok procesu detekce objektů v kódu. Tato ukázka provede jednu trénovací iteraci, ale často budete muset trénovat a testovat model vícekrát, aby byl přesnější.

> [!div class="nextstepaction"]
> [Testování a přetrénování modelu](test-your-model.md)