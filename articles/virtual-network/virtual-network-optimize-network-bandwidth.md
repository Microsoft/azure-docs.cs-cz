---
title: Optimalizace propustnost sítě virtuálních montovna | Dokumenty společnosti Microsoft
description: Zjistěte, jak optimalizovat propustnost sítě virtuálních strojů Azure.
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
ms.openlocfilehash: be5f38bdeaf51dbe23006ecf30b4deb66aa7402a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75690884"
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Optimalizace propustnost sítě pro virtuální počítače Azure

Virtuální počítače Azure (VM) mají výchozí nastavení sítě, které lze dále optimalizovat pro propustnost sítě. Tento článek popisuje, jak optimalizovat propustnost sítě pro virtuální počítače Microsoft Azure Windows a Linux, včetně hlavních distribucí, jako je Ubuntu, CentOS a Red Hat.

## <a name="windows-vm"></a>Virtuální počítač s Windows

Pokud váš virtuální počítač se systémem Windows podporuje [akcelerované sítě](create-vm-accelerated-networking-powershell.md), povolení této funkce by bylo optimální konfigurací pro pustočnost. Pro všechny ostatní virtuální servery Windows pomocí škálování na straně příjmu (RSS) může dosáhnout vyšší maximální propustnost než virtuální hod bez RSS. Služba RSS může být ve výchozím nastavení ve virtuálním provozu systému Windows zakázána. Chcete-li zjistit, zda je technologie RSS povolena, a povolit ji, pokud je aktuálně zakázána, proveďte následující kroky:

1. Podívejte se, jestli je pro síťový `Get-NetAdapterRss` adaptér povolen protokol RSS pomocí příkazu PowerShell. V následujícím příkladu výstup `Get-NetAdapterRss`vrácený z , RSS není povolena.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. Chcete-li povolit funkci RSS, zadejte následující příkaz:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    Předchozí příkaz nemá výstup. Příkaz změnil nastavení nic, což způsobilo dočasnou ztrátu připojení po dobu přibližně jedné minuty. Během ztráty připojení se zobrazí dialogové okno Opětovné připojení. Připojení se obvykle obnoví po třetím pokusu.
3. Zkontrolujte, že je ve virtuálním `Get-NetAdapterRss` virtuálním ms povoleno připojení RSS, a to znovu zadáním příkazu. Pokud je úspěšná, je vrácen následující příklad výstupu:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                  : True
    ```

## <a name="linux-vm"></a>Virtuální počítači s Linuxem

SLUŽBA RSS je ve výchozím nastavení vždy povolená v virtuálním počítači Azure Linux. Linuxová jádra vydaná od října 2017 obsahují nové možnosti optimalizace sítě, které umožňují virtuálnímu počítači SIP dosáhnout vyšší propustnosti sítě.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu pro nové nasazení

Jádro Ubuntu Azure poskytuje nejlepší síťový výkon v Azure a je výchozím jádrem od září 21, 2017. Chcete-li získat toto jádro, nejprve nainstalujte nejnovější podporovanou verzi 16.04-LTS, a to následovně:

```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```

Po dokončení vytváření zadejte následující příkazy, abyste získali nejnovější aktualizace. Tyto kroky fungují také pro virtuální počítače, které aktuálně běží jádro Ubuntu Azure.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

Následující volitelná sada příkazů může být užitečná pro stávající nasazení Ubuntu, která již mají jádro Azure, ale které se nepodařilo další aktualizace s chybami.

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

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Upgrade jádra Ubuntu Azure pro stávající virtuální počítače

Významné propustnost výkonu lze dosáhnout upgradem na jádro Azure Linux. Chcete-li ověřit, zda máte toto jádro, zkontrolujte verzi jádra.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.13.0-1007-azure
```

Pokud váš virtuální počítač nemá jádro Azure, číslo verze obvykle začíná na "4.4.". Pokud virtuální počítač nemá jádro Azure, spusťte následující příkazy jako root:

```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

Chcete-li získat nejnovější optimalizace, je nejlepší vytvořit virtuální virtuální modul s nejnovější podporovanou verzí zadáním následujících parametrů:

```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.4",
"Version": "latest"
```

Nové a stávající virtuální počítače můžou těžit z instalace nejnovějších linuxových integračních služeb (LIS). Optimalizace propustnost je v LIS, počínaje 4.2.2-2, i když novější verze obsahují další vylepšení. Chcete-li nainstalovat nejnovější lis, zadejte následující příkazy:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Chcete-li získat optimalizace, je nejlepší vytvořit virtuální virtuální modul s nejnovější podporovanou verzí zadáním následujících parametrů:

```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```

Nové a stávající virtuální počítače můžou těžit z instalace nejnovějších linuxových integračních služeb (LIS). Optimalizace propustnost je v LIS, počínaje 4.2. Zadejte následující příkazy ke stažení a instalaci LIS:

```bash
wget https://aka.ms/lis
tar xvf lis
cd LISISO
sudo ./install.sh #or upgrade.sh if prior LIS was previously installed
```

Další informace o službě Linux Integration Services verze 4.2 pro Hyper-V najdete na [stránce pro stažení](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Další kroky
* Podívejte se na optimalizovaný výsledek s [testováním šířky pásma/propustností virtuálního počítače Azure](virtual-network-bandwidth-testing.md) pro váš scénář.
* Přečtěte si, jak [se virtuálním počítačům přiděluje šířka pásma](virtual-machine-network-throughput.md)
* Další informace o [nejčastějších dotazech na virtuální síť Azure (nejčastější dotazy)](virtual-networks-faq.md)
