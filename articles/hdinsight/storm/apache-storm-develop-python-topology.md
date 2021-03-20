---
title: Apache Storm s komponentami Pythonu – Azure HDInsight
description: Naučte se vytvářet Apache Storm topologii, která používá komponenty Pythonu ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-python
ms.date: 12/16/2019
ms.openlocfilehash: e28d21ed71cf5f485165c639a8bd519b3a2736e1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98928985"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Vývoj Apache Storm topologií pomocí Pythonu ve službě HDInsight

Naučte se vytvářet [Apache Storm](https://storm.apache.org/) topologii, která používá součásti Pythonu. Apache Storm podporuje více jazyků, a to i v případě, že budete chtít kombinovat komponenty z několika jazyků v jedné topologii. Rozhraní [toků](https://storm.apache.org/releases/current/flux.html) (zavedené s pře0.10.0M) umožňuje snadno vytvářet řešení, která používají komponenty Pythonu.

> [!IMPORTANT]  
> Informace v tomto dokumentu byly testovány pomocí zaplavení v HDInsight 3,6.

## <a name="prerequisites"></a>Předpoklady

* Cluster Apache Storm v HDInsight. Přečtěte si téma [vytvoření Apache Hadoop clusterů pomocí Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) **a výběr funkce** pro **typ clusteru**.

* Místní vývojové prostředí s více podmnožinami (volitelné). Prostředí místního prostředí se vyžaduje jenom v případě, že chcete topologii spustit místně. Další informace najdete v tématu [Nastavení vývojového prostředí](https://storm.apache.org/releases/current/Setting-up-development-environment.html).

* [Python 2,7 nebo vyšší](https://www.python.org/downloads/).

* [Java Developer Kit (JDK) verze 8](/azure/developer/java/fundamentals/java-jdk-long-term-support).

* [Apache Maven](https://maven.apache.org/download.cgi) správně [nainstalované](https://maven.apache.org/install.html) v souladu s Apache.  Maven je systém sestavení projektu pro projekty v jazyce Java.

## <a name="storm-multi-language-support"></a>Podpora více jazyků

Apache Storm byla navržena pro práci s komponentami napsanými pomocí libovolného programovacího jazyka. Komponenty musí pochopit, jak pracovat s definicí Thrift pro zaplavení. V jazyce Python je modul poskytován jako součást projektu Apache Storm, který umožňuje jednoduché rozhraní se zaplavou. Tento modul můžete najít na adrese [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py) .

Přetečení je proces Java, který běží na prostředí Java Virtual Machine (JVM). Komponenty napsané v jiných jazycích jsou spouštěny jako podprocesy. Zaplave komunikuje s těmito podprocesy pomocí zpráv JSON odesílaných přes STDIN/STDOUT. Další podrobnosti o komunikaci mezi komponentami najdete v dokumentaci k [protokolu multi-lang](https://storm.apache.org/releases/current/Multilang-protocol.html) .

## <a name="python-with-the-flux-framework"></a>Python s architekturou toků

Rozhraní služby toků umožňuje definovat topologie navýšení nezávisle na komponentách. Rozhraní toků používá YAML k definování topologie zaplavení. Následující text je příkladem, jak odkazovat na komponentu Pythonu v dokumentu YAML:

```yaml
# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "org.apache.storm.flux.wrappers.spouts.FluxShellSpout"
    constructorArgs:
      # Command line
      - ["python", "sentencespout.py"]
      # Output field(s)
      - ["sentence"]
    # parallelism hint
    parallelism: 1
```

Třída `FluxShellSpout` se používá ke spuštění `sentencespout.py` skriptu, který implementuje rozhraní Spout.

Tok očekává, že se skripty v jazyce Python `/resources` nacházejí v adresáři uvnitř souboru jar, který obsahuje topologii. Proto tento příklad ukládá skripty Pythonu do `/multilang/resources` adresáře. `pom.xml`Zahrnuje tento soubor pomocí následujícího kódu XML:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Jak bylo zmíněno dříve, existuje `storm.py` soubor, který implementuje definici Thrift pro zaplavení. Rozhraní toků obsahuje `storm.py` automaticky, když je projekt sestaven, takže se nemusíte zabývat zahrnutím.

## <a name="build-the-project"></a>Sestavení projektu

1. Stáhněte si projekt z [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) .

1. Otevřete příkazový řádek a přejděte do kořenového adresáře projektu: `hdinsight-python-storm-wordcount-master` . Zadejte následující příkaz:

    ```cmd
    mvn clean compile package
    ```

    Tento příkaz vytvoří `target/WordCount-1.0-SNAPSHOT.jar` soubor, který obsahuje kompilovaná topologie.

## <a name="run-the-storm-topology-on-hdinsight"></a>Spuštění topologie pro zaplavení v HDInsight

1. Pomocí [příkazu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) zkopírujte `WordCount-1.0-SNAPSHOT.jar` soubor do vašeho clusteru HDInsight. Níže uvedený příkaz upravte tak, že ho nahradíte názvem clusteru a pak zadáte tento příkaz:

    ```cmd
    scp target/WordCount-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Po nahrání souboru se připojte ke clusteru pomocí SSH:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Z relace SSH pomocí následujícího příkazu spusťte topologii v clusteru:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

    Po spuštění se topologie přetečení spustí až do zastavení.

1. K zobrazení topologie v clusteru použijte uživatelské rozhraní systému. Uživatelské rozhraní pro vyplavení se nachází na adrese `https://CLUSTERNAME.azurehdinsight.net/stormui` . Nahraďte `CLUSTERNAME` názvem vašeho clusteru.

1. Zastavte topologii. Pomocí následujícího příkazu zastavte topologii v clusteru:

    ```bash
    storm kill wordcount
    ```

    Alternativně můžete použít uživatelské rozhraní. V části **Akce topologie** pro topologii vyberte možnost **Kill**.

## <a name="run-the-topology-locally"></a>Místní spuštění topologie

Pokud chcete topologii spustit místně, použijte následující příkaz:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> Tento příkaz vyžaduje prostředí pro vývoj v místním prostředí. Další informace najdete v tématu [Nastavení vývojového prostředí](https://storm.apache.org/releases/current/Setting-up-development-environment.html).

Jakmile se topologie spustí, vygeneruje informace místní konzole podobně jako v následujícím textu:

```output
24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}
```

K zastavení topologie použijte __kombinaci kláves CTRL + C__.

## <a name="next-steps"></a>Další kroky

Další způsoby použití Pythonu s HDInsight najdete v následujících dokumentech: [Jak používat uživatelsky definované funkce (UDF) Pythonu v Apache vepřích a Apache Hive](../hadoop/python-udf-hdinsight.md).