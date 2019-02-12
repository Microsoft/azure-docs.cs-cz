---
title: Náhled rozhraní REST API pro Azure Search 2017-11-11-Preview – Azure Search
description: Azure Search REST pro službu rozhraní API verze 2017-11-11-Preview zahrnuje seznámit s experimentálními funkcemi, jako je například synonym a moreLikeThis hledání.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 06/28/2018
ms.author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: 524c1a6d083db02349c7dae9a0131228613dc170
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997606"
---
# <a name="azure-search-service-rest-api-version-2017-11-11-preview"></a>Služby Azure Search rozhraní REST api verze 2017-11-11-Preview
Tento článek popisuje `api-version=2017-11-11-Preview` verze služby Azure Search REST API, nabízí seznámit s experimentálními funkcemi, které ještě není obecně k dispozici.

> [!NOTE]
> Funkce ve verzi Preview jsou k dispozici pro testování a experimentování s cílem shromažďování zpětné vazby a můžou se změnit. Důrazně nedoporučujeme používání verze preview rozhraní API v aplikacích v produkčním prostředí.


## <a name="new-in-2017-11-11-preview"></a>Novinka v 2017-11-11-Preview

[**Automatické dokončování** ](search-autocomplete-tutorial.md) spojí stávající [návrhy API](https://docs.microsoft.com/rest/api/searchservice/suggestions) přidat doplňkové našeptávání narazí na panelu hledání. Automatické dokončování vrátí Release candidate termíny dotazu, které může uživatel vybrat jako řetězec dotazu pro následném vyhledávání. Návrhy vrátí skutečný dokumenty v reakci na částečné vstupy: výsledky hledání jsou okamžité a dynamicky mění s růstem vstup termín vyhledávání a specifické podobě.

[**Kognitivní vyhledávání**](cognitive-search-concept-intro.md), nová funkce rozšíření ve službě Azure Search vyhledá latentní informace v jiné textové zdrojích a nediferencovanými text transformace na prohledávatelný obsah textu v plném znění ve službě Azure Search. V následujících zdrojích informací jsou zavedené nebo upraveny ve verzi preview rozhraní REST API. Všechna další rozhraní API REST jsou stejné, ať už volání obecně dostupná, nebo verze preview.

+ [Skillset operations(api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/skillset-operations)

+ [Create Indexer (api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)

Všechny ostatní rozhraní REST API jsou stejné bez ohledu na to, jak nastavit verzi api-version. Například `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11-Preview` a `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11` (bez `Preview`) jsou funkčně ekvivalentní.

## <a name="other-preview-features"></a>Další funkce ve verzi preview

Funkce, které jsme oznámili v dřívějších verzích Preview jsou stále ve verzi public preview. Pokud voláte rozhraní API z předchozích verzí preview rozhraní api –, můžete nadále používat tuto verzi nebo přepněte na `2017-11-11-Preview` beze změn očekávané chování.

+ [Soubory CSV v indexování objektů Blob v Azure](search-howto-index-csv-blobs.md), přináší `api-version=2015-02-28-Preview`, zůstane funkce ve verzi preview. Tato funkce je součástí indexování objektů Blob v Azure a je vyvolána prostřednictvím nastavení parametru. Každý řádek v souboru CSV se indexovat jako samostatný dokument.

+ [Pole JSON v indexování objektů Blob v Azure](search-howto-index-json-blobs.md), přináší `api-version=2015-02-28-Preview`, zůstane funkce ve verzi preview. Tato funkce je součástí indexování objektů Blob v Azure a je vyvolána prostřednictvím nastavení parametru. kde je každý prvek v poli indexovat jako samostatný dokument.

+ [parametr dotazu moreLikeThis](search-more-like-this.md) Vyhledá dokumenty, které souvisí s určitým dokumentem. Tato funkce byla v dřívějších verzích Preview. 


## <a name="how-to-call-a-preview-api"></a>Jak volat rozhraní API ve verzi preview

Starší verze Preview jsou stále v provozu, ale jsou pak zastaralá v čase. Pokud váš kód volá `api-version=2016-09-01-Preview` nebo `api-version=2015-02-28-Preview`, tato volání jsou stále platné. Nicméně pouze nejnovější verzi preview se aktualizují s vylepšeními. 

Následující příklad syntaxe znázorňuje volání na verzi preview rozhraní API.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2017-11-11-Preview

Služba Azure Search je dostupná ve více verzí. Další informace najdete v tématu [verze rozhraní API](search-api-versions.md).
