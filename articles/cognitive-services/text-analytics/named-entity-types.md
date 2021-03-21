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
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: 8b596a5e54c0b59c4c0b49aa5cdc4fd6477d46dc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599297"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Podporované kategorie entit v rozhraní API pro analýzu textu V3

Pomocí tohoto článku můžete najít kategorie entit, které mohou být vráceny funkcí [rozpoznávání pojmenovaných entit](how-tos/text-analytics-how-to-entity-linking.md) (ner). NER spustí prediktivní model pro identifikaci a kategorizaci pojmenovaných entit ze vstupního dokumentu.

K dispozici je také verze Preview NER verze 3.1, která zahrnuje schopnost zjišťovat osobní údaje ( `PII` ) a `PHI` informace o stavu (). Kromě toho klikněte na kartu **stav** a zobrazte seznam podporovaných kategorií v analýza textu pro stav. 

Seznam typů vrácených verzí 2,1 najdete v [Průvodci migrací](migration-guide.md?tabs=named-entity-recognition) .

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
