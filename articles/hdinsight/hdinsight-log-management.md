---
title: Správa protokolů pro cluster služby HDInsight - Azure HDInsight | Microsoft Docs
description: Určení typů, velikosti a zásady uchovávání informací pro soubory protokolů aktivity HDInsight.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: ashishth
ms.openlocfilehash: 165464bd6851bf5fa76632731455722c393060c6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="manage-logs-for-an-hdinsight-cluster"></a>Správa protokolů pro cluster HDInsight

HDInsight cluster vytvoří různé soubory protokolu. Například Apache Hadoop a související služby, jako je například Apache Spark, vytvořit protokoly spouštění podrobné úlohy. Správa souborů protokolu je součástí zachování pořádku clusteru HDInsight. Také lze zákonné požadavky pro archivaci protokolu.  Z důvodu počet a velikost souborů protokolu služby optimalizace úložiště protokolů a archivaci pomáhá s náklady na správu.

Správa protokolů clusteru HDInsight zahrnuje informace o všech aspektech prostředí clusteru zůstanou zachovány. Tyto informace zahrnují všechny přidružené protokoly služby Azure, konfiguraci clusteru, informací o provádění úloh, všechny chybové stavy a další data podle potřeby.

Obvyklé kroky v Správa protokolu HDInsight jsou:

* Krok 1: Určení protokolu zásady uchovávání informací
* Krok 2: Správa clusteru služby verze konfigurace protokolů
* Krok 3: Správa souborů protokolu spuštění úlohy clusteru
* Krok 4: Prognózy velikosti svazku úložiště protokolu a náklady
* Krok 5: Určení protokolu archivu zásadami a procesy

## <a name="step-1-determine-log-retention-policies"></a>Krok 1: Určení protokolu zásady uchovávání informací

Prvním krokem při vytváření strategie správy protokolu clusteru HDInsight se má shromažďovat informace o obchodní scénáře a požadavky na úložiště historie provádění úlohy.

### <a name="cluster-details"></a>Podrobnosti o clusteru

Následující podrobnosti o clusteru jsou užitečné pro snazší shromažďovat informace do strategie správy protokolu. Shromáždění těchto informací ze všech clusterů HDInsight, které jste vytvořili v konkrétní účet Azure.

* Název clusteru
* Oblasti a zóny Azure dostupnost clusteru
* Stav clusteru, včetně podrobností o poslední změna stavu
* Typ a počet instancí HDInsight zadané pro hlavní, základní a uzly úloh

Můžete získat většinu těchto nejvyšší úrovně informací pomocí portálu Azure.  Alternativně můžete použít rozhraní příkazového řádku Azure k získání informací o váš cluster nebo clustery HDInsight:

```
    azure hdinsight cluster list
    azure hdinsight cluster show <ClusterName>
```

Chcete-li zobrazit tyto informace můžete také použít PowerShell.  Další informace najdete v tématu [Správa clusterů systému Hadoop v HDInsight pomocí prostředí Azure PowerShell](hdinsight-administer-use-powershell.md).

### <a name="understand-the-workloads-running-on-your-clusters"></a>Pochopení úlohami spuštěnými v clusterech služby

Je důležité si uvědomit, typy úloh, které jsou spuštěné na váš cluster nebo clustery HDInsight návrhu odpovídající protokolování pro každý typ strategií.

* Jsou úlohy experimentální (například vývoj nebo testování) nebo produkční kvality?
* Jak často úlohy produkční kvality normálně spustit?
* Jsou některé z úloh náročných nebo dlouhotrvající?
* Pomocí některé z úloh komplexní sadu služby Hadoop, pro které se vytváří více typy protokolů
* Některé úlohy přidruženy regulačních provádění rodokmenu požadavky?

### <a name="example-log-retention-patterns-and-practices"></a>Příklad protokolu uchování vzory a postupy

* Vezměte v úvahu zachování rodokmenu dat sledování přidáním identifikátor každé položky protokolu, nebo prostřednictvím jiné techniky. To umožňuje vysledovat zpět původního zdroje dat, a operaci a postupujte podle dat přes každé fáze pochopit jeho konzistence a platnosti.

