---
title: Testování latence sítě virtuálních počítačů Azure ve virtuální síti Azure | Microsoft Docs
description: Naučte se testovat latenci sítě mezi virtuálními počítači Azure ve virtuální síti.
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: 7dc8aac730fdf46cab47a3297b8c001cb0b8e314
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226401"
---
# <a name="test-vm-network-latency"></a>Test latence sítě virtuálních počítačů

Abyste dosáhli nejpřesnější výsledků, změřte latenci sítě virtuálních počítačů Azure pomocí nástroje, který je pro tento úkol navržený. Veřejně dostupné nástroje, jako je SockPerf (pro Linux) a latte.exe (pro Windows), můžou izolovat a měřit latenci sítě s výjimkou jiných typů latence, jako je například latence aplikace. Tyto nástroje se zaměřují na druh síťového provozu, který má vliv na výkon aplikace (konkrétně přenosový protokol TCP] a přenos UDP (User Datagram Protocol)). 

Další běžné nástroje pro připojení, jako je třeba test, můžou měřit latenci, ale jejich výsledky nemusí představovat síťový provoz, který se používá v reálných úlohách. Důvodem je, že většina těchto nástrojů využívá protokol ICMP (Internet Control Message Protocol), který může být zpracován odlišně od provozu aplikace a jehož výsledky se nemusí vztahovat na úlohy, které používají protokoly TCP a UDP. 

Pro přesné testování latence sítě protokolů používaných většinou aplikací SockPerf (pro Linux) a latte.exe (pro Windows) se vydávají nejrelevantnější výsledky. Tento článek se týká obou těchto nástrojů.

## <a name="overview"></a>Přehled

Když použijete dva virtuální počítače, jeden jako odesílatel a jeden jako přijímač, vytvoříte obousměrný komunikační kanál. S tímto přístupem můžete odesílat a přijímat pakety v obou směrech a měřit dobu odezvy (RTT).

Tento postup můžete použít k měření latence sítě mezi dvěma virtuálními počítači nebo dokonce mezi dvěma fyzickými počítači. Měření latence může být užitečné v následujících situacích:

- Vytvořte srovnávací test latence sítě mezi nasazenými virtuálními počítači.
- Porovnání účinků změn v latenci sítě po provedení souvisejících změn:
  - Operační systém (OS) nebo software síťového zásobníku, včetně změn konfigurace.
  - Metoda nasazení virtuálního počítače, jako je například nasazení do zóny dostupnosti nebo skupiny umístění blízkosti (PPG).
  - Vlastnosti virtuálního počítače, například urychlené síťové nebo změny velikosti.
  - Virtuální síť, například směrování nebo filtrování změn.

### <a name="tools-for-testing"></a>Nástroje pro testování
Pro měření latence máte dvě různé možnosti nástrojů:

