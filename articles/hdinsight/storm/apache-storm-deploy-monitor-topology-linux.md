---
title: Nasazení a správa topologie Apache Storm na Azure HDInsight
description: Naučte se nasazovat, monitorovat a spravovat topologie Apache Storm pomocí řídicího panelu Storm na Linuxu hdinsightu. Použití hadoop nástroje pro Visual Studio.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: e890289230b3215bd102d8c5a78dca4f1b7b90f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271899"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Nasazení a správa topologie Apache Storm na Azure HDInsight

V tomto dokumentu se dozvíte základy správy a monitorování topologií [Apache Storm](https://storm.apache.org/) spuštěných na stormu na clusterech HDInsight.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Storm na HDInsight. Viz [Vytvoření clusterů Apache Hadoop pomocí portálu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) a vyberte **Storm** pro **typ clusteru**.

* (Nepovinné) Znalost zabezpečeného prostředí (SSH) a zabezpečené kopie (SCP). Další informace naleznete [v tématu Připojení k HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* (Nepovinné) Visual Studio, Azure SDK 2.5.1 nebo novější a nástroje data lake pro Visual Studio. Další informace naleznete [v tématu Apache Hadoop & Visual Studio Data Lake Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="submit-a-topology-using-visual-studio"></a>Odeslání topologie pomocí sady Visual Studio

Pomocí nástrojů Data Lake Tools pro Visual Studio můžete odeslat C# nebo hybridní topologie do clusteru Storm. Následující kroky používají ukázkovou aplikaci. Informace o vytváření topologie pomocí nástrojů data lake naleznete v tématu [Apache Storm topologie s Visual Studio a C#](apache-storm-develop-csharp-visual-studio-topology.md).

1. Pokud jste ještě nenainstalovali nejnovější verzi nástrojů Data Lake pro Visual Studio, přečtěte si část [Použití nástrojů data lake pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > Nástroje Azure Data Lake a Stream Analytics se dříve nazývaly nástroje HDInsight pro Visual Studio.
    >
    > Nástroje Azure Data Lake a Stream Analytics pro Visual Studio jsou součástí vývojové úlohy **Azure** pro Visual Studio 2019.

1. Spusťte Visual Studio.

1. V okně **Start** vyberte **Vytvořit nový projekt**.

1. V okně **Vytvořit nový projekt** vyberte vyhledávací `Storm`pole a zadejte . Pak ze seznamu výsledků zvolte **Storm Sample** a vyberte **Další**.

1. V okně **Konfigurovat nový projekt** zadejte název **projektu**a přejděte do nebo vytvořte **umístění** pro uložení nového projektu. Pak vyberte **Vytvořit**.

    ![Konfigurace nového okna projektu, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

1. Z **Průzkumníka serveru**klikněte pravým tlačítkem na **Azure** a vyberte Připojit **k předplatnému Microsoft Azure...** a dokončete proces přihlášení.

1. V **Průzkumníku řešení**klikněte pravým tlačítkem myši na projekt a **v HDInsightu**zvolte Odeslat do bouře .

    > [!NOTE]  
    > Pokud se zobrazí výzva, zadejte přihlašovací údaje pro vaše předplatné Azure. Pokud máte více než jedno předplatné, přihlaste se k tomu, které obsahuje váš cluster Storm v HDInsight.

1. V dialogovém okně **Odeslat topologii** vyberte v rozevíracím seznamu **Storm Cluster** bouři v clusteru HDInsight a pak vyberte **Odeslat**. Zobrazením podokna **Výstup** můžete sledovat, zda je odeslání úspěšné.

## <a name="submit-a-topology-using-ssh-and-the-storm-command"></a>Odeslání topologie pomocí ssh a příkazu Storm

1. Pomocí [příkazu ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) se připojte ke clusteru. Upravte níže uvedený příkaz nahrazením názvu clusteru názvem clusteru a zadejte příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. V relaci ssh spusťte topologii příkladu **WordCount** pomocí následujícího příkazu:

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount
    ```

    Tento příkaz v clusteru spustí ukázkovou topologii WordCount. Tato topologie náhodně generuje věty a poté spočítá výskyt každého slova ve větách.

    > [!NOTE]  
    > Při odesílání topologie do clusteru je třeba před použitím příkazu `storm` nejprve zkopírovat soubor JAR obsahující cluster. Chcete-li soubor zkopírovat do clusteru, `scp` můžete použít příkaz. Zadejte například `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`.
    >
    > Příklad *WordCount* a další příklady začátku bouře jsou `/usr/hdp/current/storm-client/contrib/storm-starter/`již zahrnuty ve vašem clusteru na .

## <a name="submit-a-topology-programmatically"></a>Odeslání topologie programově

Topologii můžete programově nasadit pomocí služby Nimbus. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology)Poskytuje příklad java aplikace, která ukazuje, jak nasadit a spustit topologii prostřednictvím služby Nimbus.

## <a name="monitor-and-manage-a-topology-in-visual-studio"></a>Sledování a správa topologie v sadě Visual Studio

Při odeslání topologie pomocí sady Visual Studio se zobrazí okno **Zobrazení topologií bouře.** Výběrem topologie ze seznamu zobrazíte informace o spuštěné topologii.

![Topologie monitoru, okno Zobrazení topologií Bouře, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> **Topologií bouře** můžete také zobrazit z **Průzkumníka serveru**. Rozbalte **Azure** > **HDInsight**, klikněte pravým tlačítkem myši na storm v clusteru HDInsight a pak vyberte **Zobrazit topologii bouře**.

Vyberte tvar výtoku nebo šroubů, chcete-li zobrazit informace o těchto součástech. Zobrazí se popiss s informacemi o součásti pro vybranou položku.

### <a name="deactivate-and-reactivate-a-topology"></a>Deaktivace a opětovná aktivace topologie

Deaktivace topologie ji pozastaví, dokud nebude topologie zabita nebo znovu aktivována. Chcete-li tyto operace provést, použijte tlačítka **Deaktivovat** a **znovu aktivovat** v oblasti **Akce** v horní části okna **Zobrazení topologií bouře.**

### <a name="rebalance-a-topology"></a>Vyvážení topologie

Vyvážení topologie umožňuje systému revidovat paralelismus topologie. Pokud jste například velikost clusteru přidali další poznámky, vyvažování umožňuje topologii zobrazit nové uzly.

Chcete-li znovu vyvážit topologii, použijte tlačítko **Znovu vyvážit** v oblasti **Akce** v okně **Zobrazení topologií bouře.**

> [!WARNING]  
> Vyvážení topologie deaktivuje topologii, rovnoměrně přerozdělí pracovníky v rámci clusteru a potom vrátí topologii do stavu, ve kterého byla předtím, než došlo k vyvažování. Pokud byla topologie aktivní, znovu se aktivuje. Pokud byla topologie deaktivována, zůstane deaktivována.

### <a name="kill-a-running-topology"></a>Zabít běžící topologii

Topologií bouře pokračuje v běhu, dokud nejsou zastaveny nebo cluster uodstranění. Chcete-li zastavit topologii, použijte tlačítko **Zabít** v oblasti **Akce.**

## <a name="monitor-and-manage-a-topology-using-ssh-and-the-storm-command"></a>Sledování a správa topologie pomocí SSH a příkazu Storm

Nástroj `storm` umožňuje pracovat s běžící topologie z příkazového řádku. Slouží `storm -h` k úplnému seznamu příkazů.

### <a name="list-topologies"></a>Seznam topologie

Pomocí následujícího příkazu zobrazíte všechny spuštěné topologie:

```shell
storm list
```

Tento příkaz by měl vrátit informace podobné následujícímu textu:

```shell
Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
-------------------------------------------------------------------
WordCount            ACTIVE     29         2            263
```

### <a name="deactivate-and-reactivate-a-topology"></a>Deaktivace a opětovná aktivace topologie

Deaktivace topologie ji pozastaví, dokud nebude topologie zabita nebo znovu aktivována. K deaktivaci nebo opětovné aktivaci použijte následující příkazy:

```shell
storm Deactivate TOPOLOGYNAME
```

```shell
storm Activate TOPOLOGYNAME
```

### <a name="kill-a-running-topology"></a>Zabít běžící topologii

Bouřkové topologie, jakmile začal, pokračovat v běhu, dokud se zastavil. Chcete-li topologii zastavit, použijte následující příkaz:

```shell
storm kill TOPOLOGYNAME
```

### <a name="rebalance-a-topology"></a>Vyvážení topologie

Vyvážení topologie umožňuje systému revidovat paralelismus topologie. Pokud jste například velikost clusteru přidali další poznámky, vyvažování umožňuje topologii zobrazit nové uzly.

> [!WARNING]  
> Vyvážení topologie deaktivuje topologii, rovnoměrně přerozdělí pracovníky v rámci clusteru a potom vrátí topologii do stavu, ve kterého byla předtím, než došlo k vyvažování. Pokud byla topologie aktivní, znovu se aktivuje. Pokud byla deaktivována, zůstane deaktivována.

```shell
storm rebalance TOPOLOGYNAME
```

## <a name="monitor-and-manage-a-topology-using-the-storm-ui"></a>Sledování a správa topologie pomocí ui. bouře

Uživatelské rozhraní Storm poskytuje webové rozhraní pro práci s spuštěnými topologiemi a je součástí clusteru HDInsight. Chcete-li zobrazit uživatelské uživatelské prostředí stormu, otevřete pomocí webového prohlížeče `https://CLUSTERNAME.azurehdinsight.net/stormui`, kde cluster *CLUSTERNAME* je název clusteru.

> [!NOTE]  
> Pokud budete vyzváni k zadání uživatelského jména a hesla, zadejte uživatelské jméno a heslo správce clusteru, které jste použili při vytváření clusteru.

### <a name="storm-ui-main-page"></a>Hlavní stránka storm ui

Hlavní stránka storm ui poskytuje následující informace:

| Sekce | Popis |
| --- | --- |
| Souhrn clusteru| Základní informace o clusteru Storm. |
| Nimbus shrnutí | Seznam základních informací Nimbus. |
| Souhrn topologie | Seznam běžících topologie. Chcete-li zobrazit další informace o konkrétní topologii, vyberte její odkaz ve sloupci **Název.** |
| Shrnutí nadřízených | Informace o nadřízeném Stormu. Chcete-li zobrazit prostředky pracovníka přidružené k určitému nadřízenému, vyberte jeho odkaz ve sloupci **Hostitel** nebo **ID.** |
| Konfigurace Nimbusu | Konfigurace Nimbusu pro cluster. |

Hlavní stránka uživatelského uživatelského terminálu Storm vypadá podobně jako tato webová stránka:

![Hlavní stránka, Storm UI, Apache Storm topologie, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-main-page.png)

#### <a name="topology-summary"></a>Souhrn topologie

Výběrodkazu ze souhrnné části **Topologie** zobrazí následující informace o topologii:

| Sekce | Popis |
| --- | --- |
| Souhrn topologie | Základní informace o topologii. |
| Akce topologie| Akce správy, které můžete udělat pro topologii. Dostupné akce jsou popsány dále v této části. |
| Topologie statistiky | Statistiky o topologii. Chcete-li nastavit časový rámec pro položku v této části, vyberte její odkaz ve sloupci **Okno.** |
| Výtoky *(časový rámec)* | Výtoky používané topologie. Chcete-li zobrazit další informace o určitém výtoku, vyberte její odkaz ve sloupci **ID.** |
| Šrouby *(časový rámec)* | Šrouby používané topologií. Chcete-li zobrazit další informace o konkrétním šroubu, vyberte jeho odkaz ve sloupci **Id.** |
| Zdroje pracovníka | Seznam prostředků pracovníka. Chcete-li zobrazit další informace o konkrétním pracovním prostředku, vyberte jeho odkaz ve sloupci **Hostitel.** |
| Vizualizace topologie | Tlačítko **Zobrazit vizualizaci,** které zobrazuje vizualizaci topologie. |
| Konfigurace topologie | Konfigurace vybrané topologie. |

Souhrnná stránka topologie bouře vypadá podobně jako tato webová stránka:

![Stránka souhrnu topologie, Storm UI, Apache Storm, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-topology-summary.png)

V části **Akce topologie** můžete vybrat následující tlačítka pro provedení akce:

| Tlačítko | Popis |
| --- | --- |
| Aktivovat | Obnoví zpracování deaktivované topologie. |
| Deaktivovat | Pozastaví spuštěnou topologii. |
| Přehodnotit | Upravuje paralelismus topologie. Měli byste znovu vyvážit spuštěné topologie po změně počtu uzlů v clusteru. Tato operace umožňuje topologii upravit paralelismus kompenzovat další nebo snížený počet uzlů v clusteru.<br/><br/>Další informace naleznete <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">v tématu Principy paralelismu topologie Apache Storm</a>.
| Zabít | Ukončí topologii bouře po zadaném časovém udůsledku. |
| Ladění | Zahájí relaci ladění pro spuštěnou topologii. |
| Zastavit ladění | Ukončí relaci ladění pro spuštěnou topologii. |
| Změnit úroveň protokolu | Upraví úroveň protokolu ladění. |

##### <a name="spout-and-bolt-summary"></a>Výtok a shrnutí šroubů

Výběr výtoku z **oddílů Výtoky** nebo **Šrouby** zobrazí následující informace o vybrané položce:

| Sekce | Popis |
| --- | --- |
| Souhrn součásti | Základní informace o výtoku nebo šroubu. |
| Akce komponent | **Ladění** a **zastavit ladění** tlačítka. |
| Statistiky výtoku nebo šrouby | Statistiky o výtoku nebo šroubu. Chcete-li nastavit časový rámec pro položku v této části, vyberte její odkaz ve sloupci **Okno.** |
| (Pouze šroub)<br/>Vstupní statistiky *(časový rámec)* | Informace o vstupních proudech spotřebovaných šroubem. |
| Výstupní statistiky *(časový rámec)* | Informace o proudech vyzařovaných výtokem nebo šroubem. |
| Profilování a ladění | Ovládací prvky pro profilování a ladění součástí na této stránce. Můžete nastavit hodnotu **Stav / Časový čas (Minuty)** a můžete vybrat tlačítka pro **JStack**, **Restart worker**a **haldy**. |
| Exekutoři *(časový rámec)* | Informace o instancích výtoku nebo šroubu. Chcete-li zobrazit protokol diagnostických informací vytvořených pro tuto instanci, vyberte položku **portu** pro konkrétního vykonavatele. Můžete také zobrazit prostředky pracovníka přidružené k určitému prováděcímu modulu výběrem jeho odkazu ve sloupci **Hostitel.** |
| chyby | Veškeré chybové informace o výtoku nebo šroubu. |

Souhrnná stránka Storm bolt vypadá podobně jako tato webová stránka:

![Souhrnná stránka šroubů, Storm UI, Apache Storm, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-bolt-summary.png)

## <a name="monitor-and-manage-the-topology-using-the-rest-api"></a>Sledování a správa topologie pomocí rozhraní REST API

Storm ui je postaven na rozhraní REST API, takže můžete provést podobné úlohy správy a monitorování pomocí rozhraní REST API. Rozhraní REST API můžete použít k vytvoření vlastních nástrojů pro správu a monitorování topologií Storm.

Další informace naleznete v [tématu Apache Storm UI REST API](https://storm.apache.org/releases/current/STORM-UI-REST-API.html). Následující informace jsou specifické pro použití rozhraní REST API s Apache Storm na HDInsight.

> [!IMPORTANT]  
> Rozhraní Storm REST API není veřejně dostupné přes internet. Je nutné přistupovat pomocí tunelu SSH do hlavního uzlu clusteru HDInsight. Informace o vytváření a používání tunelového propojení SSH najdete [v tématu Použití tunelového propojení SSH pro přístup k Azure HDInsight](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>Základní identifikátor URI

Základní identifikátor URI pro rozhraní REST API v clusterech HDInsight založených na Linuxu je k dispozici na adrese `https://HEADNODEFQDN:8744/api/v1/`URL , kde nahradíte *HEADNODEFQDN* hlavním uzlem. Název domény hlavního uzlu je generován během vytváření clusteru a není statický.

Plně kvalifikovaný název domény (FQDN) pro hlavní uzel clusteru najdete několika způsoby:

| Metoda zjišťování fQDN | Popis |
| --- | --- |
| Relace SSH | Použijte příkaz `headnode -f` z relace SSH do clusteru. |
| Ambari Web | Na webové stránce clusteru Ambari (`https://CLUSTERNAME.azurehdinsight.net`) vyberte **služby** v horní části stránky a pak vyberte **Storm**. Na kartě **Souhrn** vyberte **Storm UI Server**. V horní části stránky se zobrazí hlavní přístupkový soubor uzlu, který hostuje rozhraní Storm A rozhraní REST API. |
| Ambari REST API | Pomocí příkazu `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` načtěte informace o uzlu, na který jsou spuštěny rozhraní Storm A rozhraní REST API. Nahraďte dvě instance *clusteru CLUSTERNAME* názvem clusteru. Po zobrazení výzvy zadejte heslo pro účet uživatele (správce). V odpovědi položka "host_name" výstupu JSON obsahuje vícenežvitný kód uzlu. |

### <a name="authentication"></a>Ověřování

Požadavky na rozhraní REST API musí používat *základní ověřování*, takže je nutné použít název správce a heslo pro cluster HDInsight.

> [!NOTE]  
> Vzhledem k tomu, že základní ověřování je odesíláno pomocí prostého textu, měli byste *vždy* používat protokol HTTPS k zabezpečení komunikace s clusterem.

### <a name="return-values"></a>Vrácené hodnoty

Informace, které jsou vráceny z rozhraní REST API může být použitelné pouze z v rámci clusteru. Například plně kvalifikovaný název domény (FQDN) vrácený pro servery [Apache ZooKeeper](https://zookeeper.apache.org/) není přístupný z internetu.

## <a name="next-steps"></a>Další kroky

Naučte se [vyvíjet topologii založenou na Javě pomocí Apache Maven](apache-storm-develop-java-topology.md).

Seznam dalších příkladů topologie najdete [v tématu Příklad topologie Apache Storm v Azure HDInsight](apache-storm-example-topology.md).
