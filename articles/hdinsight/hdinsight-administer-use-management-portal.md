---
title: Správa clusterů Hadoop využívající systém Windows v HDInsight pomocí webu Azure portal
description: Zjistěte, jak spravovat službu HDInsight. Vytvoření clusteru HDInsight, otevřete interaktivní konzoly jazyka JavaScript a otevřete konzoly příkaz Hadoop.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: a4cbc0c14d0f6c505b391becf33c56dd95bfc251
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39592216"
---
# <a name="manage-windows-based-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Správa clusterů Hadoop využívající systém Windows v HDInsight pomocí webu Azure portal

Použití [webu Azure portal][azure-portal], můžete vytvářet clustery Hadoop využívající systém Windows v Azure HDInsight, změnit heslo uživatele Hadoop a povolení protokolu RDP (Remote Desktop), aby měli přístup k příkazu Hadoop konzoly v clusteru.

Informace v tomto článku se vztahuje pouze na clusterech HDInsight založených na oknech. Informace o správě clusterů se systémem Linux najdete v tématu [spravovat Hadoop clusterů v HDInsight pomocí webu Azure portal](hdinsight-administer-use-portal-linux.md).

> [!IMPORTANT]
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).


## <a name="prerequisites"></a>Požadavky

Je nutné, abyste před zahájením tohoto článku měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Účet služby Azure Storage** – cluster HDInsight používá kontejneru úložiště objektů Blob v Azure jako výchozí systém souborů. Další informace o tom, jak Azure Blob storage poskytuje bezproblémové prostředí s clustery HDInsight najdete v tématu [použití služby Azure Blob Storage s HDInsight](hdinsight-hadoop-use-blob-storage.md). Podrobnosti o vytvoření účtu služby Azure Storage najdete v tématu [způsob vytvoření účtu úložiště](../storage/common/storage-create-storage-account.md).

