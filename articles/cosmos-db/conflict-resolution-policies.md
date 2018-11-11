---
title: Řešení konfliktu a zásady řešení ve službě Azure Cosmos DB
description: Tento článek popisuje kategorie konflikt a zásady řešení konfliktu ve službě Azure Cosmos DB.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 1b2a122cc8a04d4f0044ecb0fe0341357bc29c0f
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2018
ms.locfileid: "51514821"
---
# <a name="conflict-types-and-resolution-policies"></a>Zásady rozlišení a typy konfliktů

Konflikty a řešení konfliktů, že zásady se dají použít, pokud je nakonfigurovaný účet Cosmos s několika zápisu oblastech.

Účty Cosmos nakonfigurovanou s využitím více oblastí zápisu může dojít ke konfliktům aktualizace více zapisujících současně aktualizace jedné položce v několika oblastech. Aktualizace je v konfliktu se zařazují do následující tři typy:

1. **Vložit konflikty:** tyto konflikty mohou nastat při aplikaci současně vloží dva nebo více položek se stejný jedinečný index (například vlastnost ID) ze dvou nebo více oblastech. V takovém případě všechny operace zápisu může být zpočátku úspěšné, v jejich příslušných místních oblastech, ale podle zásada řešení konfliktů, kterou zvolíte, je jenom jedna položka s ID původního nakonec potvrdit.

1. **Nahraďte konflikty:** tyto konflikty může dojít, pokud aplikace aktualizuje jednu položku současně ze dvou nebo více oblastech.

1. **Odstranit konflikty:** tyto konflikty mohou nastat při aplikaci současně odstraní položku z jedné oblasti a aktualizuje ji z jiné oblasti.

## <a name="conflict-resolution-policies"></a>Zásady řešení konfliktů

Cosmos DB nabízí flexibilní založená na zásadách mechanismus k vyřešení konfliktů při aktualizacích. Můžete vybrat z následujících zásad řešení dvě konflikt v kontejneru Cosmos:

- **Služby Wins posledního zápisu (LWW):** řešení ve výchozím nastavení, pomocí vlastnosti definované v systému časové razítko (založené na protokolu synchronizaci času hodin). Můžete také při použití rozhraní SQL API, Cosmos DB můžete zadat jakoukoli jinou vlastní číselné vlastnosti (také označované jako "cesta k řešení konfliktů") se použije k odstranění konfliktů.  

  Pokud dva nebo více položek konflikt na vložení nebo nahrazení operace, položky, který obsahuje nejvyšší hodnotu pro "cestu k řešení konfliktů" stane "vítěze". Stejnou číselnou hodnotu pro cestu k řešení konfliktů máte více položek vybraných "vítěze" verze je určena v systému. Všechny oblasti zaručeně sloučit do jednoho vítěze a end nahoru pomocí stejné verze položky potvrzeny. Pokud se využívá řada delete je v konfliktu, odstraněné verze vždy wins přes buď vložit nebo nahraďte je v konfliktu, bez ohledu na hodnotu cesty k řešení konfliktů.

  > [!NOTE]
  > Výchozí zásada řešení konfliktů je služby Wins poslední zápis a je dostupná pro účty SQL, Table, MongoDB, Cassandra a Gremlin API nevztahují.

  Další informace najdete v tématu [příklady použití LWW konfliktu zásad řešení](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

- **Vlastní:** této zásady překladu IP adres je navržená pro sémantiku definovaného aplikací pro odsouhlasení je v konfliktu. Při nastavení této zásady v kontejneru Cosmos, musíte také zaregistrovat sloučení uložené postup, který je automaticky vyvolána při zjištění konfliktům aktualizací v rámci transakce databáze na serveru. Poskytuje tento systém přesně jednou zaručit k provádění procedury sloučení jako součást závazku protokolu.  

  Nicméně pokud konfigurace vašeho kontejneru s možností vlastní řešení, ale buď nezdaří jejich registrace sloučení proceduru ke kontejneru nebo sloučení postupu dojde k výjimce za běhu, konflikty se zapisují do konflikty informačního kanálu. Aplikace pak muset ručně vyřešit konflikty v informačním kanálu je v konfliktu. Další informace najdete v tématu [příklady pomocí zásad vlastní řešení a použití je v konfliktu kanálu](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

  > [!NOTE]
  > Vlastní konflikt zásada řešení je dostupná jenom pro účty rozhraní SQL API.

## <a name="next-steps"></a>Další postup

Dále můžete zjistíte, jak nakonfigurovat zásady řešení konfliktu s použitím následujících článcích:

* [Jak používat LWW zásada řešení konfliktů](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Jak používat vlastní konflikt zásada řešení](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Jak používat informačního kanálu je v konfliktu](how-to-manage-conflicts.md#read-from-conflict-feed)
