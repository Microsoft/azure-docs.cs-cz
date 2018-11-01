---
title: Konfigurace protokolu DHCPv6 pro virtuální počítače s Linuxem | Dokumentace Microsoftu
description: Postup konfigurace protokolu DHCPv6 pro virtuální počítače s Linuxem.
services: load-balancer
documentationcenter: na
author: KumudD
keywords: protokol IPv6, nástroje pro vyrovnávání zatížení azure, duálním zásobníkem, veřejné IP adresy, nativní protokol ipv6, mobilní zařízení, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 7ef376c044bceb14614388a72c11942869dbde07
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741619"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Konfigurace protokolu DHCPv6 pro virtuální počítače s Linuxem


Některé z Linuxových imagí virtuálního počítače na webu Azure Marketplace nemají Dynamic Host Configuration Protocol verze 6 (DHCPv6) nakonfigurovaná ve výchozím nastavení. Pro podporu protokolu IPv6, je třeba nastavit DHCPv6 v distribuci operačních systémů Linux, kterou používáte. Různých Linuxových distribucí konfigurovat DHCPv6 v mnoha různými způsoby, protože používají různé balíčky.

> [!NOTE]
> Poslední imagí operačního systému SUSE Linux a CoreOS v Azure Marketplace se předem nakonfigurovaným rozhraním DHCPv6. Když použijete tyto obrázky nejsou potřeba žádné další změny.

Tento dokument popisuje, jak povolit DHCPv6 tak, aby virtuální počítač s Linuxem získá adresu IPv6.

> [!WARNING]
> Úpravou nesprávně konfiguračních souborech sítě, můžete ztratit síťový přístup k vašemu virtuálnímu počítači. Doporučujeme, abyste otestovali změny konfigurace o neprodukční systémy. Pokyny v tomto článku jsme otestovali na nejnovějších verzích imagí Linuxu v Tržišti Azure Marketplace. Podrobné pokyny najdete v dokumentaci pro vlastní verzi systému Linux.

## <a name="ubuntu"></a>Ubuntu

1. Upravit */etc/dhcp/dhclient6.conf* soubor a přidejte následující řádek:

        timeout 10;

2. Upravte konfiguraci sítě pro rozhraní eth0 s následující konfigurací:

   * Na **Ubuntu 12.04 a 14.04**, upravit */etc/network/interfaces.d/eth0.cfg* souboru. 
   * Na **Ubuntu 16.04**, upravit */etc/network/interfaces.d/50-cloud-init.cfg* souboru.

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Obnovte adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. Upravit */etc/dhcp/dhclient6.conf* soubor a přidejte následující řádek:

        timeout 10;

2. Upravit */etc/network/interfaces* soubor a přidejte následující konfiguraci:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Obnovte adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS a Oracle Linux

1. Upravit */etc/sysconfig/network* a přidejte následující parametr:

        NETWORKING_IPV6=yes

2. Upravit */etc/sysconfig/network-scripts/ifcfg-eth0* a přidejte následující dva parametry:

        IPV6INIT=yes
        DHCPV6C=yes

3. Obnovte adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 a openSUSE 13

Poslední SUSE Linux Enterprise Server (SLES) a openSUSE imagí v Azure jsou předem nakonfigurovaným rozhraním DHCPv6. Když použijete tyto obrázky nejsou potřeba žádné další změny. Pokud máte virtuální počítač, který je založen na starší nebo vlastní image operačního systému SUSE, postupujte takto:

1. Nainstalujte `dhcp-client` balíček, v případě potřeby:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Upravit */etc/sysconfig/network/ifcfg-eth0* a přidejte následující parametr:

        DHCLIENT6_MODE='managed'

3. Obnovte adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 a openSUSE přestupné

Poslední imagí SLES a openSUSE v Azure byly předem nakonfigurovaným rozhraním DHCPv6. Když použijete tyto obrázky nejsou potřeba žádné další změny. Pokud máte virtuální počítač, který je založen na starší nebo vlastní image operačního systému SUSE, postupujte takto:

1. Upravit */etc/sysconfig/network/ifcfg-eth0* soubor a nahradit `#BOOTPROTO='dhcp4'` parametru s následující hodnotou:

        BOOTPROTO='dhcp'

2. Chcete */etc/sysconfig/network/ifcfg-eth0* přidejte následující parametr:

        DHCLIENT6_MODE='managed'

3. Obnovte adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Poslední imagí CoreOS v Azure byly předem nakonfigurovaným rozhraním DHCPv6. Když použijete tyto obrázky nejsou potřeba žádné další změny. Pokud máte virtuální počítač založený na imagi CoreOS starší nebo vlastní, postupujte takto:

1. Upravit */etc/systemd/network/10_dhcp.network* souboru:

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Obnovte adresu IPv6:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
