---
title: Nasazení a Správa topologií Apache Storm v Azure HDInsight
description: Naučte se nasazovat, monitorovat a spravovat Apache Storm topologie pomocí řídicího panelu pro zaplavování v HDInsight se systémem Linux. Použijte nástroje Hadoop pro Visual Studio.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: 1c219c85836eb4730fa90918385555c433a12449
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915097"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Nasazení a Správa topologií Apache Storm v Azure HDInsight 

V tomto dokumentu se naučíte základy správy a monitorování [Apache Stormch](https://storm.apache.org/) topologií běžících na clusterech HDInsight.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Storm v HDInsight. Přečtěte si téma [vytvoření Apache Hadoop clusterů pomocí Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) **a výběr funkce** pro **typ clusteru**.


* Volitelné Znalost protokolu SSH a SCP: Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Volitelné Visual Studio: Azure SDK 2.5.1 nebo novější a nástroje pro Data Lake pro Visual Studio. Další informace najdete v tématu [Začínáme s používáním data Lakech nástrojů pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    Jedna z následujících verzí sady Visual Studio:

  * Visual Studio 2012 s aktualizací Update 4

  * Visual Studio 2013 s aktualizací Update 4 nebo [Visual Studio 2013 Community](https://go.microsoft.com/fwlink/?LinkId=517284)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/)

  * Visual Studio 2015 (jakákoli edice)

  * Visual Studio 2017 (libovolná edice). Nástroje pro Data Lake pro Visual Studio 2017 se instalují jako součást úlohy Azure.

## <a name="submit-a-topology-visual-studio"></a>Odeslat topologii: Visual Studio

Nástroje HDInsight je možné použít k odesílání C# nebo hybridních topologií do clusteru s více podprocesy. Následující kroky používají ukázkovou aplikaci. Informace o vytváření pomocí nástrojů HDInsight najdete v tématu [ C# Vývoj topologií pomocí nástrojů HDInsight pro Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

1. Pokud jste ještě nenainstalovali nejnovější verzi nástrojů Data Lake Tools for Visual Studio, přečtěte si téma Začínáme [používat data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > Nástroje pro Data Lake pro Visual Studio se dřív nazývaly nástroje HDInsight pro Visual Studio.
    >
    > Sady nástrojů pro Data Lake pro Visual Studio jsou součástí __úlohy Azure__ pro visual Studio 2017.

2. Otevřete Visual Studio, vyberte **soubor** > **Nový** > **projekt**.

3. V dialogovém okně **Nový projekt** rozbalte položku **nainstalované** > **šablony**a poté vyberte možnost **HDInsight**. V seznamu šablon vyberte možnost napisovat **vzorek**. V dolní části dialogového okna zadejte název aplikace.

    ![image](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

4. V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt a vyberte **Odeslat pro**zaplavení v HDInsight.

   > [!NOTE]  
   > Pokud se zobrazí výzva, zadejte přihlašovací údaje pro vaše předplatné Azure. Pokud máte více než jedno předplatné, přihlaste se k počítači, který obsahuje vaše vyplavení v clusteru HDInsight.

5. Z rozevíracího seznamu **cluster** pro zaplavení vyberte svou navýšení na clusteru HDInsight a pak vyberte **Odeslat**. Můžete monitorovat, zda je odeslání úspěšné, pomocí okna **výstup** .

## <a name="submit-a-topology-ssh-and-the-storm-command"></a>Odeslat topologii: SSH a příkaz pro zaplavení

1. Pomocí SSH se připojte ke clusteru HDInsight. V poli **uživatelské jméno** nahraďte název svého přihlášení SSH. Nahraďte **název_clusteru** názvem clusteru HDInsight:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Další informace o použití SSH pro připojení ke clusteru HDInsight najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Následující příkaz použijte ke spuštění ukázkové topologie:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount

    Tento příkaz v clusteru spustí ukázkovou topologii WordCount. Tato topologie náhodně generuje věty a pak počítá výskyt každého slova ve větách.

   > [!NOTE]  
   > Při odesílání topologie do clusteru je nutné nejprve zkopírovat soubor jar obsahující cluster před použitím příkazu `storm`. K zkopírování souboru do clusteru můžete použít `scp` příkaz. Například `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`.
   >
   > Příklad WordCount a další příklady starter storm jsou již zahrnuty v clusteru na `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Odeslání topologie: programově

Topologii můžete programově nasadit pomocí služby Nimbus. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology)poskytuje ukázkovou aplikaci v jazyce Java, která ukazuje, jak nasadit a spustit topologii prostřednictvím služby Nimbus.

## <a name="monitor-and-manage-visual-studio"></a>Monitorování a správa: Visual Studio

Při odeslání topologie pomocí sady Visual Studio se zobrazí zobrazení **topologie** navýšení. Vyberte topologii ze seznamu a zobrazte informace o spuštěné topologii.

![monitorování sady Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> Z **Průzkumník serveru** můžete také pomocí rozšíření **Azure** > **HDInsight**zobrazit **topologie** rozplavování a potom kliknout pravým tlačítkem na cluster HDInsight a vybrat **Zobrazit topologie**nenáročného čtení.

Vyberte tvar pro spoutů nebo šrouby pro zobrazení informací o těchto součástech. Pro každou vybranou položku se otevře nové okno.

### <a name="deactivate-and-reactivate"></a>Deaktivovat a znovu aktivovat

Deaktivace topologie ji pozastaví, dokud nebude ukončena nebo znovu aktivována. Chcete-li provést tyto operace, použijte tlačítka __dezaktivovat__ a __znovu aktivovat__ v horní části __souhrnu topologie__.

### <a name="rebalance"></a>Obnovit rovnováhu

Nové vyrovnávání topologie umožňuje systému revidovat paralelismus topologie. Pokud jste například změnili velikost clusteru tak, aby bylo možné přidat další poznámky, umožňuje nové vyrovnávání topologie zobrazení nových uzlů.

K vyrovnávání zatížení topologie použijte tlačítko pro __vyvážení__ v horní části __souhrnu topologie__.

> [!WARNING]  
> Při vyrovnávání topologie se nejdřív deaktivuje topologie a pak se v clusteru znovu rozšíří a pak se nakonec vrátí topologie do stavu, ve kterém došlo k novému vyvážení. Takže pokud byla topologie aktivní, bude znovu aktivní. Pokud byla deaktivována, zůstane deaktivována.

### <a name="kill-a-topology"></a>Odstranit topologii

Topologie zaplavování pokračují v běhu, dokud nejsou zastavené nebo se cluster odstraní. K zastavení topologie použijte tlačítko __Kill__ v horní části __souhrnu topologie__.

## <a name="monitor-and-manage-ssh-and-the-storm-command"></a>Monitorování a správa: SSH a příkaz pro zaplavení

`storm` Nástroj umožňuje pracovat s běžícími topologiemi z příkazového řádku. Použijte `storm -h` pro úplný seznam příkazů.

### <a name="list-topologies"></a>Seznam topologií

K vypsání všech spuštěných topologií použijte následující příkaz:

    storm list

Tento příkaz by měl vrátit informace podobné následujícímu textu:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Deaktivovat a znovu aktivovat

Deaktivace topologie ji pozastaví, dokud nebude ukončena nebo znovu aktivována. K deaktivaci a opětovné aktivaci použijte následující příkaz:

    storm Deactivate TOPOLOGYNAME

    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>Ukončí spuštěnou topologii.

Po spuštění budou topologie s více podsystémy spuštěny, dokud se zastaví. K zastavení topologie použijte následující příkaz:

    storm kill TOPOLOGYNAME

### <a name="rebalance"></a>Obnovit rovnováhu

Nové vyrovnávání topologie umožňuje systému revidovat paralelismus topologie. Pokud jste například změnili velikost clusteru tak, aby bylo možné přidat další poznámky, umožňuje nové vyrovnávání topologie zobrazení nových uzlů.

> [!WARNING]  
> Při vyrovnávání topologie se nejdřív deaktivuje topologie a pak se v clusteru znovu rozšíří a pak se nakonec vrátí topologie do stavu, ve kterém došlo k novému vyvážení. Takže pokud byla topologie aktivní, bude znovu aktivní. Pokud byla deaktivována, zůstane deaktivována.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-storm-ui"></a>Monitorování a správa: Uživatelské rozhraní pro vyplavení

Uživatelské rozhraní Storm poskytuje webové rozhraní pro práci se spuštěnými topologiemi a je součástí clusteru HDInsight. Chcete-li zobrazit uživatelské rozhraní pro vyplavení, použijte **https://CLUSTERNAME.azurehdinsight.net/stormui** webový prohlížeč, který otevřete, kde **název_clusteru** je název vašeho clusteru.

> [!NOTE]  
> Pokud budete vyzváni k zadání uživatelského jména a hesla, zadejte správce clusteru (admin) a heslo použité při vytvoření clusteru.

### <a name="main-page"></a>Hlavní stránka

Hlavní stránka uživatelského rozhraní pro zaplavení poskytuje následující informace:

* **Souhrn clusteru**: Základní informace o clusteru zaplavení.
* **Souhrn topologie**: Seznam spuštěných topologií. Pomocí odkazů v této části můžete zobrazit další informace o konkrétních topologiích.
* **Souhrn vedoucího**: Informace o nadřízeném správce.
* **Konfigurace Nimbus**: Konfigurace Nimbus pro cluster

### <a name="topology-summary"></a>Souhrn topologie

Výběrem odkazu v části **Souhrn topologie** se zobrazí následující informace o topologii:

* **Souhrn topologie**: Základní informace o topologii.
* **Akce topologie**: Akce správy, které lze pro topologii provést.

  * **Aktivovat**: Obnoví zpracování deaktivované topologie.
  * **Deaktivovat**: Pozastaví běžící topologii.
  * **Převážení**: Upravuje paralelismus topologie. Po změně počtu uzlů v clusteru musíte znovu vyvážit spuštěné topologie. Tato operace umožňuje, aby topologie upravila paralelismus pro zvýšení nebo snížení počtu uzlů v clusteru.

    Další informace najdete v tématu <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Princip paralelismu Apache Storm topologie</a>.
  * **Dezaktivační**: Ukončí topologii nečinnosti po zadaném časovém limitu.
* **Statistika topologie**: Statistika topologie. Chcete-li nastavit časový rámec pro zbývající položky na stránce, použijte odkazy ve sloupci **okna** .
* **Spoutů**: Spoutů používaná topologií. Pomocí odkazů v této části můžete zobrazit další informace o konkrétních spoutů.
* **Šrouby**: Šrouby, kterou používá topologie. Pomocí odkazů v této části můžete zobrazit další informace o určitém šrouby.
* **Konfigurace topologie**: Konfigurace vybrané topologie.

### <a name="spout-and-bolt-summary"></a>Souhrn Spout a šroubů

Výběr Spout z částí **spoutů** nebo **šrouby** zobrazí následující informace o vybrané položce:

* **Souhrn komponenty**: Základní informace o Spout nebo šroubu
* **Statistika Spout/šroub**: Statistika Spout nebo šroubu Chcete-li nastavit časový rámec pro zbývající položky na stránce, použijte odkazy ve sloupci **okna** .
* **Statistiky vstupu** (jenom šroub): Informace o vstupních streamech spotřebovaných šroubem
* **Statistika výstupu**: Informace o datových proudech emitovaných Spout nebo šroubem.
* **Vykonavatelé**: Informace o instancích Spout nebo šroubu. Vyberte záznam **portu** pro konkrétní prováděcí modul, aby se zobrazil protokol diagnostických informací vytvořených pro tuto instanci.
* **Chyby**: Jakékoli informace o chybě pro Spout nebo šroub.

## <a name="monitor-and-manage-rest-api"></a>Monitorování a správa: REST API

Uživatelské rozhraní funkce je postavené na REST API, takže můžete provádět podobné funkce správy a monitorování pomocí REST API. REST API můžete použít k vytvoření vlastních nástrojů pro správu a monitorování topologií nečinnosti.

Další informace najdete v tématu [Apache Storm REST API uživatelského rozhraní](https://storm.apache.org/releases/current/STORM-UI-REST-API.html). Následující informace jsou specifické pro použití REST API s Apache Storm ve službě HDInsight.

> [!IMPORTANT]  
> NeREST APIá technologie není veřejně dostupná přes Internet a musí se k ní získat přístup pomocí tunelového propojení SSH k hlavnímu uzlu clusteru HDInsight. Informace o vytváření a používání tunelu SSH najdete v tématu [použití tunelového propojení SSH pro přístup k webovému uživatelskému rozhraní Apache Ambari, ResourceManager, JobHistory, NameNode, Apache Oozie a dalším webovým uživatelská rozhraní](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>Základní identifikátor URI

Základní identifikátor URI pro REST API v clusterech HDInsight se systémem Linux je k dispozici v hlavním uzlu na adrese **https:\//HEADNODEFQDN: 8744/API/v1/** . Název domény hlavního uzlu je vygenerován během vytváření clusteru a není statický.

Plně kvalifikovaný název domény (FQDN) pro hlavní uzel clusteru můžete najít několika různými způsoby:

* **Z relace SSH**: Použijte příkaz `headnode -f` z relace SSH do clusteru.
* **Z webu Ambari**: V horní části stránky vyberte **služby** a pak **Vyberte možnost**zaplavení. Na kartě **Souhrn** vyberte možnost **Server uživatelského rozhraní**. V horní části stránky se zobrazí plně kvalifikovaný název domény uzlu, který je hostitelem uživatelského rozhraní a REST API.
* **Z Ambari REST API**: Pomocí příkazu `curl -u admin -G "https:\//CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` načtěte informace o uzlu, na kterém běží uživatelské rozhraní a REST API. Položku **název_clusteru** nahraďte názvem clusteru. Po zobrazení výzvy zadejte heslo pro účet přihlášení (správce). V odpovědi položka "HOST_NAME" obsahuje plně kvalifikovaný název domény uzlu.

### <a name="authentication"></a>Ověřování

Požadavky na REST API musí používat **základní ověřování**, takže použijte název a heslo správce clusteru HDInsight.

> [!NOTE]  
> Vzhledem k tomu, že se základní ověřování posílá pomocí prostého textu, měli byste k zabezpečení komunikace s clusterem **vždycky** používat protokol HTTPS.

### <a name="return-values"></a>Vrácené hodnoty

Informace vrácené z REST API mohou být použitelné pouze v rámci clusteru. Například plně kvalifikovaný název domény (FQDN) vrácený pro [Apache Zookeeper](https://zookeeper.apache.org/) servery není přístupný z Internetu.

## <a name="next-steps"></a>Další kroky

Naučte se [vyvíjet topologie založené na jazyce Java pomocí Apache Maven](apache-storm-develop-java-topology.md).

Seznam více ukázkových topologií najdete v tématu [Příklady topologií pro Apache Storm v HDInsight](apache-storm-example-topology.md).
