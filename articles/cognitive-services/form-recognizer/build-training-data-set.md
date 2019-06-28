---
title: Vytvoření trénovací datové sady pro vlastní model - Rozlišovač formuláře
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak zajistit vaše trénovací datové sady je optimalizovaná pro trénování modelu Rozlišovač formuláře.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 611d5f7983c61fab12c55a46fedf35a3c420c4c8
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454815"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Vytvoření trénovací datové sady pro vlastní model

Při použití vlastního modelu Rozlišovač formuláře zadejte cvičných dat tak můžete do formulářů specifických pro dané odvětví trénování modelu. Můžete vyškolíme model pomocí pěti formuláře vyplněné nebo prázdný formulář (obsahovat slovo "prázdný" v názvu souboru) a navíc dva formuláře vyplněné. I v případě, že máte dostatek formuláře vyplněné trénování s, přidání prázdný formulář do vaší trénovací datové sady můžete zlepšit přesnost modelu.

## <a name="training-data-tips"></a>Školení datových tipech

Je důležité používat datové sady, která je optimalizovaná pro vzdělávání. Použijte následující tipy k zajistěte, aby co nejlépe z [Train Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) operace:

* Pokud je to možné používejte textové dokumenty PDF místo dokumentů na základě bitové kopie. Prohledaná PDF jsou zpracovávány jako bitové kopie.
* Pokud je k dispozici máte použijte jeden prázdný formulář a dva formuláře vyplněné.
* Pro formuláře vyplněné použijte příklady, které mají na všech jejich pole vyplněna.
* Použití s různými hodnotami v každé pole formulářů.
* Pokud váš obrázky formuláře jsou nižší kvality, použijte větší datové sady (například obrázky 10 až 15).

## <a name="general-input-requirements"></a>Obecné požadavky na vstupu

Zajistěte, aby že vaše trénovací datové sady také řídí vstupní požadavky pro veškerý obsah formuláře Rozlišovače.
[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="next-steps"></a>Další postup

Teď, když jste zjistili, jak vytvářet trénovací datové sady, postupujte podle rychlého startu trénování modelu vlastní formulář rozpoznávání a začít používat ve formulářích.

* [Rychlé zprovoznění: Trénování modelu a extrahovat data formuláře pomocí cURL](./quickstarts/curl-train-extract.md)
* [Rychlé zprovoznění: Trénování modelu a extrahování dat formuláře pomocí rozhraní REST API s využitím Pythonu](./quickstarts/python-train-extract.md)

