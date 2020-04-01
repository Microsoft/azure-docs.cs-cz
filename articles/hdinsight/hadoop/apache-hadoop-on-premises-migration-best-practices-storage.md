---
title: 'Úložiště: Migrace místního Apache Hadoop do Azure HDInsight'
description: Seznamte se s doporučenými postupy úložiště pro migraci místních clusterů Hadoop do Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/10/2019
ms.openlocfilehash: f19d4adad675cdf95f59aca0f752f46211b75e8f
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436917"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight"></a>Migrace místních clusterů Apache Hadoop do Azure HDInsight

Tento článek obsahuje doporučení pro ukládání dat v systémech Azure HDInsight. Je součástí řady, která poskytuje osvědčené postupy, které vám pomohou s migrací místních systémů Apache Hadoop do Azure HDInsight.

## <a name="choose-right-storage-system-for-hdinsight-clusters"></a>Výběr správného úložného systému pro clustery HDInsight

Místní adresářová struktura systému Souborů Apache Hadoop (HDFS) se dá znovu vytvořit v Azure Storage nebo Azure Data Lake Storage. Potom můžete bezpečně odstranit clustery HDInsight, které se používají pro výpočty bez ztráty uživatelských dat. Obě služby lze použít jako výchozí systém souborů i jako další systém souborů pro cluster HDInsight. Cluster HDInsight a účet úložiště musí být hostované ve stejné oblasti.

### <a name="azure-storage"></a>Azure Storage

Clustery HDInsight můžou použít kontejner objektů blob ve službě Azure Storage jako výchozí systém souborů nebo jako další souborový systém.Účet úložiště úrovně Standard je podporován pro použití s clustery HDInsight. Úroveň Premier není podporována. Výchozí kontejner objektu blob ukládá konkrétní informace, jako je historie úlohy a protokoly.Sdílení jednoho kontejneru objektů blob jako výchozího systému souborů pro více clusterů není podporováno.

Účty úložiště, které jsou definovány v procesu `%HADOOP_HOME%/conf/core-site.xml` vytváření a jejich příslušné klíče jsou uloženy v uzlech clusteru. Mohou být také přístupné v části "Vlastní základní lokalita" v konfiguraci HDFS v uzdu Ambari. Klíč účtu úložiště je ve výchozím nastavení zašifrován a vlastní dešifrovací skript se používá k dešifrování klíčů před předáním daemonům Hadoop. Úlohy včetně Hive, MapReduce, Hadoop streaming a Pig, nesou s sebou popis účtů úložiště a metadat.

Azure Storage může být geograficky replikované. Přestože geografická replikace poskytuje geografické obnovení a redundanci dat, převzetí služeb při selhání geograficky replikované umístění vážně ovlivňuje výkon a může vzniknout další náklady. Doporučujeme zvolit geografickou replikaci moudře a pouze v případě, že hodnota dat stojí za dodatečné náklady.

Jeden z následujících formátů lze použít pro přístup k datům uloženým ve službě Azure Storage:

|Formát přístupu k datům |Popis |
|---|---|
|`wasb:///`|Získejte přístup k výchozímu úložišti pomocí nešifrované komunikace.|
|`wasbs:///`|Získejte přístup k výchozímu úložišti pomocí šifrované komunikace.|
|`wasb://<container-name>@<account-name>.blob.core.windows.net/`|Používá se při komunikaci s nevýchozím účtem úložiště. |

[Cíle škálovatelnosti pro účty standardního úložiště](../../storage/common/scalability-targets-standard-account.md) uvádějí aktuální limity na účty Azure Storage. Pokud potřeby aplikace překročí cíle škálovatelnosti jednoho účtu úložiště, aplikace může být vytvořena tak, aby používala více účtů úložiště a pak rozdělila datové objekty mezi těmito účty úložiště.

