---
title: Co je rozhraní API Detektoru anomálií? | Dokumenty Microsoft
titleSuffix: Azure Cognitive Services
description: Pomocí pokročilých algoritmů rozhraní API detekce anomálií můžete identifikovat anomálie ve vašich datech časových řad.
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 38b23ee4bfa8a1dbcc11615425ccd580c23eb3e1
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593087"
---
# <a name="what-is-the-anomaly-detector-api"></a>Co je rozhraní API Detektoru anomálií?

Rozhraní API detekce anomálií vám umožňuje monitorovat a zjišťovat anomálie ve vašich datech časových řad pomocí služby machine learning. Automaticky identifikuje a použitím nejlepšího těsně modely ke svým datům, bez ohledu na odvětví, scénáři nebo objem dat se přizpůsobí API detekce anomálií. Použití dat časových řad, rozhraní API určuje hranice pro detekci anomálií, očekávané hodnoty, a které datové body jsou anomálie.

![Zjištění změn vzoru v žádosti o služby](./media/anomaly_detection2.png)

Pomocí detekce anomálií nevyžaduje žádné předchozí zkušenosti v strojového učení a rozhraní RESTful API umožňuje jednoduše integrovat službu do aplikace a procesy.

## <a name="features"></a>Funkce

Pomocí detekce anomálií můžete automaticky detekovat anomálie v rámci vašich datech časových řad, nebo když k nim dojde v reálném čase. 

|Funkce  |Popis  |
|---------|---------|
|Když k nim dojde v reálném čase zjišťovat anomálie. | Zjišťovat anomálie v streamovaných dat s použitím dříve zobrazenou datové body k určení, zda je nejnovější jeden anomálie. Tato operace vytvoří model s použitím datových bodů, Odeslat a určuje, zda je cílový bod anomálie. Voláním rozhraní API se každý datový bod, které vytvoříte, můžete monitorovat vaše data, jakmile je vytvořena. |
|Zjišťovat anomálie v celé datové sady v dávce. | Umožňuje detekovat případné anomálie, které mohou existovat v rámci vašich dat časové řady. Tato operace vytvoří model s použitím vašich dat celou časových řad s každý bod analyzovány pomocí stejného modelu.         |
| Získejte další informace o vašich datech. | Získejte užitečné informace o vašich dat a zjištěnou anomálie, včetně očekávané hodnoty, hranice anomálií a umístění. |
| Upravte hranice detekce anomálií. | Rozhraní API detekce anomálií automaticky vytvářet hranice pro detekci anomálií. Upravte tyto hranice chcete zvýšit nebo snížit citlivost rozhraní API pro data anomálie a lépe vyhovovaly vaše data. |

## <a name="demo"></a>Ukázka

Pokud chcete rychle začít používat rozhraní API detekce anomálií, zkuste [online ukázku](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector) , který můžete spustit v prohlížeči. V této ukázce běží v hostované webové Poznámkový blok Jupyter a ukazuje, jak poslat žádost o rozhraní API a vizualizovat výsledek.

Chcete-li spustit ukázku, proveďte následující kroky:

1. Získáte platný klíč rozhraní API detekce anomálií předplatného a koncový bod rozhraní API. Následující oddíl obsahuje pokyny pro registraci. 
2. Přihlaste se a klikněte na možnost klonovat, v pravém horním rohu.
3. Klikněte na tlačítko **spouštět bezplatnými výpočetními funkcemi**
4. Vyberte jednu z poznámkových bloků pro tuto ukázku.
5. Přidat klíč platný předplatného rozhraní API detekce anomálií na `subscription_key` proměnné. Změnit `endpoint` proměnné do vašeho koncového bodu. Příklad: `https://westus2.api.cognitive.microsoft.com`
1. V horní nabídce klikněte na **buňky**, pak **spustit všechny**.

## <a name="workflow"></a>Pracovní postup

Rozhraní API detekce anomálií je RESTful webová služba, což usnadňuje volat z libovolného programovacího jazyka, který může vytvářet požadavky HTTP a parsování formátu JSON.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Po registraci:

1. Využít data časových řad a převádět je do platný formát JSON. Použití [osvědčené postupy](concepts/anomaly-detection-best-practices.md) při přípravě vaše data, aby co nejlépe.
1. Odeslat požadavek na rozhraní API detekce anomálií s vašimi daty.
1. Zpracujte odpověď rozhraní API parsováním vrácené zprávy JSON.

## <a name="next-steps"></a>Další postup

* [Rychlé zprovoznění: Detekovat anomálie ve vašich datech časových řad pomocí rozhraní REST API detekce anomálií](quickstarts/detect-data-anomalies-csharp.md)
* Rozhraní API detekce anomálií [online ukázku](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector)
* Detekce anomálií [reference k rozhraní REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)