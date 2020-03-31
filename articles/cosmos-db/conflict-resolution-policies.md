---
title: Typy řešení konfliktů a zásady řešení v Azure Cosmos DB
description: Tento článek popisuje kategorie konfliktů a zásady řešení konfliktů v Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: a8ee72f46e1789088e779c10a0824262469ffde8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441983"
---
# <a name="conflict-types-and-resolution-policies"></a>Zásady řešení a typy konfliktů

Konflikty a zásady řešení konfliktů jsou použitelné, pokud je váš účet Azure Cosmos DB nakonfigurovaný s více oblastmi zápisu.

U účtů Azure Cosmos nakonfigurovaných s více oblastmi zápisu může dojít ke konfliktům aktualizací, když autoři současně aktualizují stejnou položku ve více oblastech. Konflikty aktualizací mohou být následujících tří typů:

* **Vložit konflikty**: Tyto konflikty mohou nastat, když aplikace současně vloží dvě nebo více položek se stejným jedinečným indexem ve dvou nebo více oblastech. K tomuto konfliktu může dojít například s vlastností ID.

* **Nahradit konflikty**: Tyto konflikty mohou nastat, když aplikace aktualizuje stejnou položku současně ve dvou nebo více oblastech.

* **Odstranit konflikty**: Tyto konflikty mohou nastat, když aplikace současně odstraní položku v jedné oblasti a aktualizuje ji v jiné oblasti.

## <a name="conflict-resolution-policies"></a>Zásady řešení konfliktů

Azure Cosmos DB nabízí flexibilní mechanismus řízený zásadami k řešení konfliktů zápisů. Můžete vybrat ze dvou zásad řešení konfliktů v kontejneru Azure Cosmos:

* **Poslední počet výher zápisu (LWW):** Tato zásada řešení ve výchozím nastavení používá vlastnost časového razítka definovanou systémem. Je to založeno na protokolu hodin pro synchronizaci času. Pokud používáte rozhraní SQL API, můžete zadat jakoukoli jinou vlastní číselnou vlastnost (např. vlastní pojem časového razítka), která bude použita pro řešení konfliktů. Vlastní číselná vlastnost se také označuje jako *cesta k řešení konfliktů*. 

  Pokud jsou při operacích vložení nebo nahrazení v konfliktu dvě nebo více položek, vítězem se stane položka s nejvyšší hodnotou cesty řešení konfliktů. Systém určí vítěze, pokud více položek má stejnou číselnou hodnotu pro cestu řešení konfliktů. U všech oblastí je zaručeno, že se sblíží s jedním vítězem a skončí se stejnou verzí potvrzené položky. Pokud se jedná o konflikty odstranění, odstraněná verze vždy vyhraje nad konflikty vložení nebo nahrazení. K tomuto výsledku dochází bez ohledu na to, jaká je hodnota cesty řešení konfliktu.

  > [!NOTE]
  > Poslední počet vítězství při zápisu je `_ts` výchozí zásada řešení konfliktů a používá časové razítko pro následující rozhraní API: SQL, MongoDB, Cassandra, Gremlin a Table. Vlastní numerická vlastnost je k dispozici pouze pro rozhraní SQL API.

  Další informace naleznete v [příkladech, které používají zásady řešení konfliktů LWW](how-to-manage-conflicts.md).

* **Vlastní**: Tato zásada řešení je určena pro sémantiku definovanou aplikací pro sladění konfliktů. Když nastavíte tuto zásadu v kontejneru Azure Cosmos, musíte také zaregistrovat *uloženou proceduru sloučení*. Tento postup je automaticky vyvolán, když jsou zjištěny konflikty v rámci databázové transakce na serveru. Systém poskytuje přesně jednou záruku pro provedení postupu sloučení jako součást protokolu závazku.  

  Pokud nakonfigurujete kontejner s možností vlastního rozlišení a nepodaří se zaregistrovat proceduru sloučení v kontejneru nebo procedura sloučení vyvolá výjimku za běhu, konflikty jsou *zapsány*do kanálu konfliktů . Aplikace pak musí ručně vyřešit konflikty v kanálu konfliktů. Další informace naleznete v [příkladech použití vlastních zásad řešení a použití informačního kanálu konfliktů](how-to-manage-conflicts.md).

  > [!NOTE]
  > Vlastní zásady řešení konfliktů jsou k dispozici pouze pro účty rozhraní SQL API.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak nakonfigurovat zásady řešení konfliktů:

* [Jak nakonfigurovat multimaster ve vašich aplikacích](how-to-multi-master.md)
* [Jak spravovat zásady řešení konfliktů](how-to-manage-conflicts.md)
* [Jak číst z konflikty krmiva](how-to-manage-conflicts.md#read-from-conflict-feed)
