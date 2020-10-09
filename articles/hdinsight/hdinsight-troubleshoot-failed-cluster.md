---
title: Řešení potíží s pomalými nebo neúspěšnými úlohami v clusteru Azure HDInsight
description: Diagnostika a řešení potíží s pomalými nebo neúspěšnými úlohami v clusteru Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: be991b63784a2c72a51bfbdc8506f3b4695ed6c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75895318"
---
# <a name="troubleshoot-a-slow-or-failing-job-on-a-hdinsight-cluster"></a>Řešení potíží s pomalou úlohou na clusteru HDInsight nebo jejím selháním

Pokud aplikace zpracovávající data v clusteru HDInsight běží pomalu nebo selhává s kódem chyby, máte několik možností pro odstraňování potíží. Pokud vaše úlohy trvá déle, než se očekávalo, nebo pokud vidíte pomalé odezvy obecně, může dojít k chybám, které jsou v clusteru v provozu, jako jsou například služby, na kterých cluster běží. Nejběžnější příčinou těchto zpomalování je ale nedostatečné škálování. Když vytváříte nový cluster HDInsight, vyberte odpovídající [velikosti virtuálních počítačů](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters).

Pro diagnostiku pomalého nebo neúspěšného clusteru Shromážděte informace o všech aspektech prostředí, jako jsou přidružené služby Azure, konfigurace clusteru a informace o spuštění úloh. Užitečnou diagnostikou je pokus o reprodukování chybového stavu na jiném clusteru.

* Krok 1: shromáždění dat o problému.
* Krok 2: ověření prostředí clusteru HDInsight.
* Krok 3: zobrazení stavu clusteru
* Krok 4: Kontrola zásobníku prostředí a verzí.
* Krok 5: Prověřte soubory protokolu clusteru.
* Krok 6: Ověřte nastavení konfigurace.
* Krok 7: reprodukování selhání v jiném clusteru.

## <a name="step-1-gather-data-about-the-issue"></a>Krok 1: shromáždění dat o problému

HDInsight poskytuje řadu nástrojů, které můžete použít k identifikaci a řešení problémů s clustery. Následující kroky vás provedou těmito nástroji a poskytnou návrhy pro určení problému.

### <a name="identify-the-problem"></a>Identifikace problému

K identifikaci problému Vezměte v úvahu následující otázky:

* Co se očekává, že dojde k tomu? Co se stalo?
* Jak dlouho trvá spuštění procesu? Jak dlouho má běžet?
* Mají se vždycky spouštět Moje úkoly v tomto clusteru pomalu? Pracovaly rychleji v jiném clusteru?
* Kdy k tomuto problému došlo poprvé? Jak často k tomu došlo od?
* Změnila se v konfiguraci clusteru něco?

### <a name="cluster-details"></a>Podrobnosti o clusteru

Důležité informace o clusteru zahrnují:

* Název clusteru
* Oblast clusteru – kontrolovat [výpadky oblastí](https://azure.microsoft.com/status/).
* Typ a verze clusteru HDInsight.
* Typ a počet instancí služby HDInsight určených pro hlavní uzly a uzly pracovního procesu.

Azure Portal může poskytnout tyto informace:

![Informace o Azure Portal HDInsight](./media/hdinsight-troubleshoot-failed-cluster/hdi-azure-portal-info.png)

Můžete použít také rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest):

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Další možností je použití prostředí PowerShell. Další informace najdete v tématu  [správa Apache Hadoop clusterů ve službě HDInsight s Azure PowerShell](hdinsight-administer-use-powershell.md).

## <a name="step-2-validate-the-hdinsight-cluster-environment"></a>Krok 2: ověření prostředí clusteru HDInsight

Každý cluster HDInsight spoléhá na různé služby Azure a na open source softwaru, jako je Apache HBA a Apache Spark. Clustery HDInsight můžou volat taky na jiné služby Azure, jako jsou třeba virtuální sítě Azure.  Selhání clusteru může být způsobeno některou z spuštěných služeb v clusteru nebo externí službou.  Změna konfigurace Clusterové služby může také způsobit selhání clusteru.

