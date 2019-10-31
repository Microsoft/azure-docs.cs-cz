---
title: Testování latence sítě virtuálních počítačů Azure v Azure Virtual Network | Microsoft Docs
titleSuffix: Azure Virtual Network latency
description: Naučte se testovat latenci sítě mezi virtuálními počítači Azure ve virtuální síti.
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: d5efc1b802246597b4ee545b4d805e4f0d10ebb5
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73166561"
---
# <a name="testing-network-latency"></a>Testování latence sítě

Měření latence sítě pomocí nástroje, který je navržen pro úlohu, poskytne nejpřesnější výsledky. Mezi veřejně dostupné nástroje, jako je SockPerf (pro Linux) a latte (pro Windows), patří příklady nástrojů, které mohou izolovat a měřit latenci sítě s výjimkou jiných typů latence, jako je například latence aplikace. Tyto nástroje se zaměřují na druh síťového provozu, který ovlivňuje výkon aplikace, konkrétně TCP a UDP. K měření latence se někdy můžou použít i další běžné nástroje pro připojení, jako je třeba test, ale tyto výsledky nemusí být reprezentativní pro síťový provoz, který se používá v reálných úlohách. To&#39;vzhledem k tomu, že většina těchto nástrojů využívá protokol ICMP, který se může zpracovávat jinak než provoz aplikací, a výsledky nemusí být použitelné pro úlohy, které používají protokoly TCP a UDP. V případě přesného testování latence sítě protokolů používaných většinou aplikací SockPerf (pro Linux) a latte (pro Windows) se vydávají nejrelevantnější výsledky. Tento dokument popisuje oba tyto nástroje.

## <a name="overview"></a>Přehled

Pomocí dvou virtuálních počítačů, jako je odesílatel a jeden jako přijímač, se vytvoří obousměrný komunikační kanál pro odesílání a příjem paketů v obou směrech k měření doby odezvy (RTT).

Tyto kroky se dají použít k měření latence sítě mezi dvěma Virtual Machines (virtuálními počítači) nebo dokonce mezi dvěma fyzickými počítači. Měření latence může být užitečné v následujících situacích:

- Stanovení srovnávacích testů pro latenci sítě mezi nasazenými virtuálními počítači
- Porovnání účinků změn v latenci sítě po provedení souvisejících změn:
  - Software operačního systému nebo síťového zásobníku, včetně změn konfigurace
  - Metoda nasazení virtuálních počítačů, například nasazení do zóny nebo PPG
  - Vlastnosti virtuálního počítače, například urychlené síťové nebo změny velikosti
  - Virtuální síť, například změny směrování nebo filtrování

### <a name="tools-for-testing"></a>Nástroje pro testování
Pro měření latence máme dvě různé možnosti, jeden pro systémy Windows a jeden pro systémy na platformě Linux.

