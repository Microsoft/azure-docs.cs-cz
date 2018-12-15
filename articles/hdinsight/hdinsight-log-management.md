---
title: Správa protokolů pro cluster HDInsight – Azure HDInsight
description: Určení typů, velikosti a zásady uchovávání informací pro soubory protokolů aktivit HDInsight.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/11/2018
ms.author: ashishth
ms.openlocfilehash: 9a76ad219e538874af04a72c9aa64e87a35bc53d
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53434881"
---
# <a name="manage-logs-for-an-hdinsight-cluster"></a>Správa protokolů pro cluster HDInsight

HDInsight cluster vytvoří různé soubory protokolu. Protokoly spouštění úlohy podrobné vytvořit například Apache Hadoop a souvisejících služeb, jako je Apache Spark. Správa souborů protokolu je součástí této údržby pořádku clusteru HDInsight. Také může existovat zákonné požadavky pro archivaci protokolu.  Z důvodu počet a velikost souborů protokolů optimalizace úložiště protokolů a archivace pomáhá s služby cost management.

Správa protokolů clusteru HDInsight zahrnuje informace o všech aspektech prostředí clusteru zůstanou zachovány. Tyto informace zahrnují veškeré související protokoly služby Azure, konfiguraci clusteru, informace o spuštění úlohy, všechny chybové stavy licencí a další data podle potřeby.

Typické postupy ve službě HDInsight log management jsou:

* Krok 1: Určení zásady uchovávání protokolů
* Krok 2: Správa clusteru service verze konfigurace protokolů
* Krok 3: Správa souborů protokolu úlohy spuštění clusteru
* Krok 4: Odhad velikosti svazku úložiště protokolů a nákladů
* Krok 5: Určení protokolu archivu zásadami a procesy

## <a name="step-1-determine-log-retention-policies"></a>Krok 1: Určení zásady uchovávání protokolů

Prvním krokem při vytváření strategie správy protokolu clusteru HDInsight je ke shromažďování informací o obchodní scénáře a požadavky na úložiště historie provádění úlohy.

### <a name="cluster-details"></a>Podrobnosti o clusteru

Následující podrobnosti o clusteru jsou užitečné při shromažďování informací ve vaší strategie správy protokolu. Shromažďování těchto informací ze všech clusterů HDInsight, které jste vytvořili v konkrétní účet Azure.

* Název clusteru
* Cluster oblasti a zóny dostupnosti Azure
* Stav clusteru, včetně podrobností o poslední změny stavu
* Typ a počet instancí HDInsight zadaný pro hlavní, core a úlohy uzly

Můžete získat většinu těchto nejvyšší úrovně informací pomocí webu Azure portal.  Alternativně můžete použít rozhraní příkazového řádku Azure Classic k získání informací o vašich clusterů HDInsight:

