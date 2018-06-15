---
title: Konfigurace DHCPv6 pro virtuální počítače s Linuxem | Microsoft Docs
description: Postup konfigurace DHCPv6 pro virtuální počítače s Linuxem.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
keywords: protokol IPv6, nástroje pro vyrovnávání zatížení azure, duálním zásobníkem, veřejnou IP adresu, nativní protokol ipv6, mobilní, iot
ms.assetid: b32719b6-00e8-4cd0-ba7f-e60e8146084b
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 6248ed2f55fb5bbcc2061af6ce1dedf2bd31ccad
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
ms.locfileid: "30261843"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Konfigurace DHCPv6 pro virtuální počítače s Linuxem


Některé bitové kopie virtuálního počítače Linux v Azure Marketplace nemají Dynamic Host Configuration Protocol verze 6 (DHCPv6) ve výchozím nastavení nakonfigurované. Chcete-li podporovat protokol IPv6, musí být nakonfigurován DHCPv6 v distribuci operační systém Linux, kterou používáte. Různé Linuxových distribucích DHCPv6 nakonfigurovat různými způsoby, protože používají různé balíčky.

> [!NOTE]
> Poslední SUSE Linux a CoreOS bitové kopie v Azure Marketplace se předem nakonfigurovaným rozhraním DHCPv6. Při použití těchto bitových kopií nejsou vyžadovány žádné další změny.

Tento dokument popisuje, jak povolit DHCPv6 tak, aby virtuální počítač Linux získá adresu IPv6.

> [!WARNING]
> Úpravou nesprávně konfiguračních souborů síť, můžete k virtuálnímu počítači ztratit přístup k síti. Doporučujeme, abyste otestovali změny konfigurace v systémech nevýrobní prostředí. Podle pokynů v tomto článku jsme otestovali na nejnovější verze Linux obrázků v Azure Marketplace. Podrobnější pokyny naleznete v dokumentaci k vaší vlastní verzi systému Linux.

## <a name="ubuntu"></a>Ubuntu

1. Upravit */etc/dhcp/dhclient6.conf* souboru a přidejte následující řádek:

        timeout 10;

2. Upravte konfiguraci sítě pro položku eth0 rozhraní s následující konfigurací:

   * Na **Ubuntu 12.04 a 14.04**, upravit */etc/network/interfaces.d/eth0.cfg* souboru. 
   * Na **Ubuntu 16.04**, upravit */etc/network/interfaces.d/50-cloud-init.cfg* souboru.

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Obnovení na adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. Upravit */etc/dhcp/dhclient6.conf* souboru a přidejte následující řádek:

        timeout 10;

2. Upravit */etc/network/interfaces* souboru a přidejte následující konfigurace:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Obnovení na adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL a CentOS, Oracle Linux

1. Upravit */etc/sysconfig/network* souboru a přidejte následující parametr:

        NETWORKING_IPV6=yes

2. Upravit */etc/sysconfig/network-scripts/ifcfg-eth0* souboru a přidejte následující dva parametry:

        IPV6INIT=yes
        DHCPV6C=yes

3. Obnovení na adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 a openSUSE 13

Poslední SUSE Linux Enterprise Server (SLES) a openSUSE obrázků v Azure se předem nakonfigurovaným rozhraním DHCPv6. Při použití těchto bitových kopií nejsou vyžadovány žádné další změny. Pokud máte virtuální počítač, který je založen na bitovou kopii starší nebo vlastní SUSE, postupujte takto:

1. Nainstalujte `dhcp-client` balíčku, v případě potřeby:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Upravit */etc/sysconfig/network/ifcfg-eth0* souboru a přidejte následující parametr:

        DHCLIENT6_MODE='managed'

3. Obnovení na adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 a openSUSE přestupného

Poslední SLES a openSUSE bitové kopie v Azure se předem nakonfigurovaným rozhraním DHCPv6. Při použití těchto bitových kopií nejsou vyžadovány žádné další změny. Pokud máte virtuální počítač, který je založen na bitovou kopii starší nebo vlastní SUSE, postupujte takto:

1. Upravit */etc/sysconfig/network/ifcfg-eth0* souboru a nahraďte `#BOOTPROTO='dhcp4'` parametru s následující hodnotou:

        BOOTPROTO='dhcp'

2. Na */etc/sysconfig/network/ifcfg-eth0* soubor, přidejte následující parametr:

        DHCLIENT6_MODE='managed'

3. Obnovení na adresu IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Poslední CoreOS bitové kopie v Azure se předem nakonfigurovaným rozhraním DHCPv6. Při použití těchto bitových kopií nejsou vyžadovány žádné další změny. Pokud máte virtuální počítač, na základě bitové kopie starší nebo vlastní CoreOS, postupujte takto:

1. Upravit */etc/systemd/network/10_dhcp.network* souboru:

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Obnovení na adresu IPv6:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
