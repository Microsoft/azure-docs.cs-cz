---
title: Migrace úložiště datových jezer Azure z Gen1 do Gen2
description: Migrace úložiště datových jezer Azure z Gen1 do Gen2.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 03/11/2020
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: fb982324b66c5ac0d2db00eb906ed850827bc72e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533279"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>Migrace úložiště datových jezer Azure z Gen1 do Gen2

Svá data, úlohy a aplikace můžete migrovat z Data Lake Storage Gen1 do Data Lake Storage Gen2.

Azure Data Lake Storage Gen2 je integrované na [úložišti Objektů blob Azure](storage-blobs-introduction.md) a poskytuje sadu funkcí určených pro analýzu velkých objemů dat. [Storage Data Lake Storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/) kombinuje funkce z [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), jako je sémantika systému souborů, zabezpečení adresářů a souborů a škálování s levným vrstveným úložištěm, vysokou dostupností a možností zotavení po havárii z úložiště objektů [Blob Azure](storage-blobs-introduction.md).

> [!NOTE]
> Pro snadnější čtení tento článek používá termín *Gen1* odkazovat na Azure Data Lake Storage Gen1 a termín *Gen2* odkazovat na Azure Data Lake Storage Gen2.

## <a name="recommended-approach"></a>Doporučený přístup

Chcete-li migrovat na Gen2, doporučujeme následující přístup.

:heavy_check_mark: Krok 1: Posouzení připravenosti

:heavy_check_mark: Krok 2: Příprava na migraci

:heavy_check_mark: Krok 3: Migrace úloh dat a aplikací

:heavy_check_mark: Krok 4: Cutover z Gen1 do Gen2

> [!NOTE]
> Gen1 a Gen2 jsou různé služby, neexistuje žádné zkušenosti s upgradem na místě, vyžaduje se úmyslné úsilí o migraci. 

### <a name="step-1-assess-readiness"></a>Krok 1: Posouzení připravenosti

1. Další informace o [nabídce Data Lake Storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/); je to výhody, náklady a obecná architektura. 

