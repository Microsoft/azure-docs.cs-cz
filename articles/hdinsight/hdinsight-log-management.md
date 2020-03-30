---
title: Správa protokolů pro cluster HDInsight – Azure HDInsight
description: Určete typy, velikosti a zásady uchovávání informací pro soubory protokolu aktivit HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/05/2020
ms.openlocfilehash: 8c3cbf4c18b32a94abfe95e77be768020b44fda6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272302"
---
# <a name="manage-logs-for-an-hdinsight-cluster"></a>Správa protokolů pro cluster HDInsight

Cluster HDInsight vytváří celou řadu souborů protokolu. Například Apache Hadoop a související služby, jako je Apache Spark, vytvářejí podrobné protokoly provádění úloh. Správa souborů protokolu je součástí údržby clusteru HDInsight v pořádku. Mohou existovat také regulační požadavky pro archivaci protokolů.  Vzhledem k počtu a velikosti souborů protokolu pomáhá optimalizace ukládání protokolů a archivace se správou nákladů na služby.

Správa protokolů clusteru HDInsight zahrnuje uchování informací o všech aspektech prostředí clusteru. Tyto informace zahrnují všechny přidružené protokoly služby Azure, konfiguraci clusteru, informace o spuštění úlohy, všechny chybové stavy a další data podle potřeby.

Typické kroky ve správě protokolu HDInsight jsou:

* Krok 1: Určení zásad uchovávání informací protokolu
* Krok 2: Správa protokolů konfigurace verzí clusterových služeb
* Krok 3: Správa souborů protokolu spuštění úloh clusteru
* Krok 4: Prognóza velikosti a nákladů na objem úložiště svazků protokolu
* Krok 5: Určení zásad a procesů archivu protokolů

## <a name="step-1-determine-log-retention-policies"></a>Krok 1: Určení zásad uchovávání informací protokolu

Prvním krokem při vytváření strategie správy protokolu clusteru HDInsight je shromažďování informací o obchodních scénářích a požadavcích na úložiště historie provádění úloh.

### <a name="cluster-details"></a>Podrobnosti o clusteru

Následující podrobnosti o clusteru jsou užitečné při shromažďování informací ve strategii správy protokolů. Shromážděte tyto informace ze všech clusterů HDInsight, které jste vytvořili v konkrétním účtu Azure.

* Název clusteru
* Oblast clusteru a zóna dostupnosti Azure
* Stav clusteru, včetně podrobností o poslední změně stavu
* Typ a počet instancí HDInsight určených pro hlavní, základní a pracovní uzly

Většinu těchto informací nejvyšší úrovně můžete získat pomocí portálu Azure.  Případně můžete pomocí [rozhraní příkazového příkazu Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) získat informace o clusterech HDInsight):

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Tyto informace můžete zobrazit také pomocí prostředí PowerShell.  Další informace najdete [v tématu Apache Správa clusterů Hadoop v HDInsight pomocí Azure PowerShell](hdinsight-administer-use-powershell.md).

### <a name="understand-the-workloads-running-on-your-clusters"></a>Principy úloh spuštěných ve vašich clusterech

Je důležité porozumět typům úloh spuštěným v clusterech HDInsight a navrhnout vhodné strategie protokolování pro každý typ.

* Jsou úlohy experimentální (například vývoj nebo test) nebo kvalita výroby?
* Jak často se úlohy produkční kvality obvykle spouštějí?
* Jsou některá úloha náročná na prostředky a/nebo dlouhotrvající?
* Používá některá úloha komplexní sadu služeb Hadoop, pro které se vytváří více typů protokolů?
* Má některá z úloh přidružené požadavky na linii provádění předpisů?

### <a name="example-log-retention-patterns-and-practices"></a>Příklad vzorců a postupů uchovávání protokolů

* Zvažte zachování sledování datového původu přidáním identifikátoru ke každé položce protokolu nebo jinými technikami. To umožňuje trasovat zpět původní zdroj dat a operace a sledovat data prostřednictvím každé fáze pochopit jejich konzistence a platnosti.