[Azure Storage Analytics](../../storage/storage-analytics.md) poskytuje metriky pro všechny služby úložiště a Azure Portal lze nakonfigurovat sbírat metriky, které mají být vizualizovány prostřednictvím grafů. Výstrahy lze vytvořit upozornit, pokud byly dosaženy prahové hodnoty pro metriky prostředků úložiště.

Azure Storage offers [soft delete for blob objects](../../storage/blobs/storage-blob-soft-delete.md) to help recover data when it's accidentally modified or deleted by an application or other storage account user.

Můžete vytvořit [snímky objektů blob](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob). Snímek je verze objektu blob jen pro čtení, který je přijata v určitém okamžiku a poskytuje způsob, jak zálohovat objekt blob. Po vytvoření snímku jej lze číst, kopírovat nebo odstraňovat, ale nelze jej upravovat.

> [!Note]
> Pro starší verze místních distribuce Hadoop, které nemají certifikát "wasbs", je třeba je importovat do úložiště důvěryhodnosti Java.

K importu certifikátů do úložiště důvěryhodných certifikátů java lze použít následující metody:

Stažení certifikátu Azure Blob TLS/SSL do souboru

```bash
echo -n | openssl s_client -connect <storage-account>.blob.core.windows.net:443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > Azure_Storage.cer
```

Import výše uvedeného souboru do úložiště důvěryhodnosti Java na všech uzlech

```bash
keytool -import -trustcacerts -keystore /path/to/jre/lib/security/cacerts -storepass changeit -noprompt -alias blobtrust -file Azure_Storage.cer
```

Ověřte, zda je přidaný certifikát v úložišti důvěryhodnosti.

```bash
keytool -list -v -keystore /path/to/jre/lib/security/cacerts
```

Další informace najdete v těchto článcích:

- [Použití Azure Storage s clustery Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Cíle škálovatelnosti pro standardní účty úložiště](../../storage/common/scalability-targets-standard-account.md)
- [Škálovatelnost a výkonnostní cíle pro úložiště objektů Blob](../../storage/blobs/scalability-targets.md)
- [Kontrolní seznam pro výkon a škálovatelnost služby Microsoft Azure Storage](../../storage/common/storage-performance-checklist.md)
- [Monitorování, diagnostika a řešení problémů s Microsoft Azure Storage](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)
- [Monitorování účtu úložiště na webu Azure Portal](../../storage/common/storage-monitor-storage-account.md)

### <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Azure Data Lake Storage implementuje model řízení přístupu ve stylu HDFS a POSIX. Poskytuje prvotřídní integraci s AAD pro jemně odstupňované řízení přístupu. Velikost dat, která může ukládat, ani jejich schopnost spouštět masivně paralelní analýzy nejsou nijak limitována.

Další informace najdete v těchto článcích:

