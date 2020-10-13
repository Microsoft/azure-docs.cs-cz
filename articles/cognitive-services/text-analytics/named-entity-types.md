---
title: Podporované kategorie pro rozpoznávání pojmenovaných entit
titleSuffix: Azure Cognitive Services
description: Seznamte se s podporovanými kategoriemi entit v rozhraní API pro analýzu textu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/28/2020
ms.author: aahi
ms.openlocfilehash: 77b75b1134bbc8366478b1f9f4d14e86e9684f70
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91709312"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Podporované kategorie entit v rozhraní API pro analýzu textu V3

Pomocí tohoto článku můžete najít kategorie entit, které mohou být vráceny funkcí [rozpoznávání pojmenovaných entit](how-tos/text-analytics-how-to-entity-linking.md) (ner). K dispozici je také verze Preview NER verze 3.1, která zahrnuje schopnost zjišťovat osobní údaje ( `PII` ) a `PHI` informace o stavu (). Kromě toho klikněte na kartu **stav** a zobrazte seznam podporovaných kategorií v analýza textu pro stav.

## <a name="entity-categories"></a>Kategorie entit

#### <a name="general"></a>[Obecné](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="pii"></a>[PII](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[Zdravotnictví](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

***

## <a name="next-steps"></a>Další kroky

* [Jak používat rozpoznávání pojmenovaných entit v Analýza textu](how-tos/text-analytics-how-to-entity-linking.md)
