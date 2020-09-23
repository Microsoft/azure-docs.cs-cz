---
title: Co je rozhraní API Detektoru anomálií?
titleSuffix: Azure Cognitive Services
description: Pomocí pokročilých algoritmů rozhraní API pro detekci anomálií můžete identifikovat anomálie v datech časových řad.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: ba094709d78d92b678e3aa5ad9995923888caf00
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90887505"
---
# <a name="what-is-the-anomaly-detector-api"></a>Co je rozhraní API Detektoru anomálií?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Rozhraní API pro detekci anomálií umožňuje monitorovat a zjišťovat anomálie v datech časových řad pomocí strojového učení. Rozhraní API detektoru anomálií se přizpůsobí tím, že se automaticky identifikují a aplikují nejvhodnější modely pro vaše data, bez ohledu na obor, scénář nebo objem dat. Pomocí dat časových řad rozhraní API určuje hranice pro detekci anomálií, očekávané hodnoty a které datové body jsou anomálie.

![Zjistit změny vzoru v žádostech o služby](./media/anomaly_detection2.png)

Používání detektoru anomálií nevyžaduje žádné předchozí prostředí ve službě Machine Learning a rozhraní RESTful API umožňuje snadnou integraci služby do aplikací a procesů.

## <a name="features"></a>Funkce

Pomocí detektoru anomálií můžete automaticky detekovat anomálie v rámci vašich dat časových řad nebo k nim v reálném čase.

|Funkce  |Popis  |
|---------|---------|
|Detekuje anomálie při výskytu v reálném čase. | Pomocí dříve zjištěných datových bodů můžete zjistit anomálie v datech streamování, abyste zjistili, jestli je vaše nejnovější anomálií. Tato operace generuje model pomocí datových bodů, které odesíláte, a určuje, zda je cílovým bodem anomálie. Voláním rozhraní API s každým novým datovým bodem, který vygenerujete, můžete monitorovat data tak, jak jsou vytvořená. |
|Detekuje anomálie v celé sadě dat jako dávku. | Použijte svoji časovou řadu ke zjištění anomálií, které by mohly existovat v rámci vašich dat. Tato operace vygeneruje model s využitím celých dat časových řad, přičemž každý bod se analyzuje se stejným modelem.         |
|Detekuje body změn v celé sadě dat jako dávku. | Použijte svoji časovou řadu ke zjištění všech bodů změny trendů, které existují ve vašich datech. Tato operace vygeneruje model s využitím celých dat časových řad, přičemž každý bod se analyzuje se stejným modelem.    |
| Získejte další informace o vašich datech. | Získáte užitečné podrobnosti o datech a všech pozorovaných anomáliích, včetně očekávaných hodnot, hranic anomálií a pozic. |
| Upravte hranice detekce anomálií. | Rozhraní API detektoru anomálií automaticky vytvoří hranice pro detekci anomálií. Upravte tyto hranice tak, aby se zvýšil nebo snížila citlivost rozhraní API na anomálie dat a aby lépe vyhovovala vašim datům. |

## <a name="demo"></a>Ukázka

Podívejte se na tuto [interaktivní ukázku](https://aka.ms/adDemo) , abyste porozuměli tomu, jak detektor anomálií funguje.
Pokud chcete ukázku spustit, musíte vytvořit prostředek detektoru anomálií a získat klíč rozhraní API a koncový bod.

## <a name="notebook"></a>Poznámkový blok

Pokud se chcete dozvědět, jak volat rozhraní API pro detekci anomálií, zkuste tento [Poznámkový blok](https://aka.ms/adNotebook). V tomto Jupyter Notebook se dozvíte, jak odeslat požadavek rozhraní API a vizualizovat výsledek.

Chcete-li spustit Poznámkový blok, proveďte následující kroky:

1. Získejte platný klíč předplatného rozhraní API detektoru anomálií a koncový bod rozhraní API. Následující část obsahuje pokyny k registraci.
1. Přihlaste se a v pravém horním rohu klikněte na klonovat.
1. Před dokončením operace klonování zrušte v dialogovém okně možnost Public. v opačném případě bude veřejný Poznámkový blok, včetně všech klíčů předplatného.
1. Klikněte na **Spustit na bezplatné výpočetní** prostředky
1. Vyberte jeden z poznámkových bloků.
1. Do proměnné přidejte svůj platný klíč předplatného rozhraní API detektoru anomálií `subscription_key` .
1. Změňte `endpoint` proměnnou na koncový bod. Příklad: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. V horním řádku nabídek klikněte na **buňka**a pak na **Spustit vše**.

## <a name="workflow"></a>Pracovní postup

Rozhraní API pro detekci anomálií je webová služba RESTful, která usnadňuje volání ze všech programovacích jazyků, které mohou provádět požadavky HTTP a analyzovat JSON.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Po registraci:

1. Vezměte data časové řady a převeďte je do platného formátu JSON. Pro dosažení nejlepších výsledků používejte [osvědčené postupy](concepts/anomaly-detection-best-practices.md) při přípravě dat.
1. Odešlete žádost do rozhraní API detektoru anomálií s Vašimi daty.
1. Zpracujte odpověď rozhraní API parsováním vrácené zprávy JSON.

## <a name="algorithms"></a>Algoritmy

* V následujících technických blogech najdete informace o použitých algoritmech:
    * [Představujeme rozhraní API Detektoru anomálií Azure](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162)
    * [Přehled algoritmu SR-CNN v detektoru anomálií Azure](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Overview-of-SR-CNN-algorithm-in-Azure-Anomaly-Detector/ba-p/982798)

[Službu pro detekci anomálií](https://arxiv.org/abs/1906.03821) na papíře si můžete přečíst v článku Microsoft (PŘIJATÝ serverem konference KDD 2019), kde najdete další informace o algoritmech SR-CNN vyvinutých společností Microsoft.


> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="join-the-anomaly-detector-community"></a>Zapojení do komunity Detektoru anomálií

* Připojit se ke [skupině poradců pro detekci anomálií v Microsoft Teams](https://aka.ms/AdAdvisorsJoin)
* Zobrazit vybraný [obsah generovaný uživatelem](user-generated-content.md)

## <a name="next-steps"></a>Další kroky

* [Rychlý Start: zjištění anomálií v datech časových řad pomocí REST API detektoru anomálií](quickstarts/detect-data-anomalies-csharp.md)
* [Online ukázka](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector) rozhraní API pro detekci anomálií
* [Referenční informace o REST API](https://aka.ms/anomaly-detector-rest-api-ref) detektoru anomálií
