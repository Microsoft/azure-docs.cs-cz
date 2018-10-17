---
title: 'Kurz: Detekce anomálií, Java'
titlesuffix: Azure Cognitive Services
description: Prozkoumejte aplikaci Javy, která používá rozhraní API pro detekci anomálií. Odešlete původní datové body do rozhraní API a získejte očekávanou hodnotu a body anomálie.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.component: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 4b544e2e59a40cebf75042c4040b84bceebcecf7
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2018
ms.locfileid: "48887696"
---
# <a name="tutorial-anomaly-detection-with-java-application"></a>Kurz: Detekce anomálií pomocí aplikace Javy

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Tento článek ukazuje použití jednoduché aplikace Javy k volání rozhraní API pro detekci anomálií.  
V příkladu budou do rozhraní API pro detekci anomálií odeslána data časové řady s vaším klíčem předplatného. Poté budou z rozhraní API získány všechny body anomálií a očekávané hodnoty pro každý datový bod.

## <a name="prerequisites"></a>Požadavky

### <a name="platform-requirements"></a>Požadavky platformy

Tento kurz jsme vyvinuli v [IntelliJ IDEA](https://www.jetbrains.com/idea). Také je potřeba nainstalovat [Java Development Kit (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/index.html) verze 1.8 nebo vyšší a aktuální kompilátor [Apache's Maven](http://maven.apache.org/).

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Předplaťte si službu detekce anomálií a získejte klíč předplatného 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]
 

## <a name="download-the-tutorial-project"></a>Stáhněte si výukový projekt

1. Přejděte do [repozitáře Javy](https://github.com/MicrosoftAnomalyDetection/java-sample) MicrosoftAnomalyDetection.
2. Klikněte na tlačítko Naklonovat nebo stáhnout.
3. Klikněte na možnost Stáhnout ZIP a stáhněte soubor ZIP výukového projektu.

<a name="Step1"></a>
### <a name="open-the-tutorial-project"></a>Otevřete výukový projekt

1. Rozbalte soubor ZIP výukového projektu.
2. V IntelliJ IDEA klikněte na možnost **Soubor > Otevřít**. Zobrazí se dialog Otevřít soubor nebo projekt.
3. Vyberte kořenovou cestu extrahovaného projektu a klikněte na tlačítko OK.
4. Na panelu Projekty rozbalte cestu **src > main > java**.
5. Dvojitým kliknutím na položku com.microsoft.cognitiveservice.anomalydetection.Main.java načtěte soubor do editoru.

<a name="Step2"></a>
### <a name="replace-subscriptionkey-and-uri-region"></a>Nahraďte hodnoty subscriptionKey a oblast URI

```
// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
public static final String subscriptionKey = "<Subscription Key>";

public static final String uriBase = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

```

<a name="Step3"></a>
### <a name="build-and-run-the-tutorial-project"></a>Sestavte a spusťte výukový projekt

1. Kliknutím kdekoli na kartě zdrojového kódu souboru com.microsoft.cognitiveservice.anomalydetection.Main.java otevřete nabídku. 
2. Vyberte možnost Spustit 'Main.main()'
3. Bude vrácen výsledek ukázkového požadavku, který se zobrazí v terminálu.

### <a name="result-of-the-tutorial-project"></a>Výsledek výukového projektu

[!INCLUDE [diagrams](../includes/diagrams.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [REST API – referenční informace](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
