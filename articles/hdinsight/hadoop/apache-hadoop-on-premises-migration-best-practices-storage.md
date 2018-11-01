---
title: Migrace s místními clustery systému Apache Hadoop do HDInsight Azure - storage osvědčené postupy
description: Přečtěte si osvědčené postupy úložiště migrace místních Hadoop clusterů pro Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 4f4aedd1d85a83e6f55d5729b82b88e2e9e8c00d
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50415929"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---storage-best-practices"></a>Migrace s místními clustery systému Apache Hadoop do HDInsight Azure - storage osvědčené postupy

Tento článek obsahuje doporučení pro ukládání dat v Azure HDInsight systémy. To je součástí série, která poskytuje osvědčené postupy pro pomoc s migrací místních systémů Apache Hadoop do Azure HDInsight.

## <a name="choose-the-right-storage-system-for-hdinsight-clusters"></a>Zvolte systém úložiště pro clustery HDInsight

Místní adresářovou strukturu Apache Hadoop File System (HDFS) je znovu vytvořit v Azure Storage nebo Azure Data Lake Storage. Pak můžete bezpečně odstranit clusterů HDInsight, které jsou používány pro výpočty, aniž by se ztratila uživatelská data. Obě služby může sloužit jako výchozí systém souborů a systém další souborů pro HDInsight cluster. HDInsight cluster a účet úložiště musí být hostovaný ve stejné oblasti.

### <a name="azure-storage"></a>Úložiště Azure

Clustery HDInsight můžete použít kontejner objektů blob ve službě Azure Storage jako výchozí systém souborů nebo systém další soubor. Účet úložiště úrovně Standard podporuje pro použití s clustery HDInsight. Premier úrovně se nepodporuje. Výchozí kontejner objektu blob ukládá konkrétní informace, jako je historie úlohy a protokoly. Sdílení jednoho kontejneru objektů blob jako výchozího systému souborů pro několik clusterů se nepodporuje.

Účty úložiště, které jsou definovány v procesu vytváření a jejich klíče jsou uložené v `%HADOOP_HOME%/conf/core-site.xml` na uzlech clusteru. Jsou také dostupné v sekci "Vlastní základní lokality" v HDFS konfiguraci v Uživatelském rozhraní Ambari. Ve výchozím nastavení je šifrovaný klíč účtu úložiště a dešifrování vlastní skript slouží k dešifrování klíče před předáním Hadoop procesy démon. Úlohy, včetně Hive, MapReduce, streamování Hadoop a Pig, obsahovat popis účtů úložiště a spojených metadat.

Úložiště Azure může být geograficky replikovaný. I když georeplikace zajišťuje geografické obnovení a redundanci dat, převzetí služeb při selhání do geograficky replikovaného umístění vážně ovlivňuje výkon, a to může způsobit dodatečné náklady. Doporučuje se zvolit geografickou replikaci dobře a pouze pokud je hodnota dat. Další vyplatí.

Pro přístup k datům uloženým ve službě Azure Storage se dá jednu z následujících formátů:

- `wasb:///`: Výchozí úložiště s přístupem pomocí nešifrovaná komunikace.
- `wasbs:///`: Výchozí úložiště s přístupem pomocí šifrovanou komunikaci.
- `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Používá se při komunikaci s účtem služby storage jiné než výchozí. 

[Azure Storage škálovatelnost a cíle výkonnosti](../../storage/common/storage-scalability-targets.md) uvádí aktuální omezení pro účty úložiště Azure. Potřebám vaší aplikace překročení cíle škálovatelnosti z jednoho účtu úložiště, můžete aplikace sestavena pro použití více účtů úložiště a pak rozdělit na oddíly datové objekty mezi těmito účty úložiště.

[Azure Storage Analytics](../../storage/storage-analytics.md) poskytuje metriky pro všechny služby úložiště a webu Azure portal může být nakonfigurovaná shromažďovat metriky, které vizualizována prostřednictvím grafy. Výstrahy lze vytvořit upozornit, když bylo dosaženo prahové hodnoty pro metriky prostředků úložiště.

Azure Storage nabízí [obnovitelného odstranění pro objekty blob](../../storage/blobs/storage-blob-soft-delete.md) můžete obnovit data, když je náhodně změněného nebo odstraněného aplikaci nebo jiný uživatel účet úložiště.

Můžete vytvořit [snímků objektů blob](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob). Snímek je verze jen pro čtení objektu blob, která je provedena v bodě v čase a poskytuje způsob, jak zálohovat objekt blob. Po vytvoření snímku, lze je číst, zkopírovat, nebo odstranit, ale nedojde ke změně.

> [!Note]
> Pro starší verze místních místních Hadoop distribucí, který nemá certifikát "wasbs" musí být naimportovány do úložiště důvěryhodných Java.

Import certifikátů do úložiště důvěryhodných Java je možné následující metody:

Stáhněte si certifikát ssl objektů Blob v Azure do souboru

```bash
echo -n | openssl s_client -connect <storage-account>.blob.core.windows.net:443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > Azure_Storage.cer
```

Import výše uvedeného souboru do úložiště důvěryhodných Java na všech uzlech

```bash
keytool -import -trustcacerts -keystore /path/to/jre/lib/security/cacerts -storepass changeit -noprompt -alias blobtrust -file Azure_Storage.cer
```

Ověřte, zda je přidání certifikátu do úložiště důvěryhodných

```bash
keytool -list -v -keystore /path/to/jre/lib/security/cacerts
```

Další informace najdete v následujících článcích:

- [Použití služby Azure storage s clustery Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Škálovatelnost a výkonnostní cíle Azure Storage](../../storage/common/storage-scalability-targets.md)
- [Kontrolní seznam pro výkon a škálovatelnost služby Microsoft Azure Storage](../../storage/common/storage-performance-checklist.md)
- [Monitorování, diagnostika a řešení problémů s Microsoft Azure Storage](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)
- [Monitorování účtu úložiště na webu Azure Portal](../../storage/common/storage-monitor-storage-account.md)

### <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Azure Data Lake Storage implementuje HDFS a POSIX model řízení přístupu style. Řízení přístupu na jemné jemněji nabízí prvotřídní integrace s AAD. Nejsou žádná omezení velikosti dat, která může ukládat a schopnosti spouštět výkonné paralelní analýzy.

Další informace najdete v následujících článcích:

- [Vytvoření clusterů HDInsight s Data Lake Storage pomocí webu Azure portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
- [Použití Data Lake Storage s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md)

### <a name="azure-data-lake-storage-gen2-preview"></a>Azure Data Lake Storage Gen2 (preview)

Azure Data Lake Storage Gen2 je nejnovější nabídky a je ve verzi Preview v době psaní tohoto dokumentu. Základní funkce z první generace služby Azure Data Lake Storage sjednocuje s koncovým bodem systém Hadoop kompatibilní soubor přímo integrované do Azure Blob Storage. Toto vylepšení kombinuje výhody škálování a náklady na úložiště objektů s spolehlivost a výkon, obvykle související jenom s místní systémy souborů.

2. generace ADLS je postavený na [úložiště objektů Blob v Azure](../../storage/blobs/storage-blobs-introduction.md) a umožňuje rozhraní s daty pomocí obou paradigmat souboru systému a objektu úložiště. Funkce z [Azure Data Lake Storage Gen1](../../data-lake-store/index.md), například sémantiku systému souborů, souboru úroveň zabezpečení a škálování se sloučí s vrstveného úložiště s nízkými náklady, funkcím pro obnovení vysoká dostupnost/zotavení po havárii a velké sady SDK a nástroje ekosystém z [úložiště objektů Blob v Azure](../../storage/blobs/storage-blobs-introduction.md). V Data Lake Storage Gen2 zůstanou všechny vlastnosti objektu úložiště při přidávání výhody rozhraní systému souborů optimalizované pro analýzy úloh.

Základní funkcí služby Data Lake Storage Gen2 je přidání [hierarchického oboru názvů](../../storage/data-lake-storage/namespace.md) do služby Blob storage, který slouží k uspořádání objektů nebo souborů do hierarchie adresářů pro přístup k datům výkonné. Hierarchické struktuře umožňuje operace, jako je přejmenování nebo odstranění adresáře, které mají být metadat jednotného atomické operace na adresář místo vytváření výčtů a zpracování všech objektů, které sdílejí předpona názvu adresáře.

V minulosti cloudové analýzy došlo k ohrožení v oblasti výkonu, správy a zabezpečení. Klíč funkce Gen2 Azure Data Lake Storage (ADLS) jsou následující:

- **Hadoop kompatibilní přístup**: Azure Data Lake Storage Gen2 umožňuje spravovat a přistupovat k datům, stejně jako při použití [souboru systému HDFS (Hadoop Distributed)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Nové [ABFS ovladač](../../storage/data-lake-storage/abfs-driver.md) je k dispozici ve všech prostředích Apache Hadoop, které jsou součástí [Azure HDInsight](../index.yml). Tento ovladač umožňuje přístup k datům uloženým v Data Lake Storage Gen2.

- **Nadmnožina POSIX oprávnění**: model zabezpečení pro Data Lake Gen2 plně podporuje oprávnění řízení přístupu a POSIX spolu s nějaké další specifické pro Data Lake Storage Gen2 členitosti. Nastavení může být nakonfigurován prostřednictvím správce nástroje nebo architektury, jako je Hive a Spark.

- **Nákladově efektivní**: Gen2 úložiště Data Lake nabízí kapacitu úložiště s nízkými náklady a transakce. Jako přechody data prostřednictvím jejich kompletní životní cyklus, změnit fakturační sazby minimalizovat náklady prostřednictvím integrované funkce, jako [životní cyklus úložiště objektů Blob v Azure](../../storage/common/storage-lifecycle-managment-concepts.md).

- **Funguje s Blob storage nástroje, platformy a aplikace**: Data Lake Storage Gen2 nadále využívat širokou škálu nástroje, platformy a aplikace, které existují ještě dnes pro úložiště objektů Blob.

- **Optimalizované ovladač**: ovladač systém souborů Azure Blob (ABFS) je [speciálně optimalizovaná](../../storage/data-lake-storage/abfs-driver.md) pro analýzy velkých objemů dat. Zobrazují se prostřednictvím koncového bodu systému souborů dfs, odpovídající rozhraní REST API dfs.core.windows.net.

Jeden z následujících formátů je možné získat přístup k datům, která je uložena v ADLS Gen2:
- `abfs:///`: Přístup k výchozí Data Lake Storage pro cluster.
- `abfs[s]://file_system@account_name.dfs.core.windows.net`: Používá se při komunikaci s úložištěm Data Lake jiné než výchozí.

