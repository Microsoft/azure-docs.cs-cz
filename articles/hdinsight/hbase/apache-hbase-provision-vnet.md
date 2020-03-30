---
title: Vytvoření clusterů HBase ve virtuální síti – Azure
description: Začínáme používat HBase v Azure HDInsight. Přečtěte si, jak vytvořit clustery HDInsight HBase ve virtuální síti Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/23/2019
ms.openlocfilehash: e4e15d1c6554fc567f668b2033bff5b5664db918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972793"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Vytvoření clusterů Apache HBase na HDInsight u virtuální sítě Azure

Přečtěte si, jak vytvořit clustery Azure HDInsight Apache HBase ve [virtuální síti Azure](https://azure.microsoft.com/services/virtual-network/).

Díky integraci virtuálních sítí lze clustery Apache HBase nasadit do stejné virtuální sítě jako vaše aplikace, aby aplikace mohly komunikovat přímo s HBase. Nabízí například tyto výhody:

* Přímé připojení webové aplikace k uzlům clusteru HBase, které umožňuje komunikaci prostřednictvím rozhraní API vzdáleného volání procedur HBase Java (RPC).
* Zlepšený výkon tím, že váš provoz neprochází přes více bran a nástroj pro vyrovnávání zatížení.
* Schopnost zpracovávat citlivé informace bezpečnějším způsobem bez vystavení veřejného koncového bodu.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Vytvoření clusteru Apache HBase do virtuální sítě

V této části vytvoříte cluster Apache HBase založený na Linuxu se závislým účtem Azure Storage ve virtuální síti Azure pomocí [šablony Azure Resource Manager](../../azure-resource-manager/templates/deploy-powershell.md). Další metody vytváření clusteru a pochopení nastavení najdete v tématu [Vytvoření clusterů HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Další informace o použití šablony k vytvoření clusterů Apache Hadoop ve hdinsightu najdete v tématu [Vytváření clusterů Apache Hadoop ve službě HDInsight pomocí šablon Azure Resource Manageru.](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]  
> Některé vlastnosti jsou pevně zakódovány do šablony. Například:
>
> * **Umístění**: Východní USA 2
> * **Verze clusteru**: 3.6
> * **Počet pracovních uzlů clusteru**: 2
> * **Výchozí účet úložiště**: jedinečný řetězec
> * **Název virtuální sítě**: CLUSTERNAME-vnet
> * **Adresní prostor virtuální sítě**: 10.0.0.0/16
> * **Název podsítě**: podsíť1
> * **Rozsah adres podsítě**: 10.0.0.0/24
>
> `CLUSTERNAME`je nahrazen názvem clusteru, který zadáte při použití šablony.

1. Kliknutím na následující obrázek otevřete šablonu na webu Azure Portal. Šablona se nachází v [šablonách azure quickstart](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. V dialogovém okně **Vlastní nasazení** vyberte **Upravit šablonu**.

1. Na řádku 165 `Standard_A3` změňte hodnotu na `Standard_A4_V2`. Potom vyberte **Uložit**.

1. Zbývající šablonu doplňte následujícími informacemi:

    |Vlastnost |Hodnota |
    |---|---|
    |Předplatné|Vyberte předplatné Azure, které se používá k vytvoření clusteru HDInsight, závislého účtu úložiště a virtuální sítě Azure.|
    Skupina prostředků|Vyberte **Vytvořit nový**a zadejte nový název skupiny prostředků.|
    |Umístění|Vyberte umístění skupiny prostředků.|
    |Název clusteru|Zadejte název pro vytvoření clusteru Hadoop.|
    |Uživatelské jméno a heslo přihlášení k clusteru|Výchozí uživatelské jméno je **admin**. Zadejte heslo.|
    |Uživatelské jméno a heslo SSH|Výchozí uživatelské jméno je **sshuser**.  Zadejte heslo.|

    Vyberte **Souhlasím s podmínkami a podmínkami uvedenými výše**.

1. Vyberte **Koupit**. Vytvoření clusteru trvá přibližně 20 minut. Po vytvoření clusteru můžete vybrat cluster na portálu a otevřít jej.

Po dokončení článku můžete chtít odstranit cluster. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány. Pokyny k odstranění clusteru najdete v tématu [Správa clusterů Apache Hadoop v HDInsightu pomocí portálu Azure](../hdinsight-administer-use-portal-linux.md#delete-clusters).

Chcete-li začít pracovat s novým clusterem HBase, můžete použít postupy nalezené v [začínáme používat Apache HBase s Apache Hadoop v HDInsight](./apache-hbase-tutorial-get-started-linux.md).

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Připojení ke clusteru Apache HBase pomocí hraničních urážlivých zařízení Apache HBase Java

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte infrastrukturu jako službu (IaaS) virtuální počítač do stejné virtuální sítě Azure a stejné podsítě. Pokyny k vytvoření nového virtuálního počítače IaaS najdete [v tématu Vytvoření virtuálního počítače se systémem Windows Server](../../virtual-machines/windows/quick-create-portal.md). Při následujících krocích v tomto dokumentu je nutné použít následující hodnoty pro konfiguraci sítě:

* **Virtuální síť**: CLUSTERNAME-vnet
* **Podsíť**: podsíť1

> [!IMPORTANT]  
> Nahraďte `CLUSTERNAME` název, který jste použili při vytváření clusteru HDInsight v předchozích krocích.

Pomocí těchto hodnot se virtuální počítač umístí do stejné virtuální sítě a podsítě jako cluster HDInsight. Tato konfigurace jim umožňuje přímokomunikovat mezi sebou. Existuje způsob, jak vytvořit cluster HDInsight s prázdným hraničním uzlem. Hraniční uzel lze použít ke správě clusteru.  Další informace naleznete [v tématu Použití prázdných hraničních uzlů v HDInsight](../hdinsight-apps-use-edge-node.md).

### <a name="obtain-fully-qualified-domain-name"></a>Získání plně kvalifikovaného názvu domény

Při vzdáleném připojení k HBase pomocí java aplikace je nutné použít plně kvalifikovaný název domény (FQDN). Chcete-li to zjistit, musíte získat příponu DNS specifické pro připojení clusteru HBase. Chcete-li to provést, můžete použít jednu z následujících metod:

* Pomocí webového prohlížeče můžete volat [Apache Ambari:](https://ambari.apache.org/)

    Přejděte na `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts?minimal_response=true`. Vrátí soubor JSON s příponami DNS.

* Použijte webové stránky Ambari:

    1. Přejděte na `https://CLUSTERNAME.azurehdinsight.net`.
    2. V horní nabídce vyberte **Hostitelé.**

* Pomocí curlu můžete volat REST:

    ```bash
    curl -u <username>:<password> -k https://CLUSTERNAME.azurehdinsight.net/ambari/api/v1/clusters/CLUSTERNAME.azurehdinsight.net/services/hbase/components/hbrest
    ```

V vrácených datech zápisu objektu JavaScript (JSON) vyhledejte položku "host_name". Obsahuje hlavní název sítě pro uzly v clusteru. Například:

```
"host_name" : "hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net"
```

Část názvu domény začínající názvem clusteru je přípona DNS. Například, `hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix](./media/apache-hbase-provision-vnet/hdi-primary-dns-suffix.png)
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

### <a name="verify-communication-inside-virtual-network"></a>Ověření komunikace uvnitř virtuální sítě

Chcete-li ověřit, že virtuální počítač může komunikovat `ping headnode0.<dns suffix>` s clusterem HBase, použijte příkaz z virtuálního počítače. Například, `ping hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

Chcete-li tyto informace použít v aplikaci Java, můžete postupovat podle kroků v [části Použití Apache Maven k vytvoření java aplikací, které k vytvoření aplikace používají Apache HBase s HDInsight (Hadoop).](./apache-hbase-build-java-maven-linux.md) Chcete-li, aby se aplikace připojila ke vzdálenému serveru HBase, upravte soubor **hbase-site.xml** v tomto příkladu tak, aby používal hlavní název souboru Pro vydružitpro Zookeeper. Například:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]  
> Další informace o překladu názvů ve virtuálních sítích Azure, včetně použití vlastního serveru DNS, najdete [v tématu Překlad názvů (DNS).](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak vytvořit cluster Apache HBase. Další informace naleznete v tématu:

* [Začínáme se službou HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Použití prázdných hraničních uzlů v HDInsightu](../hdinsight-apps-use-edge-node.md)
* [Konfigurace replikace Apache HBase v HDInsightu](apache-hbase-replication.md)
* [Vytvoření clusterů Apache Hadoop v HDInsightu](../hdinsight-hadoop-provision-linux-clusters.md)
* [Začínáme používat Apache HBase s Apache Hadoop v HDInsightu](./apache-hbase-tutorial-get-started-linux.md)
* [Přehled virtuální sítě](../../virtual-network/virtual-networks-overview.md)
