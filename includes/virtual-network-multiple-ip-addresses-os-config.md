---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-network
author: jimdial
ms.service: virtual-network
ms.topic: include
ms.date: 04/09/2018
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: 1febadbbf7821988600d6feddc94fce25d15e989
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
## <a name="os-config"></a>Přidání IP adres do operačního systému virtuálního počítače

Připojte a přihlaste se k virtuálnímu počítači, který jste vytvořili s více privátními IP adresami. Je třeba ručně přidat všechny privátní IP adresy (včetně primární), které jste přidali do virtuálního počítače. Proveďte kroky tohoto následující pro operační systém virtuálního počítače.

### <a name="windows"></a>Windows

1. Na příkazovém řádku zadejte *ipconfig /all*.  Zobrazí se pouze *primární* privátní IP adresa (prostřednictvím protokolu DHCP).
2. Zadáním *ncpa.cpl* na příkazovém řádku otevřete okno **Síťová připojení**.
3. Otevřete vlastnosti pro příslušný adaptér: **Připojení k místní síti**.
4. Dvakrát klikněte na Protokol IPv4 (Internet Protocol verze 4).
5. Vyberte **Použít následující IP adresu** a zadejte tyto hodnoty:

    * **IP adresa:** Zadejte *primární* privátní IP adresu.
    * **Maska podsítě:** Nastavte podle vaší podsítě. Pokud je podsíť například /24, maska podsítě bude 255.255.255.0.
    * **Výchozí brána:** První IP adresa v podsíti. Pokud je vaše podsíť 10.0.0.0/24, IP adresa brány bude 10.0.0.1.
    * Vyberte **použít následující adresy serverů DNS** a zadejte následující hodnoty:
        * **Upřednostňovaný server DNS:** Pokud nepoužíváte vlastní server DNS, zadejte 168.63.129.16.  Pokud používáte vlastní server DNS, zadejte IP adresu pro váš server.
    * Vyberte **Upřesnit** tlačítko a přidejte další IP adresy. Přidejte všechny sekundární soukromé IP adresy, které jste přidali do Azure síťového rozhraní v předchozím kroku, přiřazený přiřazené Azure síťové rozhraní primární IP adresy rozhraní sítě systému Windows.

        Byste měli přiřadit nikdy ručně veřejnou IP adresu přiřazené pro virtuální počítač Azure v rámci operačního systému virtuálního počítače. Když nastavíte ručně IP adresu v operačním systému, zajistěte, aby byl stejnou adresu jako přiřazené Azure privátní IP adresy [síťové rozhraní](../articles/virtual-network/virtual-network-network-interface-addresses.md#change-ip-address-settings), nebo můžete ztratit připojení k virtuálnímu počítači. Další informace o [privátní IP adresu](../articles/virtual-network/virtual-network-network-interface-addresses.md#private) nastavení. Nikdy byste měli přiřadit Azure veřejnou IP adresu v operačním systému.

    * Kliknutím na **OK** zavřete nastavení protokolu TCP/IP a pak znovu kliknutím na **OK** zavřete nastavení adaptéru. Vaše připojení RDP je obnoveno.

6. Na příkazovém řádku zadejte *ipconfig /all*. Zobrazí se všechny IP adresy, které jste přidali, a protokol DHCP je vypnutý.
7. Konfigurace systému Windows na privátní IP adresa primární konfigurace protokolu IP v Azure jako primární IP adresu pro systém Windows. V tématu [přístup k Internetu bez z virtuálního počítače Windows Azure, který má více IP adres](https://support.microsoft.com/help/4040882/no-internet-access-from-azure-windows-vm-that-has-multiple-ip-addresse) podrobnosti. 

### <a name="validation-windows"></a>Ověření (Windows)

Abyste se ujistili, že se můžete připojit k internetu ze sekundární konfigurace IP adresy přes přidruženou veřejnou IP adresu, po jejím přidání pomocí výše uvedených kroků použijte následující příkaz:

```bash
ping -S 10.0.0.5 hotmail.com
```
>[!NOTE]
>Pro sekundární konfigurace IP pouze připojíte k Internetu Pokud konfigurace má veřejnou IP adresu s ním spojená. Pro primární konfigurace protokolu IP není potřeba veřejnou IP adresu na příkaz ping k Internetu.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)

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

### <a name="linux-redhat-centos-and-others"></a>Linux (Red Hat, CentOS a další)

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

### <a name="validation-linux"></a>Ověření (Linux)

Abyste se ujistili, že se můžete připojit k internetu ze sekundární konfigurace IP adresy přes přidruženou veřejnou IP adresu, použijte následující příkaz:

```bash
ping -I 10.0.0.5 hotmail.com
```
>[!NOTE]
>Pro sekundární konfigurace IP pouze připojíte k Internetu Pokud konfigurace má veřejnou IP adresu s ním spojená. Pro primární konfigurace protokolu IP není potřeba veřejnou IP adresu na příkaz ping k Internetu.

Pokud ověřujete odchozí připojení ze sekundárního síťového rozhraní na virtuálním počítači s Linuxem, možná bude nutné přidat odpovídající trasy. To lze provést několika způsoby. Další informace najdete v odpovídající dokumentaci k vaší distribuci Linuxu. Toto je jedna z metod, jak to provést:

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- Nezapomeňte nahradit:
    - **10.0.0.5** za privátní IP adresu, ke které je přidružená veřejná IP adresa,
    - **10.0.0.1** za vaši výchozí bránu,
    - **eth2** za název sekundárního síťového rozhraní.
