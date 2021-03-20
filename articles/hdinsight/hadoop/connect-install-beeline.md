---
title: Připojení nebo instalace Apache Beeline – Azure HDInsight
description: Naučte se připojit k klientovi Apache Beeline a spouštět dotazy na podregistry pomocí Hadoop v HDInsight. Beeline je nástroj pro práci s HiveServer2 nad JDBC.
ms.service: hdinsight
ms.topic: how-to
ms.custom: contperf-fy21q1
ms.date: 05/27/2020
ms.openlocfilehash: ab5dedf6718dad4f16fde59d905e2e59be5c495f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944456"
---
# <a name="connect-to-apache-beeline-on-hdinsight-or-install-it-locally"></a>Připojte se k Apache Beeline ve službě HDInsight nebo ji nainstalujte místně.

[Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) je klient podregistru, který je součástí hlavních uzlů clusteru HDInsight. Tento článek popisuje, jak se připojit k Beeline klientovi nainstalovanému v clusteru HDInsight napříč různými typy připojení. Popisuje také, jak [místně nainstalovat klienta Beeline](#install-beeline-client). 

## <a name="types-of-connections"></a>Typy připojení

### <a name="from-an-ssh-session"></a>Z relace SSH

Když se připojujete z relace SSH k hlavnímu uzlu clusteru, můžete se připojit k `headnodehost` adrese na portu `10001` :

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

### <a name="over-an-azure-virtual-network"></a>Přes Virtual Network Azure

Když se připojujete z klienta k HDInsight přes Virtual Network Azure, musíte zadat plně kvalifikovaný název domény (FQDN) hlavního uzlu clusteru. Vzhledem k tomu, že se toto připojení provádí přímo na uzlech clusteru, připojení používá port `10001` :

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Nahraďte `<headnode-FQDN>` plně kvalifikovaným názvem domény hlavnímu uzlu clusteru. K vyhledání plně kvalifikovaného názvu domény hlavnímu uzlu použijte informace v části [Správa HDInsight pomocí dokumentu Apache Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes) .

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Do clusteru HDInsight Balíček zabezpečení podniku (ESP) pomocí protokolu Kerberos

Když se připojujete z klienta k clusteru Balíček zabezpečení podniku (ESP) připojenému k Azure Active Directory (AAD) – DS na počítači ve stejné sféře clusteru, musíte zadat také název domény `<AAD-Domain>` a název účtu uživatele domény s oprávněním k přístupu ke clusteru `<username>` :

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Nahraďte `<username>` názvem účtu v doméně, který má oprávnění pro přístup ke clusteru. Nahraďte `<AAD-DOMAIN>` názvem Azure Active Directory (AAD), ke které je cluster připojený. Pro hodnotu použijte velká písmena `<AAD-DOMAIN>` , jinak se přihlašovací údaje nenašly. `/etc/krb5.conf`V případě potřeby vyhledejte názvy sféry.

Vyhledání adresy URL JDBC z Ambari:

1. Z webového prohlížeče přejděte do `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` umístění, kde `CLUSTERNAME` je název vašeho clusteru. Ujistěte se, že je spuštěný HiveServer2.

1. Pomocí schránky zkopírujte adresu URL HiveServer2 JDBC.

### <a name="over-public-or-private-endpoints"></a>Přes veřejné nebo soukromé koncové body

Při připojování ke clusteru pomocí veřejných nebo privátních koncových bodů je nutné zadat název přihlašovacího účtu clusteru (výchozí `admin` ) a heslo. Například pomocí Beeline z klientského systému se připojte k `clustername.azurehdinsight.net` adrese. Toto připojení se provádí přes port `443` a je šifrované pomocí protokolu TLS/SSL.

Parametr `clustername` nahraďte názvem vašeho clusteru HDInsight. Nahraďte `admin` přihlašovacím účtem clusteru pro svůj cluster. U clusterů ESP použijte úplný název uživatele (například user@domain.com ). Nahraďte `password` heslem přihlašovacího účtu clusteru.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

nebo pro soukromý koncový bod:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

Soukromé koncové body odkazují na základní nástroj pro vyrovnávání zatížení, ke kterému se dá dostat jenom z partnerského vztahu virtuální sítě ve stejné oblasti. Další informace najdete v tématu [omezení globálních partnerských vztahů virtuální sítě a nástrojů pro vyrovnávání zatížení](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . Pomocí `curl` příkazu s `-v` možností můžete řešit problémy s připojením pomocí veřejných nebo privátních koncových bodů před použitím Beeline.

### <a name="use-beeline-with-apache-spark"></a>Použití Beeline s Apache Spark

Apache Spark poskytuje vlastní implementaci HiveServer2, která se někdy označuje jako server Spark Thrift. Tato služba používá Spark SQL k překladu dotazů místo podregistru. A v závislosti na dotazu může poskytovat lepší výkon.

#### <a name="through-public-or-private-endpoints"></a>Prostřednictvím veřejných nebo privátních koncových bodů

Použitý připojovací řetězec je trochu odlišný. Místo obsahujícího `httpPath=/hive2` použití `httpPath/sparkhive2` . Parametr `clustername` nahraďte názvem vašeho clusteru HDInsight. Nahraďte `admin` přihlašovacím účtem clusteru pro svůj cluster. U clusterů ESP použijte úplný název uživatele (například user@domain.com ). Nahraďte `password` heslem přihlašovacího účtu clusteru.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

nebo pro soukromý koncový bod:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

Soukromé koncové body odkazují na základní nástroj pro vyrovnávání zatížení, ke kterému se dá dostat jenom z partnerského vztahu virtuální sítě ve stejné oblasti. Další informace najdete v tématu [omezení globálních partnerských vztahů virtuální sítě a nástrojů pro vyrovnávání zatížení](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . Pomocí `curl` příkazu s `-v` možností můžete řešit problémy s připojením pomocí veřejných nebo privátních koncových bodů před použitím Beeline.

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Z hlav clusteru nebo uvnitř Azure Virtual Network s Apache Spark

Při přímém připojení z hlavního uzlu clusteru nebo z prostředku ve stejném Virtual Network Azure jako cluster HDInsight `10002` by se měl port použít pro server Spark Thrift místo `10001` . Následující příklad ukazuje, jak se připojit přímo k hlavnímu uzlu:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

## <a name="install-beeline-client"></a>Nainstalovat klienta Beeline

I když je Beeline obsažený v hlavních uzlech, můžete ho chtít nainstalovat místně.  Kroky instalace pro místní počítač jsou založené na [subsystému Windows pro Linux](/windows/wsl/install-win10).

1. Aktualizujte seznamy balíčků. Do prostředí bash zadejte následující příkaz:

    ```bash
    sudo apt-get update
    ```

1. Pokud není nainstalovaný, nainstalujte Java. Můžete se podívat na `which java` příkaz.

    1. Pokud není nainstalován žádný balíček Java, zadejte následující příkaz:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Otevřete soubor bashrc (často se nachází v ~/.bashrc): `nano ~/.bashrc` .

    1. Opravte soubor bashrc. Na konec souboru přidejte následující řádek:

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        Pak stiskněte klávesy **CTRL + X**, pak **Y** a potom zadejte.

1. Stáhněte si archivy Hadoop a Beeline, zadejte následující příkazy:

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. Rozbalte archivy a zadejte následující příkazy:

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. Dále opravte soubor bashrc. Budete muset určit cestu, kam se archivy nebalí. Pokud používáte [subsystém Windows pro Linux](/windows/wsl/install-win10)a provedli jste přesně tento postup, vaše cesta by měla být `/mnt/c/Users/user/` , kde `user` je vaše uživatelské jméno.

    1. Otevřete soubor: `nano ~/.bashrc`

    1. Níže uvedené příkazy upravte podle příslušné cesty a pak je zadejte na konci souboru bashrc:

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. Pak stiskněte klávesy **CTRL + X**, pak **Y** a potom zadejte.

1. Zavřete a znovu otevřete relaci bash.

1. Otestujte připojení. Použijte formát připojení z [více než veřejných nebo privátních koncových bodů](#over-public-or-private-endpoints)výše.

## <a name="next-steps"></a>Další kroky

* Příklady použití klienta Beeline s Apache Hive najdete v tématu [použití Apache Beeline s Apache Hive](apache-hadoop-use-hive-beeline.md)
* Obecnější informace o podregistru v HDInsight najdete v tématu [použití Apache Hive s Apache Hadoop v HDInsight](hdinsight-use-hive.md) .