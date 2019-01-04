---
title: Nasazení a správa topologií Apache Storm v Azure HDInsight
description: Zjistěte, jak nasadit, monitorovat a správa topologií Apache Storm pomocí řídicího panelu Storm v HDInsight se systémem Linux. Použití nástrojů Hadoopu pro Visual Studio.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: d194a5929e648c09eb204860c528e48bc55259ee
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53635393"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Nasazení a správa topologií Apache Storm v Azure HDInsight 

V tomto dokumentu, seznamte se se základy monitorování a správa [Apache Storm](https://storm.apache.org/) topologií Storm a systémem v clusterech HDInsight.

> [!IMPORTANT]  
> Kroky v tomto článku vyžadují linuxovým Storm v clusteru HDInsight. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement). 
>
> Informace o nasazení a monitorování topologií v HDInsight se systémem Windows, naleznete v tématu [nasazení a správa topologií Apache Storm v HDInsight se systémem Windows](apache-storm-deploy-monitor-topology.md).


## <a name="prerequisites"></a>Požadavky

* **Storm se systémem Linux v clusteru HDInsight**: viz [Začínáme s Apache Storm v HDInsight](apache-storm-tutorial-get-started-linux.md) pokyny týkající se vytvoření clusteru

* (Volitelné) **Znalost SSH a SCP**: Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

