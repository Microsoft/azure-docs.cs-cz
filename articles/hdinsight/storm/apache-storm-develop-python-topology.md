---
title: Apache Storm s komponentami Pythonu – Azure HDInsight
description: Zjistěte, jak vytvořit Apache Storm topologie, která používá komponent v Pythonu.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
keywords: Apache storm pythonu
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: jasonh
ms.openlocfilehash: 753c870f5d99d7bf887944b0d180645dc019b2b1
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43045763"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Vývoj topologií Apache Storm pomocí Pythonu v HDInsight

Zjistěte, jak vytvořit Apache Storm topologie, která používá komponent v Pythonu. Apache Storm podporuje více jazyků, dokonce i díky tomu můžete kombinovat součásti z různých jazyků v jedné topologie. Tok framework (zavedl se Stormem 0.10.0) umožňuje snadno vytvářet řešení, která používají komponent v Pythonu.

> [!IMPORTANT]
> Informace v tomto dokumentu byl testován pomocí Storm v HDInsight 3.6. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

Kód pro tento projekt je k dispozici na [ https://github.com/Azure-Samples/hdinsight-python-storm-wordcount ](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount).

## <a name="prerequisites"></a>Požadavky

* Python 2.7 nebo novější

* Java JDK 1.8 nebo vyšší

* Maven 3

* (Volitelné) Místní vývojové prostředí Storm. Místní prostředí Storm je potřeba pouze v případě, že chcete spustit topologii místně. Další informace najdete v tématu [nastavení vývojového prostředí](http://storm.apache.org/releases/1.1.2/Setting-up-development-environment.html).

## <a name="storm-multi-language-support"></a>Podpora víc jazyků Storm

Apache Storm je navržená pro práci s komponentami, které jsou napsané v libovolném programovacím jazyce. Součásti musí pochopit, jak pracovat [definici Thrift pro Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). Pro Python modul je součástí projektu Apache Storm, který umožňuje snadno spolupracovat s Storm. Můžete najít tento modul na [ https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py ](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Storm je Java proces, který běží na virtuálním počítači Java (JVM). Jako podprocesů, které provádějí komponenty napsané v jiných jazycích. Storm komunikuje s těmito podprocesů, které se pomocí JSON zprávy odeslané přes stdin/stdout. Další podrobnosti o komunikaci mezi součástmi najdete v [Multi-lang protokol](https://storm.apache.org/documentation/Multilang-protocol.html) dokumentaci.

## <a name="python-with-the-flux-framework"></a>Python s tokem framework

Tok framework umožňuje definovat topologií Storm nezávisle na součásti. Rozhraní tok YAML používá k definování topologie Storm. Následující text je příklad toho, jak odkazovat na součást Python v dokumentu YAML:

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

Třída `FluxShellSpout` se používá ke spuštění `sentencespout.py` skript, který implementuje spout.

Tok skriptů Pythonu v očekává, že `/resources` adresáře uvnitř na soubor jar obsahující topologie. Takže ukládá skriptů Pythonu v tomto příkladu `/multilang/resources` adresáře. `pom.xml` Obsahuje tento soubor pomocí následující kód XML:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Jak bylo zmíněno výše, je `storm.py` soubor, který implementuje definici Thrift pro Storm. Tok framework obsahuje `storm.py` automaticky při sestavení projektu, aby nemuseli se starat o jeho zahrnutí.

## <a name="build-the-project"></a>Sestavení projektu

Z kořenové složky projektu použijte následující příkaz:

```bash
mvn clean compile package
```

Tento příkaz vytvoří `target/WordCount-1.0-SNAPSHOT.jar` soubor, který obsahuje zkompilovaný topologie.

## <a name="run-the-topology-locally"></a>Místní spuštění topologie

Topologie spouštět místně, použijte následující příkaz:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]
> Tento příkaz vyžaduje místní vývojové prostředí Storm. Další informace najdete v tématu [nastavení vývojového prostředí](http://storm.apache.org/releases/current/Setting-up-development-environment.html)

Po spuštění topologie vydává informace do místní konzoly podobný následujícímu textu:


    24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
    24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
    24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
    24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}


Chcete-li ukončit topologii, použijte __Ctrl + C__.

## <a name="run-the-storm-topology-on-hdinsight"></a>Spustit topologie Storm v HDInsight

1. Použijte následující příkaz pro kopírování `WordCount-1.0-SNAPSHOT.jar` soubor do Storm na clusteru HDInsight:

    ```bash
    scp target\WordCount-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net
    ```

    Nahraďte `sshuser` s uživatele SSH pro váš cluster. Nahraďte `mycluster` s názvem clusteru. Můžete být vyzváni k zadání hesla pro uživatele SSH.

    Další informace o použití SSH a SCP v tématu [použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Po odeslání souboru připojte ke clusteru pomocí SSH:

    ```bash
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Z relace SSH pomocí následujícího příkazu spusťte topologie v clusteru:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

3. Chcete-li zobrazit topologii v clusteru můžete použít uživatelské rozhraní Storm. Uživatelské rozhraní Storm se nachází na https://mycluster.azurehdinsight.net/stormui. Nahraďte `mycluster` názvem vašeho clusteru.

> [!NOTE]
> Po zahájení topologie Stormu spouští až do ukončení. Pokud chcete ukončit topologii, použijte jednu z následujících metod:
>
> * `storm kill TOPOLOGYNAME` Příkazu z příkazového řádku
> * **Kill** tlačítka uživatelského rozhraní Storm.


## <a name="next-steps"></a>Další postup

Najdete v následujících dokumentech další způsoby, jak používat Python s HDInsight:

* [Použití Pythonu pro streamovacích úloh MapReduce](../hadoop/apache-hadoop-streaming-python.md)
* [Jak používat Python uživateli definované funkce (UDF) v Pigu a Hivu](../hadoop/python-udf-hdinsight.md)
