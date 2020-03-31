---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76963647"
---
Chcete-li maximalizovat výkon, použijte samostatný účet úložiště pro každou aplikaci funkce. To je obzvláště důležité, pokud máte trvalé funkce nebo události hub aktivované funkce, které oba generovat velký objem transakcí úložiště. Když vaše aplikační logika spolupracuje s Azure Storage, buď přímo (pomocí sady Storage SDK) nebo prostřednictvím jedné z vazeb úložiště, měli byste použít účet vyhrazeného úložiště. Například pokud máte funkci aktivovanou centrem událostí, která zapisuje&mdash;některá data do úložiště objektů blob, použijte dva účty úložiště, jeden pro aplikaci funkce a druhý pro objekty BLOB uložené funkcí.