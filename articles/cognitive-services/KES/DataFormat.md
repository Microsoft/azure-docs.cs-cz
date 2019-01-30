---
title: Formát dat – Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: Další informace o formátu dat v znalostní báze zkoumání služby (KES) rozhraní API.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: c06a90b7c53067723280b6a1573ada9fbf65e10d
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55223234"
---
# <a name="data-format"></a>Formát dat

Popisuje datový soubor seznamu objektů pro indexování.
Každý řádek v souboru určuje hodnoty atributů objektu v [formátu JSON](http://json.org/) s kódováním UTF-8.
Kromě atributů, které jsou definovány v [schématu](SchemaFormat.md), každý objekt má atribut volitelné "logprob", který určuje relativní protokolu pravděpodobnost mezi objekty.
Při služba vrátí objekty v pořadí podle snižuje pravděpodobnost, můžeme použít "logprob" k označení vrácení pořadí sesouhlasení objektů.
Zadaný pravděpodobnost *p* mezi 0 a 1, nelze vypočítat odpovídající pravděpodobnost protokolu protokolu (*p*), kde je log() funkci přirozený protokolu.
Když pro logprob není zadána žádná hodnota, použije se výchozí hodnota 0.

```json
{"logprob":-5.3, "Title":"latent dirichlet allocation", "Year":2003, "Author":{"Name":"david m blei", "Affiliation":"uc berkeley"}, "Author":{"Name":"andrew y ng", "Affiliation":"stanford"}, "Author":{"Name":"michael i jordan", "Affiliation":"uc berkeley"}}
{"logprob":-6.1, "Title":"probabilistic latent semantic indexing", "Year":1999, "Author":{"Name":"thomas hofmann", "Affiliation":"uc berkeley"}}
...
```

Pro atributy String, GUID a objektů Blob že hodnota je vyjádřena jako řetězec v uvozovkách JSON.  Hodnoty pro číselné atributy (Int32, Int64, Double), je vyjádřené číslem v JSON.  Složený atributů hodnota je objekt JSON, který určuje hodnoty dílčí atributů.  Rychlejší sestavování indexu presort objekty snížením pravděpodobnost protokolu.

Obecně platí atribut může mít 0 nebo více hodnot.  Pokud atribut nemá žádnou hodnotu, můžeme jednoduše přetáhněte ho z ve formátu JSON.  Pokud atribut obsahuje 2 nebo více hodnot, budeme opakovat dvojici hodnot atributu v objektu JSON.  Případně My je můžeme přiřadit pole JSON obsahující více hodnot atributu.

```json
{"logprob":0, "Title":"0 keyword"}
{"logprob":0, "Title":"1 keyword", "Keyword":"foo"}
{"logprob":0, "Title":"2 keywords", "Keyword":"foo", "Keyword":"bar"}
{"logprob":0, "Title":"2 keywords (alt)", "Keyword":["foo", "bar"]}
```