Další informace najdete v následujících článcích:

- [Úvod do služby Azure Data Lake Storage Gen2 ve verzi Preview](../../storage/data-lake-storage/introduction.md)
- [Ovladač systému souborů Azure Blob (ABFS.md)](../../storage/data-lake-storage/abfs-driver.md)

## <a name="protect-azure-storage-key-visibility-within-the-on-premises-hadoop-cluster-configuration"></a>Ochrana klíčů viditelnost služby Azure Storage v rámci konfigurace v místním clusteru Hadoop

Klíče Azure storage, které jsou přidány do konfiguračních souborů Hadoop, spojení mezi na místní HDFS a Azure Blob storage. Tyto klíče se dají chránit pomocí šifrování pomocí rozhraní poskytovatele přihlašovacích údajů systému Hadoop. Jakmile zašifrovaný, může být ukládají a získávají bezpečně.

**Ke zřízení přihlašovací údaje:**

```bash
hadoop credential create fs.azure.account.key.account.blob.core.windows.net -value <storage key> -provider jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks/file
```

**Chcete-li přidat poskytovatele cestě core-site.xml nebo Ambari konfiguraci v části základní vlastní web:**

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
> Vlastnost path zprostředkovatele lze také přidat do příkazového řádku distcp místo uložení klíče na úrovni clusteru na core-site.xml následujícím způsobem:

