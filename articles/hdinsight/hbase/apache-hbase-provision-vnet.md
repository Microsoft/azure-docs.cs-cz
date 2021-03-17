---
title: Vytváření clusterů HBA v Virtual Network – Azure
description: Začněte používat HBA ve službě Azure HDInsight. Naučte se vytvářet clustery HDInsight HBA na Azure Virtual Network.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/23/2019
ms.openlocfilehash: 9f179981aa39402681b4830d58a29f5b1259c7e2
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946123"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Vytváření clusterů Apache HBA v HDInsight v Azure Virtual Network

Naučte se vytvářet clustery Azure HDInsight Apache HBA v [Virtual Network Azure](https://azure.microsoft.com/services/virtual-network/).

Díky integraci virtuální sítě můžete clustery Apache HBA nasadit do stejné virtuální sítě jako své aplikace, aby aplikace mohly komunikovat s adaptéry HBA přímo. Nabízí například tyto výhody:

* Přímá konektivita webové aplikace v uzlech clusteru HBA, který umožňuje komunikaci prostřednictvím rozhraní API vzdáleného volání procedur (RPC) Java.
* Vylepšený výkon tím, že není přenos přes několik bran a nástrojů pro vyrovnávání zatížení.
* Možnost zpracovávat citlivé informace zabezpečeným způsobem bez odhalení veřejného koncového bodu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Vytvoření clusteru Apache HBA do virtuální sítě

V této části vytvoříte cluster Apache HBA založený na systému Linux s závislým Azure Storage účtem ve službě Azure Virtual Network pomocí [šablony Azure Resource Manager](../../azure-resource-manager/templates/deploy-powershell.md). Další metody vytváření clusterů a porozumění nastavením najdete v tématu [Vytvoření clusterů HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Další informace o použití šablony k vytvoření Apache Hadoop clusterů ve službě HDInsight najdete v tématu [vytvoření Apache Hadoop clusterů ve službě HDInsight pomocí šablon Azure Resource Manager](../hdinsight-hadoop-create-linux-clusters-arm-templates.md) .

> [!NOTE]  
> Některé vlastnosti jsou pevně zakódované do šablony. Příklad:
>
> * **Umístění**: východní USA 2
> * **Verze clusteru**: 3.6
> * **Počet uzlů pracovního procesu clusteru**: 2
> * **Výchozí účet úložiště**: jedinečný řetězec
> * **Název virtuální sítě**: název_clusteru-VNet
> * **Adresní prostor virtuální sítě**: 10.0.0.0/16
> * **Název podsítě**: SUBNET1
> * **Rozsah adres podsítě**: 10.0.0.0/24
>
> `CLUSTERNAME` je nahrazen názvem clusteru, který zadáte při použití šablony.

1. Výběrem následujícího obrázku otevřete šablonu v Azure Portal. Šablona se nachází v [šablonách rychlý Start pro Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. V dialogovém okně **vlastní nasazení** vyberte **Upravit šablonu**.

1. Na řádku 165 změňte hodnotu `Standard_A3` na `Standard_A4_V2` . Pak vyberte **Uložit**.

1. Dokončete zbývající šablonu s následujícími informacemi:

    |Vlastnost |Hodnota |
    |---|---|
    |Předplatné|Vyberte předplatné Azure, které se používá k vytvoření clusteru HDInsight, závislého účtu úložiště a virtuální sítě Azure.|
    Skupina prostředků|Vyberte **vytvořit novou** a zadejte název nové skupiny prostředků.|
    |Umístění|Vyberte umístění skupiny prostředků.|
    |Název clusteru|Zadejte název clusteru Hadoop, který se má vytvořit.|
    |Uživatelské jméno a heslo pro přihlášení ke clusteru|Výchozí uživatelské jméno je **admin**. Zadejte heslo.|
    |Uživatelské jméno a heslo SSH|Výchozí uživatelské jméno je **sshuser**.  Zadejte heslo.|

    Vyberte Souhlasím **s podmínkami a výše uvedenými podmínkami**.

1. Vyberte **Koupit**. Vytvoření clusteru trvá přibližně 20 minut. Po vytvoření clusteru můžete vybrat cluster na portálu a otevřít ho.

Po dokončení tohoto článku budete možná chtít cluster odstranit. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány. Pokyny k odstranění clusteru najdete v tématu [správa Apache Hadoop clusterů ve službě HDInsight pomocí Azure Portal](../hdinsight-administer-use-portal-linux.md#delete-clusters).

Pokud chcete začít pracovat s novým clusterem adaptérů HBA, můžete použít postupy, které najdete v části [Začínáme s používáním prostředí Apache HBA s Apache Hadoop v HDInsight](./apache-hbase-tutorial-get-started-linux.md).

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Připojení ke clusteru Apache HBA pomocí rozhraní Apache HBA Java RPC API

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač infrastruktury jako služby (IaaS) ve stejné podsíti Azure a stejné podsíti. Pokyny k vytvoření nového virtuálního počítače s IaaS najdete v tématu [Vytvoření virtuálního počítače s Windows serverem](../../virtual-machines/windows/quick-create-portal.md). Pokud budete postupovat podle kroků v tomto dokumentu, musíte pro konfiguraci sítě použít následující hodnoty:

* **Virtuální síť**: název_clusteru-VNet
* **Podsíť**: SUBNET1

> [!IMPORTANT]  
> Nahraďte `CLUSTERNAME` názvem, který jste použili při vytváření clusteru HDInsight v předchozích krocích.

Pomocí těchto hodnot se virtuální počítač umístí do stejné virtuální sítě a podsítě jako cluster HDInsight. Tato konfigurace umožňuje, aby mezi sebou navzájem komunikovala přímo. Existuje způsob, jak vytvořit cluster HDInsight s prázdným hraničním uzlem. Hraniční uzel lze použít ke správě clusteru.  Další informace najdete v tématu [použití prázdných hraničních uzlů v HDInsight](../hdinsight-apps-use-edge-node.md).

### <a name="obtain-fully-qualified-domain-name"></a>Získání plně kvalifikovaného názvu domény

Pokud se k připojení k adaptérům HBA vzdáleně připojujete pomocí aplikace Java, je nutné použít plně kvalifikovaný název domény (FQDN). Chcete-li to zjistit, je třeba získat příponu DNS pro adaptéry pro připojení clusteru HBA. K tomu můžete použít jednu z následujících metod:

* Pomocí webového prohlížeče proveďte volání [Apache Ambari](https://ambari.apache.org/) :

    Přejděte na adresu `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts?minimal_response=true`. Vrátí soubor JSON s příponami DNS.

* Použijte web Ambari:

    1. Přejděte na adresu `https://CLUSTERNAME.azurehdinsight.net`.
    2. V horní nabídce vyberte **hostitelé** .

* Pro volání REST použijte oblé:

    ```bash
    curl -u <username>:<password> -k https://CLUSTERNAME.azurehdinsight.net/ambari/api/v1/clusters/CLUSTERNAME.azurehdinsight.net/services/hbase/components/hbrest
    ```

V vrácených datech JavaScript Object Notation (JSON) vyhledejte položku "host_name". Obsahuje plně kvalifikovaný název domény pro uzly v clusteru. Příklad:

```
"host_name" : "hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net"
```

Část názvu domény začínající názvem clusteru je přípona DNS. Například `hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

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

Pokud chcete ověřit, jestli virtuální počítač může komunikovat s clusterem HBA, použijte příkaz `ping headnode0.<dns suffix>` z virtuálního počítače. Například `ping hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

Pokud chcete tyto informace použít v aplikaci Java, můžete postupovat podle kroků v části [použití Apache Maven k vytváření aplikací v jazyce Java, které používají Apache HBA s HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md) k vytvoření aplikace. Chcete-li aplikaci připojit ke vzdálenému serveru HBA, upravte soubor **hbase-site.xml** v tomto příkladu tak, aby používal plně kvalifikovaný název domény pro Zookeeper. Příklad:

```xml
<property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
</property>
```

> [!NOTE]  
> Další informace o překladu názvů ve virtuálních sítích Azure, včetně toho, jak používat vlastní server DNS, najdete v tématu [překlad IP adres (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak vytvořit cluster Apache HBA. Další informace najdete v následujících tématech:

* [Začínáme se službou HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Použití prázdných hraničních uzlů v HDInsight](../hdinsight-apps-use-edge-node.md)
* [Konfigurace replikace Apache HBA v HDInsight](apache-hbase-replication.md)
* [Vytváření clusterů Apache Hadoop ve službě HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Začínáme používat Apache HBA s Apache Hadoop ve službě HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Přehled Virtual Network](../../virtual-network/virtual-networks-overview.md)