- [Vytváření clusterů HDInsight pomocí úložiště datových jezer pomocí portálu Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
- [Použití úložiště datových jezer s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md)

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 je nejnovější nabídka úložiště. Sjednocuje základní funkce z první generace Azure Data Lake Storage s koncovým bodem systému souborů kompatibilním s Hadoopem, který je přímo integrován do úložiště objektů blob Azure. Toto vylepšení kombinuje výhody škálování a nákladů úložiště objektů se spolehlivostí a výkonem, které jsou obvykle přidruženy pouze k místním systémům souborů.

ADLS Gen 2 je postavenna nad [úložiště objektů blob Azure](../../storage/blobs/storage-blobs-introduction.md) a umožňuje rozhraní s daty pomocí paradigmat úložiště souborů a objektů. Funkce z [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml), jako je sémantika systému souborů, zabezpečení na úrovni souborů a škálování, jsou kombinovány s levným, vrstveným úložištěm, vysokou dostupností/zotavením po havárii a rozsáhlým ekosystémem SDK/nástrojů z úložiště objektů [Blob Azure](../../storage/blobs/storage-blobs-introduction.md). V data lake storage gen2 zůstávají všechny vlastnosti úložiště objektů a přidávají výhody rozhraní systému souborů optimalizované pro analytické úlohy.

Základní funkcí Data Lake Storage Gen2 je přidání [hierarchického oboru názvů](../../storage/data-lake-storage/namespace.md) do služby úložiště objektů Blob, která organizuje objekty nebo soubory do hierarchie adresářů pro přístup k výkonným datům.Hierarchická struktura umožňuje operacím, jako je přejmenování nebo odstranění adresáře, aby byly operace s jedním atomickými metadaty v adresáři, nikoli výčet a zpracování všech objektů, které sdílejí předponu názvu adresáře.

V minulosti musely cloudové analýzy dělat kompromisy v oblastech výkonu, správy a zabezpečení. Klíčové funkce Azure Data Lake Storage (ADLS) Gen2 jsou následující:

- **Přístup kompatibilní s Hadoopem**: Azure Data Lake Storage Gen2 umožňuje spravovat data a přistupovat k nim stejně jako u [hadoopského distribuovaného souborového systému (HDFS).](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) Nový  [ovladač ABFS](../../storage/data-lake-storage/abfs-driver.md)je k dispozici ve všech prostředích Apache Hadoop, které jsou součástí [Azure HDInsight](../index.yml). Tento ovladač umožňuje přístup k datům uloženým v datovém úložišti dat Gen2.

- **Nadmnožina oprávnění POSIX**: Model zabezpečení pro Data Lake Gen2 plně podporuje oprávnění ACL a POSIX spolu s některými extra rozlišovací schopnost specifické pro Data Lake Storage Gen2. Nastavení může být nakonfigurováno prostřednictvím nástrojů pro správu nebo prostřednictvím architektur, jako je Hive a Spark.

- **Nákladově efektivní**: Data Lake Storage Gen2 nabízí nízkonákladovou úložnou kapacitu a transakce. S přechodem dat prostřednictvím celého životního cyklu se sazby za fakturaci mění, aby se minimalizovaly náklady prostřednictvím integrovaných funkcí, jako je [životní cyklus úložiště objektů Blob Azure](../../storage/common/storage-lifecycle-management-concepts.md).

- **Pracuje s nástroji, architekturami a aplikacemi pro ukládání objektů Blob**: Data Lake Storage Gen2 pokračuje v práci s širokou škálou nástrojů, architektur a aplikací, které dnes existují pro úložiště objektů Blob.

- **Optimalizovaný ovladač**: Ovladač souborového systému objektů blob Azure (ABFS) je [optimalizovaný speciálně](../../storage/data-lake-storage/abfs-driver.md) pro analýzu velkých objemů dat. Odpovídající REST API jsou konsurfaced prostřednictvím koncového bodu dfs, dfs.core.windows.net.

Jeden z následujících formátů lze použít pro přístup k datům uloženým v ADLS Gen2:
- `abfs:///`: Přístup k výchozímu úložišti datových jezer pro cluster.
- `abfs://file_system@account_name.dfs.core.windows.net`: Používá se při komunikaci s nevýchozím úložištěm datových jezer.

Další informace najdete v těchto článcích:

- [Úvod do úložiště datových jezer Azure Gen2](../../storage/data-lake-storage/introduction.md)
- [Ovladač souborového systému objektů blob Azure (ABFS.md)](../../storage/data-lake-storage/abfs-driver.md)
- [Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="secure-azure-storage-keys-within-on-premises-hadoop-cluster-configuration"></a>Zabezpečené klíče úložiště Azure v rámci místní konfigurace clusteru Hadoop

Klíče azure storage, které se přidají do konfiguračních souborů Hadoop, navazují připojení mezi místně HDFS a úložištěm objektů blob Azure. Tyto klíče lze chránit jejich šifrováním pomocí architektury zprostředkovatele pověření Hadoop. Po zašifrování mohou být bezpečně uloženy a přístupné.

**Zřízení pověření:**

```bash
hadoop credential create fs.azure.account.key.account.blob.core.windows.net -value <storage key> -provider jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks/file
```

**Chcete-li přidat výše uvedenou cestu zprostředkovatele do souboru core-site.xml nebo do konfigurace Ambari pod vlastním jádrem:**

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
> Vlastnost cesta zprostředkovatele lze také přidat do příkazového řádku distcp namísto ukládání klíče na úrovni clusteru na adrese core-site.xml následujícím způsobem:

```bash
hadoop distcp -D hadoop.security.credential.provider.path=jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks /user/user1/ wasb:<//yourcontainer@youraccount.blob.core.windows.net/>user1
```

## <a name="restrict-azure-storage-data-access-using-sas"></a>Omezení přístupu k datům azure úložiště pomocí SAS

HDInsight má ve výchozím nastavení úplný přístup k datům v účtech Azure Storage přidružených ke clusteru. Sdílené přístupové podpisy (SAS) v kontejneru objektů blob lze omezit přístup k datům, jako je například poskytnout uživatelům přístup jen pro čtení k datům.

### <a name="using-the-sas-token-created-with-python"></a>Použití tokenu SAS vytvořeného v pythonu

1. Otevřete [soubor SASToken.py](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature/blob/master/Python/SASToken.py) a změňte následující hodnoty:

    |Vlastnost tokenu|Popis|
    |---|---|
    |policy_name|Název, který chcete použít pro uložené zásady k vytvoření.|
    |storage_account_name|Název vašeho účtu úložiště.|
    |storage_account_key|Klíč pro účet úložiště.|
    |storage_container_name|Kontejner v účtu úložiště, ke kterému chcete omezit přístup.|
    |example_file_path|Cesta k souboru, který je odeslán do kontejneru.|

2. Soubor SASToken.py je `ContainerPermissions.READ + ContainerPermissions.LIST` dodáván s oprávněními a lze jej upravit na základě případu použití.

3. Spusťte skript takto:`python SASToken.py`

4. Po dokončení skriptu se zobrazí token SAS podobný následujícímu textu:`sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14`

5. Chcete-li omezit přístup ke kontejneru se sdíleným přístupovým podpisem, přidejte vlastní položku do konfigurace hlavní lokality pro cluster v části Ambari HDFS Configs Advanced Custom core-site Add vlastnost.

6. Pro pole **Klíč** a **Hodnota** použijte následující hodnoty:

    **Klíč** `fs.azure.sas.YOURCONTAINER.YOURACCOUNT.blob.core.windows.net` : **Hodnota:** Klíč SAS vrácený aplikací Pythonu from step 4 výše.

7. Kliknutím na tlačítko **Přidat** uložte tento klíč a hodnotu a klepnutím na tlačítko **Uložit** uložte změny konfigurace. Po zobrazení výzvy přidejte popis změny (například přidání přístupu k úložišti SAS) a klepněte na tlačítko **Uložit**.

8. V uživatelském rozhraní webu Ambari vyberte hdfs ze seznamu vlevo a pak v rozevíracím seznamu Akce služby vpravo vyberte **Restartovat všechny ovlivněné.** Po zobrazení výzvy vyberte **Potvrdit restartovat vše**.

9. Tento postup opakujte pro MapReduce2 a YARN.

O používání tokenů SAS v Azure si můžete pamatovat tři důležité věci:

1. Při vytvoření tokenů SAS s oprávněními "READ + LIST", uživatelé, kteří přistupují ke kontejneru objektů blob s tímto tokenem SAS, nebudou moci "zapisovat a odstraňovat" data. Uživatelé, kteří přistupují ke kontejneru objektů Blob pomocí tohoto `"This request is not authorized to perform this operation"`tokenu SAS a pokusí se o operaci zápisu nebo odstranění, obdrží zprávu jako .

2. Když jsou tokeny SAS `READ + LIST + WRITE` generovány s `DELETE` oprávněními (pouze omezit), příkazy, jako je `hadoop fs -put` první zápis do souboru `\_COPYING\_` a zkuste přejmenovat soubor. Tato operace HDFS `copy+delete` mapuje na pro WASB. Vzhledem `DELETE` k tomu, že povolení nebylo poskytnuto, "put" by se nezdaří. Operace `\_COPYING\_` je hadoop funkce určené k poskytování některé řízení souběžnosti. V současné době neexistuje žádný způsob, jak omezit pouze "DELETE" operace bez ovlivnění "WRITE" operace stejně.

3. Bohužel poskytovatel pověření hadoop a zprostředkovatel dešifrovacího klíče (ShellDecryptionKeyProvider) v současné době nepracují s tokeny SAS, a proto v současné době nelze chránit před viditelností.

Další informace najdete [v tématu Použití sdílených přístupových podpisů služby Azure Storage k omezení přístupu k datům ve službě HDInsight](../hdinsight-storage-sharedaccesssignature-permissions.md).

## <a name="use-data-encryption-and-replication"></a>Použití šifrování a replikace dat

Všechna data zapsaná do Služby Azure Se automaticky zašifrují pomocí [šifrování služby Storage Service Encryption (SSE).](../../storage/common/storage-service-encryption.md) Data v účtu Azure Storage se vždy replikují pro vysokou dostupnost.Při vytváření účtu úložiště můžete zvolit jednu z následujících možností replikace:

- [Místně redundantní úložiště (LRS)](../../storage/common/storage-redundancy-lrs.md)
- [Zónově redundantní úložiště (ZRS)](../../storage/common/storage-redundancy-zrs.md)
- [Geograficky redundantní úložiště (GRS)](../../storage/common/storage-redundancy-grs.md)
- [Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)](../../storage/common/storage-redundancy.md)

Azure Data Lake Storage poskytuje místně redundantní úložiště (LRS), ale měli byste také zkopírovat důležitá data do jiného účtu úložiště datového jezera v jiné oblasti s frekvencí v souladu s potřebami plánu zotavení po havárii.Existují různé metody kopírování dat, včetně [ADLCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md), [DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html), [Azure PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md)nebo [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md).Doporučujeme také vynutit zásady přístupu pro účet úložiště Datové ho jezera, aby se zabránilo náhodnému odstranění.

Další informace najdete v těchto článcích:

- [Replikace úložiště Azure](../../storage/common/storage-redundancy.md)
- [Pokyny pro katastrofy pro Azure Data Lake Storage (ADLS)](../../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

## <a name="attach-additional-azure-storage-accounts-to-cluster"></a>Připojení dalších účtů Úložiště Azure ke clusteru

Během procesu vytváření HDInsight se jako výchozí souborový systém volí účet Azure Storage nebo Účet Azure Data Lake Storage. Kromě tohoto výchozího účtu úložiště lze přidat další účty úložiště ze stejného předplatného Azure nebo různých předplatných Azure během procesu vytváření clusteru nebo po vytvoření clusteru.

Další účet úložiště lze přidat jedním z následujících způsobů:
- Ambari HDFS Config Advanced Custom core-site: Přidání názvu účtu úložiště a klíče Restartování služeb
- Použití [akce Skript](../hdinsight-hadoop-add-storage.md) předáním názvu a klíče účtu úložiště

> [!Note]
> V platných případech použití lze limity úložiště Azure zvýšit prostřednictvím požadavku na [podporu Azure](https://azure.microsoft.com/support/faq/).

Další informace najdete v tématu [Přidání dalších účtů úložiště do služby HDInsight](../hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Další kroky

Přečtěte si další článek v této sérii: [Doporučené postupy pro migraci dat pro místní migraci Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-data-migration.md).
