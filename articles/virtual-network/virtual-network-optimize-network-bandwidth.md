---
title: Optimalizace propustnosti sítě virtuálních počítačů | Dokumentace Microsoftu
description: Zjistěte, jak optimalizovat propustnost sítě virtuálních počítačů Azure.
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
ms.openlocfilehash: 50d7ca73e5e18f88f5d789e12fc7f26908e8b8f0
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50025544"
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Optimalizace propustnosti sítě pro virtuální počítače Azure

Virtuální počítače Azure (VM) mají výchozí nastavení sítě, které lze dále optimalizovat propustnost sítě. Tento článek popisuje, jak optimalizovat propustnost sítě pro Microsoft Windows Azure a virtuální počítače s Linuxem, včetně hlavních distribuce, jako jsou Red Hat, Ubuntu a CentOS.

## <a name="windows-vm"></a>Virtuální počítač s Windows

Pokud je váš virtuální počítač Windows podporuje [Akcelerovanými síťovými službami](create-vm-accelerated-networking-powershell.md), povolením této funkce by být optimální propustnosti. U všech ostatních Windows virtuálních počítačů může přijímat na straně příjmu (RSS) pomocí dosáhnout vyšší maximální propustnost než virtuální počítač bez RSS. RSS může být v virtuálního počítače s Windows ve výchozím nastavení zakázaná. Pokud chcete zjistit, jestli je povolené RSS a ho povolit, pokud je aktuálně zakázaná, proveďte následující kroky:

1. Zobrazit, pokud je technologie RSS zapnutá pro síťový adaptér s podporou `Get-NetAdapterRss` příkaz prostředí PowerShell. Následující ukázkový výstup vrácený `Get-NetAdapterRss`, RSS není povolená.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. Pokud chcete povolit RSS, zadejte následující příkaz:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    Předchozí příkaz nemá výstup. Příkaz změnit nastavení síťového adaptéru, způsobí ztrátu dočasné připojení pro přibližně jednu minutu. Při ztrátě připojení se zobrazí dialogové okno znovu připojíte. Připojení je obvykle obnoví po třetí pokus.
3. Potvrďte, že byla povolená technologie RSS ve virtuálním počítači tak, že zadáte `Get-NetAdapterRss` příkaz znovu. V případě úspěchu se vrátí následující příklad výstupu:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                  : True
    ```

## <a name="linux-vm"></a>Virtuální počítači s Linuxem

RSS je vždy povolena ve výchozím nastavení ve virtuálním počítači Azure s Linuxem. Linuxová jádra všeobecně dostupné od října 2017 zahrnout nové možnosti optimalizace sítě, které umožňují virtuálního počítače s Linuxem, abyste dosáhli vyšší propustnosti sítě.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu pro nová nasazení

Jádra Ubuntu Azure poskytuje nejlepší výkon sítě v Azure a byl výchozí jádra od 21. září 2017. Chcete-li získat tento jádra, nejprve nainstalujte nejnovější podporovanou verzi 16.04 LTS, následujícím způsobem:

```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```

Po vytvoření se zobrazí, zadejte následující příkazy, chcete-li získat nejnovější aktualizace. Tento postup funguje i pro virtuální počítače aktuálně spuštěné jádru Azure s Ubuntu.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

Následující volitelný příkaz sady mohou být užitečné pro existující nasazení Ubuntu, které už máte Azure jádra, ale, která se nepodařilo další aktualizace s chybami.

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

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Upgrade Azure s Ubuntu jádra pro stávající virtuální počítače

Díky upgradu na jádru Azure s Linuxem můžete dosáhnout výrazné propustnost. Pokud chcete ověřit, zda je nutné tento jádra, zkontrolujte svoji verzi jádra.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.13.0-1007-azure
```

Pokud váš virtuální počítač nemá žádné Azure jádra, číslo verze obvykle začíná řetězcem "4.4." Pokud virtuální počítač nemá žádné Azure jádra, jako kořenové spusťte následující příkazy:

```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

Pokud chcete získat nejnovější optimalizace, je nejlepší vytvořit virtuální počítač s nejnovější podporovanou verzi tak, že zadáte následující parametry:

```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.4",
"Version": "latest"
```

Nové i stávající virtuální počítače můžete těžit z instalace nejnovější Linux Integration Services (LIS). Optimalizace propustnosti se LIS od 4.2.2-2, i když novější verze obsahuje další vylepšení. Zadejte následující příkazy, abyste nainstalovali nejnovější LIS:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Abyste získali optimalizace, je nejlepší vytvořit virtuální počítač s nejnovější podporovanou verzi tak, že zadáte následující parametry:

```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```

Nové i stávající virtuální počítače můžete těžit z instalace nejnovější Linux Integration Services (LIS). Optimalizace propustnosti se LIS od 4.2. Zadejte následující příkazy ke stažení a instalaci služeb LIS:

```bash
mkdir lis4.2.3-5
cd lis4.2.3-5
wget https://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-5.tar.gz
tar xvzf lis-rpms-4.2.3-5.tar.gz
cd LISISO
install.sh #or upgrade.sh if prior LIS was previously installed
```

Další informace o systému Linux Integration Services verze 4.2 pro technologii Hyper-V zobrazení [stránce pro stažení](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Další postup
* Zobrazení optimalizovaných výsledku s [propustnost šířky pásma nebo testování virtuálních počítačů Azure](virtual-network-bandwidth-testing.md) pro váš scénář.
* Přečtěte si, jak [šířky pásma je přidělen k virtuálním počítačům](virtual-machine-network-throughput.md)
* Další informace najdete [Azure Virtual Network – nejčastější dotazy (FAQ)](virtual-networks-faq.md)
