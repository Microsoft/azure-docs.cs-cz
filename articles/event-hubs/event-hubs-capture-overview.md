---
title: Přehled služby Azure Event Hubs Capture | Dokumentace Microsoftu
description: Zachytávat data telemetrie s Event Hubs Capture
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: shvija
ms.openlocfilehash: 0269fb1f9ea6b1969a7a59b4f65c94f646274767
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "40004017"
---
# <a name="azure-event-hubs-capture"></a>Azure Event Hubs Capture

Azure Event Hubs Capture umožňuje automatické doručování streamovaných dat ve službě Event Hubs do [úložiště objektů Blob v Azure](https://azure.microsoft.com/services/storage/blobs/) nebo [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) účet podle vašeho výběru s flexibilnější určení časový nebo velikostní interval. Nastavení zachytávání je rychlý, neexistují žádné administrativní náklady na spuštění a automaticky se škáluje s Event Hubs [jednotek propustnosti](event-hubs-features.md#capacity). Funkce Event Hubs Capture představuje nejjednodušší způsob, jak načíst streamovaná data do Azure a vám umožní zaměřit se na zpracování dat a nikoli na sběr dat.

Funkce Event Hubs Capture umožňuje zpracovávat v reálném čase a dávkově kanály pro stejný datový proud. To znamená, že můžete vytvářet řešení, které podle vašich potřeb v čase. Ať už vytváříte systémy batch ještě dnes s přehled směrem k budoucímu zpracování v reálném čase, nebo chcete přidat do existujícího řešení v reálném čase efektivní studené cesty, Event Hubs Capture usnadňuje práci se streamovanými daty jednodušší.

## <a name="how-event-hubs-capture-works"></a>Princip funkce Event Hubs Capture

Event Hubs je doba uchovávání trvalý vyrovnávací paměti pro příchozí telemetrická data, podobně jako distribuované protokolu. Klíčem k škálování ve službě Event Hubs je [modelu oddělených příjemců je pravidlo](event-hubs-features.md#partitions). Každý oddíl je nezávislé segment dat a je využívat nezávisle. V čase ages tato data, na základě období možnost konfigurace uchovávání. V důsledku toho se daný eventhub nikdy získá "plný."

Funkce Event Hubs Capture umožňuje určit vlastní účet úložiště objektů Blob v Azure a kontejneru nebo účtu Azure Data Lake Store, které se používají k ukládání zachycená data. Tyto účty může být ve stejné oblasti jako vaše Centrum událostí nebo v jiné oblasti, přidání flexibility funkce Event Hubs Capture.

Zachycená data se zapisují [Apache Avro] [ Apache Avro] formátu: compact, rychlé, binární formát, který poskytuje bohaté datové struktury vložené schéma. Tento formát je široce používat v ekosystému Hadoop, Stream Analytics a Azure Data Factory. Další informace o práci s Avrem je k dispozici dále v tomto článku.

### <a name="capture-windowing"></a>Zachycení časová okna

Funkce Event Hubs Capture umožňuje nastavit časové období pro řízení zachytávání. Toto okno je minimální velikost a konfigurace času zásadám"první wins," to znamená, že první aktivační události došlo k způsobí, že operace zachycení. Pokud máte 15 minut, 100 MB okna Sběr a odeslat 1 MB za sekundu, aktivační události velikost okna před časový interval. Každý oddíl zaznamená nezávisle a zapíše objekt blob bloku dokončené v době zachycení, s názvem po dobu, kdy došlo k zachycení intervalu. Zásady vytváření názvů úložiště je následujícím způsobem:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Všimněte si, že hodnoty data jsou doplněny nulami; Příklad souboru může být:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

### <a name="scaling-to-throughput-units"></a>Škálování s jednotkami propustnosti

Event Hubs provoz se řídí [jednotek propustnosti](event-hubs-features.md#capacity). Jedna jednotka propustnosti za sekundu nebo 1000 událostí za sekundu dvakrát šířka odchozích přenosů dat a příchozího přenosu dat umožňuje 1 MB. Event Hubs úrovně standard se dá nakonfigurovat s 1 až 20 jednotek propustnosti, a můžete koupit další s kvótou zvýšit [žádost o podporu][support request]. Použití nad rámec vašeho zakoupené jednotky propustnosti se omezí. Funkce Event Hubs Capture kopíruje data přímo z interní úložiště služby Event Hubs, obejít kvóty odchozího přenosu dat jednotky propustnosti a ukládání vašich odchozího přenosu dat pro jiné čtečky zpracování, jako je například Stream Analytics nebo Spark.

Po nakonfigurování funkce Event Hubs Capture automaticky spustí, když posíláte první událost a bude nadále spuštěn. Aby bylo snazší pro příjem dat zpracování vědět, že proces funguje, Event Hubs zapíše prázdné soubory, když nejsou žádná data. Tento proces zajišťuje předvídatelný tempo a značky, který může zadat procesorů služby batch.

## <a name="setting-up-event-hubs-capture"></a>Nastavení funkce Event Hubs Capture

Funkci Capture můžete nakonfigurovat pomocí event hub pro čas vytvoření [webu Azure portal](https://portal.azure.com), nebo pomocí šablon Azure Resource Manageru. Další informace najdete v následujících článcích:

- [Povolení funkce Event Hubs Capture pomocí webu Azure portal](event-hubs-capture-enable-through-portal.md)
- [Vytvořte obor názvů služby Event Hubs s centrem událostí a povolení funkce Capture pomocí šablony Azure Resource Manageru](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)

## <a name="exploring-the-captured-files-and-working-with-avro"></a>Zkoumání zaznamenané soubory prostředků a práci s Avrem

Funkce Event Hubs Capture vytvoří soubory ve formátu Avro, jak je uvedeno na nakonfigurované časové okno. Tyto soubory můžete zobrazit všechny nástroje, jako [Průzkumníka služby Azure Storage][Azure Storage Explorer]. Můžete si stáhnout soubory místně pro práci s nimi.

Soubory vytvořené metodou Event Hubs Capture mají následující schématu Avro:

![][3]

Snadný způsob, jak prozkoumat souborů Avro je použít [nástroje Avro] [ Avro Tools] jar z Apache. Po stažení tento soubor jar, zobrazí se schéma konkrétní soubor Avro spuštěním následujícího příkazu:

```shell
java -jar avro-tools-1.8.2.jar getschema <name of capture file>
```

Tento příkaz vrátí

```json
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

Můžete také použít nástroje Avro převést soubor do formátu JSON a provádět další zpracování.

K provádění rozšířené zpracování, stáhněte a nainstalujte Avro pro platformy podle vašeho výběru. V době psaní tohoto návodu, nejsou k dispozici pro C, C++, C implementace\#, Java, NodeJS, Perl, PHP, Python nebo Ruby.

Apache Avro má kompletní příručky Začínáme pro [Java] [ Java] a [Python][Python]. Také můžete přečíst [Začínáme se službou Event Hubs Capture](event-hubs-capture-python.md) článku.

## <a name="how-event-hubs-capture-is-charged"></a>Jak funkce Event Hubs Capture se účtuje

Funkce Event Hubs Capture se měří podobně pro jednotky propustnosti: jako hodinovou sazbu. Sazba je přímo úměrný počtu jednotek propustnosti zakoupili pro obor názvů. Jednotky propustnosti jsou zvýšení a snížení, Event Hubs Capture měřiče zvýšit nebo snížit zajistit odpovídající výkonu. Při vytvoření celostní dojde k měřiče. Podrobnosti o cenách najdete v tématu [ceny služby Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/). 

## <a name="next-steps"></a>Další postup

Event Hubs Capture představuje nejjednodušší způsob, jak načíst data do Azure. Pomocí Azure Data Lake, Azure Data Factory a Azure HDInsight, můžete provádět dávkové zpracování a další analýzy pomocí známých nástrojů a platformy podle vašeho výběru, v libovolném měřítku potřebujete.

Další informace o službě Event Hubs najdete na následujících odkazech:

* [Začínáme, odesílání a příjem událostí](event-hubs-dotnet-framework-getstarted-send.md)
* [Přehled služby Event Hubs][Event Hubs overview]

[Apache Avro]: http://avro.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: http://www-us.apache.org/dist/avro/avro-1.8.2/java/avro-tools-1.8.2.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
