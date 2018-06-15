---
title: Přehled služby Azure Event Hubs zaznamenat | Microsoft Docs
description: Zaznamenat telemetrická data s zaznamenat centra událostí
services: event-hubs
documentationcenter: ''
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: sethm
ms.openlocfilehash: 00eee302cc15d94ec62f5f3332e18ee2df24f5cd
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2018
ms.locfileid: "32311429"
---
# <a name="azure-event-hubs-capture"></a>Zachycení Azure Event Hubs

Zaznamenat Azure Event Hubs umožňuje automaticky doručovat dat v centra událostí pro [úložiště objektů Azure Blob](https://azure.microsoft.com/services/storage/blobs/) nebo [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) účet podle svého výběru s flexibilnější zadání intervalu jednorázově nebo velikost. Nastavení zachycení je rychlé, neexistují žádné náklady na správu spouštět a automaticky přizpůsobí službou Event Hubs [jednotky propustnosti](event-hubs-features.md#capacity). Zaznamenat centra událostí je nejjednodušší způsob, jak načtení dat do Azure a umožní vám zaměřit se na zpracování dat, ne na sběr dat.

Zaznamenat centra událostí můžete ke zpracování v reálném čase a na základě batch kanály pro stejný datový proud. To znamená, že můžete vytvářet řešení, která růst s časem vašim potřebám. Ať už vytváříte batch systémy dnes s přehled směrem pozdější zpracování v reálném čase, nebo chcete přidat do existujícího řešení v reálném čase efektivní neaktivní trase, zaznamenat centra událostí je práce s streamování dat jednodušší.

## <a name="how-event-hubs-capture-works"></a>Jak funguje zaznamenat centra událostí

Event Hubs je doba uchování trvanlivý vyrovnávací paměti pro příchozí telemetrická data, podobně jako distribuované protokolu. Klíčem k příjmu ve službě Event Hubs je [modelu oddělených příjemců](event-hubs-features.md#partitions). Každý oddíl je segment nezávislé dat a obsazením nezávisle. V čase tato data ages vypnutý, založené na dobu uchování konfigurovat. V důsledku toho se dané události rozbočovače nikdy získá "příliš úplná."

Zaznamenat centra událostí můžete určit vlastní účtu Azure Blob storage a kontejner nebo účtu Azure Data Lake Store, které se používají k ukládání zaznamenaná data. Tyto účty může být ve stejné oblasti jako vaše Centrum událostí nebo v jiné oblasti, přidání do flexibilitu funkci zachycení centra událostí.

Zaznamenaná data je napsána v [Apache Avro] [ Apache Avro] formátu: compact, rychlá a binární formát, který poskytuje bohaté datové struktury vloženého schématu. Tento formát se často používá v ekosystému Hadoop, Stream Analytics a Azure Data Factory. Další informace o práci s Avro najdete dále v tomto článku.

### <a name="capture-windowing"></a>Zaznamenat okna

Zaznamenat centra událostí můžete nastavit okno k řízení zaznamenávání. Toto okno je minimální velikost a konfigurace času zásadám"první wins," znamená, že první aktivační události došlo způsobí, že operace zachycení. Pokud máte 15 minut, 100 MB okna Sběr a odeslat 1 MB za sekundu, aktivačních událostí velikost okna před časový interval. Každý oddíl zaznamená nezávisle a zapíše objekt blob bloku dokončené v době zachycení, s názvem dobu, kdy došlo k zachycení intervalu. Zásady vytváření názvů úložiště je následující:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Všimněte si, že se hodnoty data vyplní nulami; Název souboru příkladu může být:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

### <a name="scaling-to-throughput-units"></a>Škálování jednotky propustnosti

Provoz centra událostí řídí [jednotky propustnosti](event-hubs-features.md#capacity). Jedna jednotka propustnosti umožňuje 1 MB za sekundu nebo 1000 událostí za sekundu příjem příchozích dat a dvakrát toto množství odchozí. Standardní služby Event Hubs se dá nakonfigurovat s 1-20 jednotky propustnosti a další můžete zakoupit kvótu zvýšit [žádost o podporu][support request]. Použití mimo vaší zakoupené jednotky propustnosti je omezen. Zaznamenat centra událostí zkopíruje data přímo z interní úložiště služby Event Hubs, obcházení kvóty odchozí jednotky propustnosti a uložit vaše odchozí pro další zpracování čtečky, jako je například Stream Analytics nebo Spark.

Po nakonfigurování zaznamenat centra událostí spustí automaticky při odeslání první událost a běžet dál. Aby bylo snazší pro příjem dat zpracování vědět, že proces funguje, zapíše Event Hubs prázdné soubory, pokud nejsou žádná data. Tento proces zajišťuje předvídatelný cadence a značky, který může zadat vaše batch procesory.

## <a name="setting-up-event-hubs-capture"></a>Nastavení zachycení centra událostí

Zachycení můžete nakonfigurovat pomocí události rozbočovače pro čas vytvoření [portál Azure](https://portal.azure.com), nebo pomocí šablony Azure Resource Manager. Další informace najdete v následujících článcích:

- [Povolit pomocí portálu Azure Capture centra událostí](event-hubs-capture-enable-through-portal.md)
- [Vytvoření oboru názvů Event Hubs s centra událostí a povolte zachycení pomocí šablony Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)

## <a name="exploring-the-captured-files-and-working-with-avro"></a>Zkoumání zaznamenané soubory a práci s Avro

Zaznamenat centra událostí vytvoří soubory ve formátu Avro, jako je zadaný na nakonfigurované časové okno. Tyto soubory můžete zobrazit v libovolného nástroje, jako [Azure Storage Explorer][Azure Storage Explorer]. Můžete stáhnout soubory místně a pracovat s nimi.

Soubory vytvořené pomocí Capture centra událostí mají následující schématu Avro:

![][3]

Je snadný způsob, jak prozkoumat Avro soubory pomocí [nástroje Avro] [ Avro Tools] jar z Apache. Po stažení této jar, uvidíte schéma konkrétní soubor Avro tak, že spustíte následující příkaz:

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

Můžete taky nástroje Avro převeďte soubor do formátu JSON a provádět další zpracování.

Pokud chcete provést pokročilejší zpracování, stáhněte a nainstalujte Avro pro vaši volbu platformy. V době psaní tohoto textu, nejsou k dispozici pro C, C++, C implementace\#, Java, NodeJS, Perl, PHP, Python nebo Ruby.

Dokončení Průvodce Začínáme pro má Apache Avro [Java] [ Java] a [Python][Python]. Také můžete přečíst [Začínáme se službou Event Hubs zaznamenat](event-hubs-capture-python.md) článku.

## <a name="how-event-hubs-capture-is-charged"></a>Jak je účtován zaznamenat centra událostí

Zaznamenat centra událostí je podobně měřeného na jednotky propustnosti: jako poplatek po hodinách. Zřizování je přímo úměrná počtu jednotek propustnosti zakoupili pro obor názvů. Jednotky propustnosti jsou vyšší a zmenšit, zachycení událostí centra měřidla zvýšit nebo snížit zajistit odpovídající výkon. Měřidla nastat současně. Podrobnosti o cenách najdete v části [cenách služby Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/). 

## <a name="next-steps"></a>Další postup

Zaznamenat centra událostí je nejjednodušší způsob, jak načíst data do Azure. Pomocí Azure Data Lake, Azure Data Factory a Azure HDInsight, můžete provést dávkové zpracování a dalších analytics pomocí známých nástrojů a platformy dle vlastního výběru, v jakémkoli měřítku potřebujete.

Další informace o službě Event Hubs najdete na následujících odkazech:

* [Začínáme s odesílání a příjem událostí](event-hubs-dotnet-framework-getstarted-send.md)
* [Přehled služby Event Hubs][Event Hubs overview]

[Apache Avro]: http://avro.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: http://www-us.apache.org/dist/avro/avro-1.8.2/java/avro-tools-1.8.2.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
