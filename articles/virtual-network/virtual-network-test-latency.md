---
title: Testování latence sítě virtuálních strojů Azure ve virtuální síti Azure | Dokumenty společnosti Microsoft
description: Přečtěte si, jak otestovat latenci sítě mezi virtuálními počítači Azure ve virtuální síti
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
ms.openlocfilehash: 00efc2754948d53d4f80a6261dbd4041b358185b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896359"
---
# <a name="test-vm-network-latency"></a>Test latence sítě virtuálních počítačů

Chcete-li dosáhnout co nejpřesnějších výsledků, změřte latenci sítě virtuálního počítače (VM) pomocí nástroje, který je navržený pro daný úkol. Veřejně dostupné nástroje, jako je SockPerf (pro Linux) a latte.exe (pro Windows) můžete izolovat a měřit latenci sítě při vyloučení jiných typů latence, jako je například latence aplikace. Tyto nástroje se zaměřují na druh síťového provozu, který ovlivňuje výkon aplikace (konkrétně přenosový řídicí protokol [TCP] a přenosový protokol User Datagram Protocol [UDP] ). 

Další běžné nástroje pro připojení, jako je například ping, mohou měřit latenci, ale jejich výsledky nemusí představovat síťový provoz, který se používá v reálných úlohách. Důvodem je, že většina z těchto nástrojů používají Protokol ICMP (Internet Control Message Protocol), který lze zacházet jinak než s provozem aplikací a jejichž výsledky se nemusí vztahovat na úlohy, které používají protokoly TCP a UDP. 

Pro přesné testování latence sítě protokolů používaných většinou aplikací, SockPerf (pro Linux) a latte.exe (pro Windows) produkují nejrelevantnější výsledky. Tento článek popisuje oba tyto nástroje.

## <a name="overview"></a>Přehled

Pomocí dvou virtuálních m, jeden jako odesílatel a jeden jako příjemce, můžete vytvořit obousměrný komunikační kanál. S tímto přístupem můžete odesílat a přijímat pakety v obou směrech a měřit dobu odezvy (RTT).

Tento přístup můžete použít k měření latence sítě mezi dvěma virtuálními počítači nebo dokonce mezi dvěma fyzickými počítači. Měření latence může být užitečné pro následující scénáře:

- Vytvořte měřítko pro latenci sítě mezi nasazenými virtuálními servery.
- Porovnejte účinky změn latence sítě po provedených souvisejících změnách s:
  - Operační systém (OS) nebo software síťového zásobníku, včetně změn konfigurace.
  - Metoda nasazení virtuálního počítače, jako je například nasazení do zóny dostupnosti nebo skupiny umístění bez kontaktních míst (PPG).
  - Vlastnosti virtuálního počítače, jako je například akcelerované sítě nebo změny velikosti.
  - Virtuální síť, jako je například směrování nebo filtrování změn.

### <a name="tools-for-testing"></a>Nástroje pro testování
Chcete-li měřit latenci, máte dvě různé možnosti nástroje:

