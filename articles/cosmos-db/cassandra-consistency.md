---
title: Úrovně konzistence Apache Cassandra a Azure Cosmos DB
description: Cassandra Apache a Azure Cosmos DB úrovně konzistence.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 3107610215d5b37c43124ce4129b2eb5437e3b62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97516828"
---
# <a name="apache-cassandra-and-azure-cosmos-db-cassandra-api-consistency-levels"></a>Cassandra Apache a Azure Cosmos DB rozhraní API Cassandra úrovně konzistence
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Na rozdíl od Azure Cosmos DB Apache Cassandra nativně neposkytuje přesně definované záruky konzistence. Místo toho Apache Cassandra poskytuje úroveň konzistence pro zápis a úroveň konzistence pro čtení, aby bylo možné dosáhnout vysoké dostupnosti, konzistence a kompromisů latence. Při použití rozhraní API Cassandra Azure Cosmos DB:

* Úroveň konzistence zápisu Apache Cassandra je namapovaná na výchozí úroveň konzistence nakonfigurovanou na vašem účtu Azure Cosmos. Konzistence operace zápisu (CL) se u jednotlivých požadavků nedá změnit.

* Azure Cosmos DB bude dynamicky mapovat úroveň konzistence čtení určenou ovladačem klienta Cassandra na jednu z Azure Cosmos DB úrovní konzistence nakonfigurovaných dynamicky pro požadavek na čtení.

## <a name="multi-region-writes-vs-single-region-writes"></a>Zápisy ve více oblastech vs – zápisy v jedné oblasti

Apache Cassandra Database je ve výchozím nastavení vícenásobný hlavní systém a neposkytuje předem připravenou možnost pro zápisy v jedné oblasti s replikací ve více oblastech pro čtení. Azure Cosmos DB ale poskytuje možnost klíč možnosti mít jednu oblast nebo konfiguraci zápisu ve [více oblastech](how-to-multi-master.md) . Jednou z výhod možnosti výběru jedné oblasti pro zápis konfigurace napříč několika oblastmi je vyhnout se scénářům konfliktů mezi oblastmi a možností zachovat silnou konzistenci napříč několika oblastmi. 

Zápisy do jedné oblasti vám umožní zachovat silnou konzistenci a zároveň zachovat úroveň vysoké dostupnosti napříč oblastmi s [automatickým převzetím služeb při selhání](high-availability.md#multi-region-accounts-with-a-single-write-region-write-region-outage). V této konfiguraci můžete i nadále využívat data v závislosti na tom, abyste snížili latenci čtení tím, že se na základě jednotlivých požadavků odchází na konečnou konzistenci. Kromě těchto funkcí poskytuje Azure Cosmos DB platforma taky možnost povolit [redundanci zóny](high-availability.md#availability-zone-support) při výběru oblasti. Proto vám na rozdíl od nativní Apache Cassandra Azure Cosmos DB umožňuje procházet [špičkové spektrum](consistency-levels.md#rto) věta s větší členitosti.

## <a name="mapping-consistency-levels"></a>Mapování úrovní konzistence

Platforma Azure Cosmos DB poskytuje sadu pěti dobře definovaných nastavení konzistence pro obchodní použití s ohledem na replikaci a kompromisy definované [Cap věta](https://en.wikipedia.org/wiki/CAP_theorem) a [PACLC věta](https://en.wikipedia.org/wiki/PACELC_theorem). Vzhledem k tomu, že se tento přístup významně liší od Apache Cassandra, doporučujeme, abyste si v naší [dokumentaci](consistency-levels.md)vybrali čas na kontrolu a porozuměli Azure Cosmos DB nastavení konzistence a pomohli vám toto krátké [video](https://www.youtube.com/watch?v=t1--kZjrG-o) Průvodce pochopit nastavení konzistence v Azure Cosmos DB platformě.

Následující tabulka ilustruje možné mapování mezi Apache Cassandra a Azure Cosmos DB úrovní konzistence při použití rozhraní API Cassandra. Zobrazí se konfigurace pro jednu oblast, čtení ve více oblastech s zápisy v jedné oblasti a zápisy ve více oblastech.

> [!NOTE]
> Nejedná se o přesné mapování. Místo toho jsme poskytovali nejbližší analogické rozhraní Apache Cassandra a nedošlo ke zjištění všech kvalitativních rozdílů ve sloupci nejvíce vpravo. Jak je uvedeno výše, doporučujeme zkontrolovat [Nastavení konzistence](consistency-levels.md)Azure Cosmos DB. 

:::image type="content" source="./media/cassandra-consistency/account.png" alt-text="Mapování úrovně účtu Cassandra konzistence" lightbox="./media/cassandra-consistency/account.png" :::

:::image type="content" source="./media/cassandra-consistency/dynamic.png" alt-text="Dynamické mapování konzistence Cassandra" lightbox="./media/cassandra-consistency/dynamic.png" :::

Pokud je váš účet Azure Cosmos nakonfigurovaný s úrovní konzistence jinou než silná konzistence, můžete zjistit pravděpodobnost, že klienti mohou získat silné a konzistentní čtení pro vaše úlohy, a to tak, že se podíváte na metriku služby PBS ( *probabilistically Bounded* ). Tato metrika se zveřejňuje v Azure Portal. Další informace najdete v tématu [monitorování metriky služby PBS (probabilistically Bounded)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Pravděpodobnostní s ohraničenou neaktuálností ukazuje, jak je to vaše konečná konzistence. Tato metrika poskytuje přehled o tom, jak často můžete získat silnější konzistenci než úroveň konzistence, kterou jste v účtu Azure Cosmos aktuálně nakonfigurovali. Jinými slovy můžete zobrazit pravděpodobnost (měřeno v milisekundách) pro získání silně konzistentních čtení pro kombinaci oblastí zápisu a čtení.

## <a name="next-steps"></a>Další kroky

Další informace o globální distribuci a úrovních konzistence pro Azure Cosmos DB:

* [Přehled globální distribuce](distribute-data-globally.md)
* [Přehled úrovně konzistence](consistency-levels.md)
* [Vysoká dostupnost](high-availability.md)