* Pro systémy Windows: [latte.exe (Windows)](https://github.com/microsoft/latte/releases/download/v0/latte.exe)
* Pro počítače se systémem Linux: [SockPerf (Linux)](https://github.com/mellanox/sockperf)

Pomocí těchto nástrojů pomůžete zajistit, že se měří jenom doba doručení datových částí TCP nebo UDP, a ne protokol ICMP (příkazového testu) nebo jiné typy paketů, které nepoužívá aplikace a neovlivní jejich výkon.

### <a name="tips-for-creating-an-optimal-vm-configuration"></a>Tipy pro vytvoření optimální konfigurace virtuálních počítačů

Při vytváření konfigurace virtuálních počítačů mějte na paměti následující doporučení:
- Použijte nejnovější verzi systému Windows nebo Linux.
- Povolte urychlené síťové služby pro dosažení nejlepších výsledků.
- Nasaďte virtuální počítače se [skupinou umístění blízkosti Azure](../virtual-machines/co-location.md).
- Větší virtuální počítače obvykle provádějí lepší výkon než menší virtuální počítače.

### <a name="tips-for-analysis"></a>Tipy pro analýzu

Při analýze výsledků testů Pamatujte na následující doporučení:

- Navažte základní základnu, jakmile se dokončí nasazení, konfigurace a optimalizace.
- Vždy Porovnejte nové výsledky se směrným plánem, nebo v opačném případě z jednoho testu na jiný se kontrolovanými změnami.
- Opakovat testy vždy, když jsou zjištěny nebo plánovány změny.


## <a name="test-vms-that-are-running-windows"></a>Testovací virtuální počítače se systémem Windows

### <a name="get-latteexe-onto-the-vms"></a>Získat latte.exe na virtuální počítače

Stáhněte si [nejnovější verzi latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b).

Zvažte vložení latte.exe do samostatné složky, jako je například *c:\Tools*.

### <a name="allow-latteexe-through-windows-defender-firewall"></a>Povolení latte.exe prostřednictvím firewallu v programu Windows Defender

Na *přijímači* vytvořte pravidlo povolení v bráně firewall v programu Windows Defender, které umožní doručení latte.exe provozu. Je nejjednodušší povolit celý latte.exe program podle názvu, nikoli povolit příchozí porty TCP.

Povolte latte.exe pomocí brány firewall v programu Windows Defender spuštěním následujícího příkazu:

```cmd
netsh advfirewall firewall add rule program=<path>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

Pokud jste například zkopírovali latte.exe do složky *c:\Tools* , bude to příkaz:

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Testy latence spuštění

* Na *přijímači* spusťte latte.exe (spusťte ho z okna cmd, nikoli z PowerShellu):

    ```cmd
    latte -a <Receiver IP address>:<port> -i <iterations>
    ```

    Přibližně 65 000 iterací jsou dostatečně dlouhé, aby vracely reprezentativní výsledky.

    Jakékoli dostupné číslo portu je přesné.

    Pokud má virtuální počítač IP adresu 10.0.0.4, příkaz by vypadal takto:

    `latte -a 10.0.0.4:5005 -i 65100`

* Na *odesilateli* spusťte latte.exe (spusťte ho z okna cmd, nikoli z PowerShellu):

    ```cmd
    latte -c -a <Receiver IP address>:<port> -i <iterations>
    ```

    Výsledný příkaz je stejný jako na přijímači s výjimkou přidání &nbsp; *-c* k označení toho, že se jedná o *klienta* nebo *odesílateli*:

    `latte -c -a 10.0.0.4:5005 -i 65100`

Počkejte na výsledky. V závislosti na tom, jak daleko se jedná o virtuální počítače, může dokončení testu trvat několik minut. Zvažte, že začnete s menším počtem iterací a otestujete úspěch před spuštěním delších testů.

## <a name="test-vms-that-are-running-linux"></a>Testovací virtuální počítače se systémem Linux

K testování virtuálních počítačů, na kterých běží Linux, použijte [SockPerf](https://github.com/mellanox/sockperf).

### <a name="install-sockperf-on-the-vms"></a>Instalace SockPerf na virtuální počítače

Na virtuálních počítačích se systémem Linux ( *Odesílatel* i *přijímač*) spusťte následující příkazy, které připraví SockPerf na virtuálních počítačích. Příkazy jsou k dispozici pro hlavní distribuce.

#### <a name="for-red-hat-enterprise-linux-rhelcentos"></a>Pro Red Hat Enterprise Linux (RHEL)/CentOS

Spusťte následující příkazy:

```bash
#RHEL/CentOS - Install Git and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu"></a>Pro Ubuntu

Spusťte následující příkazy:

```bash
#Ubuntu - Install Git and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros"></a>Pro všechny distribuce

Zkopírujte, zkompilujte a nainstalujte SockPerf podle následujících kroků:

```bash
#Bash - all distros

#From bash command line (assumes Git is installed)
git clone https://github.com/mellanox/sockperf
cd sockperf/
./autogen.sh
./configure --prefix=

#make is slower, may take several minutes
make

#make install is fast
sudo make install
```

### <a name="run-sockperf-on-the-vms"></a>Spuštění SockPerf na virtuálních počítačích

Po dokončení instalace SockPerf jsou virtuální počítače připravené ke spuštění testů latence. 

Nejdřív na *přijímači* spusťte SockPerf.

Jakékoli dostupné číslo portu je přesné. V tomto příkladu používáme port 12345:

```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```

Teď, když server naslouchá, může klient začít odesílat pakety na server na portu, na kterém naslouchá (v tomto případě 12345).

Přibližně 100 sekund je dostatečně dlouhý, aby vracely reprezentativní výsledky, jak je znázorněno v následujícím příkladu:

```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

Počkejte na výsledky. V závislosti na tom, jak daleko se jedná o virtuální počítače, se počet iterací bude lišit. Chcete-li provést test na úspěch před spuštěním delších testů, zvažte spuštění s kratšími testy přibližně 5 sekund.

Tento příklad SockPerf používá velikost zprávy 350, což je typické pro průměrný paket. Velikost můžete nastavit na vyšší nebo nižší, abyste dosáhli výsledků, které přesněji reprezentují úlohy, které běží na vašich virtuálních počítačích.


## <a name="next-steps"></a>Další kroky
* Vylepšete latenci pomocí [skupiny umístění s použitím blízkosti Azure](../virtual-machines/co-location.md).
* Naučte se [optimalizovat sítě pro virtuální počítače](../virtual-network/virtual-network-optimize-network-bandwidth.md) ve vašem scénáři.
* Přečtěte si o [tom, jak je šířka pásma přidělena virtuálním počítačům](../virtual-network/virtual-machine-network-throughput.md).
* Další informace najdete v tématu [Nejčastější dotazy k Azure Virtual Network](../virtual-network/virtual-networks-faq.md).