---
title: Řešení potíží s pomalé nebo jeho selháním HDInsight clusteru – Azure HDInsight
description: Diagnostika a řešení potíží pomalé nebo selhání clusteru HDInsight.
services: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/11/2018
ms.openlocfilehash: f18e667e86c310e4d2950ba195911307aec5426d
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047021"
---
# <a name="troubleshoot-a-slow-or-failing-hdinsight-cluster"></a>Řešení potíží s pomalým clusterem HDInsight nebo jeho selháním

Pokud se HDInsight cluster pracuje pomalu nebo neúspěšné s kódem chyby, máte několik možností, jak řešení potíží. Pokud vaše úlohy trvá delší dobu, než se očekávalo, nebo pomalé odezvy se zobrazuje v obecných, může být selhání upstream z vašeho clusteru, jako jsou služby, na kterých běží clusteru. Nejčastější příčinou tomuto zpomalování je však nedostatečné škálování. Když vytvoříte nový cluster HDInsight, vyberte odpovídající [velikosti virtuálních počítačů](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters)

K diagnostice pomalé nebo selhání clusteru, shromážděte informace o všech aspektech prostředí, jako jsou přidružené služby Azure, konfiguraci clusteru a informace o spuštění úlohy. Užitečné Diagnostika je pokusit se reprodukovat chyby stavu na jiném clusteru.

* Krok 1: Shromáždění dat o problému
* Krok 2: Ověření clusteru prostředí HDInsight 
* Krok 3: Zobrazení stavu vašeho clusteru
* Krok 4: Kontrola prostředí zásobníku a verze
* Krok 5: Zkontrolujte soubory protokolu clusteru
* Krok 6: Zkontrolujte nastavení konfigurace
* Krok 7: Reprodukujte chyby na jiném clusteru 

## <a name="step-1-gather-data-about-the-issue"></a>Krok 1: Shromáždění dat o problému

HDInsight poskytuje celou řadu nástrojů, které slouží k identifikaci a řešení potíží s clustery. Následující kroky vás provedou tyto nástroje a pošlete nám návrhy pro přesné určení problém.

### <a name="identify-the-problem"></a>Zjistěte, co problém

Vám pomůže identifikovat problém, zvažte následující otázky:

* Co je očekávat provést? Co se stalo místo?
* Jak dlouho procesu trvala ke spuštění? Jak dlouho by mělo mít být spuštěné?
* Máte úkoly vždy pomalý na tomto clusteru? Se rychleji na jiném clusteru?
* Pokud tento problém nejdřív vzniknout? Jak často se to stalo od?
* Nic se změnilo v konfiguraci clusteru?

### <a name="cluster-details"></a>Podrobnosti o clusteru

Obsahuje informace o důležitých clusteru:

