---
title: Vysoce výkonná výpočetní technika – virtuální počítače Azure | Dokumenty společnosti Microsoft
description: Přečtěte si o vysoce výkonném výpočetním prostředí v Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 10549abfbdacf1fc1ae6b99f4cab20a290c32a2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707825"
---
# <a name="optimization-for-linux"></a>Optimalizace pro Linux

Tento článek ukazuje několik klíčových technik pro optimalizaci obrázku operačního systému. Přečtěte si další informace o [povolení infinibandu](enable-infiniband.md) a optimalizaci obrázků operačního spoje.

## <a name="update-lis"></a>Aktualizace LIS

Pokud nasazujete pomocí vlastní image (například starší operační systém, jako je CentOS/RHEL 7.4 nebo 7.5), aktualizujte LIS na virtuálním počítači.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>Paměť pro získání paměti

Zvyšte efektivitu automatickým rekultivací paměti, abyste se vyhnuli vzdálenému přístupu do paměti.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Chcete-li to toto přetrvávat po restartování virtuálního počítače:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>Zakázání brány firewall a SELinuxu

Zakažte firewall a SELinux.

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

## <a name="disable-cpupower"></a>Zakázání výkonu procesoru

Zakažte výkon procesoru.

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [povolení infinibandu](enable-infiniband.md) a optimalizaci obrázků operačního prostředí.

* Přečtěte si další informace o [HPC v](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) Azure.
