---
title: Řešení potíží s pomalými nebo chybě cluster HDInsight - Azure HDInsight | Microsoft Docs
description: Diagnostika a řešení potíží s pomalým nebo selhání clusteru HDInsight.
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
ms.openlocfilehash: bd026169062788fa7587cb6357b3764fbfdb24fb
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31415907"
---
# <a name="troubleshoot-a-slow-or-failing-hdinsight-cluster"></a>Řešení potíží s pomalým clusterem HDInsight nebo jeho selháním

Pokud cluster služby HDInsight je pomalý nebo selhání s kódem chyby, máte několik možností pro řešení potíží. Pokud vaše úlohy trvá delší dobu, než se očekávalo, nebo pomalé odezvy se zobrazuje obecně, může být selhání proti proudu z clusteru, například služby, na kterých běží clusteru. Nejčastější příčinou těchto zpomalení je však dostatek škálování. Při vytváření nového clusteru HDInsight, vyberte odpovídající [velikostí virtuálních počítačů](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters)

Při diagnostice pomalé nebo selhání clusteru, shromážděte informace o všech aspektech prostředí, například přidružených službách Azure, konfiguraci clusteru a informací o provádění úloh. Užitečné Diagnostika se pokuste se znovu provést chybový stav na jiný cluster.

* Krok 1: Shromáždění dat o problému
* Krok 2: Ověření clusteru prostředí HDInsight 
* Krok 3: Přehled o stavu vašeho clusteru
* Krok 4: Kontrola prostředí zásobníku a verze
* Krok 5: Zkontrolujte soubory protokolů clusteru
* Krok 6: Zkontrolujte nastavení konfigurace
* Krok 7: Reprodukujte selhání na jiný cluster 

## <a name="step-1-gather-data-about-the-issue"></a>Krok 1: Shromáždění dat o problému

HDInsight nabízí celou řadu nástrojů, které můžete použít k identifikaci a řešení problémů s clustery. Následující postup vás provede tyto nástroje a zadejte návrhy přesným rozpoznáním problém.

### <a name="identify-the-problem"></a>Identifikovat problém

Aby bylo možné identifikovat problém, zvažte následující otázky:

* Co očekávají provést? Co se stalo místo?
* Jak dlouho tento proces trvat spustit? Jak dlouho by mělo mít být spuštěné?
* Mají Moje úlohy vždy pomalu spustit na tomto clusteru? Se budou spouštět rychleji na jiného clusteru?
* Pokud tento problém nejdřív vyskytnout? Jak často má došlo od?
* Nic změny v konfiguraci clusteru?

### <a name="cluster-details"></a>Podrobnosti o clusteru

Obsahuje informace o důležitých clusteru:

