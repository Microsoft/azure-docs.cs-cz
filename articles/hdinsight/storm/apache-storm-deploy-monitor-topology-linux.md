---
title: Nasazení a Správa topologií Apache Storm v Azure HDInsight
description: Naučte se nasazovat, monitorovat a spravovat Apache Storm topologie pomocí řídicího panelu pro zaplavování v HDInsight se systémem Linux. Použijte nástroje Hadoop pro Visual Studio.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: 417819cad3bc2ee258381426dfcee7c800b69d42
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929168"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Nasazení a Správa topologií Apache Storm v Azure HDInsight

V tomto dokumentu se naučíte základy správy a monitorování [Apache Stormch](https://storm.apache.org/) topologií běžících na clusterech HDInsight.

## <a name="prerequisites"></a>Předpoklady

* Cluster Apache Storm v HDInsight. Přečtěte si téma [vytvoření Apache Hadoop clusterů pomocí Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) **a výběr funkce** pro **typ clusteru**.

* Volitelné Znalost Secure Shell (SSH) a zabezpečeného kopírování (SCP). Další informace najdete v tématu [Připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Volitelné Visual Studio, Azure SDK 2.5.1 nebo novější a nástroje pro Data Lake pro Visual Studio. Další informace naleznete v tématu [Apache Hadoop & Visual Studio Data Lake Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="submit-a-topology-using-visual-studio"></a>Odeslání topologie pomocí sady Visual Studio

Pomocí nástrojů Data Lake pro Visual Studio můžete odesílat do clusteru s více jazyky C# nebo hybridní topologie. Následující kroky používají ukázkovou aplikaci. Informace o vytvoření topologie pomocí nástrojů Data Lake naleznete v tématu [Apache Storm topologiís se sadou Visual Studio a jazykem C#](apache-storm-develop-csharp-visual-studio-topology.md).

1. Pokud jste ještě nenainstalovali nejnovější verzi nástroje Data Lake Tools for Visual Studio, přečtěte si téma [použití nástrojů Data Lake pro sadu Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > Nástroje Azure Data Lake a Stream Analytics se dřív nazývaly nástroje HDInsight pro Visual Studio.
    >
    > Azure Data Lake a Stream Analytics nástroje pro Visual Studio jsou součástí úlohy **vývoje Azure** pro visual Studio 2019.

1. Spusťte Visual Studio.

1. V okně **Start** vyberte **vytvořit nový projekt**.

1. V okně **vytvořit nový projekt** vyberte vyhledávací pole a zadejte `Storm` . Pak ze seznamu výsledek zvolte možnost převést **vzorek** a vyberte **Další**.

1. V okně **Konfigurovat nový projekt** zadejte **název projektu** a přejděte na nebo vytvořte **umístění** , do kterého chcete nový projekt uložit. Potom vyberte **Vytvořit**.

    ![Konfigurace nového okna projektu, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

1. Z **Průzkumník serveru** klikněte pravým tlačítkem myši na **Azure** a vyberte **připojit se k Microsoft Azure předplatného...** a dokončete proces přihlašování.

1. Z **Průzkumník řešení** klikněte pravým tlačítkem myši na projekt a vyberte **Odeslat pro** zaplavení v HDInsight.

    > [!NOTE]  
    > Pokud se zobrazí výzva, zadejte přihlašovací údaje pro vaše předplatné Azure. Pokud máte více než jedno předplatné, přihlaste se k počítači, který obsahuje vaše navýšení na clusteru HDInsight.

1. V dialogovém okně **Odeslat topologii** v rozevíracím seznamu cluster nenáročného **clusteru** zvolte své zaplavení na clusteru HDInsight a pak vyberte **Odeslat**. Můžete sledovat, zda je odeslání úspěšné, zobrazením podokna **výstup** .

## <a name="submit-a-topology-using-ssh-and-the-storm-command"></a>Odeslání topologie pomocí SSH a příkazu pro zaplavení

1. Připojte se ke clusteru pomocí [příkazu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) . Níže uvedený příkaz upravte tak, že ho nahradíte názvem clusteru a pak zadáte tento příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Z relace SSH pomocí následujícího příkazu spusťte **WORDCOUNT** příklad topologie:

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount
    ```

    Tento příkaz v clusteru spustí ukázkovou topologii WordCount. Tato topologie náhodně generuje věty a pak počítá výskyt každého slova ve větách.

    > [!NOTE]  
    > Při odesílání topologie do clusteru je nutné nejprve zkopírovat soubor. jar obsahující cluster před použitím `storm` příkazu. K zkopírování souboru do clusteru můžete použít `scp` příkaz. Zadejte například `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`.
    >
    > Příklad *WORDCOUNT* a další příklady pro počáteční funkce s více podsystému jsou již v clusteru součástí `/usr/hdp/current/storm-client/contrib/storm-starter/` .

## <a name="submit-a-topology-programmatically"></a>Programové odeslání topologie

Topologii můžete programově nasadit pomocí služby Nimbus. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) poskytuje ukázkovou aplikaci v jazyce Java, která ukazuje, jak nasadit a spustit topologii prostřednictvím služby Nimbus.

## <a name="monitor-and-manage-a-topology-in-visual-studio"></a>Monitorování a Správa topologie v aplikaci Visual Studio

Při odesílání topologie pomocí sady Visual Studio se zobrazí okno **zobrazení topologií** s více podmnožinami. Vyberte topologii ze seznamu a zobrazte informace o spuštěné topologii.

![Monitorování topologie, okna zobrazení topologií s více topologiemi, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> **Topologie** **přePrůzkumník serveru** můžete zobrazit také z. Rozbalte **Azure**  >  **HDInsight**, klikněte pravým tlačítkem na cluster HDInsight a pak vyberte **Zobrazit topologie** přeplavování.

Vyberte tvar pro spoutů nebo šrouby pro zobrazení informací o těchto součástech. Pro vybranou položku se zobrazí popisek s informacemi o komponentě.

### <a name="deactivate-and-reactivate-a-topology"></a>Deaktivace a opětovná aktivace topologie

Deaktivace topologie ji pozastaví, dokud se topologie neukončila nebo znovu neaktivuje. Chcete-li provést tyto operace, použijte tlačítka **dezaktivovat** a **znovu aktivovat** v oblasti **Akce** v horní části okna **zobrazení topologie** navýšení.

### <a name="rebalance-a-topology"></a>Vyvážení topologie

Nové vyrovnávání topologie umožňuje systému revidovat paralelismus topologie. Pokud jste například změnili velikost clusteru tak, aby bylo možné přidat další poznámky, umožňuje nové vyrovnávání topologie zobrazení nových uzlů.

K vyrovnání topologie použijte tlačítko pro **Vyrovnávání** zatížení v oblasti **Akce** v okně **zobrazení topologií** navýšení topologie.

> [!WARNING]  
> Při opětovném vyrovnávání topologie se topologie deaktivuje, rozšíří se i v clusteru rovnoměrně a pak se vrátí topologie do stavu, ve kterém se nacházelo, než se vyvážení stalo. Pokud byla topologie aktivní, bude znovu aktivní. Pokud byla topologie deaktivována, zůstane deaktivována.

### <a name="kill-a-running-topology"></a>Ukončí spuštěnou topologii.

Topologie zaplavování pokračují v běhu, dokud se nezastaví nebo dokud se cluster neodstraní. K zastavení topologie použijte tlačítko **Kill** v oblasti **Actions (akce** ).

## <a name="monitor-and-manage-a-topology-using-ssh-and-the-storm-command"></a>Monitorování a Správa topologie pomocí SSH a příkazu pro zaplavení

`storm`Nástroj umožňuje pracovat s běžícími topologiemi z příkazového řádku. Použijte `storm -h` pro úplný seznam příkazů.

### <a name="list-topologies"></a>Seznam topologií

K vypsání všech spuštěných topologií použijte následující příkaz:

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

Deaktivace topologie ji pozastaví, dokud se topologie neukončila nebo znovu neaktivuje. K deaktivaci nebo opětovné aktivaci použijte následující příkazy:

```shell
storm Deactivate TOPOLOGYNAME
```

```shell
storm Activate TOPOLOGYNAME
```

### <a name="kill-a-running-topology"></a>Ukončí spuštěnou topologii.

Po spuštění budou topologie s více podsystémy spuštěny, dokud se zastaví. K zastavení topologie použijte následující příkaz:

```shell
storm kill TOPOLOGYNAME
```

### <a name="rebalance-a-topology"></a>Vyvážení topologie

Nové vyrovnávání topologie umožňuje systému revidovat paralelismus topologie. Pokud jste například změnili velikost clusteru tak, aby bylo možné přidat další poznámky, umožňuje nové vyrovnávání topologie zobrazení nových uzlů.

> [!WARNING]  
> Při opětovném vyrovnávání topologie se topologie deaktivuje, rozšíří se i v clusteru rovnoměrně a pak se vrátí topologie do stavu, ve kterém se nacházelo, než se vyvážení stalo. Pokud byla topologie aktivní, bude znovu aktivní. Pokud byla deaktivována, zůstane deaktivována.

```shell
storm rebalance TOPOLOGYNAME
```

## <a name="monitor-and-manage-a-topology-using-the-storm-ui"></a>Monitorování a Správa topologie pomocí uživatelského rozhraní systému využití

Uživatelské rozhraní funkce pro zaplavení poskytuje webové rozhraní pro práci s běžícími topologiemi a je zahrnuté do vašeho clusteru HDInsight. Chcete-li zobrazit uživatelské rozhraní pro vyplavení, použijte webový prohlížeč, který otevřete `https://CLUSTERNAME.azurehdinsight.net/stormui` , kde *název_clusteru* je název vašeho clusteru.

> [!NOTE]  
> Pokud budete požádáni o zadání uživatelského jména a hesla, zadejte uživatelské jméno a heslo správce clusteru, které jste použili při vytváření clusteru.

### <a name="storm-ui-main-page"></a>Hlavní stránka uživatelského rozhraní

Hlavní stránka uživatelského rozhraní pro zaplavení poskytuje následující informace:

| Sekce | Description |
| --- | --- |
| Souhrn clusteru| Základní informace o clusteru zaplavení. |
| Nimbus souhrn | Seznam základních informací Nimbus. |
| Souhrn topologie | Seznam spuštěných topologií. Chcete-li zobrazit další informace o konkrétní topologii, vyberte její odkaz ve sloupci **název** . |
| Souhrn vedoucího | Informace o nadřízeném správce. Chcete-li zobrazit prostředky pracovních procesů přidružené k určitému vedoucímu, vyberte její odkaz ve sloupci **hostitel** nebo **ID** . |
| Konfigurace Nimbus | Konfigurace Nimbus pro cluster |

Hlavní stránka uživatelského rozhraní se bude podobat této webové stránce:

![Hlavní stránka, uživatelské rozhraní, Apache Storm topologie, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-main-page.png)

#### <a name="topology-summary"></a>Souhrn topologie

Výběrem odkazu v části **Souhrn topologie** se zobrazí následující informace o topologii:

| Sekce | Description |
| --- | --- |
| Souhrn topologie | Základní informace o topologii. |
| Akce topologie| Akce správy, které lze provést pro topologii. Dostupné akce jsou popsány dále v této části. |
| Statistiky topologie | Statistika topologie. Chcete-li nastavit časový rámec pro položku v této části, vyberte její odkaz ve sloupci **okno** . |
| Spoutů *(časový rámec)* | Spoutů používaná topologií. Pokud chcete zobrazit další informace o konkrétním Spout, vyberte jeho odkaz ve sloupci **ID** . |
| Šrouby *(časový rámec)* | Šrouby, kterou používá topologie. Pokud chcete zobrazit další informace o konkrétním poli, vyberte jeho odkaz ve sloupci **ID** . |
| Prostředky pracovního procesu | Seznam prostředků pracovních procesů. Pokud chcete zobrazit další informace o konkrétním prostředku pracovního procesu, vyberte jeho odkaz ve sloupci **hostitel** . |
| Vizualizace topologie | Tlačítko **Zobrazit vizualizaci** , které zobrazuje vizualizaci topologie. |
| Konfigurace topologie | Konfigurace vybrané topologie. |

Stránka Souhrn topologie s více podsítěmi vypadá podobně jako tato webová stránka:

![Stránka souhrnu topologie, uživatelské rozhraní pro vyplavení, Apache Storm, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-topology-summary.png)

V části **Akce topologie** můžete vybrat následující tlačítka k provedení akce:

| Tlačítko | Description |
| --- | --- |
| Aktivovat | Obnoví zpracování deaktivované topologie. |
| Deaktivovat | Pozastaví běžící topologii. |
| Obnovení rovnováhy | Upravuje paralelismus topologie. Po změně počtu uzlů v clusteru byste měli znovu vyrovnat běžící topologie. Tato operace umožňuje, aby topologie upravila paralelismus, aby vyrovnala dodatečné nebo omezené množství uzlů v clusteru.<br/><br/>Další informace najdete v tématu <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Princip paralelismu Apache Storm topologie</a>.
| Kill | Ukončí topologii nečinnosti po zadaném časovém limitu. |
| Ladění | Spustí ladicí relaci pro spuštěnou topologii. |
| Zastavit ladění | Ukončí relaci ladění pro spuštěnou topologii. |
| Změnit úroveň protokolu | Upraví úroveň protokolu ladění. |

##### <a name="spout-and-bolt-summary"></a>Souhrn Spout a šroubů

Výběr Spout z částí **spoutů** nebo **šrouby** zobrazí následující informace o vybrané položce:

| Sekce | Description |
| --- | --- |
| Souhrn komponenty | Základní informace o Spout nebo šroubu |
| Akce komponenty | **Ladění** a **zastavování tlačítek ladění** . |
| Statistiky Spout nebo šroubů | Statistika Spout nebo šroubu Chcete-li nastavit časový rámec pro položku v této části, vyberte její odkaz ve sloupci **okno** . |
| (Pouze šroub)<br/>Vstupní statistiky *(časový rámec)* | Informace o vstupních streamech spotřebovaných šroubem |
| Statistiky výstupu *(časový rámec)* | Informace o datových proudech emitovaných Spout nebo šroubem. |
| Profilace a ladění | Ovládací prvky pro profilaci a ladění komponent na této stránce. Můžete nastavit hodnotu **stav/časový limit (minuty)** a můžete vybrat tlačítka pro **JStack**, **restartovat pracovní proces** a **haldu**. |
| Prováděcí moduly *(časový rámec)* | Informace o instancích Spout nebo šroubu. Chcete-li zobrazit protokol diagnostických informací vytvořených pro tuto instanci, vyberte položku **port** pro konkrétního prováděcího modulu. Pracovní prostředky přidružené ke konkrétnímu vykonavateli můžete zobrazit také tak, že vyberete jeho odkaz ve sloupci **hostitel** . |
| Chyby | Jakékoli informace o chybě pro Spout nebo šroub. |

Stránka souhrnu šroubového šroubu bude vypadat podobně jako tato webová stránka:

![Stránka souhrnu šroubů, uživatelské rozhraní pro vyplavení, Apache Storm, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-bolt-summary.png)

## <a name="monitor-and-manage-the-topology-using-the-rest-api"></a>Monitorování a Správa topologie pomocí REST API

Uživatelské rozhraní pro obdobu je postavené na REST API, takže můžete provádět podobné úlohy správy a monitorování pomocí REST API. REST API můžete použít k vytvoření vlastních nástrojů pro správu a monitorování topologií nečinnosti.

Další informace najdete v tématu [Apache Storm REST API uživatelského rozhraní](https://storm.apache.org/releases/current/STORM-UI-REST-API.html). Následující informace jsou specifické pro použití REST API s Apache Storm ve službě HDInsight.

> [!IMPORTANT]  
> REST API s více podsítěmi nejsou veřejně dostupné prostřednictvím Internetu. Musí být přístupná pomocí tunelu SSH k hlavnímu uzlu clusteru HDInsight. Informace o vytváření a používání tunelu SSH najdete v tématu [použití tunelového propojení SSH pro přístup k Azure HDInsight](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>Základní identifikátor URI

Základní identifikátor URI pro REST API v clusterech HDInsight se systémem Linux je k dispozici na adrese URL `https://HEADNODEFQDN:8744/api/v1/` , kde *HEADNODEFQDN* nahradíte hlavní uzel. Název domény hlavního uzlu je vygenerován během vytváření clusteru a není statický.

Plně kvalifikovaný název domény (FQDN) pro hlavní uzel clusteru můžete najít několika způsoby:

| Metoda zjišťování plně kvalifikovaného názvu domény | Description |
| --- | --- |
| Relace SSH | Použijte příkaz `headnode -f` z relace SSH do clusteru. |
| Ambari Web | Na webové stránce clusteru Ambari ( `https://CLUSTERNAME.azurehdinsight.net` ) vyberte **služby** v horní části stránky a pak vyberte možnost přestránkovat . Na kartě **Souhrn** vyberte možnost **Server uživatelského rozhraní**. V horní části stránky se zobrazí plně kvalifikovaný název domény uzlu, který je hostitelem uživatelského rozhraní a REST API. |
| Ambari REST API | Pomocí příkazu `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` načtěte informace o uzlu, na kterém běží uživatelské rozhraní a REST API. Nahraďte dvě instance *název_clusteru* názvem clusteru. Až budete vyzváni, zadejte heslo pro účet uživatele (správce). V odpovědi obsahuje položka "host_name" výstupu JSON plně kvalifikovaný název domény uzlu. |

### <a name="authentication"></a>Authentication

Požadavky na REST API musí používat *základní ověřování*, proto musíte pro cluster HDInsight použít jméno správce a heslo.

> [!NOTE]  
> Vzhledem k tomu, že se základní ověřování posílá pomocí prostého textu, měli byste k zabezpečení komunikace s clusterem *vždycky* používat protokol HTTPS.

### <a name="return-values"></a>Vrácené hodnoty

Informace vrácené z REST API mohou být použitelné pouze v rámci clusteru. Například plně kvalifikovaný název domény (FQDN) vrácený pro [Apache Zookeeper](https://zookeeper.apache.org/) servery není přístupný z Internetu.

## <a name="next-steps"></a>Další kroky

Naučte se [vyvíjet topologie založené na jazyce Java pomocí Apache Maven](apache-storm-develop-java-topology.md).

Seznam více ukázkových topologií najdete v tématu [příklad Apache Storm topologie ve službě Azure HDInsight](apache-storm-example-topology.md).
