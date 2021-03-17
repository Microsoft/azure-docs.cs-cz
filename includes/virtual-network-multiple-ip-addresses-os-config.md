---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-network
author: jimdial
ms.service: virtual-network
ms.topic: include
ms.date: 05/10/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 93caf39216ef0479ec2799267a9ba8181f37f802
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84194198"
---
## <a name="add-ip-addresses-to-a-vm-operating-system"></a><a name="os-config"></a>Přidání IP adres do operačního systému virtuálního počítače

Připojte se a přihlaste se k virtuálnímu počítači, který jste vytvořili s více privátními IP adresami. Je třeba ručně přidat všechny privátní IP adresy (včetně primární), které jste přidali do virtuálního počítače. Proveďte následující kroky pro operační systém virtuálního počítače.

### <a name="windows-server"></a>Windows Server
<details>
  <summary>Rozbalit</summary>

1. Na příkazovém řádku zadejte *ipconfig /all*.  Zobrazí se pouze *primární* privátní IP adresa (prostřednictvím protokolu DHCP).
2. Zadáním *ncpa.cpl* na příkazovém řádku otevřete okno **Síťová připojení**.
3. Otevřete vlastnosti pro příslušný adaptér: **Ethernet**.
4. Dvakrát klikněte na Protokol IPv4 (Internet Protocol verze 4).
5. Vyberte **Použít následující IP adresu** a zadejte tyto hodnoty:

    * **IP adresa:** Zadejte *primární* privátní IP adresu.
    * **Maska podsítě:** Nastavte podle vaší podsítě. Pokud je podsíť například /24, maska podsítě bude 255.255.255.0.
    * **Výchozí brána:** První IP adresa v podsíti. Pokud je vaše podsíť 10.0.0.0/24, IP adresa brány bude 10.0.0.1.
    * Vyberte **použít následující adresy serverů DNS** a zadejte následující hodnoty:
        * **Upřednostňovaný server DNS:** Pokud nepoužíváte vlastní server DNS, zadejte 168.63.129.16.  Pokud používáte vlastní server DNS, zadejte IP adresu pro váš server.  (U alternativního serveru DNS můžete vybrat jakoukoli bezplatnou veřejnou adresu serveru DNS.)
    * Vyberte tlačítko **Upřesnit** a přidejte další IP adresy. Přidejte každou ze sekundárních privátních IP adres, které jste přidali do síťového rozhraní Azure v předchozím kroku, do síťového rozhraní Windows, kterému je přiřazena primární IP adresa přiřazená k síťovému rozhraní Azure.

        Nikdy byste neměli ručně přiřadit veřejnou IP adresu přiřazenou k virtuálnímu počítači Azure v operačním systému virtuálního počítače. Při ručním nastavení IP adresy v operačním systému se ujistěte, že se jedná o stejnou adresu jako soukromá IP adresa přiřazená [síťovému rozhraní](../articles/virtual-network/virtual-network-network-interface-addresses.md#change-ip-address-settings)Azure, nebo můžete ztratit připojení k virtuálnímu počítači. Přečtěte si další informace o nastavení [privátních IP adres](../articles/virtual-network/virtual-network-network-interface-addresses.md#private) . V operačním systému nikdy nepřiřazujte veřejnou IP adresu Azure.

    * Kliknutím na **OK** zavřete nastavení protokolu TCP/IP a pak znovu kliknutím na **OK** zavřete nastavení adaptéru. Vaše připojení RDP je obnoveno.

6. Na příkazovém řádku zadejte *ipconfig /all*. Ověřte, že jsou zobrazené všechny IP adresy, které jste přidali, a služba DHCP je vypnutá.
7. Nakonfigurujte systém Windows tak, aby používal privátní IP adresu primární konfigurace IP adresy v Azure jako primární IP adresu pro Windows. Další informace najdete v tématu [žádný přístup k Internetu z virtuálního počítače Azure s Windows, který má víc IP adres](https://support.microsoft.com/help/4040882/no-internet-access-from-azure-windows-vm-that-has-multiple-ip-addresse) . 

### <a name="validation-windows-server"></a>Ověření (Windows Server)

Abyste se ujistili, že se můžete připojit k Internetu ze své sekundární konfigurace IP přes jí přidruženou veřejnou IP adresu, po jeho přidání pomocí výše uvedených kroků použijte následující příkaz (nahrazující 10.0.0.7 se sekundární privátní IP adresou):

```bash
ping -S 10.0.0.7 outlook.com
```
>[!NOTE]
>V případě sekundárních konfigurací IP můžete k ověřování pomocí příkazů pouze testovat připojení k Internetu, pokud má konfigurace přidruženou veřejnou IP adresu. U primárních konfigurací IP adres není k ověřování na internetu nutná veřejná IP adresa.
</details>

### <a name="linux-ubuntu-1416"></a>Linux (Ubuntu 14/16)
<details>
  <summary>Rozbalit</summary>
Doporučujeme si prohlédnout si nejnovější dokumentaci pro distribuci systému Linux. 

1. Otevřete okno terminálu.
2. Ujistěte se, že jste uživatel root. Pokud ne, zadejte následující příkaz:

   ```bash
   sudo -i
   ```

3. Aktualizujte konfigurační soubor síťového rozhraní (předpokládejme, že je to „eth0“).

   * Ponechejte stávající položku řádku pro protokol DHCP. Primární IP adresa bude nakonfigurována stejně jako dříve.
   * Přidejte konfiguraci pro další statickou IP adresu pomocí následujících příkazů:

     ```bash
     cd /etc/network/interfaces.d/
     ls
     ```

     Měl by se zobrazit soubor .cfg.
4. Otevřete tento soubor. Na konci souboru by se měly zobrazit tyto řádky:

   ```bash
   auto eth0
   iface eth0 inet dhcp
   ```

5. Následující řádky přidejte za všechny řádky v tomto souboru:

   ```bash
   iface eth0 inet static
   address <your private IP address here>
   netmask <your subnet mask>
   ```

6. Uložte soubor pomocí tohoto příkazu:

   ```bash
   :wq
   ```

7. Resetujte síťové rozhraní pomocí tohoto příkazu:

   ```bash
   sudo ifdown eth0 && sudo ifup eth0
   ```

   > [!IMPORTANT]
   > Pokud používáte vzdálené připojení, spusťte ifdown a ifup na stejném řádku.
   >

8. Ověřte přidání IP adresy k síťovému rozhraní pomocí následujícího příkazu:

   ```bash
   ip addr list eth0
   ```

   V seznamu by se měla zobrazit IP adresa, kterou jste přidali.

### <a name="validation-ubuntu-1416"></a>Ověření (Ubuntu 14/16)

Abyste se ujistili, že se můžete připojit k internetu ze sekundární konfigurace IP adresy přes přidruženou veřejnou IP adresu, použijte následující příkaz:

```bash
ping -I 10.0.0.5 outlook.com
```
>[!NOTE]
>V případě sekundárních konfigurací IP můžete k ověřování pomocí příkazů pouze testovat připojení k Internetu, pokud má konfigurace přidruženou veřejnou IP adresu. U primárních konfigurací IP adres není k ověřování na internetu nutná veřejná IP adresa.

Pokud ověřujete odchozí připojení ze sekundárního síťového rozhraní na virtuálním počítači s Linuxem, možná bude nutné přidat odpovídající trasy. To lze provést několika způsoby. Další informace najdete v odpovídající dokumentaci k vaší distribuci Linuxu. Toto je jedna z metod, jak to provést:

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- Nezapomeňte nahradit:
    - **10.0.0.5** za privátní IP adresu, ke které je přidružená veřejná IP adresa,
    - **10.0.0.1** za vaši výchozí bránu,
    - **ETH2** se na název sekundární síťové karty. </details>

### <a name="linux-ubuntu-1804"></a>Linux (Ubuntu 18.04 +)
<details>
  <summary>Rozbalit</summary>
Ubuntu 18,04 a novější se změnily na `netplan` pro správu sítě OS. Doporučujeme si prohlédnout si nejnovější dokumentaci pro distribuci systému Linux. 

1. Otevřete okno terminálu.
2. Ujistěte se, že jste uživatel root. Pokud ne, zadejte následující příkaz:

    ```bash
    sudo -i
    ```

3. Vytvořte soubor pro druhé rozhraní a otevřete ho v textovém editoru:

    ```bash
    vi /etc/netplan/60-static.yaml
    ```

4. Do souboru přidejte následující řádky, které nahradíte `10.0.0.6/24` IP/maskou:

    ```bash
    network:
        version: 2
        ethernets:
            eth0:
                addresses:
                    - 10.0.0.6/24
    ```

5. Uložte soubor pomocí tohoto příkazu:

    ```bash
    :wq
    ```

6. Otestujte změny pomocí [netplan, zkuste](http://manpages.ubuntu.com/manpages/cosmic/man8/netplan-try.8.html) ověřit syntaxi:

    ```bash
    netplan try
    ```

> [!NOTE]
> `netplan try` změny se projeví dočasně a změny se vrátí po 120 sekundách. Pokud dojde ke ztrátě připojení, počkejte prosím 120 sekund a pak se znovu připojte. V tuto chvíli se změny vrátí zpět.

7. Pokud se nepředpokládá žádné problémy s `netplan try` , použijte změny konfigurace:

    ```bash
    netplan apply
    ```

8. Ověřte přidání IP adresy k síťovému rozhraní pomocí následujícího příkazu:

    ```bash
    ip addr list eth0
    ```

    V seznamu by se měla zobrazit IP adresa, kterou jste přidali. Příklad:

    ```bash
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
        valid_lft forever preferred_lft forever
    2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
        link/ether 00:0d:3a:8c:14:a5 brd ff:ff:ff:ff:ff:ff
        inet 10.0.0.6/24 brd 10.0.0.255 scope global eth0
        valid_lft forever preferred_lft forever
        inet 10.0.0.4/24 brd 10.0.0.255 scope global secondary eth0
        valid_lft forever preferred_lft forever
        inet6 fe80::20d:3aff:fe8c:14a5/64 scope link
        valid_lft forever preferred_lft forever
    ```
### <a name="validation-ubuntu-1804"></a>Ověření (Ubuntu 18.04 +)

Abyste se ujistili, že se můžete připojit k internetu ze sekundární konfigurace IP adresy přes přidruženou veřejnou IP adresu, použijte následující příkaz:

```bash
ping -I 10.0.0.5 outlook.com
```
>[!NOTE]
>V případě sekundárních konfigurací IP můžete k ověřování pomocí příkazů pouze testovat připojení k Internetu, pokud má konfigurace přidruženou veřejnou IP adresu. U primárních konfigurací IP adres není k ověřování na internetu nutná veřejná IP adresa.

Pokud ověřujete odchozí připojení ze sekundárního síťového rozhraní na virtuálním počítači s Linuxem, možná bude nutné přidat odpovídající trasy. To lze provést několika způsoby. Další informace najdete v odpovídající dokumentaci k vaší distribuci Linuxu. Toto je jedna z metod, jak to provést:

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- Nezapomeňte nahradit:
    - **10.0.0.5** za privátní IP adresu, ke které je přidružená veřejná IP adresa,
    - **10.0.0.1** za vaši výchozí bránu,
    - **ETH2** se na název sekundární síťové karty. </details>

### <a name="linux-red-hat-centos-and-others"></a>Linux (Red Hat, CentOS a další)
<details>
  <summary>Rozbalit</summary>

1. Otevřete okno terminálu.
2. Ujistěte se, že jste uživatel root. Pokud ne, zadejte následující příkaz:

    ```bash
    sudo -i
    ```

3. Zadejte své heslo a postupujte podle zobrazených pokynů. Jakmile budete uživatel root, přejděte do složky se síťovými skripty pomocí následujícího příkazu:

    ```bash
    cd /etc/sysconfig/network-scripts
    ```

4. Zobrazte seznam souvisejících souborů ifcfg pomocí následujícího příkazu:

    ```bash
    ls ifcfg-*
    ```

    Mezi soubory by se měl zobrazit soubor *ifcfg-eth0*.

5. Pokud chcete přidat IP adresu, vytvořte pro ni konfigurační soubor, jak je znázorněno níže. Pro každou konfiguraci IP adresy se musí vytvořit jeden soubor.

    ```bash
    touch ifcfg-eth0:0
    ```

6. Otevřete soubor *ifcfg-eth0:0* pomocí tohoto příkazu:

    ```bash
    vi ifcfg-eth0:0
    ```

7. Přidejte obsah do souboru, v tomto případě *eth0:0*, pomocí následujícího příkazu. Nezapomeňte aktualizovat informace podle vaší IP adresy.

    ```bash
    DEVICE=eth0:0
    BOOTPROTO=static
    ONBOOT=yes
    IPADDR=192.168.101.101
    NETMASK=255.255.255.0
    ```

8. Uložte soubor pomocí tohoto příkazu:

    ```bash
    :wq
    ```

9. Restartujte síťové služby a ověřte úspěšné provedení změn spuštěním následujících příkazů:

    ```bash
    /etc/init.d/network restart
    ifconfig
    ```

    V navráceném seznamu by se měla zobrazit IP adresa, kterou jste přidali – *eth0:0*.

### <a name="validation-red-hat-centos-and-others"></a>Ověření (Red Hat, CentOS a další)

Abyste se ujistili, že se můžete připojit k internetu ze sekundární konfigurace IP adresy přes přidruženou veřejnou IP adresu, použijte následující příkaz:

```bash
ping -I 10.0.0.5 outlook.com
```
>[!NOTE]
>V případě sekundárních konfigurací IP můžete k ověřování pomocí příkazů pouze testovat připojení k Internetu, pokud má konfigurace přidruženou veřejnou IP adresu. U primárních konfigurací IP adres není k ověřování na internetu nutná veřejná IP adresa.

Pokud ověřujete odchozí připojení ze sekundárního síťového rozhraní na virtuálním počítači s Linuxem, možná bude nutné přidat odpovídající trasy. To lze provést několika způsoby. Další informace najdete v odpovídající dokumentaci k vaší distribuci Linuxu. Toto je jedna z metod, jak to provést:

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- Nezapomeňte nahradit:
    - **10.0.0.5** za privátní IP adresu, ke které je přidružená veřejná IP adresa,
    - **10.0.0.1** za vaši výchozí bránu,
    - **ETH2** se na název sekundární síťové karty. </details>