* Název clusteru.
* Cluster oblast - zkontrolujte soubor pro [oblast výpadků](https://azure.microsoft.com/status/).
* Typ clusteru HDInsight a verze.
* Typ a počet instancí HDInsight zadané pro head a pracovní uzly.

Na portálu Azure můžete poskytnout tyto informace:

![Portál HDInsight Azure informace](./media/hdinsight-troubleshoot-failed-cluster/portal.png)

Můžete také použít rozhraní příkazového řádku Azure:

```
    azure hdinsight cluster list
    azure hdinsight cluster show <ClusterName>
```

Další možností je pomocí prostředí PowerShell. Další informace najdete v tématu [spravovat Hadoop clusterů v HDInsight pomocí prostředí Azure PowerShell](hdinsight-administer-use-powershell.md).

## <a name="step-2-validate-the-hdinsight-cluster-environment"></a>Krok 2: Ověření clusteru prostředí HDInsight

Každý cluster HDInsight spoléhá na různé služby Azure a na open-source softwaru, jako je například Apache HBase a Apache Spark. Clustery HDInsight můžete také zavolat na jiné služby Azure, třeba virtuálních sítí Azure.  Selhání clusteru může být způsobeno žádné služby běžící v clusteru, nebo externí služby.  Změna konfigurace služby clusteru může také způsobit selhání clusteru.

### <a name="service-details"></a>Podrobnosti služby

* Kontrola verze knihovny open-source
* Zkontrolujte [výpadků služby Azure](https://azure.microsoft.com/status/) 
* Kontrola omezení využití služby Azure 
* Zkontrolujte konfiguraci podsítě virtuální sítě Azure 

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Zobrazit nastavení konfigurace clusteru pomocí uživatelského rozhraní Ambari

Apache Ambari poskytuje správu a sledování clusteru HDInsight pomocí webového uživatelského rozhraní a rozhraní REST API. Ambari je obsažena v clusterech HDInsight se systémem Linux. Vyberte **řídicí panel clusteru** podokně na stránky portálu Azure HDInsight.  Vyberte **řídicí panel clusteru HDInsight** podokně otevřete uživatelské rozhraní Ambari, a zadejte přihlašovací údaje clusteru.  

![Uživatelské rozhraní Ambari](./media/hdinsight-troubleshoot-failed-cluster/ambari-ui.png)

Otevřete seznam služby zobrazení, vyberte **zobrazení Ambari** na stránce portálu Azure.  Tento seznam závisí na které knihovny jsou nainstalovány. Může se zobrazit například správce front YARN, zobrazení Hive a zobrazení Tez.  Vyberte odkaz zobrazíte informace o služby a konfiguraci služby.

#### <a name="check-for-azure-service-outages"></a>Zkontrolujte výpadků služby Azure

HDInsight využívá několik služeb Azure. Spuštění virtuálních serverů v Azure HDInsight, ukládá data a skripty do úložiště objektů Blob v Azure nebo Azure DataLake úložiště, a indexy soubory protokolů ve službě Azure Table storage. Přerušení na tyto služby, i když je taková situace vzácná, můžou způsobit problémy v prostředí HDInsight. Pokud máte neočekávané zpomalení nebo selhání v clusteru, zkontrolujte [řídicího panelu Azure stav](https://azure.microsoft.com/status/). Stav každé služby je uveden podle oblasti. Zkontrolujte váš cluster oblasti a také oblasti pro všechny související služby.

#### <a name="check-azure-service-usage-limits"></a>Zkontrolujte limitů používání služby Azure

Pokud se spuštění clusteru s podporou velké nebo současně spustit mnoho clustery, cluster může selhat, pokud byl překročen limit služby služby Azure. Omezení služby se liší v závislosti na vašem předplatném Azure. Další informace najdete v tématu [předplatného Azure a omezení služby, kvóty a omezení](https://docs.microsoft.com/azure/azure-subscription-service-limits).
Můžete požádat, aby Microsoft zvýšit počet prostředků služby HDInsight k dispozici (například jader virtuálních počítačů a instancí virtuálních počítačů) s [Resource Manager žádost o zvýšení kvóty základní](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

#### <a name="check-the-release-version"></a>Zkontrolujte verzi

Porovnejte verze clusteru HDInsight verzi. Každý HDInsight verze obsahuje vylepšení, jako je například nové aplikace, funkce, opravy a opravy chyb. Problém, který ovlivňuje clusteru může byl opraven v nejnovější verzi. Pokud je to možné, znovu spusťte váš cluster pomocí nejnovější verze HDInsight a přidružené knihovny jako Apache HBase, Apache Spark a dalších.

#### <a name="restart-your-cluster-services"></a>Restartujte služby vašeho clusteru

Pokud se setkáváte s zpomalení v clusteru, zvažte, restartování vašim službám prostřednictvím rozhraní Ambari nebo rozhraní příkazového řádku Azure. Cluster může docházet k přechodné chyby a restartování je nejrychlejší způsob, jak stabilizaci prostředí a potenciálně tak vylepšit výkon.

## <a name="step-3-view-your-clusters-health"></a>Krok 3: Přehled o stavu vašeho clusteru

Clustery HDInsight se skládají z různých typů uzlů systémem instancí virtuálního počítače. Každý uzel se dá sledovat vyčerpání prostředků, problémy se síťovým připojením a jiné problémy, které mohou být zpomaleny clusteru. Každý cluster obsahuje dva uzly head a většinu typů clusteru obsahovat kombinaci pracovního procesu a uzly okraj. 

Popis různých uzlech používá každý typ clusteru najdete v tématu [nastavit clusterů v HDInsight Hadoop, Spark, Kafka a dalšími](hdinsight-hadoop-provision-linux-clusters.md).

Následující části popisují postup kontroly stavu každého uzlu a celkové clusteru.

### <a name="get-a-snapshot-of-the-cluster-health-using-the-ambari-ui-dashboard"></a>Získat přehled o stavu clusteru pomocí uživatelského rozhraní Ambari řídicího panelu

[Řídicí panel Uživatelské rozhraní Ambari](#view-cluster-configuration-settings-with-the-ambari-ui) (`https://<clustername>.azurehdinsight.net`) poskytuje přehled o stavu clusteru, jako je například doba provozu, paměti, sítě a využití procesoru, využití disku HDFS a tak dále. Části hostitelů Ambari slouží k zobrazení prostředků na úrovni hostitele. Můžete také zastavit a restartovat služby.

### <a name="check-your-webhcat-service"></a>Zkontrolujte služby WebHCat

Jeden běžný scénář pro úlohy Hive, Pig nebo Sqoop nejsou-li se o chybu s [WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md) (nebo *Templeton*) služby. WebHCat je rozhraní REST pro provádění vzdálené úlohy, například Hive, Pig, Scoop a MapReduce. WebHCat překládá žádostí o odeslání úlohy do YARN aplikace a vrátí stav odvozené z YARN stav aplikace.  Následující části popisují běžné stavové kódy WebHCat HTTP.

#### <a name="badgateway-502-status-code"></a>BadGateway (502 stavový kód)

Toto je obecná zpráva z uzlů brány a je nejběžnější chybový kód. Možnou příčinou pro tuto je služba WebHCat se dolů na aktivním uzlu head. Pokud chcete zkontrolovat pro tuto možnost, použijte následující příkaz CURL:

```bash
$ curl -u admin:{HTTP PASSWD} https://{CLUSTERNAME}.azurehdinsight.net/templeton/v1/status?user.name=admin
```

Ambari zobrazí výstrahu zobrazující hostitele, na kterých je služba WebHCat dolů. Můžete zkusit zprovoznit službu WebHCat zpět restartováním služby na jeho hostitele.

![Restartujte WebHCat Server](./media/hdinsight-troubleshoot-failed-cluster/restart-webhcat.png)

Pokud WebHCat server stále nepochází, zkontrolujte v protokolu operations zprávy o selhání. Podrobné informace, zkontrolujte `stderr` a `stdout` soubory odkazuje na uzlu.

#### <a name="webhcat-times-out"></a>WebHCat časového limitu

Bránu HDInsight časového limitu odpovědi, které trvat déle než dvě minuty, vrácení `502 BadGateway`. WebHCat dotazuje služby YARN pro úlohy stavy, a pokud YARN trvá déle než dvě minuty reagovat, který žádost může časového limitu.

V takovém případě zkontrolujte následující protokoly v `/var/log/webhcat` directory:

* **webhcat.log** protokolu log4j, které v protokolech serveru zápisu
* **webhcat console.log** je stdout serveru při spuštění
* **webhcat. konzoly error.log** je stderr procesu serveru

> [!NOTE]
> Každý `webhcat.log` je převracet denně, generování souborů s názvem `webhcat.log.YYYY-MM-DD`. Vyberte příslušný soubor pro časové rozmezí, kterou zkoumáte.

Následující části popisují některé možné příčiny vypršení časových limitů WebHCat.

##### <a name="webhcat-level-timeout"></a>Časový limit úrovni WebHCat

Při WebHCat zatížení, s víc než 10 otevřete sockets, trvá déle navázat nová připojení soketu, kdy dojde k vypršení časového limitu. K zobrazení seznamu připojeních k síti do a z WebHCat, použijte `netstat` na aktuální aktivní headnode:

```bash
$ netstat | grep 30111
```

30111 je port, který WebHCat naslouchá na. Počet otevřených soketů by měl být menší než 10.

Pokud neexistují žádné otevřete sockets, předchozí příkaz nevytváří výsledku. Zkontrolujte, jestli je Templeton nahoru a naslouchá na portu 30111, použijte:

```bash
$ netstat -l | grep 30111
```

##### <a name="yarn-level-timeout"></a>YARN úrovně vypršení časového limitu

Templeton volá YARN ke spuštění úloh a komunikace mezi Templeton a YARN může způsobit vypršení časového limitu.

Na úrovni YARN existují dva typy časové limity:

1. Odeslání úlohy YARN může trvat dlouho dostatek způsobí vypršení časového limitu.

    Pokud otevřete `/var/log/webhcat/webhcat.log` souboru protokolu a hledat "zařazených do fronty úloha", zobrazí se v několika kde doba provádění je příliš dlouhá (> 2000 ms), s položkami zobrazující zvýšení počkejte časy.

    Čas úloh zařazených do fronty i nadále zvýšit, protože je vyšší než počet, kdy se starý úlohy provádějí rychlost, jakou získat nové úlohy odeslání. Paměť YARN po 100 % používá, *joblauncher fronty* mohou už půjčit kapacity z *výchozí fronty*. Proto mohou být přijaty žádné nové úlohy do fronty joblauncher. Toto chování může způsobit, že doba čekání se déle a déle způsobuje vypršení časového limitu, který obvykle následuje mnohé další.

    Následující obrázek ukazuje na 714.4 % Nepromyšlené joblauncher fronty. To je přijatelné, tak dlouho, dokud je stále Volná kapacita ve frontě výchozí zapůjčení z. Ale pokud clusteru je plně využít a paměť YARN je na kapacitě 100 %, nové úlohy vyčkat, což může způsobit, že vypršení časových limitů.

    ![Joblauncher fronty](./media/hdinsight-troubleshoot-failed-cluster/joblauncher-queue.png)

    Existují dva způsoby, jak tento problém vyřešit: buď snížení rychlosti nové úlohy odeslání nebo zvýšení rychlosti spotřeba starého úlohy ve vertikálním navýšení kapacity clusteru.

2. YARN zpracování může trvat dlouhou dobu, což může způsobit překročení časového limitu.

    * Seznam všechny úlohy: to je časově náročná volání. Toto volání vytvoří výčet aplikací z YARN ResourceManager a pro každou aplikaci dokončené, získá stav z YARN JobHistoryServer. S vyšší počet úloh můžete toto volání vypršení časového limitu.

    * Seznam úloh, které jsou starší než 7 dní: HDInsight YARN JobHistoryServer nakonfigurovaný tak, aby uchovávat informace dokončené úlohy sedm dnů (`mapreduce.jobhistory.max-age-ms` hodnotu). Došlo k pokusu o zobrazení výčtu výsledků vymazány úlohy k vypršení časového limitu.

Při diagnostice tyto problémy:

    1. Určení UTC časový rozsah a řešení potíží
    2. Vyberte odpovídající `webhcat.log` souborům
    3. Během této doby. vyhledejte zprávy varování a chyby

#### <a name="other-webhcat-failures"></a>Jiné chyby WebHCat

1. Stavový kód protokolu HTTP 500

    Ve většině případů, kdy WebHCat vrátí 500 chybová zpráva obsahuje podrobnosti o selhání. Jinak, projděte `webhcat.log` varování a CHYBOVÝCH zpráv.

2. Selhání úlohy

    Můžou nastat případy, kdy jsou interakce s WebHCat úspěšné, ale dochází k selhání úlohy.

    Shromažďuje výstup úlohy konzoly jako Templeton `stderr` v `statusdir`, což je často užitečné při řešení potíží. `stderr` obsahuje identifikátor aplikace YARN skutečné dotazu.

## <a name="step-4-review-the-environment-stack-and-versions"></a>Krok 4: Kontrola prostředí zásobníku a verze

Uživatelské rozhraní Ambari **zásobníku a verze** stránka obsahuje informace o clusteru služby configuration a služby historie verzí.  Příčinu selhání clusteru může být nesprávná verze knihovny služby Hadoop.  V uživatelském rozhraní Ambari, vyberte **správce** nabídce a potom **zásobníky a verze**.  Vyberte **verze** na stránce zobrazíte informace o verzi služby kartu:

![Zásobník a verze](./media/hdinsight-troubleshoot-failed-cluster/stack-versions.png)

## <a name="step-5-examine-the-log-files"></a>Krok 5: Zkontrolujte soubory protokolu

Existuje mnoho typů protokolů, které se generují z mnoha služeb a součástí, které tvoří clusteru služby HDInsight. [Soubory protokolu WebHCat](#check-your-webhcat-service) jsou popsané. Existuje několik dalších užitečné soubory protokolů, které můžete prozkoumat zúžit zaměření problémy s clusterem, jak je popsáno v následujících částech.

![Příklad souboru protokolu HDInsight](./media/hdinsight-troubleshoot-failed-cluster/logs.png)

* Clustery HDInsight se skládat z několika uzlů, většina z nich se musí spustit odeslaná úlohy. Úlohy spouštět souběžně, ale soubory protokolu může zobrazit pouze výsledky lineárně. HDInsight spustí nové úkoly, ostatní, které se nepodařilo dokončit před prováděním se ukončuje. Tato aktivita se zaznamenává `stderr` a `syslog` soubory.

* Soubory protokolu akce skriptu zobrazovat chyby nebo změny neočekávaná konfigurace během procesu vytváření vašeho clusteru.

* Protokoly Hadoop krok Identifikujte úloh Hadoop spuštěna jako součást kroku, který obsahuje chyby.

### <a name="check-the-script-action-logs"></a>Zkontrolujte protokoly akce skriptu

HDInsight [skript akce](hdinsight-hadoop-customize-cluster-linux.md) v clusteru zadán ručně, nebo když spustit skripty. Například skript akce lze instalovat další software clusteru nebo změnit nastavení konfigurace z výchozí hodnoty. Kontrola protokolů akce skriptu můžete získat přehled o chybách, ke kterým došlo během instalace a konfigurace.  Stav akce skriptu můžete zobrazit výběrem **ops** tlačítko v uživatelském rozhraní Ambari nebo přístupem k protokoly z výchozí účet úložiště.

Protokoly akcí skriptu se nacházejí v `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` adresáře.

### <a name="view-hdinsight-logs-using-ambari-quick-links"></a>Zobrazit protokoly HDInsight pomocí Ambari rychlé odkazy

Uživatelské rozhraní Ambari HDInsight zahrnuje několik **rychlé odkazy** oddíly.  Pro přístup k protokolu odkazy pro konkrétní službu, v clusteru HDInsight, otevřete uživatelské rozhraní Ambari pro váš cluster a potom vyberte odkaz služby ze seznamu na levé straně. Vyberte **rychlé odkazy** rozevíracího seznamu, pak uzlu HDInsight od vás zajímají a pak vyberte odkaz pro jeho přidružené protokolu.

Například pro HDFS protokoly:

![Ambari rychlé odkazy na soubory protokolu](./media/hdinsight-troubleshoot-failed-cluster/quick-links.png)

### <a name="view-hadoop-generated-log-files"></a>Zobrazení souborů protokolů generovaných Hadoop

Cluster služby HDInsight generuje protokoly, které se zapisují do tabulek Azure a Azure Blob storage. YARN vytvoří vlastní protokoly spuštění. Další informace najdete v tématu [správu protokolů pro cluster služby HDInsight](hdinsight-log-management.md#access-the-hadoop-log-files).

### <a name="review-heap-dumps"></a>Zkontrolujte výpisů paměti haldy

Výpisy haldy obsahovat snímek paměti aplikace, včetně hodnot proměnných v té době, které jsou užitečné pro diagnostiku problémů, ke kterým dochází za běhu. Další informace najdete v tématu [výpisy paměti haldy povolit pro služby Hadoop v HDInsight se systémem Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md).

## <a name="step-6-check-configuration-settings"></a>Krok 6: Zkontrolujte nastavení konfigurace

Clustery prostředí HDInsight jsou předem nakonfigurovaná s výchozím nastavením pro související služby, jako je například Hadoop, Hive, HBase a tak dále. V závislosti na typu clusteru, konfigurace hardwaru, jeho počet uzlů, typy úloh, kterou používáte a data, že pracujete s (a jak probíhá zpracování dat), budete muset optimalizovat konfiguraci.

Podrobné pokyny k optimalizaci výkonu konfigurace pro většinu scénářů najdete v tématu [optimalizovat konfigurace clusteru pomocí Ambari](hdinsight-changing-configs-via-ambari.md). Při použití Spark, najdete v části [úlohy Spark optimalizace výkonu](spark/apache-spark-perf.md). 

## <a name="step-7-reproduce-the-failure-on-a-different-cluster"></a>Krok 7: Reprodukujte selhání na jiný cluster

Při diagnostice zdroji chyby clusteru, spusťte do nového clusteru se stejnou konfigurací a potom odešlete znovu nezdařenou úlohu kroky po jednom. Zkontrolujte výsledky jednotlivých kroků před zpracováním dalšímu. Tato metoda poskytuje možnost opravit a znovu spusťte jeden vadný krok. Tato metoda také nabízí výhodu v podobě pouze jednou načítání vstupní data.

1. Vytvoření nového clusteru test se stejnou konfigurací jako selhání clusteru.
2. První krok úlohy do clusteru testovací odeslání.
3. Pokud krok dokončí zpracování, vyhledejte chyby v souborech protokolu krok. Připojí k hlavního uzlu zkušební cluster a zobrazit soubory protokolů, existuje. Soubory protokolu krok vyskytovat pouze po kroku běží po určitý čas dokončení, nebo se nezdaří.
4. Pokud v prvním kroku byly úspěšné, spusťte na další krok. Pokud došlo k chybám, prostudujte chybu v souborech protokolu. Pokud byl chybu v kódu, provést opravu a znovu spusťte v kroku. 
5. Pokračujte, dokud všechny kroky spustit bez chyby.
6. Po dokončení ladění testovací clusteru, odstraňte jej.

## <a name="next-steps"></a>Další postup

* [Správa clusterů HDInsight pomocí Ambari webového uživatelského rozhraní](hdinsight-hadoop-manage-ambari.md)
* [Analýza protokolů HDInsight](hdinsight-debug-jobs.md)
* [Přístup k protokolu aplikace YARN na HDInsight se systémem Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Povolit výpisů paměti haldy pro služby Hadoop v HDInsight se systémem Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Známé problémy pro cluster Apache Spark v HDInsight](hdinsight-apache-spark-known-issues.md)
