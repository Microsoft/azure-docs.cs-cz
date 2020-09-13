---
title: Ověření propustnosti sítě VPN pro Microsoft Azure Virtual Network
description: Tento článek vám pomůže ověřit propustnost sítě z vašich místních prostředků na virtuální počítač Azure.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
manager: dcscontentpm
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 09/02/2020
ms.author: radwiv
ms.reviewer: chadmat;genli
ms.openlocfilehash: d2347c0688ca58698831019a193d03fe2c6721e9
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89398503"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>Ověření propustnosti sítě VPN do virtuální sítě

Připojení brány VPN umožňuje vytvořit zabezpečené připojení mezi různými místy Virtual Network v rámci Azure a místní IT infrastruktury.

Tento článek ukazuje, jak ověřit propustnost sítě z místních prostředků na virtuální počítač Azure (VM).

> [!NOTE]
> Tento článek je určený pro pomoc s diagnostikou a řešením běžných problémů. Pokud tento problém nemůžete vyřešit pomocí následujících informací, obraťte se na [podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="overview"></a>Přehled

Připojení ke službě VPN Gateway zahrnuje následující součásti:

* Místní zařízení VPN (zobrazení seznamu [ověřených zařízení VPN](vpn-gateway-about-vpn-devices.md#devicetable))
* Veřejný Internet
* Azure VPN Gateway
* Virtuální počítač Azure

Následující diagram znázorňuje logické připojení místní sítě k virtuální síti Azure prostřednictvím sítě VPN.

![Logická Konektivita sítě zákazníka k síti protokolu MSFT pomocí sítě VPN](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Vypočítat maximální očekávaný počet příchozích a odchozích dat

1. Určení požadavků na propustnost standardních hodnot vaší aplikace
1. Určete omezení propustnosti Azure VPN Gateway. Nápovědu najdete v části "SKU brány" v tématu [o VPN Gateway](vpn-gateway-about-vpngateways.md#gwsku).
1. Určete [pokyny k propustnosti virtuálních počítačů Azure](../virtual-machines/sizes.md) pro velikost virtuálního počítače.
1. Určete šířku pásma poskytovatele internetových služeb (ISP).
1. Vypočítejte očekávanou propustnost tím, že vyberete minimální šířku pásma virtuálního počítače, VPN Gateway nebo poskytovatele internetových služeb. což se měří v megabajtech (/) dělených osmi (8).

Pokud vypočtená propustnost nevyhovuje požadavkům na propustnost směrného plánu vaší aplikace, je nutné zvýšit šířku pásma prostředku, který jste identifikovali jako kritické místo. Pokud chcete změnit velikost VPN Gateway Azure, přečtěte si téma [Změna skladové položky brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Pokud chcete změnit velikost virtuálního počítače, přečtěte si téma [Změna velikosti virtuálního](../virtual-machines/windows/resize-vm.md)počítače. Pokud nejste u očekávané šířky pásma internetu, můžete se také obrátit na poskytovatele internetových služeb.

> [!NOTE]
> VPN Gateway propustnost je agregace všech Site-to-Site\VNET-to-VNET a připojení typu Point-to-site.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Ověření propustnosti sítě pomocí nástrojů pro sledování výkonu

Toto ověření by se mělo provádět v době mimo špičku, protože při testování propustnost propustnosti tunelového připojení VPN neposkytuje přesné výsledky.

Nástroj používaný pro tento test je iPerf, který funguje v systémech Windows i Linux a má režimy klienta i serveru. Je omezený na 3Gbps pro virtuální počítače s Windows.

Tento nástroj neprovede žádné operace čtení a zápisu na disk. Výhradně vytváří samostatně generovaný provoz TCP od jednoho konce k druhému. Vygeneruje statistiku na základě experimentu, který měří šířku pásma dostupnou mezi klientským a serverovým uzlem. Při testování mezi dvěma uzly funguje jeden uzel jako server a druhý uzel funguje jako klient. Po dokončení tohoto testu doporučujeme vrátit role uzlů, abyste otestovali propustnost nahrávání i stahování obou uzlů.

### <a name="download-iperf"></a>Stáhnout iPerf

Stáhněte si [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). Podrobnosti najdete v [dokumentaci k iPerf](https://iperf.fr/iperf-doc.php).

 > [!NOTE]
 > Produkty třetích stran, které jsou popsány v tomto článku, jsou vyráběny společnostmi, které jsou nezávislé na společnosti Microsoft. Společnost Microsoft neposkytuje v souvislosti s výkonem a spolehlivostí těchto produktů žádnou záruku, předpokládanou ani jinou.

### <a name="run-iperf-iperf3exe"></a>Spustit iPerf (iperf3.exe)

1. Povolit NSG/ACL pravidlo umožňující provoz (pro testování veřejné IP adresy na virtuálním počítači Azure)

1. V obou uzlech povolte výjimku brány firewall pro port 5001.

   **Windows:** Spusťte následující příkaz jako správce:

   ```CMD
   netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
   ```

   Chcete-li po dokončení testování odebrat pravidlo, spusťte tento příkaz:

   ```CMD
   netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
   ```

   **Azure Linux:** Image Azure Linux mají opravňující brány firewall. Pokud je aplikace naslouchá na portu, je povolen přenos prostřednictvím služby. Vlastní image, které jsou zabezpečené, můžou vyžadovat explicitní otevření portů. Mezi běžné brány firewall pro Linux OS patří `iptables` , `ufw` , nebo `firewalld` .

1. V uzlu serveru přejděte do adresáře, kde je extrahováno iperf3.exe. Pak spusťte iPerf v režimu serveru a nastavte ho tak, aby naslouchal na portu 5001 jako následující příkazy:

   ```CMD
   cd c:\iperf-3.1.2-win65

   iperf3.exe -s -p 5001
   ```

   > [!Note]
   > Port 5001 je přizpůsobitelný pro účet pro konkrétní omezení brány firewall ve vašem prostředí.

1. V uzlu klient přejděte do adresáře, kde je extrahován nástroj Iperf, a poté spusťte následující příkaz:

   ```CMD
   iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
   ```

   Klient přesměruje třicet sekund provozu na portu 5001 na server. Příznak "-P" označuje, že provádíme 32 souběžných připojení k uzlu serveru.

   Následující obrazovka ukazuje výstup z tohoto příkladu:

   ![Výstup](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

1. VOLITELNÉ Chcete-li zachovat výsledky testování, spusťte tento příkaz:

   ```CMD
   iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
   ```

1. Po dokončení předchozích kroků spusťte stejný postup s rolemi obráceně, aby uzel serveru byl nyní klientským uzlem, a naopak.

> [!Note]
> Iperf není jediným nástrojem. [NTTTCP je alternativní řešení pro testování](https://docs.microsoft.com/azure/virtual-network/virtual-network-bandwidth-testing).

## <a name="test-vms-running-windows"></a>Testovací virtuální počítače s Windows

### <a name="load-latteexe-onto-the-vms"></a>Načíst Latte.exe do virtuálních počítačů

Stáhněte si nejnovější verzi [Latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Zvažte vložení Latte.exe do samostatné složky, jako je například `c:\tools`

### <a name="allow-latteexe-through-the-windows-firewall"></a>Povolení Latte.exe přes bránu Windows Firewall

Na přijímači vytvořte v bráně Windows Firewall pravidlo povolení, které umožní doručení Latte.exe provozu. Je nejjednodušší povolit celý Latte.exe program podle názvu, nikoli povolit příchozí porty TCP.

### <a name="allow-latteexe-through-the-windows-firewall-like-this"></a>Povoli Latte.exe přes bránu Windows Firewall jako

`netsh advfirewall firewall add rule program=<PATH>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

Pokud jste například zkopírovali latte.exe do složky "c:\Tools", bude to příkaz

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Testy latence spuštění

Spustit latte.exe na PŘIJÍMAČi (spustit z CMD, nikoli z PowerShellu):

`latte -a <Receiver IP address>:<port> -i <iterations>`

Kolem iterací 65 KB pro je dostatečně dlouhý, aby vracely reprezentativní výsledky.

Jakékoli dostupné číslo portu je přesné.

Pokud má virtuální počítač IP adresu 10.0.0.4, může to vypadat takto.

`latte -c -a 10.0.0.4:5005 -i 65100`

Spustit latte.exe na ODESILATELi (spustit z CMD, ne z PowerShellu)

`latte -c -a <Receiver IP address>:<port> -i <iterations>`

Výsledný příkaz je stejný jako u přijímače s výjimkou přidání "-c", který označuje, že se jedná o klienta nebo odesílatele.

`latte -c -a 10.0.0.4:5005 -i 65100`

Počkejte na výsledky. V závislosti na tom, jak daleko se jedná o virtuální počítače, může trvat několik minut, než se dokončí. Zvažte, že začnete s menším počtem iterací a otestujete úspěch před spuštěním delších testů.

## <a name="test-vms-running-linux"></a>Testovací virtuální počítače se systémem Linux

Použijte [SockPerf](https://github.com/mellanox/sockperf) k testování virtuálních počítačů.

### <a name="install-sockperf-on-the-vms"></a>Instalace SockPerf na virtuální počítače

Na virtuálních počítačích se systémem Linux (ODESÍLATEL i přijímač) spusťte tyto příkazy pro přípravu SockPerf na virtuálních počítačích:

#### <a name="centos--rhel---install-git-and-other-helpful-tools"></a>CentOS/RHEL – nainstalujte GIT a další užitečné nástroje

`sudo yum install gcc -y -q`
`sudo yum install git -y -q`
`sudo yum install gcc-c++ -y`
`sudo yum install ncurses-devel -y`
`sudo yum install -y automake`

#### <a name="ubuntu---install-git-and-other-helpful-tools"></a>Ubuntu – nainstalujte si GIT a další užitečné nástroje

`sudo apt-get install build-essential -y`
`sudo apt-get install git -y -q`
`sudo apt-get install -y autotools-dev`
`sudo apt-get install -y automake`

#### <a name="bash---all"></a>Bash – vše

Z příkazového řádku bash (předpokládá instalaci Git)

`git clone https://github.com/mellanox/sockperf`
`cd sockperf/`
`./autogen.sh`
`./configure --prefix=`

Udělejte to pomaleji, může trvat několik minut.

`make`

Vytvoření instalace je rychlé

`sudo make install`

### <a name="run-sockperf-on-the-vms"></a>Spuštění SockPerf na virtuálních počítačích

#### <a name="sample-commands-after-installation-serverreceiver---assumes-servers-ip-is-10004"></a>Ukázkové příkazy po instalaci. Server/přijímač – předpokládá, že IP adresa serveru je 10.0.0.4.

`sudo sockperf sr --tcp -i 10.0.0.4 -p 12345 --full-rtt`

#### <a name="client---assumes-servers-ip-is-10004"></a>Klient – předpokládá, že je IP adresa serveru 10.0.0.4

`sockperf ping-pong -i 10.0.0.4 --tcp -m 1400 -t 101 -p 12345  --full-rtt`

> [!Note]
> Ujistěte se, že během testování propustnosti mezi virtuálním počítačem a bránou neexistují žádné zprostředkující segmenty (např. virtuální zařízení).
> Pokud existují špatné výsledky (z hlediska celkové propustnosti) pocházející z výše uvedených testů iPERF/NTTTCP, přečtěte si následující článek, který vám pomůže pochopit klíčové faktory za možnými hlavními příčinami problému: https://docs.microsoft.com/azure/virtual-network/virtual-network-tcpip-performance-tuning

Konkrétně analýza trasování paketů (Wireshark/Sledování sítě) shromážděných paralelně od klienta a serveru během těchto testů pomůže při posuzování špatného výkonu. Tato trasování můžou zahrnovat ztrátu paketů, vysokou latenci a velikost MTU. fragmentace, okno protokolu TCP 0, fragmenty mimo pořadí a tak dále.

## <a name="address-slow-file-copy-issues"></a>Vyřešit problémy s pomalým kopírováním souborů

I v případě, že celková propustnost vyhodnocená pomocí předchozích kroků (iPERF/NTTTCP/atd.) byla dobrá, může docházet ke zpomalení souboru kopírování při použití Průzkumníka Windows nebo přetahování přes relaci RDP. K tomuto problému obvykle dochází v důsledku jednoho nebo obou následujících faktorů:

* Kopírování souborů aplikace, jako je například Průzkumník Windows nebo RDP, nepoužívají při kopírování souborů více vláken. Pro lepší výkon použijte vícevláknovou aplikaci kopírování souborů, například [RichCopy](https://technet.microsoft.com/magazine/2009.04.utilityspotlight.aspx) , ke kopírování souborů pomocí 16 nebo 32 vláken. Pokud chcete změnit číslo vlákna pro kopírování souborů v RichCopy, klikněte na **Akce**kopírovat  >  **Možnosti**kopírování  >  **souborů**.

   ![Pomalé problémy při kopírování souborů](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>

   > [!Note]
   > Ne všechny aplikace pracují stejně, a ne všechny aplikace nebo procesy využívají všechna vlákna. Pokud spustíte test, můžete vidět, že některá vlákna jsou prázdná a neposkytují přesné výsledky propustnosti.
   > Chcete-li ověřit výkon přenosu souborů aplikace, použijte více vláken tím, že zvýšíte počet vláken v průběhu úspěchu nebo je zmenšíte, aby bylo možné zjistit optimální propustnost aplikace nebo přenosu souborů.

* Nedostatečná rychlost čtení/zápisu disku virtuálního počítače Další informace najdete v tématu [řešení potíží s Azure Storage](../storage/common/storage-e2e-troubleshooting.md).

## <a name="on-premises-device-external-facing-interface"></a>Externí rozhraní pro místní zařízení

Uvedli jsme podsítě místních rozsahů, které chcete, aby Azure dosáhl přes VPN v bráně místní sítě. Současně definujte adresní prostor virtuální sítě v Azure na místní zařízení.

* **Brána založená na trasách**: zásady nebo selektor provozu pro sítě VPN založené na trasách jsou nakonfigurovány jako any (nebo zástupné karty).

* **Brána založená**na zásadách: sítě VPN založené na zásadách šifrují a směrují pakety prostřednictvím tunelů IPsec na základě kombinací předpon adres mezi vaší místní sítí a virtuální sítí Azure. Zásada (nebo selektor provozu) se většinou v konfiguraci sítě VPN definuje jako přístupový seznam.

* Připojení **UsePolicyBasedTrafficSelector** : ("UsePolicyBasedTrafficSelectors" pro $true v připojení nakonfiguruje bránu Azure VPN, aby se připojila k místní bráně firewall sítě VPN na základě zásad. Pokud povolíte PolicyBasedTrafficSelectors, musíte zajistit, aby vaše zařízení VPN odpovídalo selektorům přenosu definovaných pomocí všech kombinací místní sítě (brány místní sítě) k předponám virtuální sítě Azure a místo libovolných.

Nesprávná konfigurace může vést k častému odpojení v rámci tunelu, poklesu paketů, špatné propustnosti a latenci.

## <a name="check-latency"></a>Kontrolovat latenci

Latenci můžete kontrolovat pomocí následujících nástrojů:

* WinMTR
* TCPTraceroute
* `ping` a `psping` (tyto nástroje můžou poskytovat dobrý odhad času RTT, ale nedají se použít ve všech případech.)

![Kontrolovat latenci](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

Pokud si všimnete vysoké latence u libovolného směrování před přechodem na páteřní síť v síti, můžete pokračovat s dalším šetřením u svého poskytovatele internetových služeb.

Pokud se v rámci "msn.net" zaznamená velký špička latence, kontaktujte prosím podporu společnosti MS a požádejte o další šetření.

## <a name="next-steps"></a>Další kroky

Chcete-li získat další informace nebo nápovědu, podívejte se na následující odkaz:

* [Podpora společnosti Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