* Zvažte, jak můžete shromažďování protokolů z clusteru, nebo z více než jednoho clusteru a collate je pro účely, například auditování, monitorování, plánování a výstrahy. Přístup a stáhnout soubory protokolů v pravidelných intervalech a zkombinovat a analyzovat, aby slouží k zobrazení řídicího panelu můžete použít vlastní řešení. Můžete také přidat další možnosti pro generování výstrah pro zabezpečení nebo detekce chyb. Můžete vytvořit tyto nástroje pomocí prostředí PowerShell, sady SDK HDInsight nebo kód, který přistupuje k modelu nasazení Azure classic.

* Zvažte, zda monitorování řešení nebo služby by být užitečné výhody. Poskytuje nástroje Microsoft System Center [HDInsight sady management pack](https://www.microsoft.com/download/details.aspx?id=42521). Můžete taky nástroje třetích stran, jako je Chukwa a Ganglia ke sběru a centralizovat protokoly. Mnoho společností nabízejí služby monitorování řešení pro velká data na základě Hadoop, třeba Centerity, Compuware APM, Sematext SPM a Zettaset Orchestrator.

## <a name="step-2-manage-cluster-service-versions-and-view-script-action-logs"></a>Krok 2: Správa verzí služby clusteru a zobrazit protokoly akce skriptu

Typické clusteru HDInsight používá několik služeb a softwaru open-source balíčky (například Apache HBase, Apache Spark a tak dále). Pro některé úlohy, jako je například Bioinformatika může být nutné uchování historie protokolu konfigurace služby kromě protokoly spouštění úlohy.

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Zobrazit nastavení konfigurace clusteru pomocí uživatelského rozhraní Ambari

Apache Ambari zjednodušuje správu, konfiguraci a monitorování clusteru HDInsight tím, že poskytuje webového uživatelského rozhraní a rozhraní REST API. Ambari je obsažena v clusterech HDInsight se systémem Linux. Vyberte **řídicí panel clusteru** podokně na stránky portálu Azure HDInsight otevřete**' řídicí panely clusteru** odkaz stránky.  Potom vyberte **řídicí panel clusteru HDInsight** podokně otevřete uživatelské rozhraní Ambari.  Zobrazí se výzva k zadání pověření přihlášení clusteru.

Otevřete seznam služby zobrazení, vyberte **zobrazení Ambari** podokno na stránce portálu Azure pro HDInsight.  Tento seznam se liší, v závislosti na tom, které knihovny jste nainstalovali.  Může se zobrazit například správce front YARN, zobrazení Hive a zobrazení Tez.  Vyberte všechny služby odkaz zobrazíte informace o služby a konfiguraci.  Uživatelské rozhraní Ambari **zásobníku a verze** stránka obsahuje informace o konfiguraci služby clusteru a historie verzí služby. Přejděte na tento oddíl rozhraní Ambari, vyberte **správce** nabídce a potom **zásobníky a verze**.  Vyberte **verze** karty zobrazíte informace o verzi služby.

![Zásobník a verze](./media/hdinsight-log-management/stack-versions.png)

Pomocí uživatelského rozhraní Ambari, můžete si stáhnout konfigurace pro všechny (nebo všechny) běžící na konkrétním hostiteli (nebo uzel) v clusteru.  Vyberte **hostitele** nabídce a potom na odkaz pro hostitele, které vás zajímají. Na stránce tohoto hostitele, vyberte **hostitele akce** tlačítko a potom **stáhnout konfigurací klienta**. 

![Konfigurací klienta hostitele](./media/hdinsight-log-management/client-configs.png)

### <a name="view-the-script-action-logs"></a>Zobrazit protokoly akcí skriptu

HDInsight [skript akce](hdinsight-hadoop-customize-cluster-linux.md) spouštění skriptů na cluster, buď ručně, nebo když zadán. Například skript akce lze instalovat další software clusteru nebo změnit nastavení konfigurace z výchozí hodnoty. Protokoly akcí skriptu můžete získat přehled o chybách, ke kterým došlo k chybě během instalace clusteru a také změny nastavení konfigurace, které mohou ovlivnit výkon clusteru a dostupnost.  Pokud chcete zobrazit stav akce skriptu, vyberte **ops** tlačítko na uživatelské rozhraní Ambari, nebo přístup stav přihlášení výchozí účet úložiště. Protokoly úložiště jsou dostupné v `/STORAGE_ACCOUNT_NAME/DEFAULT_CONTAINER_NAME/custom-scriptaction-logs/CLUSTER_NAME/DATE`.

## <a name="step-3-manage-the-cluster-job-execution-log-files"></a>Krok 3: Správa souborů protokolu spuštění úlohy clusteru

Dalším krokem je kontrola soubory protokolu spuštění úlohy pro různé služby.  Služby mohou zahrnovat Apache HBase, Apache Spark a mnohé další. Hadoop cluster vytváří velký počet podrobné protokoly, takže určování, které protokoly jsou užitečné (a které nejsou) může být časově náročná.  Principy protokolování systému je důležité pro cílové správu souborů protokolu.  Následuje příklad souboru protokolu.

![Příklad souboru protokolu HDInsight](./media/hdinsight-troubleshoot-failed-cluster/logs.png)

### <a name="access-the-hadoop-log-files"></a>Přístup k souborům protokolu Hadoop

HDInsight ukládá její soubory protokolu v clusteru systému souborů i v úložišti Azure. Soubory protokolu v clusteru můžete zkontrolovat otevřením připojení SSH do clusteru a procházení systému souborů nebo pomocí portálu Hadoop YARN stavu na serveru vzdáleného hlavního uzlu. Můžete zkontrolovat soubory protokolů v úložišti Azure pomocí některé z nástroje, které můžete používat a stahování dat z úložiště Azure. Příklady jsou AZCopy, CloudXplorer a Průzkumníka serveru Visual Studia. Můžete také pomocí prostředí PowerShell a knihovny klienta úložiště Azure nebo .NET sady Azure SDK pro přístup k datům v Azure blob storage.

Hadoop spouští pracovních úloh, jako *úkolů pokusy o* v různých uzlech v clusteru. HDInsight můžete zahájit pokusy o spekulativní úloh, ukončení ostatní pokusy úloh, které nejsou dokončeny nejdřív. Tím se vygeneruje výrazné aktivity, která je zaznamenána do řadiče, stderr a syslog protokolu soubory na průběžně. Kromě toho více pokusů úloha běží současně, ale soubor protokolu může zobrazit pouze výsledky lineárně.

#### <a name="hdinsight-logs-written-to-azure-tables"></a>HDInsight protokoly zapisují do tabulek Azure

Protokoly zapisují do tabulek Azure poskytují přehled o dění pomocí clusteru služby HDInsight. Při vytváření clusteru HDInsight se systémem Linux, šesti tabulky se automaticky vytvoří ve službě Table storage výchozí:

* hdinsightagentlog
* syslog
* daemonlog
* hadoopservicelog
* ambariserverlog
* ambariagentlog

#### <a name="hdinsight-logs-written-to-azure-blob-storage"></a>Protokoly HDInsight zapisovat do úložiště objektů Blob v Azure

Clustery HDInsight jsou konfigurována pro zápis protokolů úloh k účtu úložiště objektů Blob v Azure pro všechny úlohy, které je odeslána pomocí rutin prostředí Azure PowerShell nebo odeslání úlohy .NET API.  Pokud odešlete úloh prostřednictvím SSH do clusteru je popsané v předchozí části spuštění protokolování informace ukládat do tabulek Azure.

Kromě souborů jádra protokolu vygenerovaných HDInsight služby, jako je YARN také generovat souborů protokolu spuštění úlohy nainstalovány.  Počet a typ souborů protokolů závisí na službách nainstalována.  Obecné služby jsou Apache HBase, Apache Spark a tak dále.  Prozkoumejte soubory protokolu spuštění úlohy pro každou službu vám pomohou pochopit, že celkový protokolování soubory k dispozici v clusteru.  Každá služba má svůj vlastní jedinečný metody protokolování a umístění pro ukládání souborů protokolu.  Jako příklad jsou podrobnosti pro přístup k nejběžnější soubory protokolu služby (od YARN) popsané v následující části.

### <a name="hdinsight-logs-generated-by-yarn"></a>HDInsight protokoly YARN

YARN slučuje protokolů v rámci všech kontejnerů v pracovním uzlu a ukládá tyto protokoly jako jeden soubor protokolu agregované pro pracovního uzlu. Tento protokol je uložený na výchozí systém souborů po dokončení aplikace. Vaše aplikace může používat stovkami nebo tisíci kontejnery, ale protokoly pro všechny kontejnery, které jsou v jednom pracovního uzlu agregovány vždy pro jeden soubor. Není k dispozici pouze jeden protokol za pracovního uzlu používá vaše aplikace. Agregace protokolu je povoleno ve výchozím nastavení v clusterech HDInsight verze 3.0 nebo novějším. Agregované protokoly jsou umístěny ve výchozím nastavení úložiště pro cluster.

```
    /app-logs/<user>/logs/<applicationId>
```

Agregované protokoly nejsou přímo čitelný, jako jsou zapsány v binárním formátu TFile indexovat pomocí kontejneru. Chcete-li zobrazit tyto protokoly jako prostý text pro aplikace nebo kontejnerů, které vás zajímají použijte protokoly YARN ResourceManager nebo nástrojů příkazového řádku.

#### <a name="yarn-cli-tools"></a>YARN rozhraní příkazového řádku nástroje

Při použití nástroje pro YARN rozhraní příkazového řádku, je nutné se nejprve připojit ke clusteru HDInsight pomocí protokolu SSH. Zadejte `<applicationId>`, `<user-who-started-the-application>`, `<containerId>`, a `<worker-node-address>` informace při spuštění těchto příkazů. Protokoly můžete zobrazit jako prostý text s jedním z následujících příkazů:

```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
```

#### <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager uživatelského rozhraní

Rozhraní YARN ResourceManager běží na hlavního uzlu clusteru a je přístupné přes webovému uživatelskému rozhraní Ambari. Pokud chcete zobrazit protokoly YARN použijte následující kroky:

1. Ve webovém prohlížeči, přejděte na `https://CLUSTERNAME.azurehdinsight.net`. Nahraďte název clusteru s názvem clusteru HDInsight.
2. V seznamu služeb na levé straně vyberte YARN.
3. Z rozevíracího seznamu Rychlé odkazy, vyberte jeden z hlavních uzlech clusteru a pak vyberte **ResourceManager protokoly**. Zobrazí se seznam odkazů na protokoly YARN.

## <a name="step-4-forecast-log-volume-storage-sizes-and-costs"></a>Krok 4: Prognózy velikosti svazku úložiště protokolu a náklady

Po dokončení předchozích kroků, máte představu o typech a svazky soubory protokolů, které jsou generovala váš cluster nebo clustery HDInsight.

V dalším kroku analýza objem dat protokolu v umístění úložiště klíčů protokolů po určitou dobu. Například můžete analyzovat svazek a růstu více než 30 – 60-90 denní období.  Zaznamenejte tyto informace v tabulce nebo použít jiné nástroje, jako je například Visual Studio, Azure Storage Explorer nebo Power Query pro Excel. Další informace najdete v tématu [protokoly analyzovat HDInsight](hdinsight-debug-jobs.md).  

Nyní máte dostatek informací pro vytvoření strategie správy protokolu klíče protokolů.  Pomocí tabulky (nebo nástroje) prognózy obou nárůst velikosti protokolu a protokolu náklady na úložiště služby Azure do budoucna.  Zvažte také veškeré protokolu uchování požadavky pro sadu protokoly, které je zkoumán.  Nyní můžete reforecast budoucí protokolu náklady na úložiště, po určení, které soubory protokolů můžete odstranit (pokud existuje) a protokoly, které by měl být uchována a archivovat do levnějšího úložiště Azure.

## <a name="step-5-determine-log-archive-policies-and-processes"></a>Krok 5: Určení protokolu archivu zásadami a procesy

Po určení, které soubory protokolů můžete odstranit, můžete upravit parametry protokolování na mnoho služby Hadoop po zadaném časovém období automaticky odstranit soubory protokolu.

Pro některé soubory protokolu můžete použít nižší cenou soubor protokolu archivace přístup. Protokoly aktivity Azure Resource Manager můžete prozkoumat tento přístup pomocí portálu Azure.  Nastavit archivace protokolů ARM výběrem **protokol aktivit**' odkaz na portálu Azure HDInsight instance.  Nahoře na stránce Protokol aktivit vyhledávání, vyberte **exportovat** položku otevřete **Export protokolu aktivit** podokně.  Vyplňte předplatné, oblast, jestli se mají exportovat do účtu úložiště a jak dlouho se má tyto protokoly uchovávat. V tomto podokně stejné můžete také určit, jestli chcete exportovat do centra událostí. 

![Exportovat soubory protokolu](./media/hdinsight-log-management/archive.png)

Alternativně můžete skript, archivaci protokolu pomocí prostředí PowerShell.  Příklad skriptu prostředí PowerShell najdete v tématu [archivu Azure Automation protokoluje do Azure Blob Storage](https://gallery.technet.microsoft.com/scriptcenter/Archive-Azure-Automation-898a1aa8).

### <a name="accessing-azure-storage-metrics"></a>Přístup k metrik Azure storage

Úložiště Azure můžete nakonfigurovat operací protokolu úložiště a přístup. Tato velmi podrobné protokoly můžete použít pro monitorování a plánování kapacity a auditování požadavky na úložiště. Protokolované informace zahrnují latence podrobnosti, které umožňují monitorovat a vyladit výkon řešení.
.NET SDK pro Hadoop můžete použít k analýze souborů protokolů, které jsou generovány pro úložiště Azure, která obsahuje data pro cluster služby HDInsight.

### <a name="control-the-size-and-number-of-backup-indexes-for-old-log-files"></a>Řídí velikost a počet zálohování indexů pro staré soubory protokolu

Chcete-li řídit velikost a počet souborů protokolu uchována, nastavte následující vlastnosti `RollingFileAppender`:

* `maxFileSize` je důležité velikost souboru, nad kterou soubor vrácena. Výchozí hodnota je 10 MB.
* `maxBackupIndex` Určuje počet záložních souborů má být vytvořen výchozí 1.

### <a name="other-log-management-techniques"></a>Jiné postupy správy protokolu

Aby se zabránilo spouštění nedostatek místa na disku, můžete použít některé nástroje operačního systému, jako `logrotate` ke správě zpracování souborů protokolu. Můžete nakonfigurovat `logrotate` spouštět každý den, komprese protokolu souborů a odebírání staré. Váš přístup závisí na vaše požadavky, například jak dlouho zachovat žurnálů na místní uzly. 

Můžete také zkontrolovat, zda ladění je povoleno protokolování pro jednu nebo více služeb, který se značně zvyšuje velikost výstup protokolu. 

Chcete-li shromažďovat protokoly ze všech uzlů na jednom centrálním místě, můžete vytvořit tok dat, jako je například příjem všech položek protokolů do Solr.

## <a name="next-steps"></a>Další postup

* [Sledování a protokolování postupem pro HDInsight](https://msdn.microsoft.com/library/dn749790.aspx)
* [Přístup k protokolu aplikace YARN na HDInsight se systémem Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Tom, jak řídit velikost protokolových souborů pro různé součásti Hadoop](https://community.hortonworks.com/articles/8882/how-to-control-size-of-log-files-for-various-hdp-c.html)
