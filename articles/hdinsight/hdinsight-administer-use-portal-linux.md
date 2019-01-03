---
title: Správa clusterů systému Apache Hadoop v HDInsight pomocí webu Azure portal
description: Zjistěte, jak vytvořit a spravovat clustery HDInsight pomocí webu Azure portal.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: hrasheed
ms.openlocfilehash: ce30b752ecf1d5413ae534fa03907cbf11b1c694
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53794477"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Spravovat clustery systému Apache Hadoop v HDInsight pomocí webu Azure portal

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Použití [webu Azure portal][azure-portal], můžete spravovat [Apache Hadoop](https://hadoop.apache.org/) clustery v Azure HDInsight. Informace o správě clusterů Hadoop v HDInsight pomocí jiných nástrojů, pomocí modulu pro výběr karty výše.

**Požadavky**
- Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Existující cluster Apache Hadoop v HDInsight.  Zobrazit [vytvoření linuxových clusterech v HDInsight pomocí webu Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="getting-started"></a>Začínáme
Přihlaste se k webu [https://portal.azure.com](https://portal.azure.com).


## <a name="showClusters"></a> Seznam a zobrazení clusterů
**Clustery HDInsight** stránky se zobrazí seznam stávajících clusterů.  Z portálu:
1. Vyberte **všechny služby** v levé nabídce.
2. Vyberte **clustery HDInsight** pod **ANALYTICS**.

## <a name="homePage"></a> Domovská stránka clusteru 
Vyberte název vašeho clusteru z [ **clustery HDInsight** stránky](#showClusters).  Tím se otevře **přehled** zobrazení, která vypadá podobně jako na následujícím obrázku:

![Azure portal essentials clusteru HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png)

**Hlavní nabídka:**  
- **Přesunout**: Cluster se přesune do jiné skupiny prostředků nebo do jiného předplatného.  
- **Odstranit**: Odstraní cluster.  
- **Aktualizovat**:  Aktualizuje zobrazení.

**Levé nabídky:**  
 - **Nabídku vlevo nahoře**  
    - **Přehled**:  Obsahuje obecné informace pro váš cluster.
    -  **Protokol aktivit**: Zobrazení a dotazování protokolů aktivit.
    - **Řízení přístupu (IAM)**: Použití přiřazení rolí.  Zobrazit [použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](../role-based-access-control/role-assignments-portal.md).
    - **Značky**: Umožňuje nastavit páry klíč/hodnota k definování vlastní taxonomii z vašich cloudových služeb. Můžete například vytvořit klíč s názvem **projektu**a pak použít společné hodnoty pro všechny služby související s konkrétním projektu.
    - **Diagnostikovat a řešit problémy**: Zobrazte informace o odstraňování potíží.
    - **Rychlý Start**:  Zobrazí informace, které vám pomůže začít používat HDInsight.
    - **Nástroje**: Nápověda pro HDInsight související nástroje.

- **Nabídka nastavení**  
  - **Velikost clusteru**: Kontrola, zvýšit a snížit počet pracovních uzlů clusteru. Zobrazit [škálování clusterů](hdinsight-administer-use-management-portal.md#scale-clusters).
  - **Kvóty**: Zobrazte používaných a dostupných jader pro vaše předplatné.
  - **SSH + přihlašovací údaje clusteru**: Zobrazí pokyny pro připojení ke clusteru pomocí připojení Secure Shell (SSH). Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
  - **Data Lake Storage Gen1**: Konfigurace přístupu k Data Lake Storage Gen1.  Zobrazit [rychlý start: Nastavení clusterů v HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).
  - **Účty úložiště**: Zobrazte účty úložiště a klíče. Účty úložiště jsou nakonfigurované během procesu vytváření clusteru.
  - **Aplikace**: Přidání nebo odebrání aplikací HDInsight.  Zobrazit [instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md).
  - **Akcí skriptů**: Spouštění skriptů Bash v clusteru. Zobrazit [HDInsight založených na Linuxu přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md).
  - **Externí metaúložiště**: Zobrazení [Apache Hive](https://hive.apache.org/) a [Apache Oozie](https://oozie.apache.org/) metaúložiště. Metaúložiště se dá nakonfigurovat jenom během procesu vytváření clusteru.
  - **HDInsight partner**: Přidat nebo odebrat aktuální HDInsight Partner.
  - **Vlastnosti**: Zobrazení [vlastnosti clusteru](#properties).
  - **Zamkne**: Přidáte zámek proti zabránit clusteru se změnily nebo odstranily.
  -  **Automatizační skript**: Zobrazení a export šablony Azure Resource Manageru pro cluster. V současné době jde exportovat jenom účet závislého úložiště Azure. Zobrazit [clustery založené na Linuxu se vytvořit Apache Hadoop v HDInsight pomocí šablon Azure Resource Manageru](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

- **Monitorování nabídky**
  - **Výstrahy**: Správa výstrah a akce.
  - **Metriky**: Monitorujte metriky clusteru v Azure Log Analytics.
  - **Nastavení diagnostiky**: Nastavení, ve kterém můžete ukládat metriky diagnostiku.
  - **Operations Management Suite**:  Monitorování clusteru v Azure Operations Management Suite (OMS) a Azure Log Analytics.

- **Podpora a řešení potíží nabídky**
  - **Služba Resource health**: Zobrazit [přehled Azure resource health](../service-health/resource-health-overview.md).
  - **Nová žádost o podporu**: Umožňuje vytvořit lístek podpory s podporou Microsoftu.
    
## <a name="properties"></a> Vlastnosti clusteru
Z [clusteru domovskou stránku](#homePage)v části **nastavení** vyberte **vlastnosti**.
* **Název hostitele**: Název clusteru.
* **Adresa URL clusteru**: Adresa URL pro webové rozhraní Ambari.
* **Secure shell (SSH)**: Uživatelské jméno a název hostitele použít při přístupu ke clusteru přes SSH.
* **Stav**: Jedna z: Bylo přerušeno, přijetí, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, provozní, s, chyba, odstranění, odstranit, vypršel časový limit, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued, nebo ClusterCustomization.
* **Oblast**: Umístění Azure. Seznam podporovaných umístění Azure, najdete v článku **oblasti** pole rozevíracího seznamu na [ceny HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
* **Datum vytvoření**: Datum, kdy byl nasazen clusteru.
* **Operační systém**: Buď **Windows** nebo **Linux**.
* **Typ**: Hadoop, HBase, Storm, Spark.
* **Verze**. Zobrazit [HDInsight verze](hdinsight-component-versioning.md).
* **Předplatné**: Název předplatného
* **Výchozí zdroj dat**: Výchozí systém souborů clusteru.
* **Velikost uzlů pracovního procesu**: Velikost vybraného virtuálního počítače pracovních uzlů.
* **Velikost uzlu HEAD**: Velikost vybraného virtuálního počítače hlavní uzly.
* **Virtuální síť**: Název virtuální sítě, které se cluster nasazuje, pokud byl vybrán v době nasazení.

## <a name="move-clusters"></a>Přesunout clusterů

Cluster služby HDInsight můžete přesunout do jiné skupiny prostředků Azure nebo jiného předplatného. 

Z [clusteru domovskou stránku](#homePage):

1. Vyberte **přesunout** z hlavní nabídky.
2. Vyberte **přesunout do jiné skupiny prostředků** nebo **přesunout do jiného předplatného**.
3. Postupujte podle pokynů na stránce Nový.

## <a name="delete-clusters"></a>Odstranění clusterů
Odstranění clusteru nedojde k odstranění výchozí účet úložiště ani všechny propojené účty úložiště. Cluster můžete znovu vytvořit pomocí stejné účty úložiště a metaúložišti stejné. Doporučujeme použít nový výchozí kontejner objektu Blob, když znovu vytvoříte cluster.

Z [clusteru domovskou stránku](#homePage):

1. Vyberte **odstranit** z hlavní nabídky.
2. Postupujte podle pokynů na stránce Nový.

Viz také [pozastavit a vypnout clustery](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Přidání dalších účtů úložiště

Po vytvoření clusteru můžete přidat další účty Azure Storage a účty úložiště Azure Data Lake. Další informace najdete v tématu [Přidání dalších účtů úložiště do služby HDInsight](./hdinsight-hadoop-add-storage.md).

## <a name="scale-clusters"></a>Škálování clusterů
Funkce škálování clusteru umožňuje změnit počet uzlů pracovního procesu používán clusterem Azure HDInsight bez nutnosti nového vytváření clusteru.

> [!NOTE]  
> Pouze clustery HDInsight verze 3.1.3 nebo vyšší nejsou podporovány. Pokud si nejste jistí verze vašeho clusteru, můžete zkontrolovat na stránce Vlastnosti.  Zobrazit [výpisu a zobrazení clusterů](#list-and-show-clusters).

Z [clusteru domovskou stránku](#homePage):

1. V části **nastavení**vyberte **velikost clusteru**.
2. Zadejte **počet pracovních uzlů** číselné textového pole. Limit počtu uzlů clusteru se liší mezi předplatným Azure. Můžete požádat podporu fakturace o navýšení limitu.  Informace o nákladech odráží změny, které jste provedli z počtu uzlů.
3. Vyberte **Uložit**.

    ![HDInsight hadoop hbase storm spark škálování](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster2.png)

Změna počtu datových uzlů se liší pro každý typ clusteru podporuje HDInsight:

* Apache Hadoop

    Bezproblémově můžete zvýšit počet pracovních uzlů v clusteru Hadoop, na kterém běží bez dopadu na všechny úlohy čekající na vyřízení nebo spuštěné. Nové úlohy můžete odeslat také když probíhá operace. Selhání v rámci operace škálování jsou zpracovány bez výpadku v tak, aby cluster zůstane vždy ve funkčním stavu.

    Pokud je Hadoop cluster je kapacitu vertikálně snížit snížením počtu datových uzlů, jsou restartovat některé ze služeb v clusteru. Toto chování způsobí, že všechny spuštěné a čekající úlohy selhání po dokončení operace škálování. Můžete, ale neúspěšné úlohy po dokončení operace.
* Apache HBase

    Bezproblémově můžete přidat nebo odebrat uzly do clusteru HBase během jejího běhu. Oblastní servery jsou automaticky rovnoměrně rozdělen do několika minut od dokončení operace škálování. Oblastní servery však můžete také ručně vyvážit změnou přihlášení k hlavnímu uzlu clusteru a spustíte tento příkaz z okna příkazového řádku:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Další informace o používání prostředí HBase najdete v tématu [Začínáme s příkladem Apache HBase v HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Bezproblémově můžete přidat nebo odebrat datových uzlů do clusteru Storm během jejího běhu. Ale po úspěšném dokončení operace škálování, je potřeba obnovit rovnováhu topologie.

    Opětovné vyvážení lze provést dvěma způsoby:

  * Webové uživatelské rozhraní Storm
  * Nástroje rozhraní příkazového řádku (CLI)

    Odkazovat [dokumentaci Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) další podrobnosti.

    Webové uživatelské rozhraní Storm je k dispozici v clusteru HDInsight:

    ![Obnovení rovnováhy škálování HDInsight Storm](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Tady je příklad příkazu rozhraní příkazového řádku, chcete-li obnovit rovnováhu topologie Storm:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```


## <a name="pauseshut-down-clusters"></a>Pozastavit a vypnout clusterů

Většina úloh Hadoop jsou dávkové úlohy, které jsou pouze čas od času spuštění. Většina clusterů Hadoop existují velké časová období, ve které se cluster nepoužívá pro zpracování. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán.
Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány.

Existuje mnoho způsobů, které můžete naprogramovat procesu:

* Uživatel Azure Data Factory. Zobrazit [vytvořit na vyžádání založené na Linuxu Apache Hadoop clusterů v HDInsight pomocí Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) pro vytváření HDInsight na vyžádání propojené služby.
* Použití Azure Powershellu.  Zobrazit [analyzovat zpoždění letů](hdinsight-analyze-flight-delay-data.md).
* Pomocí příkazového řádku Azure Classic. Zobrazit [HDInsight Správa clusterů pomocí rozhraní příkazového řádku Azure Classic](hdinsight-administer-use-command-line.md).
* Použití sady HDInsight .NET SDK. Zobrazit [úlohy odeslání Apache Hadoop](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Informace o cenách najdete v části [ceny HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Pokud chcete odstranit cluster z portálu, přečtěte si téma [odstranění clusterů](#delete-clusters)



## <a name="upgrade-clusters"></a>Upgradovat clustery

Zobrazit [clusteru HDInsight Upgrade na novější verzi](./hdinsight-upgrade-cluster.md).

## <a name="open-the-apache-ambari-web-ui"></a>Otevřete webové uživatelské rozhraní Apache Ambari

Ambari obsahuje intuitivní a snadno použitelné Hadoop správu webovému rozhraní uživatelského rozhraní se opírá o jeho rozhraní REST API. Ambari umožňuje správcům systému pro správu a monitorování clusterů systému Hadoop.

Z [clusteru domovskou stránku](#homePage):

1. Vyberte **řídicí panely clusteru**.

    ![Nabídka clusteru HDInsight Hadoop](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png)

1. Vyberte **Ambari domácí** z nové stránky.
2. Zadejte uživatelské jméno clusteru a heslo.  Výchozí uživatelské jméno clusteru _správce_. Webové rozhraní Ambari uživatelského rozhraní vypadá takto:

Další informace najdete v tématu [HDInsight Správa clusterů pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Změna hesla
HDInsight cluster může mít dva uživatelské účty. HDInsight clusteru (označovaný také jako uživatelský účet. HTTP uživatelský účet) a uživatelský účet SSH se vytvoří během procesu vytváření. Na portálu můžete použít ke změně hesla uživatelského účtu clusteru a akce skriptu ke změně uživatelského účtu SSH.

### <a name="change-the-cluster-user-password"></a>Změna hesla uživatele clusteru

> [!NOTE]  
> Změna hesla uživatele (správce) clusteru může způsobit spustit tento cluster selhání akce skriptu. Pokud máte jakékoli trvalé akce se skripty této cílové uzly pracovního procesu, tyto skripty může selhat, když přidáte uzly clusteru prostřednictvím změnit velikost operace. Další informace o akcí skriptů najdete v tématu [HDInsight přizpůsobit clustery pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md).

Z [clusteru domovskou stránku](#homePage):
1. Vyberte **SSH + clusteru přihlášení** pod **nastavení**.
2. Vyberte **resetovat přihlašovací údaje**.
3. Zadejte a potvrďte nové heslo do textových polí.
4. Vyberte **OK**.

Heslo je změněno na všech uzlech v clusteru.

### <a name="change-the-ssh-user-password"></a>Změňte heslo uživatele SSH
1. Pomocí textového editoru, uložte následující text do souboru s názvem **changepassword.sh**.

    > [!IMPORTANT]  
    > Je nutné použít editor, který používá LF jako ukončení řádku. Pokud editor používá CRLF, skript se nefunguje.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Nahrajte soubor do umístění úložiště, který je přístupný z HDInsight pomocí adresy protokolu HTTP nebo HTTPS. Například soubor veřejného ukládat jako je například OneDrive nebo Azure Blob storage. Uložte identifikátor URI (adresu HTTP nebo HTTPS) k souboru, jak pomocí tohoto identifikátoru URI je potřeba v dalším kroku.
3. Z [clusteru domovskou stránku](#homePage) vyberte **akcí skriptů** pod **nastavení**.
4. Z **akcí skriptů** okně vyberte **odeslat novou**. 
5. Z **odeslat akci skriptu** okně zadejte následující informace:

   | Pole | Hodnota |
   | --- | --- |
   | Typ skriptu | Vyberte **– vlastní** z rozevíracího seznamu.|
   | Název |"Změnit ssh heslo" |
   | URI skriptu Bash |Identifikátor URI souboru changepassword.sh |
   | Typy uzlů: (Vedoucí, pracovního procesu, Nimbus, správce, Zookeeper, atd.) |✓ pro všechny typy uzlů, které jsou uvedené |
   | Parametry |Zadejte uživatelské jméno SSH a pak nové heslo. Měla by existovat jednu mezeru mezi uživatelské jméno a heslo. |
   | Zachovat tuto akci se skripty... |Nechejte pole nezaškrtnuté. |
5. Vyberte **vytvořit** použít skript. Po dokončení skriptu budete moct připojit ke clusteru pomocí SSH pomocí nového hesla.

## <a name="grantrevoke-access"></a>Udělení nebo odvolání přístupu
Clustery HDInsight mají následující webové služby HTTP (mít všechny tyto služby RESTful koncových bodů):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Tyto služby jsou ve výchozím nastavení oprávnění pro přístup. Vám může k nim odvolat/udělení přístupu pomocí [rozhraní příkazového řádku Azure Classic](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) a [prostředí Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access).

## <a name="find-the-subscription-id"></a>Najít ID předplatného
Každý cluster se váže k předplatnému Azure.  ID je viditelná z předplatného Azure [clusteru domovskou stránku](#homePage).

## <a name="find-the-resource-group"></a>Najít skupinu prostředků
V režimu Azure Resource Manageru se vytvoří každý cluster HDInsight s Azure Resource Manageru skupinou. Skupina Resource Manageru je viditelný [clusteru domovskou stránku](#homePage).

## <a name="find-the-storage-accounts"></a>Najít všechny účty úložišť
Clustery HDInsight pomocí účtu Azure Storage nebo Azure Data Lake Storage k ukládání dat. Každý cluster HDInsight může mít jeden výchozí účet úložiště a počet propojené účty úložiště. Seznam účtů úložiště z [clusteru domovskou stránku](#homePage) pod **nastavení**vyberte **účty úložiště**.


## <a name="monitor-jobs"></a>Monitorování úloh
Zobrazit [HDInsight Správa clusterů pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md#monitoring).


## <a name="monitor-cluster-usage"></a>Monitorování využití clusteru
**Využití** části z okna clusteru HDInsight se zobrazují informace o počet jader dostupných pro vaše předplatné pro použití s HDInsight, jakož i počet jader, které jsou přiděleny do tohoto clusteru a jak se přidělují pro uzly v tomto clusteru. Zobrazit [výpisu a zobrazení clusterů](#list-and-show-clusters).

> [!IMPORTANT]  
> Pokud chcete monitorovat služby poskytované clusteru HDInsight, musíte použít Ambari Web nebo Ambari REST API. Další informace o použití Ambari, naleznete v tématu [HDInsight Správa clusterů pomocí nástroje Apache Ambari](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Připojení ke clusteru

* [Použití Apache Hivu se službou HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Použití SSH s HDInsightem](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Další postup

V tomto článku jste se naučili některé základní funkce pro správu. Další informace naleznete v následujících článcích:

* [Správa HDInsight pomocí Azure Powershellu](hdinsight-administer-use-powershell.md)
* [Správa HDInsight pomocí příkazového řádku Azure Classic](hdinsight-administer-use-command-line.md)
* [Vytvoření clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Další informace o pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Podrobnosti o použití rozhraní Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Použití Apache Hivu ve službě HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití Apache Pig v HDInsight](hadoop/hdinsight-use-pig.md)
* [Použití Apache Sqoop v HDInsight](hadoop/hdinsight-use-sqoop.md)
* [Začínáme s Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Jakou verzi Apache Hadoop je v Azure HDInsight?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Hadoop příkazového řádku"
