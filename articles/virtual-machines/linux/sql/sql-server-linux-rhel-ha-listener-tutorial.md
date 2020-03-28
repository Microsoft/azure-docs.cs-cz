---
title: Konfigurace naslouchací proces skupiny dostupnosti pro SQL Server na virtuálních počítačích RHEL v Azure – virtuální počítače SIP | Dokumenty společnosti Microsoft
description: Informace o nastavení naslouchací proces skupiny dostupnosti na SQL Serveru na virtuálních počítačích RHEL v Azure
ms.service: virtual-machines-linux
ms.subservice: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 03/11/2020
ms.openlocfilehash: 80557eb3776ba17a4922d1fc384b87419ffbd67e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79096590"
---
# <a name="tutorial-configure-availability-group-listener-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Kurz: Konfigurace naslouchací proces skupiny dostupnosti pro SQL Server na virtuálních počítačích RHEL v Azure

> [!NOTE]
> Prezentovaný kurz je ve **verzi Public Preview**. 
>
> V tomto kurzu používáme SQL Server 2017 s RHEL 7.6, ale je možné použít SQL Server 2019 v RHEL 7 nebo RHEL 8 ke konfiguraci HA. Příkazy ke konfiguraci prostředků skupiny dostupnosti se změnily v RHEL 8 a budete chtít podívat na článek, [Vytvořit zdroj skupiny dostupnosti](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) a zdroje RHEL 8 pro další informace o správné příkazy.

V tomto kurzu naleznete kroky, jak vytvořit naslouchací proces skupiny dostupnosti pro vaše servery SQL na virtuálních počítačích RHEL v Azure. V tomto kurzu se naučíte:

> [!div class="checklist"]
> - Vytvoření správce zatížení na webu Azure Portal
> - Konfigurace back-endového fondu pro balancer na zatížení
> - Vytvoření sondy pro balancer
> - Nastavení pravidel vyrovnávání zatížení
> - Vytvoření prostředku správce zatížení v clusteru
> - Vytvoření naslouchací proces skupiny dostupnosti
> - Testování připojení k naslouchacímu procesu
> - Testování převzetí služeb při selhání

## <a name="prerequisite"></a>Požadavek

[ **Dokončený kurz: Konfigurace skupin dostupnosti pro SQL Server na virtuálních počítačích RHEL v Azure**](sql-server-linux-rhel-ha-stonith-tutorial.md)

## <a name="create-the-load-balancer-in-the-azure-portal"></a>Vytvoření správce zatížení na webu Azure Portal

