---
title: Rychlý Start pro lineárníí knihovny softwaru Java pro detekci anomálií
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: f2e227b2a589955191a2e602495cf0ffbb3f6d8b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732357"
---
Začněte s klientskou knihovnou anomálie lineární pro Java. Pomocí těchto kroků nainstalujete balíček Start s použitím algoritmů poskytovaných službou. Nová rozhraní API pro detekci anomálií lineární umožňují vývojářům snadnou integraci pokročilého AI pro detekci anomálií ze skupin metrik, aniž by to mělo znalosti strojového učení nebo data s popisky. Závislosti a vzájemné korelace mezi různými signály se automaticky počítají jako klíčové faktory. To vám pomůže aktivně chránit komplexní systémy před chybami.

Pomocí klientské knihovny lineární detektoru anomálií pro jazyk Java:

* Zjišťuje anomálie na úrovni systému ze skupiny časových řad.
* Když kterákoli z jednotlivých časových řad neřekne hodně a musíte se podívat na všechny signály a zjistit problém.
* Predicative údržbu drahých fyzických prostředků s desítkou až stovkami různých typů senzorů, které měří různé aspekty stavu systému.

[Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/anomalydetector/azure-ai-anomalydetector)  |  [Balíček (Maven)](https://repo1.maven.org/maven2/com/azure/azure-ai-anomalydetector/3.0.0-beta.2/)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* Aktuální verze sady [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Nástroj Gradle Build](https://gradle.org/install/)nebo jiný správce závislostí.
* Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" vytvořte prostředek pro detekci anomálií "  target="_blank"> vytvořením prostředku detektoru anomálií </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Počkejte na nasazení a klikněte na tlačítko **Přejít k prostředku** .
    * K připojení aplikace k rozhraní API detektoru anomálií budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-gradle-project"></a>Vytvořit nový projekt Gradle

V tomto rychlém startu se používá správce závislostí Gradle. Další informace o klientské knihovně najdete v [úložišti Maven Central](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor).

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp && cd myapp
```

Spusťte `gradle init` příkaz z pracovního adresáře. Tento příkaz vytvoří základní soubory sestavení pro Gradle, včetně *Build. Gradle. kts* , který se používá za běhu k vytvoření a konfiguraci vaší aplikace.

```console
gradle init --type basic
```

Po zobrazení výzvy k výběru **DSL** vyberte **Kotlin**.

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Vyhledejte *Build. Gradle. kts* a otevřete ho pomocí vašeho preferovaného integrovaného vývojového prostředí (IDE) nebo textového editoru. Pak zkopírujte do této konfigurace sestavení. Nezapomeňte zahrnout závislosti projektu.

```kotlin
dependencies {
    compile("com.azure:azure-ai-anomalydetector")
}
```

### <a name="create-a-java-file"></a>Vytvoření souboru Java

Vytvořte složku pro ukázkovou aplikaci. V pracovním adresáři spusťte následující příkaz:

```console
mkdir -p src/main/java
```

Přejděte do nové složky a vytvořte soubor s názvem *MetricsAdvisorQuickstarts. Java*. Otevřete ho v preferovaném editoru nebo integrovaném vývojovém prostředí a přidejte následující `import` příkazy:

```java
package com.azure.ai.anomalydetector;

import com.azure.ai.anomalydetector.models.*;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.core.http.*;
import com.azure.core.http.policy.*;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.http.rest.PagedResponse;
import com.azure.core.http.rest.Response;
import com.azure.core.http.rest.StreamResponse;
import com.azure.core.util.Context;
import reactor.core.publisher.Flux;

import java.io.FileOutputStream;
import java.io.IOException;
import java.io.UncheckedIOException;
import java.nio.ByteBuffer;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.time.*;
import java.time.format.DateTimeFormatter;
import java.util.Iterator;
import java.util.List;
import java.util.UUID;
import java.util.stream.Collectors;
```

Vytvořte proměnné koncového bodu a klíče Azure prostředku. Vytvořte další proměnnou pro ukázkový datový soubor.

```java
String key = "YOUR_API_KEY";
String endpoint = "YOUR_ENDPOINT";
```

 Aby bylo možné používat rozhraní API pro detekci anomálií lineární, musíme před použitím detekce vyškolit náš vlastní model. Data použitá pro školení jsou dávkou časových řad, přičemž každá časová řada by měla být ve formátu CSV se dvěma sloupci, časovým razítkem a hodnotou. Všechny časové řady by měly být komprimované do jednoho souboru zip a nahrály do [Azure Blob Storage](../../../../storage/blobs/storage-blobs-introduction.md). Ve výchozím nastavení se název souboru použije k reprezentaci proměnné pro časovou řadu. Alternativně je možné do souboru ZIP zahrnout další meta.js, pokud chcete, aby název proměnné byl jiný než název souboru. zip. Po vygenerování [adresy URL (SAS) objektu BLOB (Shared Access signatury)](../../../../storage/common/storage-sas-overview.md)můžeme pro školení použít adresu URL souboru ZIP.

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce pomocí klientské knihovny pro detekci anomálií pro Node.js:

* [Ověření klienta](#authenticate-the-client)
* [Učení modelu](#train-a-model)
* [Detekovat anomálie](#detect-anomalies)
* [Exportovat model](#export-model)
* [Odstranit model](#delete-model)

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte instanci `anomalyDetectorClient` objektu s vaším koncovým bodem a přihlašovacími údaji.

```java
HttpHeaders headers = new HttpHeaders()
    .put("Accept", ContentType.APPLICATION_JSON);

HttpPipelinePolicy authPolicy = new AzureKeyCredentialPolicy(key,
    new AzureKeyCredential(key));
AddHeadersPolicy addHeadersPolicy = new AddHeadersPolicy(headers);

HttpPipeline httpPipeline = new HttpPipelineBuilder().httpClient(HttpClient.createDefault())
    .policies(authPolicy, addHeadersPolicy).build();
// Instantiate a client that will be used to call the service.
HttpLogOptions httpLogOptions = new HttpLogOptions();
httpLogOptions.setLogLevel(HttpLogDetailLevel.BODY_AND_HEADERS);

AnomalyDetectorClient anomalyDetectorClient = new AnomalyDetectorClientBuilder()
    .pipeline(httpPipeline)
    .endpoint(endpoint)
    .httpLogOptions(httpLogOptions)
    .buildClient();
```

## <a name="train-a-model"></a>Učení modelu

### <a name="construct-a-model-result-and-train-model"></a>Sestavit výsledek modelu a model výuky

Nejdřív musíme vytvořit požadavek na model. Ujistěte se, že počáteční a koncový čas se zarovnává se zdrojem dat.

 Aby bylo možné používat rozhraní API pro detekci anomálií lineární, musíme před použitím detekce vyškolit náš vlastní model. Data použitá pro školení jsou dávkou časových řad, přičemž každá časová řada by měla být ve formátu CSV se dvěma sloupci, časovým razítkem a hodnotou. Všechny časové řady by měly být komprimované do jednoho souboru zip a nahrály do [Azure Blob Storage](../../../../storage/blobs/storage-blobs-introduction.md#blobs). Ve výchozím nastavení se název souboru použije k reprezentaci proměnné pro časovou řadu. Alternativně je možné do souboru ZIP zahrnout další meta.js, pokud chcete, aby název proměnné byl jiný než název souboru. zip. Po vygenerování [adresy URL (SAS) objektu BLOB (Shared Access signatury)](../../../../storage/common/storage-sas-overview.md)můžeme pro školení použít adresu URL souboru ZIP.

```java
Path path = Paths.get("test-data.csv");
List<String> requestData = Files.readAllLines(path);
List<TimeSeriesPoint> series = requestData.stream()
    .map(line -> line.trim())
    .filter(line -> line.length() > 0)
    .map(line -> line.split(",", 2))
    .filter(splits -> splits.length == 2)
    .map(splits -> {
        TimeSeriesPoint timeSeriesPoint = new TimeSeriesPoint();
        timeSeriesPoint.setTimestamp(OffsetDateTime.parse(splits[0]));
        timeSeriesPoint.setValue(Float.parseFloat(splits[1]));
        return timeSeriesPoint;
    })
    .collect(Collectors.toList());

Integer window = 28;
AlignMode alignMode = AlignMode.OUTER;
FillNAMethod fillNAMethod = FillNAMethod.LINEAR;
Integer paddingValue = 0;
AlignPolicy alignPolicy = new AlignPolicy().setAlignMode(alignMode).setFillNAMethod(fillNAMethod).setPaddingValue(paddingValue);
String source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
OffsetDateTime startTime = OffsetDateTime.of(2021, 1, 2, 0, 0, 0, 0, ZoneOffset.UTC);
;
OffsetDateTime endTime = OffsetDateTime.of(2021, 1, 3, 0, 0, 0, 0, ZoneOffset.UTC);
;
String displayName = "Devops-MultiAD";

ModelInfo request = new ModelInfo().setSlidingWindow(window).setAlignPolicy(alignPolicy).setSource(source).setStartTime(startTime).setEndTime(endTime).setDisplayName(displayName);
TrainMultivariateModelResponse trainMultivariateModelResponse = anomalyDetectorClient.trainMultivariateModelWithResponse(request, Context.NONE);
String header = trainMultivariateModelResponse.getDeserializedHeaders().getLocation();
String[] model_ids = header.split("/");
UUID model_id = UUID.fromString(model_ids[model_ids.length - 1]);
System.out.println(model_id);

Integer skip = 0;
Integer top = 5;
PagedIterable<ModelSnapshot> response = anomalyDetectorClient.listMultivariateModel(skip, top);
Iterator<PagedResponse<ModelSnapshot>> ite = response.iterableByPage().iterator();

while (true) {
    Response<Model> response_model = anomalyDetectorClient.getMultivariateModelWithResponse(model_id, Context.NONE);
    UUID model = response_model.getValue().getModelId();
    System.out.println(response_model.getStatusCode());
    System.out.println(response_model.getValue().getModelInfo().getStatus());
    System.out.println(model);
    if (response_model.getValue().getModelInfo().getStatus() == ModelStatus.READY) {
        break;
    }
}
```

## <a name="detect-anomalies"></a>Detekovat anomálie

```java
DetectionRequest detectionRequest = new DetectionRequest().setSource(source).setStartTime(startTime).setEndTime(endTime);
DetectAnomalyResponse detectAnomalyResponse = anomalyDetectorClient.detectAnomalyWithResponse(model_id, detectionRequest, Context.NONE);
String result = detectAnomalyResponse.getDeserializedHeaders().getLocation();

String[] result_list = result.split("/");
UUID result_id = UUID.fromString(result_list[result_list.length - 1]);

while (true) {
    DetectionResult response_result = anomalyDetectorClient.getDetectionResult(result_id);
    if (response_result.getSummary().getStatus() == DetectionStatus.READY) {
        break;
    }
    else if(response_result.getSummary().getStatus() == DetectionStatus.FAILED){

    }
}
```

## <a name="export-model"></a>Exportovat model

K vyexportování svého vyučeného modelu použijte `exportModelWithResponse` .

```java
StreamResponse response_export = anomalyDetectorClient.exportModelWithResponse(model_id, Context.NONE);
Flux<ByteBuffer> value = response_export.getValue();
FileOutputStream bw = new FileOutputStream("result.zip");
value.subscribe(s -> write(bw, s), (e) -> close(bw), () -> close(bw));
```

## <a name="delete-model"></a>Odstranit model

Pokud chcete odstranit existující model, který je k dispozici pro aktuální prostředek, použijte `deleteMultivariateModelWithResponse` funkci.

```java
Response<Void> deleteMultivariateModelWithResponse = anomalyDetectorClient.deleteMultivariateModelWithResponse(model_id, Context.NONE);
```

## <a name="run-the-application"></a>Spuštění aplikace

Aplikaci můžete vytvořit pomocí:

```console
gradle build
```
### <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci s `run` cílem:

```console
gradle run
```

## <a name="next-steps"></a>Další kroky

* [Osvědčené postupy pro detekci anomálií lineární](../../concepts/best-practices-multivariate.md)
