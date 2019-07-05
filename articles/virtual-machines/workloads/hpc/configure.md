---
title: Vysokovýkonné výpočetní prostředí – virtuální počítače Azure | Dokumentace Microsoftu
description: Přečtěte si o Vysokovýkonného výpočetního prostředí v Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: e8ff4147130dfeff14be41ed292b51ed34966df0
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537677"
---
# <a name="optimization-for-linux"></a>Optimalizace pro Linux

Tento článek popisuje několik klíčových techniky k optimalizaci vaší image operačního systému. Další informace o [povolení InfiniBand](enable-infiniband.md) a optimalizaci bitové kopie operačního systému.

## <a name="update-lis"></a>Aktualizace služby LIS

Pokud nasazení pomocí vlastní image (například starší operační systém jako je například RHEL se/CentOS 7.4 nebo 7.5), aktualizace služeb LIS na virtuálním počítači.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>Uvolnění paměti.

Zvýšit efektivitu automaticky uvolní paměť, aby se zabránilo přístup do paměti vzdáleného.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Chcete-li to trvají po restartování virtuálního počítače:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>Zakázat bránu firewall a SELinux

Zakážete bránu firewall a SELinux.

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

## <a name="disable-cpupower"></a>Zakázat cpupower

Zakážete cpupower.

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>Další postup

* Další informace o [povolení InfiniBand](enable-infiniband.md) a optimalizaci bitové kopie operačního systému.

* Další informace o [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) v Azure.
