---
title: Zadejte verzi modelu v Analýza textu V3
titleSuffix: Azure Cognitive Services
description: Naučte se, jak zadat model rozhraní API pro analýzu textu použitý pro vaše data.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: 7ab2e037cc7593431a668a216fcc152c0511c410
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965081"
---
# <a name="model-versioning-in-the-text-analytics-api"></a>Správa verzí modelů v rozhraní API pro analýzu textu

Verze 3 rozhraní API pro analýzu textu vám umožňuje vybrat verzi modelu, která se používá pro vaše data. Pomocí volitelného `model-version` parametru vyberte verzi modelu v požadavcích rozhraní API. Příklad: `<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01`. Pokud tento parametr není zadán, bude rozhraní API výchozím nastavením nejnovější stabilní verze. 

## <a name="available-versions"></a>Dostupné verze

Pomocí následující tabulky můžete zjistit, které verze modelu jsou podporovány každým hostovaným koncovým bodem.


| Koncový bod                        | Podporované verze                                     | nejnovější verze |
|---------------------------------|--------------------------------------------------------|----------------|
| `/sentiment`                    | `2019-10-01`, `2020-04-01`                             | `2020-04-01`   |
| `/languages`                    | `2019-10-01`, `2020-07-01`, `2020-09-01`               | `2020-09-01`   |
| `/entities/linking`             | `2019-10-01`, `2020-02-01`                             | `2020-02-01`   |
| `/entities/recognition/general` | `2019-10-01`, `2020-02-01`, `2020-04-01`,              | `2020-04-01`   |
| `/entities/recognition/pii`     | `2019-10-01`, `2020-02-01`, `2020-04-01`,`2020-07-01`  | `2020-07-01`   |
| `/entities/health`              | `2020-09-03`                           | `2020-09-03`   |
| `/keyphrases`                   | `2019-10-01`, `2020-07-01`                             | `2020-07-01`   |


Podrobnosti o aktualizacích pro tyto modely najdete v části [co je nového](../whats-new.md).

## <a name="text-analytics-for-health"></a>Analýza textu pro zdravotnictví

[Analýza textu pro kontejner stavů](../how-tos/text-analytics-for-health.md) používá samostatnou správu verzí modelů než výše uvedené koncové body rozhraní API.  Všimněte si, že pro každou image kontejneru je dostupná jenom jedna verze modelu.

| Koncový bod                        | Značka image kontejneru                     | Verze modelu |
|---------------------------------|-----------------------------------------|---------------|
| `/entities/health`              | `1.1.013530001-amd64-preview` nebo nejnovější          | `2020-09-03`  |
| `/entities/health`              | `1.1.013150001-amd64-preview`           | `2020-07-24`  |
| `/domains/health`               | `1.1.012640001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012420001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012070001-amd64-preview`           | `2020-04-16`  |


## <a name="next-steps"></a>Další kroky

* [Přehled analýzy textu](../overview.md)
* [Analýza mínění](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [rozpoznávání entit,](../how-tos/text-analytics-how-to-entity-linking.md)
