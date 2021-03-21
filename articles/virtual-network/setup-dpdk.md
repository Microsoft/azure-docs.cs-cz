---
title: DPDK na virtuálním počítači Azure Linux | Microsoft Docs
description: Seznamte se s výhodami DPDK (data Development Kit) a jak nastavit DPDK na virtuálním počítači se systémem Linux.
services: virtual-network
documentationcenter: na
author: laxmanrb
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2020
ms.author: labattul
ms.openlocfilehash: 3b4d66525ec52ef2382dfbe97bc09278e35b31fb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102124665"
---
# <a name="set-up-dpdk-in-a-linux-virtual-machine"></a>Nastavení DPDK na virtuálním počítači se systémem Linux

Sada data Development Kit (DPDK) v Azure nabízí rychlejší rozhraní pro zpracování paketů v uživatelském prostoru pro aplikace náročné na výkon. Toto rozhraní obchází sadu síťových protokolů jádra virtuálního počítače.

V typickém zpracování paketů, které používá zásobník sítě jádra, je proces řízený přerušením. Když síťové rozhraní obdrží příchozí pakety, dojde k přerušení jádra za účelem zpracování paketu a kontextu přepnutí z prostoru jádra do uživatelského prostoru. DPDK eliminuje přepínání kontextu a metodu řízenou přerušením ve prospěch implementace v uživatelském prostoru, která pro rychlé zpracování paketů používá ovladače režimu cyklického dotazování.

DPDK se skládá ze sad knihoven uživatelských prostorů, které poskytují přístup k prostředkům nižší úrovně. Tyto prostředky můžou zahrnovat hardware, logické jádra, správu paměti a ovladače režimu cyklického dotazování pro síťové karty.

DPDK může běžet na virtuálních počítačích Azure, které podporují víc distribucí operačních systémů. DPDK poskytuje klíčové rozlišení výkonu při řízení implementace virtualizace síťové funkce. Tyto implementace můžou mít formu síťových virtuálních zařízení (síťová virtuální zařízení), jako jsou virtuální směrovače, brány firewall, sítě VPN, nástroje pro vyrovnávání zatížení, virtualizované jádro paketů a aplikace DDoS (Denial of Service).

## <a name="benefit"></a>Výhoda

**Vyšší počet paketů za sekundu (PPS)**: vynechání jádra a převzetí kontroly paketů v prostoru uživatele zmenší počet cyklů tím, že se odřadí přepínače kontextu. Zlepšuje také počet paketů zpracovávaných za sekundu ve virtuálních počítačích Azure Linux.


## <a name="supported-operating-systems"></a>Podporované operační systémy

Jsou podporovány následující distribuce z Azure Marketplace:

| Operační systém Linux     | Verze jádra               | 
|--------------|---------------------------   |
| Ubuntu 16.04 | 4.15.0-1014 – Azure +           | 
| Ubuntu 18.04 | 4.15.0-1014 – Azure +           |
| SLES 15 SP1  | 4.12.14-8.19 – Azure +          | 
| RHEL 7.5     | 3.10.0-862.11.6.el7.x86_64 +  | 
| CentOS 7.5   | 3.10.0-862.11.6.el7.x86_64 +  | 

**Podpora vlastního jádra**

