---
title: Apache Storm s comopnents Python - Azure HDInsight | Microsoft Docs
description: Naučte se vytvářet topologie Apache Storm, která používá Python součásti.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
keywords: Apache storm python
ms.assetid: edd0ec4f-664d-4266-910c-6ecc94172ad8
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: python
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: larryfr
ms.openlocfilehash: ebfc9d5aa1c3a650a938c0a9f5fc3d047f90458d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Vývoj topologií Apache Storm v HDInsight používá Python

Naučte se vytvářet topologie Apache Storm, která používá Python součásti. Apache Storm podporuje několik jazyků, i umožňuje zkombinovat součásti z několika jazyků v jedné topologii. Rozhraní framework tok (zavedené Storm 0.10.0) umožňuje snadno vytvářet řešení, která používají Python součásti.

> [!IMPORTANT]
> Informace v tomto dokumentu byla testována pomocí Storm v HDInsight 3.6. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

Kód pro tento projekt je k dispozici na [ https://github.com/Azure-Samples/hdinsight-python-storm-wordcount ](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount).

## <a name="prerequisites"></a>Požadavky

* Python 2.7 nebo vyšší

* Java JDK 1.8 nebo vyšší

* Maven 3

* (Volitelné) Místní Storm vývojové prostředí. Místní prostředí Storm je vyžadován, pouze pokud chcete spustit topologii místně. Další informace najdete v tématu [nastavení vývojového prostředí](http://storm.apache.org/releases/1.1.0/Setting-up-development-environment.html).

## <a name="storm-multi-language-support"></a>Podpora více jazyků Storm

Apache Storm je navržené pro práci s součásti, které jsou napsané v žádný programovací jazyk. Součásti musíte pochopit, jak pracovat [definici Thrift Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). Pro Python se v rámci Apache Storm projekt, který umožňuje snadno rozhraní s Storm poskytuje modul. Můžete najít na tento modul [ https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py ](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Storm je Java proces, který běží na virtuálním počítači Java (JVM). Součásti, které jsou napsané v dalších jazycích jsou spouštěny jako podprocesů, které se. Storm komunikuje se tyto podprocesů, které se pomocí JSON zprávy odeslané přes stdin/stdout. Další informace o komunikaci mezi součástmi najdete v [více jazyků protokol](https://storm.apache.org/documentation/Multilang-protocol.html) dokumentaci.

## <a name="python-with-the-flux-framework"></a>Python s tokem framework

Rozhraní framework tok umožňuje definovat topologie Storm nezávisle na součásti. Rozhraní framework tok YAML používá k definování topologie Storm. Tento text je příklad toho, jak odkazovat na komponentu Python v dokumentu YAML:

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

Třída `FluxShellSpout` se používá ke spuštění `sentencespout.py` skript, který implementuje funkcí spout.

Tok očekává skriptů Python ve `/resources` adresář uvnitř soubor jar obsahující topologii. Takže ukládá skriptů Python v tomto příkladu `/multilang/resources` adresáře. `pom.xml` Zahrnuje tento soubor pomocí následující kód XML:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Jak už bylo zmíněno dříve, není `storm.py` soubor, který implementuje definici Thrift Storm. Zahrnuje rozhraní tok `storm.py` automaticky při sestavení projektu, takže není nutné se obávat, včetně ho.

## <a name="build-the-project"></a>Sestavení projektu

Z kořenového adresáře projektu použijte následující příkaz:

```bash
mvn clean compile package
```

Tento příkaz vytvoří `target/WordCount-1.0-SNAPSHOT.jar` soubor, který obsahuje kompilované topologie.

## <a name="run-the-topology-locally"></a>Místní spuštění topologie

Pokud chcete spustit topologii místně, použijte následující příkaz:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]
> Tento příkaz vyžaduje místní vývojové prostředí Storm. Další informace najdete v tématu [nastavení vývojového prostředí](http://storm.apache.org/releases/1.0.1/Setting-up-development-environment.html)

Jednou spuštěním topologie, se vydá informace do místní konzoly podobný následujícímu:


    24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
    24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
    24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
    24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}


Chcete-li zastavit topologii, použijte __kombinaci kláves Ctrl + C__.

## <a name="run-the-storm-topology-on-hdinsight"></a>Spustit topologie Storm v HDInsight

1. Použijte následující příkaz pro kopírování `WordCount-1.0-SNAPSHOT.jar` soubor Storm na clusteru HDInsight:

    ```bash
    scp target\WordCount-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net
    ```

    Nahraďte `sshuser` s uživatelem SSH pro váš cluster. Nahraďte `mycluster` se název clusteru. Můžete být vyzváni k zadání hesla pro uživatele SSH.

    Další informace o používání SSH a SCP najdete v tématu [použití SSH s HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Jakmile soubor byl odeslán, připojte se ke clusteru pomocí protokolu SSH:

    ```bash
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Z relace SSH použijte následující příkaz v clusteru spusťte topologie:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

3. Uživatelské rozhraní Storm můžete použít k zobrazení topologii v clusteru. Uživatelské rozhraní Storm se nachází v https://mycluster.azurehdinsight.net/stormui. Nahraďte `mycluster` se název clusteru.

> [!NOTE]
> Po spuštění, topologie Storm spustí, dokud nebude zastaven. Zastavení topologie, použijte jednu z následujících metod:
>
> * `storm kill TOPOLOGYNAME` Příkazu z příkazového řádku
> * **Kill** tlačítko v uživatelském rozhraní Storm.


## <a name="next-steps"></a>Další postup

Najdete v následujících dokumentech pro další způsoby použití Python s HDInsight:

* [Jak používat Python pro streamování úloh MapReduce](../hadoop/apache-hadoop-streaming-python.md)
* [Jak používat Python uživatele definované funkce (UDF) v Pig a Hive](../hadoop/python-udf-hdinsight.md)
