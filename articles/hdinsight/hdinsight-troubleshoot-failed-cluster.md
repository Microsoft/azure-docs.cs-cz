---
title: Poradce při potížích s pomalou nebo selhávající úlohou v clusteru Azure HDInsight
description: Diagnostikujte a vyřešte problémy s pomalou nebo selhávající úlohou v clusteru Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: be991b63784a2c72a51bfbdc8506f3b4695ed6c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895318"
---
# <a name="troubleshoot-a-slow-or-failing-job-on-a-hdinsight-cluster"></a>Řešení potíží s pomalou úlohou na clusteru HDInsight nebo jejím selháním

Pokud aplikace zpracovávající data v clusteru HDInsight běží pomalu nebo selhává s kódem chyby, máte několik možností řešení potíží. Pokud vaše úlohy trvá déle, než bylo očekáváno, nebo se obecně zobrazují pomalé doby odezvy, může dojít k selhání před cházejícím u clusteru, jako jsou například služby, na kterých cluster běží. Nejčastější příčinou těchto zpomalení je však nedostatečné škálování. Při vytváření nového clusteru HDInsight vyberte příslušné [velikosti virtuálních strojů](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters).

Chcete-li diagnostikovat pomalý nebo selhávající cluster, shromážděte informace o všech aspektech prostředí, jako jsou přidružené služby Azure, konfigurace clusteru a informace o spuštění úloh. Užitečnou diagnostikou je pokusit se reprodukovat chybový stav v jiném clusteru.

* Krok 1: Shromážděte data o problému.
* Krok 2: Ověřte prostředí clusteru HDInsight.
* Krok 3: Zobrazení stavu clusteru.
* Krok 4: Zkontrolujte zásobník prostředí a verze.
* Krok 5: Zkontrolujte soubory protokolu clusteru.
* Krok 6: Zkontrolujte nastavení konfigurace.
* Krok 7: Reprodukovat selhání v jiném clusteru.

## <a name="step-1-gather-data-about-the-issue"></a>Krok 1: Shromažďování dat o problému

HDInsight poskytuje mnoho nástrojů, které můžete použít k identifikaci a řešení problémů s clustery. Následující kroky vás provedou těmito nástroji a poskytnou návrhy pro určení problému.

### <a name="identify-the-problem"></a>Identifikace problému

Chcete-li problém identifikovat, zvažte následující otázky:

* Co jsem čekal, že se stane? Co se místo toho stalo?
* Jak dlouho trvalo spuštění procesu? Jak dlouho by to mělo běžet?
* Mají moje úlohy v tomto clusteru vždy pomalé? Běželi rychleji v jiném clusteru?
* Kdy se tento problém poprvé vyskytl? Jak často se to od té doby stalo?
* Změnilo se něco v konfiguraci clusteru?

### <a name="cluster-details"></a>Podrobnosti o clusteru

Mezi důležité informace o clusteru patří:

* Název clusteru.
* Oblast clusteru – zkontrolujte [výpadky oblasti](https://azure.microsoft.com/status/).
* Typ a verze clusteru HDInsight.
* Typ a počet instancí HDInsight určených pro hlavní a pracovní uzly.

Portál Azure může poskytnout tyto informace:

![Informace o portálu HDInsight Azure](./media/hdinsight-troubleshoot-failed-cluster/hdi-azure-portal-info.png)

Můžete také použít [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest):

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Další možností je použití prostředí PowerShell. Další informace najdete [v tématu Správa clusterů Apache Hadoop ve službě HDInsight pomocí Azure PowerShellu](hdinsight-administer-use-powershell.md).

## <a name="step-2-validate-the-hdinsight-cluster-environment"></a>Krok 2: Ověření prostředí clusteru HDInsight

Každý cluster HDInsight závisí na různých službách Azure a na open source softwaru, jako jsou Apache HBase a Apache Spark. Clustery HDInsight můžou taky volat na jiné služby Azure, jako jsou virtuální sítě Azure.  Selhání clusteru může být způsobeno některou ze spuštěných služeb v clusteru nebo externí službou.  Změna konfigurace clusterové služby může také způsobit selhání clusteru.

### <a name="service-details"></a>Podrobnosti o službě

* Zkontrolujte verze knihovny s otevřeným zdrojovým kódem.
* Zkontrolujte [výpadky služeb Azure](https://azure.microsoft.com/status/).  
* Zkontrolujte limity využití služby Azure. 
* Zkontrolujte konfiguraci podsítě Virtuální sítě Azure.  

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Zobrazení nastavení konfigurace clusteru pomocí uzla Ambari

Apache Ambari poskytuje správu a monitorování clusteru HDInsight s webovým rozhraním a rozhraním REST API. Ambari je součástí linuxových clusterů HDInsight. Vyberte **podokno Řídicí panel clusteru** na stránce HDInsight portálu Azure.  Vyberte podokno **řídicího panelu clusteru HDInsight,** chcete-li otevřít uživatelské rozhraní Ambari, a zadejte přihlašovací údaje clusteru.  

![Přehled řídicího panelu Apache Ambari](./media/hdinsight-troubleshoot-failed-cluster/apache-ambari-overview.png)

Pokud chcete otevřít seznam zobrazení služeb, vyberte **zobrazení Ambari** na stránce portálu Azure.  Tento seznam závisí na tom, které knihovny jsou nainstalovány. Můžete se například zobrazit Správce front YARN, Zobrazení hive a zobrazení Tez.  Vyberte odkaz na službu, chcete-li zobrazit informace o konfiguraci a službě.

#### <a name="check-for-azure-service-outages"></a>Zkontrolujte výpadky služeb Azure

HDInsight spoléhá na několik služeb Azure. Spouští virtuální servery na Azure HDInsight, ukládá data a skripty do úložiště objektů Blob Azure nebo Azure Data Lake Storage a indexuje soubory protokolu v úložišti Azure Table. Přerušení těchto služeb, i když vzácné, může způsobit problémy v HDInsight. Pokud máte v clusteru neočekávané zpomalení nebo selhání, zkontrolujte [řídicí panel stavu Azure](https://azure.microsoft.com/status/). Stav každé služby je uveden podle oblasti. Zkontrolujte oblast clusteru a také oblasti pro všechny související služby.

#### <a name="check-azure-service-usage-limits"></a>Kontrola limitů využití služeb Azure

Pokud spouštíte velký cluster nebo jste spustili mnoho clusterů současně, cluster může selhat, pokud jste překročili limit služby Azure. Limity služeb se liší v závislosti na vašem předplatném Azure. Další informace najdete v tématu [Limity, kvóty a omezení předplatného a služeb Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).
Můžete požádat, aby Microsoft zvýšil počet dostupných prostředků HDInsight (například jádra virtuálních aplikací a instance virtuálních aplikací) pomocí [požadavku na zvýšení kvóty správce prostředků.](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)

#### <a name="check-the-release-version"></a>Kontrola verze vydání

Porovnejte verzi clusteru s nejnovější verzí HDInsight. Každá verze HDInsight obsahuje vylepšení, jako jsou nové aplikace, funkce, opravy a opravy chyb. Problém, který ovlivňuje váš cluster, byl pravděpodobně opraven v nejnovější verzi. Pokud je to možné, spusťte cluster pomocí nejnovější verze HDInsight a přidružených knihoven, jako je Apache HBase, Apache Spark a další.

#### <a name="restart-your-cluster-services"></a>Restartování clusterových služeb

Pokud dochází zpomalení ve vašem clusteru, zvažte restartování služeb prostřednictvím ui Ambari nebo Azure Classic CLI. Cluster může docházet k přechodným chybám a restartování je nejrychlejší způsob, jak stabilizovat prostředí a případně zlepšit výkon.

## <a name="step-3-view-your-clusters-health"></a>Krok 3: Zobrazení stavu clusteru

Clustery HDInsight se skládají z různých typů uzlů spuštěných na instancích virtuálních počítačů. Každý uzel lze sledovat z důvodu hladovění prostředků, problémů s připojením k síti a dalších problémů, které mohou zpomalit cluster. Každý cluster obsahuje dva hlavní uzly a většina typů clusteru obsahuje kombinaci pracovních a hraničních uzlů. 

Popis různých uzlů, které jednotlivé typy clusterů používají, najdete [v tématu Nastavení clusterů v HDInsightu pomocí Apache Hadoop, Apache Spark, Apache Kafka a dalších](hdinsight-hadoop-provision-linux-clusters.md).

Následující části popisují, jak zkontrolovat stav každého uzlu a celkového clusteru.

### <a name="get-a-snapshot-of-the-cluster-health-using-the-ambari-ui-dashboard"></a>Získání snímku stavu clusteru pomocí řídicího panelu ui Ambari

[Řídicí panel ui rozhraní Ambari](#view-cluster-configuration-settings-with-the-ambari-ui) (`https://<clustername>.azurehdinsight.net`) poskytuje přehled o stavu clusteru, jako je doba připojení, paměť, využití sítě a procesoru, využití disku HDFS a tak dále. Pomocí části Hosts v Ambari můžete zobrazit zdroje na úrovni hostitele. Můžete také zastavit a restartovat služby.

### <a name="check-your-webhcat-service"></a>Zkontrolujte službu WebHCat

Jeden běžný scénář pro Apache Hive, Apache Pig, nebo Apache Sqoop pracovních míst selhání je selhání se službou [WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md) (nebo *Templeton).* WebHCat je rozhraní REST pro vzdálené provádění úloh, jako je například Hive, Pig, Scoop a MapReduce. WebHCat přeloží žádosti o odeslání úlohy do aplikací Apache Hadoop YARN a vrátí stav odvozený ze stavu aplikace YARN.  Následující části popisují běžné stavové kódy HTTP WebHCat.

#### <a name="badgateway-502-status-code"></a>BadGateway (stavový kód 502)

Tento kód je obecná zpráva z uzlů brány a je nejběžnější kódy stavu selhání. Jednou z možných příčin je služba WebHCat, která je dole na aktivním hlavním uzlu. Chcete-li tuto možnost zkontrolovat, použijte následující příkaz CURL:

```bash
curl -u admin:{HTTP PASSWD} https://{CLUSTERNAME}.azurehdinsight.net/templeton/v1/status?user.name=admin
```

Ambari zobrazí výstrahu zobrazující hostitele, na kterých je služba WebHCat vypnutá. Můžete se pokusit obnovit službu WebHCat restartováním služby na jejím hostiteli.

![Apache Ambari Restartovat WebHCat server](./media/hdinsight-troubleshoot-failed-cluster/restart-webhcat-server.png)

Pokud server WebHCat stále není přijít, zkontrolujte, zda protokol operací pro chyby zprávy. Podrobnější informace naleznete v `stderr` `stdout` souborech a souborech, na které se odkazuje v uzlu.

#### <a name="webhcat-times-out"></a>Časový opojení webhcat

Služba HDInsight Gateway zapisuje časový čas `502 BadGateway`odpovědí, které trvá déle než dvě minuty a vracejí se . WebHCat dotazy YARN služby pro stavy úloh, a pokud YARN trvá déle než dvě minuty reagovat, může tento požadavek časový mzda.

V takovém případě zkontrolujte následující `/var/log/webhcat` protokoly v adresáři:

* **webhcat.log** je log4j log, do kterého server zapisuje protokoly
* **webhcat-console.log** je stdout serveru při spuštění
* **webhcat-console-error.log** je stderr procesu serveru

> [!NOTE]  
> Každý `webhcat.log` je převrácený denně, `webhcat.log.YYYY-MM-DD`generování souborů s názvem . Vyberte příslušný soubor pro časový rozsah, který zkoumáte.

Následující části popisují některé možné příčiny pro časové opony WebHCat.

##### <a name="webhcat-level-timeout"></a>Časový čas úrovně WebHCat

Když webHcat je pod zatížením, s více než 10 otevřených soketů, trvá déle vytvořit nové připojení soketu, což může mít za následek časový čas. Chcete-li vypsat síťová připojení `netstat` k webhcatu a zpět, použijte aktuální aktivní headnode:

```bash
netstat | grep 30111
```

30111 je port WebHCat poslouchá dál. Počet otevřených soketů by měl být menší než 10.

Pokud neexistují žádné otevřené sokety, předchozí příkaz nevede k výsledku. Chcete-li zkontrolovat, zda je Templeton na portu 30111, použijte:

```bash
netstat -l | grep 30111
```

##### <a name="yarn-level-timeout"></a>Časový čas úrovně YARN

Templeton volá YARN ke spuštění úlohy a komunikace mezi Templeton a YARN může způsobit časový odčasový čas.

Na úrovni YARN existují dva typy časových opovenek:

1. Odeslání úlohy YARN může trvat dost dlouho, aby způsobila časový čas.

    Pokud otevřete `/var/log/webhcat/webhcat.log` soubor protokolu a hledat "úlohy ve frontě", může se zobrazit více položek, kde doba provádění je příliš dlouhá (>2000 ms), s položkami zobrazujícími rostoucí čekací doby.

    Čas úloh ve frontě se nadále zvyšuje, protože rychlost, s jakou jsou nové úlohy odeslány, je vyšší než rychlost dokončení starých úloh. Jakmile je paměť YARN 100 % používána, *fronta joblauncher* již nemůže vypůjčit kapacitu z *výchozí fronty*. Proto žádné další nové úlohy mohou být přijaty do fronty joblauncheer. Toto chování může způsobit, že čekací doba bude delší a delší, což způsobuje chybu časového období, po které obvykle následuje mnoho dalších.

    Následující obrázek ukazuje frontu joblauncher na 714,4 % nadužíváno. To je přijatelné, pokud je stále volné kapacity ve výchozí frontě půjčit. Však při clusteru je plně využita a paměti YARN je na 100 % kapacity, nové úlohy musí počkat, což nakonec způsobí časové toky.

    ![Zobrazení fronty spouštěče úloh HDInsight](./media/hdinsight-troubleshoot-failed-cluster/hdi-job-launcher-queue.png)

    Tento problém lze vyřešit dvěma způsoby: buď snížit rychlost předkládání nových úloh, nebo zvýšit rychlost spotřeby starých úloh navýšením clusteru.

2. YARN zpracování může trvat dlouhou dobu, což může způsobit časové propojek.

    * Seznam všech úloh: Jedná se o časově náročné volání. Toto volání vyjmenovává aplikace z YARN ResourceManager a pro každou dokončenou aplikaci získá stav z YARN JobHistoryServer. S vyšším počtem úloh může tento hovor časový modus.

    * Seznam úloh starších než sedm dní: HDInsight YARN JobHistoryServer je`mapreduce.jobhistory.max-age-ms` nakonfigurován tak, aby uchovával informace o dokončené úloze po dobu sedmi dnů (hodnota). Pokus o vytvoření výčtu vyčištěných úloh má za následek časový čas.

Diagnostika těchto problémů:

1. Určení časového rozsahu UTC, který chcete řešit
2. Vyberte `webhcat.log` příslušné soubory
3. Podívejte se na zprávy WARN a ERROR během této doby

#### <a name="other-webhcat-failures"></a>Další selhání webhcatu

1. Stavový kód HTTP 500

    Ve většině případů, kde WebHCat vrátí 500, chybová zpráva obsahuje podrobnosti o selhání. V opačném `webhcat.log` případě vyhledejte zprávy WARN a ERROR.

2. Selhání úlohy

    Mohou existovat případy, kdy interakce s WebHCat jsou úspěšné, ale úlohy selhávají.

    Templeton shromažďuje výstup konzoly `stderr` `statusdir`úloh jako v , což je často užitečné pro řešení potíží. `stderr`obsahuje identifikátor aplikace YARN skutečného dotazu.

## <a name="step-4-review-the-environment-stack-and-versions"></a>Krok 4: Kontrola zásobníku prostředí a verzí

Stránka **Zásobník a verze** umbari poskytuje informace o konfiguraci clusterových služeb a historii verzí služby.  Nesprávné verze knihovny služeb Hadoop mohou být příčinou selhání clusteru.  V uživatelském rozhraní Ambari vyberte nabídku **Správce** a potom **položky Zásobníky a verze**.  Chcete-li zobrazit informace o verzi **služby,** vyberte na stránce kartu Verze:

![Apache Ambari zásobníku a verze](./media/hdinsight-troubleshoot-failed-cluster/ambari-stack-versions.png)

## <a name="step-5-examine-the-log-files"></a>Krok 5: Zkontrolujte soubory protokolu

Existuje mnoho typů protokolů, které jsou generovány z mnoha služeb a součástí, které tvoří cluster HDInsight. [Soubory protokolu WebHCat](#check-your-webhcat-service) jsou popsány výše. Existuje několik dalších užitečných souborů protokolu, které můžete prozkoumat zúžit problémy s clusterem, jak je popsáno v následujících částech.

* Clustery HDInsight se skládají z několika uzlů, z nichž většina má za úkol spouštět odeslané úlohy. Úlohy se spouštějí souběžně, ale soubory protokolu mohou zobrazovat výsledky pouze lineárně. HDInsight provádí nové úkoly a ukončuje ostatní, které se nepodaří dokončit jako první. Všechny tyto aktivity je `stderr` `syslog` zaznamenána do a soubory.

* Soubory protokolu akce skriptu zobrazují chyby nebo neočekávané změny konfigurace během procesu vytváření clusteru.

* Protokoly kroků Hadoop identifikují úlohy Hadoop ukterého, který byl spuštěn jako součást kroku obsahujícího chyby.

### <a name="check-the-script-action-logs"></a>Kontrola protokolů akcí skriptu

Akce [skriptu](hdinsight-hadoop-customize-cluster-linux.md) HDInsight spouštějí skripty v clusteru ručně nebo po zadání. Akce skriptu lze například použít k instalaci dalšího softwaru do clusteru nebo ke změně nastavení konfigurace z výchozích hodnot. Kontrola protokolů akcí skriptu může poskytnout přehled o chybách, ke kterým došlo při instalaci a konfiguraci clusteru.  Stav akce skriptu můžete zobrazit výběrem tlačítka **ops** v uzlech Ambari nebo přístupem k protokolům z výchozího účtu úložiště.

Protokoly akcí skriptu jsou `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` umístěny v adresáři.

### <a name="view-hdinsight-logs-using-ambari-quick-links"></a>Zobrazit protokoly HDInsight pomocí rychlých odkazů Ambari

Rozhraní HDInsight Ambari obsahuje řadu **sekcí rychlých odkazů.**  Chcete-li získat přístup k odkazům protokolu pro určitou službu v clusteru HDInsight, otevřete ui ambari pro váš cluster a vyberte odkaz na službu ze seznamu vlevo. Vyberte rozevírací přehled **Rychlé odkazy,** pak uzel zájmu HDInsight a pak vyberte odkaz pro přidružený protokol.

Například pro protokoly HDFS:

![Rychlé odkazy na soubory protokolu ambari](./media/hdinsight-troubleshoot-failed-cluster/apache-ambari-quick-links.png)

### <a name="view-hadoop-generated-log-files"></a>Zobrazit soubory protokolu generované hadoopem

Cluster HDInsight generuje protokoly, které jsou zapsány do tabulek Azure a úložiště objektů blob Azure. YARN vytvoří vlastní protokoly provádění. Další informace naleznete v [tématu Správa protokolů pro cluster HDInsight](hdinsight-log-management.md#access-the-hadoop-log-files).

### <a name="review-heap-dumps"></a>Zkontrolovat výpisy haldy

Výpisy haldy obsahují snímek paměti aplikace, včetně hodnot proměnných v té době, které jsou užitečné pro diagnostiku problémů, ke kterým dochází za běhu. Další informace naleznete v [tématu Enable haldy pro služby Apache Hadoop na Linux-založené HDInsight](hdinsight-hadoop-collect-debug-heap-dump-linux.md).

## <a name="step-6-check-configuration-settings"></a>Krok 6: Kontrola nastavení konfigurace

Clustery HDInsight jsou předem nakonfigurované s výchozím nastavením pro související služby, jako je Hadoop, Hive, HBase a tak dále. V závislosti na typu clusteru, jeho hardwarové konfiguraci, počtu uzlů, typech úloh, které spouštěte, a na datech, se kterými pracujete (a na způsobu zpracování těchto dat), může být nutné optimalizovat konfiguraci.

Podrobné pokyny k optimalizaci konfigurací výkonu pro většinu scénářů najdete v [tématu Optimalizace konfigurací clusteru pomocí Apache Ambari](hdinsight-changing-configs-via-ambari.md). Když používáte Spark, podívejte [se na informace o optimalizaci úloh Apache Spark.](spark/apache-spark-perf.md) 

## <a name="step-7-reproduce-the-failure-on-a-different-cluster"></a>Krok 7: Reprodukovat selhání v jiném clusteru

Chcete-li diagnostikovat zdroj chyby clusteru, spusťte nový cluster se stejnou konfigurací a potom znovu odešlete kroky neúspěšné úlohy jeden po druhém. Před zpracováním dalšího kroku zkontrolujte výsledky každého kroku. Tato metoda umožňuje opravit a znovu spustit jeden neúspěšný krok. Tato metoda má také tu výhodu, že načte te pouze vstupní data jednou.

1. Vytvořte nový testovací cluster se stejnou konfigurací jako neúspěšný cluster.
2. Odešlete první krok úlohy do testovacího clusteru.
3. Po dokončení zpracování kroku zkontrolujte chyby v souborech protokolu krok. Připojte se k hlavnímu uzlu testovacího clusteru a zobrazte tam soubory protokolu. Soubory protokolu krok se zobrazí pouze po spuštění kroku po určitou dobu, dokončí nebo selže.
4. Pokud byl první krok úspěšný, spusťte další krok. Pokud došlo k chybám, prozkoumejte chybu v souborech protokolu. Pokud se jednalo o chybu v kódu, proveďte opravu a znovu spusťte krok.
5. Pokračujte, dokud nebudou spuštěny všechny kroky bez chyby.
6. Po dokončení ladění testovacího clusteru jej odstraňte.

## <a name="next-steps"></a>Další kroky

* [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Analýza protokolů HDInsight](hdinsight-debug-jobs.md)
* [Přístup Apache Hadoop YARN aplikace přihlásit v Linuxu-založené HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Povolení výpisů hald pro služby Apache Hadoop na Linuxu založeném na HDInsightu](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Známé problémy pro cluster Apache Spark na HDInsightu](hdinsight-apache-spark-known-issues.md)