```bash
hadoop distcp -D hadoop.security.credential.provider.path=jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks /user/user1/ wasb:<//yourcontainer@youraccount.blob.core.windows.net/>user1
```

## <a name="restrict-access-to-azure-storage-data-using-sas-signatures"></a>Omezení přístupu k datům služby Azure storage pomocí SAS podpisy

HDInsight ve výchozím nastavení má úplný přístup k datům v Azure Storage účty přidružené ke clusteru. Sdílených přístupových podpisů (SAS) na kontejner objektů blob můžete použít k omezení přístupu k datům, jako například poskytnout uživatelům s přístupem jen pro čtení k datům.

### <a name="using-the-sas-token-created-with-python"></a>Pomocí tokenu SAS, které jsou vytvořené pomocí pythonu

1. Otevřít [SASToken.py](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature/blob/master/Python/SASToken.py) soubor a změňte následující hodnoty:
    - název_zásad: název, který má použít pro uložené zásady k vytvoření.
    - název_účtu_úložiště: název účtu úložiště.
    - klíč_účtu_úložiště: klíč pro účet úložiště.
    - storage_container_name: kontejneru v účtu úložiště, který chcete omezit přístup k datům.
    - example_file_path: cesta k souboru, který se nahraje do kontejneru

2. Součástí souboru SASToken.py `ContainerPermissions.READ + ContainerPermissions.LIST` oprávnění a je možné upravit podle případu použití.

3. Spusťte skript následujícím způsobem: `python SASToken.py`

4. Po dokončení skriptu zobrazí token SAS, který je podobný následujícímu textu: `sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14`

5. K omezení přístupu ke kontejneru pomocí sdíleného přístupového podpisu, přidejte vlastní položky ke konfiguraci základního webu pro cluster pod Ambari HDFS Configs Advanced vlastní základní web přidat vlastnost.

6. Použijte následující hodnoty **klíč** a **hodnotu** pole:

    **Klíč**: `fs.azure.sas.YOURCONTAINER.YOURACCOUNT.blob.core.windows.net` **hodnotu**: The klíč SAS pro vrácený Python aplikace od kroku 4 výše.

7. Klikněte na tlačítko **přidat** tlačítko Uložit tento klíč a hodnotu a pak klikněte na tlačítko **Uložit** tlačítko Uložit změny konfigurace. Po zobrazení výzvy, přidejte popis změny ("Přidání přístup k úložišti SAS" příklad) a potom klikněte na **Uložit**.

8. V Ambari webového uživatelského rozhraní, HDFS vyberte ze seznamu na levé straně a pak vyberte **restartujte všechny ovlivněné** z akce služby rozevírací seznam na pravé straně. Po zobrazení výzvy vyberte **potvrďte restartování všech**.

9. Tento postup opakujte pro MapReduce2 a YARN.

Existují tři hlavně třeba si zapamatovat týkající se použití tokenů SAS v Azure:

1. Při vytváření tokenů SAS s oprávněními "ČÍST seznam +", uživatelé, kteří přístup ke kontejneru objektů Blob pomocí tohoto tokenu SAS, nebude možné "zapisovat a odstraňovat" data. Uživatelé, kteří přístup ke kontejneru objektů Blob pomocí SAS token a zkuste to zápis nebo odstranění operace, se zobrazí zpráva podobná `"This request is not authorized to perform this operation"`.

