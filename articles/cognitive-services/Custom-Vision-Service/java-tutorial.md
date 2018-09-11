---
title: Custom Vision Service v Javě kurz – vytvoření Azure Cognitive Services | Dokumentace Microsoftu
description: Prozkoumejte základní aplikace v Javě, která používá vlastní rozhraní API pro zpracování obrazu ve službě Microsoft Cognitive Services. Vytvoření projektu, přidání značek, nahrávat obrázky, trénování váš projekt a předpověď pomocí výchozí koncový bod.
services: cognitive-services
author: areddish
manager: chbuehle
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 08/28/2018
ms.author: areddish
ms.openlocfilehash: a83a2f5cac9281a4cd79c1a0cead0f2af82d73df
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44305694"
---
# <a name="use-custom-vision-api-to-build-an-image-classification-project-with-java"></a>Použití vlastní rozhraní API pro zpracování obrazu k sestavení projekt klasifikace obrázků s Javou

Zjistěte, jak vytvořit projekt klasifikace obrázků s využitím Javy službu Custom Vision Service. Po jeho vytvoření je můžete přidat značky, nahrávání obrázků, trénování projektu, získat adresu URL koncového bodu projektu výchozí předpovědi a pomocí něho můžete testovat programově bitovou kopii. Použijte tento příklad open source jako šablonu pro vytváření vlastních aplikací s použitím vlastní rozhraní API pro zpracování obrazu.

## <a name="prerequisites"></a>Požadavky

- JDK 7 nebo 8 nainstalované.
- Maven nainstalovat.

## <a name="get-the-training-and-prediction-keys"></a>Získání klíčů trénování a predikcí

