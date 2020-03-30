---
title: Zachytávání událostí streamování – Azure Event Hubs | Dokumenty společnosti Microsoft
description: Tento článek obsahuje přehled funkce zachycení, která umožňuje zachytit události streamování prostřednictvím centra událostí Azure.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2020
ms.author: shvija
ms.openlocfilehash: c166f4cace6a8cc25b36a84f4614033801e69a51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265009"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Zachyťte události prostřednictvím Azure Event Hubs v Azure Blob Storage nebo Azure Data Lake Storage
Azure Event Hubs umožňuje automaticky zachytit streamovaná data v rozbočovačích událostí v [úložišti Azure Blob](https://azure.microsoft.com/services/storage/blobs/) nebo [azure data lake storage gen 1 nebo gen 2](https://azure.microsoft.com/services/data-lake-store/) podle vašeho výběru, s přidanou flexibilitou určení časového intervalu nebo intervalu velikosti. Nastavení sběru je rychlé, neexistují žádné administrativní náklady na jeho spuštění a automaticky se škáluje s [jednotkami propustnosti](event-hubs-scalability.md#throughput-units)centra událostí . Zachycení centra událostí je nejjednodušší způsob, jak načíst streamovaná data do Azure a umožňuje zaměřit se na zpracování dat, nikoli na sběr dat.

Zachycení centra událostí umožňuje zpracovávat kanály založené na reálném čase a na základě dávek ve stejném datovém proudu. To znamená, že můžete vytvářet řešení, která rostou s vašimi potřebami v průběhu času. Ať už dnes vytváříte dávkové systémy s ohledem na budoucí zpracování v reálném čase, nebo chcete přidat efektivní studenou cestu ke stávajícímu řešení v reálném čase, event huby Capture usnadňují práci s streamováním dat.


## <a name="how-event-hubs-capture-works"></a>Jak funguje zachycení centra událostí

Event Hubs je trvalá vyrovnávací paměť pro přenos dat telemetrie, podobně jako distribuovaný protokol. Klíčem k škálování v rozbočovačích událostí je [model rozdělovaného spotřebitele](event-hubs-scalability.md#partitions). Každý oddíl je nezávislý segment dat a je spotřebována nezávisle. V průběhu času tato data stárne, na základě konfigurovatelné doby uchovávání. V důsledku toho dané centrum událostí nikdy dostane "příliš plný."

Funkce Event Hubs Capture umožňuje zadat vlastní účet úložiště objektů blob Azure nebo kontejner nebo účet Azure Data Lake Storage, které se používají k ukládání zachycených dat. Tyto účty mohou být ve stejné oblasti jako centrum událostí nebo v jiné oblasti, což zvyšuje flexibilitu funkce zachycení centra událostí.

Zachycená data jsou napsána ve formátu [Apache Avro:][Apache Avro] kompaktní, rychlý, binární formát, který poskytuje bohaté datové struktury s inline schématem. Tento formát se široce používá v ekosystému Hadoop, Stream Analytics a Azure Data Factory. Další informace o práci s Avro jsou k dispozici dále v tomto článku.

### <a name="capture-windowing"></a>Zachytávání oken

Zachycení centra událostí umožňuje nastavit okno pro řízení zachytávání. Toto okno je konfigurace minimální velikosti a času s "první wins zásady", což znamená, že první aktivační událost došlo způsobí operaci sběru. Pokud máte patnáctiminutové okno pro digitalizaci 100 MB a odesíláte 1 MB za sekundu, okno velikosti se aktivuje před časovým oknem. Každý oddíl zachycuje nezávisle a zapíše objekt blob dokončeného bloku v době zachycení, pojmenovaný pro čas, kdy byl zjištěn interval sběru. Konvence pojmenování úložiště je následující:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Všimněte si, že hodnoty kalendářních dat jsou doplněny nulami; ukázkovým názvem souboru může být:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

V případě, že je objekt blob úložiště Azure dočasně nedostupný, služba Capture centra událostí uchová vaše data po dobu uchovávání dat nakonfigurované ve vašem centru událostí a data znovu vyplní, jakmile bude váš účet úložiště znovu k dispozici.

### <a name="scaling-to-throughput-units"></a>Změna velikosti na jednotky propustností

Provoz v Centru událostí je řízen [jednotkami propustností](event-hubs-scalability.md#throughput-units). Jedna jednotka propustnost umožňuje 1 MB za sekundu nebo 1000 událostí za sekundu příchozího přenosu dat a dvojnásobek této částky odchozího přenosu dat. Standardní centra událostí lze konfigurovat s jednotkami 1-20 propustností a můžete zakoupit další s [žádostí o podporu][support request]zvýšení kvóty . Využití nad rámec zakoupených jednotek propustnostje je omezeno. Event Hubs Capture kopíruje data přímo z interního úložiště Event Hubs, obcházejí kvóty odchozího přenosu jednotky propustnosta a ukládají odchozí přenos ový přenos pro jiné čtecí zařízení pro zpracování, jako je Stream Analytics nebo Spark.

Po nakonfigurování se funkce Capture centra událostí spustí automaticky při odeslání první události a pokračuje v běhu. Chcete-li usnadnit pro následné zpracování vědět, že proces funguje, Event Hubs zapíše prázdné soubory, když nejsou k dispozici žádná data. Tento proces poskytuje předvídatelnou kadenci a značku, která může krmit vaše dávkové procesory.

## <a name="setting-up-event-hubs-capture"></a>Nastavení zachycení centra událostí

Digitalizaci můžete nakonfigurovat v době vytvoření centra událostí pomocí [portálu Azure nebo](https://portal.azure.com)pomocí šablon Azure Resource Manager. Další informace najdete v těchto článcích:

- [Povolení funkce Event Hubs Capture prostřednictvím webu Azure Portal](event-hubs-capture-enable-through-portal.md)
- [Vytvoření oboru názvů Event Hubs s centrem událostí a povolení funkce Capture pomocí šablony Azure Resource Manageru](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


## <a name="exploring-the-captured-files-and-working-with-avro"></a>Zkoumání zachycených souborů a práce s Avro

Funkce Capture centra událostí vytváří soubory ve formátu Avro, jak je uvedeno v nakonfigurovaném časovém okně. Tyto soubory můžete zobrazit v libovolném nástroji, jako je [Například Průzkumník a údržby zařízení Azure][Azure Storage Explorer]. Soubory si můžete stáhnout místně a pracovat na nich.

Soubory vytvořené event huby Capture mají následující schéma Avro:

![Schéma Avro][3]

Snadný způsob, jak prozkoumat soubory Avro, je použití nádoby [Avro Tools][Avro Tools] od Apache. Můžete také použít [Apache Drill][Apache Drill] pro zjednodušené prostředí řízené SQL nebo [Apache Spark][Apache Spark] k provádění komplexního distribuovaného zpracování na ingemovaných datech. 

### <a name="use-apache-drill"></a>Použití Apache Drill

[Apache Drill][Apache Drill] je "open-source SQL dotazovací engine pro zkoumání velkých objemů dat", který může dotazovat strukturovaná a částečně strukturovaná data, ať je kdekoli. Modul může běžet jako samostatný uzel nebo jako obrovský cluster pro skvělý výkon.

K dispozici je nativní podpora úložiště objektů blob Azure, která usnadňuje dotazování dat v souboru Avro, jak je popsáno v dokumentaci:

[Apache Drill: Modul plug-in úložiště objektů blob Azure][Apache Drill: Azure Blob Storage Plugin]

Chcete-li snadno dotazovat zachycené soubory, můžete vytvořit a spustit virtuální počítač s Apache Drill povoleno prostřednictvím kontejneru pro přístup k úložišti Objektů blob Azure:

https://github.com/yorek/apache-drill-azure-blob

Kompletní ukázka od konce je k dispozici v úložišti Streamování ve velkém měřítku:

[Streamování ve velkém měřítku: Zachycení centra událostí]

### <a name="use-apache-spark"></a>Použití Apache Spark

[Apache Spark][Apache Spark] je "jednotný analytický modul pro rozsáhlé zpracování dat". Podporuje různé jazyky, včetně SQL, a může snadno přistupovat k úložišti objektů Blob Azure. Existuje několik možností, jak spustit Apache Spark v Azure a každá poskytuje snadný přístup k úložišti objektů Blob Azure:

- [HDInsight: Soubory adres v úložišti Azure][HDInsight: Address files in Azure storage]
- [Datové cihly Azure: Úložiště objektů blob Azure][Azure Databricks: Azure Blob Storage]
- [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/spark-job) 

### <a name="use-avro-tools"></a>Použití nástrojů Avro

[Avro Tools][Avro Tools] jsou k dispozici jako balíček jar. Po stažení souboru jar můžete zobrazit schéma konkrétního souboru Avro spuštěním následujícího příkazu:

```shell
java -jar avro-tools-1.9.1.jar getschema <name of capture file>
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

Můžete také použít Avro Tools převést soubor do formátu JSON a provádět další zpracování.

Chcete-li provádět pokročilejší zpracování, stáhněte a nainstalujte Avro pro vaši volbu platformy. V době psaní tohoto článku jsou k dispozici implementace pro\#C, C++, C , Java, NodeJS, Perl, PHP, Python a Ruby.

Apache Avro má kompletní Návody začínáme pro [Javu][Java] a [Python][Python]. Můžete si také přečíst článek [Začínáme s akcemi Zachycení.](event-hubs-capture-python.md)

## <a name="how-event-hubs-capture-is-charged"></a>Jak se účtuje zachytávání centra událostí

Zachycení centra událostí se měří podobně jako jednotky propustností: jako hodinové nabíjení. Poplatek je přímo úměrný počtu jednotek propustností zakoupených pro obor názvů. Vzhledem k tomu, že jednotky propustnost se zvyšují a snižují, měřiče zachycení centra událostí se zvyšují a snižují, aby poskytovaly odpovídající výkon. Měřiče se vyskytují v tandemu. Podrobnosti o cenách najdete v [tématu Ceny Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/). 

Všimněte si, že zachycení nespotřebovává odchozí kvótu, protože se účtuje samostatně. 

## <a name="integration-with-event-grid"></a>Integrace s mřížkou událostí 

Můžete vytvořit předplatné Azure Event Grid s oborem názvů Event Hubs jako jeho zdrojem. Následující kurz ukazuje, jak vytvořit odběr Event Grid s centrem událostí jako zdroj a aplikace Azure Functions jako jímka: [Zpracovat a migrovat zachycená data centra událostí do datového skladu SQL pomocí Event Grid a Azure Functions](store-captured-data-data-warehouse.md).

## <a name="next-steps"></a>Další kroky
Zachycení centra událostí je nejjednodušší způsob, jak získat data do Azure. Pomocí Azure Data Lake, Azure Data Factory a Azure HDInsight můžete provádět dávkové zpracování a další analýzy pomocí známých nástrojů a platforem podle vašeho výběru v libovolném měřítku, které potřebujete.

Zjistěte, jak tuto funkci povolit pomocí portálu Azure a šablony Azure Resource Manager:

- [Použití webu Azure Portal k povolení funkce Event Hubs Capture](event-hubs-capture-enable-through-portal.md)
- [Povolení funkce Zachycení centra událostí pomocí šablony Azure Resource Manageru](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


[Apache Avro]: https://avro.apache.org/
[Apache Drill]: https://drill.apache.org/
[Apache Spark]: https://spark.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: https://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: https://www.apache.org/dist/avro/stable/java/avro-tools-1.9.2.jar
[Java]: https://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: https://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight: Address files in Azure storage]:https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-blob-storage
[Azure Databricks: Azure Blob Storage]:https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[Apache Drill: Azure Blob Storage Plugin]:https://drill.apache.org/docs/azure-blob-storage-plugin/
[Streamování ve velkém měřítku: Zachycení centra událostí]:https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture
