---
author: craigshoemaker
ms.service: static-web-apps
ms.topic: include
ms.date: 03/25/2021
ms.author: cshoe
ms.openlocfilehash: 806a30214e0307b8fa101c0de51ed2f16622d433
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105543549"
---
| Vlastnost | Popis | Příklad | Vyžadováno |
|---|---|---|---|
| `app_location` | Umístění kódu aplikace | Zadejte `/` , zda je zdrojový kód vaší aplikace v kořenu úložiště, nebo `/app` Pokud je kód aplikace v adresáři s názvem `app` . | Yes |
| `api_location` | Umístění kódu Azure Functions. | `/api`Pokud je kód vaší aplikace ve složce s názvem `api` , zadejte. Pokud ve složce není zjištěna žádná Azure Functions aplikace, sestavení neselže, pracovní postup předpokládá, že nechcete rozhraní API. | No |
| `output_location` | Umístění výstupního adresáře sestavení vzhledem k `app_location` . | Pokud je zdrojový kód aplikace umístěn na `/app` , a skript sestavení výstupuje soubory do složky a `/app/build` nastaví `build` jako `output_location` hodnotu. | Ne |