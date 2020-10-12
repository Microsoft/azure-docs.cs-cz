---
title: Správa protokolů pro cluster HDInsight – Azure HDInsight
description: Určete typy, velikosti a zásady uchovávání souborů protokolu aktivit HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/05/2020
ms.openlocfilehash: e279f0ba5186ae4e4ad4b403ad823a59ee085170
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88997553"
---
# <a name="manage-logs-for-an-hdinsight-cluster"></a>Správa protokolů pro cluster HDInsight

Cluster An HDInsight vytváří různé soubory protokolů. Například Apache Hadoop a související služby, jako je například Apache Spark, poskytují podrobné protokoly spuštění úloh. Správa souborů protokolu je součástí údržby clusteru HDInsight, který je v pořádku. Pro archivaci protokolů můžou být také zákonné požadavky.  Vzhledem k počtu a velikosti souborů protokolu optimalizuje úložiště protokolů a archivace a pomáhá se správou nákladů na služby.

Správa protokolů clusteru HDInsight zahrnuje uchovávání informací o všech aspektech prostředí clusteru. Tyto informace zahrnují všechny přidružené protokoly služby Azure, konfiguraci clusteru, informace o spuštění úlohy, všechny stavy chyb a další data podle potřeby.

Typický postup ve správě protokolů HDInsight:

* Krok 1: určení zásad uchovávání protokolů
* Krok 2: Správa protokolů konfigurace verzí clusterových služeb
* Krok 3: Správa souborů protokolu spouštění úloh clusteru
* Krok 4: velikost a náklady úložiště svazků protokolů prognózy
* Krok 5: určení zásad a procesů archivu protokolů

## <a name="step-1-determine-log-retention-policies"></a>Krok 1: určení zásad uchovávání protokolů

Prvním krokem při vytváření strategie správy protokolů clusteru HDInsight je shromáždění informací o obchodních scénářích a požadavcích na úložiště historie spouštění úloh.

### <a name="cluster-details"></a>Podrobnosti o clusteru

Následující podrobnosti o clusteru jsou užitečné při nasnazším shromažďování informací v strategii správy protokolů. Shromážděte tyto informace ze všech clusterů HDInsight, které jste vytvořili v konkrétním účtu Azure.

* Název clusteru
* Oblast clusteru a zóna dostupnosti Azure
* Stav clusteru, včetně podrobností o poslední změně stavu
* Typ a počet instancí služby HDInsight zadaných pro uzly Master, Core a Task

Většinu těchto informací nejvyšší úrovně můžete získat pomocí Azure Portal.  Alternativně můžete pomocí [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) získat informace o clusterech HDInsight:

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

K zobrazení těchto informací můžete také použít PowerShell.  Další informace najdete v tématu [Správa clusterů Hadoop ve službě HDInsight pomocí Azure PowerShell](hdinsight-administer-use-powershell.md).

### <a name="understand-the-workloads-running-on-your-clusters"></a>Pochopení úloh spuštěných ve vašich clusterech

Je důležité pochopit typy úloh, které jsou spuštěny v clusterech HDInsight a navrhovat vhodné strategie protokolování pro každý typ.

* Jsou úlohy experimentální (například vývoj nebo testování) nebo kvalita produkce?
* Jak často se normálně spouštějí úlohy provozní kvality?
* Jsou některé úlohy náročné na prostředky a/nebo dlouho spuštěné?
* Chcete, aby všechny úlohy používaly komplexní sadu služeb Hadoop, pro které se vytvářely různé typy protokolů?
* Má kterákoli z úloh přiřazené požadavky na linku spuštění regulativního programu?

### <a name="example-log-retention-patterns-and-practices"></a>Příklady způsobů uchovávání protokolů a postupy

* Zvažte udržení sledování datových linií přidáním identifikátoru ke každé položce protokolu nebo jinými technikami. To vám umožňuje sledovat původní zdroj dat a operaci a sledovat data v jednotlivých fázích a porozumět jejich konzistenci a platnosti.

