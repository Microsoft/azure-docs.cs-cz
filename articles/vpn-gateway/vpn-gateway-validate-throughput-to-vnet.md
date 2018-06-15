---
title: Ověření propustnost sítě VPN do virtuální sítě Microsoft Azure | Microsoft Docs
description: Účelem tohoto dokumentu je pomoct uživatele ověřit propustnost sítě ze svých místních prostředků pro virtuální počítač Azure.
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: jasmc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2017
ms.author: radwiv;chadmat;genli
ms.openlocfilehash: cad7719eb077d7aca9c1db5741a5fe1e0ca910a2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32191311"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>Postup ověření propustnost sítě VPN do virtuální sítě

Umožňuje vytvořit bezpečné, mezi různými místy připojení mezi virtuální sítě v rámci Azure a místní připojení k bráně VPN infrastruktury IT.

Tento článek ukazuje, jak ověřit propustnost sítě z místních prostředků do Azure virtuálního počítače (VM). Obsahuje také pokyny k odstraňování problémů.

>[!NOTE]
>Tento článek je určený k diagnostice a řešení běžných problémů. Pokud se nemůžete tento problém vyřešit pomocí následujících informací [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="overview"></a>Přehled

Připojení brány sítě VPN zahrnuje následující součásti:

- Místní zařízení VPN (zobrazení seznamu [ověřit zařízení VPN)](vpn-gateway-about-vpn-devices.md#devicetable).
- Veřejný Internet
- Služba Azure VPN gateway
- Virtuální počítač Azure

Následující diagram znázorňuje logické připojení místní síti pro virtuální síť Azure prostřednictvím sítě VPN.

![Logické připojení pro zákazníka síťová MSFT síti pomocí sítě VPN](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Vypočítat maximální očekávaný vstup/výstup

1.  Určete požadavky na propustnost směrného plánu vaší aplikace.
2.  Určení vaší omezení propustnosti brány Azure VPN. Nápovědu najdete v části "Agregovaná propustnost podle typů SKU a síť VPN" [plánování a návrhu pro bránu VPN](vpn-gateway-plan-design.md).
3.  Určení [virtuálního počítače Azure propustnost pokyny](../virtual-machines/virtual-machines-windows-sizes.md) pro vaše velikost virtuálního počítače.
4.  Určuje šířku pásma vašeho poskytovatele služeb sítě Internet (ISP).
5.  Vypočítat očekávané propustnost - minimálně šířka pásma (virtuální počítač, brány, poskytovatele internetových služeb) * 0,8.

Pokud vaše počítané propustnost nesplňuje požadavky na propustnost směrného plánu vaší aplikace, je potřeba zvýšit šířku pásma k prostředku, který jste určili jako kritická místa. Ke změně velikosti služby Azure VPN Gateway, najdete v části [změna skladová položka brány](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku). Chcete-li změnit velikost virtuálního počítače, přečtěte si téma [změnit velikost virtuálního počítače](../virtual-machines/virtual-machines-windows-resize-vm.md). Pokud k těmto problémům očekávané šířky pásma Internetu, můžete také na bezpečném místě.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Ověření propustnost sítě pomocí nástroje pro sledování výkonu

Toto ověření je třeba provést v době mimo špičku jako sytost propustnost tunelové propojení VPN během testování není poskytnuta přesné výsledky.

Nástroj, který používáme pro tento test je iPerf, který má klient i server režimy a funguje v systému Windows a Linux. Je omezený na 3 GB/s pro virtuální počítače Windows.

Tento nástroj neprovádí žádné operace čtení a zápisu na disk. Vytvoří výhradně generovaný sám sebou TCP provoz z jednoho elementu end na druhý. Vygeneroval statistiky podle experimenty, které měří šířku pásma mezi klientem a serverem uzly k dispozici. Při testování mezi dvěma uzly, jeden funguje jako server a další jako klient. Po dokončení se tento test, doporučujeme zpětné rolí k testování obou nahrávání a stahování propustnost v obou uzlech.

### <a name="download-iperf"></a>Stažení nástroje iPerf
Stáhněte si [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). Podrobnosti najdete v tématu [iPerf dokumentaci](https://iperf.fr/iperf-doc.php).

 >[!NOTE]
 >Produkty třetích stran, které tento článek popisuje, pocházejí od společností, které jsou nezávislé na Microsoftu. Společnost Microsoft neposkytuje žádné záruky, předpokládanou ani jinou souvislosti s výkonem a spolehlivostí těchto produktů.
 >
 >

### <a name="run-iperf-iperf3exe"></a>Spuštění nástroje iPerf (iperf3.exe)
1. Povolte pravidlo NSG nebo seznamu ACL umožňuje provoz (pro veřejné IP adresy testování na virtuálním počítači Azure).

2. V obou uzlech povolte výjimku brány firewall pro port 5001.

    **Windows:** jako správce spusťte následující příkaz:

    ```CMD
    netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
    ```

    Pro odebrání pravidla při testování skončí, spusťte tento příkaz:

    ```CMD
    netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
    ```
    </br>
    **Azure Linux:** obrázků Azure Linux je projektovou brány firewall. Pokud je aplikace, která naslouchá na portu, provoz může prostřednictvím. Vlastní Image, které jsou zabezpečené může být nutné explicitně otevřené porty. Běžné brány firewall vrstvy operačního systému Linux zahrnují `iptables`, `ufw`, nebo `firewalld`.

3. Na uzlu serveru přejděte do adresáře, které je extrahován iperf3.exe. Potom spusťte iPerf v režimu serveru a nastavte ji naslouchat na portu 5001 jako následující příkazy:

     ```CMD
     cd c:\iperf-3.1.2-win65

     iperf3.exe -s -p 5001
     ```

4. Na uzlu klienta přejděte do adresáře, kde je nástroj iperf extrahovat a pak spusťte následující příkaz:

    ```CMD
    iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
    ```

    Klient způsobuje přenosy na portu 5001 k serveru pro 30 sekund. Příznak "-P" určující používáme 32 současných připojení k uzlu serveru.

    Na následující obrazovce se zobrazí výstup z tohoto příkladu:

    ![Výstup](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

5. (VOLITELNÉ) Pokud chcete zachovat testování výsledky, spusťte tento příkaz:

    ```CMD
    iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
    ```

6. Po dokončení předchozích kroků provést stejný postup s rolemi vrátit zpět, tak, aby uzel serveru bude nyní klienta a naopak.

## <a name="address-slow-file-copy-issues"></a>Řeší problémy kopie pomalé souboru
Může dojít k pomalé souboru kopírování při pomocí Průzkumníka Windows nebo přetahování a vkládání přes relaci protokolu RDP. Tento problém je obvykle kvůli jedno nebo obě následující faktory:

- Při kopírování souborů aplikace kopírování souboru, jako je například Průzkumník Windows a protokolu RDP, nepoužívejte více vláken. Pro lepší výkon použijte aplikaci kopie Vícevláknová souborového [Richcopy](https://technet.microsoft.com/magazine/2009.04.utilityspotlight.aspx) kopírovat soubory pomocí 16 nebo 32 vláken. Chcete-li změnit počet vláken pro kopírování souborů v Richcopy, klikněte na tlačítko **akce** > **možnosti kopírování** > **kopírování souboru**.<br><br>
![Pomalé souboru kopie problémy](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>
- Nedostatečná rychlost čtení/zápis disku virtuálního počítače. Další informace najdete v tématu [řešení potíží s Azure Storage](../storage/common/storage-e2e-troubleshooting.md).

## <a name="on-premises-device-external-facing-interface"></a>Místní zařízení externí přístupných rozhraní
Pokud je součástí místní zařízení VPN internetového IP adresu [místní sítě](vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) definice v Azure, můžete zaznamenat neschopnost otevře místní okno, odpojení sítě VPN, ojediněle, problémy s výkonem.

## <a name="checking-latency"></a>Kontrola latence
Tracert pro trasování k Microsoft Azure hraniční zařízení použijte k určení, zda neexistují žádné zpoždění vyšší než 100 ms mezi segmenty směrování.

Z místní sítě, spusťte *tracert* do virtuální IP adresu brány Azure nebo virtuálních počítačů. Jakmile se zobrazí pouze * vrátí, víte, bylo dosaženo Azure okraj. Když se názvy DNS, které zahrnují "MSN" vrátil, víte, že jste dosáhli páteřní společnosti Microsoft.<br><br>
![Kontrola latence](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

## <a name="next-steps"></a>Další postup
Další informace a nápovědu najdete na následujících odkazech:

- [Optimalizovat propustnost sítě pro virtuální počítače Azure](../virtual-network/virtual-network-optimize-network-bandwidth.md)
- [Podporu společnosti Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
