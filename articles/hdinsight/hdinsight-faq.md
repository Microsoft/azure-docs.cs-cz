---
title: Nejčastější dotazy azure HDInsight
description: Nejčastější dotazy týkající se HDInsight
keywords: nejčastější dotazy, nejčastější dotazy
author: Ramakoni1
ms.author: ramakoni
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: b05b83086cc9d8449d9517897f347b6e2685aa95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720346"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight: Nejčastější dotazy

Tento článek obsahuje odpovědi na některé z nejčastějších otázek týkajících se spuštění [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="creating-or-deleting-hdinsight-clusters"></a>Vytváření nebo odstraňování clusterů HDInsight

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>Jak zřídím cluster HDInsight?

Informace o dostupných typech clusterů HDInsight a metodách zřizování najdete v tématu [Nastavení clusterů v HDInsightu pomocí Apache Hadoop, Apache Spark, Apache Kafka a dalších](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>Jak odstraním existující cluster HDInsight?

Další informace o odstranění clusteru, když se už nepoužívá, najdete v [tématu Odstranění clusteru HDInsight](hdinsight-delete-cluster.md).

Mezi operacemi vytvoření a odstranění ponechte alespoň 30 až 60 minut. V opačném případě může dojít k selhání operace s následující chybovou zprávou:

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>Jak vybrat správný počet jader nebo uzlů pro svou pracovní zátěž?

Příslušný počet jader a další možnosti konfigurace závisí na různých faktorech.

Další informace naleznete [v tématu Plánování kapacity pro clustery HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning).

### <a name="what-can-i-do-when-cluster-provisioning-fails-because-of-a-capacity-issue"></a>Co můžu dělat, když zřizování clusteru selže kvůli problému s kapacitou?

V této části jsou uvedeny běžné chyby problému s kapacitou a techniky zmírňování.

#### <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Chyba: Nasazení by překročilo kvótu 800.

V Azure platí limit kvóty 800 nasazení na skupinu prostředků. Různé kvóty se používají pro skupinu prostředků, předplatné, účet nebo jiné obory. Například v rámci předplatného může být nakonfigurované omezení počtu jader v jedné oblasti. Pokud se pokusíte nasadit virtuální počítač, který má více jader, než je povolená částka, zobrazí se chybová zpráva, že kvóta byla překročena.

Chcete-li tento problém vyřešit, odstraňte nasazení, která už nejsou potřeba pomocí portálu Azure, CLI nebo PowerShellu.

Další informace najdete v tématu [Řešení chyb týkajících se kvót prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

#### <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Chyba: Maximální počet uzlů překročil dostupná jádra v této oblasti.

V rámci předplatného může být nakonfigurované omezení počtu jader v jedné oblasti. Pokud se pokusíte nasadit prostředek, který má více jader, než je povolená částka, zobrazí se chybová zpráva, že kvóta byla překročena.

Pokud chcete požádat o navýšení kvóty, postupujte následovně:

1. Přejděte na [portál Azure](https://portal.azure.com)a vyberte **Nápověda + podpora**.
   
1. Vyberte **nový požadavek na podporu**.
   
1. Na kartě **Základy** na stránce **Nová žádost o podporu** zadejte následující informace:
   
   - **Typ problému:** Vyberte **omezení služeb a předplatného (kvóty).**
   - **Předplatné:** Vyberte předplatné, které chcete upravit.
   - **Typ kvóty:** Vyberte **HDInsight**.

Další informace najdete v tématu [Vytvoření lístku podpory kvůli navýšení počtu jader](hdinsight-capacity-planning.md#quotas).

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>Jaké jsou různé typy uzlů v clusteru HDInsight?

Clustery Azure HDInsight mají různé typy virtuálních počítačů nebo uzlů. Každý typ uzlu hraje roli v provozu systému.

Další informace najdete v tématu [typy prostředků v clusterech Azure HDInsight](hdinsight-virtual-network-architecture.md#resource-types-in-azure-hdinsight-clusters).

## <a name="individual-components"></a>Jednotlivé komponenty

### <a name="can-i-install-additional-components-on-my-cluster"></a>Můžu do clusteru nainstalovat další součásti?

Ano. Chcete-li nainstalovat další součásti nebo přizpůsobit konfiguraci clusteru, použijte:

- Skripty během nebo po vytvoření. Skripty se navádí pomocí [akce skriptu](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux), což je možnost konfigurace, kterou můžete použít z portálu Azure, rutin prostředí HDInsight Windows PowerShell nebo sady HDInsight .NET SDK. Tuto možnost konfigurace lze použít z portálu Azure, rutin hdinsight windows prostředí Nebo sady HDInsight .NET SDK.

- [HDInsight Aplikační platforma](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) pro instalaci ekosystémových aplikací.

Seznam podporovaných komponent najdete [v tématu Jaké jsou komponenty a verze Apache Hadoop dostupné v HDInsightu?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>Je možné upgradovat jednotlivé součásti, které jsou předinstalované v clusteru?

Pokud inovujete předdefinované součásti nebo aplikace, které jsou v clusteru předinstalovány, nebude výsledná konfigurace společností Microsoft podporována. Tyto konfigurace systému nebyly společností Microsoft testovány. Zkuste použít jinou verzi clusteru HDInsight, která již může mít inovovanou verzi komponenty předinstalovanou.

Například upgrade Hive jako jednotlivé součásti není podporována. HDInsight je spravovaná služba a mnoho služeb je integrováno se serverem Ambari a testováno. Upgrade Podregistr na vlastní způsobí, že indexované binární soubory jiných součástí změnit a způsobí problémy s integrací součástí v clusteru.

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>Mohou Spark a Kafka běžet ve stejném clusteru HDInsight?

Ne, není možné spustit Apache Kafka a Apache Spark ve stejném clusteru HDInsight. Vytvořte samostatné clustery pro Kafka a Spark, abyste se vyhnuli problémům s konflikty prostředků.

### <a name="how-do-i-change-timezone-in-ambari"></a>Jak změním časové pásmo v Ambari?

1. Otevřete webové uživatelské uživatelské nastavení `https://CLUSTERNAME.azurehdinsight.net`Ambari na adrese , kde CLUSTERNAME je název clusteru.
2. V pravém horním rohu vyberte admin | Nastavení. 

   ![Nastavení Ambari](media/hdinsight-faq/ambari-settings.png)

3. V okně Uživatelská nastavení vyberte nové časové pásmo z rozevíracího okna Časové pásmo a klikněte na Uložit.

   ![Uživatelská nastavení Ambari](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>Metastore

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-server"></a>Jak můžu migrovat z existujícího metaúložiště na Azure SQL Server? 

Pokud chcete migrovat z SQL Serveru na Azure SQL Server, [přečtěte si informace o tom, že pomocí služby DMS najdete v článku Migrace serveru SQL Server do jedné databáze nebo do sdružené databáze v azure sql database](../dms/tutorial-sql-server-to-azure-sql.md).

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>Je metaúložiště Hive odstraněno, když je cluster odstraněn?

Záleží na typu metastore, který je cluster nakonfigurován pro použití.

Pro výchozí metastore: Výchozí metastore je součástí životního cyklu clusteru. Když odstraníte cluster, odstraní se také odpovídající metastore a metadata.

Pro vlastní metastore: Životní cyklus metastore není vázána na životní cyklus clusteru. Proto můžete vytvářet a odstraňovat clustery bez ztráty metadat. Metadata, jako jsou schémata Hive, zůstanou zachována i po odstranění a opětovném vytvoření clusteru HDInsight.

Další informace najdete [v tématu Použití externích úložišť metadat v Azure HDInsight](hdinsight-use-external-metadata-stores.md).

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>Migruje metaúložiště Hive také výchozí zásady databáze Ranger?

Ne, definice zásad je v databázi Ranger, takže migrace databáze Ranger bude migrovat své zásady.

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-vice-versa"></a>Můžete migrovat metaúložiště Hive z clusteru balíčků zabezpečení rozlehlé sítě (ESP) do clusteru bez ESP a naopak?

Ano, metaúložiště Hive můžete migrovat z esp do clusteru bez ESP.

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Jak lze odhadnout velikost databáze metastore Hive?

Metastore Hive se používá k ukládání metadat pro zdroje dat, které jsou používány serverem Hive. Požadavky na velikost závisí částečně na počtu a složitosti zdrojů dat Hive a nelze je odhadnout předem. Jak je uvedeno v [hive metastore osvědčených postupů](hdinsight-use-external-metadata-stores.md#hive-metastore-best-practices), můžete začít s Úrovní S2, která poskytuje 50 DTU a 250 GB úložiště, a pokud vidíte problémové místo, můžete vertikálně navýšit kapacitu databáze.

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>Podporujete jinou databázi než Azure SQL Database jako externí metastore?

Ne, Microsoft podporuje jenom Azure SQL Database jako externí vlastní metastore.

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>Mohu sdílet metastore ve více clusterech?

Ano, můžete sdílet vlastní metastore ve více clusterech, pokud používají stejnou verzi HDInsight.

## <a name="connectivity-and-virtual-networks"></a>Připojení a virtuální sítě  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>Jaké jsou důsledky blokování portů 22 a 23 v mé síti?

Pokud zablokujete porty 22 a port 23, nebudete mít přístup SSH ke clusteru. Tyto porty nejsou používány službou HDInsight.

Další informace najdete v následujících dokumentech:

- [Řízení síťového provozu](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic)

- [Zabezpečení příchozího provozu do clusterů HDInsight ve virtuální síti s privátním koncovým bodem](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)

- [IP adresy hdinsight pro správu](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>Můžu nasadit další virtuální počítač ve stejné podsíti jako cluster HDInsight?

Ano, můžete nasadit další virtuální počítač ve stejné podsíti jako cluster HDInsight. Jsou možné následující konfigurace:

- Hraniční uzly: Do clusteru můžete přidat další uzel okrajů, jak je popsáno v [části Použití prázdných hraničních uzlů v clusterech Apache Hadoop v HDInsightu](hdinsight-apps-use-edge-node.md).

- Samostatné uzly: Můžete přidat samostatný virtuální počítač do stejné podsítě a přistupovat ke clusteru z tohoto virtuálního počítače pomocí privátního koncového bodu `https://<CLUSTERNAME>-int.azurehdinsight.net`. Další informace naleznete v [tématu Řízení síťového provozu](hdinsight-plan-virtual-network-deployment.md#networktraffic).

### <a name="should-i-store-data-on-the-local-disk-of-an-edge-node"></a>Mám ukládat data na místní disk hraničního uzlu?

Ne, ukládání dat na místní disk není dobrý nápad. Pokud uzel selže, všechna data uložená místně budou ztracena. Doporučujeme ukládat data v Azure Data Lake Storage Gen2 nebo Azure Blob storage nebo připojením sdílené složky Azure Files pro ukládání dat.


### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>Můžu přidat existující cluster HDInsight do jiné virtuální sítě?

Ne, to nemůžeš. Virtuální síť by měla být určena v době zřizování. Pokud během zřizování není zadána žádná virtuální síť, nasazení vytvoří interní síť, která není přístupná zvenčí. Další informace najdete [v tématu Přidání HDInsightu do existující virtuální sítě](hdinsight-plan-virtual-network-deployment.md#existingvnet).

## <a name="security-and-certificates"></a>Zabezpečení a certifikáty

### <a name="what-are-the-recommendations-for-malware-protection-on-azure-hdinsight-clusters"></a>Jaká jsou doporučení pro ochranu proti malwaru v clusterech Azure HDInsight?

Informace o ochraně proti malwaru najdete v [tématu Microsoft Antimalware for Azure Cloud Services and Virtual Machines](../security/fundamentals/antimalware.md).

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>Jak vytvořím kartu klíčů pro cluster HDInsight ESP?

Vytvořte kartu s klíči protokolu Kerberos pro uživatelské jméno domény. Později můžete tuto kartu klíčů použít k ověření clusterů připojení ke vzdálené doméně bez zadání hesla. Název domény je velká:

```shell
ktutil
ktutil: addent -password -p <username>@<DOMAIN.COM> -k 1 -e RC4-HMAC
Password for <username>@<DOMAIN.COM>: <password>
ktutil: wkt <username>.keytab
ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>Můžu použít existujícího klienta Služby Azure Active Directory k vytvoření clusteru HDInsight, který má ESP?

Před vytvořením clusteru HDInsight s ESP je nutné povolit službu Azure Active Directory Domain Services (Azure AD DS). Open source Hadoop spoléhá na Kerberos pro ověřování (na rozdíl od OAuth).

Chcete-li připojit virtuální zařízení k doméně, musíte mít řadič domény. Azure AD DS je spravovaný řadič domény a je považován za rozšíření služby Azure Active Directory, která poskytuje všechny požadavky protokolu Kerberos na vytvoření zabezpečeného clusteru Hadoop spravovaným způsobem. HDInsight jako spravovaná služba se integruje s Azure AD DS a poskytuje komplexní zabezpečení.

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>Je možné použít certifikát podepsaný svým držitelem v zabezpečeném nastavení protokolu LDAP systému AAD-DS a zřízení clusteru ESP?

Doporučuje se používat certifikát vydaný certifikační autoritou, ale použití certifikátu podepsaného svým držitelem je podporováno také v systému ESP. Další informace naleznete v tématu:

- [Povolení služby Azure Active Directory Domain Services](domain-joined/apache-domain-joined-configure-using-azure-adds.md#enable-azure-ad-ds)

- [Kurz: Konfigurace zabezpečeného protokolu LDAP pro spravovanou doménu služby Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-configure-ldaps.md)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>Jak mohu vytáhnout přihlašovací aktivity zobrazené v Ranger?

Pro požadavky na auditování společnost Microsoft doporučuje povolit protokoly Azure Monitor, jak je popsáno v [protokolech Azure Monitor, ke sledování clusterů HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial).

### <a name="can-i-disable-clamscan-on-my-cluster"></a>Mohu službu Clamscan ve svém clusteru zakázat?

Clamscan je antivirový software, který běží v clusteru HDInsight a používá Azure security (azsecd) k ochraně clusterů před virovými útoky. Společnost Microsoft důrazně doporučuje, aby uživatelé neprovázkovali žádné změny výchozí konfigurace služby Clamscan.

Tento proces nenarušuje nebo vzít žádné cykly od jiných procesů. Vždy se poddá jinému procesu. Špičky procesoru z Clamscan upozorní pouze v případě, že je systém nečinný.  

Ve scénářích, ve kterých je nutné řídit plán, můžete použít následující kroky:

1. Zakažte automatické spuštění pomocí následujícího příkazu:
   
   `/usr/local/vbin/azsecd config -s clamav -d Disabled`
   
1. Přidejte úlohu Cron, která spouští následující příkaz jako root:
   
   `/usr/local/bin/azsecd manual -s clamav`

Další informace o nastavení a spuštění úlohy cronu naleznete v [tématu Jak nastavím úlohu Cron](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)?

### <a name="why-is-llap-available-on-spark-esp-clusters"></a>Proč je LLAP k dispozici v clusterech Spark ESP?
V clusterech ESP Spark je LLAP povolena z bezpečnostních důvodů (tj. Apache Ranger), nikoli výkonu. Měli byste použít větší virtuální počítače uzlů pro použití prostředků LLAP (například minimální D13V2). 

### <a name="how-can-i-add-additional-aad-groups-after-creating-an-esp-cluster"></a>Jak lze přidat další skupiny AAD po vytvoření clusteru ESP?
Existují dva způsoby, jak toho dosáhnout: 1- Můžete znovu vytvořit cluster a přidat další skupinu v době vytvoření clusteru. Pokud používáte synchronizaci s vymezeným oborem v aad-ds, ujistěte se, že skupina B je součástí synchronizace s vymezeným oborem.
2 - Přidejte skupinu jako vnořenou podskupinu předchozí skupiny, která byla použita k vytvoření clusteru ESP. Pokud jste například vytvořili cluster ESP se skupinou `A` `B` , můžete později `A` přidat skupinu jako vnořenou podskupinu a přibližně po jedné hodině bude synchronizována a k dispozici v clusteru automaticky. 

## <a name="storage"></a>Úložiště

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>Můžu přidat Azure Data Lake Storage Gen2 do existujícího clusteru HDInsight jako další účet úložiště?

Ne, v současné době není možné přidat účet úložiště Azure Data Lake Storage Gen2 do clusteru, který má úložiště objektů blob jako primární úložiště. Další informace naleznete v [tématu Porovnání možností úložiště](hdinsight-hadoop-compare-storage-options.md).

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Jak najdu aktuálně propojený objekt zabezpečení služeb pro účet úložiště datového jezera?

Nastavení najdete v **přístupu K datová jezera Gen1** pod vlastnostmi clusteru na webu Azure Portal. Další informace naleznete v tématu [Ověření nastavení clusteru](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#verify-cluster-set-up).
 
### <a name="how-can-i-calculate-the-usage-of-storage-accounts-and-blob-containers-for-my-hdinsight-clusters"></a>Jak lze vypočítat využití účtů úložiště a kontejnerů objektů blob pro clustery HDInsight?

Proveďte jednu z těchto akcí:

- [Použití prostředí PowerShell](../storage/scripts/storage-blobs-container-calculate-size-powershell.md)

- Najděte velikost */user/hive/. Koš/* složka v clusteru HDInsight pomocí následujícího příkazového řádku:
  
  `hdfs dfs -du -h /user/hive/.Trash/`

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>Jak můžu nastavit auditování pro svůj účet úložiště objektů blob?

Pokud chcete auditovat účty úložiště objektů blob, nakonfigurujte monitorování pomocí postupu sledování [účtu úložiště na webu Azure Portal](../storage/common/storage-monitor-storage-account.md). Protokol auditu HDFS poskytuje pouze auditování informací pouze pro místní souborový systém HDFS (hdfs://mycluster).  Nezahrnuje operace, které jsou prováděny ve vzdáleném úložišti.

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>Jak můžu přenášet soubory mezi kontejnerem objektů blob a hlavním uzlem HDInsight?

Spusťte skript podobný následujícímu skriptu prostředí na hlavním uzlu:

```shell
for i in cat filenames.txt
do
   hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> Soubor *filenames.txt* bude mít absolutní cestu k souborům v kontejnerech objektů blob.
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>Existují nějaké Ranger pluginy pro skladování?

V současné době neexistuje žádný modul plug-in Ranger pro ukládání objektů blob a Azure Data Lake Storage Gen1 nebo Gen2. Pro clustery ESP byste měli používat Azure Data Lake Storage, protože můžete alespoň nastavit oprávnění jemné zrnitosti ručně na úrovni systému souborů pomocí nástrojů HDFS. Také při použití Azure Data Lake Storage, ESP clustery bude dělat některé řízení přístupu k systému souborů pomocí Azure Active Directory na úrovni clusteru. 

Zásady přístupu k datům můžete přiřadit skupinám zabezpečení vašich uživatelů pomocí Průzkumníka úložiště Azure. Další informace naleznete v tématu:

- [Jak nastavím oprávnění pro uživatele Azure AD k dotazování dat v Gen2 úložiště datového jezera pomocí Hive nebo jiných služeb?](hdinsight-hadoop-use-data-lake-storage-gen2.md#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
- [Nastavení oprávnění na úrovni souborů a adresářů pomocí Azure Storage Explorer u Azure Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>Je možné zvýšit úložiště HDFS v clusteru bez zvětšení velikosti disku pracovních uzlů?

Ne, nemůžete zvětšit velikost disku libovolného pracovního uzlu, takže jediný způsob, jak zvětšit velikost disku, je přetažení clusteru a jeho opětovné vytvoření pomocí větších pracovních virtuálních počítačů. Nepoužívejte HDFS pro ukládání všech dat HDInsight, protože data se odstraní, pokud odstraníte cluster. Místo toho uložte data v Azure. Škálování clusteru může také přidat další kapacitu do clusteru HDInsight.

## <a name="edge-nodes"></a>Hraniční uzly

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>Je možné přidat hraniční uzel po vytvoření clusteru?

HDInsight nebo do nového clusteru při vytváření clusteru. Další informace najdete v tématu [Použití prázdných hraničních uzlů v clusterech Apache Hadoop ve službě HDInsight](hdinsight-apps-use-edge-node.md).

### <a name="how-can-i-connect-to-an-edge-node"></a>Jak se mohu připojit k hraničnímu uzlu?

Po vytvoření hraničního uzlu se k němu můžete připojit pomocí SSH na portu 22. Název hraničního uzlu najdete na portálu clusteru. Názvy obvykle končí *-ed*.

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>Proč se trvalé skripty nespouštějí automaticky na nově vytvořených hraničních uzlech?

Trvalé skripty slouží k přizpůsobení nových pracovních uzlů přidaných do clusteru prostřednictvím operací škálování. Trvalé skripty se nevztahují na hraniční uzly.

## <a name="rest-api"></a>REST API

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>Jaké jsou volání rozhraní REST API pro vyprošťování zobrazení dotazu Tez z clusteru?

Můžete použít následující koncové body REST k vyžádat potřebné informace ve formátu JSON. K vyžádání použijte základní ověřovací hlavičky.

- Zobrazení dotazu Tez: *https:\//\<název clusteru>.azurehdinsight.net/ws/v1/timeline/HIVE_QUERY_ID/*
- Tez Dag View: *https:\//\<název clusteru>.azurehdinsight.net/ws/v1/timeline/TEZ_DAG_ID/*

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Jak načtem podrobnosti o konfiguraci z clusteru HDI pomocí uživatele služby Azure Active Directory?

Chcete-li vyjednat správné ověřovací tokeny s uživatelem služby AAD, projděte bránu pomocí následujícího formátu:

* https://`<cluster dnsname>`(azurehdinsight.net/api/v1/clusters/testclusterdem/stack_versions/1/repository_versions/1) 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Jak lze použít ambari restful API ke sledování výkonu YARN?

Pokud zavoláte příkaz Curl ve stejné virtuální síti nebo ve virtuální síti s partnerským vztahem, příkaz je:

```curl
curl -u <cluster login username> -sS -G
http://<headnodehost>:8080/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
```
 
Pokud voláte příkaz mimo virtuální síť nebo z virtuální sítě bez partnerského vztahu, formát příkazu je:

- Pro cluster bez ESP:
  
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
> Curl vás vyzve k zadání hesla. Je nutné zadat platné heslo pro přihlašovací uživatelské jméno clusteru.

## <a name="billing"></a>Fakturace

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>Kolik stojí nasazení clusteru HDInsight?

Další informace o cenách a nejčastějších dotazech týkajících se fakturace najdete na stránce [Ceny Azure HDInsight.](https://azure.microsoft.com/pricing/details/hdinsight/)

### <a name="when-does-hdinsight-billing-start--stop"></a>Kdy se fakturace HDInsight ustane &?

Účtování clusteru HDInsight začne vytvořením clusteru a skončí jeho odstraněním. Fakturace je poměrná za minutu.

### <a name="how-do-i-cancel-my-subscription"></a>Jak zruším předplatné?

Informace o zrušení předplatného najdete v tématu [Zrušení předplatného Azure](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription).

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>Co se stane u předplatných s průběžnými platbami po zrušení předplatného?

Informace o předplatném po jeho zrušení najdete v tématu [Co se stane po zrušení předplatného?](/azure/billing/billing-how-to-cancel-azure-subscription)

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-i-am-running-an-hdinsight-36-cluster"></a>Proč se verze Hive zobrazuje jako 1.2.1000 místo 2.1 v uzdu Ambari, i když používám cluster HDInsight 3.6?

Přestože se v uzdviavém rozhraní Ambari zobrazuje pouze 1.2, hdinsight 3.6 obsahuje Hive 1.2 i Hive 2.1.

## <a name="other-faq"></a>Další nejčastější dotazy

### <a name="what-does-hdinsight-offer-in-terms-of-real-time-stream-processing-capabilities"></a>Co HDInsight nabízí, pokud jde o možnosti zpracování datových proudů v reálném čase?

Informace o možnostech integrace zpracování datových proudů v Azure HDInsight najdete v [tématu výběr technologie zpracování datového proudu v Azure](/azure/architecture/data-guide/technology-choices/stream-processing).

### <a name="is-there-a-way-to-dynamically-terminate-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>Existuje způsob, jak dynamicky ukončit hlavní uzel clusteru, když je cluster nečinný po určitou dobu?

To nelze provést pomocí clusterů HDInsight. Pro tyto scénáře můžete použít Azure Data Factory.

### <a name="what-compliance-offerings-does-hdinsight-offer"></a>Jaké nabídky dodržování předpisů hdinsight nabízí?

Informace o dodržování předpisů najdete v [Centru zabezpečení Microsoft u](https://www.microsoft.com/trust-center) [AN a Přehled dodržování předpisů microsoft azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).