2. [Porovnejte schopnosti](#gen1-gen2-feature-comparison) Gen1 s gen2. 

3. Zkontrolujte seznam [známých problémů](data-lake-storage-known-issues.md) a vyhodnoťte případné mezery ve funkčnosti.

4. Gen2 podporuje funkce úložiště objektů Blob, jako je [diagnostické protokolování](../common/storage-analytics-logging.md), [vrstvy přístupu](storage-blob-storage-tiers.md)a [zásady správy životního cyklu úložiště objektů Blob](storage-lifecycle-management-concepts.md). Pokud vás některé z těchto funkcí zajímá, zkontrolujte [aktuální úroveň podpory](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-supported-blob-storage-features).

5. Zkontrolujte aktuální stav [podpory ekosystému Azure](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-multi-protocol-access) a ujistěte se, že Gen2 podporuje všechny služby, na kterých závisí vaše řešení.

### <a name="step-2-prepare-to-migrate"></a>Krok 2: Příprava na migraci

1. Identifikujte datové sady, které budete migrovat.

   Využijte této příležitosti k vyčištění datových sad, které již nepoužíváte. Pokud neplánujete migrovat všechna data najednou, věnujte tento čas identifikaci logických skupin dat, které můžete migrovat ve fázích.
   
2. Určete dopad migrace na vaši firmu.

   Zvažte například, zda si můžete dovolit prostoje během migrace. Tyto aspekty vám mohou pomoci určit vhodný způsob migrace a zvolit nejvhodnější nástroje.

3. Vytvořte plán migrace. 

   Doporučujeme tyto [vzory migrace](#migration-patterns). Můžete si vybrat jeden z těchto vzorů, kombinovat je dohromady nebo navrhnout vlastní vzor.

### <a name="step-3-migrate-data-workloads-and-applications"></a>Krok 3: Migrace dat, úloh a aplikací

Migrujte data, úlohy a aplikace pomocí preferovaného vzoru. Doporučujeme ověřit scénáře postupně.

1. [Vytvořte účet úložiště](data-lake-storage-quickstart-create-account.md) a povolte funkci hierarchického oboru názvů. 

2. Migrujte data. 

3. Nakonfigurujte [služby ve svých úlohách](data-lake-storage-integrate-with-azure-services.md) tak, aby ukazovaly na koncový bod Gen2. 
   
4. Aktualizujte aplikace tak, aby používaly gen2 API. Viz návody pro [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md), [JavaScript](data-lake-storage-directory-file-acl-javascript.md) a [REST](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2). 
   
5. Aktualizujte skripty tak, aby [používaly rutiny Prostředí PowerShell](data-lake-storage-directory-file-acl-powershell.md)úložiště Datového jezera a [příkazy Azure CLI](data-lake-storage-directory-file-acl-cli.md).
   
6. Vyhledejte odkazy URI, které `adl://` obsahují řetězec v souborech kódu nebo v poznámkových blocích Databricks, souborech HqL Apache Hive nebo jiném souboru používaném jako součást vašich úloh. Nahraďte tyto odkazy [identifikátorem URI ve formátu Gen2](data-lake-storage-introduction-abfs-uri.md) nového účtu úložiště. Například: Identifikátor URI `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` Gen1: může být . `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile` 

7. Nakonfigurujte zabezpečení svého účtu tak, aby zahrnovalo [role řízení přístupu na základě rolí (RBAC) na základě rolí](../common/storage-auth-aad-rbac-portal.md), zabezpečení na úrovni souborů a [složek](data-lake-storage-access-control.md)a [brány firewall azure storage a virtuální sítě](../common/storage-network-security.md).

### <a name="step-4-cutover-from-gen1-to-gen2"></a>Krok 4: Cutover z Gen1 do Gen2

Poté, co jste si jisti, že vaše aplikace a úlohy jsou stabilní na Gen2, můžete začít používat Gen2 k uspokojení vašich obchodních scénářů. Vypněte všechny zbývající kanály, které jsou spuštěny na Gen1 a vyřaďte z provozu svůj účet Gen1. 

<a id="gen1-gen2-feature-comparison" />

## <a name="gen1-vs-gen2-capabilities"></a>Funkce Gen1 vs Gen2

Tato tabulka porovnává schopnosti Gen1 s gen2.

|Oblast |Gen1 (Gen1)   |Gen2 (Gen2) |
|---|---|---|
|Organizace dat|[Hierarchický obor názvů](data-lake-storage-namespace.md)<br>Podpora souborů a složek|[Hierarchický obor názvů](data-lake-storage-namespace.md)<br>Podpora kontejnerů, souborů a složek |
|Geografická redundance| [LRS](../common/storage-redundancy.md#locally-redundant-storage)| [LRS](../common/storage-redundancy.md#locally-redundant-storage), [ZRS](../common/storage-redundancy.md#zone-redundant-storage), [GRS](../common/storage-redundancy.md#geo-redundant-storage), [RA-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|Ověřování|[Identita spravovaná v aad](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Instanční objekty](../../active-directory/develop/app-objects-and-service-principals.md)|[Identita spravovaná v aad](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Instanční objekty](../../active-directory/develop/app-objects-and-service-principals.md)<br>[Sdílený přístupový klíč](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)|
|Autorizace|Management - [RBAC](../../role-based-access-control/overview.md)<br>Data – [akly](data-lake-storage-access-control.md)|Management – [RBAC](../../role-based-access-control/overview.md)<br>Data - [ACLs](data-lake-storage-access-control.md), [RBAC](../../role-based-access-control/overview.md) |
|Šifrování – data v klidovém stavu|Strana serveru – s [klíči spravovanými společností Microsoft](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) nebo [zákazníkem](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|Strana serveru – s [klíči spravovanými společností Microsoft](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) nebo [zákazníkem](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Podpora virtuálních sítí|[Integrace virtuální sítě](../../data-lake-store/data-lake-store-network-security.md)|[Koncové body služby](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [soukromé koncové body (veřejná verze Preview)](../common/storage-private-endpoints.md)|
|Vývojářské prostředí|[REST](../../data-lake-store/data-lake-store-data-operations-rest-api.md), [.NET](../../data-lake-store/data-lake-store-data-operations-net-sdk.md), [Java](../../data-lake-store/data-lake-store-get-started-java-sdk.md), [Python](../../data-lake-store/data-lake-store-data-operations-python.md), [PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md), [Azure CLI](../../data-lake-store/data-lake-store-get-started-cli-2.0.md)|[REST](/rest/api/storageservices/data-lake-storage-gen2), [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md), [JavaScript](data-lake-storage-directory-file-acl-javascript.md), [PowerShell](data-lake-storage-directory-file-acl-powershell.md), [Azure CLI](data-lake-storage-directory-file-acl-cli.md) (ve verzi Public Preview)|
|Diagnostické protokoly|Klasické protokoly<br>[Integrovaný Azure Monitor](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[Klasické protokoly](../common/storage-analytics-logging.md) (ve verzi Public Preview)<br>Integrace azure monitoru – časová osa TBD|
|Ekosystém|[HDInsight (3.6),](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) [Azure Databricks (3.1 a vyšší)](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html), [SQL DW](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store), [ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight (3.6, 4.0)](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md), [Azure Databricks (5.1 a vyšší)](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-datalake-gen2), [SQL DW](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), [ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns" />

## <a name="gen1-to-gen2-patterns"></a>Vzory Gen1 až Gen2

Zvolte vzorek migrace a podle potřeby jej upravte.

|||
|---|---|
|**Zdvižnit a posunout**|Nejjednodušší vzor. Ideální, pokud si vaše datové kanály mohou dovolit prostoje.|
|**Přírůstková kopie**|Podobně jako *výtah a posun*, ale s menšími prostoji. Ideální pro velké množství dat, která trvat déle kopírovat.|
|**Duální potrubí**|Ideální pro potrubí, které si nemohou dovolit žádné prostoje.|
|**Obousměrná synchronizace**|Podobně jako *u duálního potrubí*, ale s více fázovaným přístupem, který je vhodný pro složitější potrubí.|

Podívejme se blíže na každý vzor.
 
### <a name="lift-and-shift-pattern"></a>Vzor zdvihu a posunu

To je nejjednodušší vzor.

1. Zastavit všechny zápisy do Gen1.

2. Přesuňte data z Gen1 do Gen2. Doporučujeme [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage). AcLs kopírovat s daty.

3. Bod ingestování operace a úlohy Gen2.

4. Vyřazení gen1 z provozu.

> [!div class="mx-imgBorder"]
> ![vzor zdvihu a posunu](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>Důležité informace o použití vzoru výtahu a směny

:heavy_check_mark: Cutover z Gen1 na Gen2 pro všechny úlohy současně.

:heavy_check_mark: Očekávejte prostoje během migrace a období přerušení.

:heavy_check_mark: Ideální pro kanály, které si mohou dovolit prostoje a všechny aplikace lze upgradovat najednou.

### <a name="incremental-copy-pattern"></a>Přírůstkový vzor kopírování

1. Začněte přesouvat data z Gen1 do Gen2. Doporučujeme [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage). AcLs kopírovat s daty.

2. Postupně kopírujte nová data z Gen1.

3. Po zkopírování všech dat zastavte všechny zápisy do Gen1 a přejděte na gen2.

4. Vyřazení gen1 z provozu.

> [!div class="mx-imgBorder"]
> ![Přírůstkový vzor kopírování](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>Důležité informace o použití vzorku přírůstkové kopie:

:heavy_check_mark: Cutover z Gen1 na Gen2 pro všechny úlohy současně.

:heavy_check_mark: Očekávejte pouze prostoje během období přerušení.

:heavy_check_mark: Ideální pro kanály, kde všechny aplikace upgradovány najednou, ale kopírování dat vyžaduje více času.

### <a name="dual-pipeline-pattern"></a>Vzor duálního potrubí

1. Přesuňte data z Gen1 do Gen2. Doporučujeme [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage). AcLs kopírovat s daty.

2. Ingestovat nová data do Gen1 i Gen2.

3. Přejděte úlohy na Gen2.

4. Zastavit všechny zápisy do Gen1 a pak vyřadit z provozu Gen1.

> [!div class="mx-imgBorder"]
> ![Vzor duálního potrubí](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>Důležité informace o použití vzoru duálního kanálu:

:heavy_check_mark: Potrubí Gen1 a Gen2 běží vedle sebe.

:heavy_check_mark: Podporuje nulové prostoje.

:heavy_check_mark: Ideální v situacích, kdy si vaše úlohy a aplikace nemohou dovolit žádné prostoje, a můžete ingestovat do obou účtů úložiště.

### <a name="bi-directional-sync-pattern"></a>Obousměrný vzor synchronizace

1. Nastavte obousměrnou replikaci mezi Gen1 a Gen2. Doporučujeme [WanDisco](https://docs.wandisco.com/bigdata/wdfusion/adls/). Nabízí funkci opravy pro stávající data.

3. Po dokončení všech tahů zastavte všechny zápisy do Gen1 a vypněte obousměrnou replikaci.

4. Vyřazení gen1 z provozu.

> [!div class="mx-imgBorder"]
> ![Obousměrný vzor](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>Důležité informace o použití obousměrného vzoru synchronizace:

:heavy_check_mark: Ideální pro složité scénáře, které zahrnují velký počet kanálů a závislostí, kde může mít fázový přístup větší smysl.  

: heavy_check_mark: Migrace úsilí je vysoká, ale poskytuje side-by-side podporu pro Gen1 a Gen2.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o různých částech nastavení zabezpečení pro účet úložiště. Viz [Průvodce zabezpečením azure storage](../common/storage-security-guide.md).
- Optimalizujte výkon úložiště datových jezer. Viz [Optimalizace Azure Data Lake Storage Gen2 pro výkon](data-lake-storage-performance-tuning-guidance.md)
- Projděte si doporučené postupy pro správu úložiště datových jezer. Podívejte se [na doporučené postupy pro používání Azure Data Lake Storage Gen2](data-lake-storage-best-practices.md)

