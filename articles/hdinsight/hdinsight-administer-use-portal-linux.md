---
title: Správa clusterů systému Hadoop v HDInsight pomocí portálu Azure | Microsoft Docs
description: Naučte se vytvářet a spravovat clusterů HDInsight pomocí portálu Azure.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5a76f897-02e8-4437-8f2b-4fb12225854a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: jgao
ms.openlocfilehash: bcfa7a6b9a2ebd60ec804fe8a61c26300f003a5e
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097780"
---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Správa clusterů systému Hadoop v HDInsight pomocí portálu Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Pomocí [portál Azure][azure-portal], můžete spravovat clustery systému Hadoop v prostředí Azure HDInsight. Pomocí modulu pro výběr karty výše informace o správě clusterů systému Hadoop v HDInsight pomocí jiných nástrojů.

**Požadavek**

Chcete-li postupujte podle kroků v tomto článku, budete potřebovat **předplatné**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="open-the-azure-portal"></a>Otevřete portál Azure
1. Přihlaste se k [ https://portal.azure.com ](https://portal.azure.com).
2. Po otevření portálu můžete:

   * Klikněte na tlačítko **vytvořit prostředek** v levé nabídce na vytvoření nového clusteru:

       ![tlačítko Nový cluster HDInsight](./media/hdinsight-administer-use-portal-linux/azure-portal-new-button.png)

       Zadejte **HDInsight** v **vyhledávání na webu Marketplace**, klikněte na tlačítko **HDInsight**a potom klikněte na **vytvořit**.

   * Klikněte na tlačítko **clustery HDInsight** v levé nabídce seznam stávajících clusterů:

       ![Azure portálu tlačítko clusteru HDInsight](./media/hdinsight-administer-use-portal-linux/azure-portal-hdinsight-button.png)

       Pokud nevidíte **clustery HDInsight** tlačítko a potom klikněte na **clustery HDInsight** pod **Intelligence + analýzy** části.


## <a name="create-clusters"></a>Vytváření clusterů
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

HDInsight funguje s komponentami široký rozsah Hadoop. Seznam součástí, které jsou ověření a podporované najdete v tématu [jaká verze Hadoop je v Azure HDInsight?](hdinsight-component-versioning.md) Informace o vytvoření obecné clusteru, najdete v části [vytvoření Hadoop clusterů v HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

### <a name="access-control-requirements"></a>Požadavky na řízení přístupu

Předplatné Azure je třeba zadat při vytváření clusteru HDInsight. Cluster vytvořením nové skupiny prostředků Azure nebo v existující skupinu prostředků. Ověření oprávnění pro vytváření clusterů HDInsight, můžete použít následující kroky:

- Pokud chcete vytvořit novou skupinu prostředků:

    1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
    2. Klikněte na tlačítko **předplatné** v levé nabídce. Obsahuje žlutou ikonu klíče. Zobrazí se seznam předplatných.
    3. Klikněte na předplatné, který použijete k vytvoření clusterů. 
    4. Klikněte na tlačítko **Moje oprávnění**.  Zobrazuje vaše [role](../role-based-access-control/built-in-roles.md) u předplatného. Musí být minimálně Přispěvatel přístup k vytvoření clusteru HDInsight.

- Chcete-li použít existující skupinu prostředků:

    1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
    2. Klikněte na tlačítko **skupiny prostředků** v levé nabídce na seznam skupin prostředků.
    3. Klikněte na skupinu prostředků, kterou chcete použít pro vytvoření clusteru HDInsight.
    4. Klikněte na tlačítko **přístup k ovládacímu prvku (IAM)** a ověřte, že vy (nebo patří do skupiny) mají alespoň Přispěvatel přístup ke skupině prostředků.

Pokud se zobrazí chyba NoRegisteredProviderFound nebo MissingSubscriptionRegistration chyba, přečtěte si téma [odstraňování běžných chyb nasazení Azure pomocí Azure Resource Manageru](../azure-resource-manager/resource-manager-common-deployment-errors.md).

## <a name="list-and-show-clusters"></a>Seznam a zobrazit clustery
1. Přihlaste se k [ https://portal.azure.com ](https://portal.azure.com).
2. Klikněte na tlačítko **clustery HDInsight** v levé nabídce seznam stávajících clusterů. Pokud nevidíte **clustery HDInsight**, klikněte na tlačítko **všechny služby** první.
3. Klikněte na název clusteru. Pokud je seznam clusteru dlouho, můžete použít možnosti filtrovat horní části stránky.
4. Klikněte na cluster, ze seznamu na stránce Přehled v tématu:

    ![Azure portálu essentials clusteru HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials.png) **přehled nabídky:**
    * **Řídicí panel**: Otevře webovému uživatelskému rozhraní Ambari pro cluster.
    * **Secure Shell**: zobrazí pokyny pro připojení ke clusteru pomocí připojení Secure Shell (SSH).
    * **Škálování clusteru**: umožňuje změnit počet uzlů pracovního procesu pro tento cluster.
    * **Přesunout**: Přesune clusteru k jiné skupině prostředků nebo do jiného předplatného.
    * **Odstranit**: Odstraní clusteru.

    **Levé nabídce:**
    * **Protokoly aktivity**: zobrazení a dotaz aktivity protokoly.
    * **Přístup k ovládacímu prvku (IAM)**: použití přiřazení rolí.  V tématu [použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](../role-based-access-control/role-assignments-portal.md).
    * **Značky**: můžete nastavit páry klíč/hodnota k definování vlastní taxonomii cloudových služeb. Můžete například vytvořit klíč s názvem **projektu**a potom používat běžné hodnotu pro všechny služby související s konkrétní projekt.
    * **Diagnostika a řešení problémů**: Zobrazit informace o odstraňování potíží.
    * **Zamkne**: uzamčení, aby se zabránilo clusteru se přidat, upravit nebo odstranit.
    * **Skriptu pro automatizaci**: zobrazení a export šablony Azure Resource Manageru pro cluster. V současné době můžete exportovat pouze účet závislého úložiště Azure. V tématu [vytvořit systémem Linux Hadoop clusterů v HDInsight pomocí šablony Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
    * **Rychlý Start**: Zobrazí informace, které vám pomůžou začněte používat HDInsight.
    * **Nástroje pro HDInsight**: informace nápovědy pro HDInsight související nástroje.
    * **Použití jádra předplatné**: Zobrazit jádra dostupná a použitá pro vaše předplatné.
    * **Škálování clusteru**: Zvyšte a snižte počet uzlů pracovního procesu clusteru. V tématu[škálování clusterů](hdinsight-administer-use-management-portal.md#scale-clusters).
    * **SSH + clusteru přihlášení**: zobrazí pokyny pro připojení ke clusteru pomocí připojení Secure Shell (SSH). Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
    * **Partnera HDInsight**: Přidat nebo odebrat současného partnera HDInsight.
    * **Externí Metaúložiště**: Zobrazit metaúložiště Hive a Oozie. Metaúložiště se dá nakonfigurovat jenom během procesu vytváření clusteru. V tématu [použít metaúložiště Hive nebo Oozie](hdinsight-hadoop-provision-linux-clusters.md#use-hiveoozie-metastore).
    * **Skript akce**: Spusťte Bash skripty v clusteru. V tématu [HDInsight se systémem Linux přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md).
    * **Aplikace**: aplikace HDInsight přidat nebo odebrat.  V tématu [instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md).
    * **Monitorování**: monitorování clusteru, analýzy protokolů Azure.
    * **Vlastnosti**: zobrazení vlastností clusteru.
    * **Účty úložiště**: Zobrazit účty úložiště a klíče. Účty úložiště jsou nakonfigurované během procesu vytváření clusteru.
    * **Data Lake Store přístup**: Konfigurace přístupu ukládá Data Lake.  V tématu [rychlý start: nastavení clusterů v HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).
    * **Stav prostředku**: najdete v části [přehled stavu prostředků Azure](../service-health/resource-health-overview.md).
    * **Nová žádost o podporu**: vám umožní vytvořit lístek podpory s podporu společnosti Microsoft.
    
6. Klikněte na tlačítko **vlastnosti**:

    Mezi vlastnosti patří:

   * **Název hostitele**: název clusteru.
   * **Adresa URL clusteru**: adresa URL pro webové rozhraní Ambari.
   * **Secure shell (SSH)**: název uživatelské jméno a hostitele má použít při přístupu ke clusteru pomocí protokolu SSH.
   * **Stav**: jeden z: byl zrušen, přijata, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, provozní, spuštěna, chyba, odstraňování, odstranit, Timedout, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued nebo ClusterCustomization.
   * **Oblast**: umístění Azure. Seznam podporovaných umístění Azure, najdete v článku **oblast** rozevírací pole se seznamem na [HDInsight ceny](https://azure.microsoft.com/pricing/details/hdinsight/).
   * **Datum vytvoření**: datum, byl nasazen clusteru.
   * **Operační systém**: buď **Windows** nebo **Linux**.
   * **Typ**: Hadoop, HBase, Storm, Spark.
   * **Verze**. V tématu [HDInsight verze](hdinsight-component-versioning.md).
   * **Předplatné**: název odběru.
   * **Zdroj dat výchozí**: výchozí systém souborů clusteru.
   * **Velikost uzlů pracovního procesu**: uzlů pracovního procesu s vybranou velikostí virtuálního počítače.
   * **HEAD velikost uzlu**: head uzlů s vybranou velikostí virtuálního počítače.
   * **Virtuální síť**: název virtuální sítě, který je nasazen cluster, pokud v době nasazení byl vybrán jeden.

## <a name="delete-clusters"></a>Odstranění clusterů
Odstranění clusteru neodstraní výchozí účet úložiště ani všechny propojené účty úložiště. Clusteru můžete znovu vytvořit pomocí stejné účty úložiště a stejné metaúložiště. Doporučujeme používat nový výchozí kontejner objektu Blob, když znovu vytvoříte cluster.

1. Přihlaste se k [portál][azure-portal].
2. Klikněte na tlačítko **clustery HDInsight** v levé nabídce. Pokud nevidíte **clustery HDInsight**, klikněte na tlačítko **všechny služby** první.
3. Klikněte na cluster, který chcete odstranit.
4. Klikněte na tlačítko **odstranit** z hlavní nabídky a pak postupujte podle pokynů.

Viz také [pozastavení nebo vypnutí clustery](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Přidání dalších účtů úložiště

Po vytvoření clusteru můžete přidat další účty Azure Storage a účtů Azure Data Lake Store. Další informace najdete v tématu [Přidání dalších účtů úložiště do služby HDInsight](./hdinsight-hadoop-add-storage.md).

## <a name="scale-clusters"></a>Škálování clusterů
Funkce škálování clusteru umožňuje změnit počet uzlů pracovního procesu používané clusteru Azure HDInsight, aniž by museli znovu vytvořit cluster.

> [!NOTE]
> Pouze clustery s HDInsight verze 3.1.3 nebo vyšší nejsou podporovány. Pokud si nejste jistí na verzi vašeho clusteru, můžete zkontrolovat stránku vlastností.  V tématu [seznamu a zobrazit clustery](#list-and-show-clusters).
>
>

Dopad změny v počtu uzlů dat se liší pro každý typ clusteru podporuje HDInsight:

* Hadoop

    Můžete bez problémů zvýšit počet uzlů pracovního procesu v clusteru Hadoop, který běží bez dopadu na všechny úlohy čekající na vyřízení nebo spuštěné. Nové úlohy můžete také odeslány, když probíhá operace. Selhání v rámci operace škálování pohodlné zpracování tak, aby cluster zůstane vždy ve funkčním stavu.

    Pokud se Hadoop cluster měřítko snížením počtu uzlů data, některé služby v clusteru restartovat. To způsobí, že všechny spuštěné a čeká se na úlohy selhání po dokončení operace škálování. Můžete, ale odešlete znovu úloh po dokončení operace.
* HBase

    Bezproblémově můžete přidávat nebo odebírat uzly do clusteru HBase, když je spuštěná. Místní servery jsou automaticky vyváženy během několika minut po dokončení operace škálování. Místní servery však můžete také ručně vyvážit přihlašují k headnode clusteru a spuštěním následujících příkazů z okna příkazového řádku:

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

    Další informace o používání prostředí HBase naleznete v části [začít pracovat s příklad Apache HBase v HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Storm

    Můžete bezproblémově přidávat nebo odebírat uzly dat do clusteru Storm, když je spuštěná. Po úspěšném dokončení operace škálování, musíte ale znovu vyvážit topologii.

    Vyrovnává lze dosáhnout dvěma způsoby:

  * Storm webového uživatelského rozhraní
  * Nástroj pro rozhraní příkazového řádku (CLI)

    Odkazovat [dokumentaci Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) další podrobnosti.

    Uživatelské rozhraní Storm webu je k dispozici v clusteru HDInsight:

    ![Obnovte rovnováhu škálování Storm v HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Tady je příklad znovu vyvážit topologie Storm rozhraní příkazového řádku příkaz:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

**Škálování clusterů**

1. Přihlaste se k [portál][azure-portal].
2. Klikněte na tlačítko **clustery HDInsight** v levé nabídce.
3. Klikněte na cluster, kterou chcete škálovat.
3. Klikněte na tlačítko **škálování clusteru**.
4. Zadejte **číslo pracovní uzly**. Limit počtu uzlů clusteru se liší mezi předplatných Azure. Obraťte se na podporu fakturace o navýšení limitu.  Informace o nákladech odráží změny, které jste udělali počet uzlů.

    ![HDInsight hadoop hbase storm spark škálování](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster.png)

## <a name="pauseshut-down-clusters"></a>Pozastavení nebo vypnutí clustery

Většina úloh Hadoop jsou dávkové úlohy, které jsou spustit pouze občas. Pro většinu clusterů systému Hadoop jsou velké období clusteru není použitá ke zpracování. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán.
Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány.

Existuje mnoho způsobů, které můžete naprogramovat proces:

* Uživatel pro vytváření dat Azure. V tématu [vytvořit na vyžádání systémem Linux Hadoop clusterů v HDInsight pomocí Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) pro vytváření HDInsight na vyžádání propojené služby.
* Použití Azure PowerShell.  V tématu [analyzovat data zpoždění letu](hdinsight-analyze-flight-delay-data.md).
* Použití Azure CLI. V tématu [Správa clusterů HDInsight pomocí rozhraní příkazového řádku Azure](hdinsight-administer-use-command-line.md).
* Použití sady .NET SDK HDInsight. V tématu [úloh Hadoop odeslání](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Informace o cenách najdete v části [HDInsight ceny](https://azure.microsoft.com/pricing/details/hdinsight/). Pokud chcete odstranit cluster z portálu, přečtěte si téma [odstranění clusterů](#delete-clusters)

## <a name="move-cluster"></a>Přesunutí clusteru

Cluster služby HDInsight můžete přesunout do jiné skupiny prostředků Azure nebo jiné předplatné.  V tématu [seznamu a zobrazit clustery](#list-and-show-clusters).

## <a name="upgrade-clusters"></a>Upgrade clustery

V tématu [clusteru HDInsight se Upgrade na novější verzi](./hdinsight-upgrade-cluster.md).

## <a name="open-the-ambari-web-ui"></a>Otevřete webovému uživatelskému rozhraní Ambari

Ambari poskytuje intuitivní, snadno použitelné Hadoop správu webového uživatelského rozhraní zajištěna jeho rozhraní RESTful API. Ambari umožňuje správci systému pro správu a sledování clusterů systému Hadoop.

1. Otevřete cluster služby HDInsight z portálu Azure.  V tématu [seznamu a zobrazit clustery](#list-and-show-clusters).
2. Klikněte na tlačítko **clusteru řídicí panel**.

    ![Nabídky clusteru HDInsight Hadoop](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu.png)

1. Zadejte uživatelské jméno clusteru a heslo.  Výchozí uživatelské jméno clusteru _správce_. Ambari webového uživatelského rozhraní vypadá takto:

    ![HDInsight Hadoop Ambari webového uživatelského rozhraní](./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-ambari-web-ui.png)

Další informace najdete v tématu [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Změna hesla
Cluster služby HDInsight může mít dva uživatelské účty. HDInsight clusteru (také známa jako uživatelský účet. Uživatelský účet HTTP) a vytvoří se během procesu vytváření uživatelského účtu SSH. Chcete-li změnit clusteru uživatel účet uživatelské jméno a heslo a akce skriptu ke změně účtu uživatele SSH můžete použít webovému uživatelskému rozhraní Ambari

### <a name="change-the-cluster-user-password"></a>Změnit heslo uživatele clusteru
Webové uživatelské rozhraní Ambari slouží ke změně hesla uživatele clusteru. K přihlášení do Ambari, musíte použít existující cluster uživatelské jméno a heslo.

> [!NOTE]
> Změna hesla uživatele (správce) clusteru může způsobit spouštění tohoto clusteru k selhání akce skriptu. Pokud máte všechny akce trvalého skriptu této cílové uzly pracovního procesu, tyto skripty mohou selhat, když přidáte, že uzly clusteru prostřednictvím změnit velikost operace. Další informace o akcí skriptů naleznete v tématu [HDInsight přizpůsobit clustery pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md).
>
>

1. Přihlaste se k webové uživatelské rozhraní Ambari pomocí pověření uživatele clusteru HDInsight. Výchozí uživatelské jméno **admin**. Adresa URL je **https://&lt;název clusteru HDInsight > azurehdinsight.net**.
2. Klikněte na tlačítko **správce** z hlavní nabídky a pak klikněte na tlačítko "Správa Ambari".
3. V levé nabídce klikněte na tlačítko **uživatelé**.
4. Klikněte na tlačítko **správce**.
5. Klikněte na tlačítko **změnit heslo**.

Ambari poté změní heslo na všech uzlech v clusteru.

### <a name="change-the-ssh-user-password"></a>Změnit heslo uživatele SSH
1. Pomocí textového editoru, uložte následující text jako soubor s názvem **changepassword.sh**.

    > [!IMPORTANT]
    > Musíte použít editor, který používá LF jako ukončování řádků. Pokud editor používá Line FEED, skript se nefunguje.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Nahrajte soubor do umístění úložiště, který je přístupný z prostředí HDInsight pomocí adresy protokolu HTTP nebo HTTPS. Například soubor veřejného uložit jako je například OneDrive nebo Azure Blob storage. Uložte identifikátor URI (adresa protokolu HTTP nebo HTTPS) do souboru, jak tento identifikátor URI je potřeba v dalším kroku.
3. Z portálu Azure klikněte na tlačítko **clustery HDInsight**.
4. Klikněte na clusteru HDInsight.
4. Klikněte na tlačítko **skript akce**.
4. Z **akcí skriptů** vyberte **odeslání nové**. Když **odeslat akci se skripty** okno se zobrazí, zadejte následující informace:

   | Pole | Hodnota |
   | --- | --- |
   | Název |Ssh heslo změnit |
   | URI skriptu Bash |Identifikátor URI k souboru changepassword.sh |
   | Uzly (Head, pracovního procesu, Nimbus, nadřízeného, Zookeeper atd.) |✓ pro všechny typy uzlů, které jsou uvedené |
   | Parametry |Zadejte uživatelské jméno SSH a pak nové heslo. Měla by existovat jediného místa mezi uživatelské jméno a heslo. |
   | Zachovat tuto akci skriptu... |Nechte pole nezaškrtnuté. |
5. Vyberte **vytvořit** použít skript. Po dokončení skriptu, budete moci připojit ke clusteru pomocí SSH pomocí nového hesla.

## <a name="grantrevoke-access"></a>Udělení nebo odvolání přístupu
Clustery HDInsight mají následující webové služby HTTP (všechny tyto služby mají RESTful koncových bodů):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Ve výchozím nastavení jsou tyto služby oprávnění pro přístup. Vám může odvolání nebo udělit přístup pomocí [rozhraní příkazového řádku Azure](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) a [prostředí Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access).

## <a name="find-the-subscription-id"></a>Najít ID předplatného

**K vyhledání vašeho předplatného Azure ID**

1. Přihlaste se k [portál][azure-portal].
2. Klikněte na tlačítko **odběry**. Každé předplatné má název a identifikátor.

Každý cluster je vázaný na předplatné Azure. Předplatné v clusteru se zobrazí ID **základní** dlaždici. V tématu [seznamu a zobrazit clustery](#list-and-show-clusters).

## <a name="find-the-resource-group"></a>Najít skupinu prostředků
V režimu Azure Resource Manager se vytvoří každý cluster HDInsight pomocí Azure Resource Manager skupiny. Správce prostředků skupiny, který je součástí clusteru s podporou se zobrazí v:

* Seznam clusteru má **skupiny prostředků** sloupce.
* Cluster **základní** dlaždici.  

V tématu [seznamu a zobrazit clustery](#list-and-show-clusters).

## <a name="find-the-storage-accounts"></a>Najít účty úložiště

Clustery prostředí HDInsight pomocí účtu Azure Storage nebo Azure Data Lake Store ukládat data. Každý cluster HDInsight může mít jeden výchozí účet úložiště a počet propojené účty úložiště. K zobrazení seznamu účtů úložiště, nejprve otevřete cluster z portálu a pak klikněte na tlačítko **účty úložiště**:

![Účty úložiště clusteru HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-storage-accounts.png)

Na předchozím snímku obrazovky je __výchozí__ sloupec určující, zda účet je výchozí účet úložiště.

Chcete-li zobrazit seznam účtů Data Lake Store, klikněte na tlačítko **Data Lake Store přístup** v předchozím snímku obrazovky.

## <a name="run-hive-queries"></a>Spuštění dotazů Hive
Nelze spustit úlohy Hive přímo z portálu Azure, ale můžete použít zobrazení na webové uživatelské rozhraní Ambari Hive.

**Ke spouštění dotazů Hive pomocí zobrazení Ambari Hive**

1. Přihlaste se k webové uživatelské rozhraní Ambari pomocí pověření uživatele clusteru HDInsight. Výchozí uživatelské jméno **admin**. Adresa URL je **https://&lt;název clusteru HDInsight > azurehdinsight.net**.
2. Otevřete zobrazení Hive, jak je znázorněno na následujícím snímku obrazovky:  

    ![Zobrazení hive HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-hive-view.png)

3. Klikněte na tlačítko **dotazu** v hlavní nabídce.
4. Zadejte dotaz Hive v **Editor dotazů**a potom klikněte na **Execute**.

## <a name="monitor-jobs"></a>Monitorování úloh
V tématu [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Ambari](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="browse-files"></a>Procházet soubory
Pomocí portálu Azure, můžete procházet obsah výchozí kontejner.

1. Přihlaste se k [ https://portal.azure.com ](https://portal.azure.com).
2. Klikněte na tlačítko **clustery HDInsight** v levé nabídce seznam stávajících clusterů.
3. Klikněte na název clusteru. Pokud je seznam clusteru dlouho, můžete použít možnosti filtrovat horní části stránky.
4. Klikněte na tlačítko **účty úložiště** v levé nabídce clusteru.
5. Klikněte na účet úložiště.
7. Klikněte **objekty BLOB** dlaždici.
8. Klikněte na název výchozí kontejner.

## <a name="monitor-cluster-usage"></a>Monitorování využití clusteru
**Využití** části okna clusteru HDInsight se zobrazují informace o počet jader, které jsou k dispozici pro vaše předplatné pro použití s HDInsight, jakož i počet jader přidělené k tomuto clusteru a jak jsou přiděleny pro uzly v tomto clusteru. V tématu [seznamu a zobrazit clustery](#list-and-show-clusters).

> [!IMPORTANT]
> Pokud chcete monitorovat služby poskytované clusteru HDInsight, musíte použít Ambari Web nebo Ambari REST API. Další informace o používání Ambari najdete v tématu [Správa clusterů HDInsight pomocí Ambari](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Připojení ke clusteru

* [Použití Hivu se službou HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Použití SSH s HDInsightem](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Další postup

V tomto článku jste se naučili některé základní funkce správy. Další informace naleznete v následujících článcích:

* [Spravovat HDInsight pomocí prostředí Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Spravovat HDInsight pomocí rozhraní příkazového řádku Azure](hdinsight-administer-use-command-line.md)
* [Vytvoření clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Další informace o používání webové uživatelské rozhraní Ambari](hdinsight-hadoop-manage-ambari.md)
* [Podrobnosti o použití Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Používání Hive v HDInsight](hadoop/hdinsight-use-hive.md)
* [Použijte Pig v HDInsight](hadoop/hdinsight-use-pig.md)
* [Použití nástroje Sqoop v HDInsight](hadoop/hdinsight-use-sqoop.md)
* [Začínáme s Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Jaká verze Hadoop je v Azure HDInsight?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Hadoop příkazového řádku"
