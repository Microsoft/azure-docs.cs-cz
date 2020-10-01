---
title: Zachytávání událostí streamování – Azure Event Hubs | Microsoft Docs
description: Tento článek poskytuje přehled funkce Capture, která umožňuje zachytit streamování událostí prostřednictvím Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 1b79db7a7f8d0fe03b21e005ef696d5fe55ac0a1
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91613403"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Zachycení událostí prostřednictvím Azure Event Hubs v Azure Blob Storage nebo Azure Data Lake Storage
Azure Event Hubs umožňuje automaticky zachytit streamovaná data v Event Hubs v [úložišti objektů BLOB v Azure](https://azure.microsoft.com/services/storage/blobs/) nebo v účtu, který si využijete [Azure Data Lake Storage obecné 1 nebo Gen 2](https://azure.microsoft.com/services/data-lake-store/) s přidanou flexibilitou zadání času nebo velikosti intervalu. Nastavení zachytávání je rychlé, neexistují žádné náklady na správu, které by bylo možné spustit, a automaticky se škálují s Event Hubs [jednotkami propustnosti](event-hubs-scalability.md#throughput-units). Event Hubs Capture je nejjednodušší způsob, jak načíst streamovaná data do Azure, a umožňuje se zaměřit na zpracování dat, nikoli na shromažďování dat.

> [!NOTE]
> Konfigurace Event Hubsho zachycení pro použití Azure Data Lake Storage **Gen 2** je stejná jako konfigurace pro použití BLOB Storage Azure. Podrobnosti najdete v tématu [konfigurace Event Hubsho zachycení](event-hubs-capture-enable-through-portal.md). 

Event Hubs Capture umožňuje zpracovávat kanály založené na službě Batch v reálném čase a ve stejném datovém proudu. To znamená, že můžete vytvářet řešení, která se v průběhu času dorůstou podle vašich potřeb. Bez ohledu na to, jestli sestavíte do budoucna v reálném čase a chcete přidat efektivní studenou cestu k existujícímu řešení v reálném čase, Event Hubs Capture usnadňuje práci s datovými proudy.

> [!IMPORTANT]
> Cílový účet úložiště (Azure Storage nebo Azure Data Lake Storage) musí být ve stejném předplatném jako centrum událostí. 

## <a name="how-event-hubs-capture-works"></a>Jak funguje Event Hubs Capture

Event Hubs je trvalá vyrovnávací paměť pro zachycení telemetrie, podobně jako distribuovaný protokol. Klíčem k horizontálnímu navýšení kapacity v Event Hubs je [modelem rozděleného uživatele](event-hubs-scalability.md#partitions). Jednotlivé oddíly jsou nezávislé segmenty dat a spotřebovávají se nezávisle. V průběhu času tento časový limit vychází z konfigurovatelné doby uchovávání dat. V důsledku toho dané centrum událostí nikdy nezíská "příliš úplné".

Služba Event Hubs Capture umožňuje zadat vlastní účet služby Azure Blob Storage a kontejner nebo účet Azure Data Lake Storage, který se používá k uložení zachycených dat. Tyto účty můžou být ve stejné oblasti jako centrum událostí nebo v jiné oblasti, které se přidávají k flexibilitě funkce Event Hubs Capture.

Zachycená data se zapisují ve formátu [Apache Avro][Apache Avro] : kompaktní a rychlý binární formát, který poskytuje struktury s bohatou datovou strukturou s vloženým schématem. Tento formát se běžně používá v ekosystému Hadoop, Stream Analytics a Azure Data Factory. Další informace o práci s Avro je k dispozici dále v tomto článku.

### <a name="capture-windowing"></a>Okno zachycení

Event Hubs Capture vám umožní nastavit okno pro řízení zachytávání. Toto okno představuje minimální velikost a dobu konfigurace s "prvními zásadami služby WINS", což znamená, že první zjištěná aktivační událost způsobí operaci zachycení. Pokud máte okno zachycení 15 minut, 100 MB a odešlete 1 MB za sekundu, okno velikost se aktivuje před časovým intervalem. Každý oddíl zachytává nezávisle a zapisuje dokončený objekt blob bloku v době zachytávání s názvem pro čas, kdy byl zjištěn interval zachycení. Konvence pojmenování úložiště je následující:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Hodnoty data jsou doplněny nulami; Příklad názvu souboru může být:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

V případě, že je objekt BLOB služby Azure Storage dočasně nedostupný, Služba Event Hubs Capture zachová vaše data pro dobu uchování dat nakonfigurovanou v centru událostí a data se po opětovném dokončení účtu úložiště zase vyplní.

### <a name="scaling-to-throughput-units"></a>Škálování na jednotky propustnosti

Event Hubs provoz se řídí podle [jednotek propustnosti](event-hubs-scalability.md#throughput-units). Jedna jednotka propustnosti umožňuje 1 MB za sekundu nebo 1000 událostí za sekundu a dvojnásobek jejich odchozího přenosu. Standardní Event Hubs můžete nakonfigurovat s 1-20 jednotkami propustnosti a můžete si koupit další s [žádostí o podporu][support request]zvýšení kvóty. Využití nad rámec zakoupených jednotek propustnosti je omezené. Služba Event Hubs Capture kopíruje data přímo z interního úložiště Event Hubs, vynechává kvóty odchozích jednotek propustnosti a šetří výstup pro ostatní čtecí zařízení, jako je například Stream Analytics nebo Spark.

Po nakonfigurování se Event Hubs zachycení automaticky spustí při odeslání první události a pokračuje v běhu. Aby bylo zpracování pro příjem dat snazší, abyste věděli, že proces funguje, Event Hubs zapisuje prázdné soubory, pokud nejsou k dispozici žádná data. Tento proces poskytuje předvídatelné tempo a značku, které mohou zamezit dávkám procesorů.

## <a name="setting-up-event-hubs-capture"></a>Nastavení zachycení Event Hubs

V čase vytvoření centra událostí můžete pomocí [Azure Portal](https://portal.azure.com)nakonfigurovat záznam pomocí Azure Resource Manager šablony. Další informace najdete v následujících článcích:

- [Povolení funkce Event Hubs Capture prostřednictvím webu Azure Portal](event-hubs-capture-enable-through-portal.md)
- [Vytvoření oboru názvů Event Hubs s centrem událostí a povolení funkce Capture pomocí šablony Azure Resource Manageru](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


## <a name="exploring-the-captured-files-and-working-with-avro"></a>Zkoumání zachycených souborů a práce s Avro

Event Hubs Capture vytvoří soubory ve formátu Avro, jak je uvedeno v nakonfigurovaném časovém intervalu. Tyto soubory můžete zobrazit v jakémkoli nástroji, jako je například [Průzkumník služby Azure Storage][Azure Storage Explorer]. Soubory si můžete stáhnout místně, abyste na ně mohli pracovat.

Soubory vytvořené pomocí Event Hubs Capture mají následující schéma Avro:

![Avro schéma][3]

Snadný způsob, jak prozkoumat soubory Avro, je použití [nástroje Avro Tools][Avro Tools] jar od Apache. Na serveru [Apache][Apache Drill] můžete také využít zjednodušené prostředí založené na SQL nebo [Apache Spark][Apache Spark] k provádění komplexního distribuovaného zpracování na ingestovaná data. 

### <a name="use-apache-drill"></a>Použití možnosti Apache-podrobnosti

[Apache podrobněji][Apache Drill] je open source DOTAZOVACÍ modul SQL pro zkoumání velkých objemů dat, který se může dotazovat na strukturovaná a částečně strukturovaná data kdekoli. Modul může běžet jako samostatný uzel nebo jako velký cluster pro skvělý výkon.

K dispozici je nativní podpora služby Azure Blob Storage, která usnadňuje dotazování na data v souboru Avro, jak je popsáno v dokumentaci:

[Podrobnosti o Apache: modul plug-in Azure Blob Storage][Apache Drill: Azure Blob Storage Plugin]

Pro snazší dotazování zachycených souborů můžete vytvořit a spustit virtuální počítač s podporou nasazení Apache pomocí kontejneru pro přístup k úložišti objektů BLOB v Azure:

https://github.com/yorek/apache-drill-azure-blob

Kompletní ukázka je k dispozici ve streamování v úložišti škálování:

[Škálovatelné streamování: Event Hubs zachytávání]

### <a name="use-apache-spark"></a>Použít Apache Spark

[Apache Spark][Apache Spark] je "jednotný analytický modul pro velké zpracování dat". Podporuje různé jazyky, včetně SQL, a umožňuje snadný přístup k úložišti objektů BLOB v Azure. K dispozici je několik možností, jak Apache Spark v Azure spouštět, a každý z nich poskytuje snadný přístup k úložišti objektů BLOB v Azure:

- [HDInsight: adresování souborů ve službě Azure Storage][HDInsight: Address files in Azure storage]
- [Azure Databricks: úložiště objektů BLOB v Azure][Azure Databricks: Azure Blob Storage]
- [Azure Kubernetes Service](../aks/spark-job.md) 

### <a name="use-avro-tools"></a>Použití nástrojů Avro

[Nástroje Avro][Avro Tools] jsou k dispozici jako balíček jar. Po stažení souboru jar můžete zobrazit schéma konkrétního souboru Avro spuštěním následujícího příkazu:

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

Pomocí nástrojů Avro můžete také převést soubor na formát JSON a provést jiné zpracování.

Chcete-li provést pokročilejší zpracování, Stáhněte a nainstalujte Avro pro vaši volbu platformy. V době psaní tohoto zápisu jsou k dispozici implementace pro jazyky C, C++, C \# , Java, NodeJS, Perl, php, Python a Ruby.

Apache Avro nabízí kompletní Příručky k Začínáme pro [jazyky Java][Java] a [Python][Python]. Můžete si také přečíst článek [Začínáme s Event Hubs Capture](event-hubs-capture-python.md) .

## <a name="how-event-hubs-capture-is-charged"></a>Jak se účtuje Event Hubs zachytávání

Záznam Event Hubs se měří podobně jako jednotky propustnosti: jako hodinová sazba. Poplatek je přímo úměrný počtu jednotek propustnosti koupených pro obor názvů. Vzhledem k tomu, že se jednotky propustnosti zvyšují a snižují, Event Hubs, aby se měřiče zachycení zvýšily a snížily tak, aby Měřiče se objevují společně. Podrobnosti o cenách najdete v tématu [Event Hubs ceny](https://azure.microsoft.com/pricing/details/event-hubs/). 

Capture nespotřebovává kvótu pro odchozí přenosy, protože se účtuje samostatně. 

## <a name="integration-with-event-grid"></a>Integrace s Event Grid 

Jako zdroj můžete vytvořit předplatné Azure Event Grid s oborem názvů Event Hubs. V následujícím kurzu se dozvíte, jak vytvořit předplatné Event Grid s centrem událostí jako zdrojovou a Azure Functionsovou aplikací jako jímku: [zpracování a migrace zachycených Event Hubs dat do analýzy Azure synapse pomocí Event Grid a Azure Functions](store-captured-data-data-warehouse.md).

## <a name="next-steps"></a>Další kroky
Event Hubs Capture je nejjednodušší způsob, jak získat data do Azure. Pomocí Azure Data Lake, Azure Data Factory a Azure HDInsight můžete provádět dávkové zpracování a další analýzy s využitím známých nástrojů a platforem dle vašeho výběru, a to v jakémkoli měřítku, které potřebujete.

Přečtěte si, jak tuto funkci povolit pomocí šablony Azure Portal a Azure Resource Manager:

- [Použití webu Azure Portal k povolení funkce Event Hubs Capture](event-hubs-capture-enable-through-portal.md)
- [Použití šablony Azure Resource Manager k povolení zachycení Event Hubs](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


[Apache Avro]: https://avro.apache.org/
[Apache Drill]: https://drill.apache.org/
[Apache Spark]: https://spark.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: https://github.com/microsoft/AzureStorageExplorer/releases
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: https://downloads.apache.org/avro/stable/java/
[Java]: https://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: https://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: ./event-hubs-about.md
[HDInsight: Address files in Azure storage]:https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-blob-storage
[Azure Databricks: Azure Blob Storage]:https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[Apache Drill: Azure Blob Storage Plugin]:https://drill.apache.org/docs/azure-blob-storage-plugin/
[Škálovatelné streamování: Event Hubs zachytávání]:https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture
