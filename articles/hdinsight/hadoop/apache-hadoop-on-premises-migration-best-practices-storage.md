---
title: 'Úložiště: migrace místních Apache Hadoop do Azure HDInsight'
description: Naučte se osvědčené postupy úložiště pro migraci místních clusterů Hadoop do Azure HDInsight.
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/10/2019
ms.openlocfilehash: 89a2a659e195afcae336c930a101ed0c0b470cb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101715407"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight"></a>Migrace místních Apache Hadoopových clusterů do Azure HDInsight

Tento článek obsahuje doporučení pro ukládání dat v systémech Azure HDInsight. Je součástí série, která poskytuje osvědčené postupy, které vám pomůžou s migrací místních Apache Hadoop systémů do Azure HDInsight.

## <a name="choose-right-storage-system-for-hdinsight-clusters"></a>Volba správného systému úložiště pro clustery HDInsight

Místní strukturu adresářů Apache Hadoopho systému souborů (HDFS) je možné znovu vytvořit ve službě Azure Blob Storage nebo v Azure Data Lake Storage. Pak můžete bezpečně odstranit clustery HDInsight, které jsou používány pro výpočty, aniž by došlo ke ztrátě uživatelských dat. Obě služby se dají použít jako výchozí systém souborů i pro další systém souborů pro cluster HDInsight. Cluster HDInsight a účet úložiště musí být hostované ve stejné oblasti.

### <a name="azure-storage"></a>Azure Storage

Clustery HDInsight můžou použít kontejner objektů BLOB v Azure Storage jako výchozí systém souborů nebo jako další systém souborů. Účet úložiště úrovně Standard se podporuje pro použití s clustery HDInsight. Vrstva Premier není podporovaná. Výchozí kontejner objektu blob ukládá konkrétní informace, jako je historie úlohy a protokoly. Sdílení jednoho kontejneru objektů BLOB jako výchozího systému souborů pro více clusterů se nepodporuje.

Účty úložiště, které jsou definovány v procesu vytváření a jejich příslušné klíče, jsou uloženy v `%HADOOP_HOME%/conf/core-site.xml` uzlech clusteru. V konfiguraci HDFS v uživatelském rozhraní Ambari je taky možné k nim přistupovat v části vlastní základní lokalita. Klíč účtu úložiště je ve výchozím nastavení zašifrovaný a k dešifrování klíčů před předáním do démonů Hadoop se používá vlastní šifrovací skript. Úlohy, jako jsou podregistry, MapReduce, Hadoop streaming a prase, obsahují popis účtů úložiště a metadat.

Azure Storage lze geograficky replikovat. I když geografická replikace zajišťuje geografickou obnovu a redundanci dat, převzetí služeb při selhání geograficky replikovaným umístěním má vážně vliv na výkon a může se stát, že budou účtovány další náklady. Doporučení je zvolit v takovém případě geografickou replikaci a jenom v případě, že hodnota dat stojí za další náklady.

Pro přístup k datům, která jsou uložená v Azure Storage, se dá použít jeden z následujících formátů:

|Formát přístupu k datům |Description |
|---|---|
|`wasb:///`|Přístup k výchozímu úložišti pomocí nešifrované komunikace|
|`wasbs:///`|Přístup k výchozímu úložišti pomocí šifrované komunikace.|
|`wasb://<container-name>@<account-name>.blob.core.windows.net/`|Používá se při komunikaci s jiným než výchozím účtem úložiště. |

[Cíle škálovatelnosti pro účty úložiště úrovně Standard](../../storage/common/scalability-targets-standard-account.md) obsahují aktuální limity pro účty Azure Storage. Pokud požadavky aplikace překročí cíle škálovatelnosti jednoho účtu úložiště, může být aplikace sestavená tak, aby používala více účtů úložiště, a pak rozdělit datové objekty mezi tyto účty úložiště.

[Analýza úložiště Azure](../../storage/common/storage-analytics.md) poskytuje metriky pro všechny služby úložiště a Azure Portal je možné nakonfigurovat tak, aby byly metriky shromažďovány pro vizuální grafy. Výstrahy se dají vytvořit, pokud chcete upozorňovat na dosažení prahových hodnot pro metriky prostředků úložiště.

Azure Storage nabízí [obnovitelné odstranění objektů BLOB](../../storage/blobs/soft-delete-blob-overview.md) , které vám pomůžou obnovit data v případě, že je omylem upravována nebo odstraněna aplikací nebo jiným uživatelem účtu úložiště.

Můžete vytvářet [snímky objektů BLOB](/rest/api/storageservices/creating-a-snapshot-of-a-blob). Snímek je verze objektu BLOB jen pro čtení, která je pořízena v určitém časovém okamžiku, a poskytuje způsob, jak zálohovat objekt BLOB. Po vytvoření snímku je možné ho číst, kopírovat nebo odstranit, ale nemění se.

> [!Note]
> Pro starší verze místních distribucí systému Hadoop, které nemají certifikát "wasbs", je nutné je importovat do úložiště důvěryhodnosti Java.

K importu certifikátů do úložiště důvěryhodnosti Java můžete použít následující metody:

Stažení certifikátu Azure Blob TLS/SSL do souboru

```bash
echo -n | openssl s_client -connect <storage-account>.blob.core.windows.net:443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > Azure_Storage.cer
```

Naimportujte výše uvedený soubor do úložiště důvěryhodnosti Java na všech uzlech.

```bash
keytool -import -trustcacerts -keystore /path/to/jre/lib/security/cacerts -storepass changeit -noprompt -alias blobtrust -file Azure_Storage.cer
```

Ověřte, že přidaný certifikát je v úložišti důvěryhodnosti.

```bash
keytool -list -v -keystore /path/to/jre/lib/security/cacerts
```

Další informace najdete v následujících článcích:

- [Použití Azure Storage s clustery Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Cíle škálovatelnosti pro účty úložiště úrovně Standard](../../storage/common/scalability-targets-standard-account.md)
- [Škálovatelnost a výkonnostní cíle pro úložiště objektů BLOB](../../storage/blobs/scalability-targets.md)
- [Kontrolní seznam pro výkon a škálovatelnost služby Microsoft Azure Storage](../../storage/blobs/storage-performance-checklist.md)
- [Monitorování, diagnostika a řešení problémů s Microsoft Azure Storage](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)
- [Monitorování účtu úložiště na webu Azure Portal](../../storage/common/manage-storage-analytics-logs.md)

### <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 implementuje model řízení přístupu k stylu aplikace HDFS a POSIX. Poskytuje pro detailní řízení přístupu prvotřídní integraci třídy se službou Azure AD. Neexistují žádná omezení velikosti dat, která může uložit, nebo schopnost spouštět výkonné paralelní analýzy.

Další informace najdete v následujících článcích:

