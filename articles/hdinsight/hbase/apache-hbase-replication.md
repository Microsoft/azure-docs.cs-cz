---
title: HBA replikace clusteru ve virtuálních sítích – Azure HDInsight
description: Naučte se, jak nastavit replikaci HBA z jedné verze HDInsight na jinou pro vyrovnávání zatížení, vysokou dostupnost, migraci a aktualizace s nulovými výpadky a zotavení po havárii.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 12/06/2019
ms.openlocfilehash: 8fc5ba2280b5ad68a40f4992adc170408e80e5a6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021788"
---
# <a name="set-up-apache-hbase-cluster-replication-in-azure-virtual-networks"></a>Nastavení replikace clusteru Apache HBA v Azure Virtual Networks

Naučte se, jak nastavit replikaci [Apache HBA](https://hbase.apache.org/) v rámci virtuální sítě nebo mezi dvěma virtuálními sítěmi v Azure.

Replikace clusteru používá metodologii pro nabízení zdroje. Cluster HBA může být zdrojem nebo cílem nebo může současně plnit obě role. Replikace je asynchronní. Cílem replikace je konečná konzistence. Když zdroj obdrží pro rodinu sloupců úpravu, když je povolená replikace, upraví se tato úprava na všechny cílové clustery. Když jsou data replikována z jednoho clusteru do jiného, jsou sledován zdrojový cluster a všechny clustery, které již data spotřebují, aby se zabránilo cyklům replikace.

V tomto článku nastavíte replikaci zdrojového cíle. Další topologie clusteru najdete v [Referenční příručce k Apache HBA](https://hbase.apache.org/book.html#_cluster_replication).

V tomto případě jsou adaptéry pro použití replikace pro jednu virtuální síť HBA.

* Vyrovnávání zatížení. V cílovém clusteru můžete například spouštět kontroly nebo úlohy MapReduce a ingestovat data ve zdrojovém clusteru.
* Přidání vysoké dostupnosti.
* Migrují se data z jednoho clusteru HBA do jiného.
* Upgrade clusteru Azure HDInsight z jedné verze na jiný

Následující jsou adaptéry HBA v případech využití replikace pro dvě virtuální sítě:

* Nastavení zotavení po havárii.
* Vyrovnávání zatížení a rozdělování aplikace.
* Přidání vysoké dostupnosti.

Clustery můžete replikovat pomocí skriptů [skriptových akcí](../hdinsight-hadoop-customize-cluster-linux.md) z [GitHubu](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Požadavky
Než začnete tento článek, musíte mít předplatné Azure. Viz [získat bezplatnou zkušební verzi Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="set-up-the-environments"></a>Nastavení prostředí

Máte tři možnosti konfigurace:

- Dva clustery Apache HBA v jedné virtuální síti Azure.
- Dva clustery Apache HBA ve dvou různých virtuálních sítích ve stejné oblasti.
- Dva clustery Apache HBA ve dvou různých virtuálních sítích ve dvou různých oblastech (geografická replikace).

Tento článek se věnuje scénáři geografické replikace.

Abychom vám pomohli nastavit prostředí, vytvořili jsme několik [šablon Azure Resource Manager](../../azure-resource-manager/management/overview.md). Pokud dáváte přednost nastavení prostředí pomocí jiných metod, přečtěte si:

- [Vytváření clusterů Apache Hadoop ve službě HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
- [Vytváření clusterů Apache HBA v Azure Virtual Network](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Nastavení dvou virtuálních sítí ve dvou různých oblastech

Pokud chcete použít šablonu, která vytvoří dvě virtuální sítě ve dvou různých oblastech a připojení VPN mezi virtuální sítě, vyberte následující tlačítko **nasadit do Azure** . Definice šablony je uložena ve [veřejném úložišti objektů BLOB](https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

Některé pevně zakódované hodnoty v šabloně:

**Virtuální síť 1**

| Vlastnost | Hodnota |
|----------|-------|
| Umístění | USA – západ |
| Název virtuální sítě | &lt;ClusterNamePrevix> – vnet1 |
| Předpona adresního prostoru | 10.1.0.0/16 |
| Název podsítě | podsíť 1 |
| Předpona podsítě | 10.1.0.0/24 |
| Název podsítě (brány) | GatewaySubnet (nejde změnit) |
| Předpona podsítě (brány) | 10.1.255.0/27 |
| Název brány | vnet1gw |
| Typ brány | Vpn |
| Typ sítě VPN brány | RouteBased |
| SKU brány | Základní |
| IP adresa brány | vnet1gwip |

**Virtuální síť 2**

| Vlastnost | Hodnota |
|----------|-------|
| Umístění | USA – východ |
| Název virtuální sítě | &lt;ClusterNamePrevix> – vnet2 |
| Předpona adresního prostoru | 10.2.0.0/16 |
| Název podsítě | podsíť 1 |
| Předpona podsítě | 10.2.0.0/24 |
| Název podsítě (brány) | GatewaySubnet (nejde změnit) |
| Předpona podsítě (brány) | 10.2.255.0/27 |
| Název brány | vnet2gw |
| Typ brány | Vpn |
| Typ sítě VPN brány | RouteBased |
| SKU brány | Základní |
| IP adresa brány | vnet1gwip |

## <a name="setup-dns"></a>Nastavení DNS

V poslední části šablona vytvoří virtuální počítač s Ubuntu v každé z těchto dvou virtuálních sítí.  V této části nainstalujete službu BIND na dva virtuální počítače DNS a pak na těchto dvou virtuálních počítačích nakonfigurujete předávání DNS.

Aby bylo možné službu BIND nainstalovat, Yon musí najít veřejnou IP adresu dvou virtuálních počítačů DNS.

1. Otevřete web [Azure Portal](https://portal.azure.com).
2. Otevřete virtuální počítač DNS tak, že vyberete **skupiny prostředků > [název skupiny prostředků] > [vnet1DNS]**.  Název skupiny prostředků je ten, který vytvoříte v posledním postupu. Výchozí názvy virtuálních počítačů DNS jsou *vnet1DNS* a *vnet2NDS*.
3. Výběrem **vlastnosti** otevřete stránku vlastností virtuální sítě.
4. Zapište si **veřejnou IP adresu** a taky ověřte **privátní IP adresu**.  Privátní IP adresa musí být **10.1.0.4** pro vnet1DNS a **10.2.0.4** pro vnet2DNS.  
5. Změňte servery DNS pro obě virtuální sítě tak, aby používaly výchozí servery DNS (poskytované Azure), aby příchozí a odchozí přístup mohly stahovat balíčky pro instalaci vazby v následujících krocích.

K instalaci vazby použijte následující postup:

1. Pomocí SSH se připojte k __veřejné IP adrese__ virtuálního počítače DNS. Následující příklad se připojuje k virtuálnímu počítači na adrese 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Nahraďte `sshuser` uživatelským účtem SSH, který jste zadali při vytváření virtuálního počítače DNS.

    > [!NOTE]  
    > Existuje mnoho způsobů, jak tento `ssh` nástroj získat. V systému Linux, UNIX a macOS je k dispozici jako součást operačního systému. Pokud používáte systém Windows, vezměte v úvahu jednu z následujících možností:
    >
    > * [Azure Cloud Shell](../../cloud-shell/quickstart.md)
    > * [Bash na Ubuntu ve Windows 10](/windows/wsl/about)
    > * [Githttps://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSHhttps://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. K instalaci vazby použijte následující příkazy z relace SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Nakonfigurujte službu BIND na dopředné požadavky na překlad názvů na místní server DNS. K tomu použijte následující text jako obsah `/etc/bind/named.conf.options` souboru:

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
    > Nahraďte hodnoty v `goodclients` oddílu rozsahem IP adres dvou virtuálních sítí. V této části se definují adresy, od kterých tento server DNS přijímá požadavky.

    Chcete-li tento soubor upravit, použijte následující příkaz:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Pokud chcete soubor uložit, použijte __CTRL + X__, __Y__ a pak __Zadejte__.

4. Z relace SSH použijte následující příkaz:

    ```bash
    hostname -f
    ```

    Tento příkaz vrátí hodnotu podobnou následujícímu textu:

    ```output
    vnet1DNS.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net`Text je __přípona DNS__ pro tuto virtuální síť. Uložte tuto hodnotu, bude se hodit později.

    Také je nutné zjistit příponu DNS z jiného serveru DNS. Budete ho potřebovat v dalším kroku.

5. Pokud chcete nakonfigurovat službu BIND k překladu názvů DNS pro prostředky v rámci virtuální sítě, jako obsah souboru použijte následující text `/etc/bind/named.conf.local` :

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > Je nutné nahradit `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` příponu DNS druhé virtuální sítě. A IP adresa pro předávací službu je privátní IP adresa serveru DNS v druhé virtuální síti.

    Chcete-li tento soubor upravit, použijte následující příkaz:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Pokud chcete soubor uložit, použijte __CTRL + X__, __Y__ a pak __Zadejte__.

6. K zahájení vazby použijte následující příkaz:

    ```bash
    sudo service bind9 restart
    ```

7. Chcete-li ověřit, zda může vazba přeložit názvy prostředků ve druhé virtuální síti, použijte následující příkazy:

    ```bash
    sudo apt install dnsutils
    nslookup vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    ```

    > [!IMPORTANT]  
    > Nahraďte `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` plně kvalifikovaným názvem domény (FQDN) virtuálního počítače DNS ve druhé síti.
    >
    > Nahraďte `10.2.0.4` __interní IP adresou__ vašeho vlastního serveru DNS v druhé virtuální síti.

    Odpověď se zobrazí jako následující text:

    ```output
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    Až do této chvíle nebudete moci vyhledat IP adresu z jiné sítě bez zadané IP adresy serveru DNS.

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Nakonfigurujte virtuální síť tak, aby používala vlastní server DNS.

Pokud chcete virtuální síť nakonfigurovat tak, aby místo rekurzivního překladače Azure používala vlastní server DNS, použijte následující postup:

1. V [Azure Portal](https://portal.azure.com)vyberte virtuální síť a pak vyberte __servery DNS__.

2. Vyberte __vlastní__ a zadejte __interní IP adresu__ vlastního serveru DNS. Nakonec vyberte __Uložit__.

6. Otevřete virtuální počítač serveru DNS v vnet1 a klikněte na **restartovat**.  Aby se konfigurace služby DNS projevila, musíte restartovat všechny virtuální počítače ve virtuální síti.
7. Opakováním kroků nakonfigurujte vlastní server DNS pro vnet2.

Pokud chcete otestovat konfiguraci DNS, můžete se připojit ke dvěma virtuálním počítačům DNS pomocí protokolu SSH a pomocí názvu hostitele otestovat server DNS druhé virtuální sítě. Pokud to nepomůže, ověřte stav DNS pomocí následujícího příkazu:

```bash
sudo service bind9 status
```

## <a name="create-apache-hbase-clusters"></a>Vytváření clusterů Apache HBA

Vytvořte cluster [Apache HBA](https://hbase.apache.org/) v každé z těchto dvou virtuálních sítí s následující konfigurací:

- **Název skupiny prostředků**: použijte stejný název skupiny prostředků jako při vytváření virtuálních sítí.
- **Typ clusteru**: HBA
- **Verze**: HBA – 1.1.2 (HDI 3,6)
- **Umístění**: použijte stejné umístění jako virtuální síť.  Ve výchozím nastavení je vnet1 *západní USA* a vnet2 je *východní USA*.
- **Úložiště**: vytvořte nový účet úložiště pro cluster.
- **Virtuální síť** (z pokročilého nastavení na portálu): vyberte vnet1, který jste vytvořili v posledním postupu.
- **Podsíť**: výchozí název, který se používá v šabloně, je **SUBNET1**.

Aby bylo zajištěno, že je prostředí správně nakonfigurováno, musíte být schopni odeslat příkaz k otestování plně kvalifikovaného názvu domény hlavnímu uzlu mezi dvěma clustery.

## <a name="load-test-data"></a>Načtení testovacích dat

Při replikaci clusteru je nutné zadat tabulky, které chcete replikovat. V této části načtete některá data do zdrojového clusteru. V další části budete umožňovat replikaci mezi dvěma clustery.

Pokud chcete vytvořit tabulku **kontaktů** a vložit do ní nějaká data, postupujte podle pokynů v článku o [Apache HBA kurz: Začínáme používat Apache HBA v HDInsight](apache-hbase-tutorial-get-started-linux.md).

> [!NOTE]
> Pokud chcete replikovat tabulky z vlastního oboru názvů, je nutné zajistit, aby byly definovány také příslušné vlastní obory názvů v cílovém clusteru.
>

## <a name="enable-replication"></a>Povolení replikace

Následující postup popisuje, jak volat skript akce skriptu z Azure Portal. Informace o spuštění akce skriptu pomocí Azure PowerShell a Azure Classic CLI najdete v tématu [Přizpůsobení clusterů HDInsight pomocí akce skriptu](../hdinsight-hadoop-customize-cluster-linux.md).

**Povolení replikace HBA z Azure Portal**

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Otevřete zdrojový cluster HBA.
3. V nabídce cluster Vyberte **akce skriptu**.
4. V horní části stránky vyberte **Odeslat novou**.
5. Vyberte nebo zadejte následující informace:

   1. **Název**: zadejte **Povolit replikaci**.
   2. **Adresa URL skriptu bash**: zadejte **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh** .
   3. **Head**: Ujistěte se, že je vybraná možnost. Zrušte zaškrtnutí ostatních typů uzlů.
   4. **Parametry**: následující ukázkové parametry umožňují replikaci pro všechny existující tabulky a následné zkopírování všech dat ze zdrojového clusteru do cílového clusteru:

    `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata`
    
      > [!NOTE]
      > Jako název DNS zdrojového i cílového clusteru použijte název hostitele místo plně kvalifikovaného názvu domény.
      >
      > Tento názorný postup předpokládá HN1 jako aktivní hlavnímu uzlu. Zkontrolujte prosím cluster, abyste identifikovali aktivní hlavní uzel.

6. Vyberte **Vytvořit**. Spuštění skriptu může trvat delší dobu, zejména při použití argumentu **-COPYDATA** .

Požadované argumenty:

|Název|Description|
|----|-----------|
|-s,--src-cluster | Určuje název DNS zdrojového clusteru HBA. Příklad:-s hbsrccluster,--src-cluster = hbsrccluster |
|-d,--DST-cluster | Určuje název DNS cílového clusteru (repliky) HBA. Příklad:-s dsthbcluster,--src-cluster = dsthbcluster |
|-SP,--src-Ambari-Password | Určuje heslo správce pro Ambari ve zdrojovém clusteru HBA. |
|-DP,--DST-Ambari-Password | Určuje heslo správce pro Ambari v cílovém clusteru HBA.|

Nepovinné argumenty:

|Název|Description|
|----|-----------|
|-Su,--src-Ambari-User | Určuje uživatelské jméno správce pro Ambari ve zdrojovém clusteru HBA. Výchozí hodnota je **admin (správce**). |
|-du,--DST-Ambari-User | Určuje uživatelské jméno správce pro Ambari v cílovém clusteru HBA. Výchozí hodnota je **admin (správce**). |
|-t,--Table-list | Určuje tabulky, které se mají replikovat. Příklad:--Table-list = "Tabulka1; Tabulka2; TABLE3". Pokud nezadáte žádné tabulky, replikují se všechny existující tabulky HBA.|
|-m,--Machine | Určuje hlavní uzel, ve kterém se spustí akce skriptu. Hodnota by měla být zvolena v závislosti na tom, který je aktivním hlavním uzlem. Tuto možnost použijte, když spouštíte skript $0 jako akci skriptu z portálu HDInsight nebo Azure PowerShell.|
|-CP,-COPYDATA | Povolí migraci existujících dat v tabulkách, ve kterých je povolená replikace. |
|-ot./min. – replikace-Phoenix-meta | Povoluje replikaci v systémových tabulkách v Phoenixu. <br><br>*Tuto možnost používejte opatrně.* Před použitím tohoto skriptu doporučujeme, abyste v clusterech replik znovu vytvořili tabulky v Phoenixu. |
|-h,--help | Zobrazí informace o použití. |

`print_usage()`Část [skriptu](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) obsahuje podrobné vysvětlení parametrů.

Po úspěšném nasazení akce skriptu můžete použít SSH pro připojení k cílovému clusteru HBA a pak ověřte, že se data replikují.

### <a name="replication-scenarios"></a>Scénáře replikace

V následujícím seznamu jsou uvedeny některé obecné případy použití a jejich nastavení parametrů:

- **Povolte replikaci ve všech tabulkách mezi dvěma clustery**. Tento scénář nevyžaduje kopírování ani migraci existujících dat v tabulkách a nepoužívá tabulky v Phoenixu. Použijte následující parametry:

  `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>`

- **Povoluje replikaci pro konkrétní tabulky**. Pokud chcete povolit replikaci na Tabulka1, Tabulka2 a TABLE3, použijte následující parametry:

  `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"`

- **Povolte replikaci pro konkrétní tabulky a zkopírujte stávající data**. Pokud chcete povolit replikaci na Tabulka1, Tabulka2 a TABLE3, použijte následující parametry:

  `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata`

- **Povolte replikaci pro všechny tabulky a replikujte metadata Phoenix ze zdroje do cíle**. Replikace metadat v Phoenixu není ideální. Používejte je opatrně. Použijte následující parametry:

  `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta`

## <a name="copy-and-migrate-data"></a>Kopírování a migrace dat

K dispozici jsou dvě samostatné skripty akcí skriptu pro kopírování nebo migraci dat po povolení replikace:

- [Skript pro malé tabulky](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (tabulky, které mají velikost v několika gigabajtech, a celková kopie se očekává za méně než jednu hodinu)

- [Skript pro velké tabulky](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (tabulky, jejichž kopírování má trvat déle než jednu hodinu)

Stejný postup, který je popsaný v tématu [Povolení replikace](#enable-replication) , můžete použít k volání akce skriptu. Použijte následující parametry:

`-m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]`

`print_usage()`Část [skriptu](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) obsahuje podrobný popis parametrů.

### <a name="scenarios"></a>Scénáře

- **Kopírovat konkrétní tabulky (Test1, Test2 a Test3) pro všechny upravované řádky do teď (aktuální časové razítko)**:

  `-m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow`

  Ani

  `-m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow`

- **Kopírovat konkrétní tabulky se zadaným časovým rozsahem**:

  `-m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"`

## <a name="disable-replication"></a>Zákaz replikace

Pokud chcete replikaci zakázat, použijte jiný skript akce skriptu z [GitHubu](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). Stejný postup, který je popsaný v tématu [Povolení replikace](#enable-replication) , můžete použít k volání akce skriptu. Použijte následující parametry:

`-m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">`

`print_usage()`Část [skriptu](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) obsahuje podrobné vysvětlení parametrů.

### <a name="scenarios"></a>Scénáře

- **Zakázat replikaci na všech tabulkách**:

  `-m hn1 -s <source hbase cluster name> -sp Mypassword\!789 -all`

  nebo

  `--src-cluster=<source hbase cluster name> --dst-cluster=<destination hbase cluster name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>`

- **Zakažte replikaci u zadaných tabulek (Tabulka1, Tabulka2 a TABLE3)**:

  `-m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> -t "table1;table2;table3"`

> [!NOTE]
> Pokud máte v úmyslu odstranit cílový cluster, nezapomeňte jej odebrat ze seznamu partnerských uzlů zdrojového clusteru. To se dá udělat spuštěním příkazu remove_peer 1 v prostředí HBA na zdrojovém clusteru. Selhání tohoto zdrojového clusteru nemusí fungovat správně.
>

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak nastavit replikaci Apache HBA v rámci virtuální sítě nebo mezi dvěma virtuálními sítěmi. Další informace o HDInsight a Apache HBA najdete v těchto článcích:

* [Začínáme s Apache HBA v HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Přehled adaptérů HDInsight Apache HBA](./apache-hbase-overview.md)
* [Vytváření clusterů Apache HBA v Azure Virtual Network](./apache-hbase-provision-vnet.md)