* (Volitelné) **Sady visual Studio**: Azure SDK 2.5.1 nebo novější a Data Lake Tools pro Visual Studio. Další informace najdete v tématu [Začínáme pomocí nástrojů Data Lake pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    Jeden z následujících verzí sady Visual Studio:

  * Visual Studio 2012 s aktualizací Update 4

  * Visual Studio 2013 with Update 4 nebo [Visual Studio 2013 Community](https://go.microsoft.com/fwlink/?LinkId=517284)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/)

  * Visual Studio 2015 (libovolná edice)

  * Visual Studio 2017 (libovolná edice). Data Lake Tools pro Visual Studio 2017 se instalují jako součást sady funkcí Azure.

## <a name="submit-a-topology-visual-studio"></a>Odešlete topologii: Visual Studio

Nástroje HDInsight je možné odeslat jazyka C# nebo hybridní topologie pro váš cluster Storm. Následující kroky používají ukázková aplikace. Informace o vytváření pomocí nástrojů HDInsight najdete v tématu [vývoj topologií C# pomocí nástrojů HDInsight pro Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

1. Pokud jste ještě nenainstalovali nejnovější verzi nástrojů Data Lake pro Visual Studio, přečtěte si téma [Začínáme pomocí nástrojů Data Lake pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > Nástroje Data Lake pro Visual Studio se dřív označovaly jako HDInsight Tools pro Visual Studio.
    >
    > Jsou součástí nástrojů data Lake pro Visual Studio __Azure Workload__ pro Visual Studio 2017.

2. Otevřít Visual Studio, vyberte **souboru** > **nový** > **projektu**.

3. V **nový projekt** dialogového okna rozbalte **nainstalováno** > **šablony**a pak vyberte **HDInsight**. V seznamu šablon vyberte **Storm ukázka**. V dolní části dialogového okna zadejte název aplikace.

    ![image](./media/apache-storm-deploy-monitor-topology-linux/sample.png)

4. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **odeslat do Storm v HDInsight**.

   > [!NOTE]  
   > Pokud se zobrazí výzva, zadejte přihlašovací údaje pro vaše předplatné Azure. Pokud máte více než jedno předplatné, přihlaste se na ten, který obsahuje váš cluster Storm v HDInsight.

5. Vyberte váš cluster Storm v HDInsight z **Storm Cluster** rozevíracího seznamu a pak vyberte **odeslat**. Můžete sledovat, jestli je pomocí úspěšného odeslání **výstup** okna.

## <a name="submit-a-topology-ssh-and-the-storm-command"></a>Odešlete topologii: SSH a příkaz Storm

1. Pomocí SSH se připojte ke clusteru HDInsight. Nahraďte **uživatelské jméno** název vaší přihlašování přes SSH. Nahraďte **CLUSTERNAME** názvem vašeho clusteru HDInsight:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Další informace o používání SSH pro připojení ke clusteru HDInsight najdete v tématu [použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Následující příkaz použijte ke spuštění ukázkové topologie:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount

    Tento příkaz v clusteru spustí ukázkovou topologii WordCount. Tato topologie náhodně generovat věty a pak se počítá výskyt jednotlivých slov v těchto větách.

   > [!NOTE]  
   > Při odesílání topologie do clusteru je nutné nejprve zkopírovat soubor jar obsahující cluster před použitím příkazu `storm`. Pokud chcete zkopírovat soubor do clusteru, můžete použít `scp` příkazu. Například `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`.
   >
   > Příklad WordCount a další příklady starter storm jsou již zahrnuty v clusteru na `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Odeslat topologii: prostřednictvím kódu programu

Programově můžete nasadit topologii s použitím služby Nimbus. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) poskytuje příklad, který ukazuje, jak nasadit a spustit topologii přes službu Nimbus aplikace v Javě.

## <a name="monitor-and-manage-visual-studio"></a>Monitorování a správa: Visual Studio

Při odesílání topologie pomocí sady Visual Studio **topologií Storm** zobrazení se zobrazí. Vyberte ze seznamu k zobrazení informací o topologii spuštěné topologie.

![monitorování sady Visual studio](./media/apache-storm-deploy-monitor-topology-linux/vsmonitor.png)

> [!NOTE]  
> Můžete také zobrazit **topologií Storm** z **Průzkumníka serveru** tak, že rozbalíte **Azure** > **HDInsight**a pak pravým tlačítkem cluster Storm v HDInsight a výběrem **zobrazit topologie Storm**.

Vyberte tvar spoutů a boltů zobrazíte informace o těchto součástí. Otevře se nové okno pro každé vybrané položky.

### <a name="deactivate-and-reactivate"></a>Deaktivovat a znovu aktivovat

Deaktivace topologii pozastaví ho, dokud nebude ukončen nebo znovu aktivovat. Chcete-li provádět tyto operace, použijte __deaktivovat__ a __znovu aktivovat__ tlačítka v horní části __souhrn topologie__.

### <a name="rebalance"></a>Obnovit rovnováhu

Opětovné vyvážení topologii umožňuje systému revidovat paralelismus topologii. Pokud jste změnili velikost clusteru přidat další poznámky, například opětovné vyvážení umožňuje topologie zobrazíte nové uzly.

Chcete-li obnovit rovnováhu topologie, použijte __obnovit rovnováhu__ tlačítko v horní části __souhrn topologie__.

> [!WARNING]  
> Opětovné vyvážení topologii nejprve deaktivuje topologii, pak rovnoměrně distribuuje pracovní procesy napříč clusterem a pak nakonec vrátí topologie do stavu, ve kterém se nacházel před nové vyvážení došlo k chybě. Takže pokud topologii je aktivní, je opět aktivní. Pokud se deaktivovalo, zůstane deaktivované.

### <a name="kill-a-topology"></a>Ukončit topologii

Topologie Storm pokračovat, spuštění, dokud se zastaví nebo odstranění clusteru. Chcete-li ukončit topologii, použijte __Kill__ tlačítko v horní části __souhrn topologie__.

## <a name="monitor-and-manage-ssh-and-the-storm-command"></a>Monitorování a správa: SSH a příkaz Storm

`storm` Nástroj umožňuje práci se spuštěnými topologiemi z příkazového řádku. Použití `storm -h` pro úplný seznam příkazů.

### <a name="list-topologies"></a>Seznam topologií

Pomocí následujícího příkazu zobrazíte seznam všech topologií:

    storm list

Tento příkaz by měl vrátit informace podobné následujícímu textu:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Deaktivovat a znovu aktivovat

Deaktivace topologii pozastaví ho, dokud nebude ukončen nebo znovu aktivovat. Deaktivovat a znovu aktivovat, použijte následující příkaz:

    storm Deactivate TOPOLOGYNAME

    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>Ukončit probíhající topologii

Po spuštění topologií Storm, pokračovat spuštění, dokud se zastavila. Pokud chcete ukončit topologii, použijte následující příkaz:

    storm kill TOPOLOGYNAME

### <a name="rebalance"></a>Obnovit rovnováhu

Opětovné vyvážení topologii umožňuje systému revidovat paralelismus topologii. Pokud jste změnili velikost clusteru přidat další poznámky, například opětovné vyvážení umožňuje topologie zobrazíte nové uzly.

> [!WARNING]  
> Opětovné vyvážení topologii nejprve deaktivuje topologii, pak rovnoměrně distribuuje pracovní procesy napříč clusterem a pak nakonec vrátí topologie do stavu, ve kterém se nacházel před nové vyvážení došlo k chybě. Takže pokud topologii je aktivní, je opět aktivní. Pokud se deaktivovalo, zůstane deaktivované.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-storm-ui"></a>Monitorování a správa: Storm UI

Uživatelské rozhraní Storm poskytuje webové rozhraní pro práci se spuštěnými topologiemi a je součástí clusteru HDInsight. Chcete-li zobrazit uživatelské rozhraní Storm pomocí webového prohlížeče otevřete **https://CLUSTERNAME.azurehdinsight.net/stormui**, kde **CLUSTERNAME** je název vašeho clusteru.

> [!NOTE]  
> Pokud budete vyzváni k zadání uživatelského jména a hesla, zadejte správce clusteru (admin) a heslo použité při vytvoření clusteru.

### <a name="main-page"></a>Hlavní stránka

Hlavní stránka uživatelského rozhraní Storm poskytuje následující informace:

* **Souhrn clusteru**: Základní informace o clusteru Storm.
* **Souhrn topologie**: Seznam spuštěných topologií. Pomocí odkazů v této části můžete zobrazit další informace o konkrétních topologií.
* **Dohledové Souhrn**: Informace o vedoucí Storm.
* **Konfigurace nimbus**: Nimbus konfiguraci clusteru.

### <a name="topology-summary"></a>Souhrn topologie

Vyberte odkaz z **souhrn topologie** části zobrazí následující informace o topologii:

* **Souhrn topologie**: Základní informace o topologii.
* **Akce topologie**: Akce správy, které můžete provést pro topologie.

  * **Aktivovat**: Obnoví zpracování deaktivované topologie.
  * **Deaktivovat**: Pozastaví spuštěné topologie.
  * **Obnovit rovnováhu**: Upraví paralelismus topologii. Po změně počtu uzlů v clusteru musíte znovu vyvážit spuštěné topologie. Tato operace umožňuje topologii upravovat paralelismus za účelem kompenzace zvýšení nebo snížení počtu uzlů v clusteru.

    Další informace najdete v tématu <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">pochopení paralelismu topologie Apache Storm</a>.
  * **Ukončit**: Ukončí topologii Storm po zadaný časový limit.
* **Statistiky topologie**: Statistika týkající se topologie. Pokud chcete nastavit časový rámec pro zbývající položky na stránce, použijte odkazy v **okno** sloupce.
* **Spouts**: Spoutů používat topologii. Chcete-li zobrazit další informace o konkrétních funkcích spouts pomocí odkazů v této části.
* **Bolts**: Bolty používat topologii. Pomocí odkazů v této části můžete zobrazit další informace o konkrétních funkcích bolts.
* **Topologie konfigurace**: Konfigurace vybraného topologie.

### <a name="spout-and-bolt-summary"></a>Spout a Bolt souhrn

Výběr spout z **Spouts** nebo **Bolts** části zobrazí následující informace o vybrané položce:

* **Souhrn komponenty**: Základní informace o funkcích spout nebo bolt.
* **Funkcí spout/Bolt statistiky**: Statistické údaje o funkcích spout nebo bolt. Pokud chcete nastavit časový rámec pro zbývající položky na stránce, použijte odkazy v **okno** sloupce.
* **Statistiky vstupu** (pouze funkce bolt): Informace o vstupní datové proudy využívaná funkcí bolt.
* **Statististiky výstupu**: Informace o datové proudy vyslané ve funkcích spout nebo bolt.
* **Prováděcí moduly**: Informace o instancích sady funkcích spout nebo bolt. Vyberte **Port** vytvořený záznam pro konkrétní prováděcí modul zobrazení protokolu diagnostických informací pro tuto instanci.
* **Chyby**: Veškeré informace o chybě pro funkcích spout nebo bolt.

## <a name="monitor-and-manage-rest-api"></a>Monitorování a správa: REST API

Uživatelské rozhraní Storm je postavený na rozhraní REST API, takže můžete provádět podobné správy a monitorování funkce pomocí rozhraní REST API. Rozhraní REST API můžete použít k vytvoření vlastních nástrojů pro správu a monitorování topologií Storm.

Další informace najdete v tématu [Apache Storm uživatelského rozhraní REST API](https://storm.apache.org/releases/current/STORM-UI-REST-API.html). Tyto informace je specifická pro Apache Storm v HDInsight pomocí rozhraní REST API.

> [!IMPORTANT]  
> Rozhraní REST API Storm není veřejně přístupné přes internet a musí přistupovat přes tunelové propojení SSH k hlavnímu uzlu clusteru HDInsight. Informace o vytváření a používání tunelu SSH najdete v tématu [používání tunelového propojení SSH pro přístup k webové uživatelské rozhraní Apache Ambari, ResourceManager, JobHistory, NameNode, Apache Oozie a dalším webovým uživatelským rozhraním](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>Základní identifikátor URI

Základní identifikátor URI pro rozhraní REST API v clusterech HDInsight založených na Linuxu je k dispozici na na hlavní uzel **https://HEADNODEFQDN:8744/api/v1/**. Název domény hlavního uzlu je generována při vytváření clusteru a není statická.

Plně kvalifikovaný název domény (FQDN) pro hlavní uzel clusteru najdete v několika různými způsoby:

* **Z relace SSH**: Použijte příkaz `headnode -f` z relace SSH do clusteru.
* **Z webu Ambari**: Vyberte **služby** z horní části stránky vyberte **Storm**. Z **Souhrn** kartu, vyberte možnost **Server uživatelského rozhraní Storm**. Plně kvalifikovaný název domény uzlu, který je hostitelem Storm UI a rozhraní REST API se zobrazí v horní části stránky.
* **Z rozhraní Ambari REST API**: Použijte příkaz `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` k načtení informací o uzlu, který se systémem Storm UI a rozhraní REST API. Nahraďte **CLUSTERNAME** s názvem clusteru. Po zobrazení výzvy zadejte heslo pro účet (správce). V odpovědi obsahuje položky "název_hostitele" plně kvalifikovaný název domény uzlu.

### <a name="authentication"></a>Authentication

Požadavky rozhraní REST API musí používat **základní ověřování**, takže se pomocí jména správce clusteru HDInsight a heslo.

> [!NOTE]  
> Protože odesílají základní ověřování pomocí prostého textu, měli byste **vždy** zabezpečená komunikace s využitím clusteru pomocí protokolu HTTPS.

### <a name="return-values"></a>Vrácené hodnoty

Použitelné z v rámci clusteru může být pouze informace vrácené z rozhraní REST API. Například plně kvalifikovaný název domény (FQDN) vrátil pro [Apache ZooKeeper](https://zookeeper.apache.org/) serverů není přístupný z Internetu.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak [založené na jazyce Java vyvíjet topologie pomocí nástroje Apache Maven](apache-storm-develop-java-topology.md).

Seznam Další příklad topologie najdete v tématu [příklad topologií pro Apache Storm v HDInsight](apache-storm-example-topology.md).
