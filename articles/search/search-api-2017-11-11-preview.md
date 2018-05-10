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
ms.date: 05/01/2018
ms.author: HeidiSteen
ms.openlocfilehash: 3a9ff6697357dec443691b261ae6fc0477603a9c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="azure-search-service-rest-api-version-2017-11-11-preview"></a>Rozhraní API REST služby vyhledávání systému Azure: Verze 2017-11-11-Preview
Tento článek popisuje `api-version=2017-11-11-Preview` verzi služby Azure Search rozhraní REST API, poskytuje následující povolenými experimentálními funkcemi:

+ [Kognitivní vyhledávání](cognitive-search-concept-intro.md), novou funkci obohacení v indexu Azure Search, vyhledá latentní informace v jiné textové zdroje a poskytujících blíže neurčené text, převod do textu v plném znění s možností vyhledávání obsahu ve službě Azure Search.

Ve verzi preview rozhraní API je ekvivalentní všeobecně dostupná rozhraní API s výjimkou těchto dvou akcí:

+ [Vytvoření Skillset (api-version = 2017-11-11-Preview)](ref-create-skillset.md)
+ [Vytvoření Indexer (api-version = 2017-11-11-Preview)](ref-create-indexer.md)

Ujistěte se, zda jste cílové verzi preview rozhraní API `api-version=2017-11-11-Preview` při vyhodnocování předběžné verze funkcí. Následující příklad syntaxe znázorňuje volání ve verzi preview rozhraní API.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2017-11-11-Preview

> [!NOTE]
> Funkce Preview jsou k dispozici pro testování a experimenty s cílem shromažďování zpětné vazby a mohou podléhat změnám. **Důrazně nedoporučujeme používání preview rozhraní API v produkční aplikace.**

Služba Azure Search je k dispozici v několika verzích. Naleznete [verze rozhraní API](search-api-versions.md) podrobnosti.
