---
title: Práce s kanálu knihovny procesoru ve službě Azure Cosmos DB změn
description: Knihovna processor změn databáze Azure Cosmos DB pomocí kanálu.
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: rafats
ms.openlocfilehash: 9d427a8001112e4994597b86579d85156f94a870
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51629038"
---
# <a name="using-the-azure-cosmos-db-change-feed-processor-library"></a>Použití změn databáze Azure Cosmos DB kanálu knihovny procesoru

[Kanálu změn databáze Azure Cosmos DB knihovny procesoru](sql-api-sdk-dotnet-changefeed.md) usnadňuje distribuci zpracování událostí mezi několik příjemců. Tato knihovna usnadňuje čtení změny napříč oddíly a fungujících paralelně několik vláken.

Hlavní výhodou knihovnou change feed processor je, že není nutné spravovat každý oddíl a token pro pokračování a vy nemusíte ručně dotazovat jednotlivých kontejnerů.

Knihovnou change feed processor usnadňuje čtení změn napříč oddíly a fungujících paralelně několik vláken. Automaticky spravuje čtení změn napříč oddíly, použití mechanismu zapůjčení. Jak vidíte na následujícím obrázku, pokud spustíte dvě klienty, kteří používají kanálu knihovny procesoru změn, rozdělení práce mezi servery. V průběhu zvýšit počet klientů, které se zachovat rozdělení práce mezi servery.

![Použití změn databáze Azure Cosmos DB kanálu knihovny procesoru](./media/change-feed-processor/change-feed-output.png)

Byl spuštěn první levé klienta a jeho spuštění, sledování, které všechny oddíly, pak druhého klienta byla spuštěna a potom první opustila některých zapůjčení pro druhého klienta. Toto je účinný způsob, jak rozdělit práci mezi klienty a různých počítačích.

Pokud máte dvě funkce bez serveru Azure monitoring stejného kontejneru a pomocí stejného zapůjčení, může se zobrazit různé dokumenty v závislosti na tom, jak knihovny procesoru rozhodne zpracovat oddíly dvě funkce.

## <a name="implementing-the-change-feed-processor-library"></a>Implementací, informační kanál knihovny procesoru

Existují čtyři hlavní součásti implementace kanálu knihovny procesoru změn: 

1. **Monitorované kontejneru:** monitorovaných kontejner obsahuje data, ze kterého je generován kanál změn. Všechny operace vložení a změny monitorovaných kontejneru se projeví v kanálu změn kontejneru.

1. **Zapůjčení kontejneru:** souřadnicích kontejneru zapůjčení zpracování kanálu změn napříč několika pracovních procesů. Samostatný kontejner se používá k ukládání zapůjčení s jeden zapůjčení na oddíl. Je výhodné pro uložení tohoto kontejneru zapůjčení na jiný účet s oblastí zápisu blíže na kterém je spuštěný kanálu procesoru změn. Objekt zapůjčení obsahuje následující atributy:

   * Vlastník: Určuje hostitele, který vlastní zapůjčení.

   * Pokračování: Určuje umístění (token pro pokračování) v kanálu pro konkrétní oddíl změn.

   * Časové razítko: Čas poslední byla aktualizována zapůjčení; časové razítko slouží ke kontrole, jestli zapůjčení je považována za ukončenou.

1. **Hostitel procesoru:** každého hostitele Určuje, kolik oddíly k procesu v závislosti na tom, kolik instancí hostitelů máte aktivní zapůjčení.

   * Při spuštění hostitele, získá zapůjčení vyrovnávat zatížení na všech hostitelích. Hostitel pravidelně obnoví zapůjčení, tak zůstanou aktivní zapůjčení.

   * Kontrolní body hostitele poslední token pro pokračování k zapůjčení pro každé čtení. K zajištění bezpečnosti souběžnosti, zkontroluje hostitele ETag pro každou aktualizaci zapůjčení. Podporují se také další strategie zaměřené na kontrolní bod.

   * Při vypnutí počítače Hostitel uvolní všechny zapůjčení ale uchovává informace o pokračování, abyste ho mohli obnovit čtení z uložené kontrolního bodu později.

   Počet hostitelů v současné době nemůže být větší než počet oddílů (zapůjčení).

1. **Příjemci:** spotřebitelům nebo zaměstnanců, jsou vlákna, které provádějí zpracování kanálu změn inicializuje v každém hostiteli. Každý procesor hostitel může mít několik příjemců. Každý příjemce čte změnu datového kanálu z oddílu, který je přiřazen k a upozorní jeho hostitel změny a vypršení platnosti zapůjčení.

Abyste pochopili, jak tyto čtyři prvky z kanálu změn práce procesoru společně, Pojďme se podívat na příklad na následujícím diagramu. Monitorované kolekci ukládá dokumenty a používá "Město" jako klíč oddílu. Vidíme, že modrý oddíl obsahuje dokumenty s polem "Město" z "A-E –" a tak dále. Existují dva hostitele, každou s dvěma čtením čtyři oddíly paralelní konzumenty. Šipky zobrazují příjemci čtení z určité místo v kanálu změn. Do prvního oddílu představuje tmavší modrá nepřečtené změny, zatímco světle modrá představuje změny již čtení na kanálu změn. Hostitele použít kolekci zapůjčení pro uložení hodnoty "pokračování" ke sledování na aktuální pozici čtení pro každého příjemce.

![Příklad procesoru kanálu změn](./media/change-feed-processor/changefeedprocessor.png)

### <a name="change-feed-and-provisioned-throughput"></a>Kanál změn a zřízenou propustnost

Bude vám účtována od přesunu dat do a z Cosmos kontejnery vždy využívá ru spotřebovaných rezervovaných jednotek. Bude vám účtována ru spotřebovaných kontejneru zapůjčení.

## <a name="additional-resources"></a>Další zdroje informací:

* [Azure Cosmos DB knihovnou change feed processor](sql-api-sdk-dotnet-changefeed.md)
* [Balíček Nuget](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [Další ukázky na Githubu](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

## <a name="next-steps"></a>Další postup

Teď můžete přejít k další informace o změně v následujících článcích:

* [Přehled kanálu změn](change-feed.md)
* [Způsoby, jak čtení kanálu změn](read-change-feed.md)
* [Změna kanálu s využitím Azure Functions](change-feed-functions.md)