---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: ac91ef5a56fe234cba47b430b78e74ce81c9d504
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96536952"
---
Azure Storage poskytuje následující metriky transakcí v Azure Monitor.

| Metric | Popis |
| ------------------- | ----------------- |
| Transakce | Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. <br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Použitelné dimenze: ResponseType, typ, ApiName a ověřování ([definice](#metrics-dimensions))<br/> Příklad hodnoty: 1024 |
| Příchozí přenos dat | Množství příchozích dat. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure. <br/><br/> Jednotka: bajtů <br/> Typ agregace: celkem <br/> Použitelné dimenze: typ, ApiName a ověřování ([definice](#metrics-dimensions)) <br/> Příklad hodnoty: 1024 |
| Výchozí přenos dat | Množství výchozích dat. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat. <br/><br/> Jednotka: bajtů <br/> Typ agregace: celkem <br/> Použitelné dimenze: typ, ApiName a ověřování ([definice](#metrics-dimensions)) <br/> Příklad hodnoty: 1024 |
| SuccessServerLatency | Průměrná doba zpracování úspěšného požadavku službou Azure Storage. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu. <br/><br/> Jednotka: milisekundy <br/> Typ agregace: průměr <br/> Použitelné dimenze: typ, ApiName a ověřování ([definice](#metrics-dimensions)) <br/> Příklad hodnoty: 1024 |
| SuccessE2ELatency | Průměrná celková latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi. Rozdíl mezi hodnotami SuccessE2ELatency a SuccessServerLatency je latence pravděpodobná v důsledku sítě a klienta.<br/><br/> Jednotka: milisekundy <br/> Typ agregace: průměr <br/> Použitelné dimenze: typ, ApiName a ověřování ([definice](#metrics-dimensions)) <br/> Příklad hodnoty: 1024 |
| Dostupnost | Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota celkových fakturovatelných požadavků vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost. <br/><br/> Jednotka: procenta <br/> Typ agregace: průměr <br/> Použitelné dimenze: typ, ApiName a ověřování ([definice](#metrics-dimensions)) <br/> Příklad hodnoty: 99,99 |
