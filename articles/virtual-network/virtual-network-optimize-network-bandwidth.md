---
title: Optimalizovat propustnost sítě virtuálních počítačů | Microsoft Docs
description: Zjistěte, jak optimalizovat propustnost sítě virtuálního počítače Azure.
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2017
ms.author: steveesp
ms.openlocfilehash: b604782f917584d1ecec432c20de75f427176ed1
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2018
ms.locfileid: "29132887"
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Optimalizovat propustnost sítě pro virtuální počítače Azure

Azure virtuální počítače (VM) mají výchozí nastavení sítě, které lze dále optimalizovat pro propustnost sítě. Tento článek popisuje, jak optimalizovat propustnost sítě pro Microsoft Azure Windows a virtuální počítače s Linuxem, včetně hlavních distribuce například Red Hat, Ubuntu a CentOS.

## <a name="windows-vm"></a>Virtuální počítač s Windows

Pokud virtuální počítač Windows podporuje [Accelerated sítě](create-vm-accelerated-networking-powershell.md), povolení této funkce by být optimální konfigurace propustnost. Pro všechny ostatní virtuální počítače Windows škálování na straně příjmu (RSS) pomocí dosáhnout vyšší maximální propustnost než virtuální počítač bez RSS. RSS může být nepovolený ve výchozím nastavení v systému Windows virtuálního počítače. Pokud chcete zjistit, zda RSS zapnutá a ji povolit, pokud je aktuálně zakázáno, proveďte následující kroky:

1. Pokud je technologie RSS povolena pro síťový adaptér s `Get-NetAdapterRss` příkaz prostředí PowerShell. Ve výstupu v následujícím příkladu vrácená z `Get-NetAdapterRss`, RSS není povoleno.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. Pokud chcete povolit RSS, zadejte následující příkaz:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    Předchozí příkaz nemá výstup. Příkaz změnit nastavení síťový adaptér, což by způsobilo ztrátě dočasné připojení přibližně jednu minutu. Při ztrátě připojení se zobrazí dialogové okno Reconnecting. Obvykle se obnoví připojení k po třetí pokus.
3. Potvrďte, že byla povolená technologie RSS ve virtuálním počítači tak, že zadáte `Get-NetAdapterRss` příkaz znovu. V případě úspěchu se vrátí výstupu v následujícím příkladu:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                  : True
    ```

## <a name="linux-vm"></a>Virtuální počítač s Linuxem

RSS je vždy povolena ve výchozím nastavení v systému Linux virtuálního počítače Azure. Linux jádra vydané od října 2017 zahrnují nové možnosti optimalizace sítě, které umožňují virtuálního počítače s Linuxem k dosažení vyšší propustnost sítě.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu pro nová nasazení

Jádra Ubuntu Azure poskytuje nejlepší výkon sítě v Azure a byla výchozí jádra od 21 září 2017. Chcete-li získat tuto jádra, nejprve nainstalujte nejnovější podporovanou verzi 16.04-LTS, následujícím způsobem:

```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```

Po dokončení vytváření zadejte následující příkazy a získat nejnovější aktualizace. Tyto kroky také pro virtuální počítače aktuálně spuštěné jádra Ubuntu Azure fungovat.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

Následující volitelný příkaz sada může být užitečné pro existující Ubuntu nasazení, které již mají Azure jádra, ale které se nepodařilo další aktualizace s chybami.

```bash
#optional steps may be helpful in existing deployments with the Azure kernel
#run as root or preface with sudo
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Ubuntu Azure jádra upgradu pro stávající virtuální počítače

Upgrade na Azure Linux jádra lze dosáhnout výrazné propustnost. Pokud chcete ověřit, zda je nutné tento jádra, zkontrolujte verzi jádra.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.13.0-1007-azure
```

Pokud virtuální počítač nemá Azure jádra, číslo verze obvykle začíná "4.4." Pokud virtuální počítač nemá Azure jádra, spusťte následující příkazy jako kořenového adresáře:

```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

Chcete-li získat nejnovější optimalizace, je nejvhodnější pro vytvoření virtuálního počítače s nejnovější podporovanou verzi zadáním následujících parametrů:

```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.4",
"Version": "latest"
```

Nové a stávající virtuální počítače využívat výhod instalaci nejnovější Linux integrační služby (LIS). Optimalizace propustnosti se LIS, od 4.2.2-2, i když novější verze obsahuje další vylepšení. Zadejte následující příkazy pro instalaci nejnovější LIS:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Chcete-li získat optimalizace, je nejvhodnější pro vytvoření virtuálního počítače s nejnovější podporovanou verzi zadáním následujících parametrů:

```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```

Nové a stávající virtuální počítače využívat výhod instalaci nejnovější Linux integrační služby (LIS). Optimalizace propustnosti se LIS, od 4.2. Zadejte následující příkazy ke stažení a instalaci LIS:

```bash
mkdir lis4.2.3-5
cd lis4.2.3-5
wget https://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-5.tar.gz
tar xvzf lis-rpms-4.2.3-5.tar.gz
cd LISISO
install.sh #or upgrade.sh if prior LIS was previously installed
```

Další informace o Linux integrační služby verzi 4.2 pro Hyper-V zobrazením [stránce pro stažení](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Další postup
* V tématu optimalizované výsledků [šířky pásma nebo propustnost testování virtuální počítač Azure](virtual-network-bandwidth-testing.md) pro váš scénář.
* Přečtěte si informace o tom [šířky pásma je přidělen k virtuálním počítačům] (virtuální machine sítě throughput.md)
* Další informace s [Azure Virtual Network nejčastější dotazy (FAQ)](virtual-networks-faq.md)