Pro Windows: latte. exe (Windows) [https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Pro Linux: SockPerf (Linux) [https://github.com/mellanox/sockperf](https://github.com/mellanox/sockperf)

Pomocí těchto nástrojů se ověří, že se měří jenom doba doručení datových částí TCP nebo UDP, a ne protokol ICMP (test) nebo jiné typy paketů, které nepoužívá aplikace a neovlivňují jejich výkon.

### <a name="tips-for-an-optimal-vm-configuration"></a>Tipy pro optimální konfiguraci virtuálních počítačů:

- Použijte nejnovější verzi systému Windows nebo Linux.
- Povolit urychlené síťové služby pro dosažení nejlepších výsledků
- Nasazení virtuálních počítačů se [skupinou umístění služby Azure Proximity](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)
- Větší virtuální počítače obvykle provádějí lepší výkon než menší virtuální počítače.

### <a name="tips-for-analysis"></a>Tipy pro analýzu

- Zaveďte směrný plán včas, jakmile budou dokončená nasazení, konfigurace a optimalizace.
- Vždy Porovnejte nové výsledky se směrným plánem nebo jiným způsobem z jednoho testu s řízenými změnami
- Opakovat testy pokaždé, když jsou zjištěny nebo plánovány změny


## <a name="testing-vms-running-windows"></a>Testování virtuálních počítačů s Windows:

## <a name="get-latteexe-onto-the-vms"></a>Získání latte. exe na virtuální počítače

Stáhnout nejnovější verzi: [https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Zvažte umístění latte. exe do samostatné složky, jako je c:\Tools

## <a name="allow-latteexe-through-the-windows-firewall"></a>Povolení latte. exe přes bránu Windows Firewall

Na PŘIJÍMAČi vytvořte v bráně Windows Firewall pravidlo povolení, které umožní doručení provozu latte. exe. Je nejjednodušší povolit celý program latte. exe podle názvu, nikoli povolit příchozí porty TCP.

Povolte latte. exe přes bránu Windows Firewall podobným způsobem:

netsh advfirewall firewall Add rule program =\<cesta\>\\latte. exe název =&quot;Latte&quot; protokol = any dir = in Action = Allow Enable = Yes Profile = ANY


Pokud jste například zkopírovali latte. exe do složky &quot;c:\\Tools&quot;, bude to tento příkaz:

```cmd
netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

## <a name="running-latency-tests"></a>Spouštění testů latence

Spusťte latte. exe na PŘIJÍMAČi (spusťte z CMD, nikoli z PowerShellu):

Latte-&lt;IP adresa přijímače&gt;:&lt;&gt;-i &lt;iterace&gt;

Kolem iterací 65 KB pro je dostatečně dlouhý, aby vracely reprezentativní výsledky.

Jakékoli dostupné číslo portu je přesné.

Pokud má virtuální počítač IP adresu 10.0.0.4, může to vypadat takto:

```cmd
latte -a 10.0.0.4:5005 -i 65100
```

Spusťte latte. exe na ODESILATELi (spusťte z CMD, ne z PowerShellu):

Latte-c-\<IP adresa přijímače\>:\<\>-i \<iterace\>


Výsledný příkaz je stejný jako u přijímače s výjimkou přidání&quot; &quot;-c k označení toho, že se jedná o &quot;&quot; klienta nebo odesílateli:

```cmd
latte -c -a 10.0.0.4:5005 -i 65100
```

Počkejte na výsledky. V závislosti na tom, jak daleko se jedná o virtuální počítače, může trvat několik minut, než se dokončí. Zvažte, že začnete s menším počtem iterací a otestujete úspěch před spuštěním delších testů.



## <a name="testing-vms-running-linux"></a>Testování virtuálních počítačů se systémem Linux

Použijte SockPerf. Je k dispozici z [https://github.com/mellanox/sockperf](https://github.com/mellanox/sockperf)

### <a name="install-sockperf-on-the-vms"></a>Instalace SockPerf na virtuální počítače

Na virtuálních počítačích se systémem Linux (ODESÍLATEL i přijímač) spusťte tyto příkazy pro přípravu SockPerf na virtuálních počítačích. Příkazy jsou k dispozici pro hlavní distribuce.

#### <a name="for-rhel--centos-follow-these-steps"></a>Pro RHEL/CentOS proveďte tyto kroky:
```bash
#CentOS / RHEL - Install GIT and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu-follow-these-steps"></a>Pro Ubuntu použijte následující postup:
```bash
#Ubuntu - Install GIT and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros-copy-compile-and-install-sockperf-according-to-the-following-steps"></a>Pro všechny distribuce, zkopírujte, zkompilujte a nainstalujte SockPerf podle následujících kroků:
```bash
#Bash - all distros

#From bash command line (assumes git is installed)
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

Nejdřív na PŘIJÍMAČi spusťte SockPerf.

Jakékoli dostupné číslo portu je přesné. V tomto příkladu používáme port 12345:
```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```
Teď, když server naslouchá, může klient začít odesílat pakety na server na portu, na kterém naslouchá, v tomto případě 12345.

Přibližně 100 sekund je dostatečně dlouhý, aby vracely reprezentativní výsledky, jak je znázorněno v následujícím příkladu:
```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

Počkejte na výsledky. V závislosti na tom, jak daleko se jedná o virtuální počítače, se počet iterací bude lišit. Zvažte, že začnete s kratšími testy přibližně 5 sekund, než budete zkoušet úspěch před spuštěním delších testů.

Tento příklad SockPerf používá velikost zprávy 350 bajtů, protože se jedná o typický paket o průměrné velikosti. Velikost zprávy je možné upravit vyšší nebo nižší, aby se dosáhlo výsledků, které přesněji reprezentují zatížení, které bude na virtuálních počítačích spuštěno.


## <a name="next-steps"></a>Další kroky
* Zvýšení latence se [skupinou umístění blízkosti Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)
* Seznamte se s postupem [optimalizace sítě pro virtuální počítače](../virtual-network/virtual-network-optimize-network-bandwidth.md) ve vašem scénáři.
* Přečtěte si o tom [, jak je šířka pásma přidělena virtuálním počítačům](../virtual-network/virtual-machine-network-throughput.md) .
* Další informace o [nejčastějších dotazech k Azure Virtual Network (FAQ)](../virtual-network/virtual-networks-faq.md)
