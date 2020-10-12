---
title: Scénáře dat zahrnující Data Lake Storage Gen1 | Microsoft Docs
description: Seznamte se s různými scénáři a nástroji, pomocí kterých se data mohou ingestovat, zpracovávat, stahovat a vizuálně vyrozumět v Data Lake Storage Gen1 (dříve označované jako Azure Data Lake Store).
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 058862f2b274ef4e956c82fbcc44dec92a270d32
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89441047"
---
# <a name="using-azure-data-lake-storage-gen1-for-big-data-requirements"></a>Použití Azure Data Lake Storage Gen1 pro požadavky na velké objemy dat

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Existují čtyři klíčové fáze zpracování velkých objemů dat:

* Ingestování velkých objemů dat do úložiště dat v reálném čase nebo v dávkách
* Zpracování dat
* Stahování dat
* Vizualizace dat

V tomto článku se podíváme na tyto fáze s ohledem na Azure Data Lake Storage Gen1, abyste pochopili možnosti a nástroje, které jsou k dispozici pro splnění vašich požadavků na velké objemy dat.

## <a name="ingest-data-into-data-lake-storage-gen1"></a>Ingestování dat do Data Lake Storage Gen1
V této části se vysvětlují různé zdroje dat a různé způsoby, kterými je možné data do účtu Data Lake Storage Gen1 ingestovat.

![Ingestování dat do Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/ingest-data.png "Ingestování dat do Data Lake Storage Gen1")

### <a name="ad-hoc-data"></a>Ad hoc data
To představuje menší sady dat, které se používají pro vytváření prototypů aplikace s velkým objemem dat. Existují různé způsoby, jak ingestovat ad hoc data v závislosti na zdroji dat.

