---
title: Konfigurace naslouchacího procesu skupiny dostupnosti pro SQL Server na virtuálních počítačích s RHEL ve virtuálních počítačích Azure-Linux | Microsoft Docs
description: Přečtěte si o nastavení naslouchacího procesu skupiny dostupnosti v SQL Server na virtuálních počítačích s RHEL v Azure.
ms.service: virtual-machines-sql
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 03/11/2020
ms.openlocfilehash: 5f0b300be2f1cec4ee456065455832a2dc3598be
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449499"
---
# <a name="tutorial-configure-an-availability-group-listener-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Kurz: Konfigurace naslouchacího procesu skupiny dostupnosti pro SQL Server na virtuálních počítačích s RHEL v Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!NOTE]
> Uvedený kurz je ve **verzi Public Preview**. 
>
> V tomto kurzu používáme SQL Server 2017 s RHEL 7,6, ale ke konfiguraci vysoké dostupnosti je možné použít SQL Server 2019 v RHEL 7 nebo RHEL 8. Příkazy pro konfiguraci prostředků skupiny dostupnosti se v RHEL 8 změnily. Chcete-li získat další informace o správných příkazech, přečtěte si článek [vytvoření prostředku skupiny dostupnosti](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) a prostředků RHEL 8.

V tomto kurzu se dozvíte, jak vytvořit naslouchací proces skupiny dostupnosti pro SQL Server na virtuálních počítačích s RHEL v Azure. V tomto kurzu se naučíte:

> [!div class="checklist"]
> - Vytvoření nástroje pro vyrovnávání zatížení v Azure Portal
> - Konfigurace fondu back-end pro nástroj pro vyrovnávání zatížení
> - Vytvoření sondy pro nástroj pro vyrovnávání zatížení
> - Nastavení pravidel vyrovnávání zatížení
> - Vytvoření prostředku nástroje pro vyrovnávání zatížení v clusteru
> - Vytvoření naslouchacího procesu skupiny dostupnosti
> - Test připojení k naslouchacímu procesu
> - Testování převzetí služeb při selhání

## <a name="prerequisite"></a>Požadavek

Dokončený [kurz: Konfigurace skupin dostupnosti pro SQL Server na virtuálních počítačích RHEL v Azure](rhel-high-availability-stonith-tutorial.md)

## <a name="create-the-load-balancer-in-the-azure-portal"></a>Vytvoření nástroje pro vyrovnávání zatížení v Azure Portal

