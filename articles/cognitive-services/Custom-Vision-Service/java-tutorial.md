---
title: 'Kurz: Vytvoření projektu klasifikace obrázků pomocí sady Custom Vision SDK pro Javu'
titlesuffix: Azure Cognitive Services
description: Vytvořte projekt, přidejte značky, nahrajte obrázky, vytrénujte svůj projekt a vytvořte předpověď pomocí výchozího koncového bodu.
services: cognitive-services
author: areddish
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 08/28/2018
ms.author: areddish
ms.openlocfilehash: e302fc580d9c83d269f0deedd051a3ea23bd274e
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957205"
---
# <a name="tutorial-create-an-image-classification-project-with-the-custom-vision-sdk-for-java"></a>Kurz: Vytvoření projektu klasifikace obrázků pomocí sady Custom Vision SDK pro Javu

Zjistěte, jak vytvořit projekt klasifikace obrázků pomocí služby Custom Vision Service a Javy. Po jeho vytvoření můžete přidat značky, nahrát obrázky, vytrénovat projekt, získat adresu URL výchozího koncového bodu předpovědi projektu a použít jej k programovému testování obrázku. Tento opensourcový příklad použijte jako šablonu pro vytvoření vlastní aplikace pomocí rozhraní Custom Vision API.

## <a name="prerequisites"></a>Požadavky

- Nainstalovaná sada JDK 7 nebo 8
- Nainstalovaný Maven

## <a name="get-the-training-and-prediction-keys"></a>Získání tréninkového klíče a klíče předpovědi

Klíče používané v tomto příkladu získáte tak, že přejdete na [webovou stránku služby Custom Vision](https://customvision.ai) a vyberete __ikonu ozubeného kola__ v pravém horním rohu. V části __Účty__ zkopírujte hodnoty z polí pro __tréninkový klíč__ a __klíč předpovědi__.

![Obrázek uživatelského rozhraní klíčů](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>Instalace sady Custom Vision Service SDK

Sadu Custom Vision SDK můžete nainstalovat z centrálního úložiště Mavenu:
* [Sada SDK pro trénování](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
* [Sada SDK pro předpověď](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

## <a name="understand-the-code"></a>Vysvětlení kódu

Úplný projekt, včetně obrázků, je k dispozici v [ukázkách Azure služby Custom Vision pro úložiště Javy](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). 

Projekt `Vision/CustomVision` otevřete pomocí svého oblíbeného prostředí Java IDE. 

Tato aplikace používá dříve získaný tréninkový klíč k vytvoření nového projektu s názvem __Sample Java Project__. Potom nahraje obrázky k trénování a testování klasifikátoru. Klasifikátor identifikuje, jestli je rostlina __bolehlav__ nebo __japonská třešeň__.

Následující fragmenty kódu implementují primární funkci tohoto příkladu:

## <a name="create-a-custom-vision-service-project"></a>Vytvoření projektu služby Custom Vision Service

> [!IMPORTANT]
> Nastavte pro `trainingApiKey` hodnotu tréninkového klíče, kterou jste získali dříve.

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

## <a name="upload-images-to-the-project"></a>Nahrání obrázků do projektu

Ukázka je nastavení, které zahrnuje obrázky do koncového balíčku. Obrázky se čtou z oddílu prostředků souboru JAR a nahrávají se do služby.

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

Předchozí fragment kódu používá dvě pomocné funkce, které obrázky načítají jako streamy prostředků a nahrávají je do služby.

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

Vytvoří se tak první iterace v projektu a označí se jako výchozí iterace. 

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

## <a name="get-and-use-the-default-prediction-endpoint"></a>Získání a použití výchozího koncového bodu předpovědi

> [!IMPORTANT]
> Nastavte pro `predictionApiKey` hodnotu klíče předpovědi, kterou jste získali dříve.

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

## <a name="run-the-example"></a>Spuštění příkladu

Kompilace a spuštění řešení pomocí Mavenu:

```
mvn compile exec:java
```

Výstup aplikace je podobný následujícímu textu:

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