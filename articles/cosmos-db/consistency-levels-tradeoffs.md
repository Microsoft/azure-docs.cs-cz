---
title: Dostupnost a výkon kompromisy pro různé úrovně konzistence ve službě Azure Cosmos DB | Dokumentace Microsoftu
description: Dostupnost a výkon kompromisy pro různé úrovně konzistence ve službě Azure Cosmos DB.
keywords: konzistence, výkon, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: mjbrown
ms.openlocfilehash: 0e4105d6f56a8eb45a83e970c85319cf25041781
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2018
ms.locfileid: "51514770"
---
# <a name="availability-and-performance-tradeoffs-for-various-consistency-levels-in-azure-cosmos-db"></a>Dostupnost a výkon kompromisy pro různé úrovně konzistence ve službě Azure Cosmos DB

Distribuované databáze spoléhat na replikaci pro vysokou dostupnost, s nízkou latencí nebo obojí, ujistěte se, základní kompromis mezi konzistence čtení a dostupnosti, latence a propustnosti. Azure Cosmos DB blíží konzistence dat jako celé spektrum od volby místo dvěma extrémy silné a konečné konzistence. Cosmos DB umožňuje vývojářům lze vybírat (nejsilnější do nejslabší) – spektra konzistence mezi pět jasně definované modely konzistence **silné**, **omezená neaktuálnost**, **relace** , **konzistentní předpona**, a **konečné**. Každá z pět modelů konzistence poskytuje dostupnost a výkon kompromisy a je zajištěná komplexní smlouvy SLA.

## <a name="consistency-levels-and-latency"></a>Úrovně konzistence a latence

- **Latence čtení** pro všechny úrovně konzistence je vždycky zaručená být kratší než 10 milisekund na 99. percentilu a je zajištěná smlouva SLA. Průměr (na 50. percentil), přečtěte si, latence je obvykle 2 MS nebo nižší.

- S výjimkou účty Cosmos span několika oblastech a jsou nakonfigurované s silnou konzistenci **latence zápisu** zbývající konzistence úrovně je vždycky zaručená být kratší než 10 milisekund na 99th percentil. Latence zápisu je zajištěná smlouva SLA. Latence zápisu průměrné (na 50. percentil), je obvykle 5 MS nebo nižší.

- Pro účty Cosmos, které mají nakonfigurovanou silnou konzistenci (aktuálně ve verzi preview), několik oblastí **latence zápisu** je zaručeno, že bude menší než < (2 * zpátečního převodu času/požadavku) + 10 milisekund na 99. percentilu. Požadavku mezi dvěma nejvíce oblastí, přidružených k účtu Cosmos. Přesné čekací doba požadavku je funkce rychlost z světla vzdálenosti a přesně topologii sítí Azure. Sítě Azure neposkytuje žádné latence smlouvy o úrovni služeb pro požadavku mezi všechny dvou oblastech Azure. Latence replikace cosmos DB se zobrazují na webu Azure portal pro váš účet Cosmos umožňuje sledovat latenci replikace mezi různými oblastmi spojenými s vaším účtem Cosmos.

## <a name="consistency-levels-and-throughput"></a>Úrovně konzistence a propustnosti

- Pro stejný počet jednotek žádostí relace, konzistentní Předpona a úrovně konzistence typu případné zajišťuje přibližně 2 X čtení propustnosti ve srovnání s silná, ohraničená odolnost.

- Pro daný typ operace zápisu, jako je například vložení, nahradit, upsert, odstranění atd. zápis propustnost za jednotky žádosti je shodné pro všechny úrovně konzistence.

## <a name="next-steps"></a>Další postup

Dále můžete další informace o globální distribuci a obecné konzistence kompromisů v distribuovaných systémech pomocí následujících článcích:

* [Konzistence kompromisy v návrhu systémy moderní distribuované databáze](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [Vysoká dostupnost](high-availability.md)
* [Smlouva SLA cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
