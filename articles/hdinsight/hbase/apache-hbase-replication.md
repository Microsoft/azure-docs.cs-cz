---
title: Replikace clusteru HBase ve virtuálních sítích – Azure HDInsight
description: Zjistěte, jak nastavit replikaci HBase z jedné verze HDInsight do druhé pro vyrovnávání zatížení, vysokou dostupnost, migraci a aktualizace s nulovými prostoji a zotavení po havárii.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 1e6465584dd4e67f736b94d2939678c1a69163bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435667"
---
# <a name="set-up-apache-hbase-cluster-replication-in-azure-virtual-networks"></a>Nastavení replikace clusteru Apache HBase ve virtuálních sítích Azure

Zjistěte, jak nastavit replikaci [Apache HBase](https://hbase.apache.org/) v rámci virtuální sítě nebo mezi dvěma virtuálními sítěmi v Azure.

Replikace clusteru používá metodiku nabízení zdrojů. Cluster HBase může být zdrojem nebo cílem nebo může plnit obě role najednou. Replikace je asynchronní. Cílem replikace je konečná konzistence. Když zdroj obdrží úpravy do rodiny sloupců, když je povolena replikace, je úprava rozšířena do všech cílových clusterů. Pokud jsou data replikována z jednoho clusteru do druhého, jsou sledovány zdrojové clustery a všechny clustery, které již data spotřebovávaly, aby se zabránilo replikačním smyčkám.

V tomto článku nastavíte replikaci zdrojového cíle. Další topologie clusteru najdete v [referenční příručce Apache HBase](https://hbase.apache.org/book.html#_cluster_replication).

Následují případy využití replikace HBase pro jednu virtuální síť:

* Vyrovnávání zatížení. Můžete například spustit prohledávání nebo úlohy MapReduce v cílovém clusteru a ingestovat data ve zdrojovém clusteru.
* Přidání vysoké dostupnosti.
* Migrace dat z jednoho clusteru HBase do jiného.
* Upgrade clusteru Azure HDInsight z jedné verze na druhou.

Níže jsou uvedeny případy využití replikace HBase pro dvě virtuální sítě:

* Nastavení zotavení po havárii.
* Vyrovnávání zatížení a rozdělení aplikace.
* Přidání vysoké dostupnosti.

Clustery můžete replikovat pomocí skriptovacích [akčních](../hdinsight-hadoop-customize-cluster-linux.md) skriptů z [GitHubu](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Požadavky
Než začnete tento článek, musíte mít předplatné Azure. Podívejte [se na přečtěte si bezplatnou zkušební verzi Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="set-up-the-environments"></a>Nastavení prostředí

Máte tři možnosti konfigurace:

- Dva clustery Apache HBase v jedné virtuální síti Azure.
- Dva clustery Apache HBase ve dvou různých virtuálních sítích ve stejné oblasti.
- Dva clustery Apache HBase ve dvou různých virtuálních sítích ve dvou různých oblastech (geografická replikace).

Tento článek popisuje scénář geografické replikace.

Abychom vám pomohli nastavit prostředí, vytvořili jsme některé [šablony Azure Resource Manageru](../../azure-resource-manager/management/overview.md). Pokud dáváte přednost nastavení prostředí pomocí jiných metod, přečtěte si následující:

- [Vytvoření clusterů Apache Hadoop v HDInsightu](../hdinsight-hadoop-provision-linux-clusters.md)
- [Vytvoření clusterů Apache HBase ve virtuální síti Azure](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Nastavení dvou virtuálních sítí ve dvou různých oblastech

Pokud chcete použít šablonu, která vytvoří dvě virtuální sítě ve dvou různých oblastech a připojení VPN mezi virtuálními sítěmi, vyberte následující tlačítko **Nasazení do Azure.** Definice šablony je uložená ve [veřejném úložišti objektů blob](https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

Některé pevně zakódované hodnoty v šabloně:

**Virtuální síť 1**

| Vlastnost | Hodnota |
|----------|-------|
| Umístění | USA – západ |
| Název virtuální sítě | &lt;ClusterNamePrevix>-vnet1 |
| Předpona adresního prostoru | 10.1.0.0/16 |
| Název podsítě | podsíť 1 |
| Předpona podsítě | 10.1.0.0/24 |
| Název podsítě (brána) | GatewaySubnet (nelze změnit) |
| Předpona podsítě (brána) | 10.1.255.0/27 |
| Název brány | vnet1gw |
| Typ brány | Vpn |
| Typ VPN brány | RouteBased |
| Skladová položka brány | Basic |
| IP brána | vnet1gwip |

**Virtuální síť 2**

| Vlastnost | Hodnota |
|----------|-------|
| Umístění | USA – východ |
| Název virtuální sítě | &lt;ClusterNamePrevix>-vnet2 |
| Předpona adresního prostoru | 10.2.0.0/16 |
| Název podsítě | podsíť 1 |
| Předpona podsítě | 10.2.0.0/24 |
| Název podsítě (brána) | GatewaySubnet (nelze změnit) |
| Předpona podsítě (brána) | 10.2.255.0/27 |
| Název brány | vnet2gw |
| Typ brány | Vpn |
| Typ VPN brány | RouteBased |
| Skladová položka brány | Basic |
| IP brána | vnet1gwip |

## <a name="setup-dns"></a>Nastavení SLUŽBY DNS

V poslední části šablona vytvoří virtuální stroj Ubuntu v každé ze dvou virtuálních sítí.  V této části nainstalujete vazbu na dva virtuální počítače DNS a potom nakonfigurujete předávání DNS na dvou virtuálních počítačích.

Chcete-li nainstalovat Bind, yon muset najít veřejnou IP adresu dvou dns virtuálních počítačů.

1. Otevřete [portál Azure](https://portal.azure.com).
2. Otevřete virtuální počítač DNS výběrem **skupin prostředků > [název skupiny prostředků] > [vnet1DNS]**.  Název skupiny prostředků je ten, který vytvoříte v posledním postupu. Výchozí názvy virtuálních počítačů DNS jsou *vnet1DNS* a *vnet2NDS*.
3. Výběrem **možnosti Vlastnosti** otevřete stránku vlastností virtuální sítě.
4. Poznamenejte si **veřejnou IP adresu**a ověřte také **privátní IP adresu**.  Soukromá IP adresa musí být **10.1.0.4** pro vnet1DNS a **10.2.0.4** pro vnet2DNS.  
5. Změňte servery DNS pro obě virtuální sítě tak, aby používaly výchozí servery DNS (Za předpokladu Azure), aby příchozí a odchozí přístup umožnily stahovat balíčky pro instalaci vazby v následujících krocích.

Chcete-li nainstalovat vazbu, použijte následující postup:

1. Pomocí SSH se připojte k __veřejné IP adrese__ virtuálního počítače DNS. Následující příklad se připojí k virtuálnímu počítači na 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Nahraďte `sshuser` uživatelským účtem SSH, který jste zadali při vytváření virtuálního počítače DNS.

    > [!NOTE]  
    > Existuje celá řada způsobů, jak `ssh` získat nástroj. Na Linuxu, Unixu a macOS je k dispozici jako součást operačního systému. Pokud používáte systém Windows, zvažte jednu z následujících možností:
    >
    > * [Azure Cloud Shell](../../cloud-shell/quickstart.md)
    > * [Bash na Ubuntu na Windows 10](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Chcete-li nainstalovat vazbu, použijte následující příkazy z relace SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Nakonfigurujte vazbu tak, aby přesměrovávala požadavky na překlad názvů na místní server DNS. Chcete-li tak učinit, použijte jako `/etc/bind/named.conf.options` obsah souboru následující text:

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
    > Nahraďte hodnoty `goodclients` v části rozsahem IP adres obou virtuálních sítí. Tato část definuje adresy, od kterých tento server DNS přijímá požadavky.

    Chcete-li tento soubor upravit, použijte následující příkaz:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Chcete-li soubor uložit, použijte __kombinaci kláves Ctrl+X__, __Y__a potom __klávesu Enter__.

4. V relaci SSH použijte následující příkaz:

    ```bash
    hostname -f
    ```

    Tento příkaz vrátí hodnotu podobnou následujícímu textu:

        vnet1DNS.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    Text `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` je __přípona DNS__ pro tuto virtuální síť. Uložte tuto hodnotu, bude se hodit později.

    Musíte také zjistit příponu DNS z jiného serveru DNS. Potřebujete ji v dalším kroku.

5. Chcete-li nakonfigurovat vazbu pro překlad názvů DNS pro prostředky ve `/etc/bind/named.conf.local` virtuální síti, použijte jako obsah souboru následující text:

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > Je nutné `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` nahradit příponou DNS jiné virtuální sítě. A IP server pro předávání je privátní IP adresa DNS serveru v jiné virtuální síti.

    Chcete-li tento soubor upravit, použijte následující příkaz:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Chcete-li soubor uložit, použijte __kombinaci kláves Ctrl+X__, __Y__a potom __klávesu Enter__.

6. Chcete-li spustit vazbu, použijte následující příkaz:

    ```bash
    sudo service bind9 restart
    ```

7. Chcete-li ověřit, že vazba může přeložit názvy prostředků v jiné virtuální síti, použijte následující příkazy:

    ```bash
    sudo apt install dnsutils
    nslookup vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    ```

    > [!IMPORTANT]  
    > Nahraďte `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` plně kvalifikovaným názvem domény (FQDN) virtuálního počítače DNS v jiné síti.
    >
    > Nahraďte `10.2.0.4` __interní IP adresou__ vlastního serveru DNS v jiné virtuální síti.

    Odpověď se podobá následujícímu textu:

    ```
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    Dosud nelze vyhledat adresu IP z jiné sítě bez zadané adresy IP serveru DNS.

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Konfigurace virtuální sítě pro použití vlastního serveru DNS

Chcete-li nakonfigurovat virtuální síť tak, aby používala vlastní server DNS namísto rekurzivního překládání Azure, použijte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com)vyberte virtuální síť a pak vyberte __DNS servery__.

2. Vyberte __Vlastní__a zadejte __interní ADRESU IP__ vlastního serveru DNS. Nakonec vyberte __uložit__.

6. Otevřete virtuální počítač dns serveru ve virtuální síti 1 a klepněte na **tlačítko Restartovat**.  Je nutné restartovat všechny virtuální počítače ve virtuální síti, aby se konfigurace DNS projevila.
7. Postup opakování nakonfigurujte vlastní server DNS pro síť vnet2.

Chcete-li otestovat konfiguraci DNS, můžete se připojit ke dvěma virtuálním počítačům DNS pomocí SSH a příkazem ping na server DNS jiné virtuální sítě pomocí názvu hostitele. Pokud nefunguje, zkontrolujte stav DNS pomocí následujícího příkazu:

```bash
sudo service bind9 status
```

## <a name="create-apache-hbase-clusters"></a>Vytvoření clusterů Apache HBase

Vytvořte cluster [Apache HBase](https://hbase.apache.org/) v každé ze dvou virtuálních sítí s následující konfigurací:

- **Název skupiny prostředků**: použijte stejný název skupiny prostředků jako vytváření virtuálních sítí.
- **Typ clusteru**: HBase
- **Verze**: HBase 1.1.2 (HDI 3.6)
- **Umístění**: Použijte stejné umístění jako virtuální síť.  Ve výchozím nastavení je vnet1 *– ZÁPAD USA*a vnet2 – východ *USA*.
- **Úložiště**: Vytvořte nový účet úložiště pro cluster.
- **Virtuální síť** (z rozšířené nastavení na portálu): Vyberte vnet1, které jste vytvořili v posledním postupu.
- **Podsíť**: Výchozí název použitý v šabloně je **podsíť1**.

Chcete-li zajistit správné nakonfigurování prostředí, musíte být schopni ping hlavního název sítě pro propojení žen a služeb a služeb a) mezi dvěma clustery.

## <a name="load-test-data"></a>Načtení testovacích dat

Při replikaci clusteru je nutné zadat tabulky, které chcete replikovat. V této části načtete některá data do zdrojového clusteru. V další části povolíte replikaci mezi dvěma clustery.

Chcete-li vytvořit tabulku **Kontaktů** a vložit do ní některá data, postupujte podle pokynů v [kurzu Apache HBase: Začínáme používat Apache HBase v HDInsightu](apache-hbase-tutorial-get-started-linux.md).

> [!NOTE]
> Pokud chcete replikovat tabulky z vlastního oboru názvů, je třeba zajistit, aby příslušné vlastní obory názvů byly definovány také v cílovém clusteru.
>

## <a name="enable-replication"></a>Povolení replikace

Následující kroky popisují, jak volat skript akce skriptu skriptu skriptu z portálu Azure. Informace o spuštění akce skriptu pomocí Azure PowerShellu a rozhraní příkazového příkazu KONT Azure Classic najdete [v tématu Přizpůsobení clusterů HDInsight pomocí akce skriptu](../hdinsight-hadoop-customize-cluster-linux.md).

**Povolení replikace HBase z portálu Azure**

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Otevřete zdrojový cluster HBase.
3. V nabídce clusteru vyberte **Možnost Akce skriptu**.
4. V horní části stránky vyberte **Odeslat nový**.
5. Vyberte nebo zadejte následující informace:

   1. **Název**: Zadejte **Povolit replikaci**.
   2. **Bash Script**URL **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**: Zadejte .
   3. **Hlava**: Ujistěte se, že je vybrána. Zrušte ostatní typy uzlů.
   4. **Parametry**: Následující ukázkové parametry umožňují replikaci pro všechny existující tabulky a pak zkopírují všechna data ze zdrojového clusteru do cílového clusteru:

          -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
      > [!NOTE]
      > Místo názvu domény na domény použijte název hostitele pro název DNS zdrojového i cílového clusteru.
      >
      > Tento návod předpokládá hn1 jako aktivní headnode. Zkontrolujte cluster k identifikaci aktivního hlavního uzlu.

6. Vyberte **Vytvořit**. Spuštění skriptu může chvíli trvat, zejména při použití argumentu **-copydata.**

Požadované argumenty:

|Name (Název)|Popis|
|----|-----------|
|-s, --src-cluster | Určuje název DNS zdrojového clusteru HBase. Například: -s hbsrccluster, --src-cluster=hbsrccluster |
|-d, --dst-cluster | Určuje název DNS cílového (replikového) clusteru HBase. Příklad: -s dsthbcluster, --src-cluster=dsthbcluster |
|-sp, --src-ambari-heslo | Určuje heslo správce pro Ambari ve zdrojovém clusteru HBase. |
|-dp, --dst-ambari-heslo | Určuje heslo správce pro ambari v cílovém clusteru HBase.|

Volitelné argumenty:

|Name (Název)|Popis|
|----|-----------|
|-su, --src-ambari-uživatel | Určuje uživatelské jméno správce pro Ambari ve zdrojovém clusteru HBase. Výchozí hodnota je **admin**. |
|-du, --dst-ambari-uživatel | Určuje uživatelské jméno správce pro Ambari v cílovém clusteru HBase. Výchozí hodnota je **admin**. |
|-t, --seznam tabulek | Určuje replikované tabulky. Příklad: --table-list="table1;table2;table3". Pokud nezadáte tabulky, budou replikovány všechny existující tabulky HBase.|
|-m, --stroj | Určuje hlavní uzel, ve kterém je spuštěna akce skriptu. Hodnota by měla být zvolena na základě toho, který je aktivní hlavní uzel. Tuto možnost použijte, když spouštěte skript $0 jako akci skriptu z portálu HDInsight nebo Azure PowerShellu.|
|-cp, -copydata | Umožňuje migraci existujících dat v tabulkách, kde je povolena replikace. |
|-rpm, -replik-phoenix-meta | Umožňuje replikaci v systémových tabulkách Phoenix. <br><br>*Tuto možnost používejte opatrně.* Doporučujeme znovu vytvořit tabulky Phoenix na clustery replik před použitím tohoto skriptu. |
|-h, --pomoc | Zobrazí informace o využití. |

Část `print_usage()` [skriptu](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) obsahuje podrobné vysvětlení parametrů.

Po úspěšném nasazení akce skriptu můžete pomocí ssh připojit k cílovému clusteru HBase a potom ověřit, zda byla data replikována.

### <a name="replication-scenarios"></a>Scénáře replikace

V následujícím seznamu jsou uvedeny některé obecné případy použití a jejich nastavení parametrů:

- **Povolte replikaci ve všech tabulkách mezi dvěma clustery**. Tento scénář nevyžaduje kopírování nebo migraci existujících dat v tabulkách a nepoužívá tabulky Phoenix. Použijte následující parametry:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Povolte replikaci v konkrétních tabulkách**. Chcete-li povolit replikaci v tabulce1, tabulce2 a tabulce3, použijte následující parametry:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Povolte replikaci v konkrétních tabulkách a zkopírujte existující data**. Chcete-li povolit replikaci v tabulce1, tabulce2 a tabulce3, použijte následující parametry:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Povolte replikaci ve všech tabulkách a replikujte metadata Phoenix ze zdroje do cíle**. Phoenix replikace metadat není dokonalá. Používejte jej opatrně. Použijte následující parametry:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Kopírování a migrace dat

Po povolení replikace jsou k dispozici dva samostatné skripty akce pro kopírování nebo migraci dat:

- [Skript pro malé tabulky](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (tabulky o velikosti několika gigabajtů a očekává se, že celková kopie bude dokončena za méně než jednu hodinu)

- [Skript pro velké tabulky](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (tabulky, u kterých se očekává, že bude trvat déle než jednu hodinu)

Můžete postupovat stejným způsobem, který je popsán v [povolit replikaci](#enable-replication) volat akci skriptu. Použijte následující parametry:

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

Část `print_usage()` [skriptu](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) obsahuje podrobný popis parametrů.

### <a name="scenarios"></a>Scénáře

- **Zkopírujte konkrétní tabulky (test1, test2 a test3) pro všechny řádky upravené dosud (aktuální časové razítko):**

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  Nebo:

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Kopírování konkrétních tabulek se zadaným časovým rozsahem**:

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>Zákaz replikace

Chcete-li zakázat replikaci, použijte jiný skript akce skriptu z [GitHubu](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). Můžete postupovat stejným způsobem, který je popsán v [povolit replikaci](#enable-replication) volat akci skriptu. Použijte následující parametry:

    -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

Část `print_usage()` [skriptu](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) obsahuje podrobné vysvětlení parametrů.

### <a name="scenarios"></a>Scénáře

- **Zakázat replikaci ve všech tabulkách**:

        -m hn1 -s <source hbase cluster name> -sp Mypassword\!789 -all
  – nebo –

        --src-cluster=<source hbase cluster name> --dst-cluster=<destination hbase cluster name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **Zakázat replikaci v určených tabulkách (tabulka1, tabulka2 a tabulka3):**

        -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> -t "table1;table2;table3"

> [!NOTE]
> Pokud chcete cílový cluster odstranit, nezapomeňte jej odebrat ze seznamu rovnocenných členů zdrojového clusteru. To lze provést spuštěním příkazu remove_peer '1' v prostředí hbase ve zdrojovém clusteru. Pokud to není možné, zdrojový cluster nemusí fungovat správně.
>

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak nastavit replikaci Apache HBase v rámci virtuální sítě nebo mezi dvěma virtuálními sítěmi. Další informace o HDInsightu a Apache HBase najdete v těchto článcích:

* [Začínáme s Apache HBase v HDInsightu](./apache-hbase-tutorial-get-started-linux.md)
* [Přehled hdinsight apache hbáze](./apache-hbase-overview.md)
* [Vytvoření clusterů Apache HBase ve virtuální síti Azure](./apache-hbase-provision-vnet.md)

