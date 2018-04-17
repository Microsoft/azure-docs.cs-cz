---
title: Nasazení a správa topologií Apache Storm v HDInsight se systémem Linux | Microsoft Docs
description: Zjistěte, jak pro nasazení, monitorování a správa topologií Apache Storm pomocí řídicího panelu Storm v HDInsight se systémem Linux. Pomocí nástroje Hadoop pro sadu Visual Studio.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 35086e62-d6d8-4ccf-8cae-00073464a1e1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: larryfr
ms.openlocfilehash: 9dd63e1f3ec381dd99495ebc6193198611c76c88
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="deploy-and-manage-apache-storm-topologies-on-hdinsight"></a>Nasazení a správa topologií Apache Storm v HDInsight

V tomto dokumentu seznámíte se základy správy a monitorování topologií Storm spuštěných na Storm v HDInsight clustery.

> [!IMPORTANT]
> Kroky v tomto článku vyžadují Storm se systémem Linux v clusteru HDInsight. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement). 
>
> Informace o nasazení a monitorování topologií v HDInsight se systémem Windows, najdete v části [nasazení a správa topologií Apache Storm v HDInsight se systémem Windows](apache-storm-deploy-monitor-topology.md)


## <a name="prerequisites"></a>Požadavky

* **Storm v clusteru HDInsight se systémem Linux**: najdete v části [začít pracovat s Apache Storm v HDInsight](apache-storm-tutorial-get-started-linux.md) pokyny týkající se vytvoření clusteru

