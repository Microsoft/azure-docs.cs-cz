---
title: Nejčastější dotazy ke službě Azure HDInsight
description: Poskytuje přehled Azure HDInsight.
author: Ramakoni1
ms.author: ramakoni
ms.reviewer: v-miegge
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 4accd8c2f58dd89fb5f918ca6e58e71caeaed57f
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212752"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight: Nejčastější dotazy

Tento článek obsahuje odpovědi na některé z nejběžnějších otázek, jak spustit [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="creating-or-deleting-hdinsight-clusters"></a>Vytváření a odstraňování clusterů HDInsight

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>Návody zřídit cluster HDInsight?

Pokud chcete zkontrolovat dostupné typy clusterů HDInsight a metody zřizování, přečtěte si téma [Nastavení clusterů v HDInsight pomocí Apache Hadoop, Apache Spark, Apache Kafka a dalších](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>Návody odstranit existující cluster HDInsight?

Další informace o odstranění clusteru, když se už nepoužívá, najdete v tématu [odstranění clusteru HDInsight pomocí prohlížeče, PowerShellu nebo rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-delete-cluster). Mezi operací vytvořit a odstranit v jednom clusteru doporučujeme mít aspoň 30 až 60 minut. V opačném případě může operace selhat a vracet následující chybovou zprávu:

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>Návody vybrat správný počet jader nebo uzlů pro moje zatížení?

Odpovídající počet jader a dalších možností konfigurace závisí na různých faktorech.

Další informace najdete v tématu [plánování kapacity pro clustery HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning).

### <a name="what-can-i-do-when-cluster-provisioning-fails-because-of-a-capacity-issue"></a>Co můžu udělat, když se zřizování clusteru nepovede kvůli problému s kapacitou?

V této části jsou uvedené běžné chyby a techniky zmírnění potíží s kapacitou.

#### <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Chyba: Nasazení by překročilo kvótu "800".

V Azure platí limit kvóty 800 nasazení na skupinu prostředků. Pro každou skupinu prostředků, předplatné, účet nebo jiné obory se aplikují různé kvóty. Například v rámci předplatného může být nakonfigurované omezení počtu jader v jedné oblasti. Pokud se pokusíte nasadit virtuální počítač s více jádry, než je povolený, zobrazí se chybová zpráva s oznámením, že kvóta byla překročena.

Pokud chcete tento problém vyřešit, odstraňte nasazení, která už nepotřebujete, pomocí Azure Portal, CLI nebo PowerShellu.

Další informace najdete v tématu [Řešení chyb týkajících se kvót prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

#### <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Chyba: Maximální uzel překročil dostupné jádra v této oblasti.

V rámci předplatného může být nakonfigurované omezení počtu jader v jedné oblasti. Pokud se pokusíte nasadit prostředek, který obsahuje více jader, než je povolený, zobrazí se chybová zpráva s oznámením, že kvóta byla překročena.

Pokud chcete požádat o navýšení kvóty, postupujte následovně:

1. Otevřete Azure Portal a vyberte Help + Support (podpora).
2. Vyberte novou žádost o podporu.
3. Na stránce Nová žádost o podporu vyberte na kartě základy následující možnosti:

   * Typ problému: Limity služeb a předplatných (kvóty)
   * Předplatné: Předplatné, které chcete upravit
   * Typ kvóty: HDInsight

Další informace najdete v tématu [Vytvoření lístku podpory pro zvýšení počtu jader](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning#quotas).

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>Jaké jsou různé typy uzlů v clusteru HDInsight?

Clustery Azure HDInsight mají různé typy virtuálních počítačů nebo uzlů. Každý typ uzlu hraje roli v provozu systému.

Další informace najdete v tématu [typy prostředků v clusterech Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-virtual-network-architecture#resource-types-in-azure-hdinsight-clusters).

## <a name="individual-components"></a>Jednotlivé komponenty

### <a name="can-i-install-additional-components-on-my-cluster"></a>Můžu do clusteru nainstalovat další součásti?

Ano, můžete nainstalovat další součásti nebo přizpůsobit konfiguraci clusteru pomocí kterékoli z následujících metod:

* Použití skriptů během nebo po vytvoření. Tyto skripty jsou vyvolány pomocí [akce skriptu](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux). Tuto možnost konfigurace můžete použít z Azure Portal rutin prostředí Windows PowerShell nebo sady HDInsight .NET SDK. 
* Použití sudo nebo jiných metod po zřízení clusteru.
* Použití [platformy aplikace HDInsight](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) k instalaci aplikací ekosystému

Podpora Microsoftu týmy však mohou nabízet podporu pouze v následujících situacích:

* Problémy nebo chyby, ke kterým dochází při načítání skriptu. Všechny chyby, ke kterým dojde během provádění vlastních skriptů, jsou mimo obor lístku podpory. 
* Další aplikace, které jsou součástí procesu vytváření clusteru. 

Seznam podporovaných součástí najdete v tématu [co jsou komponenty Apache Hadoop a verze dostupné v HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)

Podpora jednotlivých komponent se může lišit také podle typu clusteru. Například Spark není podporován v clusteru Kafka a naopak.

Pro aplikace nebo služby mimo proces vytváření clusteru kontaktujte příslušného dodavatele nebo poskytovatele služeb a požádejte ho o podporu. Pro podporu těchto akcí můžete také použít weby komunity. K dispozici je mnoho webů komunity. Mezi příklady patří [Fórum MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/home?forum=hdinsight) a [Stack Overflow](https://stackoverflow.com/questions/tagged/hdinsight). Projekty Apache také obsahují projektové weby na [webu Apache](https://apache.org/). Jedním z příkladů je [Hadoop](https://hadoop.apache.org/).

 Další otázky týkající se podpory Azure najdete v [nejčastějších dotazech k podpoře](https://azure.microsoft.com/en-us/support/faq/)Azure.

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>Můžu upgradovat jednotlivé komponenty, které jsou v clusteru předem nainstalované?

Pokud upgradujete předdefinované součásti nebo aplikace, které jsou v clusteru předem nainstalované, výsledná konfigurace nebude podporována společností Microsoft. Tyto systémové konfigurace nebyly testovány společností Microsoft. Zkuste použít jinou verzi clusteru HDInsight, která už může mít upgradovanou verzi předinstalované součásti.

Například upgrade podregistru jako jednotlivé součásti není podporován. HDInsight je spravovaná služba a mnoho služeb je integrovaných s Ambari serverem a testováno. Upgrade vlastního podregistru způsobí změnu indexovaných binárních souborů jiných komponent a způsobí problémy s integrací součástí v clusteru.

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>Může Spark a Kafka běžet na stejném clusteru HDInsight?

Ne, Apache Kafka a Apache Spark na stejném clusteru HDInsight spustit nemůžete. Uživatelé by měli vytvořit samostatné clustery pro každý, aby nedocházelo k problémům s kolize prostředků.

### <a name="how-do-i-change-timezone-in-ambari"></a>Návody změnit časové pásmo v Ambari?

1. Otevřete webové uživatelské rozhraní Ambari v https://CLUSTERNAME.azurehdinsight.net, kde název_clusteru je název vašeho clusteru.
2. V pravém horním rohu vyberte Správce | Možnost. 

   ![Nastavení Ambari](media/hdinsight-faq/ambari-settings.png)

3. V okně Uživatelská nastavení vyberte v rozevíracím seznamu časové pásmo nové časové pásmo a pak klikněte na Uložit.

   ![Nastavení uživatele Ambari](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>Metastore

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-server"></a>Jak mohu migrovat ze stávajícího metastore do Azure SQL Server? 

Postup migrace z SQL Server do Azure SQL Server najdete v [tématu Kurz: Migrujte SQL Server do izolované databáze nebo databáze ve fondu v Azure SQL Database offline pomocí DMS](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql).

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>Odstraní se metastore Hive při odstranění clusteru?

Záleží na typu metastore, který je váš cluster nakonfigurovaný k použití.

Výchozí metastore: Výchozí metastore je součástí životního cyklu clusteru. Při odstranění clusteru se odstraní také odpovídající metastore a metadata.

Vlastní metastore: Životní cyklus metastore není svázán s životním cyklem clusteru. Proto můžete vytvořit a odstranit clustery bez ztráty metadat. Metadata, jako jsou schémata podregistru, se zachovává i po odstranění a opětovném vytvoření clusteru HDInsight.

Další informace najdete v tématu [použití externích úložišť metadat ve službě Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-external-metadata-stores).

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>Migruje metastore Hive také výchozí zásady databáze Ranger?

Ne, definice zásady je v databázi Ranger. Proto migrace databáze Ranger nemigruje zásady.

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-vice-versa"></a>Můžete migrovat metastore Hive z clusteru Balíček zabezpečení podniku (ESP) do clusteru bez protokolu ESP a naopak?

Ano, metastore Hive můžete migrovat z protokolu ESP do clusteru bez protokolu ESP.

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Jak si můžu odhadnout velikost metastore Hive databáze?

Metastore Hive se používá k ukládání metadat pro zdroje dat, které používá server pro podregistr. Proto jsou požadavky na velikost ovlivněny počtem zdrojů dat, které může být nutné použít pro podregistr, a způsobem složitosti zdrojů dat. Proto se velikost nedá odhadnout dopředu. Jak je uvedeno v [metastore Hive osvědčené postupy](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-external-metadata-stores#hive-metastore-best-practices), počínaje vrstvou S2 poskytují 50 DTU a 250 GB úložiště. Pokud se setkáte s kritickým bodem, můžete navýšení kapacity databáze škálovat.

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>Podporujete jako externí metastore jinou databázi než Azure SQL Database?

Ne, Microsoft podporuje jenom Azure SQL Database jako externí vlastní metastore.

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>Můžu sdílet metastore napříč několika clustery?

Ano, můžete sdílet vlastní metastore napříč několika clustery, pokud používají stejnou verzi HDInsight.

## <a name="connectivity-and-virtual-networks"></a>Připojení a virtuální sítě  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>Jaké jsou důsledky blokování portů 22 a 23 v mé síti?

Pokud zablokujete porty 22 a port 23, nebudete mít přístup ke clusteru přes SSH. Služba HDInsight tyto porty nepoužívá.

Další informace najdete v následujících dokumentech:

* [Řízení síťového provozu](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic)
* [Zabezpečení příchozího provozu do clusterů HDInsight ve virtuální síti s privátním koncovým bodem](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
* [IP adresy správy HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>Můžu nasadit další virtuální počítač ve stejné podsíti jako cluster HDInsight?

Ano, můžete nasadit další virtuální počítač ve stejné podsíti jako cluster HDInsight. Možné jsou následující konfigurace:

* Hraniční uzly: Do clusteru můžete přidat další hraniční uzel, jak je popsáno v tématu [použití prázdných hraničních uzlů na Apache Hadoop clusterech v HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-use-edge-node). 
* Samostatné uzly:  Do stejné podsítě můžete přidat samostatný virtuální počítač a přistupovat ke clusteru z tohoto virtuálního počítače pomocí privátního koncového bodu `https://<CLUSTERNAME>-int.azurehdinsight.net`. Další informace najdete v tématu [řízení síťového provozu](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic).

### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>Můžu přidat existující cluster HDInsight do jiné virtuální sítě?

Ne, existující cluster HDInsight nemůžete přidat do jiné virtuální sítě. Virtuální síť by měla být zadána v době zřizování. Pokud během zřizování není zadaná žádná virtuální síť, nasazení vytvoří interní síť, která není přístupná zvenčí. Další informace najdete v tématu [Přidání HDInsight do existující virtuální sítě](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#existingvnet).

## <a name="security-and-certificates"></a>Zabezpečení a certifikáty

Co jsou doporučení Microsoftu pro ochranu proti malwaru v clusterech Azure HD Insight?

Informace o ochraně proti malwaru najdete v tématu [Microsoft Antimalware pro Azure Cloud Services a Virtual Machines](https://docs.microsoft.com/azure/security/fundamentals/antimalware).

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>Návody vytvořit keytab pro cluster HDInsight ESP?

Pro uživatelské jméno domény můžete vytvořit keytab protokolu Kerberos. Později můžete tento keytab použít k ověření ve vzdálených clusterech připojených k doméně bez zadání hesla. Všimněte si velkých písmen v názvu domény:

```
o ktutil 
o ktutil: addent -password -p stXX@ DOMAIN.COM -k 1 -e RC4-HMAC 
o Password for stXX@DOMAIN.COM: provided by lab instructors 
o ktutil: wkt stXX.keytab 
o ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>Můžu použít stávajícího tenanta Azure Active Directory k vytvoření clusteru HDInsight, který má protokol ESP?

Povolením Azure Active Directory Domain Services (AAD-DS) je předpokladem, než budete moct vytvořit cluster HDInsight s protokolem ESP. Open Source Hadoop spoléhá na protokol Kerberos k ověřování (na rozdíl od OAuth).

Aby bylo možné připojit virtuální počítače k doméně, je vyžadován řadič domény. AAD-DS je spravovaný řadič domény. Považuje se za rozšíření AAD, které poskytuje všechny požadavky protokolu Kerberos pro vytvoření zabezpečeného clusteru Hadoop spravovaném způsobem. HDInsight jako spravovaná služba se integruje s AAD-DS, aby poskytovala komplexní zabezpečení.

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>Můžu použít certifikát podepsaný svým držitelem v nastavení zabezpečeného LDAP AAD-DS a zřídit cluster ESP?

I když se doporučuje použít certifikát vydaný certifikační autoritou (CA), v protokolu ESP se taky podporuje použití certifikátu podepsaného svým držitelem.

Další informace naleznete v tématu:

* [Povolení služby Azure AD-DS](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds#enable-azure-ad-ds)
* [Kurz: Konfigurace protokolu Secure LDAP pro Azure Active Directory Domain Services spravovanou doménu](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-configure-ldaps)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>Jak můžu načíst přihlašovací aktivitu zobrazenou v Ranger?

V případě požadavků auditování doporučuje společnost Microsoft povolit protokoly Azure Monitor, jak je popsáno v tématu [použití protokolů Azure monitor k monitorování clusterů HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial).

### <a name="can-i-disable-clamscan-on-my-cluster"></a>Můžu v clusteru zakázat ClamScan?

ClamScan je antivirový software, který běží na clusteru HDInsight a používá ho Azure Security (azsecd) k ochraně vašich clusterů před útoky z antivirového programu. Společnost Microsoft důrazně doporučuje, aby uživatelé neprováděli změny ve výchozí konfiguraci ClamScan.

Tento proces neovlivňuje ani neprovádí žádné cykly z jiných procesů. Bude vždycky vracet na jiný proces. Špičky procesoru z ClamScan by se měly zobrazit jenom v případě, že je systém nečinný.  

V situacích, kdy je nutné řídit plán, můžete použít následující postup:

1. Automatické spouštění zakažte pomocí následujícího příkazu:

   ``/usr/local/vbin/azsecd config -s clamav -d Disabled``

2. Vytvořte úlohu cron, která spustí tento příkaz jako kořen:

   ``/usr/local/bin/azsecd manual -s clamav``

Další informace o tom, jak nastavit a spustit úlohu cron, najdete v tématu [návody nastavení úlohy cron](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)?

## <a name="storage"></a>Storage

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>Je možné přidat Azure Data Lake Storage Gen2 do stávajícího clusteru HDInsight jako další účet úložiště?

Ne. V současné době nemůžete přidat Azure Data Lake Storage Gen2 do stávajícího clusteru HDInsight, který jako primární úložiště používá úložiště objektů BLOB. Další informace najdete v tématu [porovnání možností úložiště pro použití s clustery Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options). 

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Jak najdu aktuálně propojenou instanční objekt pro účet úložiště Data Lake?

Tato nastavení můžete ověřit tak, že ve vlastnostech svého clusteru na portálu zkontrolujete přístup Data Lake Storage Gen1. Další informace najdete v tématu [ověření instalace clusteru](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal#verify-cluster-set-up).
 
Jak mohu vypočítat využití účtů úložiště a kontejnerů objektů BLOB pro clustery HDInsight?
Můžete použít jeden z následujících postupů:

* [Použití PowerShellu](https://docs.microsoft.com/azure/storage/scripts/storage-blobs-container-calculate-size-powershell)
* Můžete také najít velikost/User/Hive/. Odpadkový koš/složka v clusterech HDI pomocí následujícího příkazu:

   ``hdfs dfs -du -h /user/hive/.Trash/``

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>Jak můžu nastavit auditování pro účet Blob Storage?

K auditování účtů BLOB Storage je nutné nakonfigurovat monitorování účtu úložiště BLOB pomocí postupu popsaného v [části monitorování účtu úložiště v Azure Portal](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account). HDFS – protokol auditu poskytuje jenom informace o auditování jenom pro místní systém souborů HDFS (hdfs://mycluster).  Nezahrnuje operace, které se provádějí na vzdáleném úložišti.

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>Jak můžu přenášet soubory mezi kontejnerem objektů BLOB a hlavním uzlem HDInsight?

Soubory mezi kontejnerem objektů BLOB a hlavním uzlem HDInsight můžete přenášet spuštěním skriptu prostředí na hlavním uzlu, podobně jako v následujícím příkladu:

```
for i in cat filenames.txt

do
         hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> Soubory s příponou *. txt* budou mít absolutní cestu k souborům v kontejnerech objektů BLOB.
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>Existují nějaké moduly plug-in Ranger pro úložiště?

V současné době neexistují žádné moduly plug-in Ranger pro úložiště objektů blob, Azure Data Lake Storage (ADLS) Gen1 nebo Azure Data Lake Storage Gen2. V případě clusterů ESP použijte ADLS jako osvědčený postup a na úrovni systému souborů pomocí nástrojů HDFS nastavte ručně jemně odstupňovaná oprávnění. Clustery ESP také při použití ADLS provede některé z řízení přístupu k systému souborů pomocí AAD na úrovni clusteru. 

Měli byste být schopni použít Průzkumník služby Azure Storage k přiřazení zásad přístupu k datům ke skupinám zabezpečení, kde se uživatelé nacházejí, pomocí postupů popsaných v následujících článcích:

* [Návody nastavení oprávnění pro uživatele Azure AD k dotazování na data v Data Lake Storage Gen2 pomocí podregistru nebo jiných služeb?](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
* [Nastavení oprávnění na úrovni souborů a adresářů pomocí Průzkumník služby Azure Storage s Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>Je možné zvýšit HDFS úložiště v clusteru bez zvýšení velikosti disku pracovních uzlů?

Ne, velikost disku žádného pracovního uzlu nemůžete zvětšit. Jediným způsobem, jak zvětšit velikost disku, je odstranit cluster a znovu ho vytvořit na větších virtuálních počítačích pracovních procesů.

Microsoft nedoporučuje používat HDFS k ukládání vašich dat v HDInsight, protože data se odstraní při odstranění clusteru. Místo toho doporučujeme ukládat data do Azure. Škálování clusteru může také do clusteru HDInsight přidat větší kapacitu.

## <a name="edge-nodes"></a>Hraniční uzly

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>Můžu po vytvoření clusteru přidat hraniční uzel?

Ano, můžete přidat prázdný hraniční uzel do stávajícího clusteru HDInsight nebo do nového clusteru při vytváření clusteru.

Další informace najdete v tématu [Použití prázdných hraničních uzlů v clusterech Apache Hadoop ve službě HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-use-edge-node).

### <a name="how-can-i-connect-to-an-edge-node"></a>Jak se můžu připojit k hraničnímu uzlu?

Po vytvoření hraničního uzlu se můžete připojit k hraničnímu uzlu pomocí SSH na portu 22. Název hraničního uzlu můžete najít na portálu clusteru (názvy obecně končí "-Ed").

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>Proč jsou trvalé skripty nespuštěny automaticky u nově vytvořených hraničních uzlů?

Trvalé skripty se používají k přizpůsobení nových pracovních uzlů, které se přidají do clusteru prostřednictvím operací škálování a neplatí pro hraniční uzly.

## <a name="rest-api"></a>REST API

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>Co jsou REST API voláním pro vyžádání zobrazení dotazu tez z clusteru?

Následující koncové body REST můžete použít k vyžádání potřebných informací, kde odpověď bude ve formátu JSON. K provedení těchto požadavků lze použít hlavičky základního ověřování.

* "Zobrazení dotazu tez – https://`<cluster name>`. azurehdinsight.NET/WS/v1/Timeline/HIVE_QUERY_ID/

* "Tez DAG" View- https://`<cluster name>`. azurehdinsight.NET/WS/v1/Timeline/TEZ_DAG_ID/

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Návody načíst podrobnosti o konfiguraci z clusteru HDI pomocí Azure Active Directoryho uživatele?

Pokud chcete s vaším uživatelem AAD vyjednávat správné ověřovací tokeny, Projděte bránu pomocí následujícího formátu:

* https://`<cluster dnsname>`. azurehdinsight.NET/API/v1/Clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Návody k monitorování výkonu PŘÍZu použít rozhraní RESTful API Ambari?

Pokud je příkaz složeného volání ve stejné virtuální síti nebo virtuální síti s partnerským vztahem, použijte následující příkaz:

``curl -u <cluster login username> -sS -G http://headnodehost:8080/api/v1/clusters/anssenllap/services/YARN/components/NODEMANAGER?fields=metrics/cpu``
 
Pokud se příkaz volá mimo virtuální síť nebo virtuální síť bez partnerského vztahu, formát příkazu je:
 
* Pro cluster bez protokolu ESP:``curl -u <cluster login username> -sS -G https://ClusterName.azurehdinsight.net/api/v1/clusters/ClusterName/services/YARN/components/NODEMANAGER?fields=metrics/cpu``
 
* Pro cluster ESP:``curl -u <cluster login username> -sS -G https://ClusterName.azurehdinsight.net/api/v1/clusters/ClusterName/services/YARN/components/NODEMANAGER?fields=metrics/cpu``

> [!NOTE]
> V kudrlinkou se zobrazí výzva k zadání hesla. Je nutné zadat platné heslo pro uživatelské jméno přihlášení clusteru.

## <a name="billing"></a>Fakturace

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>Kolik stojí za nasazení clusteru HDInsight?

Další informace o cenách a nejčastějších dotazech souvisejících s fakturací najdete na stránce s [cenami Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) .

### <a name="how-do-i-cancel-my-subscription"></a>Návody zrušit své předplatné?

Informace o tom, jak zrušit předplatné, najdete v tématu [zrušení předplatného Azure](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription).

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>Co se stane po zrušení předplatného s průběžnými platbami?

Informace o vašem předplatném po jeho zrušení najdete v tématu [co se stane po zrušení předplatného?](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription#what-happens-after-i-cancel-my-subscription)

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-i-am-running-an-hdinsight-36-cluster"></a>Proč se verze podregistru zobrazuje jako 1.2.1000 místo 2,1 v uživatelském rozhraní Ambari, i když Spouštím cluster HDInsight 3,6?

I když v uživatelském rozhraní Ambari se zobrazí jenom 1,2, HDInsight 3,6 obsahuje podregistry 1,2 a podregistr 2,1.

## <a name="other-faq"></a>Další Nejčastější dotazy

### <a name="what-does-hdinsight-offer-in-terms-of-real-time-stream-processing-capabilities"></a>Co nabízí HDInsight v souvislosti s funkcemi zpracování datových proudů v reálném čase?

Informace o možnostech integrace zpracování datových proudů ve službě Azure HDInsight najdete v tématu [Volba technologie zpracování datových proudů v Azure](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/stream-processing).

### <a name="is-there-a-way-to-dynamically-terminate-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>Existuje způsob, jak dynamicky ukončit hlavní uzel clusteru, když je cluster nečinný po určitou dobu?

Ne, hlavní uzel clusteru nemůžete dynamicky ukončovat. Pro tento scénář můžete použít Azure Data Factory.
