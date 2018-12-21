---
title: Nastavení replikace clusteru HBase ve virtuálních sítích Azure – Azure HDInsight
description: Zjistěte, jak nastavit HBase replikace z jedné verze HDInsight do jiné služby Vyrovnávání zatížení, vysokou dostupnost, migrace s nulovými výpadky a aktualizace a zotavení po havárii.
services: hdinsight,virtual-network
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/15/2018
ms.openlocfilehash: b03cffe35337ee5720944dc4cfe88c17c3b5b748
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53163823"
---
# <a name="set-up-apache-hbase-cluster-replication-in-azure-virtual-networks"></a>Nastavení replikace clusteru Apache HBase ve virtuálních sítích Azure

Zjistěte, jak nastavit [Apache HBase](http://hbase.apache.org/) replikace v rámci virtuální sítě nebo mezi dvěma virtuálními sítěmi v Azure.

Replikace clusteru používá metodologie zdroj nabízené oznámení. HBase cluster může být zdroj nebo cíl, nebo může najednou splnit obě role. Je asynchronní replikace. Cílem replikace je konečné konzistence. Když zdroj obdrží úpravy do rodiny sloupců po povolení replikace, úpravy se šíří do všech cílových clusterech. Pokud data se replikují z jednoho clusteru do druhého, zdrojový cluster a všechny clustery, které jste už využili data jsou sledovány, aby se zabránilo replikační cykly.

V tomto kurzu nastavíte zdroj cíl replikace. Jiných topologiích clusteru najdete v článku [referenční příručka Apache HBase](http://hbase.apache.org/book.html#_cluster_replication).

Tady jsou případy využití HBase replikace pro jednu virtuální síť:

* Vyrovnávání zatížení. Například můžete spustit prohledávání nebo úlohy mapreduce je možné v cílovém clusteru a ingestovat data ve zdrojovém clusteru.
* Přidání vysokou dostupnost.
* Migrace dat z jednoho clusteru HBase na jiný.
* Upgrade clusteru služby HDInsight k Azure z jedné verze do jiného.

Tady jsou případy využití HBase replikace pro dvě virtuální sítě:

* Nastavení zotavení po havárii.
* Vyrovnávání zatížení a dělení aplikace.
* Přidání vysokou dostupnost.

Clustery můžete replikovat pomocí [skript akce](../hdinsight-hadoop-customize-cluster-linux.md) skriptům z [Githubu](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Požadavky
Než začnete tento kurz, musíte mít předplatné Azure. Zobrazit [získat bezplatnou zkušební verzi Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="set-up-the-environments"></a>Nastavení prostředí

Existují tři možnosti konfigurace:

- Dva clustery Apache HBase v jedné virtuální síti Azure.
- Dva clustery Apache HBase ve dvou různých virtuálních sítích ve stejné oblasti.
- Dva clustery Apache HBase ve dvou různých virtuálních sítích ve dvou různých oblastech (geografické replikace).

Tento článek popisuje scénář geografickou replikaci.

Můžete nastavovat prostředí, jsme vytvořili některé [šablon Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md). Pokud chcete nastavit prostředí pomocí jiné metody, naleznete v tématu:

- [Vytvořte clustery systému Apache Hadoop v HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
- [Vytvoření clusterů Apache HBase ve virtuální síti Azure](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Nastavit dvěma virtuálními sítěmi ve dvou různých oblastech

Chcete-li použít šablonu, která vytvoří dvě virtuální sítě ve dvou různých oblastech a připojení VPN mezi virtuálními sítěmi, vyberte následující **nasadit do Azure** tlačítko. Definice šablony jsou uloženy v [úložiště objektů blob veřejného](https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

Některé z hodnot pevně zakódované v šabloně:

**1 virtuální sítě**

| Vlastnost | Hodnota |
|----------|-------|
| Umístění | Západní USA |
| Název virtuální sítě | &lt;ClusterNamePrevix >-ze sítě vnet1 |
| Předponu adresního prostoru | 10.1.0.0/16 |
| Název podsítě | podsíť 1 |
| Předpona podsítě | 10.1.0.0/24 |
| Název podsítě (brány) | GatewaySubnet (nelze změnit) |
| Předpona podsítě (brány) | 10.1.255.0/27 |
| Název brány | brány vnet1gw |
| Typ brány | Vpn |
| Typ brány VPN | RouteBased |
| SKU brány | Basic |
| Brána IP | vnet1gwip |

**2 virtuální sítě**

| Vlastnost | Hodnota |
|----------|-------|
| Umístění | USA – východ |
| Název virtuální sítě | &lt;ClusterNamePrevix >-ze sítě vnet2 |
| Předponu adresního prostoru | 10.2.0.0/16 |
| Název podsítě | podsíť 1 |
| Předpona podsítě | 10.2.0.0/24 |
| Název podsítě (brány) | GatewaySubnet (nelze změnit) |
| Předpona podsítě (brány) | 10.2.255.0/27 |
| Název brány | vnet2gw |
| Typ brány | Vpn |
| Typ brány VPN | RouteBased |
| SKU brány | Basic |
| Brána IP | vnet1gwip |

## <a name="setup-dns"></a>Nastavení DNS

V předchozí části vytvoří šablona virtuálního počítače s Ubuntu v každé dvě virtuální sítě.  V této části nainstalujte vazby na dva virtuální počítače DNS a pak nakonfigurujte předávání DNS na dva virtuální počítače.

K instalaci vazby yon muset zjistit veřejnou IP adresu dva virtuální počítače DNS.

1. Otevřete web [Azure Portal](https://portal.azure.com).
2. Spusťte virtuální počítač DNS tak, že vyberete **skupiny prostředků > [název skupiny prostředků] > [vnet1DNS]**.  Název skupiny prostředků je ten, který vytvoříte v posledním postupu. Výchozí názvy DNS virtuálního počítače jsou *vnet1DNS* a *vnet2NDS*.
3. Vyberte **vlastnosti** otevřete stránku vlastnosti ve virtuální síti.
4. Zapište **veřejnou IP adresu**a taky ověřit **privátní IP adresa**.  Privátní IP adresa musí být **10.1.0.4** pro vnet1DNS a **10.2.0.4** pro vnet2DNS.  
5. Změníte na servery DNS pro obě virtuální sítě pomocí serverů DNS výchozí (poskytováno Azure) příchozí a odchozí přístup ke stažení balíčků pro instalaci vazby v následujících krocích.

K instalaci vazby, použijte následující postup:

1. Pomocí SSH se připojte k __veřejnou IP adresu__ DNS virtuálního počítače. Následující příklad se připojí k virtuálnímu počítači na 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Nahraďte `sshuser` pomocí uživatelského účtu SSH, jste zadali při vytváření virtuálního počítače DNS.

    > [!NOTE]
    > Existuje řada různých způsobů, jak získat `ssh` nástroj. V systému Linux, Unix a macOS se poskytuje jako součást operačního systému. Pokud používáte Windows, zvažte jednu z následujících možností:
    >
    > * [Azure Cloud Shell](../../cloud-shell/quickstart.md)
    > * [Bash na Ubuntu ve Windows 10](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git)https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH)https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. K instalaci vazby, použijte následující příkazy z relace SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Konfigurace vazby předávat požadavky na název řešení na místní server DNS. K tomu použít následující text jako obsah `/etc/bind/named.conf.options` souboru:

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
            168.63.129.16; #This is the Azure DNS server
        };

        dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
    };
    ```
    
    > [!IMPORTANT]
    > Nahraďte hodnoty v `goodclients` část dvě virtuální sítě s rozsahem IP adres. Tento oddíl definuje adresy, které přijímá požadavky od tento server DNS.

    K úpravě tohoto souboru použijte následující příkaz:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Chcete-li uložit soubor, použijte __Ctrl + X__, __Y__a potom __Enter__.

4. Z relace SSH použijte následující příkaz:

    ```bash
    hostname -f
    ```

    Tento příkaz vrátí hodnotu podobné následujícímu textu:

        vnet1DNS.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` Text je __přípona DNS__ pro tuto virtuální síť. Uložte tuto hodnotu, bude se hodit později.

    Musíte také zjistit přípona DNS z jiného serveru DNS. Budete ho potřebovat v dalším kroku.

5. Ke konfiguraci vazby k překladu názvů DNS pro prostředky v rámci virtuální sítě, použijte následující text jako obsah `/etc/bind/named.conf.local` souboru:

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]
    > Je třeba nahradit `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` s příponou DNS ve virtuální síti. A předávání IP adresa je privátní IP adresu serveru DNS ve virtuální síti.

    K úpravě tohoto souboru použijte následující příkaz:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Chcete-li uložit soubor, použijte __Ctrl + X__, __Y__a potom __Enter__.

6. Spuštění vazby, použijte následující příkaz:

    ```bash
    sudo service bind9 restart
    ```

7. Chcete-li ověřit, že vazba dokáže přeložit názvy prostředků ve virtuální síti, použijte následující příkazy:

    ```bash
    sudo apt install dnsutils
    nslookup vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    ```

    > [!IMPORTANT]
    > Nahraďte `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` s plně kvalifikovaný název domény (FQDN) DNS virtuálního počítače v druhé síti.
    >
    > Nahraďte `10.2.0.4` s __interní IP adresa__ vašeho vlastního serveru DNS ve virtuální síti.

    Odpověď se zobrazí podobný následujícímu textu:

    ```
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    Až doteď nelze vyhledat IP adresu ze sítě bez zadaná IP adresa serveru DNS.

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Konfigurace virtuální sítě pro použití vlastního serveru DNS.

Ke konfiguraci virtuální sítě, abyste místo Azure rekurzivní překladač použít vlastní server DNS, použijte následující kroky:

1. V [webu Azure portal](https://portal.azure.com), vyberte virtuální síť a pak vyberte __servery DNS__.

2. Vyberte __vlastní__a zadejte __interní IP adresa__ vlastního serveru DNS. Nakonec vyberte __Uložit__.

6. Otevření virtuálního počítače pro server DNS ve virtuální síti vnet1 a klikněte na tlačítko **restartovat**.  Je nutné restartovat všechny virtuální počítače ve virtuální síti, aby byly k provedení změn konfigurace DNS.
7. Opakujte kroky konfigurace vlastního serveru DNS pro síť vnet2.

K testování této konfigurace DNS, můžete připojit na dva virtuální počítače DNS pomocí protokolu SSH a příkaz ping serveru DNS ve virtuální síti s použitím názvu hostitele. Pokud to nepomůže, použijte následující příkaz a zkontrolujte stav DNS:

```bash
sudo service bind9 status
```

## <a name="create-apache-hbase-clusters"></a>Vytvoření clusterů Apache HBase

Vytvoření [Apache HBase](http://hbase.apache.org/) clusteru v každém ze dvou virtuálních sítí s následující konfigurací:

- **Název skupiny prostředků**: použijte stejný název skupiny prostředků, při vytváření virtuální sítě.
- **Typ clusteru**: HBase
- **Verze**: HBase 1.1.2 (HDI 3.6)
- **Umístění**: Použijte stejné umístění jako virtuální síť.  Ve výchozím nastavení, je ze sítě vnet1 *USA – západ*, a síť vnet2 je *USA – východ*.
- **Úložiště**: Vytvořte nový účet úložiště pro cluster.
- **Virtuální síť** (z upřesňující nastavení na portálu): Vyberte ze sítě vnet1, kterou jste vytvořili v posledním postupu.
- **Podsíť**: Výchozím názvem používaným v šabloně je **subnet1**.

K zajištění, že je prostředí správně nakonfigurováno, musí být poslat příkaz ping plně kvalifikovaný název domény hlavního uzlu v mezi dvěma clustery.

## <a name="load-test-data"></a>Načtení testovacích dat

Při replikaci clusteru, je nutné zadat tabulky, které chcete replikovat. V této části můžete načíst data do zdrojového clusteru. V další části povolíte replikaci mezi dvěma clustery.

Chcete-li vytvořit **kontakty** tabulku a vložte některá data v tabulce, postupujte podle pokynů na adrese [kurz Apache HBase: Začněte používat Apache HBase v HDInsight](apache-hbase-tutorial-get-started-linux.md).

## <a name="enable-replication"></a>Povolení replikace

Následující kroky popisují, jak volat skript akce skriptu z webu Azure portal. Informace o spouštění akce skriptu s použitím prostředí Azure PowerShell a rozhraní příkazového řádku Azure Classic najdete v tématu [HDInsight přizpůsobit clustery pomocí akce skriptu](../hdinsight-hadoop-customize-cluster-linux.md).

**Pokud chcete povolit replikaci HBase na webu Azure Portal**

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Otevřete zdrojový cluster HBase.
3. V nabídce clusteru, vyberte **akcí skriptů**.
4. V horní části stránky vyberte **odeslat novou**.
5. Vyberte nebo zadejte následující informace:

  1. **Název**: Zadejte **povolit replikaci**.
  2. **Adresa URL skriptu bash**: Zadejte **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**.
  3.  **Hlavní**: Ujistěte se, že je vybrána tato možnost. Zrušte jiné typy uzlů.
  4. **Parametry**: Následující ukázkové parametry povolíte replikaci pro všechny existující tabulky a pak zkopírujte všechna data ze zdrojového clusteru do cílového clusteru:

          -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
    >[!note]
    >
    > Použijte název hostitele místo plně kvalifikovaný název domény pro zdrojový i cílový název DNS clusteru.

6. Vyberte **Vytvořit**. Skript může trvat nějakou dobu, zvláště když používáte **- copydata** argument.

Povinné argumenty:

|Název|Popis|
|----|-----------|
|-s, - src-clusteru | Určuje název DNS clusteru HBase zdroje. Příklad: -s hbsrccluster, clusteru – src = hbsrccluster |
|-d, – letního času mezi clustery | Určuje název DNS clusteru HBase cíl (replikovaného). Příklad: -s dsthbcluster, clusteru – src = dsthbcluster |
|-sp, src –--heslo ambari | Určuje heslo správce Ambari ve zdrojovém clusteru HBase. |
|-distribučního bodu, letního času –--heslo ambari | Určuje heslo správce Ambari v cílovém clusteru HBase.|

Volitelné argumenty:

|Název|Popis|
|----|-----------|
|-su nebo--src-ambari-user | Určuje uživatelské jméno správce pro Ambari ve zdrojovém clusteru HBase. Výchozí hodnota je **správce**. |
|-du – dst ambari uživatele | Určuje uživatelské jméno správce pro Ambari v cílovém clusteru HBase. Výchozí hodnota je **správce**. |
|-t,--seznam tabulek | Určuje tabulky, které chcete replikovat. Příklad:--tabulkového seznamu = "table1, table2; Tabulka3". Pokud nezadáte tabulky, se replikují všechny existujících tabulek HBase.|
|-min, - počítač | Určuje hlavní uzel, ve kterém běží akce skriptu. Hodnota je buď **hn0** nebo **hn1** a měli na základě které je aktivní hlavní uzel. Tuto možnost použijte, pokud používáte skript hodnotou 0 USD jako akci skriptu z portálu HDInsight nebo Azure Powershellu.|
|-prohlášení cp, - copydata | Umožňuje migraci existujících dat v tabulkách, kde je povolená replikace. |
|-ot. / min, - replikace-phoenix-meta | Umožňuje replikaci pro Phoenix systémové tabulky. <br><br>*Tuto možnost používejte s opatrností.* Doporučujeme, abyste před použitím tohoto skriptu znovu vytvořit Phoenix tabulek v clusterech repliky. |
|-h, – Nápověda | Zobrazí informace o použití. |

`print_usage()` Část [skript](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) obsahuje podrobné vysvětlení parametrů.

Po úspěšném nasazení skriptových akcí můžete použít SSH pro připojení k cílový cluster HBase a potom ověřte, že data se replikují.

### <a name="replication-scenarios"></a>Scénáře replikace

Následující seznam obsahuje některé obecné použití případů a jejich nastavení parametru:

- **Povolení replikace u všech tabulek mezi dvěma clustery**. Tento scénář nevyžaduje kopírování nebo migrace existujících dat v tabulkách a nepoužívá Phoenix tabulky. Použijte následující parametry:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Povolte replikaci na konkrétní tabulky**. Pokud chcete povolit replikaci na table1, table2 a tabulka3, použijte následující parametry:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Povolíte replikaci pro konkrétní tabulky a zkopírovat existující data**. Pokud chcete povolit replikaci na table1, table2 a tabulka3, použijte následující parametry:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Povolíte replikaci pro všechny tabulky a replikovat Phoenix metadata ze zdroje do cíle**. Phoenix metadata replikace není ideální. Používejte ji opatrně. Použijte následující parametry:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Zkopírujte a migraci dat

K dispozici dvě samostatné skript akce skriptů pro kopírování nebo migrujete data po povolení replikace:

- [Skript pro malé tabulky](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (tabulky, které jsou několik gigabajtů ve velikosti a celková kopírování se očekává dokončení za méně než hodinu)

- [Skript pro velké tabulky](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (tabulky, které se očekává, že trvá déle než jednu hodinu, než zkopírovat)

Můžete postupujte stejným způsobem, který je popsaný v [povolit replikaci](#enable-replication) volat akci skriptu. Použijte následující parametry:

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

`print_usage()` Část [skript](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) obsahuje podrobný popis parametrů.

### <a name="scenarios"></a>Scénáře

- **Zkopírujte konkrétní tabulky (test1, test2 a test3) pro všechny řádky upravit až doteď (aktuální časové razítko)**:

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  nebo:

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Zkopírujte konkrétní tabulky s zadaný časový rozsah**:

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>Zákaz replikace

Pokud chcete zakázat replikaci, použijte jiný skript akce skriptu z [Githubu](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). Můžete postupujte stejným způsobem, který je popsaný v [povolit replikaci](#enable-replication) volat akci skriptu. Použijte následující parametry:

    -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

`print_usage()` Část [skript](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) obsahuje podrobné vysvětlení parametrů.

### <a name="scenarios"></a>Scénáře

- **Zakažte replikaci na všechny tabulky**:

        -m hn1 -s <source hbase cluster name> -sp Mypassword\!789 -all
  nebo

        --src-cluster=<source hbase cluster name> --dst-cluster=<destination hbase cluster name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **Zakažte replikaci na zadaných tabulek (table1, table2 a tabulka3)**:

        -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak nastavit replikaci Apache HBase ve virtuální síti, nebo mezi dvěma virtuálními sítěmi. Další informace o HDInsight a Apache HBase, najdete v těchto článcích:

* [Začínáme s Apache HBase v HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Přehled HDInsight Apache HBase](./apache-hbase-overview.md)
* [Vytvoření clusterů Apache HBase ve virtuální síti Azure](./apache-hbase-provision-vnet.md)

