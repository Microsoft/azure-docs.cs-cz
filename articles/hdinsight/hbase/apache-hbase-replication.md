---
title: Nastavení replikace clusteru HBase v Azure virtuální sítě | Microsoft Docs
description: Zjistěte, jak nastavit replikace HBase z jedné verze HDInsight do jiného pro vyrovnávání zatížení, vysokou dostupnost, nula. výpadků migrace a aktualizací a zotavení po havárii.
services: hdinsight,virtual-network
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: c28c48b5842deec9d9c3898c5742c3d4d473094e
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="set-up-hbase-cluster-replication-in-azure-virtual-networks"></a>Nastavení replikace clusteru HBase v Azure virtuální sítě

Zjistěte, jak nastavit replikace HBase v rámci virtuální sítě, nebo mezi dvě virtuální sítě v Azure.

Clusterová replikace používá metodika nabízené zdroje. HBase cluster může být zdroj nebo cíl, nebo ji můžete najednou splnění obě role. Replikace je asynchronní. Cílem replikace je konzistence typu případné. Když zdroj obdrží upravíte sloupec rodině, pokud je zapnutá replikace, úpravy rozšířena do všech cílových clusterech. Pokud data se replikují z jednoho clusteru do druhého, zdrojovém clusteru a všechny clustery, které jste již využívat data jsou sledovány, aby se zabránilo replikační cykly.