* Zvažte, jak můžete shromažďovat protokoly z clusteru nebo z více než jednoho clusteru a shromažďovat je pro účely, jako je auditování, monitorování, plánování a výstrahy. Můžete použít vlastní řešení pro přístup a stahování souborů protokolu v pravidelných intervalech a kombinovat a analyzovat je poskytnout zobrazení řídicího panelu. Můžete také přidat další funkce pro výstrahy pro zjišťování zabezpečení nebo selhání. Tyto nástroje můžete vytvořit pomocí prostředí PowerShell, sady HDInsight SDK nebo kódu, který přistupuje k klasickému modelu nasazení Azure.

* Zvažte, zda by bylo užitečné řešení monitorování nebo služba. Microsoft System Center poskytuje [sadu Management Pack HDInsight](https://www.microsoft.com/download/details.aspx?id=42521). Můžete také použít nástroje třetích stran, jako je Apache Chukwa a Ganglia shromažďovat a centralizovat protokoly. Mnoho společností nabízí služby pro sledování řešení big data založených na Hadoopu, například: Centerity, Compuware APM, Sematext SPM a Zettaset Orchestrator.

## <a name="step-2-manage-cluster-service-versions-and-view-logs"></a>Krok 2: Správa verzí clusterových služeb a zobrazení protokolů

Typický cluster HDInsight používá několik služeb a open source softwarových balíčků (například Apache HBase, Apache Spark a tak dále). U některých úloh, jako je například bioinformatika, může být nutné zachovat historii protokolu konfigurace služby kromě protokolů spuštění úlohy.

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Zobrazení nastavení konfigurace clusteru pomocí uzla Ambari

Apache Ambari zjednodušuje správu, konfiguraci a monitorování clusteru HDInsight tím, že poskytuje webové uživatelské rozhraní a ROZHRANÍ REST API. Ambari je součástí linuxových clusterů HDInsight. Vyberte **podokno Řídicí panel clusteru** na stránce HDInsight portálu Azure a otevřete stránku odkazu **Řídicí panely clusteru.**  Dále vyberte podokno **řídicího panelu clusteru HDInsight** a otevřete ui Ambari.  Zobrazí se výzva k zadání přihlašovacích údajů k clusteru.

Pokud chcete otevřít seznam zobrazení služeb, vyberte podokno **Zobrazení Ambari** na stránce portálu Azure pro HDInsight.  Tento seznam se liší v závislosti na tom, které knihovny jste nainstalovali.  Můžete se například zobrazit Správce front YARN, Zobrazení hive a zobrazení Tez.  Chcete-li zobrazit informace o konfiguraci a službě, vyberte libovolný odkaz na službu.  Stránka **Zásobník a verze** umbari poskytuje informace o historii konfigurace a verzí clusterových služeb. Chcete-li přejít do této části uživatelského rozhraní Ambari, vyberte nabídku **Správce** a potom **položku Zásobníky a verze**.  Chcete-li zobrazit informace o verzi **služby,** vyberte kartu Verze.

![Apache Ambari admin Zásobník a verze](./media/hdinsight-log-management/ambari-stack-versions.png)

Pomocí uzlového rozhraní Ambari můžete stáhnout konfiguraci pro všechny (nebo všechny) služby spuštěné na určitém hostiteli (nebo uzlu) v clusteru.  Vyberte nabídku **Hosts** a pak odkaz pro hostitele zájmu. Na stránce tohoto hostitele vyberte tlačítko **Akce hostitele** a potom **stáhněte konfigurace klienta**.

![Apache Ambari stáhnout hostitelského klienta configs](./media/hdinsight-log-management/download-client-configs.png)

### <a name="view-the-script-action-logs"></a>Zobrazení protokolů akcí skriptu

Akce [skriptu](hdinsight-hadoop-customize-cluster-linux.md) HDInsight spouštějí skripty v clusteru, a to buď ručně, nebo pokud jsou zadány. Akce skriptu lze například použít k instalaci dalšího softwaru do clusteru nebo ke změně nastavení konfigurace z výchozích hodnot. Protokoly akcí skriptu mohou poskytnout přehled o chybách, ke kterým došlo při instalaci clusteru, a také změny nastavení konfigurace, které by mohly ovlivnit výkon a dostupnost clusteru.  Chcete-li zobrazit stav akce skriptu, vyberte tlačítko **ops** v uzlech Ambari nebo získejte přístup k protokolům stavu ve výchozím účtu úložiště. Protokoly úložiště jsou `/STORAGE_ACCOUNT_NAME/DEFAULT_CONTAINER_NAME/custom-scriptaction-logs/CLUSTER_NAME/DATE`k dispozici na adrese .

### <a name="view-ambari-alerts-status-logs"></a>Zobrazit protokoly stavu výstrah Ambari

Apache Ambari zapisuje změny stavu výstrahy do `ambari-alerts.log`. Úplná cesta `/var/log/ambari-server/ambari-alerts.log`je . Chcete-li povolit ladění protokolu, změňte `/etc/ambari-server/conf/log4j.properties.` vlastnost v `# Log alert state changes` změnit pak položku pod z:

```
log4j.logger.alerts=INFO,alerts

to

log4j.logger.alerts=DEBUG,alerts
```

## <a name="step-3-manage-the-cluster-job-execution-log-files"></a>Krok 3: Správa souborů protokolu spuštění úloh clusteru

Dalším krokem je kontrola souborů protokolu spuštění úlohy pro různé služby.  Služby mohou zahrnovat Apache HBase, Apache Spark a mnoho dalších. Cluster Hadoop vytváří velké množství podrobné protokoly, takže určení, které protokoly jsou užitečné (a které nejsou) může být časově náročné.  Pochopení systému protokolování je důležité pro cílenou správu souborů protokolu.  Následující obrázek je ukázkový soubor protokolu.

![Ukázkový výstup ukázkového souboru protokolu HDInsight](./media/hdinsight-log-management/hdi-log-file-example.png)

### <a name="access-the-hadoop-log-files"></a>Přístup k souborům protokolu Hadoop

HDInsight ukládá své soubory protokolu v systému souborů clusteru i ve službě Azure Storage. Soubory protokolu v clusteru můžete prozkoumat otevřením připojení [SSH](hdinsight-hadoop-linux-use-ssh-unix.md) ke clusteru a procházením systému souborů nebo pomocí portálu Stav Hadoop YARN na serveru vzdáleného hlavního uzlu. Soubory protokolu ve službě Azure Storage můžete prozkoumat pomocí libovolného nástroje, které mají přístup k datům z Azure Storage a stahovat je. Příklady jsou [AzCopy](../storage/common/storage-use-azcopy.md), [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer)a Průzkumník serveru Sady Visual Studio. K přístupu k datům v úložišti objektů blob Azure můžete taky použít PowerShell a knihovny klienta úložiště Azure nebo sady Azure .NET SDK.

Hadoop spouští práci úloh jako *pokusy o úlohu* na různých uzlech v clusteru. HDInsight můžete zahájit spekulativní úkol pokusy, ukončení jakékoli jiné pokusy o úkol, které nejsou dokončeny jako první. To generuje významné aktivity, která je zaznamenána do řadiče, stderr a syslog soubory protokolu on-the-fly. Kromě toho je současně spuštěno více pokusů o úlohu, ale soubor protokolu může zobrazit výsledky pouze lineárně.

#### <a name="hdinsight-logs-written-to-azure-blob-storage"></a>Protokoly HDInsight zapsané do úložiště objektů blob Azure

Clustery HDInsight jsou nakonfigurované pro zápis protokolů úloh do účtu úložiště objektů Blob Azure pro všechny úlohy, které jsou odeslány pomocí rutin Azure PowerShell nebo rozhraní API pro odesílání úloh .NET.  Pokud odešlete úlohy prostřednictvím SSH do clusteru, pak informace protokolování spuštění se uloží v azure tabulky, jak je popsáno v předchozí části.

Kromě základních souborů protokolu generovaných HDInsight, nainstalované služby, jako je Například YARN také generovat soubory protokolu spuštění úlohy.  Počet a typ souborů protokolu závisí na nainstalované služby.  Běžné služby jsou Apache HBase, Apache Spark a tak dále.  Prozkoumejte soubory spuštění protokolu úloh pro každou službu, abyste pochopili celkové soubory protokolování, které jsou k dispozici v clusteru.  Každá služba má své vlastní jedinečné metody protokolování a umístění pro ukládání souborů protokolu.  Jako příklad podrobnosti pro přístup k nejběžnější soubory protokolu služby (z YARN) jsou popsány v následující části.

### <a name="hdinsight-logs-generated-by-yarn"></a>Protokoly HDInsight generované yarn

YARN agreguje protokoly napříč všemi kontejnery na pracovní uzel a ukládá tyto protokoly jako jeden agregovaný soubor protokolu na pracovní uzel. Tento protokol je po dokončení aplikace uložen ve výchozím systému souborů. Vaše aplikace může používat stovky nebo tisíce kontejnerů, ale protokoly pro všechny kontejnery, které jsou spuštěny na jednom pracovním uzlu jsou vždy agregovány do jednoho souboru. Je pouze jeden protokol na pracovní uzel používaný vaší aplikací. Agregace protokolů je ve výchozím nastavení povolena v clusterech HDInsight verze 3.0 a vyšší. Agregované protokoly jsou umístěny ve výchozím úložišti pro cluster.

```
/app-logs/<user>/logs/<applicationId>
```

Agregované protokoly nejsou přímo čitelné, protože jsou zapsány v binárním formátu TFile indexovaném kontejnerem. Pomocí protokolů YARN ResourceManager nebo nástrojů cli zobrazit tyto protokoly jako prostý text pro aplikace nebo kontejnery zájmu.

#### <a name="yarn-cli-tools"></a>Nástroje CLI příze

Chcete-li použít nástroje YARN CLI, musíte se nejprve připojit ke clusteru HDInsight pomocí SSH. Při spuštění `<containerId>`těchto `<worker-node-address>` příkazů zadejte , , a informace. `<applicationId>` `<user-who-started-the-application>` Protokoly můžete zobrazit jako prostý text pomocí jednoho z následujících příkazů:

```bash
yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
```

#### <a name="yarn-resourcemanager-ui"></a>UI Správce prostředků yARN

Uživatelské nastavení YARN ResourceManager běží na hlavním uzlu clusteru a je přístupné prostřednictvím webového uživatelského uživatelského nastavení Ambari. Pomocí následujících kroků zobrazte protokoly YARN:

1. Ve webovém prohlížeči přejděte na adresu `https://CLUSTERNAME.azurehdinsight.net`. Nahraďte CLUSTERNAME názvem clusteru HDInsight.
2. Ze seznamu služeb vlevo vyberte YARN.
3. V rozevíracím seznamu Rychlé odkazy vyberte jeden z hlavních uzlů clusteru a pak vyberte **protokoly ResourceManager**. Zobrazí se seznam odkazů na protokoly YARN.

## <a name="step-4-forecast-log-volume-storage-sizes-and-costs"></a>Krok 4: Prognóza velikosti a nákladů na objem úložiště svazků protokolu

Po dokončení předchozích kroků rozumíte typům a svazkům souborů protokolu, které vaše clustery HDInsight vytvářejí.

Dále analyzujte objem dat protokolu v umístění chudinských úložišť klíčů po určitou dobu. Můžete například analyzovat objem a růst během období 30-60-90 dnů.  Tyto informace zaznamenejte do tabulky nebo použijte jiné nástroje, jako je Visual Studio, Průzkumník úložiště Azure nebo Power Query pro Excel. Další informace naleznete v [tématu Analyze HDInsight logs](hdinsight-debug-jobs.md).  

Nyní máte dostatek informací k vytvoření strategie správy protokolu pro protokoly klíčů.  Pomocí tabulky (nebo nástroje volby) můžete předpovědět růst velikosti protokolu i náklady na služby Azure v úložišti protokolů do budoucna.  Zvažte také všechny požadavky na uchovávání protokolu pro sadu protokolů, které zkoumáte.  Nyní můžete znovu prohlásit budoucí náklady na úložiště protokolu po určení, které soubory protokolu lze odstranit (pokud existuje) a které protokoly by měly být zachovány a archivovány do levnějšího úložiště Azure.

## <a name="step-5-determine-log-archive-policies-and-processes"></a>Krok 5: Určení zásad a procesů archivu protokolů

Po určení, které soubory protokolu lze odstranit, můžete upravit parametry protokolování u mnoha služeb Hadoop a automaticky odstranit soubory protokolu po zadaném časovém období.

U některých souborů protokolu můžete použít přístup k archivaci souborů protokolu za nižší ceny. Pro protokoly aktivit Azure Resource Manager, můžete prozkoumat tento přístup pomocí portálu Azure.  Nastavte archivaci protokolů Správce prostředků výběrem odkazu **Protokol aktivit** na portálu Azure pro vaši instanci HDInsight.  V horní části vyhledávací stránky protokolu aktivit vyberte položku **nabídky Export,** chcete-li otevřít podokno **Protokolu aktivit exportu.**  Vyplňte předplatné, oblast, zda exportovat do účtu úložiště a kolik dní chcete zachovat protokoly. V tomto stejném podokně můžete také určit, zda se má exportovat do centra událostí.

![Náhled protokolu exportu aktivit na portálu Azure](./media/hdinsight-log-management/hdi-export-log-files.png)

Případně můžete skriptovat archivaci protokolů pomocí prostředí PowerShell.  Příklad skriptu PowerShellu najdete v [tématu Archivování protokolů Azure Automation do azure blob storage](https://gallery.technet.microsoft.com/scriptcenter/Archive-Azure-Automation-898a1aa8).

### <a name="accessing-azure-storage-metrics"></a>Přístup k metrikám úložiště Azure

Azure Storage můžete nakonfigurovat pro protokolování operací úložiště a přístup. Tyto velmi podrobné protokoly můžete použít pro monitorování kapacity a plánování a pro auditování požadavků do úložiště. Protokolované informace obsahují podrobnosti o latenci, které umožňují sledovat a dolaďovat výkon vašich řešení.
Pomocí sady .NET SDK pro Hadoop můžete prozkoumat soubory protokolu generované pro úložiště Azure, které obsahuje data pro cluster HDInsight.

### <a name="control-the-size-and-number-of-backup-indexes-for-old-log-files"></a>Řízení velikosti a počtu indexů záloh pro staré soubory protokolu

Chcete-li řídit velikost a počet uchovávaných `RollingFileAppender`souborů protokolu, nastavte následující vlastnosti :

* `maxFileSize`je kritická velikost souboru, nad kterým je soubor vrácen. Výchozí hodnota je 10 MB.
* `maxBackupIndex`určuje počet záložních souborů, které mají být vytvořeny, výchozí 1.

### <a name="other-log-management-techniques"></a>Další techniky správy protokolů

Chcete-li se vyhnout nedostatku místa na disku, můžete použít některé nástroje operačního systému, jako je [logrotate](https://linux.die.net/man/8/logrotate) pro správu zpracování souborů protokolu. Můžete nakonfigurovat `logrotate` tak, aby běžel na denní bázi, komprimovat soubory protokolu a odstranit staré. Váš přístup závisí na vašich požadavcích, například jak dlouho zachovat soubory protokolu na místních uzlech.  

Můžete také zkontrolovat, zda je povoleno protokolování ladění pro jednu nebo více služeb, což výrazně zvyšuje velikost výstupního protokolu.  

Chcete-li shromažďovat protokoly ze všech uzlů do jednoho centrálního umístění, můžete vytvořit tok dat, jako je například ingestování všech položek protokolu do Solr.

## <a name="next-steps"></a>Další kroky

* [Praxe monitorování a protokolování pro HDInsight](https://msdn.microsoft.com/library/dn749790.aspx)
* [Přístup k protokolům aplikací Apache Hadoop YARN v Linuxu založeném na HDInsightu](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Jak řídit velikost souborů protokolu pro různé komponenty Apache Hadoop](https://community.hortonworks.com/articles/8882/how-to-control-size-of-log-files-for-various-hdp-c.html)
