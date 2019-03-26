---
title: Řešení konfliktu a zásady řešení s několika zápisu oblastí ve službě Azure Cosmos DB
description: Tento článek popisuje kategorie konflikt a zásady řešení konfliktu ve službě Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/24/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 24201cfd657d4f23eb962b7407ed20262d780cf7
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407415"
---
# <a name="conflict-types-and-resolution-policies"></a>Zásady řešení a typy konfliktů

Konflikty a řešení konfliktů, že zásady se dají použít, pokud váš účet Azure Cosmos DB je nakonfigurovaný s několika zápisu oblastech.

Pro účty Azure Cosmos, nakonfigurovaný s využitím více oblastí zápisu můžete aktualizace konflikt nastane, když uživatelé vytvářející obsah současně aktualizace jedné položce v několika oblastech. Ke konfliktům aktualizace může být z následujících tří typů:

* **Vložit konflikty**: Tyto konflikty mohou nastat při aplikaci současně vloží dva nebo více položek se stejný jedinečný index ve dvou nebo více oblastech. Například může vzniknout tento konflikt s vlastností ID.

* **Nahraďte je v konfliktu**: Tyto konflikty situace může nastat, pokud aplikace aktualizuje jedné položce současně ve dvou nebo více oblastech.

* **Odstranit konflikty**: Tyto konflikty mohou nastat při aplikaci současně odstraní položku v jedné oblasti a aktualizuje v jiné oblasti.

## <a name="conflict-resolution-policies"></a>Zásady řešení konfliktů

Azure Cosmos DB nabízí flexibilní založená na zásadách mechanismus k řešení konfliktů při zápisu. Můžete vybrat mezi dvěma zásadami řešení konfliktů v kontejneru Azure Cosmos:

- **Posledního zápisu Wins (LWW)**: Tato zásada řešení používá ve výchozím nastavení vlastnosti definované v systému časové razítko. Je založen na protokolu synchronizaci času hodin. Pokud používáte rozhraní SQL API, můžete zadat jakoukoli jinou vlastní číselné vlastnosti (například vlastní pojem časové razítko) se použije k odstranění konfliktů. Vlastní číselné vlastnosti se také označuje jako *cesta nalezení konflikt*. 

  Pokud dva nebo více položek konflikt na vložení nebo nahrazení operace, stane položek s nejvyšší hodnotou pro cestu k řešení konfliktů vítěze. Systém určí vítěze máte více položek na stejnou číselnou hodnotu pro cestu k řešení konfliktů. Všechny oblasti zaručeno směřovat do jednoho vítěze a end nahoru se stejnou verzí položky potvrzeny. Při odstranění je v konfliktu se podílejí, vždy odstraněné verze služby wins vložení nebo nahrazení je v konfliktu. Tento výsledek nastane bez ohledu na cestu řešení konfliktů.

  > [!NOTE]
  > Poslední zápis Wins je výchozí zásada řešení konfliktů. Je k dispozici pro následující rozhraní API: SQL, MongoDB, Cassandra, Gremlin a tabulky.

  Další informace najdete v tématu [příklady, které používají LWW konfliktu zásad řešení](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

- **Vlastní**: Tato zásada řešení je určena pro sémantiku definovaného aplikací pro odsouhlasení je v konfliktu. Když tyto zásady nastavíte na váš kontejner Azure Cosmos, musíte také zaregistrovat *sloučit uloženou proceduru*. Tento postup je automaticky vyvolána při zjištění konfliktu v rámci transakce databáze na serveru. Poskytuje tento systém přesně jednou zaručit k provádění procedury sloučení jako součást závazku protokolu.  

  Pokud konfigurace vašeho kontejneru s možností vlastního řešení a nezdaří jejich registrace sloučení proceduru ke kontejneru nebo sloučení postupu dojde k výjimce za běhu, konflikty se zapisují do *konfliktům kanál*. Aplikace pak je potřeba ručně vyřešit konflikty v konflikty informačního kanálu. Další informace najdete v tématu [příklady, jak používat zásady vlastní řešení a jak používat konfliktům kanál](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

  > [!NOTE]
  > Vlastní konflikt zásada řešení je k dispozici pouze pro účty SQL API.

## <a name="next-steps"></a>Další postup

Další informace o konfiguraci zásad řešení konfliktů:

* [Jak nakonfigurovat více hlavních databází ve svých aplikacích](how-to-multi-master.md)
* [Jak používat LWW zásada řešení konfliktů](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Jak používat vlastní konflikt zásada řešení](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Čtení z kanálu je v konfliktu](how-to-manage-conflicts.md#read-from-conflict-feed)
