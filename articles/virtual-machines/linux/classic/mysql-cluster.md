---
title: Clusterize MySQL s vyrovnáváním zatížení sad | Microsoft Docs
description: Nastavení pro zařízení s vyrovnáváním zatížení, vysokou dostupnost clusteru Linux MySQL vytvořené pomocí modelu nasazení classic na platformě Azure
services: virtual-machines-linux
documentationcenter: ''
author: bureado
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 6c413a16-e9b5-4ffe-a8a3-ae67046bbdf3
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/14/2015
ms.author: jparrel
ms.openlocfilehash: e2671def47879e3d4eae000c9084cd458e29b933
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="use-load-balanced-sets-to-clusterize-mysql-on-linux"></a>Pomocí vyrovnávání zatížení sítě nastaví clusterize MySQL v systému Linux
> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Azure Resource Manager](../../../resource-manager-deployment-model.md) a classic. Tento článek se věnuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. A [šablony Resource Manageru](https://azure.microsoft.com/documentation/templates/mysql-replication/) je k dispozici, pokud je potřeba nasadit MySQL cluster.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

V tomto článku jsou zde popsány a ukazuje různý přístup k dispozici pro nasazení vysokou dostupností služby v Microsoft Azure, prohlížení vysoké dostupnosti serveru MySQL jako základy systémem Linux. Video ilustrující tento přístup je k dispozici na [Channel 9](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL).

Nemůžeme se popisují shared nothing, dvěma uzly, jeden hlavní řešení vysoké dostupnosti databáze MySQL na základě DRBD, Corosync a kardiostimulátor. Databáze MySQL byla najednou spuštěna pouze jeden uzel. Čtení a zápis z prostředku DRBD je také omezen na jenom jeden uzel v čase.

Není nutné pro VIP řešení jako LVS, protože budete používat sady Vyrovnávání zatížení sítě v Microsoft Azure k zajištění funkcí a koncový bod zjišťování kruhového dotazování, odebrání a úspěšné obnovení virtuální IP adresu. Virtuální IP adresa je globálně směrovatelné adresu IPv4 přiřazené službou Microsoft Azure při prvním vytvoření cloudové služby.

Nejsou k dispozici jako virtuální počítač na další možné architektury pro databázi MySQL, včetně NBD clusteru, Percona, Galera a několik řešení middleware, včetně alespoň jeden [skladu virtuálních počítačů](http://vmdepot.msopentech.com). Dokud tato řešení můžete replikovat na jednosměrové a vícesměrové vysílání nebo všesměrové vysílání a nejsou spoléhají na sdílené úložiště nebo více síťových rozhraní, musí být scénáře snadno nasadit v Microsoft Azure.

Tyto clustering architektury lze rozšířit o další produkty jako PostgreSQL a OpenLDAP podobným způsobem. Například tento postup Vyrovnávání zatížení s nesdílená byla úspěšně testována s více hlavní OpenLDAP a můžete ho sledovat na našem blogu Channel 9.

## <a name="get-ready"></a>Příprava
Budete potřebovat následující prostředky a dalo:

  - Účet Microsoft Azure s platným předplatným, moci vytvořit aspoň dva virtuální počítače (XS byl použit v tomto příkladu)
  - Síť a podsíť
  - Skupiny vztahů
  - Nastavení dostupnosti
  - Možnost vytvoříte virtuální pevné disky ve stejné oblasti jako cloudová služba a připojte je k virtuální počítače s Linuxem

### <a name="tested-environment"></a>Otestované prostředí
* Ubuntu 13.10
  * DRBD
  * MySQL Server
  * Corosync a kardiostimulátor

### <a name="affinity-group"></a>Skupina vztahů
Vytvořit skupinu vztahů pro řešení po přihlášení k portálu Azure vyberte **nastavení**a vytváření skupiny vztahů. Přidělené prostředky vytvořit později přiřadí do této skupiny vztahů.

### <a name="networks"></a>Networks
Se vytvoří nová síť a podsíť je vytvořit uvnitř sítě. Tento příklad používá 10.10.10.0/24 síť s podsítí pouze jeden /24 uvnitř.

### <a name="virtual-machines"></a>Virtuální počítače
První virtuálního počítače s Ubuntu 13.10 je vytvořená pomocí image Galerie Endorsed Ubuntu a se nazývá `hadb01`. Nová Cloudová služba je vytvořená ve proces se nazývá hadb. Tento název je znázorněný v podobě sdílené, Vyrovnávání zatížení sítě, které služba bude mít po přidání více prostředků. Vytvoření `hadb01` je bezproblémové a dokončených pomocí portálu. Koncový bod SSH se automaticky vytvoří a bude nová síť je vybrána. Nyní můžete vytvořit sadu dostupnosti pro virtuální počítače.

Po vytvoření první virtuální počítač (technicky vytvoření cloudové služby) vytvořit druhý virtuální počítač, `hadb02`. Pro druhý virtuální počítač, použijte virtuálního počítače s Ubuntu 13.10 z Galerie pomocí portálu, ale použít stávající cloudovou službu, `hadb.cloudapp.net`, místo vytvoření nové. Měla by být automaticky vybrána sadu sítě a dostupnost. Koncový bod SSH bude vytvořen, příliš.

Po vytvoření oba virtuální počítače, poznamenejte si portu SSH pro `hadb01` (port TCP 22) a `hadb02` (automaticky přiřazené Azure).

### <a name="attached-storage"></a>Připojené úložiště
Nový disk Připojte oba virtuální počítače a vytvořit 5 GB disky v procesu. Disky jsou hostované v kontejneru VHD použijte pro disky hlavní operační systém. Po disky jsou vytvořeny a připojené, není nutné restartovat Linux, protože nové zařízení se zobrazí jádra. Toto zařízení je většinou `/dev/sdc`. Zkontrolujte `dmesg` pro výstup.

Na každý virtuální počítač vytvořit oddíl pomocí `cfdisk` (primární, Linux oddíl) a zápisu v nové tabulce oddílu. Nevytvářejte systém souborů na tento oddíl.

## <a name="set-up-the-cluster"></a>Nastavení clusteru
Použijte k instalaci Corosync, kardiostimulátor a DRBD oba virtuální počítače Ubuntu byt č. Uděláte to tak s `apt-get`, spusťte následující kód:

    sudo apt-get install corosync pacemaker drbd8-utils.

V tuto chvíli neinstalujte MySQL. Debian a Ubuntu instalační skripty inicializuje datový adresář MySQL na `/var/lib/mysql`, ale protože adresáři bude nahrazena systémem souborů DRBD, budete muset nainstalovat MySQL později.

Ověření (pomocí `/sbin/ifconfig`) že jsou oba virtuální počítače pomocí adresami v podsíti 10.10.10.0/24 a že se navzájem ping podle názvu. Můžete také použít `ssh-keygen` a `ssh-copy-id` a ujistěte se, oba virtuální počítače mohou komunikovat pomocí protokolu SSH bez nutnosti heslo.

### <a name="set-up-drbd"></a>Nastavit DRBD
Vytvořit DRBD prostředek, který používá základní `/dev/sdc1` oddíl, který chcete vytvořit `/dev/drbd1` prostředků, kterou můžete naformátovat pomocí ext3 a používá primární i sekundární uzly.

1. Otevřete `/etc/drbd.d/r0.res` a zkopírujte následující definici prostředků na oba virtuální počítače:

        resource r0 {
          on `hadb01` {
            device  /dev/drbd1;
            disk   /dev/sdc1;
            address  10.10.10.4:7789;
            meta-disk internal;
          }
          on `hadb02` {
            device  /dev/drbd1;
            disk   /dev/sdc1;
            address  10.10.10.5:7789;
            meta-disk internal;
          }
        }

2. Inicializace prostředku pomocí `drbdadm` na oba virtuální počítače:

        sudo drbdadm -c /etc/drbd.conf role r0
        sudo drbdadm up r0

3. Na primárním virtuálním počítači (`hadb01`), vynutit vlastnictví (primární) DRBD prostředku:

        sudo drbdadm primary --force r0

Pokud si projdete obsah nebo proc/drbd (`sudo cat /proc/drbd`) na oba virtuální počítače, měli byste vidět `Primary/Secondary` na `hadb01` a `Secondary/Primary` na `hadb02`a konzistentní s řešením v tomto okamžiku. 5 GB disk se synchronizují přes síť 10.10.10.0/24 zdarma pro zákazníky.

Po synchronizaci disk můžete vytvořit systém souborů na `hadb01`. Pro účely testování jsme použili ext2, ale následující kód vytvoří systém souborů ext3:

    mkfs.ext3 /dev/drbd1

### <a name="mount-the-drbd-resource"></a>Připojte prostředek DRBD
Nyní jste připraveni připojit prostředky DRBD na `hadb01`. Použití debian a odvozené konfigurace `/var/lib/mysql` jako adresář data na MySQL. Protože jste nenainstalovali MySQL, vytvořit adresář a připojte DRBD prostředků. Chcete-li provést tuto možnost, spusťte následující kód na `hadb01`:

    sudo mkdir /var/lib/mysql
    sudo mount /dev/drbd1 /var/lib/mysql

## <a name="set-up-mysql"></a>Nastavit MySQL
Nyní jste připraveni k instalaci databáze MySQL na `hadb01`:

    sudo apt-get install mysql-server

Pro `hadb02`, máte dvě možnosti. Můžete nainstalovat mysql-server, který bude vytvářet /var/lib/mysql, vyplnit nový adresář dat a pak odeberte obsah. Chcete-li provést tuto možnost, spusťte následující kód na `hadb02`:

    sudo apt-get install mysql-server
    sudo service mysql stop
    sudo rm –rf /var/lib/mysql/*

Druhou možností je převzetí služeb při selhání `hadb02` a pak nainstalujte mysql-server existuje. Skripty instalace si všimněte existující instalaci a nebude touch.

Spusťte následující kód `hadb01`:

    sudo drbdadm secondary –force r0

Spusťte následující kód `hadb02`:

    sudo drbdadm primary –force r0
    sudo apt-get install mysql-server

Pokud nemáte v úmyslu převzetí služeb při selhání DRBD nyní, první možností je jednodušší, i když pravděpodobně méně elegantní. Po nastavit tuto možnost, můžete začít pracovat ve vaší databázi MySQL. Spusťte následující kód `hadb02` (nebo libovolného jeden ze serverů je aktivní, podle DRBD):

    mysql –u root –p
    CREATE DATABASE azureha;
    CREATE TABLE things ( id SERIAL, name VARCHAR(255) );
    INSERT INTO things VALUES (1, "Yet another entity");
    GRANT ALL ON things.\* TO root;

> [!WARNING]
> Tento poslední příkaz efektivně zakáže ověřování pro kořenového uživatele v této tabulce. To by měl být nahrazen produkční úrovni udělit příkazy a je jen pro ilustraci zahrnuty.

Pokud chcete, aby dotazy z mimo virtuálních počítačů (což je účel tohoto průvodce), musíte také povolit sítě pro databázi MySQL. Na oba virtuální počítače, otevřete `/etc/mysql/my.cnf` a přejděte na `bind-address`. Změňte adresu z adresy 127.0.0.1 na hodnotu 0.0.0.0. Po uložení souboru, vydávání `sudo service mysql restart` na váš aktuální primární.

### <a name="create-the-mysql-load-balanced-set"></a>Vytvořit sadu MySQL Vyrovnávání zatížení sítě
Přejděte zpět na portálu, přejděte na `hadb01`a zvolte **koncové body**. Pokud chcete vytvořit koncový bod, zvolte MySQL (TCP 3306) z rozevíracího seznamu a vyberte **s vyrovnáváním zatížení vytvořit nové**. Název koncového bodu Vyrovnávání zatížení sítě `lb-mysql`. Nastavit **čas** na 5 sekund, minimální.

Po vytvoření koncového bodu, přejděte na `hadb02`, zvolte **koncové body**a vytvořit koncový bod. Zvolte `lb-mysql`a pak z rozevíracího seznamu vyberte MySQL. Můžete také použít rozhraní příkazového řádku Azure pro tento krok.

Nyní máte všechny potřebné pro ruční operaci clusteru.

### <a name="test-the-load-balanced-set"></a>Testovací sady vyrovnáváním zatížení
Testy můžete provést z mimo počítač, pomocí libovolného klienta, MySQL, nebo pomocí některých aplikací, jako je phpMyAdmin spuštěna jako web Azure. V takovém případě použít nástroj příkazového řádku na MySQL na jiného pole Linux:

    mysql azureha –u root –h hadb.cloudapp.net –e "select * from things;"

### <a name="manually-failing-over"></a>Ručně přebírání služeb při selhání
Převzetí služeb při selhání můžete simulovat vypínání databáze MySQL, přepnutí na DRBD primární a znovu spustit MySQL.

K provedení této úlohy, spusťte následující kód na hadb01:

    service mysql stop && umount /var/lib/mysql ; drbdadm secondary r0

Pak klikněte na hadb02:

    drbdadm primary r0 ; mount /dev/drbd1 /var/lib/mysql && service mysql start

Po selhání ručně, můžete opakovat vzdálený dotaz a měli perfektně fungovat.

## <a name="set-up-corosync"></a>Nastavit Corosync
Corosync je základní clusteru infrastrukturu potřebnou pro kardiostimulátor pracovat. Pro zjišťování prezenčního signálu (a další metody jako Ultramonkey) je Corosync rozdělení funkce CRM, když kardiostimulátor zůstane více podobná prezenčního signálu ve funkcích.

Hlavní omezení pro Corosync v Azure je Corosync upřednostní vícesměrového vysílání přes všesměrové vysílání přes komunikace jednosměrového vysílání, že Microsoft Azure sítě podporuje pouze jednosměrového vysílání.

Naštěstí Corosync má pracovní režim jednosměrového vysílání. Pouze skutečné omezení je, protože všechny uzly nejsou komunikaci mezi sebou, je třeba definovat uzly do konfiguračních souborů, včetně jejich IP adresy. Můžeme použít soubory příklad Corosync pro jednosměrového vysílání a změňte vazby adresu, uzel seznamy a protokolování adresáře (Ubuntu používá `/var/log/corosync` při použití souborů v příkladu `/var/log/cluster`) a povolit nástroje kvora.

> [!NOTE]
> Použijte následující `transport: udpu` směrnice a ručně definované IP adresy pro oba uzly.

Spusťte následující kód `/etc/corosync/corosync.conf` pro oba uzly:

    totem {
      version: 2
      crypto_cipher: none
      crypto_hash: none
      interface {
        ringnumber: 0
        bindnetaddr: 10.10.10.0
        mcastport: 5405
        ttl: 1
      }
      transport: udpu
    }

    logging {
      fileline: off
      to_logfile: yes
      to_syslog: yes
      logfile: /var/log/corosync/corosync.log
      debug: off
      timestamp: on
      logger_subsys {
        subsys: QUORUM
        debug: off
        }
      }

    nodelist {
      node {
        ring0_addr: 10.10.10.4
        nodeid: 1
      }

      node {
        ring0_addr: 10.10.10.5
        nodeid: 2
      }
    }

    quorum {
      provider: corosync_votequorum
    }

Zkopírujte tento konfigurační soubor na oba virtuální počítače a spustit Corosync v obou uzlů:

    sudo service start corosync

Krátce po spuštění služby, by se mělo vytvořit cluster v aktuální prstenec a by měl být vytvářen kvora. Tato funkce jsme můžete zkontrolovat kontrolou protokolů nebo spuštěním následující kód:

    sudo corosync-quorumtool –l

Zobrazí se výstup podobný na následujícím obrázku:

![corosync quorumtool - l ukázkový výstup](./media/mysql-cluster/image001.png)

## <a name="set-up-pacemaker"></a>Nastavit kardiostimulátor
Kardiostimulátor používá k monitorování pro prostředky, zadejte, kdy základní barvy přejděte a přepněte tyto prostředky do sekundární databáze clusteru. Prostředky lze definovat ze sady skriptů, k dispozici nebo z LSB skripty (init jako), mezi další možnosti.

Chceme kardiostimulátor na "vlastní" prostředek DRBD, přípojného bodu a službu MySQL. Pokud kardiostimulátor můžete zapnout a vypnout DRBD, připojit a odpojit a pak spusťte a ukončete MySQL ve správném pořadí když s primární se stane něco chybný, instalace byla dokončena.

Při první instalaci kardiostimulátor, musí být dostatečně jednoduchá něco jako konfiguraci:

    node $id="1" hadb01
      attributes standby="off"
    node $id="2" hadb02
      attributes standby="off"

1. Zkontrolujte konfiguraci spuštěním `sudo crm configure show`.
2. Pak vytvořte soubor (například `/tmp/cluster.conf`) se v následujících zdrojích informací:

        primitive drbd_mysql ocf:linbit:drbd \
              params drbd_resource="r0" \
              op monitor interval="29s" role="Master" \
              op monitor interval="31s" role="Slave"

        ms ms_drbd_mysql drbd_mysql \
              meta master-max="1" master-node-max="1" \
                clone-max="2" clone-node-max="1" \
                notify="true"

        primitive fs_mysql ocf:heartbeat:Filesystem \
              params device="/dev/drbd/by-res/r0" \
              directory="/var/lib/mysql" fstype="ext3"

        primitive mysqld lsb:mysql

        group mysql fs_mysql mysqld

        colocation mysql_on_drbd \
               inf: mysql ms_drbd_mysql:Master

        order mysql_after_drbd \
               inf: ms_drbd_mysql:promote mysql:start

        property stonith-enabled=false

        property no-quorum-policy=ignore

3. Načtení souboru do konfigurace. Stačí to udělat v jednom uzlu.

        sudo crm configure
          load update /tmp/cluster.conf
          commit
          exit

4. Ujistěte se, že kardiostimulátor začíná na spouštění v obou uzlů:

        sudo update-rc.d pacemaker defaults

5. Pomocí `sudo crm_mon –L`, ověřte, že jeden z uzlů se stal hlavní pro cluster a běží všechny prostředky. Zkontrolujte, zda jsou spuštěny prostředky můžete připojit a ps.

Následující snímek obrazovky ukazuje `crm_mon` s jedním uzlem zastavena (ukončení tak, že vyberete kombinaci kláves Ctrl + C):

![uzel crm_mon zastavena](./media/mysql-cluster/image002.png)

Tento snímek obrazovky ukazuje uzly, jeden z nich a jeden podřízený:

![podřízený provozní hlavní crm_mon](./media/mysql-cluster/image003.png)

## <a name="testing"></a>Testování
Jste připraveni simulaci automatické převzetí služeb při selhání. Existují dva způsoby, jak to udělat: a nepodmíněných.

Logicky způsob využívá funkce vypnutí clusteru: ``crm_standby -U `uname -n` -v on``. Pokud používáte na hlavním serveru tím, že podřízená má. Nezapomeňte nastavit zpět na vypnuto. Pokud to neuděláte, crm_mon zobrazí jeden uzel do úsporného režimu.

Pevné způsob, jakým se vypíná primárního virtuálního počítače (hadb01) prostřednictvím portálu nebo změnou runlevel ve virtuálním počítači (to znamená, zastavení, vypnutí). To pomáhá Corosync a kardiostimulátor podle signalizace, která je hlavním směrem dolů. Toto můžete otestovat (vhodný pro údržbu), ale můžete taky přinutit, tento scénář zmrazené virtuálního počítače.

## <a name="stonith"></a>STONITH
Musí být možné vydat vypnutí virtuálního počítače prostřednictvím rozhraní příkazového řádku Azure místo STONITH skript, který řídí fyzického zařízení. Můžete použít `/usr/lib/stonith/plugins/external/ssh` jako základní a povolit STONITH v konfiguraci clusteru. Rozhraní příkazového řádku Azure by měly být globálně nainstalovány a publikovat nastavení a profil by měly být načteny pro uživatele clusteru.

Ukázkový kód pro prostředek je k dispozici na [Githubu](https://github.com/bureado/aztonith). Změnit konfiguraci clusteru přidáním následujícího `sudo crm configure`:

    primitive st-azure stonith:external/azure \
      params hostlist="hadb01 hadb02" \
      clone fencing st-azure \
      property stonith-enabled=true \
      commit

> [!NOTE]
> Skript neprovede nahoru/dolů kontroly. Původní prostředků SSH měl 15 příkaz ping kontroluje, ale čas obnovení pro virtuální počítač Azure může být další proměnná.

## <a name="limitations"></a>Omezení
Platí následující omezení:

* Skript linbit DRBD prostředku, který spravuje DRBD jako prostředek v kardiostimulátor používá `drbdadm down` při vypnutí uzlu, i když uzlu se právě děje pohotovostní režim. Toto není ideální vzhledem k tomu, že podřízená nebude možné synchronizace DRBD prostředků při hlavní získá zápisy. Pokud je hlavní server neselže zdvořile, že podřízená může trvat přes starší stav systému souborů. Existují dva způsoby potenciální toto řešení:
  * Vynucení `drbdadm up r0` ve všech uzlech clusteru prostřednictvím místní sledovací zařízení (ne clusterized)
  * Úpravy linbit DRBD skript, a ověřte, zda `down` není volán `/usr/lib/ocf/resource.d/linbit/drbd`
* Nástroje pro vyrovnávání zatížení potřeba alespoň pět sekund reagovat, takže aplikace by měla být clustery a být větší toleranci vůči časový limit. Jiné architektury, jako v aplikaci fronty a middlewares dotaz, může také pomoct.
* Ladění MySQL je nutné zajistit, že zápis se provádí spravovat tempem a mezipaměti jsou vyprazdňuje na disk se často chcete-li minimalizovat ztrátu paměti.
* Zápis výkonu je závislý na virtuální počítač propojení ve virtuálním přepínači, protože to je používáno DRBD k replikaci zařízení.
