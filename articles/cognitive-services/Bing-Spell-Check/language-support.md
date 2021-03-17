---
title: Jazyková podpora – rozhraní API Bingu pro kontrolu pravopisu
titleSuffix: Azure Cognitive Services
description: Seznam přirozených jazyků, které rozhraní API Bingu pro kontrolu pravopisu podporuje.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: a97bd6bfc1cb7a0760894faaf3fd0617e6165f58
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351193"
---
# <a name="language-and-region-support-for-bing-spell-check-api"></a>Podpora jazyků a oblastí pro rozhraní API Bingu pro kontrolu pravopisu

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Tyto jazyky jsou podporovány rozhraní API Bingu pro kontrolu pravopisu (pouze v `spell` režimu).

Počítejte s tím, že pokud chcete pracovat s jiným jazykem `en-US` , než, `mkt` by měl být nastaven a buď, `Accept-Language` nebo `setLang` by měl být nastaven, jak byl popsán v tématu [rozhraní API pro kontrolu pravopisu v7 reference](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)

| Jazyk    | Kód jazyka |
|:----------- |:-------------:|
| Arabština      | `ar`          |
| Čínština (Čínská lidová republika)     | `zh-CN`          |
| Čínština (Hongkong – zvláštní administrativní oblast)    | `zh-HK`          |
| Čínština (Tchaj-wan)     | `zh-TW`          |
| Dánština      | `da`          |
| Nizozemština (Belgie)       | `nl-BE`          |
| nizozemština (Nizozemsko)      | `nl-NL`          |
| Angličtina (Austrálie)    | `en-AU`          |
| Angličtina (Kanada)     | `en-CA`          |
| Angličtina (Indie)    | `en-IN`          |
| Angličtina (Indonésie)     | `en-ID`          |
| Angličtina (Malajsie)     | `en-MY`          |
| Angličtina (Nový Zéland)    | `en-NZ`          |
| Angličtina (Filipíny)     | `en-PH`          |
| Angličtina (Jihoafrická republika)    | `en-ZA`          |
| Angličtina (Spojené království)    | `en-GB`          |
| Angličtina (Spojené státy)    | `en-US`          |
| Finština     | `fi`          |
| Francouzština (Belgie)     | `fr-BE`          |
| Francouzština (Kanada)     | `fr-CA`          |
| francouzština (Francie)     | `fr-FR`          |
| Francouzština (Švýcarsko)      | `fr-CH`          |
| Němčina (Rakousko)      | `de-AT`          |
| němčina (Německo)      | `de-DE`          |
| Němčina (Švýcarsko)      | `de-CH`          |
| Italština     | `it`          |
| Japonština    | `ja`          |
| Korejština      | `ko`          |
| Norština   | `no`          |
| Polština      | `pl`          |
| Portugalština (Brazílie)   | `pt-BR`|
| portugalština (Portugalsko) | `pt-PT`|
| Ruština     | `ru`          |
| Španělština (Argentina)    | `es-AR`          |
| Španělština (Chile)     | `es-CL`          |
| Španělština (Mexiko)    | `es-MX`          |
| Španělština (Španělsko)    | `es-ES`          |
| Španělština (USA)    | `es-US`          |
| Švédština     | `sv`          |
| Turečtina     | `tr`          |

## <a name="see-also"></a>Viz také

- [Stránka dokumentace ke službám Cognitive Services](../index.yml)
- [Produktová stránka služeb Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)