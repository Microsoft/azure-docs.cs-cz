---
title: Více hlavních databází Azure Cosmos DB pomocí opensourcových databáze NoSQL
description: ''
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ea3a16cf6b5aa4e46ad401e59aacb4d936a7429a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963896"
---
# <a name="using-azure-cosmos-db-multi-master-with-open-source-nosql-databases"></a>Více hlavních databází Azure Cosmos DB pomocí opensourcových databáze NoSQL

Podpora několika hlavními databázemi Azure Cosmos DB je na straně serveru, nativní implementace, která je k dispozici na všech opensourcových nabídek NoSQL Cosmos DB podporuje. Je také přístupné pro všechny podporované služby Cosmos DB v sadě SDK.

Azure Cosmos DB je první služba nabízí multimasterovou podporu pro tyto opensourcové NoSQL databáze po celém světě.

|Model  |Podpora  |
|---------|---------|
|MongoDB  | Aktivní–aktivní  |
|Graph  | Aktivní–aktivní |
|Cassandra  | Aktivní–aktivní   |

## <a name="use-mongodb-clients-with-multi-master"></a>Použití MongoDB klientů s několika hlavními uzly

Multimasterovou funkce je povolená pro účty rozhraní MongoDB API stejným způsobem, který je povolený pro jiná rozhraní API služby Azure Cosmos DB. Po povolení více hlavních databází pro účty rozhraní API MongoDB, každá instance může klientská aplikace, můžete vybrat jeho preferované oblasti pro čtení a zápis. Z pohledu ovladač MongoDB preferované oblasti se zobrazí klientovi jako primární replik. Tímto způsobem může každou oblast distribuovanou databázi fungovat jako nastavit primární repliky. Azure více hlavních databází Cosmos DB můžete výrazně snížit latenci zápisu pro globálně distribuované aplikace MongoDB. 

### <a name="set-the-primary-region"></a>Nastavit primární oblast

Každá instance klienta MongoDB můžete vybrat primární oblasti přidáním `@<preferred_primary_region_name>` do pole "název aplikace" přijal ovladače klienta MongoDB. Většina ovladačů přijměte to v připojovacím řetězci, jako například:

`mongodb://fabrikam:KEY@fabrikam.documents.azure.com:10255/?ssl=true&replicaSet=globaldb&appname=@West US`

Po připojení, může dojít jednu z následujících případech:

* Pokud je název preferované oblasti k dispozici jako oblast pro zápis, pak služby Azure Cosmos DB vybere jej jako primární oblasti.

* Pokud není zadaný název upřednostňovaná oblast, Azure Cosmos DB vybere výchozí oblast jako primární oblasti.

* Pokud je zadaný název preferované oblasti, ale není k dispozici jako oblast pro zápis pro účet databáze, služby Azure Cosmos DB Vybere nejbližší oblasti zápisu, který je k dispozici jako primární oblasti.

Některé ovladače, jako je ovladač NodeJS bude vždy prvním problémem zapisuje do hostitele zadaný počáteční připojovací řetězec. Pro tyto ovladače k zajištění, že zápisy jsou směrované na preferované oblasti, kromě název aplikace, změňte název DNS v rámci připojovacího řetězce pro vložení názvu oblasti. Ujistěte se, že zadáte název oblasti bez mezer. Příklad:

`mongodb://fabrikam:KEY@fabrikam-westus.documents.azure.com:10255/?ssl=true&replicaSet=globaldb&appname=@West US`

### <a name="conflict-resolution-mode"></a>Režimu řešení konfliktů.

Režim řešení konfliktu pro účty rozhraní API MongoDB služby Azure Cosmos DB je vždycky poslední--zápis, pomocí místní server časové razítko, které přijali zápisu.

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli o několika hlavními databázemi podporu pro účty rozhraní API MongoDB služby Azure Cosmos DB. Podívejte se v dalším kroku v následujících článcích:

* [Povolení více hlavních databází pro účty služby Azure Cosmos DB](enable-multi-master.md)

* [Principy řešení konfliktů ve službě Azure Cosmos DB](multi-master-conflict-resolution.md)
