---
title: Správa clusterů Hadoop založených na systému Windows v prostředí HDInsight pomocí portálu Azure | Microsoft Docs
description: Zjistěte, jak spravovat služby HDInsight. Vytvoření clusteru HDInsight, otevřete konzolu pro interaktivní JavaScript a otevřete konzolu pro příkaz Hadoop.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 9295a988-bd88-453a-8c8b-55fa103bf39c
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 0b34f861b306dc62cbdbf97378a1c370494bbb20
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="manage-windows-based-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Správa clusterů Hadoop založených na systému Windows v prostředí HDInsight pomocí portálu Azure

Pomocí [portál Azure][azure-portal], můžete vytvořit clustery se systémem Windows Hadoop v Azure HDInsight, změnit heslo uživatele Hadoop a povolit protokol RDP (Remote Desktop), aby příkaz Hadoop je k dispozici konzoly v clusteru.

Informace v tomto článku se vztahují pouze na clustery HDInsight se systémem Windows. Informace o správě clusterech se systémem Linux naleznete v tématu [Správa clusterů systému Hadoop v HDInsight pomocí portálu Azure](hdinsight-administer-use-portal-linux.md).

> [!IMPORTANT]
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).


## <a name="prerequisites"></a>Požadavky

Je nutné, abyste před zahájením tohoto článku měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Účet služby Azure Storage** -clusteru HDInsight používá kontejner úložiště objektů Blob v Azure jako výchozí systém souborů. Další informace o tom, jak Azure Blob storage nabízí jednotné prostředí s clustery HDInsight najdete v tématu [použití Azure Blob Storage s HDInsight](hdinsight-hadoop-use-blob-storage.md). Podrobnosti o vytvoření účtu Azure Storage najdete v tématu [postup vytvoření účtu úložiště](../storage/common/storage-create-storage-account.md).

