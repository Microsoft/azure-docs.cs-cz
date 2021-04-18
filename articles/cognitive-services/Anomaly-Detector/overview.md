---
title: Co je rozhraní API Detektoru anomálií?
titleSuffix: Azure Cognitive Services
description: Pomocí algoritmu rozhraní API pro detekci anomálií můžete pro data časových řad použít detekci anomálií.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 02/16/2021
ms.author: mbullwin
keywords: detekce anomálií, strojové učení, algoritmy
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: d63399d0f492f85a4a2d57a595a6d8ef5b606d92
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599515"
---
# <a name="what-is-the-anomaly-detector-api"></a>Co je rozhraní API Detektoru anomálií?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Rozhraní API pro detekci anomálií umožňuje monitorovat a detekovat anomálie v datech časových řad bez nutnosti znát strojové učení. Algoritmy rozhraní API pro detekci anomálií se přizpůsobují tak, že automaticky identifikují a aplikují nejvhodnější modely na vaše data, bez ohledu na obor, scénář nebo objem dat. Pomocí dat časových řad rozhraní API určuje hranice pro detekci anomálií, očekávané hodnoty a které datové body jsou anomálie.

![Zjistit změny vzoru v žádostech o služby](./media/anomaly_detection2.png)

Používání detektoru anomálií nevyžaduje žádné předchozí prostředí ve službě Machine Learning a rozhraní RESTful API umožňuje snadnou integraci služby do aplikací a procesů.

Tato dokumentace obsahuje následující typy článků:
* [Rychlé starty](./Quickstarts/client-libraries.md) jsou podrobné pokyny, které vám umožní volat službu a získat výsledky v krátké době. 
* [Příručky](./how-to/identify-anomalies.md) návody obsahují pokyny k používání služby v konkrétnějším nebo přizpůsobeném způsobu.
* [Koncepční články](./concepts/anomaly-detection-best-practices.md) poskytují podrobné vysvětlení funkcí a funkcí služby.
* [Kurzy](./tutorials/batch-anomaly-detection-powerbi.md) jsou delší než příručky, které ukazují, jak tuto službu používat jako součást v širších obchodních řešeních.

## <a name="features"></a>Funkce

Pomocí detektoru anomálií můžete automaticky detekovat anomálie v rámci vašich dat časových řad nebo k nim v reálném čase.

|Funkce  |Popis  |
|---------|---------|
|Detekce anomálií v reálném čase. | Pomocí dříve zjištěných datových bodů můžete zjistit anomálie v datech streamování, abyste zjistili, jestli je vaše nejnovější anomálií. Tato operace generuje model pomocí datových bodů, které odesíláte, a určuje, zda je cílovým bodem anomálie. Voláním rozhraní API s každým novým datovým bodem, který vygenerujete, můžete monitorovat data tak, jak jsou vytvořená. |
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
1. Přihlaste se a v pravém horním rohu vyberte klonovat.
1. Před dokončením operace klonování zrušte v dialogovém okně možnost Public. v opačném případě bude veřejný Poznámkový blok, včetně všech klíčů předplatného.
1. Vybrat **Spustit při bezplatném výpočetním** prostředí
1. Vyberte jeden z poznámkových bloků.
1. Do proměnné přidejte svůj platný klíč předplatného rozhraní API detektoru anomálií `subscription_key` .
1. Změňte `endpoint` proměnnou na koncový bod. Příklad: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. V horním panelu nabídek vyberte položku **buňka** a pak možnost **Spustit vše**.

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

## <a name="service-availability-and-redundancy"></a>Dostupnost služby a redundance

### <a name="is-the-anomaly-detector-service-zone-resilient"></a>Je zóna služeb anomálie odolná proti chybám?

Ano. Služba detektor anomálií je ve výchozím nastavení odolná proti zónám.

### <a name="how-do-i-configure-the-anomaly-detector-service-to-be-zone-resilient"></a>Návody nakonfigurovat službu Anomáliého detektoru tak, aby byla odolná proti zóně?

Pro povolení odolnosti zóny není nutná žádná konfigurace zákazníka. Zóna – odolnost u prostředků detektoru anomálií je ve výchozím nastavení dostupná a spravovaná samotnými službami.

## <a name="deploy-on-premises-using-docker-containers"></a>Nasazení místně pomocí kontejnerů Docker

[Pomocí kontejnerů detektoru anomálií](anomaly-detector-container-howto.md) nasaďte funkce rozhraní API místně. Kontejnery Docker umožňují přenést službu blíž k vašim datům, aby bylo možné dodržovat dodržování předpisů, zabezpečení nebo jiné provozní důvody.

## <a name="join-the-anomaly-detector-community"></a>Zapojení do komunity Detektoru anomálií

* Připojit se ke [skupině poradců pro detekci anomálií v Microsoft Teams](https://aka.ms/AdAdvisorsJoin)
* Zobrazit vybraný [obsah generovaný uživatelem](user-generated-content.md)

## <a name="next-steps"></a>Další kroky

* [Rychlý Start: zjištění anomálií v datech časových řad pomocí detektoru anomálií](quickstarts/client-libraries.md)
* [Online ukázka](https://github.com/Azure-Samples/AnomalyDetector/tree/master/ipython-notebook) rozhraní API pro detekci anomálií
* [Referenční informace o REST API](https://aka.ms/anomaly-detector-rest-api-ref) detektoru anomálií
