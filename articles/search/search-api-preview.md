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
ms.openlocfilehash: ce5771777762414a0229cf83425c2f3601cb979a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654993"
---
# <a name="azure-search-service-rest-api-version-2017-11-11-preview"></a>Verze rozhraní api REST služby vyhledávání systému Azure 2017-11-11-Preview
Tento článek popisuje `api-version=2017-11-11-Preview` verzi služby Azure Search rozhraní REST API, nabízí povolenými experimentálními funkcemi ještě není obecně k dispozici.

> [!NOTE]
> Funkce Preview jsou k dispozici pro testování a experimenty s cílem shromažďování zpětné vazby a mohou podléhat změnám. Důrazně nedoporučujeme používání preview rozhraní API v produkční aplikace.


## <a name="new-in-2017-11-11-preview"></a>Novinka v 2017-11-11-Preview

[Kognitivní vyhledávání](cognitive-search-concept-intro.md), novou funkci obohacení ve službě Azure Search vyhledá latentní informace v jiné textové zdroje a poskytujících blíže neurčené text, převod do textu v plném znění s možností vyhledávání obsahu ve službě Azure Search.

Následující reources jsou zavedena nebo upravit v rozhraní REST API ve verzi preview. Všechny ostatní rozhraní API REST jsou stejné, ať už volání obecně dostupná nebo náhled verze.

+ [Vytvoření Skillset (api-version = 2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)

+ [Vytvoření Indexer (api-version = 2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)

Všechny ostatní rozhraní API REST jsou stejné bez ohledu na to, jak nastavit verze rozhraní api. Například `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11-Preview` a `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11` (bez `Preview`) jsou funkčně rovnocenné.

## <a name="other-preview-features"></a>Další funkce preview

Funkce, které oznamují v dřívějších verzích Preview jsou stále ve verzi public preview. Pokud jste volání rozhraní API z předchozích verzí preview rozhraní api-, můžete dál používat tuto verzi nebo přepněte do `2017-11-11-Preview` bez změny chování očekávané.

+ [CSV soubory v Azure Blob indexování](search-howto-index-csv-blobs.md), přináší `api-version=2015-02-28-Preview`, zůstane funkce preview. Tato funkce je součástí indexování objektů Blob v Azure a je vyvolána prostřednictvím nastavení parametru. Každý řádek v souboru CSV je indexovaný jako samostatný dokument.

+ [Pole JSON v Azure Blob indexování](search-howto-index-json-blobs.md), přináší `api-version=2015-02-28-Preview`, zůstane funkce preview. Tato funkce je součástí indexování objektů Blob v Azure a je vyvolána prostřednictvím nastavení parametru. Každý prvek v poli, kde je indexovaný jako samostatný dokument.

+ [parametr dotazu moreLikeThis](search-more-like-this.md) Vyhledá dokumenty, které se vztahují k určitému dokumentu. Tato funkce byla v dřívějších verzích Preview. 


## <a name="how-to-call-a-preview-api"></a>Jak volat preview rozhraní API

Starší verze Preview jsou stále v provozu, ale budou zastaralé v čase. Pokud váš kód volá `api-version=2016-09-01-Preview` nebo `api-version=2015-02-28-Preview`, těchto volání musí být stále platné. Ale pouze nejnovější verzi preview se aktualizují s vylepšeními. 

Následující příklad syntaxe znázorňuje volání ve verzi preview rozhraní API.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2017-11-11-Preview

Služba Azure Search je k dispozici v několika verzích. Další informace najdete v tématu [verze rozhraní API](search-api-versions.md).
