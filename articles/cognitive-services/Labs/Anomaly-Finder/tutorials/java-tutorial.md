---
title: Detekce anomálií aplikace v Javě – Microsoft Cognitive Services | Dokumentace Microsoftu
description: Prozkoumejte aplikace v Javě, která používá rozhraní API pro detekci anomálií v Microsoft Cognitive Services. Odeslat původní datové body k rozhraní API a získat očekávanou hodnotu a anomálií body.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 228d440da358eba1322e2228c54f21e925e36ecd
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38597944"
---
# <a name="anomaly-detection-java-application"></a>Aplikace v Javě detekce anomálií

Tento článek ukazuje použití jednoduché aplikace v Javě pro volání rozhraní API pro detekci anomálií.  
V příkladu s klíči předplatného odešle data časových řad pro rozhraní API pro detekci anomálií a získá všechny body anomálií a očekávaná hodnota pro každý datový bod z rozhraní API.

## <a name="prerequisites"></a>Požadavky

### <a name="platform-requirements"></a>Požadavky na platformu

V tomto kurzu byla vyvinuta pomocí [IntelliJ IDEA](https://www.jetbrains.com/idea). A také je potřeba nainstalovat [Java Development Kit (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/index.html) verze 1.8 + a aktuální [nástroje pro Apache Maven](http://maven.apache.org/) nástroj sestavení.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Přihlaste se k detekci anomálií odběru a získat klíč předplatného 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]
 

## <a name="download-the-tutorial-project"></a>Stáhněte si kurz projekt

1. Přejděte MicrosoftAnomalyDetection [Java úložiště](https://github.com/MicrosoftAnomalyDetection/java-sample).
2. Klon klepněte na tlačítko Stáhnout.
3. Klikněte na tlačítko Stáhnout ZIP ke stažení souboru .zip kurz projektu.

<a name="Step1"></a>
### <a name="open-the-tutorial-project"></a>Otevřete projekt kurz

1. Extrahování souboru ZIP projektu kurz.
2. V IntelliJ IDEA, klikněte na tlačítko **soubor > Otevřít**, zobrazí se dialogové okno otevření souboru nebo projektu.
3. Vyberte kořenovou cestu extrahovaný projekt a pak klikněte na tlačítko OK.
4. Na panelu projekty rozbalte **src > hlavní > java**.
5. Dvakrát klikněte na panel com.microsoft.cognitiveservice.anomalydetection.Main.java načíst soubor v editoru.

<a name="Step2"></a>
### <a name="replace-subscriptionkey-and-uri-region"></a>Nahraďte subscriptionKey a identifikátor URI oblasti

```
// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
public static final String subscriptionKey = "<Subscription Key>";

public static final String uriBase = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

```

<a name="Step3"></a>
### <a name="build-and-run-the-tutorial-project"></a>Sestavte a spusťte projekt kurz

1. V nabídce otevřete kliknutím pravým tlačítkem myši kdekoli v kartě com.microsoft.cognitiveservice.anomalydetection.Main.java zdrojového kódu. 
2. Vybrat spuštění 'Main.main().
3. Výsledek ukázkový požadavek se vrátí a zobrazí v terminálu.

### <a name="result-of-the-tutorial-project"></a>Výsledek projekt kurz

[!INCLUDE [diagrams](../includes/diagrams.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [REST API – referenční informace](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
