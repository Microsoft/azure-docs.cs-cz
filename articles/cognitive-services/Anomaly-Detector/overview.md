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
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 70cb4a008391ad55562bc55fb45d877988e68643
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854773"
---
# <a name="what-is-the-anomaly-detector-api"></a>Co je rozhraní API Detektoru anomálií?

Rozhraní API pro detekci anomálií umožňuje monitorovat a zjišťovat anomálie v datech časových řad pomocí strojového učení. Rozhraní API detektoru anomálií se přizpůsobí tím, že se automaticky identifikují a aplikují nejvhodnější modely pro vaše data, bez ohledu na obor, scénář nebo objem dat. Pomocí dat časových řad rozhraní API určuje hranice pro detekci anomálií, očekávané hodnoty a které datové body jsou anomálie.

![Zjistit změny vzoru v žádostech o služby](./media/anomaly_detection2.png)

Používání detektoru anomálií nevyžaduje žádné předchozí prostředí ve službě Machine Learning a rozhraní RESTful API umožňuje snadnou integraci služby do aplikací a procesů.

## <a name="features"></a>Funkce

Pomocí detektoru anomálií můžete automaticky detekovat anomálie v rámci vašich dat časových řad nebo k nim v reálném čase. 

|Funkce  |Popis  |
|---------|---------|
|Detekuje anomálie při výskytu v reálném čase. | Pomocí dříve zjištěných datových bodů můžete zjistit anomálie v datech streamování, abyste zjistili, jestli je vaše nejnovější anomálií. Tato operace generuje model pomocí datových bodů, které odesíláte, a určuje, zda je cílovým bodem anomálie. Voláním rozhraní API s každým novým datovým bodem, který vygenerujete, můžete monitorovat data tak, jak jsou vytvořená. |
|Detekuje anomálie v celé sadě dat jako dávku. | Použijte svoji časovou řadu ke zjištění anomálií, které by mohly existovat v rámci vašich dat. Tato operace vygeneruje model s využitím celých dat časových řad, přičemž každý bod se analyzuje se stejným modelem.         |
| Získejte další informace o vašich datech. | Získáte užitečné podrobnosti o datech a všech pozorovaných anomáliích, včetně očekávaných hodnot, hranic anomálií a pozic. |
| Upravte hranice detekce anomálií. | Rozhraní API detektoru anomálií automaticky vytvoří hranice pro detekci anomálií. Upravte tyto hranice tak, aby se zvýšil nebo snížila citlivost rozhraní API na anomálie dat a aby lépe vyhovovala vašim datům. |

## <a name="demo"></a>Ukázka

Podívejte se na tuto [interaktivní ukázku](https://aka.ms/adDemo) , abyste porozuměli tomu, jak detektor anomálií funguje.
Pokud chcete ukázku spustit, musíte vytvořit prostředek detektoru anomálií a získat klíč rozhraní API a koncový bod.

## <a name="notebook"></a>Poznámkový blok

Pokud se chcete dozvědět, jak volat rozhraní API pro detekci anomálií, vyzkoušejte si tento [notebook Azure](https://aka.ms/adNotebook). Tento Jupyter Notebook na webu ukazuje, jak odeslat požadavek rozhraní API a vizualizovat výsledek.

Chcete-li spustit Poznámkový blok, proveďte následující kroky:

1. Získejte platný klíč předplatného rozhraní API detektoru anomálií a koncový bod rozhraní API. Následující část obsahuje pokyny k registraci.
1. Přihlaste se a v pravém horním rohu klikněte na klonovat.
1. Před dokončením operace klonování zrušte v dialogovém okně možnost Public. v opačném případě bude veřejný Poznámkový blok, včetně všech klíčů předplatného.
1. Klikněte na **Spustit na bezplatné výpočetní** prostředky
1. Vyberte jeden z poznámkových bloků.
1. Do `subscription_key` proměnné přidejte svůj platný klíč předplatného rozhraní API detektoru anomálií. 
1. `endpoint` Změňte proměnnou na koncový bod. Příklad: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
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

* Podívejte se prosím na tento technický blog, který [zavádí rozhraní API](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162) pro detekci anomálií Azure o algoritmech v digestoři.
* Informace o algoritmech SR-CNN, které vyvinul Microsoft, najdete v této [službě](https://arxiv.org/abs/1906.03821) pro detekci anomálií pro konference KDD 2019.

> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="next-steps"></a>Další kroky

* [Rychlé zprovoznění: Detekci anomálií v datech časových řad pomocí REST API detektoru anomálií](quickstarts/detect-data-anomalies-csharp.md)
* [Online ukázka](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector) rozhraní API pro detekci anomálií
* [Referenční informace o REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) detektoru anomálií
