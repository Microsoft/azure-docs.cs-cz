---
title: DPDK v virtuálním počítači Azure Linux | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nastavit DPDK ve virtuálním počítači s Linuxem.
services: virtual-network
documentationcenter: na
author: laxmanrb
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2018
ms.author: labattul
ms.openlocfilehash: c79c1fd687e329b97a854a3ff66a3cf95076b5d6
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384224"
---
# <a name="set-up-dpdk-in-a-linux-virtual-machine"></a>Nastavení dpdk ve virtuálním počítači s Linuxem

Data Plane Development Kit (DPDK) v Azure nabízí rychlejší uživatelské prostorové zpracování paketů pro aplikace náročné na výkon. Tato architektura obchází zásobník sítě jádra virtuálního počítače.

Při typickém zpracování paketů, které používá síťový zásobník jádra, je proces řízen přerušením. Když síťové rozhraní přijímá příchozí pakety, dochází k přerušení jádra pro zpracování paketu a kontextový přepínač z prostoru jádra do uživatelského prostoru. DPDK eliminuje přepínání kontextu a přerušení řízené metody ve prospěch implementace uživatelského prostoru, který používá ovladače režimu dotazování pro rychlé zpracování paketů.

DPDK se skládá ze sad knihoven uživatelského prostoru, které poskytují přístup k prostředkům nižší úrovně. Tyto prostředky mohou zahrnovat ovladače hardwaru, logických jader, správy paměti a režimu dotazování pro karty síťového rozhraní.

DPDK můžete spustit na virtuálních počítačích Azure, které podporují více distribucí operačního systému. DPDK poskytuje klíčovou diferenciaci výkonu v implementaci virtualizace funkcí sítě. Tyto implementace mohou mít podobu síťových virtuálních zařízení (NVA), jako jsou virtuální směrovače, brány firewall, sítě VIRTUÁLNÍSÍTĚ, nástroje pro vyrovnávání zatížení, vyvinutá jádra paketů a aplikace ddovařské služby (DDoS).

## <a name="benefit"></a>Výhoda

**Vyšší pakety za sekundu (PPS):** Vynechání jádra a převzetí kontroly paketů v uživatelském prostoru snižuje počet cyklů odstraněním kontextových přepínačů. Také zlepšuje rychlost paketů, které jsou zpracovávány za sekundu ve virtuálních počítačích Azure Linux.


## <a name="supported-operating-systems"></a>Podporované operační systémy

Podporovány jsou následující distribuce z Azure Marketplace:

| Operační systém Linux     | Verze jádra               | 
|--------------|---------------------------   |
| Ubuntu 16.04 | 4.15.0-1014-azure+           | 
| Ubuntu 18.04 | 4.15.0-1014-azure+           |
| SLES 15 SP1  | 4.12.14-8.27-azure+          | 
| RHEL 7.5     | 3.10.0-862.11.6.el7.x86_64+  | 
| CentOS 7.5   | 3.10.0-862.11.6.el7.x86_64+  | 

**Vlastní podpora jádra**