* (Volitelné) **Znalost SSH a SCP**: Další informace najdete v tématu [použití SSH s HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

* (Volitelné) **Visual Studio**: Azure SDK 2.5.1 nebo novější a nástrojů Data Lake pro Visual Studio. Další informace najdete v tématu [Začínáme pomocí nástrojů Data Lake pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    Jedna z následujících verzí sady Visual Studio:

  * Visual Studio 2012 s [aktualizací 4](http://www.microsoft.com/download/details.aspx?id=39305)

  * Visual Studio 2013 s [aktualizací 4](http://www.microsoft.com/download/details.aspx?id=44921) nebo [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?LinkId=517284)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/)

  * Visual Studio 2015 (všechny edice)

  * Visual Studio 2017 (všechny edice). Nástroje data Lake pro Visual Studio 2017 se instalují jako součást pracovního vytížení Azure.

## <a name="submit-a-topology-visual-studio"></a>Odeslání topologii: Visual Studio

Nástroje HDInsight slouží k odeslání jazyka C# nebo hybridní topologie pro váš cluster Storm. Následující postup použijte ukázkovou aplikaci. Informace o vytváření na použití nástrojů HDInsight naleznete v tématu [vývoj topologií C# pomocí nástrojů HDInsight pro Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

1. Pokud jste ještě nenainstalovali nejnovější verzi nástroje Data Lake pro Visual Studio, najdete v části [Začínáme pomocí nástrojů Data Lake pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]
    > Nástroje Data Lake pro Visual Studio se dřív označovaly jako nástroje HDInsight pro Visual Studio.
    >
    > Nástroje data Lake pro Visual Studio jsou součástí __pracovního vytížení Azure__ pro Visual Studio 2017.

2. Otevřete Visual Studio, vyberte **soubor** > **nový** > **projektu**.

3. V **nový projekt** dialogové okno, rozbalte seznam **nainstalovaná** > **šablony**a potom vyberte **HDInsight**. V seznamu šablon vyberte **Storm ukázka**. V dolní části dialogových oken zadejte název aplikace.

    ![Bitové kopie](./media/apache-storm-deploy-monitor-topology-linux/sample.png)

4. V **Průzkumníku řešení**, klikněte pravým tlačítkem na projekt a vyberte **odeslání do Storm v HDInsight**.

   > [!NOTE]
   > Pokud se zobrazí výzva, zadejte přihlašovací údaje pro vaše předplatné Azure. Pokud máte více než jedno předplatné, přihlaste se k ta, která obsahuje váš cluster Storm v HDInsight.

5. Vyberte váš cluster Storm v HDInsight z **Storm Cluster** rozevíracího seznamu a potom vyberte **odeslání**. Můžete sledovat, jestli je úspěšně odesílání pomocí **výstup** okno.

## <a name="submit-a-topology-ssh-and-the-storm-command"></a>Odeslání topologii: SSH a příkaz Storm

1. Použití SSH se připojit ke clusteru HDInsight. Nahraďte **uživatelské jméno** název vaší přihlašování přes SSH. Nahraďte **CLUSTERNAME** názvem clusteru HDInsight:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Další informace o používání SSH připojit ke svému clusteru HDInsight najdete v tématu [použití SSH s HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Následující příkaz použijte ke spuštění ukázkové topologie:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount

    Tento příkaz v clusteru spustí ukázkovou topologii WordCount. Tato topologie náhodně generuje věty a pak počty výskytem jednotlivých slov ve větě.

   > [!NOTE]
   > Při odesílání topologie do clusteru je nutné nejprve zkopírovat soubor jar obsahující cluster před použitím příkazu `storm`. Při kopírování souboru do clusteru, můžete použít `scp` příkaz. Například `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`.
   >
   > Příklad WordCount a další příklady starter storm jsou již zahrnuty v clusteru na `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Odeslání topologii: prostřednictvím kódu programu

Můžete nasadit prostřednictvím kódu programu topologii pomocí služby Nimbus. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) poskytuje příklad aplikaci Java, která ukazuje, jak nasadit a spustit topologii prostřednictvím služby Nimbus.

## <a name="monitor-and-manage-visual-studio"></a>Sledování a správě: Visual Studio

Při odeslání topologii pomocí sady Visual Studio **topologie Storm** zobrazení se zobrazí. Vyberte topologii ze seznamu a zobrazit informace o spuštěné topologie.

![monitorování v sadě Visual studio](./media/apache-storm-deploy-monitor-topology-linux/vsmonitor.png)

> [!NOTE]
> Můžete také zobrazit **topologie Storm** z **Průzkumníka serveru** rozšířením **Azure** > **HDInsight**a potom kliknete pravým tlačítkem cluster Storm v HDInsight a výběr **topologie Storm zobrazení**.

Vyberte tvar funkcích spouts nebo funkce bolts zobrazíte informace o těchto součástí. Otevře se nové okno pro každé vybrané položky.

### <a name="deactivate-and-reactivate"></a>Deaktivovat a znovu aktivovat

Deaktivace topologii pozastaví ho, dokud nebude ukončen nebo znovu aktivovat. Pokud chcete provést tyto operace, použijte __deaktivovat__ a __znovu aktivovat__ tlačítka v horní části __souhrn topologie__.

### <a name="rebalance"></a>Obnovit rovnováhu

Vyrovnává topologii umožňuje systému zkontrolovat, jestli paralelismus topologii. Například pokud jste změnili velikost clusteru přidat další poznámky, vyrovnává umožňuje topologii zobrazíte nové uzly.

Chcete-li znovu vyvážit topologii, použijte __znovu vyvážit__ tlačítka v horní části __souhrn topologie__.

> [!WARNING]
> Vyrovnává topologii nejprve deaktivuje topologii, pak znovu rovnoměrně distribuuje pracovních procesů v clusteru a potom nakonec vrátí topologie do stavu, ve kterém se nacházel před vyrovnává došlo k chybě. Takže pokud topologii byl aktivní, se zase aktivní. Pokud se ji deaktivovala, zůstane deaktivované.

### <a name="kill-a-topology"></a>Příkaz kill topologii

Topologie Storm pokračovat, spuštění, dokud jsou zastaveny nebo odstranění clusteru. Chcete-li zastavit topologii, použijte __Kill__ tlačítka v horní části __souhrn topologie__.

## <a name="monitor-and-manage-ssh-and-the-storm-command"></a>Sledování a správě: SSH a příkaz Storm

`storm` Nástroj umožňuje pracovat se spuštěnými topologiemi z příkazového řádku. Použití `storm -h` pro úplný seznam příkazů.

### <a name="list-topologies"></a>Seznam topologie

Použijte následující příkaz, který seznam všech spuštěnými topologiemi:

    storm list

Tento příkaz by měl vrátit informace podobné následujícímu textu:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Deaktivovat a znovu aktivovat

Deaktivace topologii pozastaví ho, dokud nebude ukončen nebo znovu aktivovat. Znovu aktivovat a deaktivovat použijte následující příkaz:

    storm Deactivate TOPOLOGYNAME

    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>Příkaz kill spuštěné topologie

Storm topologie, jednou spustit, pokračovat spuštění, dokud se zastavila. Chcete-li zastavit topologii, použijte následující příkaz:

    storm kill TOPOLOGYNAME

### <a name="rebalance"></a>Obnovit rovnováhu

Vyrovnává topologii umožňuje systému zkontrolovat, jestli paralelismus topologii. Například pokud jste změnili velikost clusteru přidat další poznámky, vyrovnává umožňuje topologii zobrazíte nové uzly.

> [!WARNING]
> Vyrovnává topologii nejprve deaktivuje topologii, pak znovu rovnoměrně distribuuje pracovních procesů v clusteru a potom nakonec vrátí topologie do stavu, ve kterém se nacházel před vyrovnává došlo k chybě. Takže pokud topologii byl aktivní, se zase aktivní. Pokud se ji deaktivovala, zůstane deaktivované.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-storm-ui"></a>Sledování a správě: Storm uživatelského rozhraní

Uživatelské rozhraní Storm poskytuje webové rozhraní pro práci se spuštěnými topologiemi a je součástí clusteru HDInsight. Chcete-li zobrazit uživatelské rozhraní Storm, otevřete pomocí webového prohlížeče **https://CLUSTERNAME.azurehdinsight.net/stormui**, kde **CLUSTERNAME** je název clusteru.

> [!NOTE]
> Pokud budete vyzváni k zadání uživatelského jména a hesla, zadejte správce clusteru (admin) a heslo použité při vytvoření clusteru.

### <a name="main-page"></a>Hlavní stránka

Hlavní stránka uživatelského rozhraní Storm poskytuje následující informace:

* **Souhrn clusteru**: základní informace o clusteru Storm.
* **Souhrn topologie**: seznam spuštěných topologií. Chcete-li zobrazit další informace o konkrétní topologie pomocí odkazů v této části.
* **Souhrn nadřízeného**: informace o nadřízeného Storm.
* **Konfigurace nimbus**: Nimbus konfiguraci pro daný cluster.

### <a name="topology-summary"></a>Souhrn topologie

Výběr odkazu z **souhrn topologie** části zobrazí následující informace o topologii:

* **Souhrn topologie**: základní informace o topologii.
* **Topologie akce**: akce správy, které můžete provést pro topologii.

  * **Aktivovat**: obnoví zpracování deaktivované topologie.
  * **Deaktivovat**: Pozastaví spuštěné topologie.
  * **Znovu vyvážit**: upraví paralelismus topologii. Po změně počtu uzlů v clusteru musíte znovu vyvážit spuštěné topologie. Tato operace umožňuje topologii upravovat paralelismus za účelem kompenzace pro vyšší nebo ke snížení počtu uzlů v clusteru.

    Další informace naleznete v části <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Pochopení paralelismu topologie Storm</a>.
  * **Příkaz kill**: ukončí topologii Storm po zadaný časový limit.
* **Statistiky topologie**: Statistika týkající se topologie. Pokud chcete nastavit časový rámec pro zbývající záznamy na stránce, použijte odkazy v **okno** sloupce.
* **Spouts**: funkcích spouts používané topologii. Chcete-li zobrazit další informace o konkrétních funkcích spouts pomocí odkazů v této části.
* **Bolts**: funkce bolts používané topologii. Použijte odkazy v této části zobrazíte další informace o konkrétních funkcích bolts.
* **Topologie konfigurace**: konfiguraci vybrané topologie.

### <a name="spout-and-bolt-summary"></a>Souhrn funkcí Bolt a spout

Výběr funkcí spout z **Spouts** nebo **Bolts** části zobrazí následující informace o vybrané položce:

* **Souhrn součást**: základní informace o funkcích spout nebo bolt.
* **Statistiky funkcí spout/Bolt**: statistiky o funkcích spout nebo bolt. Pokud chcete nastavit časový rámec pro zbývající záznamy na stránce, použijte odkazy v **okno** sloupce.
* **Statistiky vstupu** (pouze funkce bolt): informace o vstupní datové proudy využívaná funkcí bolt.
* **Statististiky výstupu**: informace o datové proudy vysílaných funkcích spout nebo bolt.
* **Vykonavatelů**: informace o funkcích spout nebo bolt instance. Vyberte **Port** vytváří záznam pro konkrétní vykonavatele chcete zobrazit protokol diagnostické informace pro tuto instanci.
* **Chyby**: všechny informace o funkcích spout nebo bolt chybách.

## <a name="monitor-and-manage-rest-api"></a>Sledování a správě: REST API

Uživatelské rozhraní Storm je postavený na rozhraní REST API, takže můžete provádět podobné správy a monitorování funkce pomocí rozhraní REST API. Rozhraní REST API můžete použít k vytvoření vlastních nástrojů pro správu a monitorování topologie Storm.

Další informace najdete v tématu [Storm uživatelského rozhraní REST API](http://storm.apache.org/releases/0.9.6/STORM-UI-REST-API.html). Tyto informace je specifická pro Apache Storm v HDInsight pomocí rozhraní REST API.

> [!IMPORTANT]
> Rozhraní API REST Storm není veřejně dostupné přes internet a musí být přístup pomocí tunelového propojení SSH k hlavnímu uzlu clusteru HDInsight. Informace o vytváření a používání tunelového propojení SSH naleznete v tématu [používání tunelového propojení SSH pro přístup k webovému uživatelskému rozhraní Ambari, ResourceManager, JobHistory, NameNode, Oozie a jiným webovým uživatelská rozhraní](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>Základní identifikátor URI

Základní identifikátor URI pro rozhraní API REST v clusterech HDInsight se systémem Linux je dostupné z hlavního uzlu v **https://HEADNODEFQDN:8744/api/v1/**. Název domény hlavního uzlu se vygeneruje při vytváření clusteru a nejsou statické.

Plně kvalifikovaný název domény (FQDN) pro hlavního uzlu clusteru najdete v několika různými způsoby:

* **Z relace SSH**: použijte příkaz `headnode -f` z relace SSH do clusteru.
* **Z webové Ambari**: vyberte **služby** z horní části stránky, pak vyberte **Storm**. Z **Souhrn** vyberte **serveru uživatelského rozhraní Storm**. Plně kvalifikovaný název domény uzlu, který je hostitelem uživatelské rozhraní Storm a REST API se zobrazí v horní části stránky.
* **Z rozhraní Ambari REST API**: použijte příkaz `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` k načtení informací o uzlu, který na běží uživatelské rozhraní Storm a REST API. Nahraďte **CLUSTERNAME** se název clusteru. Po zobrazení výzvy zadejte heslo pro účet pro přihlášení (správce). V odpovědi na položku "název_hostitele" obsahuje plně kvalifikovaný název domény uzlu.

### <a name="authentication"></a>Authentication

Požadavky na rozhraní REST API musí používat **základní ověřování**, takže můžete použít název správce clusteru HDInsight a heslo.

> [!NOTE]
> Protože základní ověřování je odeslána pomocí nešifrovaného textu, měli byste **vždy** používat protokol HTTPS pro zabezpečenou komunikaci s clusterem.

### <a name="return-values"></a>Návratové hodnoty

Informace, které se vrátí z rozhraní API REST může být pouze z použitelné v rámci clusteru. Například plně kvalifikovaný název domény (FQDN) pro servery Zookeeper vrátit není přístupný z Internetu.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak [založené na jazyce Java vyvíjet topologie pomocí nástroje Maven](apache-storm-develop-java-topology.md).

Seznam Další příklad topologií najdete v tématu [příklad topologií pro Storm v HDInsight](apache-storm-example-topology.md).