V tomto kurzu nastavíte zdroj cíl replikace. Jiných topologiích clusteru najdete v článku [referenční příručka Apache HBase](http://hbase.apache.org/book.html#_cluster_replication).

Případy použití replikace HBase jedné virtuální sítě jsou následující:

* Vyrovnávání zatížení. Můžete například spustit úlohy MapReduce nebo prohledávání v cílovém clusteru a ingestovat data ve zdrojovém clusteru.
* Přidání vysokou dostupnost.
* Migraci dat z jednoho clusteru HBase do jiného.
* Upgrade clusteru Azure HDInsight z jedné verze do jiné.

Případy využití HBase replikace pro dvě virtuální sítě jsou následující:

* Nastavení služby zotavení po havárii.
* Vyrovnávání zatížení a rozdělení do oddílů aplikace.
* Přidání vysokou dostupnost.

Při replikaci clustery pomocí [skript akce](../hdinsight-hadoop-customize-cluster-linux.md) skriptů z [Githubu](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Požadavky
Než začnete tento kurz, musíte mít předplatné Azure. V tématu [získat bezplatnou zkušební verzi Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="set-up-the-environments"></a>Nastavení prostředí

Máte tři možnosti konfigurace:

- Dva clustery HBase v jednu virtuální síť Azure.
- Dva clustery HBase ve dvou různých virtuálních sítích ve stejné oblasti.
- Dva clustery HBase ve dvou různých virtuálních sítích v různých oblastech dva (geografická replikace).

Můžete nastavit prostředích, jsme vytvořili některé [šablon Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md). Pokud dáváte přednost nastavení prostředí pomocí jiné metody, najdete v části:

- [Vytvoření clusterů systému Hadoop v HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
- [Vytvořit clustery HBase v Azure Virtual Network](apache-hbase-provision-vnet.md)

### <a name="set-up-one-virtual-network"></a>Nastavit jednu virtuální síť

Chcete-li vytvořit dva clustery HBase ve stejné virtuální síti, vyberte na následujícím obrázku. Šablona je uložena v [šablony Azure QuickStart](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-one-vnet/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-one-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

### <a name="set-up-two-virtual-networks-in-the-same-region"></a>Nastavení dvou virtuálních sítí ve stejné oblasti

Chcete-li vytvořit dvě virtuální sítě pomocí virtuální sítě partnerský vztah a dva clustery HBase ve stejné oblasti, vyberte na následujícím obrázku. Šablona je uložena v [šablony Azure QuickStart](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-two-vnets-same-region/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-two-vnets-same-region%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>



Tento scénář vyžaduje [partnerský vztah virtuální sítě](../../virtual-network/virtual-network-peering-overview.md). Šablona umožňuje partnerský vztah virtuální sítě.   

Replikace HBase používá IP adresy virtuálních počítačů ZooKeeper. Musíte vytvořit statických IP adres pro cílový HBase ZooKeeper uzlů.

**Konfigurace statické IP adresy**

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo vyberte **skupiny prostředků**.
3. Vyberte skupinu prostředků, který má cílový cluster HBase. Toto je skupina prostředků, které jste zadali při jste použili k vytvoření prostředí šablony Resource Manageru. Chcete-li zúžit seznam můžete použít filtr. Zobrazí seznam prostředků, které obsahují dvě virtuální sítě.
4. Vyberte virtuální síť, která obsahuje cílový cluster HBase. Vyberte například **xxxx-vnet2**. Tři zařízení s názvy, které začínají **seskupování-zookeepermode -** jsou uvedeny. Tato zařízení jsou tři ZooKeeper virtuálních počítačů.
5. Vyberte jednu z ZooKeeper virtuálních počítačů.
6. Vyberte **konfigurace protokolu IP**.
7. V seznamu, vyberte **ipConfig1**.
8. Vyberte **statické**a zkopírovat nebo zapište skutečné IP adresu. Při spuštění akce skriptu k povolení replikace musíte IP adresu.

  ![HDInsight HBase replikace ZooKeeper statickou IP adresu](./media/apache-hbase-replication/hdinsight-hbase-replication-zookeeper-static-ip.png)

9. Zopakujte krok 6 nastavit statickou IP adresu pro další dva uzly ZooKeeper.

Pro scénář mezi virtuální sítě, je nutné použít **- ip** přepínače při volání `hdi_enable_replication.sh` skript akce.

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Nastavení ve dvou různých oblastech dvě virtuální sítě

Chcete-li vytvořit dvě virtuální sítě ve dvou různých oblastech a připojení VPN mezi virtuální sítě, klikněte na následující obrázek. Šablona je uložena v [šablon Azure rychlý Start](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-geo%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

Některé hodnoty pevně v šabloně:

**1 virtuální sítě**

| Vlastnost | Hodnota |
|----------|-------|
| Umístění | Západní USA |
| Název virtuální sítě | &lt;ClusterNamePrevix >-vnet1 |
| Předponu adresního prostoru | 10.1.0.0/16 |
| Název podsítě | podsíť 1 |
| Předpona podsítě | 10.1.0.0/24 |
| Název podsítě (brány) | GatewaySubnet (nelze změnit) |
| Předpona podsítě (brány) | 10.1.255.0/27 |
| Název brány | vnet1gw |
| Typ brány | Vpn |
| Typ brány sítě VPN | RouteBased |
| Skladová položka brány | Basic |
| IP brány | vnet1gwip |
| Název clusteru | &lt;ClusterNamePrefix > 1 |
| Verze clusteru | 3.6 |
| Typ clusteru | hbase |
| Počet uzlů pracovního procesu clusteru | 2 |


**Virtuální sítě 2**

| Vlastnost | Hodnota |
|----------|-------|
| Umístění | Východ USA |
| Název virtuální sítě | &lt;ClusterNamePrevix >-vnet2 |
| Předponu adresního prostoru | 10.2.0.0/16 |
| Název podsítě | podsíť 1 |
| Předpona podsítě | 10.2.0.0/24 |
| Název podsítě (brány) | GatewaySubnet (nelze změnit) |
| Předpona podsítě (brány) | 10.2.255.0/27 |
| Název brány | vnet2gw |
| Typ brány | Vpn |
| Typ brány sítě VPN | RouteBased |
| Skladová položka brány | Basic |
| IP brány | vnet1gwip |
| Název clusteru | &lt;ClusterNamePrefix > 2 |
| Verze clusteru | 3.6 |
| Typ clusteru | hbase |
| Počet uzlů pracovního procesu clusteru | 2 |

Replikace HBase používá IP adresy virtuálních počítačů ZooKeeper. Musíte vytvořit statických IP adres pro cílový HBase ZooKeeper uzlů. Chcete-li nastavit statickou IP adresu, přečtěte si téma [nastavení dvou virtuálních sítí ve stejné oblasti](#set-up-two-virtual-networks-in-the-same-region) v tomto článku.

Pro scénář mezi virtuální sítě, je nutné použít **- ip** přepínače při volání `hdi_enable_replication.sh` skript akce.

## <a name="load-test-data"></a>Testovací data zatížení

Při replikaci cluster, je nutné zadat tabulky, které chcete replikovat. V této části načíst některá data do zdrojového clusteru. V další části povolíte replikaci mezi dvěma clustery.

Chcete-li vytvořit **kontakty** tabulku a vložte některá data v tabulce, postupujte podle pokynů v [kurz HBase: začněte používat Apache HBase v HDInsight](apache-hbase-tutorial-get-started-linux.md).

## <a name="enable-replication"></a>Povolení replikace

Následující kroky popisují, jak volat skript akce skriptu z portálu Azure. Informace o spuštění akce skriptu pomocí prostředí Azure PowerShell a nástroje příkazového řádku Azure (Azure CLI) najdete v tématu [clusterů HDInsight přizpůsobit pomocí akce skriptu](../hdinsight-hadoop-customize-cluster-linux.md).

**Povolení replikace HBase z portálu Azure**

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Otevřete zdrojový cluster HBase.
3. V nabídce clusteru vyberte **akcí skriptů**.
4. V horní části stránky, vyberte **odeslání nové**.
5. Vyberte nebo zadejte následující informace:

  1. **Název**: Zadejte **povolit replikaci**.
  2. **Adresa URL skriptu bash**: Zadejte **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**.
  3.  **HEAD**: Ujistěte se, tato možnost je vybrána. Zrušte jiné typy uzlů.
  4. **Parametry**: Následující ukázka parametry povolit replikaci pro všechny existující tabulky a poté zkopírujte všechna data ze zdrojového clusteru do cílového clusteru:

          -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
    >[!note]
    >
    > Název hostitele použijte místo plně kvalifikovaný název domény pro zdrojový i cílový název DNS clusteru.

6. Vyberte **Vytvořit**. Skript může trvat ke spuštění, zejména při použití **- programu copydata** argument.

Vyžaduje argumenty:

|Název|Popis|
|----|-----------|
|-s, – src-cluster | Určuje název DNS clusteru HBase zdroje. Příklad: hbsrccluster -s, cluster – src = hbsrccluster |
|-d, – dst clusteru | Určuje název DNS cílového (replikovaného) clusteru HBase. Příklad: dsthbcluster -s, cluster – src = dsthbcluster |
|-sp, – src-ambari heslo | Určuje heslo správce pro Ambari ve zdrojovém clusteru HBase. |
|-distribučního bodu, – dst-ambari heslo | Určuje heslo správce pro Ambari v cílovém clusteru HBase.|

Nepovinné argumenty:

|Název|Popis|
|----|-----------|
|-su, – src-ambari-user | Určuje uživatelské jméno správce pro Ambari ve zdrojovém clusteru HBase. Výchozí hodnota je **správce**. |
|-du – dst-ambari-user | Určuje uživatelské jméno správce pro Ambari v cílovém clusteru HBase. Výchozí hodnota je **správce**. |
|-t, – seznam tabulek | Určuje tabulky, které chcete replikovat. Příklad: – seznam tabulek = "tabulky1; tabulky2; Tabulka3". Pokud nezadáte tabulky, replikují se všechny existující tabulky HBase.|
|-m, – počítač | Určuje hlavního uzlu, kde je spuštěna akce skriptu. Hodnota je buď **hn1** nebo **hn0**. Protože **hn0** hlavního uzlu je obvykle Vytíženější, doporučujeme používat **hn1**. Tuto možnost použijte, když spouštíte skript $0 jako akce skriptu z portálu HDInsight nebo Azure PowerShell.|
|-ip | Povinné v případě, že povolení replikace mezi dvěma virtuálními sítěmi. Tento argument funguje jako přepínač pro používání statických IP adres ZooKeeper uzlů z clusterů repliky namísto názvů plně kvalifikovaný název domény. Statické IP adresy musí předkonfiguroval před povolením replikace. |
|-prohlášení cp, - programu copydata | Umožňuje migraci stávající data v tabulkách, kde je povolená replikace. |
|-ot. / min, - replikovat-phoenix-metadat | Zapne replikaci na Phoenix systémové tabulky. <br><br>*Tuto možnost použijte opatrně.* Doporučujeme, abyste před použitím tohoto skriptu znovu vytvořit Phoenix tabulek v clusterech repliky. |
|-h, – Nápověda | Zobrazí informace o využití. |

`print_usage()` Části [skriptu](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) obsahuje podrobné vysvětlení parametrů.

Po úspěšném nasazení akce skriptu, můžete pro připojení k cílový cluster HBase a potom ověřte, že data se replikují SSH.

### <a name="replication-scenarios"></a>Scénáře replikace

V následujícím seznamu jsou někdy obecné využití a jejich nastavení parametrů:

- **Povolení replikace ve všech tabulkách mezi dvěma clustery**. Tento scénář nevyžaduje kopírování nebo migraci stávající data v tabulkách a nepoužívá Phoenix tabulky. Použijte následující parametry:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Povolit replikaci na konkrétní tabulky**. Chcete-li povolit replikaci na tabulky1 a tabulky2, tabulka3, použijte následující parametry:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Povolit replikaci na konkrétní tabulky a zkopírujte existující data**. Chcete-li povolit replikaci na tabulky1 a tabulky2, tabulka3, použijte následující parametry:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Povolit replikaci pro všechny tabulky a replikovat Phoenix metadat ze zdrojového do cílového umístění**. Phoenix metadata replikace není ideální. Používejte s opatrní. Použijte následující parametry:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Zkopírujte a migraci dat

Nejsou k dispozici pro kopírování nebo migraci dat po povolení replikace dva samostatné skript akce skripty:

- [Skript pro malé tabulky](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (tabulky, které jsou několika gigabajtů velikost a celková kopie očekává se dokončit za méně než jednu hodinu)

- [Skript pro rozsáhlé tabulky](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (tabulek, které se očekává trvá déle než jednu hodinu kopírování)

Můžete postupujte stejným způsobem, který je popsán v [povolit replikaci](#enable-replication) volání akce skriptu. Použijte následující parametry:

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

`print_usage()` Části [skriptu](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) obsahuje podrobný popis parametrů.

### <a name="scenarios"></a>Scénáře

- **Zkopírujte konkrétní tabulky (test1, test2 a test3) pro všechny řádky upravit až doteď (aktuální časové razítko)**:

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  nebo:

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Zkopírujte konkrétní tabulky s zadaný časový rozsah**:

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>Zákaz replikace

Zakázat replikace, použijte jiný skript akce skriptu z [Githubu](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). Můžete postupujte stejným způsobem, který je popsán v [povolit replikaci](#enable-replication) volání akce skriptu. Použijte následující parametry:

    -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

`print_usage()` Části [skriptu](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) obsahuje podrobné vysvětlení parametrů.

### <a name="scenarios"></a>Scénáře

- **Zakázat replikaci ve všech tabulkách**:

        -m hn1 -s <source cluster DNS name> -sp Mypassword\!789 -all
  nebo

        --src-cluster=<source cluster DNS name> --dst-cluster=<destination cluster DNS name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **Zakázat replikaci na zadaných tabulek (tabulky1, tabulky2 a tabulka3)**:

        -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak nastavit replikace HBase v rámci virtuální sítě, nebo mezi dvě virtuální sítě. Další informace o HDInsight a HBase, najdete v těchto článcích:

* [Začínáme s Apache HBase v HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Přehled HDInsight HBase](./apache-hbase-overview.md)
* [Vytvořit clustery HBase v Azure Virtual Network](./apache-hbase-provision-vnet.md)