* Vezměte v úvahu, jak můžete shromažďovat protokoly z clusteru nebo z více než jednoho clusteru a třídit je pro účely, jako je auditování, monitorování, plánování a upozorňování. Můžete použít vlastní řešení pro přístup k souborům protokolu a jejich stažení v pravidelných intervalech a kombinovat je a analyzovat pro zobrazení řídicího panelu. Můžete také přidat další funkce pro upozorňování na zabezpečení nebo detekci selhání. Tyto nástroje můžete vytvořit pomocí PowerShellu, sad SDK HDInsight nebo kódu, který přistupuje k modelu nasazení Azure Classic.

* Zvažte, jestli řešení nebo služba monitorování budou užitečnou výhodou. Microsoft System Center poskytuje [Management Pack HDInsight](https://systemcenter.wiki/?Get_ManagementPackBundle=Microsoft.HDInsight.mpb&FileMD5=10C7D975C6096FFAA22C84626D211259). K shromažďování a centralizaci protokolů můžete použít také nástroje třetích stran, jako je Apache Chukwa a uzlin. Mnoho společností nabízí služby pro monitorování řešení pro velké objemy dat založené na systému Hadoop, například: centrování, Compuware APM, Sematext správce SPM a Zettaset Orchestrator.

## <a name="step-2-manage-cluster-service-versions-and-view-logs"></a>Krok 2: Správa verzí clusterových služeb a zobrazení protokolů

Typický cluster HDInsight používá několik služeb a open source softwarových balíčků (například Apache HBA, Apache Spark a tak dále). U některých úloh, jako je například Bioinformatics, může být nutné zachovat historii protokolu konfigurace služby kromě protokolů spouštění úloh.

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Zobrazení nastavení konfigurace clusteru pomocí uživatelského rozhraní Ambari

Apache Ambari zjednodušuje správu, konfiguraci a monitorování clusteru HDInsight poskytnutím webového uživatelského rozhraní a REST API. Ambari je součástí clusterů HDInsight se systémem Linux. Výběrem podokna **řídicí panel clusteru** na stránce Azure Portal HDInsight otevřete stránku odkazy na **řídicí panely clusteru** .  V dalším kroku vyberte podokno **řídicí panel clusteru HDInsight** a otevřete uživatelské rozhraní Ambari.  Zobrazí se výzva k zadání přihlašovacích údajů clusteru.

Chcete-li otevřít seznam zobrazení služeb, vyberte podokno **zobrazení Ambari** na stránce Azure Portal pro HDInsight.  Tento seznam se liší v závislosti na tom, které knihovny jste nainstalovali.  Můžete například zobrazit správce front PŘÍZ, zobrazení podregistru a tez zobrazení.  Kliknutím na libovolný odkaz služby zobrazíte informace o konfiguraci a službě.  Stránka zásobník uživatelského rozhraní **a verze** Ambari poskytuje informace o historii konfigurace a verze služby clusterových služeb. Pokud chcete přejít do této části uživatelského rozhraní Ambari, vyberte nabídku **správce** a pak nastavte **zásobníky a verze**.  Kliknutím na kartu **verze** zobrazíte informace o verzi služby.

![Sada pro správu Apache Ambari a verze](./media/hdinsight-log-management/ambari-stack-versions.png)

Pomocí uživatelského rozhraní Ambari můžete stáhnout konfiguraci pro všechny (nebo všechny) služby, které běží na konkrétním hostiteli (nebo uzlu) v clusteru.  Vyberte nabídku **hostitelé** a potom odkaz na hostitele, který vás zajímá. Na stránce tohoto hostitele vyberte tlačítko **akce hostitele** a pak **Stáhněte konfigurace klienta**.

![Konfigurace klientů hostitele pro stažení Apache Ambari](./media/hdinsight-log-management/download-client-configs.png)

### <a name="view-the-script-action-logs"></a>Zobrazit protokoly akcí skriptů

[Akce skriptu](hdinsight-hadoop-customize-cluster-linux.md) HDInsight spouštějí skripty v clusteru, a to buď ručně, nebo podle zadání. Například akce skriptu lze použít k instalaci dalšího softwaru do clusteru nebo ke změně nastavení konfigurace z výchozích hodnot. Protokoly akcí skriptů mohou poskytnout přehled o chybách, ke kterým došlo během instalace clusteru, a také změny nastavení konfigurace, které mohou ovlivnit výkon a dostupnost clusteru.  Pokud chcete zobrazit stav akce skriptu, klikněte na tlačítko **OPS** v uživatelském rozhraní Ambari nebo přejděte k protokolům stavu ve výchozím účtu úložiště. Protokoly úložiště jsou k dispozici na adrese `/STORAGE_ACCOUNT_NAME/DEFAULT_CONTAINER_NAME/custom-scriptaction-logs/CLUSTER_NAME/DATE` .

### <a name="view-ambari-alerts-status-logs"></a>Zobrazit protokoly stavu výstrah Ambari

Apache Ambari zapisuje změny stavu výstrahy do `ambari-alerts.log` . Úplná cesta je `/var/log/ambari-server/ambari-alerts.log` . Chcete-li povolit ladění protokolu, změňte vlastnost v `/etc/ambari-server/conf/log4j.properties.` nabídce změnit na položku `# Log alert state changes` z:

```
log4j.logger.alerts=INFO,alerts

to

log4j.logger.alerts=DEBUG,alerts
```

## <a name="step-3-manage-the-cluster-job-execution-log-files"></a>Krok 3: Správa souborů protokolu spuštění úlohy clusteru

Dalším krokem je kontrola souborů protokolu spuštění úlohy pro různé služby.  Služby můžou zahrnovat Apache HBA, Apache Spark a spoustu dalších. Cluster Hadoop vytvoří velký počet podrobných protokolů, takže určíte, které protokoly jsou užitečné (a které nejsou) můžou být časově náročné.  Princip protokolovacího systému je důležitý pro cílovou správu souborů protokolu.  Následující obrázek je příkladem souboru protokolu.

![Příklad výstupu ukázkového souboru protokolu HDInsight](./media/hdinsight-log-management/hdi-log-file-example.png)

### <a name="access-the-hadoop-log-files"></a>Přístup k souborům protokolu Hadoop

HDInsight ukládá své soubory protokolu jak v systému souborů clusteru, tak v Azure Storage. Soubory protokolu v clusteru můžete prozkoumávat otevřením připojení [SSH](hdinsight-hadoop-linux-use-ssh-unix.md) ke clusteru a procházením systému souborů nebo pomocí portálu stavu Hadoop příz na vzdáleném serveru hlavního serveru. Soubory protokolu v Azure Storage můžete prozkoumávat pomocí kteréhokoli z nástrojů, které mají přístup k datům z Azure Storage a stahovat je. Příklady jsou [AzCopy](../storage/common/storage-use-azcopy.md), [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer)a Visual Studio Průzkumník serveru. K přístupu k datům v úložišti objektů BLOB v Azure můžete také použít PowerShell a klientské knihovny Azure Storage nebo sady Azure .NET SDK.

Hadoop spouští práci s úlohami jako se *pokusy* v různých uzlech clusteru. HDInsight může iniciovat spekulativní pokusy o úlohy a ukončit všechny ostatní pokusy o úlohy, které se nejdřív nedokončují. Tím dojde k průběžné zaznamenání významné aktivity, která se protokoluje do souborů protokolů protokolu stderr a syslog. Kromě toho se současně spouští více pokusů o úlohy, ale soubor protokolu může zobrazit pouze lineární výsledky.

#### <a name="hdinsight-logs-written-to-azure-blob-storage"></a>Protokoly HDInsight zapsané do Azure Blob Storage

Clustery HDInsight jsou nakonfigurované tak, aby zapisovaly protokoly úloh do účtu služby Azure Blob Storage pro všechny úlohy, které se odesílají pomocí rutin Azure PowerShell nebo rozhraní API pro odeslání úloh .NET.  Pokud odešlete úlohy přes SSH do clusteru, pak se v tabulkách Azure ukládají informace o protokolování spuštění, jak je popsáno v předchozí části.

Kromě základních souborů protokolu generovaných službou HDInsight, instalované služby, jako je například PŘÍZe, generují také soubory protokolu spouštění úloh.  Počet a typ souborů protokolu závisí na nainstalovaných službách.  Mezi běžné služby patří Apache HBA, Apache Spark a tak dále.  Prozkoumejte soubory spuštění protokolu úlohy pro každou službu, abyste pochopili celkový počet souborů protokolování dostupných v clusteru.  Každá služba má své vlastní jedinečné metody protokolování a umístění pro ukládání souborů protokolu.  V následující části jsou popsány podrobné informace o přístupu k nejběžnějším souborům protokolu služby (z PŘÍZe).

### <a name="hdinsight-logs-generated-by-yarn"></a>Protokoly HDInsight generované pomocí PŘÍZe

PŘÍZe agreguje protokoly napříč všemi kontejnery v pracovním uzlu a ukládá tyto protokoly jako jeden agregovaný soubor protokolu na pracovní uzel. Tento protokol je po dokončení aplikace uložen ve výchozím systému souborů. Vaše aplikace může používat stovky nebo tisíce kontejnerů, ale protokoly pro všechny kontejnery, které jsou spuštěny v jednom pracovním uzlu, jsou vždy agregovány do jediného souboru. U každého pracovního uzlu používaného vaší aplikací se používá jenom jeden protokol. Agregace protokolů je ve výchozím nastavení povolená v clusterech HDInsight verze 3,0 a vyšší. Agregované protokoly jsou umístěny ve výchozím úložišti clusteru.

```
/app-logs/<user>/logs/<applicationId>
```

Agregované protokoly nejsou přímo čitelné, protože jsou napsány v binárním formátu TFile indexovaném kontejnerem. Použijte protokoly PŘÍZe ResourceManager nebo nástroje CLI k zobrazení těchto protokolů jako prostý text pro aplikace nebo kontejnery, které vás zajímají.

#### <a name="yarn-cli-tools"></a>Nástroje rozhraní příkazového řádku PŘÍZ

Chcete-li použít nástroje rozhraní příkazového řádku PŘÍZ, musíte se nejprve připojit ke clusteru HDInsight pomocí protokolu SSH. Zadejte `<applicationId>` informace, `<user-who-started-the-application>` , `<containerId>` a `<worker-node-address>` při spuštění těchto příkazů. Protokoly můžete zobrazit jako prostý text jedním z následujících příkazů:

```bash
yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
```

#### <a name="yarn-resourcemanager-ui"></a>Uživatelské rozhraní Správce prostředků PŘÍZe

Uživatelské rozhraní Správce prostředků PŘÍZe běží na hlavním uzlu clusteru a je k němu přistupované prostřednictvím webového uživatelského rozhraní Ambari. K zobrazení protokolů PŘÍZe použijte následující postup:

1. Ve webovém prohlížeči přejděte na adresu `https://CLUSTERNAME.azurehdinsight.net`. Nahraďte CLUSTERNAME názvem clusteru HDInsight.
2. V seznamu služeb vlevo vyberte možnost PŘÍZe.
3. V rozevíracím seznamu rychlé odkazy vyberte jeden z hlavních uzlů clusteru a pak vyberte **protokoly ResourceManager**. Zobrazí se seznam odkazů na záznamy PŘÍZe.

## <a name="step-4-forecast-log-volume-storage-sizes-and-costs"></a>Krok 4: velikost a náklady úložiště svazků protokolů prognózy

Po dokončení předchozích kroků budete vědět o typech a svazcích souborů protokolu, které vytváří clustery HDInsight.

V dalším kroku Analyzujte objem dat protokolu v umístěních protokolu klíčů úložiště v časovém intervalu. Můžete například analyzovat objem a nárůst mezi 30-60-90 dny.  Poznamenejte si tyto informace v tabulce nebo použijte jiné nástroje, jako je Visual Studio, Průzkumník služby Azure Storage nebo Power Query pro Excel. Další informace najdete v tématu [Analýza protokolů HDInsight](hdinsight-debug-jobs.md).  

Teď máte dostatek informací, abyste vytvořili strategii správy protokolů pro protokoly klíčů.  Použijte svoji tabulku (nebo nástroj podle vlastního výběru), abyste mohli předpovědět jak růst velikost protokolu, tak i náklady na službu Azure Storage, které se budou přesměrovávat.  Zvažte také všechny požadavky na uchovávání protokolů pro sadu protokolů, které zkoumáte.  Nyní můžete přepovědět budoucí náklady na úložiště protokolů, a to tak, že určíte, které soubory protokolu se můžou odstranit (pokud existují) a které protokoly se mají uchovávat a archivovat na levnější Azure Storage.

## <a name="step-5-determine-log-archive-policies-and-processes"></a>Krok 5: určení zásad a procesů archivu protokolů

Jakmile určíte, které soubory protokolu je možné odstranit, můžete upravit parametry protokolování v mnoha službách Hadoop tak, aby se soubory protokolu po zadaném časovém období automaticky odstranily.

U určitých souborů protokolu můžete použít přístup k archivaci souborů protokolu nižší ceny. V případě Azure Resource Manager protokolů aktivit můžete tento přístup prozkoumat pomocí Azure Portal.  Kliknutím na odkaz **Protokol aktivit** v Azure Portal pro instanci služby HDInsight nastavte archivaci protokolů správce prostředků.  V horní části stránky hledání protokolu aktivit vyberte položku nabídky **exportovat** a otevřete podokno **Protokol aktivit exportu** .  Vyplňte v předplatném, oblasti, jestli se má exportovat do účtu úložiště, a kolik dní si zachováte protokoly. V tomto stejném podokně můžete také určit, jestli se má exportovat do centra událostí.

![Náhled Azure Portal exportovat protokol aktivit](./media/hdinsight-log-management/hdi-export-log-files.png)

Případně můžete skript archivovat pomocí prostředí PowerShell.  Ukázkový skript PowerShellu najdete v tématu [archivace protokolů Azure Automation do Azure Blob Storage](https://gallery.technet.microsoft.com/scriptcenter/Archive-Azure-Automation-898a1aa8).

### <a name="accessing-azure-storage-metrics"></a>Přístup k metrikám Azure Storage

Azure Storage lze nakonfigurovat tak, aby protokoloval operace a přístup k úložišti. Tyto velmi podrobné protokoly můžete použít k monitorování a plánování kapacity a k auditování požadavků do úložiště. Protokolované informace obsahují podrobnosti o latenci a umožňují vám monitorovat a vyladit výkon vašich řešení.
Sadu .NET SDK pro Hadoop můžete použít k prohlédnutí souborů protokolu generovaných pro Azure Storage, které obsahují data pro cluster HDInsight.

### <a name="control-the-size-and-number-of-backup-indexes-for-old-log-files"></a>Řízení velikosti a počtu indexů zálohování pro staré soubory protokolu

Chcete-li řídit velikost a počet uchovávaných souborů protokolu, nastavte následující vlastnosti `RollingFileAppender` :

* `maxFileSize` je kritická velikost souboru, nad kterou se soubor zavedl. Výchozí hodnota je 10 MB.
* `maxBackupIndex` Určuje počet záložních souborů, které mají být vytvořeny, výchozí 1.

### <a name="other-log-management-techniques"></a>Další metody správy protokolů

Abyste se vyhnuli nedostatku místa na disku, můžete ke správě zpracování souborů protokolu použít některé nástroje operačního systému, jako je [logrotate](https://linux.die.net/man/8/logrotate) . Nástroj můžete nakonfigurovat `logrotate` tak, aby běžel každý den, aby se komprimují soubory protokolu a odstranily staré. Váš přístup závisí na vašich požadavcích, například na tom, jak dlouho chcete uchovávat soubory protokolů na místních uzlech.  

Můžete také ověřit, zda je povoleno protokolování ladění pro jednu nebo více služeb, což značně zvýší velikost výstupního protokolu.  

Chcete-li shromáždit protokoly ze všech uzlů do jednoho centrálního umístění, můžete vytvořit tok dat, například ingestování všech záznamů protokolu do Solr.

## <a name="next-steps"></a>Další kroky

* [Postupy monitorování a protokolování pro HDInsight](https://msdn.microsoft.com/library/dn749790.aspx)
* [Přístup k protokolům aplikace Apache Hadoop nitě v HDInsight se systémem Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Jak řídit velikost souborů protokolu pro různé Apache Hadoop komponenty](https://community.hortonworks.com/articles/8882/how-to-control-size-of-log-files-for-various-hdp-c.html)
