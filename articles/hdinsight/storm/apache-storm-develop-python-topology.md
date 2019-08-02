---
title: Apache Storm s komponentami Pythonu – Azure HDInsight
description: Naučte se vytvářet Apache Storm topologii, která používá součásti Pythonu.
author: hrasheed-msft
ms.reviewer: jasonh
keywords: Apache pro Python
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: hrasheed
ms.openlocfilehash: b9faf33734ba17e9912246fe9c5c2ac45c55ba44
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598474"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Vývoj Apache Storm topologií pomocí Pythonu ve službě HDInsight

Naučte se vytvářet [Apache Storm](https://storm.apache.org/) topologii, která používá součásti Pythonu. Apache Storm podporuje více jazyků, a to i v případě, že budete chtít kombinovat komponenty z několika jazyků v jedné topologii. Rozhraní [toků](https://storm.apache.org/releases/current/flux.html) (zavedené s pře0.10.0M) umožňuje snadno vytvářet řešení, která používají komponenty Pythonu.

> [!IMPORTANT]  
> Informace v tomto dokumentu byly testovány pomocí zaplavení v HDInsight 3,6. 

Kód pro tento projekt je k dispozici [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount)na adrese.

## <a name="prerequisites"></a>Požadavky

* Python 2,7 nebo vyšší

* Java JDK 1,8 nebo novější

* [Apache Maven 3](https://maven.apache.org/download.cgi)

* Volitelné Místní vývojové prostředí. Prostředí místního prostředí se vyžaduje jenom v případě, že chcete topologii spustit místně. Další informace najdete v tématu [Nastavení vývojového prostředí](http://storm.apache.org/releases/current/Setting-up-development-environment.html).

## <a name="storm-multi-language-support"></a>Podpora více jazyků

Apache Storm byla navržena pro práci s komponentami napsanými pomocí libovolného programovacího jazyka. Komponenty musí pochopit, jak pracovat s definicí [Thrift pro](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift)zaplavení. V jazyce Python je modul poskytován jako součást projektu Apache Storm, který umožňuje jednoduché rozhraní se zaplavou. Tento modul můžete najít na adrese [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Přetečení je proces Java, který běží na prostředí Java Virtual Machine (JVM). Komponenty napsané v jiných jazycích jsou spouštěny jako podprocesy. Zaplave komunikuje s těmito podprocesy pomocí zpráv JSON odesílaných přes STDIN/STDOUT. Další podrobnosti o komunikaci mezi komponentami najdete v dokumentaci k [protokolu multi-lang](https://storm.apache.org/documentation/Multilang-protocol.html) .

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

Třída `FluxShellSpout` se používá ke `sentencespout.py` spuštění skriptu, který implementuje rozhraní Spout.

Tok očekává, že se skripty v jazyce Python `/resources` nacházejí v adresáři uvnitř souboru jar, který obsahuje topologii. Proto tento příklad ukládá skripty Pythonu do `/multilang/resources` adresáře. `pom.xml` Zahrnuje tento soubor pomocí následujícího kódu XML:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Jak bylo `storm.py` zmíněno dříve, existuje soubor, který implementuje definici Thrift pro zaplavení. Rozhraní toků obsahuje `storm.py` automaticky, když je projekt sestaven, takže se nemusíte zabývat zahrnutím.

## <a name="build-the-project"></a>Sestavení projektu

Z kořenového adresáře projektu použijte následující příkaz:

```bash
mvn clean compile package
```

Tento příkaz vytvoří `target/WordCount-1.0-SNAPSHOT.jar` soubor, který obsahuje kompilovaná topologie.

## <a name="run-the-topology-locally"></a>Místní spuštění topologie

Pokud chcete topologii spustit místně, použijte následující příkaz:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> Tento příkaz vyžaduje prostředí pro vývoj v místním prostředí. Další informace najdete v tématu [Nastavení vývojového prostředí](https://storm.apache.org/releases/current/Setting-up-development-environment.html) .

Jakmile se topologie spustí, vygeneruje informace místní konzole podobně jako v následujícím textu:


    24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
    24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
    24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
    24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}


K zastavení topologie použijte __kombinaci kláves CTRL + C__.

## <a name="run-the-storm-topology-on-hdinsight"></a>Spuštění topologie pro zaplavení v HDInsight

1. Pomocí následujícího příkazu zkopírujte `WordCount-1.0-SNAPSHOT.jar` soubor do vašeho clusteru HDInsight:

    ```bash
    scp target\WordCount-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net
    ```

    Nahraďte `sshuser` uživatelem SSH pro váš cluster. Nahraďte `mycluster` názvem clusteru. Může se zobrazit výzva, abyste zadali heslo pro uživatele SSH.

    Další informace o použití SSH a SCP najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Po nahrání souboru se připojte ke clusteru pomocí SSH:

    ```bash
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Z relace SSH pomocí následujícího příkazu spusťte topologii v clusteru:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

3. K zobrazení topologie v clusteru můžete použít uživatelské rozhraní systému. Uživatelské rozhraní pro vyplavení https://mycluster.azurehdinsight.net/stormui se nachází na adrese. Nahraďte `mycluster` názvem vašeho clusteru.

> [!NOTE]  
> Po spuštění se topologie přetečení spustí až do zastavení. K zastavení topologie použijte jednu z následujících metod:
>
> * `storm kill TOPOLOGYNAME` Příkaz z příkazového řádku
> * Tlačítko **Kill** v uživatelském rozhraní se zaplavou


## <a name="next-steps"></a>Další postup

Další způsoby použití Pythonu se službou HDInsight najdete v následujících dokumentech:

* [Jak používat uživatelsky definované funkce Pythonu (UDF) v Apache prasete a Apache Hive](../hadoop/python-udf-hdinsight.md)
