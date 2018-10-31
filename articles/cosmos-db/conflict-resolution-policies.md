---
title: Řešení konfliktů ve službě Azure Cosmos DB
description: Tento článek popisuje kategorie konflikt a zásady řešení konfliktu ve službě Azure Cosmos DB.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 2f219ed6c3155e8b7d3d978116e1748f6c115fea
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50244112"
---
# <a name="conflict-types-and-resolution-policies"></a>Zásady rozlišení a typy konfliktů

Konflikty a řešení konfliktů, že zásady se dají použít, pokud je nakonfigurovaný účet Cosmos s několika zápisu oblastech.

Účty Cosmos DB s více oblastí zápisu nakonfigurovanou může dojít ke konfliktům aktualizace více zapisujících současně aktualizace jedné položce v několika oblastech. Aktualizace je v konfliktu se zařazují do následující tři typy:

1. **Vložit konflikty** může dojít, když aplikace současně vloží dva nebo více položek se stejný jedinečný index (například vlastnost ID) ze dvou nebo více oblastech. V takovém případě všechny operace zápisu může být zpočátku úspěšné, v jejich příslušných místních oblastech, ale podle zásada řešení konfliktů, kterou zvolíte, je jenom jedna položka s ID původního nakonec potvrdit.
2. **Nahraďte je v konfliktu** může dojít, pokud aplikace aktualizuje jednu položku současně ze dvou nebo více oblastech.
3. **Odstranit konflikty** může dojít, když aplikace současně odstraní položku z jedné oblasti a aktualizuje ji z jiné oblasti.

## <a name="conflict-resolution-policies"></a>Zásady řešení konfliktů

Cosmos DB nabízí flexibilní založená na zásadách mechanismus pro vyřešení konfliktů při aktualizacích. Můžete vybrat z následujících zásad řešení dvě konflikt v kontejneru Cosmos:

- **Služby Wins posledního zápisu (LWW)** , která standardně používá vlastnost definovaná systémem časové razítko (založené na protokolu synchronizaci času hodin). Můžete také pomocí rozhraní SQL API, Cosmos DB můžete zadat jakoukoli jinou vlastní číselné vlastnosti (také označované jako "cesta k řešení konfliktů") se použije k odstranění konfliktů.  

Pokud dva nebo více položek konflikt na vložení nebo nahrazení operace, položky, který obsahuje nejvyšší hodnotu pro "cestu k řešení konfliktů" stane "vítěze". Stejnou číselnou hodnotu pro cestu k řešení konfliktů máte více položek vybraných "vítěze" verze je určena v systému. Všechny oblasti zaručeně sloučit do jednoho vítěze a end nahoru pomocí stejné verze položky potvrzeny. Při odstranění zahrnutých je v konfliktu, pak odstraněné verze služby wins přes operace vložení nebo nahrazení konflikty, bez ohledu na hodnotu cesty k řešení konfliktů.

> [!NOTE]
> Je k dispozici pro SQL, Table, MongoDB, Cassandra a rozhraní Gremlin API LWW je výchozí zásada řešení konfliktů.

Další informace najdete v tématu [příklady použití LWW konfliktu zásad řešení](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

- **Vlastní** těchto zásad je navržená pro sémantiku definovaného aplikací pro odsouhlasení je v konfliktu. Při nastavení této zásady v kontejneru Cosmos, musíte také zaregistrovat sloučení uložené postup, který je automaticky vyvolána při zjištění konfliktům aktualizací v rámci databázové transakce na straně serveru. Poskytuje tento systém přesně jednou zaručit k provádění procedury sloučení jako součást závazku protokolu.  

Nicméně pokud konfigurace vašeho kontejneru s možností vlastní řešení, ale buď nezdaří jejich registrace sloučení proceduru ke kontejneru nebo sloučení postupu dojde k výjimce za běhu, konflikty se zapisují do konflikty informačního kanálu. Aplikace pak muset ručně vyřešit konflikty v informačním kanálu je v konfliktu. Další informace najdete v tématu [příklady pomocí zásad vlastní řešení a použití je v konfliktu kanálu](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

> [!NOTE]
> Vlastní konflikt zásada řešení je dostupná jenom pro účty rozhraní SQL API.

## <a name="next-steps"></a>Další postup

Dále můžete zjistíte, jak nakonfigurovat zásady řešení konfliktu s použitím následujících článcích:

* [Jak používat LWW zásada řešení konfliktů](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Jak používat vlastní konflikt zásada řešení](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Jak používat informačního kanálu je v konfliktu](how-to-manage-conflicts.md#read-from-conflict-feed)
