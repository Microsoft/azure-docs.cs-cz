---
title: Ověření propustnost sítě VPN do virtuální sítě Microsoft Azure
description: Účelem tohoto dokumentu je pomoci uživateli ověřit propustnost sítě z jejich místních prostředků do virtuálního počítače Azure.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 05/29/2019
ms.author: radwiv
ms.reviewer: chadmat;genli
ms.openlocfilehash: a88e339e82484c2ec1cd2276f6218fa718b990f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75860482"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>Ověření propustnosti sítě VPN do virtuální sítě

Připojení brány VPN umožňuje navázat zabezpečené a mezimístní připojení mezi virtuální sítí v rámci Azure a místní IT infrastrukturou.

Tento článek ukazuje, jak ověřit propustnost sítě z místních prostředků do virtuálního počítače Azure ....

> [!NOTE]
> Tento článek je určen k diagnostice a opravě běžných problémů. Pokud se vám nedaří problém vyřešit pomocí následujících informací, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="overview"></a>Přehled

Připojení brány VPN zahrnuje následující součásti:

* Místní zařízení VPN (Zobrazení seznamu [ověřených zařízení VPN](vpn-gateway-about-vpn-devices.md#devicetable).)
* Veřejný internet
* Brána Azure VPN
* Virtuální počítač Azure

Následující diagram znázorňuje logické připojení místní sítě k virtuální síti Azure prostřednictvím sítě VPN.

![Logické připojení sítě zákazníka k síti MSFT pomocí sítě VPN](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Výpočet maximálního očekávaného příchozího přenosu/výstupu

1. Určete požadavky na směrnou propustnost aplikace.
1. Určete limity propustnost brány Azure VPN. Nápovědu najdete v části Brána skutážky v [části O bráně VPN .](vpn-gateway-about-vpngateways.md#gwsku)
1. Určete pokyny pro [propustnost virtuálního počítače Azure](../virtual-machines/virtual-machines-windows-sizes.md) pro velikost virtuálního počítače.
1. Určete šířku pásma poskytovatele internetových služeb (ISP).
1. Vypočítejte očekávanou propustnost tak, že vezmete nejmenší šířku pásma virtuálního počítače, brány VPN nebo poskytovatelů. měřeno v Megabitech za sekundu (/) děleno osmi (8).

Pokud vypočtená propustnost nesplňuje požadavky na směrnou propustnost aplikace, je nutné zvýšit šířku pásma prostředku, který jste identifikovali jako kritické místo. Pokud chcete změnit velikost brány Azure VPN, přečtěte si informace [o změně skladové položky brány brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Pokud chcete změnit velikost virtuálního počítače, [přečtěte si tématu Změna velikosti virtuálního počítače](../virtual-machines/virtual-machines-windows-resize-vm.md). Pokud nedochází k očekávané šířce pásma Internetu, můžete se také obrátit na svého internetového počítače.

> [!NOTE]
> Propustnost brány VPN je agregace všech připojení Site-to-Site\VNET-to-VNET nebo Point-to-Site.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Ověření propustnostsítě pomocí nástrojů pro výkon

Toto ověření by mělo být provedeno během mimo špičky, protože saturace propustnost tunelového propojení VPN během testování neposkytuje přesné výsledky.

Nástroj, který používáme pro tento test, je iPerf, který funguje jak na Windows, tak na Linuxu a má klientský i serverový režim. Je omezena na 3Gbps pro virtuální servery se systémem Windows.

Tento nástroj neprovádí žádné operace čtení a zápisu na disk. Vytváří pouze vlastní generovaný přenos TCP z jednoho konce na druhý. Generuje statistiky založené na experimentování, které měří šířku pásma k dispozici mezi klientskými a serverovými uzly. Při testování mezi dvěma uzly jeden uzel funguje jako server a druhý uzel funguje jako klient. Po dokončení tohoto testu doporučujeme obrátit role uzlů a otestovat propustnost odesílání i stahování v obou uzlech.

### <a name="download-iperf"></a>Stažení iPerf

Stáhnout [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). Podrobnosti viz [dokumentace iPerf](https://iperf.fr/iperf-doc.php).

 > [!NOTE]
 > Produkty jiných výrobců popsané v tomto článku jsou vyráběny společnostmi nezávislými na společnosti Microsoft. Společnost Microsoft neposkytuje v souvislosti s výkonem a spolehlivostí těchto produktů žádnou záruku, předpokládanou ani jinou.

### <a name="run-iperf-iperf3exe"></a>Spustit iPerf (iperf3.exe)

1. Povolte pravidlo NSG/ACL umožňující provoz (pro testování veřejných IP adres na virtuálním počítači Azure).

1. Na obou uzlech povolte výjimku brány firewall pro port 5001.

   **Windows:** Spusťte následující příkaz jako správce:

   ```CMD
   netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
   ```

   Chcete-li pravidlo odebrat po dokončení testování, spusťte tento příkaz:

   ```CMD
   netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
   ```

   **Azure Linux:** Image Azure Linux mají tolerantní brány firewall. Pokud je aplikace naslouchání na portu, přenos je povolena prostřednictvím. Vlastní image, které jsou zabezpečeny, mohou vyžadovat explicitně otevřené porty. Mezi běžné brány firewall na `iptables` `ufw`vrstvě `firewalld`operačního systému Linux patří , , nebo .

1. V uzlu serveru změňte na adresář, kde je extrahován soubor iperf3.exe. Poté spusťte iPerf v režimu serveru a nastavte jej tak, aby naslouchal na portu 5001 jako následující příkazy:

   ```CMD
   cd c:\iperf-3.1.2-win65

   iperf3.exe -s -p 5001
   ```

   > [!Note]
   > Port 5001 lze přizpůsobit tak, aby zohledňoval konkrétní omezení brány firewall ve vašem prostředí.

1. V klientském uzlu změňte na adresář, ve kterém je nástroj iperf extrahován, a spusťte následující příkaz:

   ```CMD
   iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
   ```

   Klient směruje třicet sekund provozu na portu 5001 na server. Příznak '-P ' označuje, že provádíme 32 souběžných připojení k uzlu serveru.

   Následující obrazovka ukazuje výstup z tohoto příkladu:

   ![Výstup](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

1. (NEPOVINNÉ) Chcete-li zachovat výsledky testování, spusťte tento příkaz:

   ```CMD
   iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
   ```

1. Po dokončení předchozích kroků proveďte stejné kroky s obrácenými rolemi, takže uzel serveru bude nyní klientským uzlem a naopak.

> [!Note]
> Iperf není jediný nástroj. [NTTTCP je alternativní řešení pro testování](https://docs.microsoft.com/azure/virtual-network/virtual-network-bandwidth-testing).

## <a name="test-vms-running-windows"></a>Testování virtuálních aplikací se systémem Windows

### <a name="load-latteexe-onto-the-vms"></a>Načtení souboru Latte.exe na virtuální počítač

Stáhněte si nejnovější verzi [Latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Zvažte umístění souboru Latte.exe do samostatné složky, například`c:\tools`

### <a name="allow-latteexe-through-the-windows-firewall"></a>Povolit soubor Latte.exe prostřednictvím brány firewall systému Windows

Na přijímači vytvořte pravidlo povolit v bráně Windows Firewall, které umožní doručení provozu latte.exe. Je nejjednodušší povolit celý program Latte.exe podle názvu, nikoli povolit příchozí příchozí porty TCP.

### <a name="allow-latteexe-through-the-windows-firewall-like-this"></a>Povolit latte.exe prostřednictvím brány Windows Firewall, jako je tento

`netsh advfirewall firewall add rule program=<PATH>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

Pokud jste například zkopírovali soubor latte.exe do složky "c:\tools", bude to příkaz

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Spuštění testů latence

Spuštění souboru latte.exe na přijímači (spuštění z CMD, nikoli z prostředí PowerShell):

`latte -a <Receiver IP address>:<port> -i <iterations>`

Kolem 65 kititů je dostatečně dlouhá, aby vrátila reprezentativní výsledky.

Jakékoli dostupné číslo portu je v pořádku.

Pokud má virtuální virtuální ms IP adresu 10.0.0.4, bude vypadat takto.

`latte -c -a 10.0.0.4:5005 -i 65100`

Spuštění latte.exe na SENDER (spustit z CMD, ne z Prostředí PowerShell)

`latte -c -a <Receiver IP address>:<port> -i <iterations>`

Výsledný příkaz je stejný jako na přijímači s výjimkou přidání "-c" označující, že se jedná o "klient" nebo odesílatele

`latte -c -a 10.0.0.4:5005 -i 65100`

Počkejte na výsledky. V závislosti na tom, jak daleko od sebe virtuální chod, může trvat několik minut. Zvažte spuštění s menším počtem iterací, abyste otestovali úspěch před spuštěním delších testů.

## <a name="test-vms-running-linux"></a>Testování virtuálních počítačů se systémem Linux

Použití [SockPerf](https://github.com/mellanox/sockperf) k testování virtuálních měn.

### <a name="install-sockperf-on-the-vms"></a>Instalace SockPerf na virtuálních počítačích

Na virtuálních počítačích s Linuxem (odesílatel i příjemce) spusťte tyto příkazy a připravte sockPerf na virtuálních počítačích:

#### <a name="centos--rhel---install-git-and-other-helpful-tools"></a>CentOS / RHEL - Instalace GIT a dalších užitečných nástrojů

`sudo yum install gcc -y -q`
`sudo yum install git -y -q`
`sudo yum install gcc-c++ -y`
`sudo yum install ncurses-devel -y`
`sudo yum install -y automake`

#### <a name="ubuntu---install-git-and-other-helpful-tools"></a>Ubuntu - Instalace GIT a dalších užitečných nástrojů

`sudo apt-get install build-essential -y`
`sudo apt-get install git -y -q`
`sudo apt-get install -y autotools-dev`
`sudo apt-get install -y automake`

#### <a name="bash---all"></a>Bash - vše

Z příkazového řádku bash (předpokládá, že git je nainstalován)

`git clone https://github.com/mellanox/sockperf`
`cd sockperf/`
`./autogen.sh`
`./configure --prefix=`

Make je pomalejší, může trvat několik minut

`make`

Make instalace je rychlá

`sudo make install`

### <a name="run-sockperf-on-the-vms"></a>Spuštění sockPerf na virtuálních počítačích

#### <a name="sample-commands-after-installation-serverreceiver---assumes-servers-ip-is-10004"></a>Ukázkové příkazy po instalaci. Server/přijímač - předpokládá, že IP adresa serveru je 10.0.0.4

`sudo sockperf sr --tcp -i 10.0.0.4 -p 12345 --full-rtt`

#### <a name="client---assumes-servers-ip-is-10004"></a>Klient - předpokládá, že IP adresa serveru je 10.0.0.4

`sockperf ping-pong -i 10.0.0.4 --tcp -m 1400 -t 101 -p 12345  --full-rtt`

> [!Note]
> Ujistěte se, že neexistují žádné mezilehlé směrování (například virtuální zařízení) během testování propustnosti mezi virtuálním počítačem a bránou.
> Pokud existují špatné výsledky (z hlediska celkové propustnosti) pocházející z iPERF/NTTTCP testy výše, naleznete v následujícím článku pochopit klíčové faktory za možné příčiny problému:https://docs.microsoft.com/azure/virtual-network/virtual-network-tcpip-performance-tuning

Zejména analýza trasování sběru paketů (Wireshark/Network Monitor) shromážděná paralelně od klienta a serveru během těchto testů pomůže při hodnocení špatného výkonu. Tyto trasování může zahrnovat ztrátu paketů, vysokou latenci, velikost MTU. fragmentace, TCP 0 Window, Out of Order fragmenty a tak dále.

## <a name="address-slow-file-copy-issues"></a>Řešení problémů s pomalým kopírováním souborů

I v případě, že celková propustnost posuzovaná předchozími kroky (iPERF/NTTTCP/etc..) byla dobrá, může dojít k pomalému zvládání souborů při použití průzkumníka Windows nebo přetažení mačkání mačkání mj. Tento problém je obvykle způsoben jedním nebo oběma následujícími faktory:

* Aplikace pro kopírování souborů, například Průzkumník Windows a RDP, při kopírování souborů nepoužívají více vláken. Pro lepší výkon použijte vícevláknovou aplikaci pro kopírování souborů, jako je [Richcopy,](https://technet.microsoft.com/magazine/2009.04.utilityspotlight.aspx) ke kopírování souborů pomocí 16 nebo 32 vláken. Chcete-li změnit číslo vlákna pro kopírování souborů v richcopy, klepněte na**možnosti kopírovat položky** >  **Akce** > **Kopírování souborů**.

   ![Problémy s pomalým kopírováním souborů](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>

   > [!Note]
   > Ne všechny aplikace fungují stejně a ne všechny aplikace/proces využívá všechna vlákna. Pokud spustíte test, můžete vidět, že některá vlákna jsou prázdná a neposkytují přesné výsledky propustnost.
   > Chcete-li zkontrolovat výkon přenosu souboru aplikace, použijte více vlákna zvýšením # vlákna za sebou nebo snížení, aby bylo možné najít optimální propustnost aplikace nebo přenosu souborů.

* Nedostatečná rychlost čtení a zápisu disku virtuálního počítače. Další informace najdete v [tématu Řešení potíží s úložištěm Azure](../storage/common/storage-e2e-troubleshooting.md).

## <a name="on-premises-device-external-facing-interface"></a>Externí rozhraní pro externí přístup zařízení k místnímu zařízení

Zmínil podsítě místnírozsahy, které chcete, aby Azure přístup prostřednictvím VPN na místní síťové brány. Současně definujte adresní prostor virtuální sítě v Azure pro místní zařízení.

* **Brána založená na trase**: Zásady nebo volič provozu pro sítě VIRTUÁLNÍ SÍTĚ založené na trase jsou konfigurovány jako libovolné (nebo zástupné znaky).

* **Brána založená na zásadách:** Sítě virtuálních sítí založené na zásadách šifrují a směrují pakety prostřednictvím tunelových propojení IPsec na základě kombinací předpon adres mezi místní sítí a virtuální sítí Azure. Zásada (nebo selektor provozu) se většinou v konfiguraci sítě VPN definuje jako přístupový seznam.

* **Připojení UsePolicyBasedTrafficSelector:** ("UsePolicyBasedTrafficSelectors" ke $True připojení nakonfiguruje bránu Azure VPN pro připojení k bráně vpn založené na zásadách v místním prostředí. Pokud povolíte PolicyBasedTrafficSelectors, musíte zajistit, aby vaše zařízení VPN bylo vybaveno odpovídajícími voliči provozu definovanými se všemi kombinacemi předpon místní sítě (místní síťová brána) do a z předpon virtuální sítě Azure, namísto any-to-any.

Nevhodná konfigurace může vést k častému odpojování v rámci tunelu, poklesu paketů, chybné propustnosti a latenci.

## <a name="check-latency"></a>Kontrola latence

Latenci můžete zkontrolovat pomocí následujících nástrojů:

* WinMTR
* Trasa TCPTraceroute
* `ping`a `psping` (Tyto nástroje mohou poskytnout dobrý odhad RTT, ale nemohou být použity ve všech případech.)

![Zkontrolovat latenci](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

Pokud si všimnete špičky s vysokou latencí v některém z směrování před vstupem do páteřní sítě MS, můžete pokračovat v dalším šetření u svého poskytovatele internetových služeb.

Pokud je zaznamenán velký, neobvyklý nárůst latence z chmele v rámci "msn.net", obraťte se na podporu MS pro další šetření.

## <a name="next-steps"></a>Další kroky

Další informace nebo nápovědu najdete na následujícím odkazu:

* [Podpora společnosti Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