### <a name="service-details"></a>Podrobnosti služby

* Ověřte open source verze vydaných knihoven.
* Podívejte se na [výpadky služeb Azure](https://azure.microsoft.com/status/).  
* Podívejte se na omezení využití služeb Azure. 
* Ověřte konfiguraci podsítě Azure Virtual Network.  

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Zobrazení nastavení konfigurace clusteru pomocí uživatelského rozhraní Ambari

Apache Ambari poskytuje správu a monitorování clusteru HDInsight pomocí webového uživatelského rozhraní a REST API. Ambari je součástí clusterů HDInsight se systémem Linux. Vyberte podokno **řídicí panel clusteru** na stránce Azure Portal HDInsight.  Výběrem podokna **řídicí panel clusteru HDInsight** otevřete uživatelské rozhraní Ambari a zadejte přihlašovací údaje clusteru.  

![Přehled řídicího panelu Apache Ambari](./media/hdinsight-troubleshoot-failed-cluster/apache-ambari-overview.png)

Chcete-li otevřít seznam zobrazení služeb, vyberte **Ambari zobrazení** na stránce Azure Portal.  Tento seznam závisí na tom, které knihovny jsou nainstalovány. Můžete například zobrazit správce front PŘÍZ, zobrazení podregistru a tez zobrazení.  Kliknutím na odkaz služby zobrazíte informace o konfiguraci a službě.

#### <a name="check-for-azure-service-outages"></a>Zkontrolujte výpadky služeb Azure

HDInsight spoléhá na několik služeb Azure. Spouští virtuální servery v Azure HDInsight, ukládá data a skripty do Azure Blob Storage nebo Azure Data Lake Storage a indexuje soubory protokolů ve službě Azure Table Storage. Přerušení těchto služeb, i když zřídka, můžou způsobovat problémy v HDInsight. Pokud máte v clusteru neočekávané zpomalení nebo selhání, podívejte se na [řídicí panel stavu Azure](https://azure.microsoft.com/status/). Stav každé služby je uveden podle oblasti. Prohlédněte si oblast clusteru a také oblasti pro všechny související služby.

#### <a name="check-azure-service-usage-limits"></a>Ověřit omezení využití služeb Azure

Pokud spouštíte velký cluster nebo jste současně spustili mnoho clusterů, cluster může selhat, pokud jste překročili limit služeb Azure. Omezení služby se liší v závislosti na vašem předplatném Azure. Další informace najdete v tématu [Limity, kvóty a omezení předplatného a služeb Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).
Můžete požádat o to, aby Microsoft zvýšil počet dostupných prostředků služby HDInsight (například jader virtuálních počítačů a instancí virtuálních počítačů) s [žádostí o zvýšení kvóty správce prostředků jádra](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

#### <a name="check-the-release-version"></a>Ověřit verzi pro vydání

Porovnejte verzi clusteru s nejnovější verzí služby HDInsight. Každé vydání HDInsight zahrnuje vylepšení, jako jsou nové aplikace, funkce, opravy a opravy chyb. Problém, který má vliv na váš cluster, může být opravený v nejnovější verzi. Pokud je to možné, spusťte znovu cluster pomocí nejnovější verze služby HDInsight a přidružených knihoven, jako je Apache HBA, Apache Spark a dalších.

#### <a name="restart-your-cluster-services"></a>Restartujte Clusterové služby.

Pokud máte v clusteru zpomalení, zvažte restartování služeb prostřednictvím uživatelského rozhraní Ambari nebo Azure Classic CLI. V clusteru může docházet k přechodným chybám a restartování je nejrychlejší způsob, jak prostředí stabilizovat a případně zvýšit výkon.

## <a name="step-3-view-your-clusters-health"></a>Krok 3: zobrazení stavu vašeho clusteru

Clustery HDInsight se skládají z různých typů uzlů, které běží na instancích virtuálních počítačů. Každý uzel je možné monitorovat pro vyčerpání prostředků, problémy s připojením k síti a další problémy, které mohou cluster zpomalovat. Každý cluster obsahuje dva hlavní uzly a většina typů clusteru obsahuje kombinaci pracovních a hraničních uzlů. 

Popis různých uzlů, které každý typ clusteru používá, najdete v tématu [Nastavení clusterů v HDInsight pomocí Apache Hadoop, Apache Spark, Apache Kafka a dalších](hdinsight-hadoop-provision-linux-clusters.md).

Následující části popisují, jak kontrolovat stav jednotlivých uzlů a celkového clusteru.

### <a name="get-a-snapshot-of-the-cluster-health-using-the-ambari-ui-dashboard"></a>Získání snímku stavu clusteru pomocí řídicího panelu uživatelského rozhraní Ambari

[Řídicí panel uživatelského rozhraní Ambari](#view-cluster-configuration-settings-with-the-ambari-ui) ( `https://<clustername>.azurehdinsight.net` ) poskytuje přehled o stavu clusteru, například o době provozu, paměti, využití sítě a procesoru, HDFS využití disku a tak dále. Pomocí části hostitelé v Ambari můžete zobrazit prostředky na úrovni hostitele. Můžete také zastavit a restartovat služby.

### <a name="check-your-webhcat-service"></a>Podívejte se na službu WebHCat

Jednou z běžných scénářů pro Apache Hive, Apache Vepřu nebo Sqoop úlohy Apache je selhání služby [WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md) (nebo *Templeton*). WebHCat je rozhraní REST pro vzdálené spuštění úlohy, jako je například podregistr, prase, SCOOP a MapReduce. WebHCat překládá žádosti o odeslání úlohy na Apache Hadoop PŘÍZové aplikace a vrátí stav odvozený ze stavu aplikace PŘÍZe.  Následující části popisují běžné WebHCat stavové kódy HTTP.

#### <a name="badgateway-502-status-code"></a>BadGateway (Stavový kód 502)

Tento kód je obecná zpráva z uzlů brány a jedná se o nejběžnější stavové kódy selhání. Jednou z možných příčin je, že se služba WebHCat nachází na aktivním hlavním uzlu. Pro kontrolu této možnosti použijte následující příkaz složené závorky:

```bash
curl -u admin:{HTTP PASSWD} https://{CLUSTERNAME}.azurehdinsight.net/templeton/v1/status?user.name=admin
```

Ambari zobrazí výstrahu s informacemi o hostitelích, na kterých je služba WebHCat vypnutá. Službu WebHCat můžete zkusit zálohovat restartováním služby na jejím hostiteli.

![Server Apache Ambari restartování WebHCat serveru](./media/hdinsight-troubleshoot-failed-cluster/restart-webhcat-server.png)

Pokud se server WebHCat stále nespustí, zkontrolujte zprávy o chybách v protokolu operací. Podrobnější informace najdete v `stderr` souborech a na `stdout` uzlech, na které se odkazuje.

#### <a name="webhcat-times-out"></a>WebHCat časový limit

Vyprší časový limit An HDInsight brány na odpovědi, které trvá déle než dvě minuty, a vrátí se `502 BadGateway` . WebHCat se dotazuje na úlohy PŘÍZe na stavech úloh a pokud by PŘÍZe trvala déle než dvě minuty, může to vytrvat i vypršení tohoto požadavku.

V takovém případě zkontrolujte následující protokoly v `/var/log/webhcat` adresáři:

* **webhcat. log** je protokol log4j, na který Server zapisuje protokoly.
* **webhcat-Console. log** je stdout serveru při spuštění
* **webhcat-Console-Error. log** je stderr procesu serveru.

> [!NOTE]  
> Každý `webhcat.log` je převedený za den a generuje soubory s názvem `webhcat.log.YYYY-MM-DD` . Vyberte příslušný soubor pro časový rozsah, který zkoumáte.

Následující části popisují některé možné příčiny WebHCat časových limitů.

##### <a name="webhcat-level-timeout"></a>Časový limit úrovně WebHCat

Když je WebHCat pod zatížením, s více než 10 otevřenými sokety trvá vytvoření nových připojení soketu déle, což může mít za následek časový limit. Chcete-li zobrazit seznam síťových připojení k WebHCat a z nich, použijte `netstat`  aktuální aktivní hlavnímu uzlu:

```bash
netstat | grep 30111
```

30111 port WebHCat naslouchá. Počet otevřených soketů by měl být menší než 10.

Pokud neexistují žádné otevřené sokety, předchozí příkaz nevytvoří výsledek. Pokud chcete zjistit, jestli je Templeton v provozu a naslouchá na portu 30111, použijte:

```bash
netstat -l | grep 30111
```

##### <a name="yarn-level-timeout"></a>Časový limit úrovně PŘÍZe

Templeton volá PŘÍZi na spouštění úloh a komunikace mezi Templeton a PŘÍZí může způsobit časový limit.

Na úrovni PŘÍZe existují dva typy časových limitů:

1. Odeslání úlohy PŘÍZe může trvat dostatečně dlouho, aby se mohl zapříčinit časový limit.

    Pokud otevřete `/var/log/webhcat/webhcat.log` soubor protokolu a vyhledáte "úloha zařazená do fronty", může se zobrazit více položek, kde doba provádění je příliš dlouhá (>2000 MS), s položkami, které zobrazují zvýšení čekací doby.

    Čas úloh ve frontě se dál zvyšuje, protože frekvence, s jakou se nové úlohy odesílají, je vyšší než frekvence, s jakou jsou staré úlohy dokončené. Jakmile se použije paměť PŘÍZe 100%, *fronta joblauncher* už nemůže půjčit kapacitu z *výchozí fronty*. Proto nelze do fronty joblauncher přijmout žádné další nové úlohy. To může způsobit, že čas čekání bude trvat déle a déle, což způsobí chybu vypršení časového limitu, která obvykle následuje po mnoha dalších.

    Na následujícím obrázku je znázorněná fronta joblauncher na 714,4%, která se používá. To je přijatelné, pokud je stále volná kapacita výchozí fronty k vypůjčení. Pokud je ale cluster plně využíván a paměť PŘÍZe je na 100% kapacitě, musí nové úlohy počkat, což nakonec způsobí vypršení časového limitu.

    ![Zobrazení fronty Spouštěče úloh HDInsight](./media/hdinsight-troubleshoot-failed-cluster/hdi-job-launcher-queue.png)

    Existují dva způsoby, jak tento problém vyřešit: Snižte rychlost odeslání nových úloh nebo zvyšte rychlost spotřeby starých úloh tím, že nakonfigurujete škálování clusteru.

2. Zpracování PŘÍZe může trvat dlouhou dobu, což může způsobit vypršení časových limitů.

    * Vypsat všechny úlohy: Jedná se o časově náročné volání. Toto volání vypíše aplikace z správce prostředků PŘÍZe a pro každou dokončenou aplikaci Získá stav z JobHistoryServer PŘÍZe. U většího počtu úloh může toto volání vyprší časový limit.

    * Seznam úloh, které jsou starší než sedm dní: JobHistoryServer v HDInsight se nakonfiguruje tak, aby uchovávala informace o dokončených úlohách po dobu sedmi dní ( `mapreduce.jobhistory.max-age-ms` hodnota). Při pokusu o výčet vyčištěných úloh dojde k vypršení časového limitu.

Diagnostikujte tyto problémy:

1. Určení rozsahu času UTC pro řešení potíží
2. Vyberte příslušné `webhcat.log` soubory.
3. Vyhledat upozornění a chybové zprávy během této doby

#### <a name="other-webhcat-failures"></a>Další WebHCat selhání

1. Stavový kód HTTP 500

    Ve většině případů, kdy WebHCat vrátí 500, obsahuje chybová zpráva podrobnosti o selhání. V opačném případě se podíváte `webhcat.log` na upozornění a chybové zprávy.

2. Selhání úlohy

    Můžou nastat případy, kdy interakce s WebHCat jsou úspěšné, ale úlohy selžou.

    Templeton shromažďuje výstup konzoly úloh jako `stderr` v `statusdir` , což je často užitečné při řešení potíží. `stderr` obsahuje identifikátor aplikace nitě aktuálního dotazu.

## <a name="step-4-review-the-environment-stack-and-versions"></a>Krok 4: Kontrola zásobníku prostředí a verzí

Stránka zásobník uživatelského rozhraní **a verze** Ambari poskytuje informace o konfiguraci služby Cluster Services a historii verzí služby.  Nesprávná verze knihovny služby Hadoop může způsobovat selhání clusteru.  V uživatelském rozhraní Ambari vyberte nabídku **správce** a pak nastavte  **zásobníky a verze**.  Na stránce vyberte kartu **verze** , kde najdete informace o verzi služby:

![Stack a verze Apache Ambari](./media/hdinsight-troubleshoot-failed-cluster/ambari-stack-versions.png)

## <a name="step-5-examine-the-log-files"></a>Krok 5: Projděte si soubory protokolů

Existuje mnoho typů protokolů, které jsou generovány z mnoha služeb a součástí, které tvoří cluster HDInsight. [Soubory protokolu WebHCat](#check-your-webhcat-service) jsou popsány dříve. Existuje několik dalších užitečných souborů protokolu, které můžete prozkoumat pro zúžení potíží s clusterem, jak je popsáno v následujících částech.

* Clustery HDInsight se skládají z několika uzlů, přičemž většina z nich je spouštěna z úlohy na spouštění odeslaných úloh. Úlohy se spouštějí souběžně, ale soubory protokolu můžou výsledky zobrazit jenom lineárně. HDInsight provádí nové úlohy a ukončí nejprve jiné, které se nedaří dokončit. Veškerá tato aktivita je protokolována do `stderr` `syslog` souborů a.

* Soubory protokolu akcí skriptu zobrazují během procesu vytváření clusteru chyby nebo neočekávané změny konfigurace.

* Protokoly kroku Hadoop identifikují úlohy Hadoop spouštěné v rámci kroku obsahujícího chyby.

### <a name="check-the-script-action-logs"></a>Zkontroluje protokoly akcí skriptů.

[Akce skriptu](hdinsight-hadoop-customize-cluster-linux.md) HDInsight spouštějí skripty v clusteru ručně nebo v případě, že jsou zadané. Například akce skriptu lze použít k instalaci dalšího softwaru do clusteru nebo ke změně nastavení konfigurace z výchozích hodnot. Kontrola protokolů akcí skriptu může poskytnout přehled o chybách, ke kterým došlo během instalace a konfigurace clusteru.  Stav akce skriptu můžete zobrazit tak, že vyberete tlačítko **OPS** v uživatelském rozhraní Ambari nebo přistupujete k protokolům z výchozího účtu úložiště.

Protokoly akcí skriptu se nacházejí v `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` adresáři.

### <a name="view-hdinsight-logs-using-ambari-quick-links"></a>Zobrazení protokolů HDInsight pomocí rychlých odkazů Ambari

Uživatelské rozhraní HDInsight Ambari obsahuje několik oddílů pro **Rychlé odkazy** .  Pro přístup k odkazům na protokol pro konkrétní službu v clusteru HDInsight otevřete uživatelské rozhraní Ambari pro váš cluster a pak vyberte odkaz služby ze seznamu vlevo. Vyberte rozevírací seznam **Rychlé odkazy** , potom uzel HDInsight, který vás zajímá, a pak vyberte odkaz na jeho přidružený protokol.

Například pro protokoly HDFS:

![Ambari rychlé odkazy na soubory protokolu](./media/hdinsight-troubleshoot-failed-cluster/apache-ambari-quick-links.png)

### <a name="view-hadoop-generated-log-files"></a>Zobrazení souborů protokolu generovaných systémem Hadoop

Cluster An HDInsight generuje protokoly zapsané do Azure Tables a Azure Blob Storage. PŘÍZe vytvoří vlastní protokoly spuštění. Další informace najdete v tématu [Správa protokolů pro cluster HDInsight](hdinsight-log-management.md#access-the-hadoop-log-files).

### <a name="review-heap-dumps"></a>Zkontrolovat výpisy haldy

Výpisy haldy obsahují snímek paměti aplikace, včetně hodnot proměnných v daném čase, které jsou užitečné pro diagnostiku problémů, ke kterým dochází za běhu. Další informace najdete v tématu [Povolení výpisů paměti haldy pro Apache Hadoop služby v HDInsight se systémem Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md).

## <a name="step-6-check-configuration-settings"></a>Krok 6: ověření nastavení konfigurace

Clustery HDInsight jsou předem nakonfigurované s výchozím nastavením pro související služby, jako jsou Hadoop, podregistr, HBA a tak dále. V závislosti na typu clusteru, jeho hardwarové konfiguraci, jeho počtu uzlů, typech úloh, které používáte, a datech, se kterými pracujete (a jak se zpracovávají tato data), možná budete muset optimalizovat konfiguraci.

Podrobné pokyny k optimalizaci konfigurací výkonu pro většinu scénářů najdete v tématu [optimalizace konfigurací clusterů pomocí Apache Ambari](hdinsight-changing-configs-via-ambari.md). Pokud používáte Spark, přečtěte si téma [optimalizace úloh Apache Spark pro výkon](spark/apache-spark-perf.md). 

## <a name="step-7-reproduce-the-failure-on-a-different-cluster"></a>Krok 7: reprodukce selhání v jiném clusteru

Aby bylo možné diagnostikovat zdroj chyby clusteru, spusťte nový cluster se stejnou konfigurací a pak znovu odešlete kroky neúspěšné úlohy po jednom. Před zpracováním následujícího kroku Ověřte výsledky jednotlivých kroků. Tato metoda vám dává možnost opravit a znovu spustit jeden neúspěšný krok. Tato metoda má také výhodu, že se vstupní data načítají jenom jednou.

1. Vytvořte nový testovací cluster se stejnou konfigurací, jako má neúspěšný cluster.
2. Odešlete první krok úlohy do testovacího clusteru.
3. Když krok dokončí zpracování, vyhledejte chyby v souborech protokolu kroku. Připojte se k hlavnímu uzlu testovacího clusteru a zobrazte tam soubory protokolu. Krokové soubory protokolu se zobrazí jenom po nějaké době, kdy se tento krok spustí nebo dojde k chybě.
4. Pokud se první krok úspěšně zdařil, spusťte další krok. Pokud došlo k chybám, prozkoumejte chybu v souborech protokolu. Pokud se jedná o chybu ve vašem kódu, proveďte opravu a znovu spusťte krok.
5. Pokračovat, dokud všechny kroky nebudou spuštěny bez chyb.
6. Až skončíte s laděním testovacího clusteru, odstraňte ho.

## <a name="next-steps"></a>Další kroky

* [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Analýza protokolů HDInsight](hdinsight-debug-jobs.md)
* [Přístup k Apache Hadoop PŘÍZ aplikace v HDInsight se systémem Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Povolit výpisy haldy pro Apache Hadoop služby v HDInsight se systémem Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Známé problémy pro cluster Apache Spark v HDInsight](hdinsight-apache-spark-known-issues.md)
