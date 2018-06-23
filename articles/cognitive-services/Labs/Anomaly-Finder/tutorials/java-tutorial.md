---
title: Detekce anomálií aplikace v jazyce Java - kognitivní služby Microsoft | Microsoft Docs
description: Prozkoumejte aplikaci Java, která používá rozhraní API detekce anomálií v kognitivní služby společnosti Microsoft. Odesílat původní datové body k rozhraní API a očekávaná hodnota a body anomálií.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 228d440da358eba1322e2228c54f21e925e36ecd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343011"
---
# <a name="anomaly-detection-java-application"></a>Aplikace v jazyce Java detekce anomálií

Tento článek ukazuje použití jednoduchou aplikaci Java volat rozhraní API detekce anomálií.  
V příkladu odešle data časové řady na rozhraní API detekce anomálií s svůj klíč předplatného a pak získá všechny body anomálií a očekávaná hodnota pro každý datový bod z rozhraní API.

## <a name="prerequisites"></a>Požadavky

### <a name="platform-requirements"></a>Požadavky na platformu

V tomto kurzu byla vyvinuta pomocí [IntelliJ IDEA](https://www.jetbrains.com/idea). A také je potřeba nainstalovat [Java Development Kit (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/index.html) verze 1.8 + a aktuální [na Apache Maven](http://maven.apache.org/) nástrojem sestavení.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Přihlášení k odběru detekce anomálií a získat klíč předplatného 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]
 

## <a name="download-the-tutorial-project"></a>Stáhněte si kurz projekt

1. Přejděte na MicrosoftAnomalyDetection [Java úložiště](https://github.com/MicrosoftAnomalyDetection/java-sample).
2. Klikněte na tlačítko klonu nebo tlačítko Stáhnout.
3. Klikněte na tlačítko Stáhnout ZIP ke stažení souboru .zip kurz projektu.

<a name="Step1"></a>
### <a name="open-the-tutorial-project"></a>Otevřete projekt kurz

1. Rozbalte soubor .zip kurz projektu.
2. V IntelliJ IDEA, klikněte na **soubor > Otevřít**, zobrazí se dialogové okno otevření souboru nebo projektu.
3. Vyberte kořenové cestě extrahované projektu a potom klikněte na tlačítko OK.
4. V panelu projekty rozbalte **src > hlavní > java**.
5. Dvakrát klikněte na com.microsoft.cognitiveservice.anomalydetection.Main.java načíst soubor do editoru.

<a name="Step2"></a>
### <a name="replace-subscriptionkey-and-uri-region"></a>Nahraďte subscriptionKey a identifikátor URI oblast

```
// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
public static final String subscriptionKey = "<Subscription Key>";

public static final String uriBase = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

```

<a name="Step3"></a>
### <a name="build-and-run-the-tutorial-project"></a>Vytvoření a spuštění projektu kurz

1. V nabídce otevřete kliknutím pravým tlačítkem myši kamkoli na kartě kód com.microsoft.cognitiveservice.anomalydetection.Main.java zdroje. 
2. Vyberte možnost spustit 'Main.main().
3. Výsledek ukázková žádost bude vrácen a zobrazen v terminálu.

### <a name="result-of-the-tutorial-project"></a>Výsledek kurz projektu

[!INCLUDE [diagrams](../includes/diagrams.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [REST API – referenční informace](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
