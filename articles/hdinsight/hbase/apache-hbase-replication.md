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
ms.date: 05/11/2018
ms.author: jgao
ms.openlocfilehash: 56b2b5ae9d3e4a0e682ec3dd47cd5cc30ebf6d58
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077322"
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

Tento článek popisuje scénář geografická replikace.

Můžete nastavit prostředích, jsme vytvořili některé [šablon Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md). Pokud dáváte přednost nastavení prostředí pomocí jiné metody, najdete v části:

- [Vytvoření clusterů systému Hadoop v HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
- [Vytvořit clustery HBase v Azure Virtual Network](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Nastavení ve dvou různých oblastech dvě virtuální sítě

Vytvořit dvě virtuální sítě ve dvou různých oblastech a připojení VPN mezi virtuální sítě, vyberte následující obrázek, který má vytvořit. Šablona je uložena v [veřejného objektu blob úložiště]] (https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

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

## <a name="setup-dns"></a>Instalační program DNS

V poslední části vytvoří šablona virtuálního počítače Ubuntu v každé dvě virtuální sítě.  V této části nainstalujte vazby na dva virtuální počítače DNS a pak nakonfigurujte předávání DNS na dva virtuální počítače.

Chcete-li nainstalovat vazby, yon muset najít veřejnou IP adresu dva virtuální počítače DNS.

1. Otevřete web [Azure Portal](https://portal.azure.com).
2. Otevřete DNS virtuálního počítače tak, že vyberete **skupin prostředků > [název skupiny prostředků] > [vnet1DNS]**.  Název skupiny prostředků je ten, který vytvoříte v posledním postupu. Výchozí názvy DNS virtuálního počítače jsou *vnet1DNS* a *vnet2NDS*.
3. Vyberte **vlastnosti** chcete otevřít stránku Vlastnosti virtuální sítě.
4. Zapište **veřejnou IP adresu**a také ověřte **privátní IP adresa**.  Privátní IP adresa musí být **10.1.0.4** pro vnet1DNS a **10.2.0.4** pro vnet2DNS.  

K instalaci vazby, použijte následující postup:

1. Použití SSH se připojit k __veřejnou IP adresu__ DNS virtuálního počítače. Následující příklad se připojí k virtuálnímu počítači na 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Nahraďte `sshuser` k uživatelskému účtu SSH, který jste zadali při vytváření virtuálního počítače DNS.

    > [!NOTE]
    > Existuje mnoho různých způsobů, jak získat `ssh` nástroj. Na systému Linux, Unix a systému macOS je poskytována jako součást operačního systému. Pokud používáte systém Windows, zvažte jednu z následujících možností:
    >
    > * [Prostředí cloudu Azure](../../cloud-shell/quickstart.md)
    > * [Bash na Ubuntu na Windows 10](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Chcete-li nainstalovat vazby, použijte následující příkazy z relace SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Ke konfiguraci vazby k předávání žádostí o překlad názvu na místní server DNS, použijte následující text jako obsah `/etc/bind/named.conf.options` souboru:

    ```
    acl goodclients {
        10.1.0.0/16; # Replace with the IP address range of the virtual network 1
        10.2.0.0/16; # Replace with the IP address range of the virtual network 2
        localhost;
        localhost;
    };
    
    options {
        directory "/var/cache/bind";
        recursion yes;
        allow-query { goodclients; };

        forwarders {
            168.63.129.16 #This is the Azure DNS server
        };

        dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
    };
    ```
    
    > [!IMPORTANT]
    > Nahraďte hodnoty v `goodclients` oddíl s rozsahem IP adres dvě virtuální sítě. Tento oddíl definuje adresy, které tento server DNS přijímá požadavky od.

    K úpravě tohoto souboru, použijte následující příkaz:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Chcete-li uložit soubor, použijte __Ctrl + X__, __Y__a potom __Enter__.

4. Z relace SSH použijte následující příkaz:

    ```bash
    hostname -f
    ```

    Tento příkaz vrátí hodnotu podobná následující text:

        vnet1DNS.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` Text je __příponu DNS__ pro tuto virtuální síť. Tato hodnota, uložte, protože se později používá.

    Musíte také zjistit přípony DNS z jiný server DNS. Budete ho potřebovat v dalším kroku.

5. Ke konfiguraci vazby k překladu názvů DNS pro prostředky v rámci virtuální sítě, použijte následující text jako obsah `/etc/bind/named.conf.local` souboru:

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]
    > Musíte `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` s příponou DNS ve virtuální síti. A předávání IP je privátní IP adresa serveru DNS ve virtuální síti.

    K úpravě tohoto souboru, použijte následující příkaz:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Chcete-li uložit soubor, použijte __Ctrl + X__, __Y__a potom __Enter__.

6. Pokud chcete spustit vazby, použijte následující příkaz:

    ```bash
    sudo service bind9 restart
    ```

7. Pokud chcete ověřit, že vazby může překládat názvy prostředků ve virtuální síti, použijte následující příkazy:

    ```bash
    sudo apt install dnsutils
    nslookup vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net 10.2.0.4
    ```

    > [!IMPORTANT]
    > Nahraďte `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` s plně kvalifikovaný název domény (FQDN) virtuálního počítače DNS v jiné síti.
    >
    > Nahraďte `10.2.0.4` s __interní IP adresu__ vašeho vlastního serveru DNS ve virtuální síti.

    Odpověď se zobrazí podobná následující text:

    ```
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    Až do této chvíle nelze vyhledat IP adresu ze sítě bez zadaná IP adresa serveru DNS.

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Konfigurace virtuální sítě pro použití vlastního serveru DNS

Ke konfiguraci virtuální sítě pro použití vlastního serveru DNS místo Azure rekurzivní překladač, použijte následující kroky:

1. V [portál Azure](https://portal.azure.com), vyberte virtuální síť a potom vyberte __servery DNS__.

2. Vyberte __vlastní__a zadejte __interní IP adresu__ vlastního serveru DNS. Nakonec vyberte __Uložit__.

6. Otevřete virtuální počítač serveru DNS v vnet1 a klikněte na tlačítko **restartujte**.  Ve virtuální síti, aby konfiguraci serveru DNS se projeví po restartování všech virtuálních počítačů.
7. Zopakujte kroky konfigurace vlastního serveru DNS pro vnet2.

K testování této konfigurace DNS, můžete připojit na dva virtuální počítače DNS pomocí protokolu SSH a příkaz ping serveru DNS virtuální sítě pomocí jeho názvu hostitele. Pokud nefunguje, použijte následující příkaz a zkontrolujte stav DNS:

```bash
sudo service bind9 status
```

## <a name="create-hbase-clusters"></a>Vytvořit clustery HBase

Vytvoření clusteru HBase v každé dvě virtuální sítě s následující konfigurací:

- **Název skupiny prostředků**: použijte stejný název skupiny prostředků, jako jste vytvořili virtuální sítě.
- **Typ clusteru**: HBase
- **Verze**: HBase 1.1.2 (HDI 3.6)
- **Umístění**: používalo stejné umístění jako virtuální síť.  Ve výchozím nastavení je vnet1 *západní USA*, a je vnet2 *východní USA*.
- **Úložiště**: Vytvořte nový účet úložiště pro cluster.
- **Virtuální síť** (z pokročilé nastavení na portálu): Vyberte vnet1 jste vytvořili v posledním postupu.
- **Podsíť**: výchozí název použité v šabloně je **subnet1**.

Aby byl že správně nakonfigurován prostředí, musí umět příkaz ping headnode plně kvalifikovaný název domény mezi dvěma clustery.

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