Informace o všech neuvedených verzích jádra pro Linux najdete v tématu [opravy pro sestavení jádra systému Linux pro Azure](https://github.com/microsoft/azure-linux-kernel). Další informace můžete také kontaktovat [aznetdpdk@microsoft.com](mailto:aznetdpdk@microsoft.com) . 

## <a name="region-support"></a>Podpora oblastí

Všechny oblasti Azure podporují DPDK.

## <a name="prerequisites"></a>Předpoklady

Na virtuálním počítači se systémem Linux musí být povoleny akcelerované síťové služby. Virtuální počítač by měl mít alespoň dvě síťová rozhraní s jedním rozhraním pro správu. Povolení akcelerovaných síťových rozhraní u rozhraní pro správu se nedoporučuje. Naučte se, jak [vytvořit virtuální počítač se systémem Linux s povolenými akcelerovanými síťovými](create-vm-accelerated-networking-cli.md)službami.

## <a name="install-dpdk-dependencies"></a>Nainstalovat závislosti DPDK

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="rhel75centos-75"></a>RHEL 7.5/CentOS 7,5

```bash
yum -y groupinstall "Infiniband Support"
sudo dracut --add-drivers "mlx4_en mlx4_ib mlx5_ib" -f
yum install -y gcc kernel-devel-`uname -r` numactl-devel.x86_64 librdmacm-devel libmnl-devel
```

### <a name="sles-15-sp1"></a>SLES 15 SP1

**Jádro Azure**

```bash
zypper  \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-azure kernel-devel-azure gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

**Výchozí jádro**

```bash
zypper \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-default-devel gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

## <a name="set-up-the-virtual-machine-environment-once"></a>Nastavení prostředí virtuálního počítače (jednou)

1. [Stáhněte si nejnovější verzi DPDK](https://core.dpdk.org/download). Pro Azure se vyžaduje verze 18,11 LTS nebo 19,11 LTS.
2. Vytvořte výchozí konfiguraci pomocí `make config T=x86_64-native-linuxapp-gcc` .
3. Povolte Mellanox PMDs ve vygenerované konfiguraci pomocí `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config` .
4. Zkompilujte s `make` .
5. Nainstalujte pomocí `make install DESTDIR=<output folder>` .

## <a name="configure-the-runtime-environment"></a>Konfigurace běhového prostředí

Po restartování spusťte následující příkazy jednou:

1. Hugepages

   * Nakonfigurujte hugepage spuštěním následujícího příkazu, a to jednou pro každý uzel NUMA:

     ```bash
     echo 1024 | sudo tee /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   * Vytvořte adresář pro připojení `mkdir /mnt/huge` .
   * Připojte hugepages pomocí `mount -t hugetlbfs nodev /mnt/huge` .
   * Ověřte, že je hugepages rezervovaný `grep Huge /proc/meminfo` .

     > ZNAČTE Existuje způsob, jak upravit soubor GRUB tak, aby hugepages byly rezervované při spouštění, a to podle [pokynů](https://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment) pro DPDK. Pokyny najdete v dolní části stránky. Pokud používáte virtuální počítač Azure Linux, upravte místo toho soubory **/etc/config/grub.d** a vyhradte hugepages v rámci restartování.

2. IP adresa MAC &: použijte `ifconfig –a` k zobrazení adresy Mac a IP adresy síťových rozhraní. Síťové rozhraní *VF* a síťové rozhraní *NETVSC* mají stejnou adresu MAC, ale pouze síťové rozhraní *NETVSC* má IP adresu. Rozhraní *VF* jsou spuštěná jako podřízená rozhraní *NETVSC* rozhraní.

3. Adresy PCI

   * Použijte `ethtool -i <vf interface name>` k zjištění, která adresa PCI se má použít pro *VF*.
   * Pokud má *eth0* zapnuté akcelerované síťové služby, zajistěte, aby testpmd nechtěně převzala zařízení *VF* PCI pro *eth0*. Pokud aplikace DPDK omylem převezme rozhraní sítě pro správu a způsobí ztrátu připojení SSH, zastavte aplikaci DPDK pomocí konzoly sériového portu. K zastavení nebo spuštění virtuálního počítače můžete použít také konzolu sériového portu.

4. Načíst *ibuverbs* při každém restartování pomocí `modprobe -a ib_uverbs` . Pouze pro SLES 15 načtěte také *mlx4_ib* `modprobe -a mlx4_ib` .

## <a name="failsafe-pmd"></a>Failsafe PMD

DPDK aplikace musí běžet přes Failsafe PMD, která je vystavena v Azure. Pokud se aplikace spustí přímo přes *VF* PMD, neobdrží **všechny** pakety určené pro virtuální počítač, protože se některé pakety zobrazují přes syntetické rozhraní. 

Pokud spustíte aplikaci DPDK prostřednictvím Failsafe PMD, zaručuje to, že aplikace obdrží všechny pakety, které jsou určené pro ně. Také se ujistěte, že aplikace funguje v režimu DPDK, a to i v případě, že je VF při obsluhování hostitele odvolán. Další informace o Failsafe PMD najdete v tématu [Knihovna ovladačů režimu cyklického dotazování na selhání](https://doc.dpdk.org/guides/nics/fail_safe.html).

## <a name="run-testpmd"></a>Spustit testpmd

Chcete-li spustit testpmd v kořenovém režimu, použijte `sudo` před příkazem *testpmd* .

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>Základní: správnosti check, inicializace adaptéru Failsafe

1. Spuštěním následujících příkazů spusťte jednu aplikaci testpmd portů:

   ```bash
   testpmd -w <pci address from previous step> \
     --vdev="net_vdev_netvsc0,iface=eth1" \
     -- -i \
     --port-topology=chained
    ```

2. Spuštěním následujících příkazů spusťte aplikaci testpmd s duálním portem:

   ```bash
   testpmd -w <pci address nic1> \
   -w <pci address nic2> \
   --vdev="net_vdev_netvsc0,iface=eth1" \
   --vdev="net_vdev_netvsc1,iface=eth2" \
   -- -i
   ```

   Pokud používáte testpmd s více než dvěma síťovými rozhraními, `--vdev` argument odpovídá tomuto vzoru: `net_vdev_netvsc<id>,iface=<vf’s pairing eth>` .

3.  Po spuštění spusťte příkaz `show port info all` a ověřte informace o portu. Měl by se zobrazit jeden nebo dva porty DPDK, které jsou net_failsafe (není *net_mlx4*).
4.  Použijte `start <port> /stop <port>` ke spuštění provozu.

Předchozí příkazy se spustí *testpmd* v interaktivním režimu, což se doporučuje při vyzkoušení příkazů testpmd.

### <a name="basic-single-sendersingle-receiver"></a>Základní: jediný odesílatel/jeden přijímač

Následující příkazy pravidelně tisknou statistiku paketů za sekundu:

1. Na straně TX spusťte následující příkaz:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
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
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=rxonly \
     --eth-peer=<port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
   ```

Pokud spouštíte předchozí příkazy na virtuálním počítači, změňte *IP_SRC_ADDR* a *IP_DST_ADDR* v systému `app/test-pmd/txonly.c` tak, aby odpovídaly skutečné IP adrese virtuálních počítačů před kompilací. V opačném případě jsou pakety před dosažením přijímače vyřazeny.

### <a name="advanced-single-sendersingle-forwarder"></a>Upřesnit: jeden odesílatel/jeden server pro posílání
Následující příkazy pravidelně tisknou statistiku paketů za sekundu:

1. Na straně TX spusťte následující příkaz:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
    ```

2. Na straně FWD spusťte následující příkaz:

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

Pokud spouštíte předchozí příkazy na virtuálním počítači, změňte *IP_SRC_ADDR* a *IP_DST_ADDR* v systému `app/test-pmd/txonly.c` tak, aby odpovídaly skutečné IP adrese virtuálních počítačů před kompilací. V opačném případě jsou pakety vyřazeny před tím, než se dostane do serveru pro předání. Nebudete mít přístup k přesměrovanému provozu z jiného počítače, protože server pro přeposílání *testpmd* nemění adresy vrstvy 3, Pokud neprovedete nějaké změny kódu.

## <a name="references"></a>Reference

* [EAL možnosti](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Příkazy Testpmd](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