| Zdroj dat | Ingestování pomocí |
| --- | --- |
| Místní počítač |<ul> <li>[Azure Portal](data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Azure CLI](data-lake-store-get-started-cli-2.0.md)</li> <li>[Používání Data Lakech nástrojů pro Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Azure Storage Blob |<ul> <li>[Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)</li> <li>[Nástroj AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp běžící na clusteru HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Streamovaná data
To představuje data, která je možné vygenerovat různými zdroji, jako jsou aplikace, zařízení, senzory atd. Tato data je možné ingestovat do Data Lake Storage Gen1 pomocí různých nástrojů. Tyto nástroje budou obvykle zaznamenávat a zpracovávat data na základě události v reálném čase a následně zapisovat události v dávkách do Data Lake Storage Gen1 tak, aby je bylo možné dále zpracovat.

Níže jsou uvedené nástroje, které můžete použít:

* [Azure Stream Analytics](../stream-analytics/stream-analytics-data-lake-output.md) – události ingestované do Event Hubs lze zapsat do Azure Data Lake Storage Gen1 pomocí Azure Data Lake Storage Gen1ho výstupu.
* [Azure HDInsight pro Azure](../hdinsight/storm/apache-storm-write-data-lake-store.md) – data můžete zapisovat přímo do data Lake Storage Gen1 z clusteru s více procesory.
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) – můžete přijímat události z Event Hubs a pak je zapisovat do data Lake Storage Gen1 pomocí [sady data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Relační data
Můžete také zdrojová data z relačních databází. V časovém intervalu relační databáze shromažďují velké objemy dat, které poskytují klíčové přehledy, pokud jsou zpracovávány prostřednictvím kanálu pro velké objemy dat. Pomocí následujících nástrojů můžete přesunout taková data do Data Lake Storage Gen1.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Data protokolu webového serveru (nahrávání pomocí vlastních aplikací)
Tento typ datové sady je konkrétně vyvolán, protože analýza dat protokolu webového serveru je běžném případem použití pro aplikace s velkými objemy dat a vyžaduje nahrání velkých objemů souborů protokolu do Data Lake Storage Gen1. Pomocí některého z následujících nástrojů můžete napsat vlastní skripty nebo aplikace, které budou ukládat taková data.

* [Azure CLI](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Sada Azure Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

Pro nahrání dat protokolu webového serveru a také pro nahrávání dalších druhů dat (např. data ze sociálních sítí zabarvení) je dobrým přístupem k psaní vlastních skriptů nebo aplikací, protože nabízí flexibilitu, která umožňuje zahrnout komponentu pro nahrávání dat jako součást větší aplikace pro velké objemy dat. V některých případech může tento kód mít podobu skriptu nebo jednoduchého nástroje příkazového řádku. V jiných případech může být kód použit k integraci zpracování velkých objemů dat do obchodní aplikace nebo řešení.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Data přidružená k clusterům Azure HDInsight
Většina typů clusterů HDInsight (Hadoop, HBA, propamì) podporuje Data Lake Storage Gen1 jako úložiště dat. Clustery HDInsight přistupují k datům z objektů blob Azure Storage (WASB). Pro lepší výkon můžete zkopírovat data z WASB do účtu Data Lake Storage Gen1 přidruženého ke clusteru. K kopírování dat můžete použít následující nástroje.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [Služba AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Data uložená v místních nebo IaaS clusterech Hadoop
Velké objemy dat může být uloženo v existujících clusterech Hadoop místně na počítačích se systémem HDFS. Clustery Hadoop můžou být v místním nasazení nebo se můžou nacházet v rámci clusteru IaaS v Azure. Můžou nastat požadavky na kopírování takových dat do Azure Data Lake Storage Gen1 pro jednorázový přístup nebo pro opakovaný pokus. Existují různé možnosti, které můžete použít k dosažení tohoto. Níže je uveden seznam alternativ a Spojených kompromisů.

| Přístup | Podrobnosti | Výhody | Důležité informace |
| --- | --- | --- | --- |
| Použití Azure Data Factory (ADF) ke kopírování dat přímo z clusterů Hadoop do Azure Data Lake Storage Gen1 |[ADF podporuje HDFS jako zdroj dat.](../data-factory/connector-hdfs.md) |ADF poskytuje okamžitou podporu pro HDFS a první třídu a komplexní správu a monitorování. |Vyžaduje, aby byla Správa datá brána nasazená místně nebo v clusteru IaaS. |
| Exportujte data ze souborů Hadoop jako soubory. Pak zkopírujte soubory do Azure Data Lake Storage Gen1 pomocí vhodného mechanismu. |Soubory můžete zkopírovat do Azure Data Lake Storage Gen1 pomocí: <ul><li>[Azure PowerShell pro operační systém Windows](data-lake-store-get-started-powershell.md)</li><li>[Azure CLI](data-lake-store-get-started-cli-2.0.md)</li><li>Vlastní aplikace pomocí libovolné Data Lake Storage Gen1 sady SDK</li></ul> |Rychlé zahájení práce Může provádět přizpůsobená nahrávání |Proces s více kroky, který zahrnuje několik technologií. Správa a monitorování se budou po určitou dobu projevit jako výzvy s ohledem na vlastní povahu nástrojů. |
| Pomocí Distcp zkopírujte data z Hadoop do Azure Storage. Pak zkopírujte data z Azure Storage pro Data Lake Storage Gen1 pomocí vhodného mechanismu. |Data z Azure Storage můžete zkopírovat do Data Lake Storage Gen1 pomocí: <ul><li>[Azure Data Factory](../data-factory/copy-activity-overview.md)</li><li>[Nástroj AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[Apache DistCp běžící na clusterech HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |Můžete použít Open Source nástroje. |Proces s více kroky, který zahrnuje několik technologií |

### <a name="really-large-datasets"></a>Skutečně velké datové sady
Pro nahrání datových sad, které jsou v rozsahu několika terabajtů, můžou použití výše popsaných metod někdy být pomalé a nákladné. V takových případech můžete použít následující možnosti.

* **Pomocí Azure ExpressRoute**. Azure ExpressRoute vám umožňuje vytvářet privátní připojení mezi datacentry Azure a infrastrukturou ve vašich prostorách. To poskytuje spolehlivé možnosti pro přenos velkých objemů dat. Další informace najdete v [dokumentaci ke službě Azure ExpressRoute](../expressroute/expressroute-introduction.md).
* **"Offline" nahrávání dat**. Pokud z nějakého důvodu není možné používat Azure ExpressRoute, můžete použít [službu import/export v Azure](../storage/common/storage-import-export-service.md) k dodávání pevných disků s daty do datového centra Azure. Vaše data se napřed nahrají do Azure Storage objektů BLOB. Potom můžete pomocí nástroje [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) nebo [AdlCopy](data-lake-store-copy-data-azure-storage-blob.md) kopírovat data z Azure Storage objektů blob do data Lake Storage Gen1.

  > [!NOTE]
  > Při použití služby Import/export by velikost souborů na discích, které odesíláte do datového centra Azure, neměla být větší než 195 GB.
  >
  >

## <a name="process-data-stored-in-data-lake-storage-gen1"></a>Zpracování dat uložených v Data Lake Storage Gen1
Jakmile jsou data dostupná v Data Lake Storage Gen1 můžete pro tato data spustit analýzu pomocí podporovaných aplikací pro velké objemy dat. V současné době můžete pomocí Azure HDInsight a Azure Data Lake Analytics spouštět úlohy analýzy dat na datech uložených v Data Lake Storage Gen1.

![Analyzovat data v Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/analyze-data.png "Analyzovat data v Data Lake Storage Gen1")

Můžete se podívat na následující příklady.

* [Vytvoření clusteru HDInsight s Data Lake Storage Gen1 jako úložiště](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Použití Azure Data Lake Analytics s Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-storage-gen1"></a>Stáhnout data z Data Lake Storage Gen1
Můžete také chtít stáhnout nebo přesunout data z Azure Data Lake Storage Gen1 ve scénářích, jako jsou například:

* Přesuňte data do jiných úložišť do rozhraní s vašimi stávajícími kanály zpracování dat. Například můžete chtít přesunout data z Data Lake Storage Gen1 do Azure SQL Database nebo SQL Server.
* Při sestavování prototypů aplikací Stáhněte data do místního počítače pro zpracování v prostředích IDE.

![Výstupní data z Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/egress-data.png "Výstupní data z Data Lake Storage Gen1")

V takových případech můžete použít kteroukoli z následujících možností:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Následující metody můžete použít také k zápisu vlastního skriptu nebo aplikace pro stažení dat z Data Lake Storage Gen1.

* [Azure CLI](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Sada Azure Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-storage-gen1"></a>Vizualizovat data v Data Lake Storage Gen1
Pomocí kombinace služeb můžete vytvářet vizuální reprezentace dat uložených v Data Lake Storage Gen1.

![Vizualizovat data v Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/visualize-data.png "Vizualizovat data v Data Lake Storage Gen1")

* [K přesunu dat z Data Lake Storage Gen1 do služby Azure synapse Analytics (dříve SQL Data Warehouse)](../data-factory/copy-activity-overview.md) můžete začít pomocí Azure Data Factory.
* Potom můžete [integrovat Power BI s Azure synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) a vytvořit tak vizuální reprezentaci dat.