Pro všechny verze jádra Linuxu, která není uvedena, najdete v tématu [Opravy pro vytváření Azure-laděné linuxové jádro](https://github.com/microsoft/azure-linux-kernel). Pro více informací můžete [azuredpdk@microsoft.com](mailto:azuredpdk@microsoft.com)také kontaktovat . 

## <a name="region-support"></a>Podpora regionu

Všechny oblasti Azure podporují DPDK.

## <a name="prerequisites"></a>Požadavky

Na virtuálním počítači s Linuxem musí být povolena zrychlená síť. Virtuální počítač by měl mít alespoň dvě síťová rozhraní s jedním rozhraním pro správu. Naučte se, jak [vytvořit virtuální počítač linuxs s povolenou zrychlenou sítí](create-vm-accelerated-networking-cli.md).

## <a name="install-dpdk-dependencies"></a>Instalace závislostí DPDK

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

### <a name="rhel75centos-75"></a>RHEL7.5/CentOS 7.5

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

1. [Stáhněte si nejnovější DPDK](https://core.dpdk.org/download). Verze 18.11 LTS nebo 19.11 LTS je vyžadována pro Azure.
2. Vytvořte výchozí konfiguraci s . `make config T=x86_64-native-linuxapp-gcc`
3. Povolte Mellanox PMDv v generované `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config`konfiguraci s .
4. Kompilace `make`s .
5. Instalace `make install DESTDIR=<output folder>`pomocí aplikace .

## <a name="configure-the-runtime-environment"></a>Konfigurace prostředí runtime

Po restartování spusťte jednou následující příkazy:

1. Obrovské stránky

   * Nakonfigurujte hugepage spuštěním následujícího příkazu, jednou pro každý uzel numa:

     ```bash
     echo 1024 | sudo tee /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   * Vytvořte adresář pro `mkdir /mnt/huge`připojení pomocí aplikace .
   * Mount hugepages `mount -t hugetlbfs nodev /mnt/huge`s .
   * Zkontrolujte, zda jsou `grep Huge /proc/meminfo`stránky obrovské stránky rezervovány pomocí .

     > [POZNÁMKA] Existuje způsob, jak upravit soubor grub tak, aby hugepages jsou vyhrazeny při startu podle [pokynů](https://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment) pro DPDK. Pokyny jsou v dolní části stránky. Pokud používáte virtuální počítač Azure Linux, upravte soubory pod **/etc/config/grub.d** místo, abyste si vyhradili obrovské stránky napříč restartováními.

2. MAC & IP adresy: Slouží `ifconfig –a` k zobrazení MAC a IP adresy síťových rozhraní. Síťové rozhraní *VF* a síťové rozhraní *NETVSC* mají stejnou adresu MAC, ale pouze síťové rozhraní *NETVSC* má adresu IP. *Rozhraní VF* jsou spuštěna jako podřízená rozhraní rozhraní *NETVSC.*

3. ADRESY PCI

   * Slouží `ethtool -i <vf interface name>` k zjistit, které PCI adresu použít pro *VF*.
   * Pokud *eth0* má povoleno urychlování sítí, ujistěte se, že testpmd není omylem převzít *VF* pci zařízení pro *eth0*. Pokud aplikace DPDK omylem převezme rozhraní sítě pro správu a způsobí ztrátu připojení SSH, zastavte aplikaci DPDK pomocí sériové konzoly. Pomocí konzoly sériového počítače můžete také zastavit nebo spustit virtuální počítač.

4. Načíst *ibuverbs* při `modprobe -a ib_uverbs`každém restartu s . Pouze pro SLES 15 *mlx4_ib* také `modprobe -a mlx4_ib`naklápěte mlx4_ib s .

## <a name="failsafe-pmd"></a>Failsafe PMD

DPDK aplikace musí spustit přes failsafe PMD, který je vystaven v Azure. Pokud aplikace běží přímo přes *VF* PMD, nepřijímá **všechny** pakety, které jsou určeny pro virtuální modul, protože některé pakety se zobrazí přes syntetické rozhraní. 

Pokud spustíte aplikaci DPDK přes failsafe PMD, zaručuje, že aplikace obdrží všechny pakety, které jsou určeny k němu. Také zajišťuje, že aplikace udržuje spuštěna v režimu DPDK, i v případě, že VF je odvolán při provozu hostitele. Další informace o selhání PMD naleznete v [tématu Fail-safe poll mode driver library](https://doc.dpdk.org/guides/nics/fail_safe.html).

## <a name="run-testpmd"></a>Spustit testpmd

Chcete-li spustit testpmd `sudo` v kořenovém režimu, použijte před *testpmd* příkaz.

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>Základní: Kontrola příčetnosti, inicializace adaptéru failsafe

1. Spuštěním aplikace single port testpmd spusťte následující příkazy:

   ```bash
   testpmd -w <pci address from previous step> \
     --vdev="net_vdev_netvsc0,iface=eth1" \
     -- -i \
     --port-topology=chained
    ```

2. Spuštěním aplikace testpmd se dvěma porty spusťte následující příkazy:

   ```bash
   testpmd -w <pci address nic1> \
   -w <pci address nic2> \
   --vdev="net_vdev_netvsc0,iface=eth1" \
   --vdev="net_vdev_netvsc1,iface=eth2" \
   -- -i
   ```

   Pokud používáte testpmd s více než dvěma nic, `--vdev` argument `net_vdev_netvsc<id>,iface=<vf’s pairing eth>`následuje tento vzor: .

3.  Po spuštění spusťte `show port info all` a zkontrolujte informace o portu. Měli byste vidět jeden nebo dva porty DPDK, které jsou net_failsafe (nikoli *net_mlx4).*
4.  Slouží `start <port> /stop <port>` ke spuštění provozu.

Předchozí příkazy spustit *testpmd* v interaktivním režimu, který je doporučen pro vyzkoušení testpmd příkazy.

### <a name="basic-single-sendersingle-receiver"></a>Základní: Jeden odesílatel/jeden přijímač

Následující příkazy pravidelně tisknou pakety za sekundu statistiky:

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

2. Na straně RX spusťte následující příkaz:

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

Když na virtuálním počítači spouštěte předchozí příkazy, `app/test-pmd/txonly.c` změňte *IP_SRC_ADDR* a *IP_DST_ADDR* tak, aby odpovídaly skutečné IP adrese virtuálních počítačů před kompilací. V opačném případě pakety jsou vynechány před dosažením příjemce.

### <a name="advanced-single-sendersingle-forwarder"></a>Upřesnit: Jeden odesílatel/jeden předávání
Následující příkazy pravidelně tisknou pakety za sekundu statistiky:

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

Když na virtuálním počítači spouštěte předchozí příkazy, `app/test-pmd/txonly.c` změňte *IP_SRC_ADDR* a *IP_DST_ADDR* tak, aby odpovídaly skutečné IP adrese virtuálních počítačů před kompilací. V opačném případě pakety jsou vynechány před dosažením předávání. Nebudete moci mít třetí počítač přijímat předávaný provoz, protože *testpmd* předávání nezmění vrstvy 3 adresy, pokud provedete některé změny kódu.

## <a name="references"></a>Odkazy

* [Možnosti EAL](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Příkazy Testpmd](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
