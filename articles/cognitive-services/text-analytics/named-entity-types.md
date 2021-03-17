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
ms.date: 01/22/2021
ms.author: aahi
ms.openlocfilehash: 883c5a20612f4dab44c0d06776ee287b27174ab9
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097256"
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

**_

## <a name="next-steps"></a>Další kroky

_ [Jak použít rozpoznávání pojmenovaných entit v analýza textu](how-tos/text-analytics-how-to-entity-linking.md)
