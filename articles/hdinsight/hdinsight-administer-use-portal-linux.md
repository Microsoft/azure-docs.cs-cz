---
title: Správa clusterů Hadoop v HDInsight pomocí webu Azure portal
description: Zjistěte, jak vytvořit a spravovat clustery HDInsight pomocí webu Azure portal.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: jasonh
ms.openlocfilehash: 20a48dcd4a9c3dd4c89390c1048ec4fd5f5783ae
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39597204"
---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Správa clusterů Hadoop v HDInsight pomocí webu Azure portal

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Použití [webu Azure portal][azure-portal], můžete spravovat clustery Hadoop v Azure HDInsight. Informace o správě clusterů Hadoop v HDInsight pomocí jiných nástrojů, pomocí modulu pro výběr karty výše.

**Požadavek**

Chcete-li postupovat podle kroků v tomto článku, budete potřebovat **předplatného Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="open-the-azure-portal"></a>Otevřete na webu Azure portal
1. Přihlaste se k [ https://portal.azure.com ](https://portal.azure.com).
2. Po otevření portálu můžete:

   * Klikněte na tlačítko **vytvořit prostředek** v levé nabídce na vytvoření nového clusteru:

       ![tlačítko Nový cluster HDInsight](./media/hdinsight-administer-use-portal-linux/azure-portal-new-button.png)

       Zadejte **HDInsight** v **Hledat na Marketplace**, klikněte na tlačítko **HDInsight**a potom klikněte na tlačítko **vytvořit**.

   * Klikněte na tlačítko **clustery HDInsight** v levé nabídce na seznam stávajících clusterů:

       ![Azure portal tlačítko clusteru HDInsight](./media/hdinsight-administer-use-portal-linux/azure-portal-hdinsight-button.png)

       Pokud se nezobrazí **clustery HDInsight** tlačítko a pak klikněte na **clustery HDInsight** pod **inteligence a analýza** části.


## <a name="create-clusters"></a>Vytváření clusterů
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

HDInsight funguje s komponentami Hadoop široký rozsah. Seznam součástí, ověřit a podporovaná, najdete v části [je jaká verze systému Hadoop v Azure HDInsight?](hdinsight-component-versioning.md) Informace o vytvoření obecné clusteru, naleznete v tématu [vytváření clusterů Hadoop v HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

### <a name="access-control-requirements"></a>Požadavky na řízení přístupu

Při vytváření clusteru služby HDInsight, je nutné zadat předplatné Azure. Cluster lze vytvořit v nové skupině prostředků Azure nebo v existující skupinu prostředků. Následující kroky můžete ověřit vaše oprávnění pro vytváření clusterů HDInsight:

- Pokud chcete vytvořit novou skupinu prostředků:

    1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
    2. Klikněte na tlačítko **předplatné** v levé nabídce. Obsahuje žlutou ikonu klíče. Zobrazí se seznam předplatných.
    3. Klikněte na předplatné, které použijete k vytvoření clusterů. 
    4. Klikněte na tlačítko **Moje oprávnění**.  Zobrazuje vaše [role](../role-based-access-control/built-in-roles.md) u daného předplatného. Potřebujete aspoň přístup přispěvatele k vytvoření clusteru HDInsight.

- Chcete-li použít existující skupinu prostředků:

    1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
    2. Klikněte na tlačítko **skupiny prostředků** v levé nabídce na seznam skupin prostředků.
    3. Klikněte na skupinu prostředků, kterou chcete použít pro vytvoření clusteru HDInsight.
    4. Klikněte na tlačítko **řízení přístupu (IAM)** a ověřte, že jste (nebo skupiny, musíte patřit do) mají alespoň přístup přispěvatele do skupiny prostředků.

Pokud se zobrazí chyba NoRegisteredProviderFound nebo Chyba MissingSubscriptionRegistration, přečtěte si téma [řešit běžné chyby nasazení v Azure pomocí Azure Resource Manageru](../azure-resource-manager/resource-manager-common-deployment-errors.md).

## <a name="list-and-show-clusters"></a>Seznam a zobrazení clusterů
1. Přihlaste se k [ https://portal.azure.com ](https://portal.azure.com).
2. Klikněte na tlačítko **clustery HDInsight** v levé nabídce na seznam stávajících clusterů. Pokud nevidíte **clustery HDInsight**, klikněte na tlačítko **všechny služby** první.
3. Klikněte na název clusteru. Pokud je dlouhý seznam clusterů, můžete použít filtr horní části stránky.
4. Klikněte na cluster ze seznamu zobrazíte na stránce s přehledem:

    ![Azure portal essentials clusteru HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials.png) **nabídka Přehled služby:**
    * **Řídicí panel**: Otevře webové uživatelské rozhraní Ambari clusteru.
    * **Secure Shell**: zobrazí pokyny pro připojení ke clusteru pomocí připojení Secure Shell (SSH).
    * **Škálování clusteru**: umožňuje změnit počet uzlů pracovního procesu pro tento cluster.
    * **Přesunout**: clusteru přesune do jiné skupiny prostředků nebo do jiného předplatného.
    * **Odstranit**: Odstraní cluster.

    **Levé nabídky:**
    * **Protokoly aktivit**: zobrazení a dotazování protokolů aktivit.
    * **Řízení přístupu (IAM)**: použití přiřazení rolí.  Zobrazit [použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](../role-based-access-control/role-assignments-portal.md).
    * **Značky**: umožňuje nastavit páry klíč/hodnota k definování vlastní taxonomii z vašich cloudových služeb. Můžete například vytvořit klíč s názvem **projektu**a pak použít společné hodnoty pro všechny služby související s konkrétním projektu.
    * **Diagnostikovat a řešit problémy**: Zobrazit informace o odstraňování potíží.
    * **Zamkne**: Přidat zámek proti zabránit clusteru se změnily nebo odstranily.
    * **Automatizační skript**: zobrazení a export šablony Azure Resource Manageru pro cluster. V současné době jde exportovat jenom účet závislého úložiště Azure. Zobrazit [vytvořit systémem Linux Hadoop clusterů v HDInsight pomocí šablon Azure Resource Manageru](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
    * **Rychlý Start**: Zobrazí informace, které vám pomůže začít používat HDInsight.
    * **Nástroje pro HDInsight**: informace o HDInsight související nástroje.
    * **Využití jader předplatného**: Zobrazit používaných a dostupných jader pro vaše předplatné.
    * **Škálování clusteru**: zvýšení a snížení počtu pracovních uzlů clusteru. Zobrazit[škálování clusterů](hdinsight-administer-use-management-portal.md#scale-clusters).
    * **SSH + přihlašovací údaje clusteru**: zobrazí pokyny pro připojení ke clusteru pomocí připojení Secure Shell (SSH). Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
    * **HDInsight Partner**: Přidat nebo odebrat aktuální HDInsight Partner.
    * **Externí Metaúložiště**: Zobrazit metaúložiště Hive a Oozie. Metaúložiště se dá nakonfigurovat jenom během procesu vytváření clusteru. Zobrazit [použít metastore Hive/Oozie](hdinsight-hadoop-provision-linux-clusters.md#use-hiveoozie-metastore).
    * **Akcí skriptů**: Bash spusťte skripty v clusteru. Zobrazit [HDInsight založených na Linuxu přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md).
    * **Aplikace**: HDInsight přidávat nebo odebírat aplikace.  Zobrazit [instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md).
    * **Monitorování**: monitorování clusteru v Azure Log Analytics.
    * **Vlastnosti**: zobrazení vlastností clusteru.
    * **Účty úložiště**: Zobrazit účty úložiště a klíče. Účty úložiště jsou nakonfigurované během procesu vytváření clusteru.
    * **Přístup k data Lake Store**: Konfigurace přístupu ukládá Data Lake.  Zobrazit [rychlý start: nastavení clusterů v HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).
    * **Služba Resource health**: viz [přehled Azure resource health](../service-health/resource-health-overview.md).
    * **Nová žádost o podporu**: vám umožní vytvořit lístek podpory s podporou Microsoftu.
    
6. Klikněte na tlačítko **vlastnosti**:

    Mezi vlastnosti patří:

   * **Název hostitele**: název clusteru.
   * **Adresa URL clusteru**: adresa URL pro webové rozhraní Ambari.
   * **Secure shell (SSH)**: název uživatelské jméno a hostitele má použít při přístupu ke clusteru přes SSH.
   * **Stav**: jeden z: přerušeno, přijetí, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, provozní, s, chyba, odstranění, odstranit, vypršel časový limit, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued ResizeQueued či ClusterCustomization.
   * **Oblast**: umístění Azure. Seznam podporovaných umístění Azure, najdete v článku **oblasti** rozevíracího seznamu na [ceny HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   * **Datum vytvoření**: datum, byl nasazen clusteru.
   * **Operační systém**: buď **Windows** nebo **Linux**.
   * **Typ**: Hadoop, HBase, Storm, Spark.
   * **Verze**. Zobrazit [HDInsight verze](hdinsight-component-versioning.md).
   * **Předplatné**: Název předplatného.
   * **Výchozí zdroj dat**: výchozí systém souborů clusteru.
   * **Velikost uzlů pracovního procesu**: vybrané velikosti virtuálního počítače z pracovních uzlů.
   * **Velikost uzlu HEAD**: hlavní uzly vybrané velikost virtuálního počítače.
   * **Virtuální síť**: název virtuální sítě, které se cluster nasazuje, pokud byl vybrán v době nasazení.

## <a name="delete-clusters"></a>Odstranění clusterů
Odstranění clusteru nedojde k odstranění výchozí účet úložiště ani všechny propojené účty úložiště. Cluster můžete znovu vytvořit pomocí stejné účty úložiště a metaúložišti stejné. Doporučujeme použít nový výchozí kontejner objektu Blob, když znovu vytvoříte cluster.

1. Přihlaste se k [portál][azure-portal].
2. Klikněte na tlačítko **clustery HDInsight** v levé nabídce. Pokud nevidíte **clustery HDInsight**, klikněte na tlačítko **všechny služby** první.
3. Klikněte na cluster, který chcete odstranit.
4. Klikněte na tlačítko **odstranit** z hlavní nabídky a pak postupujte podle pokynů.

Viz také [pozastavit a vypnout clustery](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Přidání dalších účtů úložiště

Po vytvoření clusteru můžete přidat další účty Azure Storage a účty Azure Data Lake Store. Další informace najdete v tématu [Přidání dalších účtů úložiště do služby HDInsight](./hdinsight-hadoop-add-storage.md).

## <a name="scale-clusters"></a>Škálování clusterů
Funkce škálování clusteru umožňuje změnit počet uzlů pracovního procesu používán clusterem Azure HDInsight bez nutnosti nového vytváření clusteru.

> [!NOTE]
> Pouze clustery HDInsight verze 3.1.3 nebo vyšší nejsou podporovány. Pokud si nejste jistí verze vašeho clusteru, můžete zkontrolovat na stránce Vlastnosti.  Zobrazit [výpisu a zobrazení clusterů](#list-and-show-clusters).
>
>

Změna počtu datových uzlů se liší pro každý typ clusteru podporuje HDInsight:

* Hadoop

    Bezproblémově můžete zvýšit počet pracovních uzlů v clusteru Hadoop, na kterém běží bez dopadu na všechny úlohy čekající na vyřízení nebo spuštěné. Nové úlohy můžete odeslat také když probíhá operace. Selhání v rámci operace škálování jsou zpracovány bez výpadku v tak, aby cluster zůstane vždy ve funkčním stavu.

    Pokud je Hadoop cluster je kapacitu vertikálně snížit snížením počtu datových uzlů, jsou restartovat některé ze služeb v clusteru. Toto chování způsobí, že všechny spuštěné a čekající úlohy selhání po dokončení operace škálování. Můžete, ale neúspěšné úlohy po dokončení operace.
* HBase

    Bezproblémově můžete přidat nebo odebrat uzly do clusteru HBase během jejího běhu. Oblastní servery jsou automaticky rovnoměrně rozdělen do několika minut od dokončení operace škálování. Oblastní servery však můžete také ručně vyvážit změnou přihlášení k hlavnímu uzlu clusteru a spustíte tento příkaz z okna příkazového řádku:

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

    Další informace o používání prostředí HBase najdete v tématu [Začínáme s příkladem Apache HBase v HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Storm

    Bezproblémově můžete přidat nebo odebrat datových uzlů do clusteru Storm během jejího běhu. Ale po úspěšném dokončení operace škálování, je potřeba obnovit rovnováhu topologie.

    Opětovné vyvážení lze provést dvěma způsoby:

  * Webové uživatelské rozhraní Storm
  * Nástroje rozhraní příkazového řádku (CLI)

    Odkazovat [dokumentaci Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) další podrobnosti.

    Webové uživatelské rozhraní Storm je k dispozici v clusteru HDInsight:

    ![Obnovení rovnováhy škálování HDInsight Storm](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Tady je příklad příkazu rozhraní příkazového řádku, chcete-li obnovit rovnováhu topologie Storm:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

**Škálování clusterů**

1. Přihlaste se k [portál][azure-portal].
2. Klikněte na tlačítko **clustery HDInsight** v levé nabídce.
3. Klikněte na cluster, který chcete škálovat.
3. Klikněte na tlačítko **škálování clusteru**.
4. Zadejte **počet pracovních uzlů**. Limit počtu uzlů clusteru se liší mezi předplatným Azure. Můžete požádat podporu fakturace o navýšení limitu.  Informace o nákladech odráží změny, které jste provedli z počtu uzlů.

    ![HDInsight hadoop hbase storm spark škálování](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster.png)

## <a name="pauseshut-down-clusters"></a>Pozastavit a vypnout clusterů

Většina úloh Hadoop jsou dávkové úlohy, které jsou pouze čas od času spuštění. Většina clusterů Hadoop existují velké časová období, ve které se cluster nepoužívá pro zpracování. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán.
Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány.

Existuje mnoho způsobů, které můžete naprogramovat procesu:

* Uživatel Azure Data Factory. Zobrazit [vytvořit na vyžádání založené na Linuxu Hadoop clusterů v HDInsight pomocí Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) pro vytváření HDInsight na vyžádání propojené služby.
* Použití Azure Powershellu.  Zobrazit [analyzovat zpoždění letů](hdinsight-analyze-flight-delay-data.md).
* Pomocí Azure CLI. Zobrazit [HDInsight Správa clusterů pomocí rozhraní příkazového řádku Azure](hdinsight-administer-use-command-line.md).
* Použití sady HDInsight .NET SDK. Zobrazit [úlohy systému Hadoop odeslat](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Informace o cenách najdete v části [ceny HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Pokud chcete odstranit cluster z portálu, přečtěte si téma [odstranění clusterů](#delete-clusters)

## <a name="move-cluster"></a>Přesunout cluster

Cluster služby HDInsight můžete přesunout do jiné skupiny prostředků Azure nebo jiného předplatného.  Zobrazit [výpisu a zobrazení clusterů](#list-and-show-clusters).

## <a name="upgrade-clusters"></a>Upgradovat clustery

Zobrazit [clusteru HDInsight Upgrade na novější verzi](./hdinsight-upgrade-cluster.md).

## <a name="open-the-ambari-web-ui"></a>Otevřete webové uživatelské rozhraní Ambari

Ambari obsahuje intuitivní a snadno použitelné Hadoop správu webovému rozhraní uživatelského rozhraní se opírá o jeho rozhraní REST API. Ambari umožňuje správcům systému pro správu a monitorování clusterů systému Hadoop.

1. Otevřete HDInsight cluster z portálu Azure portal.  Zobrazit [výpisu a zobrazení clusterů](#list-and-show-clusters).
2. Klikněte na tlačítko **řídicí panel clusteru**.

    ![Nabídka clusteru HDInsight Hadoop](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu.png)

1. Zadejte uživatelské jméno clusteru a heslo.  Výchozí uživatelské jméno clusteru _správce_. Webové rozhraní Ambari uživatelského rozhraní vypadá takto:

    ![Uživatelské rozhraní Ambari Web HDInsight Hadoop](./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-ambari-web-ui.png)

Další informace najdete v tématu [HDInsight Správa clusterů pomocí webového uživatelského rozhraní Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Změna hesla
HDInsight cluster může mít dva uživatelské účty. HDInsight clusteru (označovaný také jako uživatelský účet. HTTP uživatelský účet) a uživatelský účet SSH se vytvoří během procesu vytváření. Chcete-li změnit uživatelské jméno účtu uživatele clusteru a heslo a akce skriptu ke změně uživatelského účtu SSH můžete použít webové uživatelské rozhraní Ambari

### <a name="change-the-cluster-user-password"></a>Změna hesla uživatele clusteru
Chcete-li změnit heslo uživatele clusteru můžete použít webové uživatelské rozhraní Ambari. Pro přihlášení k Ambari, musíte použít existující cluster uživatelské jméno a heslo.

> [!NOTE]
> Změna hesla uživatele (správce) clusteru může způsobit spustit tento cluster selhání akce skriptu. Pokud máte jakékoli trvalé akce se skripty této cílové uzly pracovního procesu, tyto skripty může selhat, když přidáte uzly clusteru prostřednictvím změnit velikost operace. Další informace o akcí skriptů najdete v tématu [HDInsight přizpůsobit clustery pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md).
>
>

1. Přihlaste se k webovému uživatelskému rozhraní Ambari pomocí přihlašovacích údajů uživatele clusteru HDInsight. Výchozí uživatelské jméno **admin**. Adresa URL je **https://&lt;název clusteru HDInsight > azurehdinsight.net**.
2. Klikněte na tlačítko **správce** z hlavní nabídky a potom klikněte na tlačítko "Ambari spravovat".
3. V levé nabídce klikněte na tlačítko **uživatelé**.
4. Klikněte na tlačítko **správce**.
5. Klikněte na tlačítko **změnit heslo**.

Ambari poté změní heslo na všech uzlech v clusteru.

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
3. Na webu Azure Portal, klikněte na tlačítko **clustery HDInsight**.
4. Klikněte na HDInsight cluster.
4. Klikněte na tlačítko **akcí skriptů**.
4. Z **akcí skriptů** okně vyberte **odeslat novou**. Když **odeslat akci skriptu** zobrazí se okno, zadejte následující informace:

   | Pole | Hodnota |
   | --- | --- |
   | Název |Ssh heslo změnit |
   | URI skriptu Bash |Identifikátor URI souboru changepassword.sh |
   | Uzly (vedoucí, pracovního procesu, Nimbus, správce, Zookeeper, atd.) |✓ pro všechny typy uzlů, které jsou uvedené |
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

Tyto služby jsou ve výchozím nastavení oprávnění pro přístup. Vám může k nim odvolat/udělení přístupu pomocí [rozhraní příkazového řádku Azure](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) a [prostředí Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access).

## <a name="find-the-subscription-id"></a>Najít ID předplatného

**Chcete-li najít vaše ID předplatných Azure**

1. Přihlaste se k [portál][azure-portal].
2. Klikněte na tlačítko **předplatná**. Každé předplatné má název a identifikátor.

Každý cluster se váže k předplatnému Azure. Předplatné ID se zobrazí na clusteru **základní** dlaždici. Zobrazit [výpisu a zobrazení clusterů](#list-and-show-clusters).

## <a name="find-the-resource-group"></a>Najít skupinu prostředků
V režimu Azure Resource Manageru se vytvoří každý cluster HDInsight s Azure Resource Manageru skupinou. Skupinu Resource Manageru, které patří clusteru se zobrazí v:

* Obsahuje seznam clusterů **skupiny prostředků** sloupce.
* Cluster **základní** dlaždici.  

Zobrazit [výpisu a zobrazení clusterů](#list-and-show-clusters).

## <a name="find-the-storage-accounts"></a>Najít všechny účty úložišť

Clustery HDInsight používat k ukládání dat účtu služby Azure Storage nebo Azure Data Lake Store. Každý cluster HDInsight může mít jeden výchozí účet úložiště a počet propojené účty úložiště. Seznam účtů úložiště, nejdřív otevřete cluster z portálu a potom klikněte na tlačítko **účty úložiště**:

![Účty úložiště clusteru HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-storage-accounts.png)

Na předchozím snímku obrazovky je __výchozí__ sloupce a udává, zda je účet výchozí účet úložiště.

Výpis účtů Data Lake Store, klikněte na tlačítko **přístupu k Data Lake Store** na předchozím snímku obrazovky.

## <a name="run-hive-queries"></a>Spuštění dotazů Hive
Nelze spustit úlohy Hive přímo z webu Azure portal, ale můžete zobrazit Hive na webové uživatelské rozhraní Ambari.

**Ke spouštění dotazů Hive pomocí zobrazení Ambari Hive**

1. Přihlaste se k webovému uživatelskému rozhraní Ambari pomocí přihlašovacích údajů uživatele clusteru HDInsight. Výchozí uživatelské jméno **admin**. Adresa URL je **https://&lt;název clusteru HDInsight > azurehdinsight.net**.
2. Otevřete zobrazení Hive, jak je znázorněno na následujícím snímku obrazovky:  

    ![Zobrazení hive HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-hive-view.png)

3. Klikněte na tlačítko **dotazu** z hlavní nabídky.
4. Zadejte dotaz Hive v **editoru dotazů**a potom klikněte na tlačítko **Execute**.

## <a name="monitor-jobs"></a>Monitorování úloh
Zobrazit [HDInsight Správa clusterů pomocí webového uživatelského rozhraní Ambari](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="browse-files"></a>Procházet soubory
Pomocí webu Azure portal, můžete procházet obsah výchozího kontejneru.

1. Přihlaste se k [ https://portal.azure.com ](https://portal.azure.com).
2. Klikněte na tlačítko **clustery HDInsight** v levé nabídce na seznam stávajících clusterů.
3. Klikněte na název clusteru. Pokud je dlouhý seznam clusterů, můžete použít filtr horní části stránky.
4. Klikněte na tlačítko **účty úložiště** v levé nabídce clusteru.
5. Klikněte na účet úložiště.
7. Klikněte na tlačítko **objekty BLOB** dlaždici.
8. Klikněte na výchozí název kontejneru.

## <a name="monitor-cluster-usage"></a>Monitorování využití clusteru
**Využití** části z okna clusteru HDInsight se zobrazují informace o počet jader dostupných pro vaše předplatné pro použití s HDInsight, jakož i počet jader, které jsou přiděleny do tohoto clusteru a jak se přidělují pro uzly v tomto clusteru. Zobrazit [výpisu a zobrazení clusterů](#list-and-show-clusters).

> [!IMPORTANT]
> Pokud chcete monitorovat služby poskytované clusteru HDInsight, musíte použít Ambari Web nebo Ambari REST API. Další informace o použití Ambari, naleznete v tématu [HDInsight Správa clusterů pomocí nástroje Ambari](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Připojení ke clusteru

* [Použití Hivu se službou HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Použití SSH s HDInsightem](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Další postup

V tomto článku jste se naučili některé základní funkce pro správu. Další informace naleznete v následujících článcích:

* [Správa HDInsight pomocí Azure Powershellu](hdinsight-administer-use-powershell.md)
* [Správa HDInsight pomocí Azure CLI](hdinsight-administer-use-command-line.md)
* [Vytvoření clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Další informace o pomocí webového uživatelského rozhraní Ambari](hdinsight-hadoop-manage-ambari.md)
* [Podrobnosti o použití rozhraní Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Použití Hivu ve službě HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití Pigu se v HDInsight](hadoop/hdinsight-use-pig.md)
* [V HDInsight pomocí Sqoop](hadoop/hdinsight-use-sqoop.md)
* [Začínáme s Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Jaká verze systému Hadoop je v Azure HDInsight?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Hadoop příkazového řádku"