2. Po vygenerování tokenů SAS s `READ + LIST + WRITE` oprávnění (omezit `DELETE` pouze), příkazy jako `hadoop fs -put` nejprve zapisovat do `\_COPYING\_` souboru a pak zkuste přejmenovat soubor. Tato operace HDFS se mapuje `copy+delete` pro WASB. Vzhledem k tomu, `DELETE` oprávnění nebyl zadán, "Vložit" selže. `\_COPYING\_` Operace je Hadoop funkcí určená poskytuje určitou kontrolu souběžnosti. Současné době neexistuje žádný způsob, jak by to ovlivnilo "" operace zápisu také omezovat jenom operaci "SMAZAT".

3. Bohužel poskytovatele přihlašovacích údajů systému hadoop a dešifrovací klíče zprostředkovatele (ShellDecryptionKeyProvider) aktuálně nefungují s použitím tokenů SAS, a proto nyní nemůže být chráněn z viditelnost.

Další informace najdete v tématu [použití Azure Storage sdílené přístupové podpisy pro omezení přístupu k datům v HDInsight](../hdinsight-storage-sharedaccesssignature-permissions.md)

## <a name="use-data-encryption-and-replication"></a>Šifrování dat a replikace

Všechna data zapsaná do služby Azure Storage budou automaticky šifrována pomocí [šifrování služby Storage (SSE)](../../storage/common/storage-service-encryption.md). Data v účtu úložiště Azure se vždy replikují pro zajištění vysoké dostupnosti. Když vytvoříte účet úložiště, můžete nastavit jednu z těchto možností replikace:

- [Místně redundantní úložiště (LRS)](../../storage/common/storage-redundancy-lrs.md)
- [Zónově redundantní úložiště (ZRS)](../../storage/common/storage-redundancy-zrs.md)
- [Geograficky redundantní úložiště (GRS)](../../storage/common/storage-redundancy-grs.md)
- [Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)](../../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

Azure Data Lake Storage poskytuje místně redundantní úložiště (LRS), ale měli byste taky zkopírovat důležitých dat do jiného účtu Data Lake Storage v jiné oblasti s frekvencí odpovídá potřebám plánu zotavení po havárii. Existuje řada různých metod pro kopírování dat včetně [ADLCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md), DistCp, [prostředí Azure PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md), nebo [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md). Doporučuje se také k vynucování zásad přístupu pro účet Data Lake Storage, abyste zabránili nechtěnému odstranění.

Další informace najdete v následujících článcích:

- [Replikace Azure storage](../../storage/common/storage-redundancy.md)
- [Pokyny po havárii pro Azure Data Lake Storage (ADLS)](../../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

## <a name="attach-additional-azure-storage-accounts-to-the-cluster"></a>Připojit další služby Azure storage účty do clusteru

Během procesu vytváření HDInsight k účtu služby Azure Storage nebo účet úložiště Azure Data Lake je vybrán jako výchozí systém souborů. Během procesu vytváření clusteru nebo po vytvoření clusteru, je kromě této výchozí účet úložiště, přidat další účty úložiště ze stejného předplatného Azure nebo různých předplatných Azure.

Ještě účet úložiště můžete přidat v jednom na následujícími způsoby:
- Základní web Ambari HDFS Config Advanced vlastní přidejte název účtu úložiště a klíč restartování služeb
- Pomocí [skript akce](../hdinsight-hadoop-add-storage.md) předáním názvu účtu úložiště a klíč

> [!Note]
> V platné případy použití, je možné zvýšit limity ve službě Azure storage prostřednictvím požadavku odeslaného do [podpory Azure](https://azure.microsoft.com/support/faq/).

Další informace najdete v následujících článcích:
- [Přidání dalších účtů úložiště pro HDInsight](../hdinsight-hadoop-add-storage.md)
- [Připojit další služby Azure storage účty do clusteru](https://blogs.msdn.microsoft.com/ashish/2016/08/25/hdinsight-attach-additional-azure-storage-accounts/)

## <a name="next-steps"></a>Další postup

Přečtěte si další článek v této sérii:

- [Data migrace osvědčené postupy pro místní migrace Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-data-migration.md)