---
title: Formát dat v rozhraní API služby zkoumání znalostní báze | Microsoft Docs
description: Další informace o formátu dat v znalostní báze zkoumání služby (KES) rozhraní API v kognitivní služby.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: a763505ac6458d68df74ae73e71029b81202ec8b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342396"
---
# <a name="data-format"></a>Formát dat
Datový soubor popisuje seznamu objektů, které chcete indexu.
Každý řádek v souboru určuje hodnoty atributu objektu v [formátu JSON](http://json.org/) s kódováním UTF-8.
Kromě atributy definované v [schématu](SchemaFormat.md), každý objekt má atribut volitelné "logprob", který určuje relativní protokolu pravděpodobnost mezi objekty.
Při službu vrací objekty v pořadí podle snížení pravděpodobnosti, jsme použijte "logprob" k označení vratky odpovídajících objektů.
Zadané pravděpodobnost *p* mezi 0 a 1, můžete vypočítat odpovídající pravděpodobnost protokolu jako protokolu (*p*), kde log() je funkce přirozené protokolu.
Když pro logprob není zadaná žádná hodnota, použije se výchozí hodnota 0.

```json
{"logprob":-5.3, "Title":"latent dirichlet allocation", "Year":2003, "Author":{"Name":"david m blei", "Affiliation":"uc berkeley"}, "Author":{"Name":"andrew y ng", "Affiliation":"stanford"}, "Author":{"Name":"michael i jordan", "Affiliation":"uc berkeley"}}
{"logprob":-6.1, "Title":"probabilistic latent semantic indexing", "Year":1999, "Author":{"Name":"thomas hofmann", "Affiliation":"uc berkeley"}}
...
```

Pro řetězec, identifikátor GUID a objektů Blob atributy je hodnota reprezentována jako řetězec v uvozovkách JSON.  Pro číselné atributy (Int32, Int64, Double) je hodnota reprezentována jako číslo JSON.  Hodnota pro složené atributy, je objekt JSON, který určuje hodnoty dílčí atributu.  Pro rychlejší sestavení indexu presort objekty snížením pravděpodobnosti protokolu.

Obecně platí atribut pravděpodobně 0 nebo více hodnot.  Pokud atribut nemá žádnou hodnotu, můžeme jednoduše ho můžete vypustit z formátu JSON.  Pokud atribut má 2 nebo více hodnot, jsme opakujte dvojice hodnot atributů v objektu JSON.  Alternativně jsme můžete přiřadit pole JSON obsahující více hodnot atributu.

```json
{"logprob":0, "Title":"0 keyword"}
{"logprob":0, "Title":"1 keyword", "Keyword":"foo"}
{"logprob":0, "Title":"2 keywords", "Keyword":"foo", "Keyword":"bar"}
{"logprob":0, "Title":"2 keywords (alt)", "Keyword":["foo", "bar"]}
```