## <a name="open-the-portal"></a>Otevřít na portálu
1. Přihlaste se k [ https://portal.azure.com ](https://portal.azure.com).
2. Po otevření portálu můžete:

   * Klikněte na tlačítko **vytvořit prostředek** v levé nabídce na vytvoření nového clusteru:

       ![tlačítko Nový cluster HDInsight](./media/hdinsight-administer-use-management-portal/azure-portal-new-button.png)
   * Klikněte na tlačítko **clustery HDInsight** v levé nabídce.

       ![Azure portal tlačítko clusteru HDInsight](./media/hdinsight-administer-use-management-portal/azure-portal-hdinsight-button.png)

     Pokud **HDInsight** nebude zobrazovat v nabídce vlevo, klikněte na tlačítko **Procházet**.

     ![Azure portal tlačítko pro procházení clusteru](./media/hdinsight-administer-use-management-portal/azure-portal-browse-button.png)

## <a name="create-clusters"></a>Vytváření clusterů
Vytvoření pokyny, pomocí portálu najdete v tématu [clusterů HDInsight vytvořit](hdinsight-hadoop-provision-linux-clusters.md).

HDInsight funguje s komponentami Hadoop široký rozsah. Seznam komponent, které byly ověřeny a podporované, najdete v části [je jaká verze systému Hadoop v Azure HDInsight](hdinsight-component-versioning.md). HDInsight můžete přizpůsobit pomocí jedné z následujících možností:

* Použití akce skriptu ke spuštění vlastních skriptů, které můžete přizpůsobit clusteru změnit konfiguraci clusteru nebo nainstalovat vlastní komponenty, například Giraph nebo Solr. Další informace najdete v tématu [clusteru HDInsight přizpůsobení pomocí akce skriptu](hdinsight-hadoop-customize-cluster.md).
* Při vytváření clusteru pomocí vlastního nastavení parametrů clusteru v HDInsight .NET SDK nebo v prostředí Azure PowerShell. Tyto změny konfigurace jsou pak nezachová dobu živostnosti clusteru a obnoví uzlu clusteru, které platformy Azure provádí pravidelné kvůli údržbě neovlivní. Další informace o používání vlastního nastavení parametrů clusteru najdete v tématu [clusterů HDInsight vytvořit](hdinsight-hadoop-provision-linux-clusters.md).
* Některé nativní Java komponent, jako jsou Mahout a možností, může běžet v clusteru jako soubory JAR. Tyto soubory JAR můžete distribuovat do úložiště objektů Blob v Azure a odeslat do clusterů HDInsight prostřednictvím mechanismy odesílání úloh Hadoop. Další informace najdete v tématu [Hadoop odeslání úlohy prostřednictvím kódu programu](hadoop/submit-apache-hadoop-jobs-programmatically.md).

  > [!NOTE]
  > Pokud máte problémy s nasazením soubory JAR do clusterů HDInsight nebo volání soubory JAR na clusterech HDInsight, obraťte se na [Microsoft Support](https://azure.microsoft.com/support/options/).
  >
  > Kaskádová šablona nepodporuje HDInsight a nesplňuje podmínky pro Microsoft Support. Seznam podporovaných součásti, naleznete v tématu [co je nového ve verzích clusterů HDInsight poskytuje](hdinsight-component-versioning.md).
  >
  >

Instalace vlastního softwaru v clusteru pomocí připojení ke vzdálené ploše není podporována. Neměli byste ukládat soubory na jednotkách hlavního uzlu, jak budou ztraceny, budete muset znovu vytvořit clustery. Doporučujeme ukládat soubory do úložiště objektů Blob v Azure. Úložiště objektů blob je trvalé.

## <a name="list-and-show-clusters"></a>Seznam a zobrazení clusterů
1. Přihlaste se k [ https://portal.azure.com ](https://portal.azure.com).
2. Klikněte na tlačítko **clustery HDInsight** v levé nabídce.
3. Klikněte na název clusteru. Pokud je dlouhý seznam clusterů, můžete použít filtr horní části stránky.
4. Dvakrát klikněte na cluster ze seznamu zobrazíte podrobnosti.

    **Nabídky a essentials**:

    ![Azure portal essentials clusteru HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-essentials.png)

   * Přizpůsobení nabídky, klikněte pravým tlačítkem na libovolné místo v nabídce a potom klikněte na tlačítko **vlastní**.
   * **Nastavení** a **všechna nastavení**: zobrazí **nastavení** okno pro cluster, který vám umožní přistupovat k podrobné informace o konfiguraci pro cluster.
   * **Řídicí panel**, **řídicí panel clusteru** a **URL**: Toto jsou všechny způsoby přístupu k řídicí panel clusteru, který je Ambari Web pro clustery založené na Linuxu.
   * **Secure Shell**: zobrazí pokyny pro připojení ke clusteru pomocí připojení Secure Shell (SSH).
   * **Škálování clusteru**: umožňuje změnit počet uzlů pracovního procesu pro tento cluster.
   * **Odstranit**: Odstraní cluster.
   * **Rychlý Start**: Zobrazí informace, které vám pomůžou začít používat HDInsight.
   * **Uživatelé**: umožňuje nastavit oprávnění pro *portálu správy* tohoto clusteru pro ostatní uživatele v rámci předplatného Azure.

     > [!IMPORTANT]
     > To *pouze* má vliv na přístup a oprávnění pro tento cluster na webu Azure Portal a nemá žádný vliv na který můžete připojit k nebo odesílat úlohy do clusteru HDInsight.
     >
     >
   * **Značky**: klíčová slova umožňují vám umožní nastavit páry klíč/hodnota k definování vlastní taxonomii z vašich cloudových služeb. Můžete například vytvořit klíč s názvem **projektu**a pak použít společné hodnoty pro všechny služby související s konkrétním projektu.
   * **Zobrazení Ambari**: odkazy na webové Ambari.

     > [!IMPORTANT]
     > Pokud chcete spravovat služby poskytované clusteru HDInsight, musíte použít Ambari Web nebo Ambari REST API. Další informace o použití Ambari, naleznete v tématu [HDInsight Správa clusterů pomocí nástroje Ambari](hdinsight-hadoop-manage-ambari.md).
     >
     >

     **Využití**:

     ![Azure portal využívání clusteru HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-portal-cluster-usage.png)
5. Klikněte na tlačítko **nastavení**.

    ![Azure portal využívání clusteru HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.settings.png)

   * **Vlastnosti**: zobrazení vlastností clusteru.
   * **Cluster AAD Identity**:
   * **Klíče Azure Storage**: Zobrazit výchozí účet úložiště a jeho klíčem. Účet úložiště je konfigurace během procesu vytváření clusteru.
   * **Přihlášení clusteru**: Změna clusteru HTTP uživatelského jména a hesla.
   * **Externí Metaúložiště**: Zobrazit metaúložiště Hive a Oozie. Metaúložiště se dá nakonfigurovat jenom během procesu vytváření clusteru.
   * **Škálování clusteru**: zvýšení a snížení počtu pracovních uzlů clusteru.
   * **Vzdálená plocha**: Povolit a zakázat přístup ke vzdálené ploše (RDP) a konfigurace protokolu RDP uživatelské jméno.  Uživatelské jméno RDP musí být odlišný od názvu uživatele protokolu HTTP.
   * **Partner of Record**:

     > [!NOTE]
     > Toto je obecný seznam dostupných nastavení; Ne všechny z nich bude k dispozici pro všechny typy clusteru.
     >
     >
6. Klikněte na tlačítko **vlastnosti**:

    Oddíl properties se zobrazí následující informace:

   * **Název hostitele**: název clusteru.
   * **Adresa URL clusteru**.
   * **Stav**: zahrnují přerušen, přijetí, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, provozní, s, chyba, odstranění, odstranit, vypršel časový limit, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, ClusterCustomization CertRolloverQueued ResizeQueued,
   * **Oblast**: umístění Azure. Seznam podporovaných umístění Azure, najdete v článku **oblasti** rozevíracího seznamu na [ceny HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   * **Data vytvořená**.
   * **Operační systém**: buď **Windows** nebo **Linux**.
   * **Typ**: Hadoop, HBase, Storm, Spark.
   * **Verze**. Zobrazit [HDInsight verze](hdinsight-component-versioning.md)
   * **Předplatné**: Název předplatného.
   * **ID předplatného**.
   * **Primární zdroj dat**. Účet úložiště objektů Blob v Azure používají jako výchozí systém souborů Hadoop.
   * **Cenová úroveň pracovních uzlů**.
   * **Hlavní uzel cenovou úroveň**.

## <a name="delete-clusters"></a>Odstranění clusterů
Odstranění clusteru nedojde k odstranění výchozí účet úložiště nebo všechny propojené účty úložiště. Cluster můžete znovu vytvořit pomocí stejné účty úložiště a metaúložišti stejné.

1. Přihlaste se k [portál][azure-portal].
2. Klikněte na tlačítko **Procházet vše** v levé nabídce klikněte na tlačítko **clustery HDInsight**, klikněte na název clusteru.
3. Klikněte na tlačítko **odstranit** z hlavní nabídky a pak postupujte podle pokynů.

Viz také [pozastavit a vypnout clustery](#pauseshut-down-clusters).

## <a name="scale-clusters"></a>Škálování clusterů
Funkce škálování clusteru umožňuje změnit počet uzlů pracovního procesu se používá cluster, který běží v Azure HDInsight bez nutnosti nového vytváření clusteru.

> [!NOTE]
> Pouze clustery HDInsight verze 3.1.3 nebo vyšší nejsou podporovány. Pokud si nejste jistí verze vašeho clusteru, můžete zkontrolovat na stránce Vlastnosti.  Zobrazit [výpisu a zobrazení clusterů](#list-and-show-clusters).
>
>

Dopad Změna počtu datových uzlů pro každý typ clusteru podporuje HDInsight:

* Hadoop

    Bezproblémově můžete zvýšit počet pracovních uzlů v clusteru Hadoop, na kterém běží bez dopadu na všechny úlohy čekající na vyřízení nebo spuštěné. Nové úlohy můžete odeslat také když probíhá operace. Selhání v rámci operace škálování jsou zpracovány bez výpadku v tak, aby cluster zůstane vždy ve funkčním stavu.

    Pokud je Hadoop cluster je kapacitu vertikálně snížit snížením počtu datových uzlů, jsou restartovat některé ze služeb v clusteru. To způsobí, že všechny spuštěné a čekající úlohy selhání po dokončení operace škálování. Můžete, ale neúspěšné úlohy po dokončení operace.
* HBase

    Bezproblémově můžete přidat nebo odebrat uzly do clusteru HBase během jejího běhu. Oblastní servery jsou automaticky rovnoměrně rozdělen do několika minut od dokončení operace škálování. Oblastní servery však můžete také ručně vyvážit změnou přihlášení k hlavnímu uzlu clusteru a spustíte tento příkaz z okna příkazového řádku:

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

    Další informace o používání prostředí HBase najdete v tématu]
* Storm

    Bezproblémově můžete přidat nebo odebrat datových uzlů do clusteru Storm během jejího běhu. Ale po úspěšném dokončení operace škálování, budete muset vyrovnat topologie.

    Opětovné vyvážení lze provést dvěma způsoby:

  * Webové uživatelské rozhraní Storm
  * Nástroje rozhraní příkazového řádku (CLI)

    Najdete [dokumentaci Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) další podrobnosti.

    Webové uživatelské rozhraní Storm je k dispozici v clusteru HDInsight:

    ![Obnovení rovnováhy škálování HDInsight storm](./media/hdinsight-administer-use-management-portal/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Tady je příklad jak obnovit rovnováhu topologie Storm pomocí příkazu rozhraní příkazového řádku:

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors
        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

**Škálování clusterů**

1. Přihlaste se k [portál][azure-portal].
2. Klikněte na tlačítko **Procházet vše** v levé nabídce klikněte na tlačítko **clustery HDInsight**, klikněte na název clusteru.
3. Klikněte na tlačítko **nastavení** z hlavní nabídky a pak klikněte na tlačítko **škálování clusteru**.
4. Zadejte **počet pracovních uzlů**. Limit počtu uzlů clusteru se liší mezi předplatným Azure. Můžete požádat podporu fakturace o navýšení limitu.  Informace o nákladech projeví změny, které jste provedli z počtu uzlů.

    ![HDInsight Hadoop HBase, Storm Spark škálování](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)

## <a name="pauseshut-down-clusters"></a>Pozastavit a vypnout clusterů
Většina úloh Hadoop jsou dávkové úlohy, které jsou pouze někdy spustili. Většina clusterů Hadoop existují velké časová období, ve které se cluster nepoužívá pro zpracování. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán.
Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány.

Existuje mnoho způsobů, které můžete naprogramovat procesu:

* Uživatel Azure Data Factory. Zobrazit [propojená služba Azure HDInsight](../data-factory/compute-linked-services.md) a [transformace a analýzy s využitím Azure Data Factory](../data-factory/transform-data.md) HDInsight na vyžádání a místním definovaná propojené služby.
* Použití Azure Powershellu.  Zobrazit [analyzovat zpoždění letů](hdinsight-analyze-flight-delay-data.md).
* Pomocí Azure CLI. Zobrazit [HDInsight Správa clusterů pomocí rozhraní příkazového řádku Azure](hdinsight-administer-use-command-line.md).
* Použití sady HDInsight .NET SDK. Zobrazit [úlohy systému Hadoop odeslat](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Informace o cenách najdete v části [ceny HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Pokud chcete odstranit cluster z portálu, přečtěte si téma [odstranění clusterů](#delete-clusters)

## <a name="change-cluster-username"></a>Změnit uživatelské jméno clusteru
HDInsight cluster může mít dva uživatelské účty. Uživatelský účet clusteru HDInsight se vytvoří během procesu vytváření. Můžete také vytvořit uživatelský účet protokolu RDP pro přístup ke clusteru pomocí protokolu RDP. Zobrazit [povolit vzdálenou plochu](#connect-to-hdinsight-clusters-by-using-rdp).

**Chcete-li změnit uživatelské jméno clusteru HDInsight a heslo**

1. Přihlaste se k [portál][azure-portal].
2. Klikněte na tlačítko **Procházet vše** v levé nabídce klikněte na tlačítko **clustery HDInsight**, klikněte na název clusteru.
3. Klikněte na tlačítko **nastavení** z hlavní nabídky a pak klikněte na tlačítko **přihlášení ke clusteru**.
4. Pokud **přihlášení ke clusteru** byla povolena, musíte kliknout na **zakázat**a potom klikněte na tlačítko **povolit** si mohli změnit uživatelské jméno a heslo...
5. Změnit **clusteru přihlašovací jméno** a/nebo **přihlašovací heslo clusteru**a potom klikněte na tlačítko **Uložit**.

    ![HDInsight změnit uživatele http heslo uživatelského jména uživatele clusteru](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

## <a name="grantrevoke-access"></a>Udělení nebo odvolání přístupu
Clustery HDInsight mají následující webové služby HTTP (mít všechny tyto služby RESTful koncových bodů):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Tyto služby jsou ve výchozím nastavení oprávnění pro přístup. Vám může revoke/udělit přístup z portálu Azure portal.

> [!NOTE]
> Pomocí udělení nebo odvolání přístupu, resetuje clusteru uživatelské jméno a heslo.
>
>

**K udělení nebo odvolání přístupu protokolu HTTP webové služby**

1. Přihlaste se k [portál][azure-portal].
2. Klikněte na tlačítko **Procházet vše** v levé nabídce klikněte na tlačítko **clustery HDInsight**, klikněte na název clusteru.
3. Klikněte na tlačítko **nastavení** z hlavní nabídky a pak klikněte na tlačítko **přihlášení ke clusteru**.
4. Pokud **přihlášení ke clusteru** byla povolena, musíte kliknout na **zakázat**a potom klikněte na tlačítko **povolit** si mohli změnit uživatelské jméno a heslo...
5. Pro **uživatelské jméno přihlášení clusteru** a **přihlašovací heslo clusteru**, zadejte nové uživatelské jméno a heslo (v uvedeném pořadí) pro cluster.
6. Klikněte na **ULOŽIT**.

    ![HDInsight součet odebrat přístup protokolu http webové služby](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

## <a name="find-the-default-storage-account"></a>Najít výchozí účet úložiště
Každý cluster HDInsight má výchozí účet úložiště. Výchozí účet úložiště a jeho klíče pro cluster se zobrazí v části **nastavení**/**vlastnosti**/**klíčů k úložišti Azure**. Zobrazit [výpisu a zobrazení clusterů](#list-and-show-clusters).

## <a name="find-the-resource-group"></a>Najít skupinu prostředků
V režimu Azure Resource Manageru se každý cluster HDInsight vytvoří se skupina prostředků Azure. Skupina prostředků Azure, které patří clusteru se zobrazí v:

* Obsahuje seznam clusterů **skupiny prostředků** sloupce.
* Cluster **základní** dlaždici.  

Zobrazit [výpisu a zobrazení clusterů](#list-and-show-clusters).

## <a name="open-hdinsight-query-console"></a>Otevřete konzoly pro dotazy na HDInsight
Konzole pro dotazy na HDInsight zahrnuje následující funkce:

* **Hive Editor**: grafickým uživatelským rozhraním A webové rozhraní pro odesílání úloh Hive.  Zobrazit [spouštění dotazů Hive pomocí konzoly pro dotazy](hadoop/apache-hadoop-use-hive-query-console.md).

    ![Editor portálu hive v HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-hive-editor.png)
* **Historie úlohy**: úlohy systému Hadoop monitorování.  

    ![Historie úlohy portálu HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-job-history.png)

    Klikněte na tlačítko **název dotazu** zobrazíte podrobnosti, včetně vlastnosti úlohy **dotaz na úlohu**, a ** výstup úlohy. Můžete také stáhnout dotaz a výstup do pracovní stanice.
* **Soubor prohlížeče**: procházení výchozí účet úložiště a propojené účty úložiště.

    ![Procházet HDInsight portálu soubor prohlížeče](./media/hdinsight-administer-use-management-portal/hdinsight-file-browser.png)

    Na snímku obrazovky **<Account>** označuje položku je účet úložiště Azure.  Klikněte na název účtu, který Procházet soubory.
* **Uživatelské rozhraní systému Hadoop**.

    ![Uživatelské rozhraní systému Hadoop HDInsight portálu](./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-ui.png)

    Z **uživatelského rozhraní systému Hadoop*, můžete procházet soubory a v protokolech.
* **Uživatelské rozhraní yarn**.

    ![Uživatelské rozhraní YARN HDInsight portálu](./media/hdinsight-administer-use-management-portal/hdinsight-yarn-ui.png)

## <a name="run-hive-queries"></a>Spuštění dotazů Hive
Spouštění úloh Hive z portálu, klikněte na tlačítko **Hive Editor** v konzole pro dotazy na HDInsight. Zobrazit [konzoly otevřete dotaz HDInsight](#open-hdinsight-query-console).

## <a name="monitor-jobs"></a>Monitorování úloh
Sledujte úlohy z portálu, klikněte na tlačítko **historie úloh** v konzole pro dotazy na HDInsight. Zobrazit [konzoly otevřete dotaz HDInsight](#open-hdinsight-query-console).

## <a name="browse-files"></a>Procházet soubory
Chcete-li procházet soubory uložené na výchozí účet úložiště a propojené účty úložiště, klikněte na tlačítko **prohlížeč souborů** v konzole pro dotazy na HDInsight. Zobrazit [konzoly otevřete dotaz HDInsight](#open-hdinsight-query-console).

Můžete také použít **procházení systémem souborů** nástroje z **uživatelského rozhraní systému Hadoop** v konzole nástroje HDInsight.  Zobrazit [konzoly otevřete dotaz HDInsight](#open-hdinsight-query-console).

## <a name="monitor-cluster-usage"></a>Monitorování využití clusteru
**Využití** části z okna clusteru HDInsight se zobrazují informace o počet jader dostupných pro vaše předplatné pro použití s HDInsight, jakož i počet jader, které jsou přiděleny do tohoto clusteru a jak se přidělují pro uzly v tomto clusteru. Zobrazit [výpisu a zobrazení clusterů](#list-and-show-clusters).

> [!IMPORTANT]
> Pokud chcete monitorovat služby poskytované clusteru HDInsight, musíte použít Ambari Web nebo Ambari REST API. Další informace o použití Ambari, naleznete v tématu [HDInsight Správa clusterů pomocí nástroje Ambari](hdinsight-hadoop-manage-ambari.md)
>
>

## <a name="open-hadoop-ui"></a>Otevřete uživatelské rozhraní systému Hadoop
Monitorování clusteru, procházení systému souborů a v protokolech, klikněte na tlačítko **uživatelského rozhraní systému Hadoop** v konzole pro dotazy na HDInsight. Zobrazit [konzoly otevřete dotaz HDInsight](#open-hdinsight-query-console).

## <a name="open-yarn-ui"></a>Otevřete uživatelské rozhraní Yarn
Pokud chcete použít uživatelské rozhraní Yarn, klikněte na tlačítko **uživatelského rozhraní Yarn** v konzole pro dotazy na HDInsight. Zobrazit [konzoly otevřete dotaz HDInsight](#open-hdinsight-query-console).

## <a name="connect-to-clusters-using-rdp"></a>Připojení ke clusterům pomocí protokolu RDP
Přihlašovací údaje pro cluster, který jste zadali při jeho vytváření poskytnout přístup ke službám v clusteru, ale ne k samotnému clusteru přes vzdálenou plochu. Přístup ke vzdálené ploše můžete zapnout při zřizování clusteru nebo po zřízení clusteru. Pokyny týkající se povolení vzdálené plochy při vytváření, naleznete v tématu [clusteru HDInsight vytvořit](hdinsight-hadoop-provision-linux-clusters.md).

**Chcete-li povolit vzdálenou plochu**

1. Přihlaste se k [portál][azure-portal].
2. Klikněte na tlačítko **Procházet vše** v levé nabídce klikněte na tlačítko **clustery HDInsight**, klikněte na název clusteru.
3. Klikněte na tlačítko **nastavení** z hlavní nabídky a pak klikněte na tlačítko **vzdálené plochy**.
4. Zadejte **vyprší dne**, **uživatelské jméno pro vzdálenou plochu** a **heslo vzdálené plochy**a potom klikněte na tlačítko **povolit**.

    ![HDInsight povolit zakázat konfigurace vzdálené plochy](./media/hdinsight-administer-use-management-portal/hdinsight.portal.remote.desktop.png)

    Výchozí hodnoty pro vypršení platnosti na dojde už za týden.

   > [!NOTE]
   > Můžete také sady HDInsight .NET SDK k povolení služby Vzdálená plocha v clusteru. Použití **EnableRdp** metodu na objekt klienta HDInsight následujícím způsobem: **klienta. EnableRdp (název clusteru, umístění, "rdpuser", "rdppassword", DateTime.Now.AddDays(6))**. Podobně, chcete-li zakázat vzdálené plochy v clusteru, můžete použít **klienta. DisableRdp (název clusteru, umístění)**. Další informace o těchto metodách, naleznete v tématu [HDInsight .NET SDK – referenční informace](http://go.microsoft.com/fwlink/?LinkId=529017). To platí pouze pro clustery HDInsight se systémem Windows.
   >
   >

**Připojení ke clusteru pomocí protokolu RDP**

1. Přihlaste se k [portál][azure-portal].
2. Klikněte na tlačítko **Procházet vše** v levé nabídce klikněte na tlačítko **clustery HDInsight**, klikněte na název clusteru.
3. Klikněte na tlačítko **nastavení** z hlavní nabídky a pak klikněte na tlačítko **vzdálené plochy**.
4. Klikněte na tlačítko **připojit** a postupujte podle pokynů. Je-li připojit zakázat, musíte ho nejdřív povolit. Ujistěte se, že pomocí vzdálené plochy uživatelské jméno a heslo.  Nelze použít pověření uživatele clusteru.

## <a name="open-hadoop-command-line"></a>Otevřete příkazový řádek systému Hadoop
Pro připojení ke clusteru pomocí vzdálené plochy a použití příkazového řádku Hadoopu, musíte nejprve povolíte vzdálené plochy přístup ke clusteru jak je popsáno v předchozí části.

**Chcete-li otevřít příkazový řádek systému Hadoop**

1. Připojte se ke clusteru pomocí vzdálené plochy.
2. Z plochy, klikněte dvakrát na **příkazového řádku Hadoopu**.

    ![HDI.HadoopCommandLine][image-hadoopcommandline]

    Další informace o příkazy Hadoop najdete v tématu [příkazy Hadoop odkaz](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html).

Na předchozím snímku obrazovky má název složky vložené číslo verze systému Hadoop. Číslo verze můžete změnit v závislosti na verzi součásti platformy Hadoop, které jsou nainstalované v clusteru. K odkazování na tyto složky můžete použít proměnné prostředí systému Hadoop. Příklad:

    cd %hadoop_home%
    cd %hive_home%
    cd %hbase_home%
    cd %pig_home%
    cd %sqoop_home%
    cd %hcatalog_home%

## <a name="next-steps"></a>Další postup
V tomto článku jste se naučili vytvořit HDInsight cluster pomocí portálu a otevřete nástroj příkazového řádku Hadoopu. Další informace naleznete v následujících článcích:

* [Správa HDInsight pomocí Azure Powershellu](hdinsight-administer-use-powershell.md)
* [Správa HDInsight pomocí Azure CLI](hdinsight-administer-use-command-line.md)
* [Vytvoření clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Odesílání úloh Hadoop prostřednictvím kódu programu](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Začínáme s Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Jaká verze systému Hadoop je v Azure HDInsight?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-command-line.png "Hadoop příkazového řádku"