* Název clusteru.
* Cluster oblasti - zkontrolujte soubor pro [výpadku oblasti](https://azure.microsoft.com/status/).
* Typ clusteru HDInsight a verze.
* Typ a počet instancí HDInsight zadaný pro hlavní a pracovní uzly.

Na webu Azure portal může poskytnout tyto informace:

![Portál HDInsight Azure informace](./media/hdinsight-troubleshoot-failed-cluster/portal.png)

Můžete také použít rozhraní příkazového řádku Azure:

```
    azure hdinsight cluster list
    azure hdinsight cluster show <ClusterName>
```

Další možností je pomocí Powershellu. Další informace najdete v tématu [spravovat Hadoop clusterů v HDInsight pomocí Azure Powershellu](hdinsight-administer-use-powershell.md).

## <a name="step-2-validate-the-hdinsight-cluster-environment"></a>Krok 2: Ověření clusteru prostředí HDInsight

Každý cluster HDInsight spoléhá na různé služby Azure a open source softwaru, jako je například Apache HBase a Apache Sparku. Clustery HDInsight můžete také volat na ostatní služby Azure, jako jsou Azure Virtual Network.  Selhání clusteru může být způsoben žádné služby spuštěné v clusteru nebo externí služby.  Změna konfigurace služby clusteru může také způsobit selhání clusteru.

### <a name="service-details"></a>Podrobnosti služby

* Kontrola verze open source knihovny
* Vyhledat [výpadek služeb Azure](https://azure.microsoft.com/status/) 
* Kontrola omezení využití služeb Azure 
* Zkontrolujte konfiguraci podsítě virtuální sítě Azure 

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Zobrazit nastavení konfigurace clusteru pomocí uživatelského rozhraní Ambari

Apache Ambari poskytuje správu a monitorování clusterů HDInsight pomocí webového uživatelského rozhraní a rozhraní REST API. Ambari je zahrnuta v clusterech HDInsight založených na Linuxu. Vyberte **řídicí panel clusteru** podokně na stránky portálu Azure HDInsight.  Vyberte **řídicí panel clusteru HDInsight** podokně otevřete uživatelské rozhraní Ambari, a zadejte přihlašovací údaje clusteru.  

![Uživatelské rozhraní Ambari](./media/hdinsight-troubleshoot-failed-cluster/ambari-ui.png)

Pokud chcete otevřít seznam zobrazení, služby, vyberte **zobrazení Ambari** na stránce portálu Azure.  Tento seznam závisí na které knihovny jsou nainstalovány. Může se zobrazit třeba správce fronty YARN, Hive zobrazení a zobrazení Tez.  Vyberte odkaz služby najdete v článku Konfigurace a informace o službě.

#### <a name="check-for-azure-service-outages"></a>Vyhledat výpadek služeb Azure

HDInsight spoléhá na několik služeb Azure. Spuštění virtuálních serverů v Azure HDInsight, úložiště dat a skriptů do úložiště objektů Blob v Azure nebo Azure DataLake Store, a indexy soubory protokolů ve službě Azure Table storage. Přerušení na tyto služby, i když se taková situace vzácná, může způsobit problémy v HDInsight. Pokud máte neočekávané zpomalení nebo selhání v clusteru, zkontrolujte, [řídicí panel stavu Azure](https://azure.microsoft.com/status/). Stav jednotlivých služeb je uveden podle oblasti. Zkontrolujte váš cluster oblasti a také oblastech souvisejících služeb.

#### <a name="check-azure-service-usage-limits"></a>Kontrola omezení využití služby Azure

Pokud provádíte spuštění velký cluster nebo současně spustit více clusterů, cluster může selhat, pokud byl překročen limit služby Azure. Omezení služby se liší v závislosti na vašem předplatném Azure. Další informace najdete v tématu [předplatného Azure a limity, kvóty a omezení](https://docs.microsoft.com/azure/azure-subscription-service-limits).
Můžete požádat o to, že Microsoft zlepšit počet prostředků HDInsight (jako třeba počet jader virtuálního počítače a instance virtuálních počítačů) s [Resource Manageru žádost o zvýšení kvóty jader](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

#### <a name="check-the-release-version"></a>Kontrola verze

Porovnejte s nejnovější verzí HDInsight verze clusteru. Jednotlivých verzích HDInsight zahrnuje vylepšení, jako jsou nové aplikace, funkce, opravy a opravy chyb. Problém, který ovlivňuje clusteru pravděpodobně byly opraveny v nejnovější verzi. Pokud je to možné, znovu spusťte váš cluster používá nejnovější verzi HDInsight a přidruženými knihovnami, jako je například Apache HBase, Apache Spark a dalších.

#### <a name="restart-your-cluster-services"></a>Restartujte služby clusteru

Pokud máte ve vašem clusteru zpomalení, zvažte restartování služby prostřednictvím uživatelského rozhraní Ambari nebo rozhraní příkazového řádku Azure. Cluster může docházet k přechodným chybám a restartování je nejrychlejší způsob, jak stabilizaci prostředí a potenciálně tak vylepšit výkon.

## <a name="step-3-view-your-clusters-health"></a>Krok 3: Zobrazení stavu vašeho clusteru

Clustery HDInsight se skládají z různých typů uzlů se systémem na instancích virtuálních počítačů. Každý uzel je možné monitorovat vyčerpání prostředků, problémy se síťovým připojením a další problémy, které mohou zpomalit clusteru. Každý cluster obsahuje dva hlavní uzly a většinu typů clusteru obsahovat kombinaci pracovního procesu a hraničních uzlů. 

Popis různých uzlech používá každý typ clusteru najdete v tématu [nastavení clusterů v HDInsight pomocí Hadoop, Spark, Kafka a další](hdinsight-hadoop-provision-linux-clusters.md).

Následující části popisují, jak zkontrolovat stav každého uzlu a celkové clusteru.

### <a name="get-a-snapshot-of-the-cluster-health-using-the-ambari-ui-dashboard"></a>Získat snímek stavu clusteru na řídicím panelu uživatelského rozhraní Ambari

[Řídicí panel uživatelského rozhraní Ambari](#view-cluster-configuration-settings-with-the-ambari-ui) (`https://<clustername>.azurehdinsight.net`) poskytuje přehled stavu clusteru, jako je například dostupnost, paměti, sítě a využití CPU, využití disku HDFS a tak dále. Část hostitele Ambari slouží k zobrazení prostředků na úrovni hostitele. Můžete také zastavit a restartovat služby.

### <a name="check-your-webhcat-service"></a>Zkontrolujte vaše služba WebHCat

Jeden běžný scénář pro úlohy Hive a Pig, Sqoop služeb při selhání je selhání se [WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md) (nebo *Templeton*) služby. WebHCat je rozhraní REST pro provádění vzdálené úlohy, jako je například Hive, Pig, MapReduce a nabídku. WebHCat žádostí o odeslání úlohy se přeloží do aplikací YARN a vrátí stav odvozený od stav aplikace YARN.  Následující části popisují běžné stavové kódy WebHCat HTTP.

#### <a name="badgateway-502-status-code"></a>BadGateway (502 stavový kód)

Toto je obecná zpráva z uzlů brány a je nejběžnější chybový kód. Jednou z možných příčin této je služba WebHCat se dolů na aktivní hlavní uzel. Pokud chcete zkontrolovat pro tuto možnost, použijte následující příkaz CURL:

```bash
$ curl -u admin:{HTTP PASSWD} https://{CLUSTERNAME}.azurehdinsight.net/templeton/v1/status?user.name=admin
```

Ambari zobrazí výstrahu zobrazující hostitele, na kterých služba WebHCat je mimo provoz. Můžete zkusit zobrazíte službě WebHCat zpět restartováním služby svého hostitele.

![Restartujte WebHCat Server](./media/hdinsight-troubleshoot-failed-cluster/restart-webhcat.png)

Pokud WebHCat server stále nepřejde, pak v protokolu operations zprávy o neúspěchu. Podrobnější informace, zkontrolujte `stderr` a `stdout` soubory odkazuje na uzlu.

#### <a name="webhcat-times-out"></a>WebHCat vyprší časový limit

HDInsight bránu vyprší časový limit odpovědi, které trvají déle než dvě minuty, vrací `502 BadGateway`. WebHCat dotazuje služby YARN pro úlohy stavy a pokud YARN trvá déle než dvě minuty reagovat, který požádat o vypršení časového limitu může.

V tomto případě následující v protokolech `/var/log/webhcat` adresáře:

* **webhcat.log** je protokolu log4j které serverové protokoly, zápisy
* **webhcat console.log** je stdout serveru při spuštění
* **webhcat. konzola error.log** je stderr proces serveru

> [!NOTE]
> Každý `webhcat.log` je jednotlivě každý den, generují se soubory s názvem `webhcat.log.YYYY-MM-DD`. Vyberte příslušný soubor pro časový rozsah, kterou právě prošetřujete.

Následující části popisují některé možné příčiny vypršení časového limitu pro WebHCat.

##### <a name="webhcat-level-timeout"></a>Časový limit úrovně WebHCat

Při zatížení s více než 10 otevřít sockets WebHCat trvá déle, k vytvoření nového připojení soketu, což může způsobit vypršení časového limitu. K zobrazení seznamu síťová připojení do a z WebHCat, použijte `netstat` na aktuální aktivní hlavní uzel:

```bash
$ netstat | grep 30111
```

30111 je port, který naslouchá WebHCat. Počet otevřených soketů by měl být menší než 10.

Pokud neexistují žádné otevřené sokety, výsledkem předchozího příkazu není výsledek. Zkontrolujte, jestli je Templeton nahoru a naslouchá na portu 30111, použijte:

```bash
$ netstat -l | grep 30111
```

##### <a name="yarn-level-timeout"></a>Časový limit úrovně YARN

Templeton volá YARN pro spouštění úloh a komunikaci mezi Templeton a YARN může způsobit vypršení časového limitu.

Na úrovni YARN existují dva druhy vypršení časového limitu pro:

1. Odeslání úlohy YARN může trvat dostatečně dlouhá, aby způsobit vypršení časového limitu.

    Pokud otevřete `/var/log/webhcat/webhcat.log` souboru protokolu a vyhledejte "zařazených do fronty úloh", vám může zobrazit více položek kde je příliš dlouhá doba spuštění (> 2000 ms), s položkami znázorňující zvýšení čekání.

    Čas potřebný pro úlohy ve frontě se nadále zvýšit, protože rychlost, jakou nové úlohy odeslání je vyšší než rychlost, jakou jsou staré úlohy dokončeny. Jakmile paměti YARN je 100 % využití, *joblauncher fronty* už si půjčte kapacity z *výchozí fronta*. Proto mohou být přijaty žádné nové úlohy ve frontě joblauncher. Toto chování může způsobit čekání se déle a nebude, způsobí vypršení časového limitu, který obvykle následuje mnoha dalších.

    Následující obrázek znázorňuje frontu joblauncher Nepromyšlené 714.4 %. Je to přijatelné, tak dlouho, dokud je stále Volná kapacita ve frontě výchozí vypůjčit z. Ale když paměti YARN je plně využívá kapacitu 100 % cluster je naplno, nové úlohy musíte počkat, které nakonec způsobí vypršení časového limitu.

    ![Joblauncher fronty](./media/hdinsight-troubleshoot-failed-cluster/joblauncher-queue.png)

    Existují dva způsoby, jak tento problém vyřešit: buď snižte rychlosti nové úlohy odesílá nebo zvýšení rychlosti spotřeby starých úloh ve vertikálním navýšení kapacity clusteru.

2. YARN zpracování může trvat dlouhou dobu, což může způsobit vypršení časového limitu.

    * Vypsat seznam všech úloh: Toto je časově náročné volání. Toto volání zobrazí aplikace ze Správce prostředků YARN a pro každé dokončené aplikace, umožňuje získat stav z YARN JobHistoryServer. Toto volání s větší počet úloh, může být vypršení časového limitu.

    * Seznam úloh, které jsou starší než 7 dní: JobHistoryServer YARN HDInsight je nakonfigurovaná na uchovávání informací dokončenou úlohu po dobu sedmi dní (`mapreduce.jobhistory.max-age-ms` hodnota). Došlo k pokusu o zobrazení výčtu výsledků vymazány úlohy vypršení časového limitu.

Chcete-li diagnostikovat tyto problémy:

    1. Určit rozsah času UTC řešení
    2. Vyberte příslušné `webhcat.log` soubory
    3. Vyhledejte zobrazit upozornění a chybové zprávy během této doby

#### <a name="other-webhcat-failures"></a>Jiné chyby WebHCat

1. Stavový kód 500 protokolu HTTP

    Ve většině případů, kdy WebHCat vrátí 500 chybová zpráva obsahuje podrobnosti o tomto selhání. V opačném případě si projít `webhcat.log` pro zobrazit upozornění a chybové zprávy.

2. Selhání úlohy

    Můžou nastat případy, kdy interakce s WebHCat jsou úspěšné, ale úlohy, které se nedaří.

    Shromažďuje výstup úlohy konzoly jako Templeton `stderr` v `statusdir`, což je často užitečné při řešení potíží. `stderr` obsahuje identifikátor YARN aplikace skutečný dotazu.

## <a name="step-4-review-the-environment-stack-and-versions"></a>Krok 4: Kontrola prostředí zásobníku a verze

Uživatelské rozhraní Ambari **zásobníku a verze** stránka obsahuje informace o clusteru služby configuration a služby historie verzí.  Nesprávná verze knihovny služby Hadoop, může být příčinou selhání clusteru.  V uživatelském rozhraní Ambari, vyberte **správce** nabídky a pak **zásobníky a verze**.  Vyberte **verze** karty na stránce zobrazíte informace o verzi služby:

![Zásobník a verze](./media/hdinsight-troubleshoot-failed-cluster/stack-versions.png)

## <a name="step-5-examine-the-log-files"></a>Krok 5: Zkontrolujte soubory protokolu

Existuje mnoho typů protokolů, které jsou generovány z mnoha služeb a komponent, které tvoří HDInsight cluster. [Soubory protokolu WebHCat](#check-your-webhcat-service) jsou popsány dříve. Existuje několik dalších užitečných souborů protokolů, které můžete zúžit problémy s vaším clusterem, můžete zjistit, jak je popsáno v následujících částech.

![Příklad souboru protokolu HDInsight](./media/hdinsight-troubleshoot-failed-cluster/logs.png)

* Clustery HDInsight se skládá z několika uzlů, ke spuštění odeslané úlohy jsou úkol většina z nich. Úlohy spustit současně, ale soubory protokolu lze zobrazit pouze výsledky lineárně. HDInsight spustí nové úkoly, ostatní, které se nepovede dokončit. nejdříve se ukončuje. Tato aktivita se protokoluje do `stderr` a `syslog` soubory.

* Soubory protokolu akce skriptu zobrazit chyby nebo změny neočekávaná konfigurace během procesu vytváření vašeho clusteru.

* Krok protokolů Hadoop identifikaci úlohy systému Hadoop spuštěn jako součást kroku, který obsahuje chyby.

### <a name="check-the-script-action-logs"></a>Zkontrolujte protokoly akce skriptu

HDInsight [akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md) spouštění skriptů v clusteru zadali ručně, nebo když. Například akce skriptu lze použít k instalaci dalšího softwaru v clusteru nebo změnit nastavení konfigurace z výchozí hodnoty. Kontrola protokolů akce skriptu vám umožní získat přehled chyb, ke kterým došlo během instalace a konfigurace.  Výběrem můžete zobrazit stav akce skriptu **ops** tlačítko v Uživatelském rozhraní Ambari nebo můžete využít protokoly z výchozí účet úložiště.

Protokoly akce skriptů jsou umístěny v `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` adresáře.

### <a name="view-hdinsight-logs-using-ambari-quick-links"></a>Zobrazit protokoly HDInsight pomocí Ambari rychlé odkazy

Uživatelské rozhraní Ambari HDInsight zahrnuje celou řadu **rychlé odkazy** oddíly.  Pro přístup k protokolu odkazy pro určité služby ve vašem clusteru HDInsight, otevřete uživatelské rozhraní Ambari pro váš cluster a potom vyberte odkaz služby ze seznamu na levé straně. Vyberte **rychlé odkazy** rozevírací seznam, poté uzel HDInsight zájmu a pak vyberte odkaz pro jeho přidružené protokolu.

Například pro HDFS protokoly:

![Ambari – rychlé odkazy na soubory protokolů](./media/hdinsight-troubleshoot-failed-cluster/quick-links.png)

### <a name="view-hadoop-generated-log-files"></a>Zobrazit soubory protokolů generované Hadoop

HDInsight cluster vytvoří protokoly, které se zapisují do tabulek Azure a Azure Blob storage. YARN vytvoří vlastní protokoly spuštění. Další informace najdete v tématu [Správa protokolů pro HDInsight cluster](hdinsight-log-management.md#access-the-hadoop-log-files).

### <a name="review-heap-dumps"></a>Zkontrolujte výpisů paměti haldy

Výpisy haldy paměti obsahují snímek paměti aplikace, včetně hodnot proměnných v době, které jsou užitečné při diagnostikování problémů, ke kterým dochází za běhu. Další informace najdete v tématu [výpisů povolit haldy pro služby Hadoop v HDInsight se systémem Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md).

## <a name="step-6-check-configuration-settings"></a>Krok 6: Zkontrolujte nastavení konfigurace

Clustery HDInsight jsou předem nakonfigurované s výchozím nastavením pro souvisejících služeb, jako jsou Hadoop, Hive, HBase a tak dále. V závislosti na typu clusteru, její konfigurace hardwaru, jeho počet uzlů, typy úloh spouštíte a data, že pracujete s (a jak tato data se právě zpracovávají), možná budete muset optimalizovat konfiguraci.

Podrobné pokyny k optimalizaci výkonu konfigurace pro většinu scénářů najdete v tématu [optimalizovat konfigurace clusterů s Ambari](hdinsight-changing-configs-via-ambari.md). Při použití Sparku, naleznete v tématu [optimalizace Sparkových úloh výkonu](spark/apache-spark-perf.md). 

## <a name="step-7-reproduce-the-failure-on-a-different-cluster"></a>Krok 7: Reprodukujte chyby na jiném clusteru

Pro usnadnění diagnostiky příčiny chyby clusteru, spusťte nový cluster se stejnou konfigurací a potom odešlete znovu neúspěšná úloha kroky jeden po druhém. Zkontrolujte výsledky každého kroku před zpracováním dalším objektem. Tato metoda poskytuje možnost opravit a znovu spusťte jeden neúspěšných kroků. Tato metoda také nabízí výhodu v podobě pouze jednou načítání vstupní data.

1. Vytvoření nového clusteru testů se stejnou konfigurací jako selhání clusteru.
2. První krok úlohy do clusteru testů odešlete.
3. Po dokončení zpracování kroku zkontrolujte chyby v souborech protokolů kroku. Připojení k hlavnímu uzlu clusteru test a zobrazit soubory protokolů existuje. Soubory protokolu krok se zobrazí pouze po kroku spustí nějakou dobu, dokončí, nebo se nezdaří.
4. Pokud prvním krokem bylo úspěšné, spusťte na další krok. Pokud došlo k chybám, prostudujte chybu v souborech protokolů. Pokud se jednalo o chybu v kódu, provést opravu a znovu spustit krok. 
5. Pokračujte, dokud nebudou všechny kroky se spustí bez chyb.
6. Po dokončení ladění testovacího clusteru, odstraňte ho.

## <a name="next-steps"></a>Další postup

* [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Ambari](hdinsight-hadoop-manage-ambari.md)
* [Analýza protokolů pro HDInsight](hdinsight-debug-jobs.md)
* [Přístup k protokolu aplikace YARN na Linuxovým systémem HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Povolení výpisů paměti haldy pro služby Hadoop v HDInsight se systémem Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Známé problémy pro cluster Apache Spark v HDInsight](hdinsight-apache-spark-known-issues.md)