- [Vytváření clusterů HDInsight s Data Lake Storage Gen1 pomocí Azure Portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
- [Použití Data Lake Storage Gen1 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen1.md)

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 je nejnovější nabídka úložiště. Sjednocuje základní funkce od první generace Azure Data Lake Storage Gen1 s koncovým bodem systému souborů kompatibilním s Hadoop přímo integrovanými do Azure Blob Storage. Toto vylepšení kombinuje výhody škálování a nákladů na úložiště objektů s spolehlivostí a výkonem obvykle přidruženou pouze k místním systémům souborů.

Azure Data Lake Storage Gen 2 je postavené na [službě Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md) a umožňuje rozhraní s daty pomocí systémů souborů i úložišť objektů. Funkce z [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml), jako jsou sémantika systému souborů, zabezpečení na úrovni souborů a škálování, jsou kombinovány s nízkými náklady, vrstvenými úložištěm, vysoce dostupnými možnostmi zotavení po havárii a rozsáhlým EKOSYSTÉMEM sady SDK/nástrojů ze služby [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md). V Data Lake Storage Gen2 všechny kvality úložiště objektů zůstanou při přidávání výhod rozhraní systému souborů optimalizovaného pro analytické úlohy.

Základní funkcí Data Lake Storage Gen2 je přidání [hierarchického oboru názvů](../../storage/blobs/data-lake-storage-namespace.md) ke službě BLOB Storage, která uspořádá objekty a soubory do hierarchie adresářů pro účely přístupu k datům. Hierarchická struktura umožňuje operace, jako je například přejmenování nebo odstranění adresáře, aby byly jednou atomické operace s metadaty v adresáři, nikoli vytváření výčtu a zpracování všech objektů, které sdílejí předponu názvu adresáře.

V minulosti se cloudové analýzy musely napadnout v oblasti výkonu, správy a zabezpečení. Klíčové funkce Azure Data Lake Storage (ADLS) Gen2 jsou následující:

- **Přístup** s platformou hadoop: Azure Data Lake Storage Gen2 umožňuje správu a přístup k datům stejně, jako byste použili [systém souborů DFS (DISTRIBUTED File System) Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Nový [ovladač ABFS](../../storage/blobs/data-lake-storage-abfs-driver.md) je dostupný ve všech Apache Hadoop prostředích, která jsou součástí [Azure HDInsight](../index.yml). Tento ovladač vám umožní získat přístup k datům uloženým v Data Lake Storage Gen2.

- **Nadmnožina oprávnění POSIX**: model zabezpečení pro data Lake Gen2 plně podporuje oprávnění ACL a POSIX spolu s některými dalšími podrobnostmi, které jsou specifické pro data Lake Storage Gen2. Nastavení lze nakonfigurovat prostřednictvím nástrojů pro správu nebo prostřednictvím architektury, jako je například podregistr a Spark.

- **Cenově výhodné**: Data Lake Storage Gen2 funkce s nízkými náklady na kapacitu úložiště a transakce. Když se data pohybují prostřednictvím celého životního cyklu, změna sazeb se změní na minimalizaci nákladů prostřednictvím integrovaných funkcí, jako je [životní cyklus úložiště objektů BLOB v Azure](../../storage/blobs/storage-lifecycle-management-concepts.md).

- **Spolupracuje s nástroji, architekturami a aplikacemi služby Blob Storage**: Data Lake Storage Gen2 nadále pracovat se spoustou nástrojů, platforem a aplikací, které existují v dnešní době pro úložiště objektů BLOB.

- **Optimalizovaný ovladač**: ovladač systému souborů objektů BLOB v Azure (ABFS) je [optimalizován speciálně](../../storage/blobs/data-lake-storage-abfs-driver.md) pro analýzu velkých objemů dat. Odpovídající rozhraní REST API se procházejí prostřednictvím koncového bodu DFS dfs.core.windows.net.

Pro přístup k datům, která jsou uložená v ADLS Gen2, se dá použít jeden z následujících formátů:
- `abfs:///`: Přístup k výchozímu Data Lake Storage clusteru.
- `abfs://file_system@account_name.dfs.core.windows.net`: Používá se při komunikaci s jiným než výchozím Data Lake Storage.

Další informace najdete v následujících článcích:

- [Úvod do Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md)
- [Ovladač systému souborů objektů BLOB v Azure (ABFS.md)](../../storage/blobs/data-lake-storage-abfs-driver.md)
- [Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="secure-azure-storage-keys-within-on-premises-hadoop-cluster-configuration"></a>Zabezpečení klíčů Azure Storage v rámci konfigurace místního clusteru Hadoop

Azure Storage klíčů, které jsou přidány do konfiguračních souborů Hadoop, navažte spojení mezi místními HDFS a úložištěm objektů BLOB v Azure. Tyto klíče lze chránit šifrováním pomocí architektury rozhraní Hadoop pro poskytovatele pověření. Po zašifrování je můžete bezpečně ukládat a přistupovat.

**Postup při zřizování přihlašovacích údajů:**

```bash
hadoop credential create fs.azure.account.key.account.blob.core.windows.net -value <storage key> -provider jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks/file
```

**Postup přidání výše uvedené cesty poskytovatele do core-site.xml nebo do konfigurace Ambari v části vlastní Core-site:**

```xml
<property>
    <name>hadoop.security.credential.provider.path</name>
        <value>
        jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks
        </value>
    <description>Path to interrogate for protected credentials.</description>
</property>
```

> [!Note]
> Do příkazového řádku Distcp se dá přidat taky vlastnost cesta zprostředkovatele místo uložení klíče na úrovni clusteru core-site.xml takto:

```bash
hadoop distcp -D hadoop.security.credential.provider.path=jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks /user/user1/ wasb:<//yourcontainer@youraccount.blob.core.windows.net/>user1
```

## <a name="restrict-azure-storage-data-access-using-sas"></a>Omezení přístupu k datům Azure Storage pomocí SAS

Ve výchozím nastavení má služba HDInsight úplný přístup k datům v účtech Azure Storage přidružených ke clusteru. Sdílené přístupové podpisy (SAS) na kontejneru objektů blob můžete použít k omezení přístupu k datům, jako je například poskytnutí přístupu k datům uživatelům s oprávněním jen pro čtení.

### <a name="using-the-sas-token-created-with-python"></a>Použití tokenu SAS vytvořeného v Pythonu

1. Otevřete soubor  [SASToken.py](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature/blob/master/Python/SASToken.py) a změňte následující hodnoty:

    |Vlastnost token|Description|
    |---|---|
    |policy_name|Název, který se má použít pro vytvoření uložené zásady.|
    |storage_account_name|Název vašeho účtu úložiště.|
    |storage_account_key|Klíč pro účet úložiště|
    |storage_container_name|Kontejner v účtu úložiště, ke kterému chcete omezit přístup|
    |example_file_path|Cesta k souboru, který je odeslán do kontejneru.|

2. Soubor SASToken.py se dodává s `ContainerPermissions.READ + ContainerPermissions.LIST` oprávněními a dá se upravit na základě případu použití.

3. Skript spusťte následujícím způsobem: `python SASToken.py`

4. Po dokončení skriptu se zobrazí token SAS podobný následujícímu textu: `sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14`

5. Pokud chcete omezit přístup k kontejneru se sdíleným přístupovým podpisem, přidejte vlastní položku do Konfigurace základního serveru pro cluster v části Ambari HDFS config Advanced Custom-site Add Property.

6. Pro pole **klíč** a **hodnota** použijte následující hodnoty:

    **Key**: `fs.azure.sas.YOURCONTAINER.YOURACCOUNT.blob.core.windows.net` **hodnota**: klíč SAS vrácený aplikací Pythonu z kroku 4 výše.

7. Kliknutím na tlačítko **Přidat** uložte tento klíč a hodnotu a potom kliknutím na tlačítko **Uložit** uložte změny konfigurace. Po zobrazení výzvy přidejte popis změny (například "Přidání přístupu k úložišti SAS") a pak klikněte na **Uložit**.

8. Ve webovém uživatelském rozhraní Ambari vyberte ze seznamu na levé straně HDFS a pak vyberte **restartovat všechny ovlivněné** v rozevíracím seznamu akce služby na pravé straně. Po zobrazení výzvy vyberte **Potvrdit restartování vše**.

9. Tento postup opakujte pro MapReduce2 a PŘÍZe.

Existují tři důležité věci, jak si pamatovat v používání tokenů SAS v Azure:

1. Pokud jsou tokeny SAS vytvořené pomocí oprávnění číst a seznam, uživatelé, kteří přistupují k kontejneru objektů BLOB s tímto tokenem SAS, nebudou moct zapisovat a odstraňovat data. Uživatelé, kteří přistupují k kontejneru objektů BLOB pomocí tohoto tokenu SAS a operaci zápisu nebo odstranění, obdrží zprávu, jako je `"This request is not authorized to perform this operation"` .

2. Pokud jsou tokeny SAS generovány s `READ + LIST + WRITE` oprávněním ( `DELETE` pouze pro omezení), příkazy, jako `hadoop fs -put` je například zápis do `\_COPYING\_` souboru, a poté pokus o přejmenování souboru. Tato operace HDFS se mapuje na `copy+delete` pro WASB. Vzhledem k `DELETE` tomu, že oprávnění nebylo zadáno, "Put" selže. `\_COPYING\_`Operace je funkce Hadoop určená k poskytnutí nějakého řízení souběžnosti. V současné době neexistuje způsob, jak omezit pouze operaci "odstranit", aniž by to ovlivnilo i "WRITE" operace.

3. Poskytovatel pověření Hadoop a zprostředkovatel dešifrovacího klíče (ShellDecryptionKeyProvider) v současné době nepracují s tokeny SAS, takže v současné době není možné je chránit před viditelností.

Další informace najdete v tématu [použití podpisů sdíleného přístupu Azure Storage k omezení přístupu k datům v HDInsight](../hdinsight-storage-sharedaccesssignature-permissions.md).

## <a name="use-data-encryption-and-replication"></a>Použití šifrování a replikace dat

Všechna data zapsaná do Azure Storage se automaticky šifrují pomocí [šifrování služby Storage (SSE)](../../storage/common/storage-service-encryption.md). Data v účtu Azure Storage jsou vždy replikována pro zajištění vysoké dostupnosti. Když vytváříte účet úložiště, můžete vybrat jednu z následujících možností replikace:

- [Místně redundantní úložiště (LRS)](../../storage/common/storage-redundancy.md#locally-redundant-storage)
- [Zónově redundantní úložiště (ZRS)](../../storage/common/storage-redundancy.md#zone-redundant-storage)
- [Geograficky redundantní úložiště (GRS)](../../storage/common/storage-redundancy.md#geo-redundant-storage)
- [Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)](../../storage/common/storage-redundancy.md)

Azure Storage poskytuje místně redundantní úložiště (LRS), ale měli byste také zkopírovat kritická data do jiného účtu Azure Storage v jiné oblasti s frekvencí, která je zarovnaná na požadavky plánu zotavení po havárii. Existují různé metody kopírování dat, včetně [ADLCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md), [DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html), [Azure PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md)nebo [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md). Doporučuje se také vyhovět zásadám přístupu pro účet Azure Storage, abyste zabránili nechtěnému odstranění.

Další informace najdete v následujících článcích:

- [Účet replikace Azure Storage](../../storage/common/storage-redundancy.md)
- [Pokyny k havárii pro Azure Data Lake Storage Gen1 (ADLS)](../../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

## <a name="attach-additional-azure-storage-accounts-to-cluster"></a>Připojení dalších účtů Azure Storage ke clusteru

Během procesu vytváření služby HDInsight je jako výchozí systém souborů zvolen účet Azure Storage, Azure Data Lake Storage Gen1 nebo Azure Data Lake Storage Gen2. Kromě tohoto výchozího účtu úložiště můžete během procesu vytváření clusteru nebo po vytvoření clusteru přidat do stejného předplatného Azure nebo různých předplatných Azure další účty úložiště.

Další účet úložiště je možné přidat jedním z následujících způsobů:
- Ambari HDFS config Advanced Custom Core-site přidejte název účtu úložiště a klíč pro restartování služeb.
- Použití [akce skriptu](../hdinsight-hadoop-add-storage.md) předáním názvu a klíče účtu úložiště

> [!Note]
> V platných případech použití se limity úložiště Azure dají zvýšit prostřednictvím žádosti o [podporu Azure](https://azure.microsoft.com/support/faq/).

Další informace najdete v tématu [Přidání dalších účtů úložiště do služby HDInsight](../hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Další kroky

Přečtěte si další článek v této sérii: [osvědčené postupy migrace dat do místní migrace Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-data-migration.md).