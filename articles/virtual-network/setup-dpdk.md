---
title: DPDK ve virtuálním počítači Azure s Linuxem | Dokumentace Microsoftu
description: Další informace o nastavení DPDK ve virtuálním počítači s Linuxem.
services: virtual-network
documentationcenter: na
author: laxmanrb
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2018
ms.author: labattul
ms.openlocfilehash: a03b72200f97c54bce188ec6a6ad8a06a43f26ae
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "40002575"
---
# <a name="setup-dpdk-in-a-linux-virtual-machine"></a>Instalační program DPDK ve virtuálním počítači s Linuxem

Data roviny Development Kit (DPDK) v Azure nabízí rychlejší uživatele místo paketů zpracování architekturu pro aplikace náročné na výkon, které obcházejí virtuální počítač jádra síťových protokolů.

Zpracování pomocí síťových protokolů jádra typické paketů je řízená přerušení. Pokaždé, když přijme příchozí pakety síťové rozhraní je přerušení jádra pro zpracování paketů a kontext přepínače z jádra prostoru do prostoru uživatele. DPDK eliminuje přepínání kontextu a přerušení řízené metoda ve prospěch na uživatele místo implementace pomocí ovladače režimu dotazování pro zpracování rychlé paketů.

DPDK se skládá sada knihoven místa na uživatele poskytuje přístup k nižší úrovni prostředky, například hardware, logických jader, správa paměti a dotazovat režimu ovladače síťové karty.

DPDK můžete spustit v Azure virtual machines, podpora více distribuce operačního systému. DPDK implementace virtualizace v podobě síťových virtuálních zařízení (NVA) poskytuje výkonu rozdíl v řízení síťové funkce jako je například virtuální směrovače, brány firewall, sítě VPN, nástroje pro vyrovnávání zatížení, core evolved paketů a (denial-of-service Aplikace před útoky DDoS).

## <a name="benefit"></a>Výhoda

**Vyšší pakety za sekundu (PPS)**: Vynechání jádra a ovládací prvek a současně paketů v prostoru uživatele snižuje počet cyklů odstraněním přepnutí kontextu a zvyšuje počet paketů zpracovaných za sekundu na virtuálních počítačích Azure s Linuxem.


## <a name="supported-operating-systems"></a>Podporované operační systémy

Podporují se následující distribucí z Galerie Azure:

| Operační systém Linux     | Verze jádra        |
|--------------|----------------       |
| Ubuntu 16.04 | 4.15.0-1015-Azure     |
| Ubuntu 18.04 | 4.15.0-1015-Azure     |
| SLES 15      | 4.12.14-5.5-Azure     |
| RHEL 7.5     | 3.10.0-862.9.1.el7    |
| CentOS 7.5   | 3.10.0-862.3.3.el7    |

**Podpora vlastních jádra**

