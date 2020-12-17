---
title: Optimalizace propustnosti sítě virtuálních počítačů | Microsoft Docs
description: Optimalizujte propustnost sítě pro Microsoft Azure virtuálních počítačů s Windows a Linux, včetně hlavních distribucí, jako jsou Ubuntu, CentOS a Red Hat.
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/06/2020
ms.author: steveesp
ms.openlocfilehash: a9db2bcc0b44dfb6146517de8a139f34cd8584af
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654451"
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Optimalizace propustnosti sítě pro virtuální počítače Azure

Virtuální počítače Azure mají výchozí nastavení sítě, které je možné dále optimalizovat pro propustnost sítě. Tento článek popisuje, jak optimalizovat propustnost sítě pro Microsoft Azure virtuální počítače s Windows a Linux, včetně hlavních distribucí, jako jsou Ubuntu, CentOS a Red Hat.

## <a name="windows-vm"></a>Virtuální počítač s Windows

Pokud váš virtuální počítač s Windows podporuje [akcelerované síťové služby](create-vm-accelerated-networking-powershell.md), bude mít tato funkce optimální konfiguraci pro propustnost. U všech ostatních virtuálních počítačů s Windows může použití škálování na straně příjmu (RSS) dosáhnout vyšší maximální propustnosti než u virtuálního počítače bez RSS. Na virtuálním počítači s Windows může být standard RSS zakázaný. Pokud chcete zjistit, jestli je povolený RSS, a povolte ho, pokud je v tuto chvíli zakázaný, proveďte následující kroky:

1. Podívejte se, jestli je povolený RSS pro síťový adaptér pomocí `Get-NetAdapterRss` příkazu PowerShellu. V následujícím příkladu výstupu vráceného z rozhraní není `Get-NetAdapterRss` RSS povoleno.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. Pokud chcete povolit RSS, zadejte následující příkaz:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    Předchozí příkaz neobsahuje výstup. Příkaz změnil nastavení síťové karty, což způsobí ztrátu dočasného připojení asi o jednu minutu. Během ztráty připojení se zobrazí dialogové okno pro opětovné připojení. Připojení se obvykle obnoví po třetím pokusu.
3. Zadáním příkazu potvrďte, že je na virtuálním počítači povolený RSS `Get-NetAdapterRss` . V případě úspěchu se vrátí následující příklad výstupu:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                  : True
    ```

## <a name="linux-vm"></a>Virtuální počítači s Linuxem

Ve výchozím nastavení je na virtuálním počítači Azure Linux vždy povolený RSS. Jádra Linux vydané od října 2017 obsahují nové možnosti optimalizace sítě, které umožňují virtuálnímu počítači se systémem Linux dosáhnout vyšší propustnosti sítě.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu pro nová nasazení

Ubuntu Azure kernel je nejlépe optimalizovaná pro výkon sítě v Azure. Chcete-li získat nejnovější optimalizace, nejprve nainstalujte nejnovější podporovanou verzi 18,04-LTS, a to následujícím způsobem:

```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "18.04-LTS",
"Version": "latest"
```

Až se vytváření dokončí, zadejte následující příkazy, které vám pomohou získat nejnovější aktualizace. Tyto kroky také fungují pro virtuální počítače, které aktuálně běží na Ubuntu jádru Azure.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

Následující volitelná sada příkazů může být užitečná pro existující nasazení Ubuntu, která již mají jádro Azure, ale která selhala při dalších aktualizacích s chybami.

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

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Ubuntu upgrade jádra Azure pro existující virtuální počítače

Významný výkon propustnosti je možné dosáhnout upgradem na jádro Azure Linux. Pokud chcete ověřit, jestli máte toto jádro, zkontrolujte verzi jádra. Mělo by to být stejné nebo pozdější než v příkladu.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.13.0-1007-azure
```

Pokud váš virtuální počítač nemá jádro Azure, číslo verze obvykle začíná řetězcem "4,4". Pokud virtuální počítač nemá jádro Azure, spusťte následující příkazy jako kořen:

```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

Aby bylo možné získat nejnovější optimalizace, je nejlepší vytvořit virtuální počítač s nejnovější podporovanou verzí, a to zadáním následujících parametrů:

```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.7",
"Version": "latest"
```

Nové a stávající virtuální počítače můžou využívat výhod instalace nejnovějších služeb Linux Integration Services (LIS). Optimalizace propustnosti je v LIS, počínaje od 4.2.2-2, i když novější verze obsahují další vylepšení. Chcete-li nainstalovat nejnovější verzi LIS, zadejte následující příkazy:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Aby bylo možné získat optimalizace, je nejlepší vytvořit virtuální počítač s nejnovější podporovanou verzí, a to zadáním následujících parametrů:

```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```

Nové a stávající virtuální počítače můžou využívat výhod instalace nejnovějších služeb Linux Integration Services (LIS). Optimalizace propustnosti je v LIS, od 4,2. Chcete-li stáhnout a nainstalovat LIS, zadejte následující příkazy:

```bash
wget https://aka.ms/lis
tar xvf lis
cd LISISO
sudo ./install.sh #or upgrade.sh if prior LIS was previously installed
```

Další informace o integračních službách Linux verze 4,2 pro Hyper-V najdete na [stránce pro stažení](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Další kroky
* Nasazení virtuálních počítačů blízko sebe navzájem pro nízkou latenci se [skupinou umístění blízkosti](../virtual-machines/windows/co-location.md)
* Podívejte se na optimalizovaný výsledek s [testováním šířky pásma a propustnosti virtuálního počítače Azure](virtual-network-bandwidth-testing.md) pro váš scénář.
* Přečtěte si o tom [, jak je šířka pásma přidělena virtuálním počítačům](virtual-machine-network-throughput.md) .
* Další informace o [nejčastějších dotazech k Azure Virtual Network (FAQ)](virtual-networks-faq.md)