Klíče používané v tomto příkladu najdete [vizi vlastní webovou stránku](https://customvision.ai) a vyberte __ikonu ozubeného kola__ v pravém horním rohu. V __účty__ tématu, zkopírujte hodnoty z __školení klíč__ a __předpovědi klíč__ pole.

![Obrázek klíče uživatelského rozhraní](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>Instalace sady SDK služby Custom Vision

Vlastní sada SDK pro zpracování obrazu můžete nainstalovat z centrálního úložiště maven:
* [Školicí sada SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
* [Predikce SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

## <a name="understand-the-code"></a>Vysvětlení kódu

Úplný projekt, včetně obrázků, je k dispozici [Custom Vision Azure ukázky pro úložiště Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). 

Otevřít pomocí oblíbeném Java IDE `Vision/CustomVision` projektu. 

Tato aplikace používá školení klíč, který jste získali dříve k vytvoření nového projektu s názvem __ukázkový projekt v jazyce Java__. Potom nahrává obrázky a natrénuje a otestuje třídění. Třídění označuje, zda je strom __Hemlock__ nebo __japonské výběru určitých položek__.

Následující fragmenty kódu implementovat primární funkce v tomto příkladu:

## <a name="create-a-custom-vision-service-project"></a>Vytvořte projekt služby Custom Vision Service

> [!IMPORTANT]
> Nastavte `trainingApiKey` hodnotě klíče školení, který jste získali dříve.

```java
final String trainingApiKey = "insert your training key here";
TrainingApi trainClient = CustomVisionTrainingManager.authenticate(trainingApiKey);

Trainings trainer = trainClient.trainings();

System.out.println("Creating project...");
Project project = trainer.createProject()
            .withName("Sample Java Project")
            .execute();
```

## <a name="add-tags-to-your-project"></a>Přidání značek do projektu

```java
// create hemlock tag
Tag hemlockTag = trainer.createTag()
    .withProjectId(project.id())
    .withName("Hemlock")
    .execute();

// create cherry tag
Tag cherryTag = trainer.createTag()
    .withProjectId(project.id())
    .withName("Japanese Cherry")
    .execute();
```

## <a name="upload-images-to-the-project"></a>Nahrávání obrázků do projektu

Vzorek je nastavení, které mají být zahrnuty do koncového balíčku bitové kopie. Image se číst z oddílu prostředků soubor jar a odešlou do služby.

```java
System.out.println("Adding images...");
for (int i = 1; i <= 10; i++) {
    String fileName = "hemlock_" + i + ".jpg";
    byte[] contents = GetImage("/Hemlock", fileName);
    AddImageToProject(trainer, project, fileName, contents, hemlockTag.id(), null);
}

for (int i = 1; i <= 10; i++) {
    String fileName = "japanese_cherry_" + i + ".jpg";
    byte[] contents = GetImage("/Japanese Cherry", fileName);
    AddImageToProject(trainer, project, fileName, contents, cherryTag.id(), null);
}
```

Předchozí fragment kódu používá dva pomocných funkcí, které načtení obrázků jako datové proudy prostředků a k jejich nahrávání do služby.

```java
private static void AddImageToProject(Trainings trainer, Project project, String fileName, byte[] contents, UUID tag)
{
    System.out.println("Adding image: " + fileName);

    ImageFileCreateEntry file = new ImageFileCreateEntry()
        .withName(fileName)
        .withContents(contents);

    ImageFileCreateBatch batch = new ImageFileCreateBatch()
        .withImages(Collections.singletonList(file));

    batch = batch.withTagIds(Collections.singletonList(tag));

    trainer.createImagesFromFiles(project.id(), batch);
}

private static byte[] GetImage(String folder, String fileName)
{
    try {
        return ByteStreams.toByteArray(CustomVisionSamples.class.getResourceAsStream(folder + "/" + fileName));
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }

    return null;
}
```

## <a name="train-the-project"></a>Trénování projektu

Tím se vytvoří první iterace v projektu a označí tuto iteraci jako výchozí iterace. 

```java
System.out.println("Training...");
Iteration iteration = trainer.trainProject(project.id());

while (iteration.status().equals("Training"))
{
    System.out.println("Training Status: "+ iteration.status());
    Thread.sleep(1000);
    iteration = trainer.getIteration(project.id(), iteration.id());
}

System.out.println("Training Status: "+ iteration.status());
trainer.updateIteration(project.id(), iteration.id(), iteration.withIsDefault(true));
```

## <a name="get-and-use-the-default-prediction-endpoint"></a>Získat a použít výchozí koncový bod predikcí

> [!IMPORTANT]
> Nastavte `predictionApiKey` hodnotě klíče predikcí, který jste získali dříve.

```java
final String predictionApiKey = "insert your prediction key here";
PredictionEndpoint predictClient = CustomVisionPredictionManager.authenticate(predictionApiKey);

// Use below for predictions from a url
// String url = "some url";
// ImagePrediction results = predictor.predictions().predictImage()
//                         .withProjectId(project.id())
//                         .withUrl(url)
//                         .execute();

// load test image
byte[] testImage = GetImage("/Test", "test_image.jpg");

// predict
ImagePrediction results = predictor.predictions().predictImage()
    .withProjectId(project.id())
    .withImageData(testImage)
    .execute();

for (Prediction prediction: results.predictions())
{
    System.out.println(String.format("\t%s: %.2f%%", prediction.tagName(), prediction.probability() * 100.0f));
}
```

## <a name="run-the-example"></a>Spustit příklad

Kompilace a spuštění řešení pomocí nástroje maven:

```
mvn compile exec:java
```

Výstup aplikace se podobá následujícímu textu:

```
Creating project...
Adding images...
Adding image: hemlock_1.jpg
Adding image: hemlock_2.jpg
Adding image: hemlock_3.jpg
Adding image: hemlock_4.jpg
Adding image: hemlock_5.jpg
Adding image: hemlock_6.jpg
Adding image: hemlock_7.jpg
Adding image: hemlock_8.jpg
Adding image: hemlock_9.jpg
Adding image: hemlock_10.jpg
Adding image: japanese_cherry_1.jpg
Adding image: japanese_cherry_2.jpg
Adding image: japanese_cherry_3.jpg
Adding image: japanese_cherry_4.jpg
Adding image: japanese_cherry_5.jpg
Adding image: japanese_cherry_6.jpg
Adding image: japanese_cherry_7.jpg
Adding image: japanese_cherry_8.jpg
Adding image: japanese_cherry_9.jpg
Adding image: japanese_cherry_10.jpg
Training...
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```