* Pro systémy se systémem Windows: [latte.exe (Windows)](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)
* Pro linuxové systémy: [SockPerf (Linux)](https://github.com/mellanox/sockperf)

Pomocí těchto nástrojů můžete zajistit, že se měří pouze doby doručení datové části Protokolu TCP nebo UDP, nikoli protokol ICMP (Ping) nebo jiné typy paketů, které aplikace nepoužívají a nemají vliv na jejich výkon.

### <a name="tips-for-creating-an-optimal-vm-configuration"></a>Tipy pro vytvoření optimální konfigurace virtuálního počítače

Při vytváření konfigurace virtuálního počítače mějte na paměti následující doporučení:
- Použijte nejnovější verzi Windows nebo Linuxu.
- Povolte zrychlené vytváření sítí pro dosažení nejlepších výsledků.
- Nasazení virtuálních počítačů se [skupinou umístění bezkontaktní](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)komunikace Azure .
- Větší virtuální virtuální zařízení obecně fungují lépe než menší virtuální chod.

### <a name="tips-for-analysis"></a>Tipy pro analýzu

Při analýze výsledků testů mějte na paměti následující doporučení:

- Vytvořte směrný plán brzy, jakmile nasazení, konfigurace a optimalizace jsou dokončeny.
- Vždy porovnejte nové výsledky s výchozím stavem nebo jiným způsobem z jednoho testu do druhého s řízenými změnami.
- Opakujte testy vždy, když jsou pozorovány nebo plánovány změny.


## <a name="test-vms-that-are-running-windows"></a>Testování virtuálních aplikací se systémem Windows

### <a name="get-latteexe-onto-the-vms"></a>Získání souboru latte.exe na virtuální počítačích

Stáhněte si [nejnovější verzi latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b).

Zvažte umístění souboru latte.exe do samostatné složky, například *c:\tools*.

### <a name="allow-latteexe-through-windows-defender-firewall"></a>Povolit soubor latte.exe prostřednictvím brány Windows Defender Firewall

V *přijímači*vytvořte pravidlo povolit v bráně Windows Defender Firewall, které umožní doručení provozu latte.exe. Je nejjednodušší povolit celý program latte.exe podle názvu, nikoli povolit příchozí příchozí porty TCP.

Povolit soubor latte.exe prostřednictvím brány Windows Defender Firewall spuštěním následujícího příkazu:

```cmd
netsh advfirewall firewall add rule program=<path>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

Pokud jste například zkopírovali soubor latte.exe do složky *c:\tools,* bude to příkaz:

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Spuštění testů latence

* Na *přijímači*spusťte latte.exe (spusťte jej z okna CMD, nikoli z prostředí PowerShell):

    ```cmd
    latte -a <Receiver IP address>:<port> -i <iterations>
    ```

    Přibližně 65 000 iterací je dostatečně dlouhé na to, aby bylo vráceno reprezentativní výsledky.

    Jakékoli dostupné číslo portu je v pořádku.

    Pokud má virtuální virtuální ms IP adresu 10.0.0.4, bude příkaz vypadat takto:

    `latte -a 10.0.0.4:5005 -i 65100`

* Na *odesílateli*, spusťte latte.exe (spusťte jej z okna CMD, nikoli z prostředí PowerShell):

    ```cmd
    latte -c -a <Receiver IP address>:<port> -i <iterations>
    ```

    Výsledný příkaz je stejný jako na přijímači, s&nbsp;výjimkou přidání *-c* označující, že se jedná o *klienta*nebo *odesílatele*:

    `latte -c -a 10.0.0.4:5005 -i 65100`

Počkejte na výsledky. V závislosti na tom, jak daleko od sebe virtuálních počítačích jsou, test může trvat několik minut na dokončení. Zvažte spuštění s menším počtem iterací, abyste otestovali úspěch před spuštěním delších testů.

## <a name="test-vms-that-are-running-linux"></a>Testování virtuálních počítačů se systémem Linux

Chcete-li otestovat virtuální počítače, které jsou spuštěny Linux, použijte [SockPerf](https://github.com/mellanox/sockperf).

### <a name="install-sockperf-on-the-vms"></a>Instalace SockPerf na virtuálních počítačích

Na virtuálních počítačích s Linuxem, *odesílatel* i *příjemce*, spusťte následující příkazy pro přípravu SockPerf na virtuálních počítačích. Příkazy jsou k dispozici pro hlavní distribuce.

#### <a name="for-red-hat-enterprise-linux-rhelcentos"></a>Pro Red Hat Enterprise Linux (RHEL)/Centos

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

Kopírování, kompilace a instalace SockPerf podle následujících kroků:

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

### <a name="run-sockperf-on-the-vms"></a>Spuštění sockPerf na virtuálních počítačích

Po dokončení instalace SockPerf virtuálních zařízení jsou připraveni ke spuštění testů latence. 

Nejprve spusťte SockPerf na *přijímači*.

Jakékoli dostupné číslo portu je v pořádku. V tomto příkladu používáme port 12345:

```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```

Nyní, když server naslouchá, může klient začít odesílat pakety na server na portu, na kterém naslouchá (v tomto případě 12345).

Přibližně 100 sekund je dostatečně dlouhé, aby bylo vráceno reprezentativní výsledky, jak je znázorněno v následujícím příkladu:

```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

Počkejte na výsledky. V závislosti na tom, jak daleko od sebe virtuálních počítačích jsou, počet iterací se bude lišit. Chcete-li otestovat úspěch před spuštěním delší testy, zvažte počínaje kratší testy asi 5 sekund.

Tento příklad SockPerf používá velikost zprávy 350 bajtů, což je typické pro průměrný paket. Můžete upravit velikost vyšší nebo nižší k dosažení výsledků, které přesněji představují zatížení, které běží na virtuálních počítačích.


## <a name="next-steps"></a>Další kroky
* Zlepšete latenci pomocí [skupiny umístění azure .](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)
* Přečtěte si, jak [optimalizovat sítě pro virtuální počítače](../virtual-network/virtual-network-optimize-network-bandwidth.md) pro váš scénář.
* Přečtěte si, [jak se přiděluje šířku pásma virtuálním počítačům](../virtual-network/virtual-machine-network-throughput.md).
* Další informace najdete v [tématu Nejčastější dotazy k virtuální síti Azure](../virtual-network/virtual-networks-faq.md).