```
    azure hdinsight cluster list
    azure hdinsight cluster show <ClusterName>
```
[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

Chcete-li zobrazit tyto informace můžete také použít prostředí PowerShell.  Další informace najdete v tématu [clusterů systému Apache spravovat Hadoop v HDInsight pomocí Azure Powershellu](hdinsight-administer-use-powershell.md).

### <a name="understand-the-workloads-running-on-your-clusters"></a>Vysvětlení úloh, které běží na vašich clusterů

Je důležité porozumět typům úloh běží na vašich clusterů HDInsight navrhování odpovídající strategie pro každý typ protokolování.

* Jsou úlohy experimentální (například vývojové nebo testovací) nebo produkční kvality?
* Jak často produkční kvality úloh obvykle běží?
* Jsou všechny úlohy náročné a/nebo dlouhotrvající?
* Některé z úloh pomocí komplexní sady služby Hadoop, pro které jsou vytvářeny více typy protokolů?
* Některý z úlohy mají přidružené požadavky na dodržování legislativních provádění rodokmenu?

### <a name="example-log-retention-patterns-and-practices"></a>Příklad protokolu uchování vzory a postupy

* Vezměte v úvahu zachování rodokmenu dat tak, že přidáte identifikátor pro každý záznam protokolu nebo prostřednictvím jiné techniky pro sledování. To umožňuje vysledovat zpět na původní zdroj dat a operace a postupujte podle data každé fázi vám pomohou pochopit jeho konzistence a platnosti.

* Vezměte v úvahu, jak shromažďovat protokoly z clusteru, nebo z více než jeden cluster a kolaci pro účely, jako je auditování, plánování a sledování výstrah. Můžete použít vlastní řešení pro přístup k a stáhněte si soubory protokolů v pravidelných intervalech a kombinovat a analyzovat, aby poskytují zobrazení řídicího panelu. Můžete také přidat další funkce pro generování výstrah pro zabezpečení nebo detekce chyb. Můžete vytvořit tyto nástroje, pomocí prostředí PowerShell, sady HDInsight SDK nebo kód, který má přístup k modelu nasazení Azure classic.

* Vezměte v úvahu, jestli řešení monitorování nebo služba bude užitečné výhodu. Nástroje Microsoft System Center poskytuje [sady management pack HDInsight](https://www.microsoft.com/download/details.aspx?id=42521). Nástroje třetích stran, jako je Apache Chukwa a Ganglia můžete použít také ke shromáždění a centralizovat protokoly. Mnoho společností nabízet služby monitorování řešení pro velké objemy dat založenému na Hadoop, například: Centerity Compuware APM, Sematext SPM a Zettaset Orchestrator.

## <a name="step-2-manage-cluster-service-versions-and-view-script-action-logs"></a>Krok 2: Správa verzí služby clusteru a zobrazovat protokoly akce skriptu

Typické clusteru HDInsight používá několik služeb a open source softwaru (jako je například Apache HBase, Apache Spark a tak dále). Pro některé úlohy, jako je například Bioinformatika může být nutné zachování historie protokolu služby konfigurace kromě protokoly spouštění úlohy.

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Zobrazit nastavení konfigurace clusteru pomocí uživatelského rozhraní Ambari

Apache Ambari ve skupinách usnadňuje správu, konfiguraci a monitorování HDInsight cluster poskytují webové uživatelské rozhraní a rozhraní REST API. Ambari je zahrnuta v clusterech HDInsight založených na Linuxu. Vyberte **řídicí panel clusteru** podokně na stránky portálu Azure HDInsight a otevřete **"řídicí panely clusteru** stránka odkazu.  V dalším kroku vyberte **řídicí panel clusteru HDInsight** podokně otevřete uživatelské rozhraní Ambari.  Zobrazí se výzva pro přihlašovací údaje clusteru.

Pokud chcete otevřít seznam zobrazení, služby, vyberte **zobrazení Ambari** podokně na stránky portálu Azure pro HDInsight.  Tento seznam se liší, v závislosti na tom, které knihovny jste nainstalovali.  Může se zobrazit třeba správce fronty YARN, Hive zobrazení a zobrazení Tez.  Vyberte všechny služby odkaz zobrazíte konfiguraci a informace o službě.  Uživatelské rozhraní Ambari **zásobníku a verze** stránka obsahuje informace o konfiguraci Clusterové služby a historie verzí služby. Chcete-li přejít do této části uživatelského rozhraní Ambari, vyberte **správce** nabídky a pak **zásobníky a verze**.  Vyberte **verze** kartu pro zobrazení informací o verzi služby.

![Zásobník a verze](./media/hdinsight-log-management/stack-versions.png)

Pomocí uživatelského rozhraní Ambari, si můžete stáhnout konfiguraci pro všechny (nebo všechny) služby spuštěné na konkrétního hostitele (nebo uzel) v clusteru.  Vyberte **hostitele** nabídky a potom na odkaz pro hostitele, které vás zajímají. Na stránce, které hostují, vyberte **hostitele akce** tlačítko a pak **stažení konfigurace klienta**. 

![Konfigurace hostitele klienta](./media/hdinsight-log-management/client-configs.png)

### <a name="view-the-script-action-logs"></a>Zobrazit protokoly akce skriptu

HDInsight [akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md) spouštění skriptů v clusteru, zadat buď ručně, nebo když. Například akce skriptu lze použít k instalaci dalšího softwaru v clusteru nebo změnit nastavení konfigurace z výchozí hodnoty. Protokoly akcí skriptů můžete poskytují přehled o chybách, ke kterým došlo během instalace clusteru a také změny nastavení konfigurace, které by mohly ovlivnit clusteru výkon a dostupnost.  Chcete-li zobrazit stav akce skriptu, vyberte **ops** tlačítko na uživatelské rozhraní Ambari nebo přístup stav přihlášení výchozí účet úložiště. Protokoly úložiště najdete na adrese `/STORAGE_ACCOUNT_NAME/DEFAULT_CONTAINER_NAME/custom-scriptaction-logs/CLUSTER_NAME/DATE`.

## <a name="step-3-manage-the-cluster-job-execution-log-files"></a>Krok 3: Správa souborů protokolu spuštění úlohy clusteru

Dalším krokem je kontrola souborů protokolu spuštění úlohy pro různé služby.  Služby mohou zahrnovat Apache HBase, Apache Spark a mnoha dalších. Hadoop cluster vytvoří velký počet podrobné protokoly, takže určující, které protokoly jsou užitečné (a které nejsou) může být časově náročné.  Principy systému protokolování je důležité pro správu cílových souborů protokolů.  Následuje příklad souboru protokolu.

![Příklad souboru protokolu HDInsight](./media/hdinsight-troubleshoot-failed-cluster/logs.png)

### <a name="access-the-hadoop-log-files"></a>Soubory protokolů Hadoop

HDInsight ukládá soubory protokolu v systému souborů clusteru i ve službě Azure storage. Soubory protokolu v clusteru můžete zkontrolovat otevřením připojení SSH ke clusteru a procházení systému souborů nebo pomocí portálu Hadoop YARN stavu na serveru pro vzdálený hlavního uzlu. Můžete zkontrolovat soubory protokolů ve službě Azure storage pomocí některého nástroje, které můžete používat a stahovat data ze služby Azure storage. Příklady jsou AZCopy CloudXplorer a Průzkumníka serveru Visual Studia. Prostředí PowerShell a knihovny klienta úložiště Azure nebo Azure .NET SDK, můžete také použít pro přístup k datům ve službě Azure blob storage.

Hadoop spustí pracovní úlohy jako *úkolů pokusy* na různých uzlech v clusteru. HDInsight, může iniciovat pokusy o spuštění úkolu spekulativního, ukončuje se žádné další pokusy o spuštění úkolu, které nejprve dokončí. Tím se vygeneruje významnou aktivitu, která je zaznamenána řadič, stderr a syslog protokolu souborů v běhu. Kromě toho více pokusy o spuštění úkolu jsou spuštěny současně, ale soubor protokolu lze zobrazit pouze výsledky lineárně.

#### <a name="hdinsight-logs-written-to-azure-blob-storage"></a>HDInsight protokoly zapisují do úložiště objektů Blob v Azure

Clustery HDInsight jsou nakonfigurovány pro zápis protokolů úloh na účet úložiště objektů Blob v Azure pro úlohy, které je odeslána pomocí rutin Azure Powershellu nebo odeslání úlohy .NET API.  Pokud odešlete úlohy přes SSH ke clusteru se jak je popsáno v předchozí části informace o protokolování spuštění ukládají do tabulek Azure.

Kromě základní soubory protokolu vygenerované službou HDInsight nainstalované služby, jako je YARN také generovat soubory protokolu spuštění úlohy.  Počet a typ souborů protokolů závisí na službách nainstalované.  Běžné služby jsou Apache HBase, Apache Spark a tak dále.  Prozkoumejte soubory protokolu spuštění úlohy pro každou službu vám pomohou pochopit, že celkový protokolování souborů k dispozici ve vašem clusteru.  Každá služba má svůj vlastní jedinečný metody protokolování a umístění pro ukládání souborů protokolu.  Jako příklad najdete podrobnosti pro přístup k běžných souborů protokolu služby (od YARN) jsou popsány v následující části.

### <a name="hdinsight-logs-generated-by-yarn"></a>HDInsight protokoly generované YARN

YARN protokoly agreguje přes všechny kontejnery na pracovním uzlu a uloží tyto protokoly jako jeden soubor protokolu agregované podle počtu uzlů pracovního procesu. Tento protokol je uložen na výchozí systém souborů po dokončení aplikace. Vaše aplikace může používat stovek nebo tisíců kontejnerů, ale protokolů pro všechny kontejnery, které jsou spuštěny na jednoho pracovního uzlu se vždy agregují do jediného souboru. Existuje pouze jeden protokol na pracovní uzel používaný vaší aplikací. Agregace protokolu je povolené ve výchozím nastavení verze clustery HDInsight 3.0 a vyšší. Agregované protokoly jsou umístěny ve výchozím nastavení úložiště pro cluster.

```
    /app-logs/<user>/logs/<applicationId>
```

Agregované protokoly nejsou přímo čitelné, jak jsou psány v binárním formátu TFile indexovat pomocí kontejnerů. Chcete-li zobrazit tyto protokoly jako prostý text pro aplikace nebo kontejnery, které vás zajímají použijte protokoly YARN ResourceManager nebo nástroje rozhraní příkazového řádku.

#### <a name="yarn-cli-tools"></a>Nástroje rozhraní příkazového řádku YARN

Chcete-li využívat nástroje příkazového řádku YARN, se musí se poprvé připojíte ke clusteru HDInsight pomocí SSH. Zadejte `<applicationId>`, `<user-who-started-the-application>`, `<containerId>`, a `<worker-node-address>` informace při spuštění těchto příkazů. Zobrazení protokolů jako prostý text s jedním z následujících příkazů:

```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
```

#### <a name="yarn-resourcemanager-ui"></a>Uživatelské rozhraní správce prostředků YARN

Uživatelské rozhraní správce prostředků YARN běží na hlavního uzlu clusteru a je přístupný prostřednictvím webového uživatelského rozhraní Ambari. Chcete-li zobrazit protokoly YARN, postupujte následovně:

1. Ve webovém prohlížeči přejděte na `https://CLUSTERNAME.azurehdinsight.net`. CLUSTERNAME nahraďte názvem vašeho clusteru HDInsight.
2. V seznamu služeb na levé straně vyberte YARN.
3. Rychlé odkazy rozevíracího seznamu vyberte jednu z hlavní uzly clusteru a pak vyberte **ResourceManager protokoly**. Zobrazí se seznam odkazů na protokoly YARN.

## <a name="step-4-forecast-log-volume-storage-sizes-and-costs"></a>Krok 4: Odhad velikosti svazku úložiště protokolů a nákladů

Po dokončení předchozích kroků, budete mít pochopení typů a svazky protokolů, které vytvářejí vašich clusterů HDInsight.

Dále analyzujte objem dat protokolu v umístění úložiště klíčů protokolů po určitou dobu. Například můžete analyzovat objem a růst více než 30 – 60 – 90 denní období.  Poznamenejte si tyto údaje v tabulce nebo použít jiné nástroje, jako je Visual Studio, Průzkumníka služby Azure Storage nebo doplňku Power Query pro Excel. Další informace najdete v tématu [HDInsight analyzovat protokoly](hdinsight-debug-jobs.md).  

Nyní máte dostatek informací pro vytvoření protokolu strategie pro klíče protokoly.  Pomocí tabulky (nebo nástroj) prognózy obou nárůst velikosti protokolů a protokolů náklady na úložiště služby Azure do budoucna.  Zvažte také veškeré protokol uchovávání požadavky pro sadu protokolů, které se, že zkoumáte.  Nyní můžete reforecast budoucí protokolu náklady na úložiště, po určení, které soubory protokolů můžete odstranit, (pokud existuje) a které protokoly by měl být zachovává a archivovat do levnějšího úložiště Azure.

## <a name="step-5-determine-log-archive-policies-and-processes"></a>Krok 5: Určení protokolu archivu zásadami a procesy

Až zjistíte, které soubory protokolů můžete odstranit, můžete upravit parametry protokolování na mnoha službách Hadoop se automaticky odstranit soubory protokolu po zadaném časovém období.

Pro některé soubory protokolů můžete použít nízkonákladových soubor protokolu archivace přístup. Pro protokoly aktivit Azure Resource Manageru můžete prozkoumat tento přístup pomocí webu Azure portal.  Nastavit archivaci protokoly ARM tak, že vyberete **protokolu aktivit**"odkaz na webu Azure Portal pro vaši instanci HDInsight.  Nahoře na stránce Protokol aktivit vyhledávání, vyberte **exportovat** otevřete položku nabídky **exportovat protokol aktivit** podokně.  Zadejte předplatné, oblast, jestli se má exportovat do účtu úložiště a kolik dní na tyto protokoly uchovávat. V tomto podokně stejné lze také určit, jestli se má exportovat do centra událostí. 

![Exportovat soubory protokolů](./media/hdinsight-log-management/archive.png)

Alternativně můžete používat skripty pro archivaci protokolu pomocí prostředí PowerShell.  Například skript prostředí PowerShell najdete v části [archivu Azure Automation se zaznamená do Azure Blob Storage](https://gallery.technet.microsoft.com/scriptcenter/Archive-Azure-Automation-898a1aa8).

### <a name="accessing-azure-storage-metrics"></a>Přístup k metrik Azure storage

Úložiště Azure je možné nakonfigurovat operací protokolu úložiště a přístup. Velmi podrobné protokoly můžete použít pro monitorování a plánování kapacity a auditování požadavky na úložiště. Zaznamenané informace zahrnuje podrobnosti latencí, umožňuje sledovat a optimalizovat výkon vašeho řešení.
Sada .NET SDK pro Hadoop můžete použít k prozkoumání soubory protokolu vygenerované pro Azure storage, který obsahuje data pro HDInsight cluster.

### <a name="control-the-size-and-number-of-backup-indexes-for-old-log-files"></a>Nastavit velikost a počet záloh indexů pro staré soubory protokolu

K řízení velikosti a počtu souborů protokolů, které uchovávají, nastavte následující vlastnosti `RollingFileAppender`:

* `maxFileSize` je důležité velikost souboru, výše jsou vráceny souboru. Výchozí hodnota je 10 MB.
* `maxBackupIndex` Určuje počet záložních souborů má být vytvořen, výchozí hodnota: 1.

### <a name="other-log-management-techniques"></a>Další postupy správy protokolu

Aby se zabránilo spouštění volné místo na disku, můžete použít některé nástroje operačního systému, jako `logrotate` ke správě zpracování souborů protokolů. Můžete nakonfigurovat `logrotate` ke spuštění na každý den, komprese protokolu souborů a odstranění starých verzí. Váš přístup závisí na vaše požadavky, jako například jak dlouho pro zajištění, logfiles na místní uzly. 

Můžete také zkontrolovat, zda ladění je povoleno protokolování pro jeden nebo víc služeb, které podstatně zvýší velikost protokolu výstupu. 

Shromažďovat protokoly ze všech uzlů na jednom centrálním místě, můžete vytvořit tok dat, jako jsou například ingestování všech položek protokolů do Solr.

## <a name="next-steps"></a>Další postup

* [Monitorování a protokolování normy pro HDInsight](https://msdn.microsoft.com/library/dn749790.aspx)
* [Protokol aplikace přístup Apache Hadoop YARN v HDInsight se systémem Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Jak řídit velikost protokolových souborů pro různé součásti Apache Hadoop](https://community.hortonworks.com/articles/8882/how-to-control-size-of-log-files-for-various-hdp-c.html)
