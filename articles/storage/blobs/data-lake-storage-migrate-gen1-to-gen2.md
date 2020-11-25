---
title: Migrace Azure Data Lake Storage z Gen1 na Gen2
description: Migrujte Azure Data Lake Storage z Gen1 na Gen2, která je postavená na službě Azure Blob Storage a poskytuje sadu funkcí vyhrazených pro analýzy velkých objemů dat.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 03/11/2020
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: aeb2c58504d1f058a3b887e02a7b7406c09db5b6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913143"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>Migrace Azure Data Lake Storage z Gen1 na Gen2

Data, úlohy a aplikace můžete migrovat z Data Lake Storage Gen1 do Data Lake Storage Gen2.

Azure Data Lake Storage Gen2 je postavená na [službě Azure Blob Storage](storage-blobs-introduction.md) a poskytuje sadu funkcí vyhrazených pro analýzy velkých objemů dat. [Data Lake Storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/) kombinuje funkce z [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml), jako jsou sémantika systému souborů, adresář a zabezpečení na úrovni souborů a škálování s nízkými náklady, vrstvenými úložištěm, vysokou dostupností a možnostmi zotavení po havárii z [Azure Blob Storage](storage-blobs-introduction.md).

> [!NOTE]
> Pro snazší čtení Tento článek používá termín *Gen1* k odkazování na Azure Data Lake Storage Gen1 a pojem *Gen2* pro odkazování na Azure Data Lake Storage Gen2.

## <a name="recommended-approach"></a>Doporučený postup

K migraci na Gen2 doporučujeme následující postup.

: heavy_check_mark: Krok 1: vyhodnocení připravenosti

: heavy_check_mark: Krok 2: Příprava na migraci

: heavy_check_mark: Krok 3: migrace úloh dat a aplikací

: heavy_check_mark: Krok 4: přímou migraci z Gen1 na Gen2

> [!NOTE]
> Gen1 a Gen2 jsou různé služby, ale není k dispozici žádné místní prostředí pro upgrade, vyžaduje se úmyslné úsilí migrace. 

### <a name="step-1-assess-readiness"></a>Krok 1: vyhodnocení připravenosti

