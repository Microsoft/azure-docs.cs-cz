---
title: Co je rozhraní API Detektoru anomálií?
titleSuffix: Azure Cognitive Services
description: Pomocí pokročilých algoritmů rozhraní API detektoru anomálií můžete identifikovat anomálie v datech časových řad.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: 9237e670dd8d43c4036f996c477948944718e3aa
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80053699"
---
# <a name="what-is-the-anomaly-detector-api"></a>Co je rozhraní API Detektoru anomálií?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Rozhraní API detektoru anomálií umožňuje sledovat a detekovat abnormality v datech časových řad pomocí strojového učení. Rozhraní API detektoru anomálií se přizpůsobí automatickou identifikací a použitím nejvhodnějších modelů pro vaše data, bez ohledu na odvětví, scénář nebo objem dat. Pomocí dat časových řad rozhraní API určuje hranice pro detekci anomálií, očekávané hodnoty a datové body jsou anomálie.

![Zjišťování změn vzoru v žádostech o služby](./media/anomaly_detection2.png)

Použití detektoru anomálií nevyžaduje žádné předchozí zkušenosti ve strojovém učení a ROZHRANÍ RESTful API umožňuje snadno integrovat službu do vašich aplikací a procesů.

## <a name="features"></a>Funkce

Pomocí detektoru anomálií můžete automaticky detekovat anomálie v datech časových řad nebo tak, jak se vyskytují v reálném čase.

|Funkce  |Popis  |
|---------|---------|
|Detekujte anomálie, jak se vyskytují v reálném čase. | Zjistěte anomálie ve streamovaných datech pomocí dříve viděných datových bodů k určení, zda je vaše nejnovější anomálie. Tato operace generuje model pomocí datových bodů, které odesíláte a určuje, zda cílový bod je anomálie. Voláním rozhraní API s každým novým datovým bodem, který generujete, můžete sledovat data při jejich vytváření. |
|Detekujte anomálie v celé sadě dat jako dávku. | Pomocí časových řad můžete zjistit všechny anomálie, které by mohly existovat ve vašich datech. Tato operace generuje model s využitím celých dat časové řady, přičemž každý bod je analyzován stejným modelem.         |
| Získejte další informace o svých datech. | Získejte užitečné podrobnosti o vašich datech a všech pozorovaných anomáliích, včetně očekávaných hodnot, hranic anomálií a pozic. |
| Upravte hranice detekce anomálií. | Rozhraní API detektoru anomálií automaticky vytváří hranice pro detekci anomálií. Upravte tyto hranice, abyste zvýšili nebo snížili citlivost rozhraní API na anomálie dat a lépe vyhovovali vašim datům. |

## <a name="demo"></a>Ukázka

Podívejte se na tuto [interaktivní ukázku,](https://aka.ms/adDemo) abyste pochopili, jak funguje detektor anomálií.
Chcete-li spustit ukázku, musíte vytvořit prostředek detektoru anomálií a získat klíč rozhraní API a koncový bod.

## <a name="notebook"></a>Poznámkový blok

Chcete-li se dozvědět, jak volat rozhraní API detektoru anomálií, zkuste tento [poznámkový blok Azure](https://aka.ms/adNotebook). Tento webový poznámkový blok Jupyter vám ukáže, jak odeslat požadavek rozhraní API a vizualizovat výsledek.

Chcete-li poznámkový blok spustit, proveďte následující kroky:

1. Získejte platný klíč předplatného rozhraní API detektoru anomálií a koncový bod rozhraní API. Níže uvedená část obsahuje pokyny pro registraci.
1. Přihlaste se a v pravém horním rohu klikněte na Klonovat.
1. Zrušit zaškrtnutí možnosti "veřejné" v dialogovém okně před dokončením operace klonování, jinak bude váš poznámkový blok, včetně klíčů předplatného, veřejný.
1. Klikněte **na Spustit na volné výpočetní prostředky**
1. Vyberte jeden z poznámkových bloků.
1. Přidejte do `subscription_key` proměnné platný klíč předplatného rozhraní API detektoru anomálií.
1. Změňte `endpoint` proměnnou na koncový bod. Příklad: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. Na horním řádku nabídek klikněte na **Buňka**a potom **na Spustit vše**.

## <a name="workflow"></a>Pracovní postup

Rozhraní API detektoru anomálií je webová služba RESTful, což usnadňuje volání z libovolného programovacího jazyka, který může provádět požadavky HTTP a analyzovat JSON.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Po registraci:

1. Vezměte si data časových řad a převeďte je do platného formátu JSON. Při přípravě dat používejte [osvědčené postupy,](concepts/anomaly-detection-best-practices.md) abyste získali co nejlepší výsledky.
1. Odešlete požadavek do rozhraní API detektoru anomálií s vašimi daty.
1. Zpracujte odpověď rozhraní API parsováním vrácené zprávy JSON.

## <a name="algorithms"></a>Algoritmy

* Informace o použitých algoritmech naleznete v následujících technických blozích:
    * [Představujeme rozhraní API Detektoru anomálií Azure](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162)
    * [Přehled algoritmu SR-CNN v Azure Anomaly Detector](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Overview-of-SR-CNN-algorithm-in-Azure-Anomaly-Detector/ba-p/982798)

Můžete si přečíst článek [Time-Series Anomaly Detection Service ve společnosti Microsoft](https://arxiv.org/abs/1906.03821) (akceptovaný KDD 2019), kde se dozvíte více o algoritmech SR-CNN vyvinutých společností Microsoft.


> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="join-the-anomaly-detector-community"></a>Zapojení do komunity Detektoru anomálií

* Připojte se ke [skupině Poradce detektoru anomálií v Microsoft Teams](https://aka.ms/AdAdvisorsJoin)
* Zobrazit vybraný [obsah generovaný uživatelem](user-generated-content.md)

## <a name="next-steps"></a>Další kroky

* [Úvodní příručka: Detekce anomálií v datech časových řad pomocí rozhraní REST API detektoru anomálií](quickstarts/detect-data-anomalies-csharp.md)
* [Online ukázka](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector) rozhraní API pro detektory anomálií
* Odkaz REST [API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) detektoru anomálií
