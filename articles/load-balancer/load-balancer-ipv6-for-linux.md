---
title: Konfigurace protokolu DHCPv6 pro virtuální počítače se systémem Linux
titleSuffix: Azure Load Balancer
description: V tomto článku se dozvíte, jak nakonfigurovat DHCPv6 pro virtuální počítače se systémem Linux.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, Azure Load Balancer, duální zásobník, veřejná IP adresa, nativní IPv6, mobilní zařízení, IoT
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2019
ms.author: allensu
ms.openlocfilehash: d8bd62bab627beb70a8fcba276bf8c2eca309c45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96006742"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Konfigurace protokolu DHCPv6 pro virtuální počítače se systémem Linux


Některé image virtuálních počítačů se systémem Linux v Azure Marketplace nemají ve výchozím nastavení nakonfigurován protokol DHCPv6 (Dynamic Host Configuration Protocol verze 6). Aby bylo možné podporovat protokol IPv6, musí být v rámci distribuce operačního systému Linux, kterou používáte, nakonfigurován protokol DHCPv6. Různá distribuce systému Linux konfiguruje DHCPv6 různými způsoby, protože používají různé balíčky.

> [!NOTE]
> Poslední image SUSE Linux a CoreOS v Azure Marketplace jsou předem nakonfigurované pomocí protokolu DHCPv6. Při použití těchto imagí nejsou vyžadovány žádné další změny.

Tento dokument popisuje, jak povolit protokol DHCPv6, aby virtuální počítač se systémem Linux získal adresu IPv6.

> [!WARNING]
> Při nesprávné úpravě konfiguračních souborů sítě můžete ztratit síťový přístup k vašemu VIRTUÁLNÍmu počítači. Doporučujeme, abyste otestovali změny konfigurace v jiných než produkčních systémech. Pokyny v tomto článku byly testovány na nejnovější verze imagí pro Linux v Azure Marketplace. Podrobnější pokyny najdete v dokumentaci k vaší vlastní verzi systému Linux.

## <a name="ubuntu"></a>Ubuntu

1. Upravte soubor */etc/DHCP/dhclient6.conf* a přidejte následující řádek:

    ```config
    timeout 10;
    ```

2. Upravte konfiguraci sítě pro rozhraní eth0 s následující konfigurací:

   * V **Ubuntu 12,04 a 14,04** upravte soubor */etc/Network/Interfaces.d/eth0.cfg* . 
   * V **Ubuntu 16,04** upravte soubor */etc/Network/Interfaces.d/50-Cloud-init.cfg* .

    ```config
    iface eth0 inet6 auto
        up sleep 5
        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true
    ```

3. Prodloužit adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

Počínaje verzí Ubuntu 17,10 je výchozí mechanismus konfigurace sítě [NETPLAN]( https://netplan.io).  V době instalace/vytvoření instance NETPLAN přečte konfiguraci sítě z konfiguračních souborů YAML v tomto umístění:/{lib, atd. Run}/netplan/*. yaml.

Pro každé rozhraní sítě Ethernet ve vaší konfiguraci uveďte příkaz *dhcp6: true* .  Například:

```config
network:
  version: 2
  ethernets:
    eno1:
      dhcp6: true
```

Během předčasného spuštění netplan "Nástroj pro vyřízení sítě" zapisuje konfiguraci do programu/Run, aby bylo možné předat řízení zařízení zadanému síťovému démonu pro referenční informace o NETPLAN najdete v tématu https://netplan.io/reference .
 
## <a name="debian"></a>Debian

1. Upravte soubor */etc/DHCP/dhclient6.conf* a přidejte následující řádek:

    ```config
    timeout 10;
    ```

2. Upravte soubor */etc/network/interfaces* a přidejte následující konfiguraci:

    ```config
    iface eth0 inet6 auto
        up sleep 5
        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true
    ```

3. Prodloužit adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS a Oracle Linux

1. Upravte soubor */etc/sysconfig/Network* a přidejte následující parametr:

    ```config
    NETWORKING_IPV6=yes
    ```

2. Upravte soubor */etc/sysconfig/Network-Scripts/ifcfg-eth0* a přidejte následující dva parametry:

    ```config
    IPV6INIT=yes
    DHCPV6C=yes
    ```

3. Prodloužit adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 a openSUSE 13

Nedávné SUSE Linux Enterprise Server (SLES) a image openSUSE v Azure jsou předem nakonfigurované s protokolem DHCPv6. Při použití těchto imagí nejsou vyžadovány žádné další změny. Pokud máte virtuální počítač, který je založený na starší nebo vlastní imagi SUSE, udělejte toto:

1. V `dhcp-client` případě potřeby nainstalujte balíček:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Upravte soubor */etc/sysconfig/Network/ifcfg-eth0* a přidejte následující parametr:

    ```config
    DHCLIENT6_MODE='managed'
    

3. Renew the IPv6 address:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 a openSUSE – přestupnost

Poslední image SLES a openSUSE v Azure jsou předem nakonfigurované s protokolem DHCPv6. Při použití těchto imagí nejsou vyžadovány žádné další změny. Pokud máte virtuální počítač, který je založený na starší nebo vlastní imagi SUSE, udělejte toto:

1. Upravte soubor */etc/sysconfig/Network/ifcfg-eth0* a nahraďte `#BOOTPROTO='dhcp4'` parametr následující hodnotou:

    ```config
    BOOTPROTO='dhcp'
    ```

2. Do souboru */etc/sysconfig/Network/ifcfg-eth0* přidejte následující parametr:

    ```config
    DHCLIENT6_MODE='managed'
    ```

3. Prodloužit adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Poslední image CoreOS v Azure jsou předem nakonfigurované s protokolem DHCPv6. Při použití těchto imagí nejsou vyžadovány žádné další změny. Pokud máte virtuální počítač založený na starší nebo vlastní imagi CoreOS, udělejte toto:

1. Upravte soubor */etc/systemd/network/10_dhcp. Network* :

    ```config
    [Match]
    eth0

    [Network]
    DHCP=ipv6
    ```

2. Prodloužit adresu IPv6:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
