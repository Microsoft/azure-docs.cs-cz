---
title: Nejčastější dotazy k Azure HDInsight
description: Nejčastější dotazy týkající se HDInsight
keywords: Nejčastější dotazy najdete v nejčastějších dotazech.
author: Ramakoni1
ms.author: ramakoni
ms.service: hdinsight
ms.custom: hdinsightactive,seoapr2020
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: 13282962886d8682b6056d10f8f0cc5a7f626d60
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946022"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight: Nejčastější dotazy

Tento článek obsahuje odpovědi na některé z nejběžnějších otázek, jak spustit [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="creating-or-deleting-hdinsight-clusters"></a>Vytváření nebo odstraňování clusterů HDInsight

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>Návody zřídit cluster HDInsight?

Pokud chcete zkontrolovat typy clusterů HDInsight a metody zřizování, přečtěte si téma [Nastavení clusterů v HDInsight pomocí Apache Hadoop, Apache Spark, Apache Kafka a dalších](./hdinsight-hadoop-provision-linux-clusters.md).

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>Návody odstranit existující cluster HDInsight?

Další informace o odstranění clusteru, když se už nepoužívá, najdete v tématu [odstranění clusteru HDInsight](hdinsight-delete-cluster.md).

Zkuste z operací vytvoření a odstranění ponechte aspoň 30 až 60 minut. V opačném případě může operace selhat s následující chybovou zprávou:

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>Návody vybrat správný počet jader nebo uzlů pro moje zatížení?

Odpovídající počet jader a dalších možností konfigurace závisí na různých faktorech.

Další informace najdete v tématu [plánování kapacity pro clustery HDInsight](./hdinsight-capacity-planning.md).

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>Jaké jsou různé typy uzlů v clusteru HDInsight?

Seznamte [se s typy prostředků v clusterech Azure HDInsight](hdinsight-virtual-network-architecture.md#resource-types-in-azure-hdinsight-clusters).

### <a name="what-are-the-best-practices-for-creating-large-hdinsight-clusters"></a>Jaké jsou osvědčené postupy pro vytváření velkých clusterů HDInsight?

1. Doporučuje se nastavit clustery HDInsight s [vlastní AMBARI DB](./hdinsight-custom-ambari-db.md) pro zlepšení škálovatelnosti clusteru.
2. Pomocí [Azure Data Lake Storage Gen2](./hdinsight-hadoop-use-data-lake-storage-gen2.md) můžete vytvořit clustery HDInsight, abyste mohli využívat větší šířku pásma a další výkonnostní charakteristiky Azure Data Lake Storage Gen2.
3. Hlavních by měl být dostatečně velký, aby vyhovovalo více hlavním službám běžícím na těchto uzlech.
4. Některé konkrétní úlohy, jako například interaktivní dotaz, budou také potřebovat větší Zookeeper uzly. Zvažte prosím minimálně 8 jader virtuálních počítačů.
5. V případě podregistru a Sparku použijte [externí metastore Hive](./hdinsight-use-external-metadata-stores.md).

## <a name="individual-components"></a>Jednotlivé komponenty

### <a name="can-i-install-additional-components-on-my-cluster"></a>Můžu do clusteru nainstalovat další součásti?

Ano. K instalaci dalších součástí nebo přizpůsobení konfigurace clusteru použijte:

- Skripty během nebo po vytvoření. Skripty jsou vyvolány pomocí [akce skriptu](./hdinsight-hadoop-customize-cluster-linux.md). Akce skriptu je možnost konfigurace, kterou můžete použít z Azure Portal, rutin prostředí Windows PowerShell pro HDInsight nebo sady HDInsight .NET SDK. Tuto možnost konfigurace můžete použít z Azure Portal rutin prostředí Windows PowerShell nebo sady HDInsight .NET SDK.

- [Aplikační platforma HDInsight](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) pro instalaci aplikací.

Seznam podporovaných součástí najdete v tématu [co jsou komponenty Apache Hadoop a verze dostupné v HDInsight?](./hdinsight-component-versioning.md#apache-components-available-with-different-hdinsight-versions)

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>Můžu upgradovat jednotlivé komponenty, které jsou v clusteru předem nainstalované?

Pokud upgradujete předdefinované komponenty nebo aplikace, které jsou v clusteru předem nainstalované, výsledná konfigurace není podporovaná Microsoftem. Tyto systémové konfigurace nebyly testovány společností Microsoft. Zkuste použít jinou verzi clusteru HDInsight, která už může mít upgradovanou verzi předinstalované součásti.

Například upgrade podregistru jako jednotlivé součásti není podporován. HDInsight je spravovaná služba a mnoho služeb je integrovaných s Ambari serverem a testováno. Upgrade vlastního podregistru způsobí změnu indexovaných binárních souborů jiných komponent a způsobí problémy s integrací součástí v clusteru.

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>Může Spark a Kafka běžet na stejném clusteru HDInsight?

Ne, Apache Kafka a Apache Spark na stejném clusteru HDInsight není možné spustit. Vytvořte samostatné clustery pro Kafka a Spark, abyste se vyhnuli problémům s kolize prostředků.

### <a name="how-do-i-change-timezone-in-ambari"></a>Návody změnit časové pásmo v Ambari?

1. Otevřete webové uživatelské rozhraní Ambari v `https://CLUSTERNAME.azurehdinsight.net` , kde název_clusteru je název vašeho clusteru.
2. V pravém horním rohu vyberte Správce | Možnost. 

   ![Nastavení Ambari](media/hdinsight-faq/ambari-settings.png)

3. V okně Uživatelská nastavení vyberte v rozevíracím seznamu časové pásmo nové časové pásmo a pak klikněte na Uložit.

   ![Nastavení uživatele Ambari](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>Metastore

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-database"></a>Jak mohu migrovat ze stávajícího metastore do Azure SQL Database? 

Postup migrace z SQL Server na Azure SQL Database najdete v tématu [kurz: migrace SQL Server na izolovanou databázi nebo ve fondu databáze v Azure SQL Database offline pomocí DMS](../dms/tutorial-sql-server-to-azure-sql.md).

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>Odstraní se metastore Hive při odstranění clusteru?

Záleží na typu metastore, který je váš cluster nakonfigurovaný k použití.

Výchozí metastore: výchozí metastore je součástí životního cyklu clusteru. Při odstranění clusteru se odstraní také odpovídající metastore a metadata.

Vlastní metastore: životní cyklus metastore není svázán s životním cyklem clusteru. Takže můžete vytvořit a odstranit clustery bez ztráty metadat. Metadata, jako jsou schémata podregistru, se zachovává i po odstranění a opětovném vytvoření clusteru HDInsight.

Další informace najdete v tématu [použití externích úložišť metadat ve službě Azure HDInsight](hdinsight-use-external-metadata-stores.md).

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>Migruje metastore Hive také výchozí zásady databáze Ranger?

Ne, definice zásady je v databázi Ranger, takže migrace databáze Ranger migruje zásady.

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-the-other-way-around"></a>Můžete migrovat metastore Hive z clusteru Balíček zabezpečení podniku (ESP) do clusteru bez protokolu ESP a druhým způsobem?

Ano, metastore Hive můžete migrovat z protokolu ESP do clusteru bez protokolu ESP.

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Jak si můžu odhadnout velikost metastore Hive databáze?

Metastore Hive se používá k ukládání metadat pro zdroje dat, které používá server pro podregistr. Požadavky na velikost závisí částečně na počtu a složitosti zdrojů dat podregistru. Tyto položky se nedají odhadnout předem. Jak je uvedeno v [pokynech pro metastore Hive](hdinsight-use-external-metadata-stores.md#hive-metastore-guidelines), můžete začít s vrstvou S2. Úroveň poskytuje 50 DTU a 250 GB úložiště, a pokud se zobrazí kritický bod, navýšení kapacity databáze.

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>Podporujete jako externí metastore jinou databázi než Azure SQL Database?

Ne, Microsoft podporuje jenom Azure SQL Database jako externí vlastní metastore.

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>Můžu sdílet metastore napříč několika clustery?

Ano, můžete sdílet vlastní metastore napříč několika clustery, pokud používáte stejnou verzi HDInsight.

## <a name="connectivity-and-virtual-networks"></a>Připojení a virtuální sítě  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>Jaké jsou důsledky blokování portů 22 a 23 v mé síti?

Pokud zablokujete porty 22 a port 23, nebudete mít přístup přes SSH ke clusteru. Služba HDInsight tyto porty nepoužívá.

Další informace najdete v následujících dokumentech:

- [Porty používané službou Apache Hadoop Services ve službě HDInsight](./hdinsight-hadoop-port-settings-for-services.md)

- [Zabezpečení příchozího provozu do clusterů HDInsight ve virtuální síti s privátním koncovým bodem](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)

- [IP adresy pro správu služby HDInsight](./hdinsight-management-ip-addresses.md)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>Můžu nasadit další virtuální počítač ve stejné podsíti jako cluster HDInsight?

Ano, můžete nasadit další virtuální počítač ve stejné podsíti jako cluster HDInsight. Možné jsou následující konfigurace:

- Hraniční uzly: do clusteru můžete přidat další hraniční uzel, jak je popsáno v tématu [použití prázdných hraničních uzlů na Apache Hadoop clusterech v HDInsight](hdinsight-apps-use-edge-node.md).

- Samostatné uzly: do stejné podsítě můžete přidat samostatný virtuální počítač a přistupovat ke clusteru z tohoto virtuálního počítače pomocí privátního koncového bodu `https://<CLUSTERNAME>-int.azurehdinsight.net` . Další informace najdete v tématu [řízení síťového provozu](./control-network-traffic.md).

### <a name="should-i-store-data-on-the-local-disk-of-an-edge-node"></a>Mám ukládat data na místní disk hraničního uzlu?

Ne, není vhodné ukládat data na místním disku. Pokud uzel selže, všechna místně uložená data budou ztracena. Doporučujeme ukládat data do Azure Data Lake Storage Gen2 nebo úložiště objektů BLOB v Azure nebo připojením sdílené složky služby soubory Azure pro ukládání dat.


### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>Můžu přidat existující cluster HDInsight do jiné virtuální sítě?

Ne, nemůžete. Virtuální síť by měla být zadána v době zřizování. Pokud během zřizování není zadaná žádná virtuální síť, nasazení vytvoří interní síť, která není přístupná zvenčí. Další informace najdete v tématu [Přidání HDInsight do existující virtuální sítě](hdinsight-plan-virtual-network-deployment.md#existingvnet).

## <a name="security-and-certificates"></a>Zabezpečení a certifikáty

### <a name="what-are-the-recommendations-for-malware-protection-on-azure-hdinsight-clusters"></a>Jaká jsou doporučení pro ochranu proti malwaru v clusterech Azure HDInsight?

Informace o ochraně proti malwaru najdete v tématu [Microsoft Antimalware pro Azure Cloud Services a Virtual Machines](../security/fundamentals/antimalware.md).

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>Návody vytvořit keytab pro cluster HDInsight ESP?

Vytvořte keytab protokolu Kerberos pro uživatelské jméno domény. Později můžete tento keytab použít k ověření ve vzdálených clusterech připojených k doméně bez zadání hesla. Název domény je velká písmena:

```shell
ktutil
ktutil: addent -password -p <username>@<DOMAIN.COM> -k 1 -e RC4-HMAC
Password for <username>@<DOMAIN.COM>: <password>
ktutil: wkt <username>.keytab
ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>Můžu použít stávajícího tenanta Azure Active Directory k vytvoření clusteru HDInsight, který má protokol ESP?

Než budete moct vytvořit cluster HDInsight s protokolem ESP, povolte Azure Active Directory Domain Services (Azure služba AD DS). Open Source Hadoop spoléhá na protokol Kerberos k ověřování (na rozdíl od OAuth).

Pokud chcete připojit virtuální počítače k doméně, musíte mít řadič domény. Azure služba AD DS je spravovaný řadič domény a považuje se za rozšíření Azure Active Directory. Azure služba AD DS poskytuje všechny požadavky protokolu Kerberos k sestavení zabezpečeného clusteru Hadoop spravovaným způsobem. HDInsight jako spravovaná služba se integruje se službou Azure služba AD DS k zajištění zabezpečení.

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>Můžu použít certifikát podepsaný svým držitelem v nastavení zabezpečeného LDAP AAD-DS a zřídit cluster ESP?

Doporučuje se použít certifikát vydaný certifikační autoritou. Použití certifikátu podepsaného svým držitelem je také podporováno v protokolu ESP. Další informace naleznete v tématu:

- [Povolení služby Azure Active Directory Domain Services](domain-joined/apache-domain-joined-configure-using-azure-adds.md#enable-azure-ad-ds)

- [Kurz: Konfigurace zabezpečeného protokolu LDAP pro Azure Active Directory Domain Services spravovanou doménu](../active-directory-domain-services/tutorial-configure-ldaps.md)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>Jak můžu načíst přihlašovací aktivitu zobrazenou v Ranger?

V případě požadavků auditování doporučuje společnost Microsoft povolit protokoly Azure Monitor, jak je popsáno v tématu [použití protokolů Azure monitor k monitorování clusterů HDInsight](./hdinsight-hadoop-oms-log-analytics-tutorial.md).

### <a name="can-i-disable-clamscan-on-my-cluster"></a>Můžu `Clamscan` v clusteru zakázat?

`Clamscan` je antivirový software, který běží na clusteru HDInsight a používá ho Azure Security (azsecd) k ochraně vašich clusterů před útoky z antivirového programu. Společnost Microsoft důrazně doporučuje, aby uživatelé neprováděli změny ve výchozí `Clamscan` konfiguraci.

Tento proces není v konfliktu s jinými procesy nebo z nich nebere žádný cyklus. Bude vždycky vracet na jiný proces. Špičky procesoru od `Clamscan` by se měly zobrazit jenom v případě, že je systém nečinný.  

V situacích, kdy je nutné řídit plán, můžete použít následující postup:

1. Zakažte automatické spouštění pomocí následujícího příkazu:
   
  sudo `usr/local/bin/azsecd config -s clamav -d Disabled` restartování služby sudo pro azsecd 
   
1. Přidejte úlohu cron, která spustí následující příkaz jako kořenový adresář:
   
   `/usr/local/bin/azsecd manual -s clamav`

Další informace o tom, jak nastavit a spustit úlohu cron, najdete v tématu [návody nastavení úlohy cron](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)?

### <a name="why-is-llap-available-on-spark-esp-clusters"></a>Proč je LLAP k dispozici v clusterech Spark ESP?
LLAP je povolen z bezpečnostních důvodů (Apache Ranger), nikoli z hlediska výkonu. Využijte virtuální počítače s větším uzlem pro uspokojení využití prostředků LLAP (například minimální D13V2). 

### <a name="how-can-i-add-additional-aad-groups-after-creating-an-esp-cluster"></a>Jak můžu přidat další skupiny AAD po vytvoření clusteru ESP?
Existují dva způsoby, jak dosáhnout tohoto cíle: 1 – cluster můžete znovu vytvořit a přidat další skupinu v době vytváření clusteru. Pokud v AAD-DS používáte vymezenou synchronizaci, ujistěte se, že je skupina B zahrnutá v rámci synchronizace s vymezeným oborem.
2 – přidejte skupinu jako vnořenou dílčí skupinu předchozí skupiny, která se použila k vytvoření clusteru ESP. Pokud jste například vytvořili cluster ESP se skupinou `A` , můžete později přidat skupinu `B` jako vnořenou podskupinu `A` a po přibližně jednu hodinu bude synchronizována a k dispozici v clusteru automaticky. 

## <a name="storage"></a>Storage

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>Je možné přidat Azure Data Lake Storage Gen2 do stávajícího clusteru HDInsight jako další účet úložiště?

Ne, v současnosti není možné přidat účet úložiště Azure Data Lake Storage Gen2 do clusteru, který má jako primární úložiště BLOB Storage. Další informace najdete v tématu [porovnání možností úložiště](hdinsight-hadoop-compare-storage-options.md).

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Jak najdu aktuálně propojenou instanční objekt pro účet úložiště Data Lake?

Nastavení můžete najít v **Data Lake Storage Gen1 přístupu** ve vlastnostech clusteru v Azure Portal. Další informace najdete v tématu [ověření instalace clusteru](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#verify-cluster-set-up).
 
### <a name="how-can-i-calculate-the-usage-of-storage-accounts-and-blob-containers-for-my-hdinsight-clusters"></a>Jak mohu vypočítat využití účtů úložiště a kontejnerů objektů BLOB pro clustery HDInsight?

Proveďte jednu z následujících akcí:

- [Použití PowerShellu](../storage/scripts/storage-blobs-container-calculate-size-powershell.md)

- Najít velikost */User/Hive/. Odpadkový koš nebo* složka v clusteru HDInsight pomocí následujícího příkazového řádku:
  
  `hdfs dfs -du -h /user/hive/.Trash/`

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>Jak můžu nastavit auditování pro účet Blob Storage?

Pokud chcete auditovat účty úložiště BLOB, nakonfigurujte monitorování pomocí postupu v [části monitorování účtu úložiště v Azure Portal](../storage/common/storage-monitor-storage-account.md). HDFS – protokol auditu poskytuje jenom informace o auditování jenom pro místní systém souborů HDFS (hdfs://mycluster).  Nezahrnuje operace, které se provádějí na vzdáleném úložišti.

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>Jak můžu přenášet soubory mezi kontejnerem objektů BLOB a hlavním uzlem HDInsight?

Spusťte skript podobný následujícímu skriptu prostředí v hlavním uzlu:

```shell
for i in cat filenames.txt
do
   hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> Soubor *filenames.txt* bude mít absolutní cestu k souborům v kontejnerech objektů BLOB.
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>Existují nějaké moduly plug-in Ranger pro úložiště?

V současné době neexistuje žádný modul plug-in Ranger pro úložiště objektů BLOB a Azure Data Lake Storage Gen1 ani Gen2. Pro clustery ESP byste měli použít Azure Data Lake Storage. Pomocí nástrojů HDFS můžete ručně nastavit jemně odstupňovaná oprávnění na úrovni systému souborů. Při použití Azure Data Lake Storage se taky v clusterech s protokolem ESP provede některé řízení přístupu k systému souborů pomocí Azure Active Directory na úrovni clusteru. 

Zásady přístupu k datům můžete přiřadit ke skupinám zabezpečení vašich uživatelů pomocí Průzkumník služby Azure Storage. Další informace naleznete v tématu:

- [Návody nastavení oprávnění pro uživatele Azure AD k dotazování na data v Data Lake Storage Gen2 pomocí podregistru nebo jiných služeb?](hdinsight-hadoop-use-data-lake-storage-gen2.md#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
- [Nastavení oprávnění na úrovni souborů a adresářů pomocí Průzkumník služby Azure Storage s Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-explorer.md)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>Je možné zvýšit HDFS úložiště v clusteru bez zvýšení velikosti disku pracovních uzlů?

Ne. Nemůžete zvětšit velikost disku žádného pracovního uzlu. Jediným způsobem, jak velikost disku zvýšit, je odpojit cluster a znovu ho vytvořit s většími pracovními počítači. Nepoužívejte HDFS pro uložení dat služby HDInsight, protože data se odstraní, když cluster odstraníte. Místo toho uložte data v Azure. Škálování clusteru taky může do clusteru HDInsight přidat další kapacitu.

## <a name="edge-nodes"></a>Hraniční uzly

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>Můžu po vytvoření clusteru přidat hraniční uzel?

Viz [použití prázdných hraničních uzlů na Apache Hadoop clusterech v HDInsight](hdinsight-apps-use-edge-node.md).

### <a name="how-can-i-connect-to-an-edge-node"></a>Jak se můžu připojit k hraničnímu uzlu?

Po vytvoření hraničního uzlu se k němu můžete připojit pomocí SSH na portu 22. Název hraničního uzlu najdete na portálu clusteru. Názvy obvykle končí na *-Ed*.

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>Proč jsou trvalé skripty nespuštěny automaticky u nově vytvořených hraničních uzlů?

Trvalé skripty můžete použít k přizpůsobení nových pracovních uzlů přidaných do clusteru prostřednictvím operací škálování. Trvalé skripty se nevztahují na hraniční uzly.

## <a name="rest-api"></a>REST API

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>Co jsou REST API voláním pro vyžádání zobrazení dotazu tez z clusteru?

Pomocí následujících koncových bodů REST můžete načíst informace potřebné ve formátu JSON. K provedení požadavků použijte hlavičky základního ověřování.

- `Tez Query View`: *https: \/ / \<cluster name> . azurehdinsight.NET/WS/v1/Timeline/HIVE_QUERY_ID/*
- `Tez Dag View`: *https: \/ / \<cluster name> . azurehdinsight.NET/WS/v1/Timeline/TEZ_DAG_ID/*

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Návody načíst podrobnosti o konfiguraci z clusteru HDI pomocí Azure Active Directoryho uživatele?

Pokud chcete s vaším uživatelem AAD vyjednávat správné ověřovací tokeny, Projděte bránu pomocí následujícího formátu:

* https:// `<cluster dnsname>` . azurehdinsight.NET/API/v1/Clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Návody k monitorování výkonu PŘÍZu použít rozhraní RESTful API Ambari?

Pokud zavoláte příkaz složené ve stejné virtuální síti nebo v partnerské virtuální síti, příkaz je:

```curl
curl -u <cluster login username> -sS -G
http://<headnodehost>:8080/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
```
 
Pokud příkaz zavoláte mimo virtuální síť nebo z nepartnerské virtuální sítě, formát příkazu je:

- Pro cluster bez protokolu ESP:
  
  ```curl
  curl -u <cluster login username> -sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

- Pro cluster ESP:
  
  ```curl
  curl -u <cluster login username>-sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

> [!NOTE]
> V kudrlinkou se zobrazí výzva k zadání hesla. Je nutné zadat platné heslo pro uživatelské jméno přihlášení clusteru.

## <a name="billing"></a>Fakturace

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>Kolik stojí za nasazení clusteru HDInsight?

Další informace o cenách a nejčastějších dotazech souvisejících s fakturací najdete na stránce s [cenami Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) .

### <a name="when-does-hdinsight-billing-start--stop"></a>Kdy se zahájení fakturace HDInsight & zastavit?

Účtování clusteru HDInsight začne vytvořením clusteru a skončí jeho odstraněním. Fakturace je poměrně ohodnocená za minutu.

### <a name="how-do-i-cancel-my-subscription"></a>Návody zrušit své předplatné?

Informace o tom, jak zrušit předplatné, najdete v tématu [zrušení předplatného Azure](../cost-management-billing/manage/cancel-azure-subscription.md).

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>Co se stane po zrušení předplatného s průběžnými platbami?

Informace o vašem předplatném po jeho zrušení najdete v tématu [co se stane po zrušení předplatného?](../cost-management-billing/manage/cancel-azure-subscription.md)

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-im-running-an-hdinsight-36-cluster"></a>Proč se verze podregistru zobrazuje jako 1.2.1000 místo 2,1 v uživatelském rozhraní Ambari i v případě, že používám cluster HDInsight 3,6?

I když v uživatelském rozhraní Ambari se zobrazí jenom 1,2, HDInsight 3,6 obsahuje podregistry 1,2 a podregistr 2,1.

## <a name="other-faq"></a>Další Nejčastější dotazy

### <a name="what-does-hdinsight-offer-for-real-time-stream-processing-capabilities"></a>Co HDInsight nabízí možnost zpracování datových proudů v reálném čase?

Informace o možnostech integrace zpracování streamu najdete v tématu [Volba technologie zpracování datových proudů v Azure](/azure/architecture/data-guide/technology-choices/stream-processing).

### <a name="is-there-a-way-to-dynamically-kill-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>Existuje způsob, jak dynamicky ukončit hlavní uzel clusteru, když je cluster nečinný po určitou dobu?

Tuto akci nejde provést u clusterů HDInsight. V těchto scénářích můžete použít Azure Data Factory.

### <a name="what-compliance-offerings-does-hdinsight-offer"></a>Jaké nabídky se týkají dodržování předpisů nabízí HDInsight?

Informace o kompatibilitě najdete na [webu Microsoft Trust Center](https://www.microsoft.com/trust-center) a v tématu [přehled dodržování předpisů Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).
