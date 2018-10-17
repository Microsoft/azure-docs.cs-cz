---
title: 'Kurz: Vytvoření projektu detekce objektů – rozhraní API Custom Vision, Java'
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
ms.openlocfilehash: 661242e4962a8218c48d7ea66d8a6f728b5154c8
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46365010"
---
# <a name="tutorial-build-an-object-detection-project-with-java"></a>Kurz: Sestavení projektu detekce objektů s Javou

Prozkoumejte základní aplikaci v Javě, která používá rozhraní API pro počítačové zpracování obrazu k vytvoření projektu detekce objektů. Po jeho vytvoření můžete přidat označené oblasti, nahrát obrázky, vytrénovat projekt, získat adresu URL výchozího koncového bodu předpovědi projektu a použít tento koncový bod k programovému testování obrázku. Tento opensourcový příklad použijte jako šablonu pro vytvoření vlastní aplikace pomocí rozhraní Custom Vision API.

## <a name="prerequisites"></a>Požadavky

Pro tento kurz budete muset:

- Nainstalovat sadu JDK 7 nebo 8
- Nainstalovat Maven

## <a name="install-the-custom-vision-service-sdk"></a>Nainstalovat sadu Custom Vision Service SDK

Sadu Custom Vision SDK můžete nainstalovat z centrálního úložiště Mavenu:
* [Sada SDK pro trénování](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
* [Sada SDK pro předpověď](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

## <a name="get-the-training-and-prediction-keys"></a>Získání tréninkového klíče a klíče předpovědi

Klíče používané v tomto příkladu získáte tak, že přejdete na [stránku služby Custom Vision](https://customvision.ai) a v pravém horním rohu vyberete __ikonu ozubeného kola__. V části __Účty__ zkopírujte hodnoty z polí pro __tréninkový klíč__ a __klíč předpovědi__.

![Obrázek uživatelského rozhraní klíčů](./media/python-tutorial/training-prediction-keys.png)

## <a name="understand-the-code"></a>Vysvětlení kódu

Úplný projekt, včetně obrázků, je k dispozici v [ukázkách Azure služby Custom Vision pro úložiště Javy](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). 

Projekt `Vision/CustomVision` otevřete pomocí svého oblíbeného prostředí Java IDE. 

Tato aplikace používá dříve získaný tréninkový klíč k vytvoření nového projektu s názvem __Sample Java OD Project__. Potom nahraje obrázky k trénování a testování detektoru objektů. Detektor objektů identifikuje oblasti obsahující __vidličku__ nebo __nůžky__.

Následující fragmenty kódu implementují primární funkci tohoto příkladu:

## <a name="create-a-custom-vision-service-project"></a>Vytvoření projektu služby Custom Vision Service

Všimněte si, že vytváření projektu zjišťování objektů a klasifikace obrázků se liší doménou, která je zadána při volání createProject.

> [!IMPORTANT]
> Nastavte pro `trainingApiKey` hodnotu tréninkového klíče, kterou jste získali dříve.

```java
final String trainingApiKey = "insert your training key here";
TrainingApi trainClient = CustomVisionTrainingManager.authenticate(trainingApiKey);

System.out.println("Object Detection Sample");
Trainings trainer = trainClient.trainings();

// find the object detection domain to set the project type
Domain objectDetectionDomain = null;
List<Domain> domains = trainer.getDomains();
for (final Domain domain : domains) {
    if (domain.type() == DomainType.OBJECT_DETECTION) {
        objectDetectionDomain = domain;
        break;
    }
}

if (objectDetectionDomain == null) {
    System.out.println("Unexpected result; couldn't find object detection domain.");
    return;
}

System.out.println("Creating project...");
// create an object detection project
Project project = trainer.createProject()
    .withName("Sample Java OD Project")
    .withDescription("Sample OD Project")
    .withDomainId(objectDetectionDomain.id())
    .withClassificationType(Classifier.MULTILABEL.toString())
    .execute();
```

## <a name="add-tags-to-your-project"></a>Přidání značek do projektu

```java
// create fork tag
Tag forkTag = trainer.createTag()
    .withProjectId(project.id())
    .withName("fork")
    .execute();

// create scissors tag
Tag scissorsTag = trainer.createTag()
    .withProjectId(project.id())
    .withName("scissor")
    .execute();
```

## <a name="upload-images-to-the-project"></a>Nahrání obrázků do projektu

U projektu detekce objektů je potřeba nahrát obrázek, oblasti a značky. Oblast je v normalizovaných souřadnicích a určuje umístění označeného objektu.


```java
// Mapping of filenames to their respective regions in the image. The coordinates are specified
// as left, top, width, height in normalized coordinates. I.e. (left is left in pixels / width in pixels)

// This is a hardcoded mapping of the files we'll upload along with the bounding box of the object in the
// image. The boudning box is specified as left, top, width, height in normalized coordinates.
//  Normalized Left = Left / Width (in Pixels)
//  Normalized Top = Top / Height (in Pixels)
//  Normalized Bounding Box Width = (Right - Left) / Width (in Pixels)
//  Normalized Bounding Box Height = (Bottom - Top) / Height (in Pixels)
HashMap<String, double[]> regionMap = new HashMap<String, double[]>();
regionMap.put("scissors_1.jpg", new double[] { 0.4007353, 0.194068655, 0.259803921, 0.6617647 });
regionMap.put("scissors_2.jpg", new double[] { 0.426470578, 0.185898721, 0.172794119, 0.5539216 });
regionMap.put("scissors_3.jpg", new double[] { 0.289215684, 0.259428144, 0.403186262, 0.421568632 });
regionMap.put("scissors_4.jpg", new double[] { 0.343137264, 0.105833367, 0.332107842, 0.8055556 });
regionMap.put("scissors_5.jpg", new double[] { 0.3125, 0.09766343, 0.435049027, 0.71405226 });
regionMap.put("scissors_6.jpg", new double[] { 0.379901975, 0.24308826, 0.32107842, 0.5718954 });
regionMap.put("scissors_7.jpg", new double[] { 0.341911763, 0.20714055, 0.3137255, 0.6356209 });
regionMap.put("scissors_8.jpg", new double[] { 0.231617644, 0.08459154, 0.504901946, 0.8480392 });
regionMap.put("scissors_9.jpg", new double[] { 0.170343131, 0.332957536, 0.767156839, 0.403594762 });
regionMap.put("scissors_10.jpg", new double[] { 0.204656869, 0.120539248, 0.5245098, 0.743464053 });
regionMap.put("scissors_11.jpg", new double[] { 0.05514706, 0.159754932, 0.799019635, 0.730392158 });
regionMap.put("scissors_12.jpg", new double[] { 0.265931368, 0.169558853, 0.5061275, 0.606209159 });
regionMap.put("scissors_13.jpg", new double[] { 0.241421565, 0.184264734, 0.448529422, 0.6830065 });
regionMap.put("scissors_14.jpg", new double[] { 0.05759804, 0.05027781, 0.75, 0.882352948 });
regionMap.put("scissors_15.jpg", new double[] { 0.191176474, 0.169558853, 0.6936275, 0.6748366 });
regionMap.put("scissors_16.jpg", new double[] { 0.1004902, 0.279036, 0.6911765, 0.477124184 });
regionMap.put("scissors_17.jpg", new double[] { 0.2720588, 0.131977156, 0.4987745, 0.6911765 });
regionMap.put("scissors_18.jpg", new double[] { 0.180147052, 0.112369314, 0.6262255, 0.6666667 });
regionMap.put("scissors_19.jpg", new double[] { 0.333333343, 0.0274019931, 0.443627447, 0.852941155 });
regionMap.put("scissors_20.jpg", new double[] { 0.158088237, 0.04047389, 0.6691176, 0.843137264 });
regionMap.put("fork_1.jpg", new double[] { 0.145833328, 0.3509314, 0.5894608, 0.238562092 });
regionMap.put("fork_2.jpg", new double[] { 0.294117659, 0.216944471, 0.534313738, 0.5980392 });
regionMap.put("fork_3.jpg", new double[] { 0.09191177, 0.0682516545, 0.757352948, 0.6143791 });
regionMap.put("fork_4.jpg", new double[] { 0.254901975, 0.185898721, 0.5232843, 0.594771266 });
regionMap.put("fork_5.jpg", new double[] { 0.2365196, 0.128709182, 0.5845588, 0.71405226 });
regionMap.put("fork_6.jpg", new double[] { 0.115196079, 0.133611143, 0.676470637, 0.6993464 });
regionMap.put("fork_7.jpg", new double[] { 0.164215669, 0.31008172, 0.767156839, 0.410130739 });
regionMap.put("fork_8.jpg", new double[] { 0.118872553, 0.318251669, 0.817401946, 0.225490168 });
regionMap.put("fork_9.jpg", new double[] { 0.18259804, 0.2136765, 0.6335784, 0.643790841 });
regionMap.put("fork_10.jpg", new double[] { 0.05269608, 0.282303959, 0.8088235, 0.452614367 });
regionMap.put("fork_11.jpg", new double[] { 0.05759804, 0.0894935, 0.9007353, 0.3251634 });
regionMap.put("fork_12.jpg", new double[] { 0.3345588, 0.07315363, 0.375, 0.9150327 });
regionMap.put("fork_13.jpg", new double[] { 0.269607842, 0.194068655, 0.4093137, 0.6732026 });
regionMap.put("fork_14.jpg", new double[] { 0.143382356, 0.218578458, 0.7977941, 0.295751631 })
regionMap.put("fork_15.jpg", new double[] { 0.19240196, 0.0633497, 0.5710784, 0.8398692 });
regionMap.put("fork_16.jpg", new double[] { 0.140931368, 0.480016381, 0.6838235, 0.240196079 });
regionMap.put("fork_17.jpg", new double[] { 0.305147052, 0.2512582, 0.4791667, 0.5408496 })
regionMap.put("fork_18.jpg", new double[] { 0.234068632, 0.445702642, 0.6127451, 0.344771236 });
regionMap.put("fork_19.jpg", new double[] { 0.219362751, 0.141781077, 0.5919118, 0.6683006 });
regionMap.put("fork_20.jpg", new double[] { 0.180147052, 0.239820287, 0.6887255, 0.235294119 });

System.out.println("Adding images...");
for (int i = 1; i <= 20; i++) {
    String fileName = "fork_" + i + ".jpg";
    byte[] contents = GetImage("/fork", fileName);
    AddImageToProject(trainer, project, fileName, contents, forkTag.id(), regionMap.get(fileName));
}

for (int i = 1; i <= 20; i++) {
    String fileName = "scissors_" + i + ".jpg";
    byte[] contents = GetImage("/scissors", fileName);
    AddImageToProject(trainer, project, fileName, contents, scissorsTag.id(), regionMap.get(fileName));
}
```

Předchozí fragment kódu používá dvě pomocné funkce, které obrázky načítají jako streamy prostředků a nahrávají je do služby.

```java
private static void AddImageToProject(Trainings trainer, Project project, String fileName, byte[] contents, UUID tag, double[] regionValues)
{
    System.out.println("Adding image: " + fileName);
    ImageFileCreateEntry file = new ImageFileCreateEntry()
        .withName(fileName)
        .withContents(contents);

    ImageFileCreateBatch batch = new ImageFileCreateBatch()
        .withImages(Collections.singletonList(file));

    // If Optional region is specified, tack it on and place the tag there, otherwise
    // add it to the batch.
    if (regionValues != null)
    {
        Region region = new Region()
            .withTagId(tag)
            .withLeft(regionValues[0])
            .withTop(regionValues[1])
            .withWidth(regionValues[2])
            .withHeight(regionValues[3]);
        file = file.withRegions(Collections.singletonList(region));
    } else {
        batch = batch.withTagIds(Collections.singletonList(tag));
    }

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

Vytvoří se tím první iterace v projektu a označí tuto iteraci jako výchozí iteraci. 

```java
System.out.println("Training...");
Iteration iteration = trainer.trainProject(project.id());
while (iteration.status().equals("Training"))
{
    System.out.println("Training Status: "+ iteration.status());
    Thread.sleep(5000);
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
byte[] testImage = GetImage("/ObjectTest", "test_image.jpg");

// predict
ImagePrediction results = predictor.predictions().predictImage()
    .withProjectId(project.id())
    .withImageData(testImage)
    .execute();

for (Prediction prediction: results.predictions())
{
    System.out.println(String.format("\t%s: %.2f%% at: %.2f, %.2f, %.2f, %.2f",
        prediction.tagName(),
        prediction.probability() * 100.0f,
        prediction.boundingBox().left(),
        prediction.boundingBox().top(),
        prediction.boundingBox().width(),
        prediction.boundingBox().height()
    ));
}
```

## <a name="run-the-example"></a>Spuštění příkladu

Předpověď se zobrazí v konzole spolu s protokolováním pro zobrazení průběhu.

Kompilace a spuštění řešení pomocí Mavenu:

```
mvn compile exec:java
```