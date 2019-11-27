---
title: Konfigurace protokolu DHCPv6 pro virtuální počítače s Linuxem
titleSuffix: Azure Load Balancer
description: V tomto článku se dozvíte, jak nakonfigurovat DHCPv6 pro virtuální počítače se systémem Linux.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: protokol IPv6, nástroje pro vyrovnávání zatížení azure, duálním zásobníkem, veřejné IP adresy, nativní protokol ipv6, mobilní zařízení, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2019
ms.author: allensu
ms.openlocfilehash: 6ea215b6aa826231e940f88c3687bb65591303f2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225322"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Konfigurace protokolu DHCPv6 pro virtuální počítače s Linuxem


Některé z Linuxových imagí virtuálního počítače na webu Azure Marketplace nemají Dynamic Host Configuration Protocol verze 6 (DHCPv6) nakonfigurovaná ve výchozím nastavení. Pro podporu protokolu IPv6, je třeba nastavit DHCPv6 v distribuci operačních systémů Linux, kterou používáte. Různých Linuxových distribucí konfigurovat DHCPv6 v mnoha různými způsoby, protože používají různé balíčky.

> [!NOTE]
> Poslední imagí operačního systému SUSE Linux a CoreOS v Azure Marketplace se předem nakonfigurovaným rozhraním DHCPv6. Když použijete tyto obrázky nejsou potřeba žádné další změny.

Tento dokument popisuje, jak povolit DHCPv6 tak, aby virtuální počítač s Linuxem získá adresu IPv6.

> [!WARNING]
> Úpravou nesprávně konfiguračních souborech sítě, můžete ztratit síťový přístup k vašemu virtuálnímu počítači. Doporučujeme, abyste otestovali změny konfigurace o neprodukční systémy. Pokyny v tomto článku jsme otestovali na nejnovějších verzích imagí Linuxu v Tržišti Azure Marketplace. Podrobné pokyny najdete v dokumentaci pro vlastní verzi systému Linux.

## <a name="ubuntu"></a>Ubuntu

1. Upravte soubor */etc/DHCP/dhclient6.conf* a přidejte následující řádek:

        timeout 10;

2. Upravte konfiguraci sítě pro rozhraní eth0 s následující konfigurací:

   * V **Ubuntu 12,04 a 14,04**upravte soubor */etc/Network/Interfaces.d/eth0.cfg* . 
   * V **Ubuntu 16,04**upravte soubor */etc/Network/Interfaces.d/50-Cloud-init.cfg* .

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Obnovte adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```
Počínaje verzí Ubuntu 17,10 je výchozí mechanismus konfigurace sítě [NETPLAN]( https://netplan.io).  V době instalace/vytvoření instance NETPLAN přečte konfiguraci sítě z konfiguračních souborů YAML v tomto umístění:/{lib, atd. Run}/netplan/*. yaml.

Pro každé rozhraní sítě Ethernet ve vaší konfiguraci uveďte příkaz *dhcp6: true* .  Příklad:
  
        network:
          version: 2
          ethernets:
            eno1:
              dhcp6: true

Během předčasného spuštění netplan "Nástroj pro vyřízení sítě" zapisuje konfiguraci do/run, aby bylo možné předat řízení zařízení zadanému síťovému démonu pro referenční informace o NETPLAN, viz https://netplan.io/reference.
 
## <a name="debian"></a>Debian

1. Upravte soubor */etc/DHCP/dhclient6.conf* a přidejte následující řádek:

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

1. Upravte soubor */etc/sysconfig/Network* a přidejte následující parametr:

        NETWORKING_IPV6=yes

2. Upravte soubor */etc/sysconfig/Network-Scripts/ifcfg-eth0* a přidejte následující dva parametry:

        IPV6INIT=yes
        DHCPV6C=yes

3. Obnovte adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 a openSUSE 13

Poslední SUSE Linux Enterprise Server (SLES) a openSUSE imagí v Azure jsou předem nakonfigurovaným rozhraním DHCPv6. Když použijete tyto obrázky nejsou potřeba žádné další změny. Pokud máte virtuální počítač, který je založen na starší nebo vlastní image operačního systému SUSE, postupujte takto:

1. V případě potřeby nainstalujte balíček `dhcp-client`:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Upravte soubor */etc/sysconfig/Network/ifcfg-eth0* a přidejte následující parametr:

        DHCLIENT6_MODE='managed'

3. Obnovte adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 a openSUSE přestupné

Poslední imagí SLES a openSUSE v Azure byly předem nakonfigurovaným rozhraním DHCPv6. Když použijete tyto obrázky nejsou potřeba žádné další změny. Pokud máte virtuální počítač, který je založen na starší nebo vlastní image operačního systému SUSE, postupujte takto:

1. Upravte soubor */etc/sysconfig/Network/ifcfg-eth0* a nahraďte parametr `#BOOTPROTO='dhcp4'` následující hodnotou:

        BOOTPROTO='dhcp'

2. Do souboru */etc/sysconfig/Network/ifcfg-eth0* přidejte následující parametr:

        DHCLIENT6_MODE='managed'

3. Obnovte adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Poslední imagí CoreOS v Azure byly předem nakonfigurovaným rozhraním DHCPv6. Když použijete tyto obrázky nejsou potřeba žádné další změny. Pokud máte virtuální počítač založený na imagi CoreOS starší nebo vlastní, postupujte takto:

1. Upravte soubor */etc/systemd/network/10_dhcp. Network* :

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Obnovte adresu IPv6:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
