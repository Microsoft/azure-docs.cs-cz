---
title: Apache Storm s komponentami Pythonu - Azure HDInsight
description: Přečtěte si, jak vytvořit topologii Apache Storm, která používá komponenty Pythonu v Azure HDInsightu
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/16/2019
ms.openlocfilehash: 20e4827b1a86bff338646ef71f0dd732255c09c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77460020"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Vyvíjejte topologie Apache Storm pomocí Pythonu na HDInsightu

Přečtěte si, jak vytvořit topologii [Apache Storm,](https://storm.apache.org/) která používá komponenty Pythonu. Apache Storm podporuje více jazyků, a to i umožňuje kombinovat komponenty z několika jazyků v jedné topologii. Rámec [Flux](https://storm.apache.org/releases/current/flux.html) (představený s Storm 0.10.0) umožňuje snadno vytvářet řešení, která používají komponenty Pythonu.

> [!IMPORTANT]  
> Informace v tomto dokumentu byly testovány pomocí stormu na HDInsight 3.6.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Storm na HDInsight. Viz [Vytvoření clusterů Apache Hadoop pomocí portálu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) a vyberte **Storm** pro **typ clusteru**.

* Místní vývojové prostředí stormu (volitelné). Místní prostředí Storm je potřeba pouze v případě, že chcete spustit topologii místně. Další informace naleznete [v tématu Nastavení vývojového prostředí](https://storm.apache.org/releases/current/Setting-up-development-environment.html).

* [Python 2.7 nebo vyšší](https://www.python.org/downloads/).

* [Java Developer Kit (JDK) verze 8](https://aka.ms/azure-jdks).

* [Apache Maven](https://maven.apache.org/download.cgi) správně [nainstalován](https://maven.apache.org/install.html) podle Apache.  Maven je systém vytváření projektů pro java projekty.

## <a name="storm-multi-language-support"></a>Podpora více jazyků storm

Apache Storm byl navržen pro práci s komponentami napsanými pomocí libovolného programovacího jazyka. Součásti musí pochopit, jak pracovat s definicí šetrnost pro Storm. Pro Python je modul k dispozici jako součást projektu Apache Storm, který vám umožní snadno komunikovat s Storm. Tento modul naleznete [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py)na adrese .

Storm je proces Java, který běží na Java Virtual Machine (JVM). Součásti napsané v jiných jazycích jsou spouštěny jako dílčí procesy. Storm komunikuje s těmito dílčími procesy pomocí zpráv JSON odeslaných přes stdin/stdout. Další podrobnosti o komunikaci mezi komponentami naleznete v dokumentaci [k protokolu Multi-lang.](https://storm.apache.org/releases/current/Multilang-protocol.html)

## <a name="python-with-the-flux-framework"></a>Python s frameworkem Flux

Konstrukce Flux umožňuje definovat topologii Storm odděleně od komponent. Tažné centrum používá YAML k definování topologie bouře. Následující text je příkladem odkazu na komponentu Pythonu v dokumentu YAML:

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

Třída `FluxShellSpout` se používá ke `sentencespout.py` spuštění skriptu, který implementuje výtok.

Flux očekává, že skripty Pythonu budou v adresáři `/resources` uvnitř souboru jar, který obsahuje topologii. Proto tento příklad ukládá skripty `/multilang/resources` Pythonu v adresáři. Obsahuje `pom.xml` tento soubor pomocí následujícího xml:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Jak již bylo zmíněno `storm.py` dříve, je tu soubor, který implementuje definici šetrnost pro Storm. Rámec Flux `storm.py` zahrnuje automaticky, když je projekt postaven, takže se nemusíte starat o jeho zahrnutí.

## <a name="build-the-project"></a>Sestavení projektu

1. Stáhněte si [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount)projekt z aplikace .

1. Otevřete příkazový řádek a přejděte do kořenového adresáře projektu: `hdinsight-python-storm-wordcount-master`. Zadejte následující příkaz:

    ```cmd
    mvn clean compile package
    ```

    Tento příkaz `target/WordCount-1.0-SNAPSHOT.jar` vytvoří soubor, který obsahuje zkompilovanou topologii.

## <a name="run-the-storm-topology-on-hdinsight"></a>Spuštění topologie Storm na HDInsight

1. Pomocí [příkazu ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) zkopírujte soubor do clusteru `WordCount-1.0-SNAPSHOT.jar` Storm v HDInsight. Upravte níže uvedený příkaz nahrazením názvu clusteru názvem clusteru a zadejte příkaz:

    ```cmd
    scp target/WordCount-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Po nahrání souboru se připojte ke clusteru pomocí SSH:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. V relaci SSH spusťte topologii v clusteru pomocí následujícího příkazu:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

    Po spuštění se topologie bouře spustí, dokud není zastavena.

1. Pomocí ui storm u zobrazení topologie v clusteru. Storm UI se `https://CLUSTERNAME.azurehdinsight.net/stormui`nachází na adrese . Nahraďte `CLUSTERNAME` název clusteru.

1. Zastavte topologii Bouře. K zastavení topologie v clusteru použijte následující příkaz:

    ```bash
    storm kill wordcount
    ```

    Případně můžete použít storm ui. V části **Akce topologie** pro topologii vyberte **Zabít**.

## <a name="run-the-topology-locally"></a>Spuštění topologie místně

Chcete-li topologii spustit místně, použijte následující příkaz:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> Tento příkaz vyžaduje místní vývojové prostředí Storm. Další informace naleznete [v tématu Nastavení vývojového prostředí](https://storm.apache.org/releases/current/Setting-up-development-environment.html).

Jakmile se topologie spustí, vydává informace do místní konzoly podobné následujícímu textu:

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

Chcete-li topologii zastavit, použijte __kombinaci kláves Ctrl + C__.

## <a name="next-steps"></a>Další kroky

Další způsoby použití Pythonu s HDInsight: [Jak používat uživatelem definované funkce Pythonu (UDF) v Apache Pig a Apache Hive](../hadoop/python-udf-hdinsight.md).
