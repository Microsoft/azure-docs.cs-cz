---
title: Konfigurace dhcpv6 pro virtuální počítače s Linuxem
titleSuffix: Azure Load Balancer
description: V tomto článku se dozvíte, jak nakonfigurovat virtuální počítače DHCPv6 pro Linux.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, azure balancer, dual stack, public ip, nativní ipv6, mobilní, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2019
ms.author: allensu
ms.openlocfilehash: 6ea215b6aa826231e940f88c3687bb65591303f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225322"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Konfigurace dhcpv6 pro virtuální počítače s Linuxem


Některé image virtuálního počítače linuxu na Azure Marketplace nemají ve výchozím nastavení nakonfigurovaný protokol Dhcp verze 6 (DHCPv6). Pro podporu IPv6 musí být DHCPv6 nakonfigurován v distribuci operačního systému Linux, kterou používáte. Různé distribuce Linuxu konfigurují DHCPv6 různými způsoby, protože používají různé balíčky.

> [!NOTE]
> Nejnovější image SUSE Linux a CoreOS na Azure Marketplace byly předem nakonfigurované s DHCPv6. Při použití těchto obrázků nejsou nutné žádné další změny.

Tento dokument popisuje, jak povolit DHCPv6 tak, aby váš virtuální počítač linux získá adresu IPv6.

> [!WARNING]
> Nesprávnou úpravou konfiguračních souborů sítě můžete ztratit přístup k síti k virtuálnímu počítači. Doporučujeme otestovat změny konfigurace v neprodukčních systémech. Pokyny v tomto článku byly testovány na nejnovější verze ibi linuxových inama na Azure Marketplace. Podrobnější pokyny najdete v dokumentaci k vlastní verzi Linuxu.

## <a name="ubuntu"></a>Ubuntu

1. Upravte soubor */etc/dhcp/dhclient6.conf* a přidejte následující řádek:

        timeout 10;

2. Upravte konfiguraci sítě pro rozhraní eth0 s následující konfigurací:

   * Na **Ubuntu 12.04 a 14.04**upravte soubor */etc/network/interfaces.d/eth0.cfg.* 
   * Na **Ubuntu 16.04**upravte soubor */etc/network/interfaces.d/50-cloud-init.cfg.*

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Obnovte adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```
Počínaje Ubuntu 17.10 je výchozím mechanismem konfigurace sítě [NETPLAN]( https://netplan.io).  V době instalace/instance netplan čte konfiguraci sítě z konfiguračních souborů YAML v tomto umístění: /{lib,etc,run}/netplan/*.yaml.

Do konfigurace uveďte příkaz *dhcp6:true* pro každé ethernetové rozhraní.  Například:
  
        network:
          version: 2
          ethernets:
            eno1:
              dhcp6: true

Během předčasného spuštění netplan "network renderer" zapisuje konfiguraci do /run předat ovládání zařízení určenému https://netplan.io/referencesíťovému daemonu Pro referenční informace o NETPLAN naleznete v tématu .
 
## <a name="debian"></a>Debian

1. Upravte soubor */etc/dhcp/dhclient6.conf* a přidejte následující řádek:

        timeout 10;

2. Upravte soubor */etc/network/interfaces* a přidejte následující konfiguraci:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Obnovte adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS a Oracle Linux

1. Upravte soubor */etc/sysconfig/network* a přidejte následující parametr:

        NETWORKING_IPV6=yes

2. Upravte soubor */etc/sysconfig/network-scripts/ifcfg-eth0* a přidejte následující dva parametry:

        IPV6INIT=yes
        DHCPV6C=yes

3. Obnovte adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 a openSUSE 13

Nedávné SUSE Linux Enterprise Server (SLES) a openSUSE image v Azure byly předem nakonfigurovány s DHCPv6. Při použití těchto obrázků nejsou nutné žádné další změny. Pokud máte virtuální počítač založený na starší nebo vlastní image SUSE, postupujte takto:

1. V `dhcp-client` případě potřeby nainstalujte balíček:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Upravte soubor */etc/sysconfig/network/ifcfg-eth0* a přidejte následující parametr:

        DHCLIENT6_MODE='managed'

3. Obnovte adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 a openSUSE Leap

Nedávné sles a openSUSE image v Azure byly předem nakonfigurované s DHCPv6. Při použití těchto obrázků nejsou nutné žádné další změny. Pokud máte virtuální počítač založený na starší nebo vlastní image SUSE, postupujte takto:

1. Upravte soubor */etc/sysconfig/network/ifcfg-eth0* a `#BOOTPROTO='dhcp4'` nahraďte parametr následující hodnotou:

        BOOTPROTO='dhcp'

2. Do souboru */etc/sysconfig/network/ifcfg-eth0* přidejte následující parametr:

        DHCLIENT6_MODE='managed'

3. Obnovte adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Nejnovější image CoreOS v Azure byly předem nakonfigurované s DHCPv6. Při použití těchto obrázků nejsou nutné žádné další změny. Pokud máte virtuální počítač založený na starší nebo vlastní image CoreOS, postupujte takto:

1. Upravte soubor */etc/systemd/network/10_dhcp.network:*

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Obnovte adresu IPv6:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