1. Přečtěte si o [Data Lake Storage Gen2 nabídce](https://azure.microsoft.com/services/storage/data-lake-storage/); Jedná se o výhody, náklady a obecnou architekturu. 

2. [Porovnejte možnosti](#gen1-gen2-feature-comparison) Gen1 s Gen2. 

3. Projděte si seznam [známých problémů](data-lake-storage-known-issues.md) pro vyhodnocení všech mezer ve funkcích.

4. Gen2 podporuje funkce služby Blob Storage, jako je [protokolování diagnostiky](../common/storage-analytics-logging.md), [úrovně přístupu](storage-blob-storage-tiers.md)a [zásady správy životního cyklu úložiště objektů BLOB](storage-lifecycle-management-concepts.md). Pokud jste s použitím některé z těchto funkcí zajímaví, přečtěte si [aktuální úroveň podpory](./data-lake-storage-supported-blob-storage-features.md).

5. Projděte si aktuální stav [podpory ekosystému Azure](./data-lake-storage-multi-protocol-access.md) , abyste zajistili, že Gen2 podporuje všechny služby, na kterých vaše řešení závisejí.

### <a name="step-2-prepare-to-migrate"></a>Krok 2: Příprava na migraci

1. Identifikujte datové sady, které budete migrovat.

   Využijte tuto příležitost k vyčištění datových sad, které už nepoužíváte. Pokud neplánujete migrovat všechna vaše data najednou, proveďte tuto dobu a Identifikujte logické skupiny dat, které můžete migrovat v rámci fází.
   
2. Určete, jaký dopad bude mít migrace na vaši firmu.

   Například zvažte, jestli v průběhu migrace nemůžete mít nějaké výpadky. Tyto informace vám mohou pomáhat při identifikaci vhodného vzoru migrace a výběru nejvhodnějších nástrojů.

3. Vytvořte plán migrace. 

   Doporučujeme tyto [vzory migrace](#migration-patterns). Můžete zvolit jeden z těchto vzorů, zkombinovat je dohromady nebo navrhnout vlastní vzor.

### <a name="step-3-migrate-data-workloads-and-applications"></a>Krok 3: migrace dat, úloh a aplikací

Migrujte data, úlohy a aplikace pomocí vhodného vzoru. Doporučujeme, abyste scénáře ověřovali přírůstkově.

1. [Vytvořte účet úložiště](../common/storage-account-create.md) a povolte funkci hierarchického oboru názvů. 

2. Migrujte svá data. 

3. Nakonfigurujte [služby ve vašich úlohách](./data-lake-storage-supported-azure-services.md) tak, aby odkazovaly na koncový bod Gen2. 
   
4. Aktualizujte aplikace tak, aby používaly rozhraní Gen2 API. Přečtěte si příručky pro [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md), [JavaScript](data-lake-storage-directory-file-acl-javascript.md) a [REST](/rest/api/storageservices/data-lake-storage-gen2). 
   
5. Aktualizujte skripty pro použití Data Lake Storage Gen2 [rutiny prostředí PowerShell](data-lake-storage-directory-file-acl-powershell.md)a [příkazy rozhraní příkazového řádku Azure CLI](data-lake-storage-directory-file-acl-cli.md).
   
6. Vyhledejte odkazy URI, které obsahují řetězec `adl://` v souborech kódu nebo v poznámkových blocích datacihly, Apache Hive soubory HQL nebo jakýkoli jiný soubor, který se používá jako součást vašich úloh. Nahraďte tyto odkazy [identifikátorem URI Gen2 formátovaného](data-lake-storage-introduction-abfs-uri.md) nového účtu úložiště. Příklad: identifikátor URI Gen1: `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` může být `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile` . 

7. Nakonfigurujte zabezpečení na účtu tak, aby zahrnovalo [role Azure](../common/storage-auth-aad-rbac-portal.md), [zabezpečení na úrovni souborů a složek](data-lake-storage-access-control.md)a [Azure Storage brány firewall a virtuální sítě](../common/storage-network-security.md).

### <a name="step-4-cutover-from-gen1-to-gen2"></a>Krok 4: přímou migraci z Gen1 na Gen2

Jakmile budete mít jistotu, že vaše aplikace a úlohy jsou v Gen2 stabilní, můžete začít používat Gen2 k uspokojení vašich obchodních scénářů. Vypněte všechny zbývající kanály, které běží na Gen1, a vyřaďte účet Gen1. 

<a id="gen1-gen2-feature-comparison"></a>

## <a name="gen1-vs-gen2-capabilities"></a>Možnosti Gen1 vs Gen2

Tato tabulka porovnává možnosti Gen1 s Gen2.

|Oblast |Gen1   |Gen2 |
|---|---|---|
|Organizace dat|[Hierarchický obor názvů](data-lake-storage-namespace.md)<br>Podpora souborů a složek|[Hierarchický obor názvů](data-lake-storage-namespace.md)<br>Podpora kontejnerů, souborů a složek |
|Geografická redundance| [LRS](../common/storage-redundancy.md#locally-redundant-storage)| [LRS](../common/storage-redundancy.md#locally-redundant-storage), [ZRS](../common/storage-redundancy.md#zone-redundant-storage), [GRS](../common/storage-redundancy.md#geo-redundant-storage), [RA-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|Authentication|[Spravovaná identita AAD](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Instanční objekty](../../active-directory/develop/app-objects-and-service-principals.md)|[Spravovaná identita AAD](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Instanční objekty](../../active-directory/develop/app-objects-and-service-principals.md)<br>[Sdílený přístupový klíč](/rest/api/storageservices/authorize-with-shared-key)|
|Autorizace|Správa – [Azure RBAC](../../role-based-access-control/overview.md)<br>Data – [seznamy ACL](data-lake-storage-access-control.md)|Správa – [Azure RBAC](../../role-based-access-control/overview.md)<br>Data-  [seznamy řízení přístupu (ACL)](data-lake-storage-access-control.md), [Azure RBAC](../../role-based-access-control/overview.md) |
|Šifrování – neaktivní neaktivní data|Strana serveru – s použitím klíčů spravovaných [Microsoftem](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) nebo [zákazníky](../common/customer-managed-keys-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|Strana serveru – s použitím klíčů spravovaných [Microsoftem](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) nebo [zákazníky](../common/customer-managed-keys-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Podpora virtuální sítě|[Integrace virtuální sítě](../../data-lake-store/data-lake-store-network-security.md)|[Koncové body služby](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [privátní koncové body](../common/storage-private-endpoints.md)|
|Vývojářské prostředí|[REST](../../data-lake-store/data-lake-store-data-operations-rest-api.md), [.NET](../../data-lake-store/data-lake-store-data-operations-net-sdk.md), [Java](../../data-lake-store/data-lake-store-get-started-java-sdk.md), [Python](../../data-lake-store/data-lake-store-data-operations-python.md), [PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md), [Azure CLI](../../data-lake-store/data-lake-store-get-started-cli-2.0.md)|Všeobecně dostupná – [REST](/rest/api/storageservices/data-lake-storage-gen2), [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md)<br>Public Preview – [JavaScript](data-lake-storage-directory-file-acl-javascript.md), [POWERSHELL](data-lake-storage-directory-file-acl-powershell.md), [Azure CLI](data-lake-storage-directory-file-acl-cli.md)|
|Protokoly prostředků|Klasické protokoly<br>[Azure Monitor integrovaný](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[Klasické protokoly](../common/storage-analytics-logging.md) – všeobecně dostupné<br>Integrace se službou Azure monitor – TBD časová osa|
|Ekosystém|[HDInsight (3,6)](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md), [Azure Databricks (3,1 a vyšší)](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html), [Azure synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md), [ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight (3,6, 4,0)](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md), [Azure Databricks (5,1 a vyšší)](/azure/databricks/data/data-sources/azure/azure-datalake-gen2), [Azure synapse Analytics](../../azure-sql/database/vnet-service-endpoint-rule-overview.md), [ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns"></a>

## <a name="gen1-to-gen2-patterns"></a>Gen1 ke vzorům Gen2

Zvolte způsob migrace a pak tento model upravte podle potřeby.

|||
|---|---|
|**Zvednutí a posunutí**|Nejjednodušší vzor. Ideální, pokud vaše datové kanály můžou umožnit výpadky.|
|**Přírůstková kopie**|Podobný jako u *zvedání a Shift*, ale s menším prostojem. Ideální pro velké objemy dat, která kopírování trvá déle.|
|**Duální kanál**|Ideální pro kanály, které nemůžou dovolit žádné výpadky.|
|**Obousměrná synchronizace**|Podobně jako *duální kanál*, ale s více stupným přístupem, který je vhodný pro složitější kanály.|

Pojďme se podrobněji podívat na každý vzor.
 
### <a name="lift-and-shift-pattern"></a>Vzorek zvednutí a posunutí

Toto je nejjednodušší vzor. 

1. Zastavte všechny zápisy do Gen1.

2. Přesuňte data z Gen1 do Gen2. Doporučujeme [Azure Data Factory](../../data-factory/connector-azure-data-lake-storage.md). Seznamy ACL se kopírují s daty.

3. Point ingestujte operace a úlohy do Gen2.

4. Vyřaďte z provozu Gen1.

Podívejte se na náš vzorový kód pro vzor výtahu a posunutí v naší [ukázce migrace a posunutí](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Lift%20and%20Shift/README.md).

> [!div class="mx-imgBorder"]
> ![vzorek zvednutí a posunutí](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>Pokyny pro použití vzorku výtahu a posunutí

: heavy_check_mark: přímou migraci z Gen1 na Gen2 pro všechny úlohy ve stejnou dobu.

: heavy_check_mark: během migrace a za dobu přímou migraci očekávat prostoje.

: heavy_check_mark: ideální pro kanály, které můžou poskytovat výpadky a všechny aplikace je možné upgradovat najednou.

### <a name="incremental-copy-pattern"></a>Vzor přírůstkového kopírování

1. Začněte přesouvat data z Gen1 do Gen2. Doporučujeme [Azure Data Factory](../../data-factory/connector-azure-data-lake-storage.md). Seznamy ACL se kopírují s daty.

2. Přírůstkové kopírování nových dat z Gen1.

3. Po zkopírování všech dat zastavte všechny zápisy do Gen1 a nasměrujte úlohy na Gen2.

4. Vyřaďte z provozu Gen1.

Podívejte se na náš vzorový kód pro vzor přírůstkové kopírování v naší [ukázce migrace přírůstkového kopírování](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Incremental/README.md).


> [!div class="mx-imgBorder"]
> ![Vzor přírůstkového kopírování](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>Pokyny pro použití vzoru přírůstkového kopírování:

: heavy_check_mark: přímou migraci z Gen1 na Gen2 pro všechny úlohy ve stejnou dobu.

: heavy_check_mark: očekává se výpadky jenom během přímou migraci období.

: heavy_check_mark: ideální pro kanály, kde jsou všechny aplikace upgradovány najednou, ale kopírování dat vyžaduje více času.

### <a name="dual-pipeline-pattern"></a>Vzor duálního kanálu

1. Přesuňte data z Gen1 do Gen2. Doporučujeme [Azure Data Factory](../../data-factory/connector-azure-data-lake-storage.md). Seznamy ACL se kopírují s daty.

2. Ingestování nových dat do Gen1 i Gen2.

3. Najeďte úlohy na Gen2.

4. Zastavte všechny zápisy do Gen1 a potom vyřaďte z provozu Gen1.

Podívejte se na náš vzorový kód pro vzor duálního kanálu v naší [ukázce migrace dvou kanálů](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Dual%20pipeline/README.md).

> [!div class="mx-imgBorder"]
> ![Vzor duálního kanálu](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>Předpoklady pro používání vzoru Dual Pipeline:

: heavy_check_mark: kanály Gen1 a Gen2 se spouštějí vedle sebe.

: heavy_check_mark: podporuje nulové výpadky.

: heavy_check_mark: ideální v situacích, kdy vaše úlohy a aplikace neumožňují žádné výpadky, a můžete ingestovat do obou účtů úložiště.

### <a name="bi-directional-sync-pattern"></a>Způsob obousměrné synchronizace

1. Nastavte obousměrnou replikaci mezi Gen1 a Gen2. Doporučujeme [WanDisco](https://docs.wandisco.com/bigdata/wdfusion/adls/). Nabízí opravnou funkci pro stávající data.

3. Po dokončení všech přesunů zastavte všechny zápisy na Gen1 a vypněte obousměrnou replikaci.

4. Vyřaďte z provozu Gen1.

Podívejte se na náš vzorový kód pro obousměrný způsob synchronizace v [ukázce obousměrné synchronizace migrace](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Bi-directional/README.md).

> [!div class="mx-imgBorder"]
> ![Obousměrný vzor](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>Pokyny pro použití schématu obousměrné synchronizace:

: heavy_check_mark: ideální pro složité scénáře, které zahrnují velký počet kanálů a závislostí, kde postup postupného přístupu může být smysluplnější.  

: heavy_check_mark: úsilí při migraci je vysoké, ale poskytuje souběžnou podporu pro Gen1 a Gen2.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o různých součástech nastavení zabezpečení pro účet úložiště. Viz [Průvodce zabezpečením Azure Storage](./security-recommendations.md).
- Optimalizujte výkon pro vaše Data Lake Store. Zvýšení výkonu najdete v tématu věnovaném [optimalizaci Azure Data Lake Storage Gen2](data-lake-storage-performance-tuning-guidance.md) .
- Projděte si osvědčené postupy pro správu Data Lake Store. Viz [osvědčené postupy pro používání Azure Data Lake Storage Gen2](data-lake-storage-best-practices.md)