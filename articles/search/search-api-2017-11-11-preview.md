---
title: Verze rozhraní API 2017 modulu Azure Search služby REST-11-11-Preview | Microsoft Docs
description: Azure vyhledávání služby REST verze rozhraní API 2017-11-11-Preview zahrnuje povolenými experimentálními funkcemi, jako je například synonyma a moreLikeThis hledání.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/17/2018
ms.author: HeidiSteen
ms.openlocfilehash: afcc8ac31c45c1a43d3d759ef6f5a1cee8166c0a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="azure-search-service-rest-api-version-2017-11-11-preview"></a>Rozhraní API REST služby vyhledávání systému Azure: Verze 2017-11-11-Preview
Tento článek popisuje `api-version=2017-11-11-Preview` verzi služby Azure Search rozhraní REST API, nabízí povolenými experimentálními funkcemi ještě není obecně k dispozici.

> [!NOTE]
> Funkce Preview jsou k dispozici pro testování a experimenty s cílem shromažďování zpětné vazby a mohou podléhat změnám. Důrazně nedoporučujeme používání preview rozhraní API v produkční aplikace.


## <a name="new-in-this-preview"></a>Nové ve verzi preview

+ [Kognitivní vyhledávání](cognitive-search-concept-intro.md), novou funkci obohacení ve službě Azure Search vyhledá latentní informace v jiné textové zdroje a poskytujících blíže neurčené text, převod do textu v plném znění s možností vyhledávání obsahu ve službě Azure Search.

Následující dvě operace jsou zavedena nebo upravit v rozhraní REST API ve verzi preview. Všechny ostatní rozhraní API REST jsou stejné, ať už volání všeobecně dostupná, nebo verze preview (například.

+ [Vytvoření Skillset (api-version = 2017-11-11-Preview)](ref-create-skillset.md)

+ [Vytvoření Indexer (api-version = 2017-11-11-Preview)](ref-create-indexer.md)

Všechny ostatní rozhraní API REST jsou stejné, ať už volání obecně dostupná nebo náhled verze. Například `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11-Preview` a `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11` (bez `Preview`) jsou funkčně rovnocenné.

## <a name="other-preview-features"></a>Další funkce preview

Funkce z dřívějších verzí Preview jsou stále ve verzi public preview.

+ [CSV soubory v Azure Blob indexování](search-howto-index-csv-blobs.md), kde je každý řádek v souboru CSV indexovaný jako samostatný dokument.

+ [Pole JSON v Azure Blob indexování](search-howto-index-json-blobs.md), kde je každý prvek v poli indexovaný jako samostatný dokument.

+ [`moreLikeThis` parametr dotazu](search-more-like-this.md) najít dokumenty, které se vztahují k určitému dokumentu. Tato funkce byla v dřívějších verzích Preview. Pokud jste volající toto rozhraní API se starší verzí rozhraní api, můžete nadále používat tuto verzi.


## <a name="how-to-call-a-preview-api"></a>Jak volat preview rozhraní API

Starší verze Preview jsou stále v provozu, ale budou zastaralé v čase. Pokud váš kód volá `api-version=2016-09-01-Preview` nebo `api-version=2015-02-25-Preview`, těchto volání musí být stále platné. Ale pouze nejnovější verzi preview se aktualizují s vylepšeními. 

Následující příklad syntaxe znázorňuje volání ve verzi preview rozhraní API.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2017-11-11-Preview

Služba Azure Search je k dispozici v několika verzích. Další informace najdete v tématu [verze rozhraní API](search-api-versions.md).