V následujících pokynech vás provedete kroky 1 až 4 z tématu [Vytvoření a konfigurace nástroje pro vyrovnávání zatížení v části Azure Portal v](../windows/availability-group-load-balancer-portal-configure.md#create--configure-load-balancer) článku [Azure Portal vyrovnávání zatížení](../windows/availability-group-load-balancer-portal-configure.md) .

### <a name="create-the-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

1. V Azure Portal otevřete skupinu prostředků, která obsahuje SQL Server virtuálních počítačů. 

2. Ve skupině prostředků klikněte na **Přidat**.

3. Vyhledejte **Nástroj pro vyrovnávání zatížení** a potom ve výsledcích hledání vyberte položku **Load Balancer**, která je publikována společností **Microsoft**.

4. V okně **Load Balancer** klikněte na **vytvořit**.

5. V dialogovém okně **vytvořit nástroj pro vyrovnávání zatížení** nakonfigurujte nástroj pro vyrovnávání zatížení následujícím způsobem:

   | Nastavení | Hodnota |
   | --- | --- |
   | **Název** |Textový název představující Nástroj pro vyrovnávání zatížení. Například **sqlLB**. |
   | **Typ** |**Vnitřních** |
   | **Virtuální síť** |Výchozí virtuální síť, která byla vytvořena, by měla mít název **VM1VNET**. |
   | **Podsíť** |Vyberte podsíť, ve které jsou instance SQL Server. Výchozí hodnota by měla být **VM1Subnet**.|
   | **Přiřazení IP adresy** |**staticky**. |
   | **Privátní IP adresa** |Použijte `virtualip` IP adresu, která byla vytvořena v clusteru. |
   | **Předplatné** |Použijte předplatné, které se použilo pro vaši skupinu prostředků. |
   | **Skupina prostředků** |Vyberte skupinu prostředků, ve které jsou instance SQL Server. |
   | **Umístění** |Vyberte umístění Azure, ve kterém jsou instance SQL Server. |

### <a name="configure-the-back-end-pool"></a>Konfigurace fondu back-end
Azure volá *fond back*-end fondu adres back-endu. V tomto případě je fond back-end adresami tří SQL Server instancí ve skupině dostupnosti. 

1. Ve vaší skupině prostředků klikněte na nástroj pro vyrovnávání zatížení, který jste vytvořili. 

2. V **Nastavení** klikněte na **back-endové fondy**.

3. V případě **back-endu fondů** klikněte na tlačítko **Přidat** a vytvořte fond adres back-endu. 

4. V části **název** do pole název zadejte **název back-** end fondu.

5. V části **přidruženo k** vyberte **virtuální počítač**. 

6. Vyberte každý virtuální počítač v prostředí a přidružte k jednotlivým výběrům příslušnou IP adresu.

    :::image type="content" source="media/rhel-high-availability-listener-tutorial/add-backend-pool.png" alt-text="Přidat back-end fond":::

7. Klikněte na **Přidat**. 

### <a name="create-a-probe"></a>Vytvoření testu paměti

Sonda definuje, jak Azure ověřuje, které instance SQL Server aktuálně vlastní naslouchací proces skupiny dostupnosti. Azure sonduje službu na základě IP adresy na portu, který definujete při vytváření testu.

1. V okně **Nastavení** nástroje pro vyrovnávání zatížení klikněte na **sondy stavu**. 

2. V okně **sondy stavu** klikněte na **Přidat**.

3. Nakonfigurujte sondu v okně **Přidat test paměti** . Ke konfiguraci testu použijte následující hodnoty:

   | Nastavení | Hodnota |
   | --- | --- |
   | **Název** |Textový název, který představuje test. Například **SQLAlwaysOnEndPointProbe**. |
   | **Protokol** |**TCP** |
   | **Port** |Můžete použít libovolný dostupný port. Například *59999*. |
   | **Interval** |*5* |
   | **Prahová hodnota pro poškozený stav** |*2* |

4.  Klikněte na **OK**. 

5. Přihlaste se ke všem virtuálním počítačům a otevřete zkušební port pomocí následujících příkazů:

    ```bash
    sudo firewall-cmd --zone=public --add-port=59999/tcp --permanent
    sudo firewall-cmd --reload
    ```

Azure vytvoří test a pak ho použije k otestování, která instance SQL Server má naslouchací proces pro skupinu dostupnosti.

### <a name="set-the-load-balancing-rules"></a>Nastavení pravidel vyrovnávání zatížení

Pravidla vyrovnávání zatížení konfigurují způsob, jakým nástroj pro vyrovnávání zatížení směruje provoz do instancí SQL Server. Pro tento nástroj pro vyrovnávání zatížení povolte přímé vrácení serveru, protože prostředek naslouchacího procesu skupiny dostupnosti má vlastní jenom jedna ze tří instancí SQL Server.

1. V okně **Nastavení** nástroje pro vyrovnávání zatížení klikněte na **pravidla vyrovnávání zatížení**. 

2. V okně **pravidla vyrovnávání zatížení** klikněte na **Přidat**.

3. V okně **Přidat pravidla vyrovnávání zatížení** nakonfigurujte pravidlo vyrovnávání zatížení. Použijte následující nastavení: 

   | Nastavení | Hodnota |
   | --- | --- |
   | **Název** |Textový název reprezentující pravidla vyrovnávání zatížení. Například **SQLAlwaysOnEndPointListener**. |
   | **Protokol** |**TCP** |
   | **Port** |*1433* |
   | **Back-endový port** |*1433*. Tato hodnota se ignoruje, protože toto pravidlo používá **plovoucí IP adresu (přímá návratová hodnota serveru)**. |
   | **Sonda** |Použijte název testu, který jste vytvořili pro tento nástroj pro vyrovnávání zatížení. |
   | **Trvalost relace** |**Žádný** |
   | **Časový limit nečinnosti (minuty)** |*4* |
   | **Plovoucí IP adresa (přímá návrat ze serveru)** |**Povoleno** |

   :::image type="content" source="media/rhel-high-availability-listener-tutorial/add-load-balancing-rule.png" alt-text="Přidat pravidlo vyrovnávání zatížení":::

4. Klikněte na **OK**. 
5. Azure nakonfiguruje pravidlo vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení teď má nakonfigurované směrování provozu do SQL Server instance, která hostuje naslouchací proces pro skupinu dostupnosti. 

V tomto okamžiku má skupina prostředků Nástroj pro vyrovnávání zatížení, který se připojuje ke všem počítačům s SQL Server. Nástroj pro vyrovnávání zatížení obsahuje také IP adresu pro naslouchací proces skupiny dostupnosti Always On SQL Server, aby každý počítač mohl reagovat na žádosti pro skupiny dostupnosti.

## <a name="create-the-load-balancer-resource-in-the-cluster"></a>Vytvoření prostředku nástroje pro vyrovnávání zatížení v clusteru

1. Přihlaste se k primárnímu virtuálnímu počítači. Musíme vytvořit prostředek, který povolí port testu služby Azure Load Balancer (59999 se v našem příkladu používá). Spusťte následující příkaz:

    ```bash
    sudo pcs resource create azure_load_balancer azure-lb port=59999
    ```

1. Vytvořte skupinu, která obsahuje `virtualip` prostředek a `azure_load_balancer` :

    ```bash
    sudo pcs resource group add virtualip_group azure_load_balancer virtualip
    ```

### <a name="add-constraints"></a>Přidat omezení

1. Aby bylo zajištěno, že IP adresa služby Vyrovnávání zatížení Azure a prostředek AG běží na stejném uzlu, musí být nakonfigurováno omezení kolokace. Spusťte následující příkaz:

    ```bash
    sudo pcs constraint colocation add azure_load_balancer ag_cluster-master INFINITY with-rsc-role=Master
    ```
1. Vytvořte omezení řazení, abyste měli jistotu, že je prostředek AG v provozu, a teprve potom IP adresu služby Azure Load Balancer. I když omezení pro společné umístění implikuje omezení řazení, vynutilo ho.

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start azure_load_balancer
    ```

1. Chcete-li ověřit omezení, spusťte následující příkaz:

    ```bash
    sudo pcs constraint list --full
    ```

    Měl by se zobrazit následující výstup:

    ```output
    Location Constraints:
    Ordering Constraints:
      promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
      promote ag_cluster-master then start azure_load_balancer (kind:Mandatory) (id:order-ag_cluster-master-azure_load_balancer-mandatory)
    Colocation Constraints:
      virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
      azure_load_balancer with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-azure_load_balancer-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

## <a name="create-the-availability-group-listener"></a>Vytvoření naslouchacího procesu skupiny dostupnosti

1. Na primárním uzlu spusťte v SQLCMD nebo SSMS následující příkaz:

    - Nahraďte IP adresu použitou níže s `virtualip` IP adresou.

    ```sql
    ALTER AVAILABILITY
    GROUP [ag1] ADD LISTENER 'ag1-listener' (
            WITH IP(('10.0.0.7'    ,'255.255.255.0'))
                ,PORT = 1433
            );
    GO
    ```

1. Přihlaste se ke každému uzlu virtuálního počítače. Pomocí následujícího příkazu otevřete soubor Hosts a nastavte překlad názvů hostitelů pro na `ag1-listener` každém počítači.

    ```
    sudo vi /etc/hosts
    ```

    V editoru **VI** zadejte `i` text pro vložení textu a na prázdný řádek přidejte IP adresu `ag1-listener` . Pak přidejte `ag1-listener` za mezeru vedle IP adresy.

    ```output
    <IP of ag1-listener> ag1-listener
    ```

    Chcete-li ukončit Editor **VI** , nejprve stiskněte klávesu **ESC** a potom zadejte příkaz `:wq` pro zápis souboru a ukončení. Udělejte to na každém uzlu.

## <a name="test-the-listener-and-a-failover"></a>Testování naslouchacího procesu a převzetí služeb při selhání

### <a name="test-logging-in-to-sql-server-using-the-availability-group-listener"></a>Otestujte přihlášení k SQL Server pomocí naslouchacího procesu skupiny dostupnosti.

1. Pomocí nástroje SQLCMD se přihlaste k primárnímu uzlu SQL Server pomocí názvu naslouchacího procesu skupiny dostupnosti:

    - Použijte přihlašovací jméno, které bylo dříve vytvořeno a nahraďte `<YourPassword>` správným heslem. Následující příklad používá `sa` přihlášení, které bylo vytvořeno pomocí SQL Server.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. Ověřte název serveru, ke kterému jste se připojili. V SQLCMD spusťte následující příkaz:

    ```sql
    SELECT @@SERVERNAME
    ```

    Výstup by měl zobrazovat aktuální primární uzel. To by mělo být `VM1` , pokud jste nikdy neotestovali převzetí služeb při selhání.

    Ukončete relaci SQL Server zadáním `exit` příkazu.

### <a name="test-a-failover"></a>Testování převzetí služeb při selhání

1. Spusťte následující příkaz, který selže při selhání primární repliky do `<VM2>` nebo jiné repliky. Nahraďte `<VM2>` hodnotou názvu vašeho serveru.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. Pokud zkontrolujete vaše omezení, uvidíte, že se do ručního převzetí služeb při selhání přidalo jiné omezení:

    ```bash
    sudo pcs constraint list --full
    ```

    Uvidíte, že bylo přidáno omezení s ID `cli-prefer-ag_cluster-master` .

1. Odeberte omezení s ID `cli-prefer-ag_cluster-master` pomocí následujícího příkazu:

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. Pomocí příkazu zkontrolujte prostředky clusteru `sudo pcs resource` a měli byste vidět, že je primární instance nyní `<VM2>` .

    > [!NOTE]
    > Tento článek obsahuje odkazy na podřízený termín, termín, který už Microsoft nepoužívá. Po odebrání termínu ze softwaru ho odebereme z tohoto článku.


    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
        Masters: [ <VM2> ]
        Slaves: [ <VM1> <VM3> ]
    Resource Group: virtualip_group
        azure_load_balancer        (ocf::heartbeat:azure-lb):      Started <VM2>
        virtualip  (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

1. Pomocí nástroje SQLCMD se přihlaste k primární replice pomocí názvu naslouchacího procesu:

    - Použijte přihlašovací jméno, které bylo dříve vytvořeno a nahraďte `<YourPassword>` správným heslem. Následující příklad používá `sa` přihlášení, které bylo vytvořeno pomocí SQL Server.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
     ```

1. Ověřte server, ke kterému jste připojeni. V SQLCMD spusťte následující příkaz:

    ```sql
    SELECT @@SERVERNAME
    ```

    Měli byste vidět, že jste nyní připojeni k virtuálnímu počítači, na který jste převzali převzetí služeb při selhání.

## <a name="next-steps"></a>Další kroky

Další informace o nástrojích pro vyrovnávání zatížení v Azure najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Konfigurace vyrovnávání zatížení pro skupinu dostupnosti v SQL Server na virtuálních počítačích Azure](../windows/availability-group-load-balancer-portal-configure.md)