Odkazovat na [opravy pro vytváření optimalizaci Azure linuxového jádra](https://github.com/microsoft/azure-linux-kernel) pro všechny verze jádra Linux nejsou uvedené, nebo o další informace, obraťte se na [ azuredpdk@microsoft.com ](mailto:azuredpdk@microsoft.com). 

## <a name="region-support"></a>Oblasti podpory

Všechny oblasti Azure podporují DPDK.

## <a name="prerequisites"></a>Požadavky

Akcelerované síťové služby musí být povolené na virtuálním počítači s Linuxem. Virtuální počítač by měl mít alespoň dvě síťová rozhraní, pomocí jednoho rozhraní pro správu. Zjistěte, jak [vytvořit virtuální počítač s Linuxem s povolenými akcelerovanými síťovými službami](create-vm-accelerated-networking-cli.md).

## <a name="install-dpdk-dependencies"></a>Nainstalujte DPDK závislosti

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev
```

### <a name="rhel75centos-75"></a>RHEL7.5/CentOS 7.5

```bash
yum -y groupinstall "Infiniband Support"
sudo dracut --add-drivers "mlx4_en mlx4_ib mlx5_ib" -f
yum install -y gcc kernel-devel-`uname -r` numactl-devel.x86_64 librdmacm-devel
```

### <a name="sles-15"></a>SLES 15

**Azure jádra**

```bash
zypper  \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-azure kernel-devel-azure gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

**Výchozí jádra**

```bash
zypper \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-default-devel gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

## <a name="setup-virtual-machine-environment-once"></a>Nastavení prostředí virtuálních počítačů (po)

1. [Stáhněte si nejnovější DPDK](https://core.dpdk.org/download). Verze 18.02 nebo vyšší je vyžadována pro Azure.
2. Nejdřív sestavit výchozí konfigurace s `make config T=x86_64-native-linuxapp-gcc`.
3. Povolit Mellanox PMDs generované konfiguraci s `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config`.
4. Kompilovat s `make`.
5. Instalace pomocí `make install DESTDIR=<output folder>`.

# <a name="configure-runtime-environment"></a>Konfigurace prostředí modulu runtime

Spuštěním následujících příkazů jednou, po restartování:

1. Hugepages

   * Pomocí následujícího příkazu, jednou pro všechny numanodes nakonfigurujte hugepage:

     ```bash
     echo 1024 | sudo tee
     /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   *  Vytvořte adresář pro připojení s `mkdir /mnt/huge`.
   *  Připojení hugepages s `mount -t hugetlbfs nodev /mnt/huge`.
   *  Kontrola hugepages jsou vyhrazena pro `grep Huge /proc/meminfo`.

     > [!NOTE]
     > Existuje způsob, jak upravit soubor grub tak, aby velké stránky jsou vyhrazené při spuštění následující [pokyny](http://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment) pro DPDK. Instrukce je v dolní části stránky. Při spouštění na virtuálním počítači Azure s Linuxem, místo toho upravte soubory pod /etc/config/grub.d rezervovat hugepages mezi restartováními.

2. MAC a IP adres: použití `ifconfig –a` zobrazíte MAC a IP adresy síťových rozhraní. *VF* síťové rozhraní a *NETVSC* síťové rozhraní mají stejnou adresu MAC, ale jen *NETVSC* síťové rozhraní má IP adresu. Rozhraní VF fungují jako podřízený server rozhraní NETVSC rozhraní.

3. PCI adresy

   * Přečtěte si adresu PCI, která se má použít pro *VF* s `ethtool -i <vf interface name>`.
   * Zkontrolujte tuto testpmd nepřijímá omylem přes zařízení pci VF pro *eth0*, pokud *eth0* nemá akcelerované síťové povolena. Pokud aplikace DPDK omylem převzalo rozhraní pro správu sítě a způsobí ztrátu připojení k SSH, použijte konzole sériového portu k ukončení DPDK aplikace, nebo pro zastavení nebo spuštění virtuálního počítače.

4. Zatížení *ibuverbs* při každé restartování s `modprobe -a ib_uverbs`. SLES 15 také načíst *mlx4_ib* s `modprobe -a mlx4_ib`.

## <a name="failsafe-pmd"></a>Bezporuchový PMD

DPDK aplikací musí spouštět bezporuchový PMD, která je vystavena v Azure. Pokud aplikace běží přímo nad VF PMD, neobdrží **všechny** pakety směřující do virtuálního počítače, protože některé pakety se zobrazí nad syntetické rozhraní. Spuštění přes bezporuchový PMD záruky, že aplikace obdrží všechny pakety směřující do ní a také zajišťuje, aby aplikace bude nadále spuštěna v režimu DPDK i v případě, VF je odebrán, když probíhá údržba hostitele. Další informace o bezporuchový PMD najdete [knihovny ovladačů režimu odolný proti selhání dotazování](http://doc.dpdk.org/guides/nics/fail_safe.html).

## <a name="run-testpmd"></a>Spustit testpmd

Použití `sudo` před *testpmd* příkaz ke spuštění v režimu root.

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>Basic: Inicializace adaptéru bezporuchový, Kontrola správnosti

1. Spusťte následující příkazy a spusťte aplikaci testpmd jednoho portu:

   ```bash
   testpmd -w <pci address from previous step> \
     --vdev="net_vdev_netvsc0,iface=eth1" \
     -- -i \
     --port-topology=chained
    ```

2. Spusťte následující příkazy a spusťte aplikaci testpmd duální port:

   ```bash
   testpmd -w <pci address nic1> \
   -w <pci address nic2> \
   --vdev="net_vdev_netvsc0,iface=eth1" \
   --vdev="net_vdev_netvsc1,iface=eth2" \
   -- -i
   ```

   Pokud spuštění s více než 2 adaptéry nic `--vdev` argument používá tento vzor: `net_vdev_netvsc<id>,iface=<vf’s pairing eth>`.

3.  Po zahájení spuštění `show port info all` chcete zkontrolovat informace o portu. Zobrazí se jedné nebo dvou DPDK porty, které jsou net_failsafe (ne *net_mlx4*).
4.  Použití `start <port> /stop <port>` spuštění provozu.

Předchozí příkazy Spustit *testpmd* v interaktivním režimu, který se doporučuje, vyzkoušet si některé příkazy testpmd.

### <a name="basic-single-sendersingle-receiver"></a>Basic: Jednoho odesílatele/jednoho příjemce

Následující příkazy pravidelně tisk pakety Statistika za sekundu:

1. Na straně Odesílání spusťte následující příkaz:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device intended to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
   ```

2. Na straně příjmu spusťte následující příkaz:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device intended to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=rxonly \
     --eth-peer=<port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
   ```

Při spouštění předchozích příkazů na virtuální počítač, změňte *IP_SRC_ADDR* a *IP_DST_ADDR* v `app/test-pmd/txonly.c` tak, aby odpovídaly vlastní IP adresu virtuálních počítačů před kompilací. Jinak pakety nezahodí před dosažením příjemce.

### <a name="advanced-single-sendersingle-forwarder"></a>Pokročilé: Předávání jednoho odesílatele/jedním
Následující příkazy pravidelně tisk pakety Statistika za sekundu:

1. Na straně Odesílání spusťte následující příkaz:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device intended to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
    ```

2. Na straně FWD (předat) spusťte následující příkaz:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address NIC1> \
     -w <pci address NIC2> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     --vdev="net_vdev_netvsc<2nd id>,iface=<2nd iface to attach to>" (you need as many --vdev arguments as the number of devices used by testpmd, in this case) \
     -- --nb-cores <number of cores to use for test pmd> \
     --forward-mode=io \
     --eth-peer=<recv port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
    ```

Při spouštění předchozích příkazů na virtuální počítač, změňte *IP_SRC_ADDR* a *IP_DST_ADDR* v `app/test-pmd/txonly.c` tak, aby odpovídaly vlastní IP adresu virtuálních počítačů před kompilací. Jinak pakety nezahodí před dosažením předávání. Není možné mít třetí počítač přijímat přenosy přesměrované, protože *testpmd* předávání nemění adresy vrstvy 3, není-li provést nějaké změny kódu.

## <a name="references"></a>Odkazy

* [EAL možnosti](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Příkazy Testpmd](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