Následující pokyny vás provedou kroky 1 až 4 z [vytvoření a konfigurace vyrovnávání zatížení v](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md#create-and-configure-the-load-balancer-in-the-azure-portal) části Portál Azure v článku [Vyrovnávání zatížení – portál Azure.](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)

### <a name="create-the-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

1. Na webu Azure Portal otevřete skupinu prostředků, která obsahuje virtuální počítače SQL Serveru. 

2. Ve skupině prostředků klepněte na tlačítko **Přidat**.

3. Vyhledejte **systém vyrovnávání zatížení** a ve výsledcích hledání vyberte **položku Balancer**, kterou vydává společnost **Microsoft**.

4. V okně **Balancer zatížení** klepněte na **tlačítko Vytvořit**.

5. V dialogovém okně **Vytvořit balancer** nakonfigurujte vyvažovač zatížení následujícím způsobem:

   | Nastavení | Hodnota |
   | --- | --- |
   | **Název** |Textový název představující provynační stav zatížení. Například **sqlLB**. |
   | **Typ** |**Vnitřní** |
   | **Virtuální síť** |Výchozí virtuální síť, která byla vytvořena by měl mít název **VM1VNET**. |
   | **Podsíť** |Vyberte podsíť, ve které se nacházejí instance serveru SQL Server. Výchozí hodnota by měla být **VM1Subnet**.|
   | **Přiřazení IP adresy** |**Statické** |
   | **Privátní IP adresa** |Použijte `virtualip` adresu IP, která byla vytvořena v clusteru. |
   | **Předplatné** |Použijte předplatné, které bylo použito pro vaši skupinu prostředků. |
   | **Skupina prostředků** |Vyberte skupinu prostředků, ve které se nacházejí instance serveru SQL Server. |
   | **Umístění** |Vyberte umístění Azure, ve kterých se nacházejí instance SQL Serveru. |

### <a name="configure-the-back-end-pool"></a>Konfigurace back-endového fondu
Azure volá back-endový *fond back-endového fondu adres*. V tomto případě back-end fond je adresy tří instancí serveru SQL Server ve skupině dostupnosti. 

1. Ve skupině prostředků klikněte na prostředek pro vyrovnávání zatížení, které jste vytvořili. 

2. V **části Nastavení**klepněte na **položku Fondy back-end .**

3. V **back-endových fondech**klikněte na **Přidat** a vytvořte fond adres back-end. 

4. V **části Přidat back-endový fond**zadejte název back-endového fondu. **Name**

5. V části **Přidružená k**vyberte **Virtuální počítač**. 

6. Vyberte každý virtuální počítač v prostředí a přidružte ke každému výběru příslušnou adresu IP.

    :::image type="content" source="media/sql-server-linux-rhel-ha-listener-tutorial/add-backend-pool.png" alt-text="Přidat back-endový fond":::

7. Klikněte na **Přidat**. 

### <a name="create-a-probe"></a>Vytvoření sondy

Sonda definuje, jak Azure ověří, které instance serveru SQL Server aktuálně vlastní naslouchací proces skupiny dostupnosti. Azure sonduje službu na základě IP adresy na portu, který definujete při vytváření sondy.

1. V okně **Nastavení** pro vyrovnávání zatížení klepněte na **položku Sondy stavu**. 

2. V okně **Sondy stavu** klepněte na tlačítko **Přidat**.

3. Nakonfigurujte sondu v okně **Přidat sondu.** Ke konfiguraci sondy použijte následující hodnoty:

   | Nastavení | Hodnota |
   | --- | --- |
   | **Název** |Textový název představující sondu. Například **SQLAlwaysOnEndPointProbe**. |
   | **Protokol** |**TCP** |
   | **Port** |Můžete použít libovolný dostupný port. Například *59999*. |
   | **Interval** |*5* |
   | **Prahová hodnota pro poškozený stav** |*2* |

4.  Klikněte na tlačítko **OK**. 

5. Přihlaste se ke všem virtuálním počítačům a otevřete port sondy pomocí následujících příkazů:

    ```bash
    sudo firewall-cmd --zone=public --add-port=59999/tcp --permanent
    sudo firewall-cmd --reload
    ```

Azure vytvoří sondu a pak ji použije k testování, která instance serveru SQL Server má naslouchací proces pro skupinu dostupnosti.

### <a name="set-the-load-balancing-rules"></a>Nastavení pravidel vyrovnávání zatížení

Pravidla vyrovnávání zatížení nakonfigurují způsob, jakým nástroje pro vyrovnávání zatížení směruje provoz do instancí serveru SQL Server. Pro tento nástroj pro vyrovnávání zatížení povolíte přímé vrácení serveru, protože pouze jedna ze tří instancí serveru SQL Server vlastní prostředek posluchače skupiny dostupnosti najednou.

1. V okně **Nastavení** pro vyrovnávání zatížení klepněte na **položku Pravidla vyrovnávání zatížení**. 

2. V okně **Pravidla vyrovnávání zatížení** klepněte na tlačítko **Přidat**.

3. V okně **Přidat pravidla vyrovnávání zatížení** nakonfigurujte pravidlo vyrovnávání zatížení. Použijte následující nastavení: 

   | Nastavení | Hodnota |
   | --- | --- |
   | **Název** |Textový název představující pravidla vyrovnávání zatížení. Například **SQLAlwaysOnEndPointListener**. |
   | **Protokol** |**TCP** |
   | **Port** |*1433* |
   | **Back-endový port** |*1433*. Tato hodnota je ignorována, protože toto pravidlo používá **plovoucí IP (přímé vrácení serveru)**. |
   | **Sonda** |Použijte název sondy, kterou jste vytvořili pro tento výtažek zatížení. |
   | **Trvalost relace** |**Žádné** |
   | **Časový limit nečinnosti (minuty)** |*4* |
   | **Plovoucí IP (přímý návrat serveru)** |**Enabled** (Povoleno) |

   :::image type="content" source="media/sql-server-linux-rhel-ha-listener-tutorial/add-load-balancing-rule.png" alt-text="Přidání pravidla vyrovnávání zatížení":::

4. Klikněte na tlačítko **OK**. 
5. Azure konfiguruje pravidlo vyrovnávání zatížení. Nyní je nástroje pro vyrovnávání zatížení nakonfigurován tak, aby směroval provoz na instanci serveru SQL Server, která hostuje naslouchací proces pro skupinu dostupnosti. 

V tomto okamžiku skupina prostředků má nástroje pro vyrovnávání zatížení, který se připojuje ke všem počítačům SQL Server. Nástroje pro vyrovnávání zatížení také obsahuje IP adresu pro sql server vždy na naslouchací proces skupiny dostupnosti, takže každý počítač může reagovat na požadavky pro skupiny dostupnosti.

## <a name="create-the-load-balancer-resource-in-the-cluster"></a>Vytvoření prostředku správce zatížení v clusteru

1. Přihlaste se k primárnímu virtuálnímu počítači. Potřebujeme vytvořit prostředek povolit azure vyrovnávání zatížení sonda port (59999 se používá v našem příkladu). Spusťte následující příkaz:

    ```bash
    sudo pcs resource create azure_load_balancer azure-lb port=59999
    ```

1. Vytvořte skupinu, `virtualip` `azure_load_balancer` která obsahuje a zdroj:

    ```bash
    sudo pcs resource group add virtualip_group azure_load_balancer virtualip
    ```

### <a name="add-constraints"></a>Přidání omezení

1. Omezení společného umístění musí být nakonfigurováno, aby bylo zajištěno, že IP adresa správce zatížení Azure a prostředek AG běží na stejném uzlu. Spusťte následující příkaz:

    ```bash
    sudo pcs constraint colocation add azure_load_balancer ag_cluster-master INFINITY with-rsc-role=Master
    ```
1. Vytvořte omezení řazení, abyste zajistili, že prostředek AG je v provozu před IP adresou Azure pro vyrovnávání zatížení. Zatímco omezení společného umístění znamená omezení řazení, to to vynucuje.

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

## <a name="create-the-availability-group-listener"></a>Vytvoření naslouchací proces skupiny dostupnosti

1. Na primárním uzlu spusťte následující příkaz v SQLCMD nebo SSMS:

    - Nahraďte níže použitou `virtualip` IP adresu adresou IP.

    ```sql
    ALTER AVAILABILITY
    GROUP [ag1] ADD LISTENER 'ag1-listener' (
            WITH IP(('10.0.0.7'    ,'255.255.255.0'))
                ,PORT = 1433
            );
    GO
    ```

1. Přihlaste se ke každému uzlu virtuálního virtuálního provozu. Pomocí následujícího příkazu otevřete soubor hosts a `ag1-listener` nastavte překlad názvů hostitelů pro každý počítač.

    ```
    sudo vi /etc/hosts
    ```

    V editoru **vi** zadejte `i` vložení textu a na prázdný řádek `ag1-listener`přidejte IP adresu . Pak `ag1-listener` přidejte za mezeru vedle IP.

    ```output
    <IP of ag1-listener> ag1-listener
    ```

    Chcete-li ukončit editor **vi,** nejprve stiskněte klávesu **Esc** a potom zadejte příkaz `:wq` pro zápis souboru a ukončete. Proveďte to na každém uzlu.

## <a name="test-the-listener-and-a-failover"></a>Testování naslouchací proces a převzetí služeb při selhání

### <a name="test-logging-into-sql-server-using-the-availability-group-listener"></a>Testování přihlášení k serveru SQL Server pomocí naslouchací proces skupiny dostupnosti

1. Pomocí sqlcmd se přihlaste k primárnímu uzlu serveru SQL Server pomocí názvu posluchače skupiny dostupnosti:

    - Použijte přihlašovací jméno, které `<YourPassword>` bylo dříve vytvořeno, a nahraďte je správným heslem. Následující příklad používá `sa` přihlášení, které bylo vytvořeno pomocí serveru SQL Server.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. Zkontrolujte název serveru, ke kterému jste připojeni. Spusťte následující příkaz v SQLCMD:

    ```sql
    SELECT @@SERVERNAME
    ```

    Výstup by měl zobrazovat aktuální primární uzel. Mělo by `VM1` to být, pokud jste nikdy netestovali převzetí služeb při selhání.

    Ukončete relaci SQL `exit` zadáním příkazu.

### <a name="test-a-failover"></a>Testování převzetí služeb při selhání

1. Spusťte následující příkaz a ručně přepojujte službu převzetí služeb při selhání primární repliky do jiné repliky nebo jinou `<VM2>` repliku. Nahraďte `<VM2>` hodnotou názvu serveru.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. Pokud zkontrolujete omezení, uvidíte, že z důvodu ručního převzetí služeb při selhání bylo přidáno další omezení:

    ```bash
    sudo pcs constraint list --full
    ```

    Uvidíte, že bylo přidáno omezení s ID. `cli-prefer-ag_cluster-master`

1. Odstraňte omezení s `cli-prefer-ag_cluster-master` ID pomocí následujícího příkazu:

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. Zkontrolujte prostředky clusteru `sudo pcs resource`pomocí příkazu a měli byste `<VM2>`vidět, že primární instance je nyní .

    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
        Masters: [ <VM2> ]
        Slaves: [ <VM1> <VM3> ]
    Resource Group: virtualip_group
        azure_load_balancer        (ocf::heartbeat:azure-lb):      Started <VM2>
        virtualip  (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

1. Pomocí SQLCMD se přihlaste k primární replice pomocí názvu naslouchací procesu:

    - Použijte přihlašovací jméno, které `<YourPassword>` bylo dříve vytvořeno, a nahraďte je správným heslem. Následující příklad používá `sa` přihlášení, které bylo vytvořeno pomocí serveru SQL Server.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. Zkontrolujte server, ke kterému jste připojeni. Spusťte následující příkaz v SQLCMD:

    ```sql
    SELECT @@SERVERNAME
    ```

    Měli byste vidět, že jste teď připojeni k virtuálnímu virtuálnímu virtuálnímu zařízení, který jste převzali.

## <a name="next-steps"></a>Další kroky

Další informace o vyvyčovávačích zatížení v Azure najdete v tématu:

> [!div class="nextstepaction"]
> [Konfigurace nástroje pro vyrovnávání zatížení pro skupinu dostupnosti na virtuálních počítačích Azure SQL Server](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)