## <a name="open-the-portal"></a>Otevřete portál
1. Přihlaste se k [ https://portal.azure.com ](https://portal.azure.com).
2. Po otevření portálu můžete:

   * Klikněte na tlačítko **vytvořit prostředek** v levé nabídce na vytvoření nového clusteru:

       ![tlačítko Nový cluster HDInsight](./media/hdinsight-administer-use-management-portal/azure-portal-new-button.png)
   * Klikněte na tlačítko **clustery HDInsight** v levé nabídce.

       ![Azure portálu tlačítko clusteru HDInsight](./media/hdinsight-administer-use-management-portal/azure-portal-hdinsight-button.png)

     Pokud **HDInsight** nebude zobrazovat v nabídce vlevo, klikněte na tlačítko **Procházet**.

     ![Tlačítko Procházet clusteru Azure portálu](./media/hdinsight-administer-use-management-portal/azure-portal-browse-button.png)

## <a name="create-clusters"></a>Vytváření clusterů
Postup vytvoření, pomocí portálu naleznete v tématu [Tvorba clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

HDInsight funguje s komponentami široký rozsah Hadoop. Seznam součástí, které byly ověřeny a podporovány, naleznete v části [jaká verze Hadoop je v Azure HDInsight](hdinsight-component-versioning.md). HDInsight můžete přizpůsobit pomocí jedné z následujících možností:

* Chcete-li spustit vlastní skripty, které můžete přizpůsobit cluster změnit konfiguraci clusteru nebo nainstalovat vlastní komponenty, například Giraph nebo Solr použití akce skriptu. Další informace najdete v tématu [clusteru HDInsight přizpůsobit pomocí akce skriptu](hdinsight-hadoop-customize-cluster.md).
* Při vytváření clusteru použijte vlastní nastavení parametrů clusteru v rozhraní .NET SDK HDInsight nebo Azure PowerShell. Tyto změny konfigurace se pak zachovají prostřednictvím dobu životnosti clusteru a nemá vliv reimages uzlu clusteru, které platformy Azure pravidelně provádí za účelem údržby. Další informace o používání vlastního nastavení parametrů clusteru najdete v tématu [Tvorba clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Některé nativní součásti Java, jako je Mahout a možností konfigurace, můžete spustit v clusteru jako souborů JAR. Tyto soubory JAR můžete distribuovat do úložiště objektů Blob v Azure a odeslat ke clusterům HDInsight prostřednictvím mechanismy odesílání úloh Hadoop. Další informace najdete v tématu [Hadoop odeslání úlohy prostřednictvím kódu programu](hadoop/submit-apache-hadoop-jobs-programmatically.md).

  > [!NOTE]
  > Pokud máte problémy s nasazením souborů JAR ke clusterům HDInsight nebo volání souborů JAR na clusterech HDInsight, obraťte se na [Microsoft Support](https://azure.microsoft.com/support/options/).
  >
  > Kaskádových není podporována v prostředí HDInsight a nejsou vhodné pro systém Microsoft Support. Seznam podporovaných součásti, najdete v části [co je nového ve verzích clusterů poskytovaných v HDInsight](hdinsight-component-versioning.md).
  >
  >

Instalace vlastních softwaru v clusteru pomocí připojení ke vzdálené ploše není podporována. Neměli byste ukládat soubory na jednotkách hlavního uzlu, jak budou ztraceny, budete muset znovu vytvořit clustery. Doporučujeme ukládat soubory do úložiště objektů Blob v Azure. Úložiště objektů blob je trvalé.

## <a name="list-and-show-clusters"></a>Seznam a zobrazit clustery
1. Přihlaste se k [ https://portal.azure.com ](https://portal.azure.com).
2. Klikněte na tlačítko **clustery HDInsight** v levé nabídce.
3. Klikněte na název clusteru. Pokud je seznam clusteru dlouho, můžete použít možnosti filtrovat horní části stránky.
4. Dvakrát klikněte na cluster ze seznamu a zobrazit podrobnosti.

    **Nabídky a essentials**:

    ![Azure portálu essentials clusteru HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-essentials.png)

   * Upravit nabídku, klikněte pravým tlačítkem na libovolné místo v nabídce a pak klikněte na tlačítko **přizpůsobit**.
   * **Nastavení** a **všechna nastavení**: zobrazí **nastavení** okno pro cluster, který umožňuje zobrazit podrobné informace o konfiguraci pro cluster.
   * **Řídicí panel**, **řídicí panel clusteru** a **adresy URL: Toto jsou všechny způsoby, jak přístup k řídicímu panelu clusteru, který je Ambari Web pro clustery se systémem Linux. -**Zabezpečené prostředí **: Zobrazí pokyny pro připojení ke clusteru pomocí připojení Secure Shell (SSH).
   * **Škálování clusteru**: umožňuje změnit počet uzlů pracovního procesu pro tento cluster.
   * **Odstranit**: Odstraní clusteru.
   * **Rychlý Start**: Zobrazí informace, které vám pomohou začněte používat HDInsight.
   * ** Uživatele: Umožňuje nastavit oprávnění pro *portálu správy* tohoto clusteru pro ostatní uživatele ve vašem předplatném Azure.

     > [!IMPORTANT]
     > To *pouze* ovlivňuje oprávnění a přístupová práva k tomuto clusteru na portálu Azure a nemá žádný vliv na který můžete připojit k nebo odesílání úloh do clusteru HDInsight.
     >
     >
   * **Značky**: značky umožňují nastavit páry klíč/hodnota k definování vlastní taxonomii cloudových služeb. Můžete například vytvořit klíč s názvem **projektu**a potom používat běžné hodnotu pro všechny služby související s konkrétní projekt.
   * **Zobrazení Ambari**: odkazy na Ambari Web.

     > [!IMPORTANT]
     > Ke správě služeb poskytovaných clusteru HDInsight, je nutné použít Ambari Web nebo Ambari REST API. Další informace o používání Ambari najdete v tématu [Správa clusterů HDInsight pomocí Ambari](hdinsight-hadoop-manage-ambari.md).
     >
     >

     **Využití**:

     ![Azure portálu použití clusteru HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-portal-cluster-usage.png)
5. Klikněte na tlačítko **nastavení**.

    ![Azure portálu použití clusteru HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.settings.png)

   * **Vlastnosti**: zobrazení vlastností clusteru.
   * **Identita AAD clusteru**:
   * **Klíče k úložišti Azure**: Zobrazit výchozí účet úložiště a jeho klíčem. Účet úložiště je konfigurace během procesu vytváření clusteru.
   * **Cluster přihlášení**: Změňte clusteru HTTP uživatelské jméno a heslo.
   * **Externí Metaúložiště**: Zobrazit metaúložiště Hive a Oozie. Metaúložiště se dá nakonfigurovat jenom během procesu vytváření clusteru.
   * **Škálování clusteru**: Zvyšte a snižte počet uzlů pracovního procesu clusteru.
   * **Vzdálená plocha**: Povolit a zakázat přístup ke vzdálené ploše (RDP) a nakonfigurovat uživatelské jméno protokolu RDP.  Uživatelské jméno protokolu RDP musí být odlišný od uživatelské jméno protokolu HTTP.
   * **Preferovaného partnera**:

     > [!NOTE]
     > Toto je obecná seznam dostupných nastavení; Ne všechny z nich bude k dispozici pro všechny typy clusteru.
     >
     >
6. Klikněte na tlačítko **vlastnosti**:

    V části vlastnosti jsou uvedeny následující:

   * **Název hostitele**: název clusteru.
   * **Adresa URL clusteru**.
   * **Stav**: zahrnují byl zrušen, přijata, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, provozní, spuštěna, chyba, odstraňování, odstranit, Timedout, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, ClusterCustomization CertRolloverQueued, ResizeQueued,
   * **Oblast**: umístění Azure. Seznam podporovaných umístění Azure, najdete v článku **oblast** rozevírací pole se seznamem na [HDInsight ceny](https://azure.microsoft.com/pricing/details/hdinsight/).
   * **Data vytvořená**.
   * **Operační systém**: buď **Windows** nebo **Linux**.
   * **Typ**: Hadoop, HBase, Storm, Spark.
   * **Verze**. V tématu [HDInsight verze](hdinsight-component-versioning.md)
   * **Předplatné**: název odběru.
   * **ID předplatného**.
   * **Primární zdroj dat**. Účet úložiště objektů Blob v Azure použito jako výchozí systém souborů Hadoop.
   * **Pracovní uzly cenová úroveň**.
   * **Hlavní uzel cenová úroveň**.

## <a name="delete-clusters"></a>Odstranění clusterů
Odstranění clusteru se neodstraní výchozí účet úložiště nebo všechny propojené účty úložiště. Clusteru můžete znovu vytvořit pomocí stejné účty úložiště a stejné metaúložiště.

1. Přihlaste se k [portál][azure-portal].
2. Klikněte na tlačítko **Procházet vše** v levé nabídce klikněte na tlačítko **clustery HDInsight**, klikněte na název clusteru.
3. Klikněte na tlačítko **odstranit** z hlavní nabídky a pak postupujte podle pokynů.

Viz také [pozastavení nebo vypnutí clustery](#pauseshut-down-clusters).

## <a name="scale-clusters"></a>Škálování clusterů
Funkce škálování clusteru umožňuje změnit počet uzlů pracovního procesu používá cluster, který běží v Azure HDInsight bez nutnosti znovu vytvořit cluster.

> [!NOTE]
> Pouze clustery s HDInsight verze 3.1.3 nebo vyšší nejsou podporovány. Pokud si nejste jistí na verzi vašeho clusteru, můžete zkontrolovat stránku vlastností.  V tématu [seznamu a zobrazit clustery](#list-and-show-clusters).
>
>

Dopad změny v počtu uzlů dat pro každý typ clusteru podporuje HDInsight:

* Hadoop

    Můžete bez problémů zvýšit počet uzlů pracovního procesu v clusteru Hadoop, který běží bez dopadu na všechny úlohy čekající na vyřízení nebo spuštěné. Nové úlohy můžete také odeslány, když probíhá operace. Selhání v rámci operace škálování pohodlné zpracování tak, aby cluster zůstane vždy ve funkčním stavu.

    Pokud se Hadoop cluster měřítko snížením počtu uzlů data, některé služby v clusteru restartovat. To způsobí, že všechny spuštěné a čeká se na úlohy selhání po dokončení operace škálování. Můžete, ale odešlete znovu úloh po dokončení operace.
* HBase

    Bezproblémově můžete přidávat nebo odebírat uzly do clusteru HBase, když je spuštěná. Místní servery jsou automaticky vyváženy během několika minut po dokončení operace škálování. Protokolování do headnode clusteru a spuštěním následujících příkazů z okna příkazového řádku však můžete také ručně vyvážit místní servery:

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

    Další informace o používání prostředí HBase naleznete v tématu]
* Storm

    Můžete bezproblémově přidávat nebo odebírat uzly dat do clusteru Storm, když je spuštěná. Ale po úspěšném dokončení operace škálování, budete muset znovu vyvážit topologii.

    Vyrovnává lze dosáhnout dvěma způsoby:

  * Storm webového uživatelského rozhraní
  * Nástroj pro rozhraní příkazového řádku (CLI)

    Podrobnosti najdete [dokumentaci Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) další podrobnosti.

    Uživatelské rozhraní Storm webu je k dispozici v clusteru HDInsight:

    ![Obnovte rovnováhu škálování HDInsight storm](./media/hdinsight-administer-use-management-portal/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Tady je příklad jak znovu vyvážit topologie Storm pomocí rozhraní příkazového řádku příkaz:

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors
        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

**Škálování clusterů**

1. Přihlaste se k [portál][azure-portal].
2. Klikněte na tlačítko **Procházet vše** v levé nabídce klikněte na tlačítko **clustery HDInsight**, klikněte na název clusteru.
3. Klikněte na tlačítko **nastavení** z horní nabídce a pak klikněte na tlačítko **škálování clusteru**.
4. Zadejte **číslo pracovní uzly**. Limit počtu uzlů clusteru se liší podle předplatných Azure. Obraťte se na podporu fakturace o navýšení limitu.  Informace o nákladech se projeví změny, které jste udělali počet uzlů.

    ![HDInsight Hadoop HBase, Storm Spark škálování](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)

## <a name="pauseshut-down-clusters"></a>Pozastavení nebo vypnutí clustery
Většina úloh Hadoop jsou dávkové úlohy, které jsou jen občas spustili. Pro většinu clusterů systému Hadoop jsou velké období clusteru není použitá ke zpracování. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán.
Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány.

Existuje mnoho způsobů, které můžete naprogramovat proces:

* Uživatel pro vytváření dat Azure. V tématu [propojená služba Azure HDInsight](../data-factory/compute-linked-services.md) a [transformovat a analyzovat pomocí Azure Data Factory](../data-factory/transform-data.md) pro HDInsight na vyžádání a samoobslužné definované propojené služby.
* Použití Azure PowerShell.  V tématu [analyzovat data zpoždění letu](hdinsight-analyze-flight-delay-data.md).
* Použití Azure CLI. V tématu [Správa clusterů HDInsight pomocí rozhraní příkazového řádku Azure](hdinsight-administer-use-command-line.md).
* Použití sady .NET SDK HDInsight. V tématu [úloh Hadoop odeslání](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Informace o cenách najdete v části [HDInsight ceny](https://azure.microsoft.com/pricing/details/hdinsight/). Pokud chcete odstranit cluster z portálu, přečtěte si téma [odstranění clusterů](#delete-clusters)

## <a name="change-cluster-username"></a>Uživatelské jméno změnit clusteru
Cluster služby HDInsight může mít dva uživatelské účty. Uživatelský účet clusteru HDInsight se vytvoří během procesu vytváření. Můžete také vytvořit uživatelský účet protokolu RDP pro přístup ke clusteru pomocí protokolu RDP. V tématu [povolení vzdálené plochy](#connect-to-hdinsight-clusters-by-using-rdp).

**Chcete-li změnit HDInsight clusteru uživatelské jméno a heslo**

1. Přihlaste se k [portál][azure-portal].
2. Klikněte na tlačítko **Procházet vše** v levé nabídce klikněte na tlačítko **clustery HDInsight**, klikněte na název clusteru.
3. Klikněte na tlačítko **nastavení** z horní nabídce a pak klikněte na tlačítko **clusteru přihlášení**.
4. Pokud **clusteru přihlášení** byla povolena, musíte kliknout na **zakázat**a potom klikněte na **povolit** před změnou uživatelského jména a hesla...
5. Změna **clusteru přihlašovací jméno** nebo **clusteru přihlašovacího hesla**a potom klikněte na **Uložit**.

    ![HDInsight změnit clusteru uživatel uživatelské jméno heslo http uživatele](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

## <a name="grantrevoke-access"></a>Udělení nebo odvolání přístupu
Clustery HDInsight mají následující webové služby HTTP (všechny tyto služby mají RESTful koncových bodů):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Ve výchozím nastavení jsou tyto služby oprávnění pro přístup. Vám může odvolání nebo udělit přístup z portálu Azure.

> [!NOTE]
> Pomocí udělení nebo odvolání přístupu, obnoví clusteru uživatelské jméno a heslo.
>
>

**K udělení nebo odvolání přístup protokolu HTTP webové služby**

1. Přihlaste se k [portál][azure-portal].
2. Klikněte na tlačítko **Procházet vše** v levé nabídce klikněte na tlačítko **clustery HDInsight**, klikněte na název clusteru.
3. Klikněte na tlačítko **nastavení** z horní nabídce a pak klikněte na tlačítko **clusteru přihlášení**.
4. Pokud **clusteru přihlášení** byla povolena, musíte kliknout na **zakázat**a potom klikněte na **povolit** před změnou uživatelského jména a hesla...
5. Pro **uživatelské jméno přihlášení clusteru** a **clusteru přihlašovacího hesla**, zadejte nové uživatelské jméno a heslo (v uvedeném pořadí) pro cluster.
6. Klikněte na **ULOŽIT**.

    ![HDInsight celkové odebrat přístup protokolu http webové služby](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

## <a name="find-the-default-storage-account"></a>Najít výchozí účet úložiště
Každý cluster HDInsight má výchozí účet úložiště. Výchozí účet úložiště a jeho klíče pro cluster se zobrazí v části **nastavení**/**vlastnosti**/**klíčů k úložišti Azure**. V tématu [seznamu a zobrazit clustery](#list-and-show-clusters).

## <a name="find-the-resource-group"></a>Najít skupinu prostředků
V režimu Azure Resource Manager se vytvoří každý cluster HDInsight s skupinu prostředků Azure. Skupina prostředků Azure, který je součástí clusteru s podporou se zobrazí v:

* Seznam clusteru má **skupiny prostředků** sloupce.
* Cluster **základní** dlaždici.  

V tématu [seznamu a zobrazit clustery](#list-and-show-clusters).

## <a name="open-hdinsight-query-console"></a>Otevřete konzolu HDInsight dotazu
Konzole dotazu HDInsight zahrnuje následující funkce:

* **Hive Editor**: grafickým uživatelským rozhraním A webové rozhraní pro odesílání úloh Hive.  V tématu [spouštění dotazů Hive pomocí konzole dotazu](hadoop/apache-hadoop-use-hive-query-console.md).

    ![Editor portálu hive HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-hive-editor.png)
* **Historie úlohy**: úloh Hadoop monitorování.  

    ![Historie úlohy portálu HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-job-history.png)

    Klikněte na tlačítko **název dotazu** a zobrazit podrobnosti, včetně vlastnosti úlohy **dotaz úlohy**, a ** výstup úlohy. Můžete také stáhnout dotaz a výstup do pracovní stanice.
* **Soubor prohlížeče**: Procházet výchozí účet úložiště a propojené účty úložiště.

    ![Procházet prohlížeče portálu souboru HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-file-browser.png)

    Na tomto snímku **<Account>** typ označuje položku je účet úložiště Azure.  Klikněte na název účtu a vyhledejte soubory.
* **Uživatelské rozhraní Hadoop**.

    ![Portál HDInsight Hadoop uživatelského rozhraní](./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-ui.png)

    Z **uživatelského rozhraní Hadoop*, můžete procházet soubory a zkontrolujte protokoly.
* **Yarn uživatelského rozhraní**.

    ![Portál HDInsight YARN uživatelského rozhraní](./media/hdinsight-administer-use-management-portal/hdinsight-yarn-ui.png)

## <a name="run-hive-queries"></a>Spuštění dotazů Hive
Chcete-li spustit úlohy Hive z portálu, klikněte na tlačítko **Hive Editor** v konzole nástroje HDInsight dotazu. V tématu [otevřeného dotazu HDInsight konzoly](#open-hdinsight-query-console).

## <a name="monitor-jobs"></a>Monitorování úloh
Monitorování úloh z portálu, klikněte na tlačítko **historie úlohy** v konzole nástroje HDInsight dotazu. V tématu [otevřeného dotazu HDInsight konzoly](#open-hdinsight-query-console).

## <a name="browse-files"></a>Procházet soubory
Chcete-li procházet soubory uložené na výchozí účet úložiště a účty propojené úložiště, klikněte na tlačítko **prohlížeč souborů** v konzole nástroje HDInsight dotazu. V tématu [otevřeného dotazu HDInsight konzoly](#open-hdinsight-query-console).

Můžete také **procházet systému souborů** nástroj z **uživatelského rozhraní Hadoop** v konzole nástroje HDInsight.  V tématu [otevřeného dotazu HDInsight konzoly](#open-hdinsight-query-console).

## <a name="monitor-cluster-usage"></a>Monitorování využití clusteru
**Využití** části okna clusteru HDInsight se zobrazují informace o počet jader, které jsou k dispozici pro vaše předplatné pro použití s HDInsight, jakož i počet jader přidělené k tomuto clusteru a jak jsou přiděleny pro uzly v tomto clusteru. V tématu [seznamu a zobrazit clustery](#list-and-show-clusters).

> [!IMPORTANT]
> Pokud chcete monitorovat služby poskytované clusteru HDInsight, musíte použít Ambari Web nebo Ambari REST API. Další informace o používání Ambari najdete v tématu [Správa clusterů HDInsight pomocí Ambari](hdinsight-hadoop-manage-ambari.md)
>
>

## <a name="open-hadoop-ui"></a>Otevřete uživatelské rozhraní pro Hadoop
Monitorovat clusteru, procházet systému souborů a zkontrolujte protokoly, klikněte na tlačítko **uživatelského rozhraní Hadoop** v konzole nástroje HDInsight dotazu. V tématu [otevřeného dotazu HDInsight konzoly](#open-hdinsight-query-console).

## <a name="open-yarn-ui"></a>Otevřete Yarn uživatelského rozhraní
Chcete-li použít uživatelské rozhraní Yarn, klikněte na tlačítko **uživatelském rozhraní Yarn** v konzole nástroje HDInsight dotazu. V tématu [otevřeného dotazu HDInsight konzoly](#open-hdinsight-query-console).

## <a name="connect-to-clusters-using-rdp"></a>Připojení ke clusterům pomocí protokolu RDP
Přihlašovací údaje pro cluster, který jste zadali při jeho vytváření poskytnout přístup ke službám v clusteru, ale nechcete samotného clusteru pomocí vzdálené plochy. Přístup ke vzdálené ploše můžete zapnout při zřizování clusteru nebo po zřízení clusteru. Pokyny k povolení funkce Vzdálená plocha při vytváření naleznete v tématu [clusteru HDInsight se vytvořit](hdinsight-hadoop-provision-linux-clusters.md).

**Chcete-li povolit vzdálená plocha**

1. Přihlaste se k [portál][azure-portal].
2. Klikněte na tlačítko **Procházet vše** v levé nabídce klikněte na tlačítko **clustery HDInsight**, klikněte na název clusteru.
3. Klikněte na tlačítko **nastavení** z horní nabídce a pak klikněte na tlačítko **vzdálené plochy**.
4. Zadejte **vyprší dne**, **uživatelské jméno vzdálené plochy** a **heslo vzdálené plochy**a potom klikněte na **povolit**.

    ![HDInsight povolit zakázat konfigurace vzdálené plochy](./media/hdinsight-administer-use-management-portal/hdinsight.portal.remote.desktop.png)

    Výchozí hodnoty pro vyprší dne je za týden.

   > [!NOTE]
   > .NET SDK služby HDInsight můžete použít také k povolení vzdálené plochy na clusteru. Použití **EnableRdp** metodu klientského objektu HDInsight následujícím způsobem: **klienta. EnableRdp (název clusteru, místo, "rdpuser", "rdppassword", DateTime.Now.AddDays(6))**. Podobně zakázání vzdálené plochy na clusteru, můžete použít **klienta. DisableRdp (název clusteru, umístění)**. Další informace o těchto metodách v tématu [HDInsight .NET SDK – referenční informace](http://go.microsoft.com/fwlink/?LinkId=529017). Tuto možnost lze použít pouze pro clustery služby HDInsight v systému Windows.
   >
   >

**K připojení ke clusteru pomocí protokolu RDP**

1. Přihlaste se k [portál][azure-portal].
2. Klikněte na tlačítko **Procházet vše** v levé nabídce klikněte na tlačítko **clustery HDInsight**, klikněte na název clusteru.
3. Klikněte na tlačítko **nastavení** z horní nabídce a pak klikněte na tlačítko **vzdálené plochy**.
4. Klikněte na tlačítko **Connect** a postupujte podle pokynů. Pokud připojení je zakázat, musíte ji nejprve povolit. Zajistěte, aby pomocí vzdálené plochy uživatele uživatelského jména a hesla.  Nelze použít přihlašovací údaje uživatele clusteru.

## <a name="open-hadoop-command-line"></a>Otevřete příkazový řádek Hadoop
Připojte se ke clusteru pomocí vzdálené plochy a použijte příkazový řádek Hadoop, musíte nejprve povolíte přístup ke vzdálené ploše do clusteru jak je popsáno v předchozí části.

**Chcete-li otevřít příkazový řádek Hadoop**

1. Připojte se ke clusteru pomocí vzdálené plochy.
2. Z plochy, klikněte dvakrát na **Hadoop příkazového řádku**.

    ![HDI.HadoopCommandLine][image-hadoopcommandline]

    Další informace o Hadoop příkazů najdete v tématu [Hadoop příkazy odkaz](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html).

Název složky v předchozím snímku obrazovky má číslo verze Hadoop vložených. Číslo verze můžete měnit v závislosti na verzi Hadoop komponenty nainstalované v clusteru. Proměnné prostředí Hadoop můžete použít k odkazování na těchto složek. Příklad:

    cd %hadoop_home%
    cd %hive_home%
    cd %hbase_home%
    cd %pig_home%
    cd %sqoop_home%
    cd %hcatalog_home%

## <a name="next-steps"></a>Další postup
V tomto článku jste se naučili postup vytvoření clusteru HDInsight pomocí portálu a otevřete nástroj příkazového řádku Hadoop. Další informace naleznete v následujících článcích:

* [Spravovat HDInsight pomocí prostředí Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Spravovat HDInsight pomocí rozhraní příkazového řádku Azure](hdinsight-administer-use-command-line.md)
* [Vytvoření clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Odesílání úloh Hadoop prostřednictvím kódu programu](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Začínáme s Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Jaká verze Hadoop je v Azure HDInsight?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-command-line.png "Hadoop příkazového řádku"
