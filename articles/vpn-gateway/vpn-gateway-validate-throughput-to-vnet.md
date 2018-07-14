---
title: Ověření propustnosti sítě VPN k virtuální síti Microsoft Azure | Dokumentace Microsoftu
description: Účelem tohoto dokumentu je umožňující uživateli ověření propustnosti sítě ze svých místních prostředků do virtuálního počítače Azure.
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
ms.date: 06/15/2018
ms.author: radwiv;chadmat;genli
ms.openlocfilehash: 7e6b3e7496c4a063156ff3b8feae1f5096efe55f
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035614"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>Ověření propustnosti sítě VPN do virtuální sítě

Připojení k síti VPN gateway umožňuje bezpečně spojovat různá místa mezi lokalitami, připojení mezi vaší virtuální sítě v rámci Azure a vaší místní infrastruktuře IT.

Tento článek ukazuje, jak ověření propustnosti sítě z místních prostředků do Azure virtuální počítač (VM). Také poskytuje pokyny k odstraňování problémů.

>[!NOTE]
>Tento článek je určený při diagnostice a řešení běžných problémů. Pokud nemůžete problém vyřešit pomocí následujících informací, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="overview"></a>Přehled

Připojení brány VPN typu zahrnuje následující součásti:

- Místní zařízení VPN (Úplný seznam [ověřených zařízení VPN)](vpn-gateway-about-vpn-devices.md#devicetable).
- Veřejný Internet
- Služba Azure VPN gateway
- Virtuální počítač Azure

Následující diagram znázorňuje logické připojení místní sítě ke službě Azure virtual network přes VPN.

![Logické připojení ze sítě MSFT na úrovni sítě prostřednictvím sítě VPN](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Vypočítat maximální očekávané příchozí/odchozí

1.  Určete požadavky na propustnost aplikace směrného plánu.
2.  Určení vaší omezení propustnosti brány Azure VPN. Nápovědu najdete v části "Agregovaná propustnost podle SKU a typu sítě VPN" [plánování a návrh pro VPN Gateway](vpn-gateway-plan-design.md).
3.  Určit, [propustnosti virtuálního počítače Azure](../virtual-machines/virtual-machines-windows-sizes.md) pro velikosti virtuálních počítačů.
4.  Určete šířku pásma vašeho poskytovatele služeb Internetu (ISP).
5.  Vypočítat očekávané propustnosti - nejmenší šířka pásma (virtuální počítač brány, poskytovatele internetových služeb) * 0,8.

Pokud počítané propustnost nesplňuje požadavky na propustnost směrného plánu vaší aplikace, potřebujete zvětšit šířku pásma prostředku, který je označen jako problémové místo. Změna velikosti službou Azure VPN Gateway, naleznete v tématu [změna skladové položky brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Pokud chcete změnit velikost virtuálního počítače, přečtěte si téma [změnit velikost virtuálního počítače](../virtual-machines/virtual-machines-windows-resize-vm.md). Pokud nejsou očekávané internetovou šířku pásma, můžete také kontaktovat svého poskytovatele internetových služeb.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Ověření propustnosti sítě s použitím nástroje pro měření výkonu

Toto ověření se provádí během hodin mimo špičku, sytost propustnost tunelového připojení VPN při testování vám není udělena přesné výsledky.

Tento nástroj, které používáme pro tento test je iPerf, které funguje ve Windows a Linuxu a má klient i server režimy. Je omezená na 3 GB/s pro Windows virtuální počítače.

Tento nástroj neprovádí žádné operace čtení a zápis na disk. Vytvoří výhradně vygenerovaný sám sebou provoz TCP konce na druhý. Často generovaná statistiky podle služby experimentování ve službě, která měří šířka pásma dostupná mezi klientem a serverem uzly. Při testování mezi dvěma uzly, jeden funguje jako server a další jako klient. Po dokončení tohoto testu, doporučujeme, že jste vrátili zpět role, které chcete otestovat obou nahrávání a stahování propustnosti na oba uzly.

### <a name="download-iperf"></a>Stáhněte si iPerf
Stáhněte si [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). Podrobnosti najdete v tématu [iPerf dokumentaci](https://iperf.fr/iperf-doc.php).

 >[!NOTE]
 >Produkty třetích stran, které tento článek popisuje, pocházejí od společností, které jsou nezávislé na společnosti Microsoft. Společnost Microsoft neposkytuje žádnou záruku, předpokládanou ani souvislosti s výkonem a spolehlivostí těchto produktů.
 >
 >

### <a name="run-iperf-iperf3exe"></a>Spustit iPerf (iperf3.exe)
1. Povolte pravidlo seznamu ACL skupiny zabezpečení sítě povoluje provoz (pro testování na virtuálním počítači Azure veřejnou IP adresu).

2. Na obou uzlech povolte výjimku brány firewall pro port 5001.

    **Windows:** jako správce spusťte následující příkaz:

    ```CMD
    netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
    ```

    Při testování odebrat pravidlo dokončení, spusťte tento příkaz:

    ```CMD
    netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
    ```
     
    **Azure s Linuxem:** Azure s Linuxem Image obsahují povolující brány firewall. Pokud je aplikace, která naslouchá na portu, provoz je povolený průchod přes. Vlastních imagí, které jsou zabezpečené možná bude nutné explicitně otevřené porty. Běžné Linux OS – vrstva brány firewall zahrnout `iptables`, `ufw`, nebo `firewalld`.

3. Na uzlu serveru přejděte do adresáře, kde je extrahován iperf3.exe. Potom spusťte iPerf v režimu serveru a nastavte ho tak, aby naslouchala na portu 5001 jako následující příkazy:

     ```CMD
     cd c:\iperf-3.1.2-win65

     iperf3.exe -s -p 5001
     ```

4. Na uzlu klienta přejděte do adresáře, kde je nástroj iperf extrahovat a pak spusťte následující příkaz:

    ```CMD
    iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
    ```

    Klient je 30 sekund nevyvolá provoz na portu 5001 na serveru. Příznak "-P", která označuje, že 32 současných připojení k uzlu serveru, který se používá.

    Na následujícím obrázku se zobrazuje výstup z tohoto příkladu:

    ![Výstup](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

5. (VOLITELNÉ) Pokud chcete zachovat výsledky testování, spusťte tento příkaz:

    ```CMD
    iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
    ```

6. Po dokončení předchozích kroků, proveďte stejné kroky s rolemi vrátit zpět, tak, aby uzel serveru teď budou mít klienta a naopak.

## <a name="address-slow-file-copy-issues"></a>Řešení potíží s kopírování souboru pomalé
Může dojít k pomalé souboru kopírování při pomocí Průzkumníka Windows nebo přetažení přes relaci protokolu RDP. Tento problém je obvykle kvůli jedné nebo obou následujících faktorů:

- Aplikace kopírování souboru, jako je Průzkumník Windows a protokolu RDP, nepoužívejte více vláken při kopírování souborů. Pro lepší výkon, pomocí kopie aplikace Vícevláknová souborů [Richcopy](https://technet.microsoft.com/magazine/2009.04.utilityspotlight.aspx) kopírování souborů pomocí 16 a 32 vláken. Chcete-li změnit číslo vlákna pro kopírování souborů v Richcopy, klikněte na tlačítko **akce** > **možnosti kopírování** > **kopírování souboru**.<br><br>
![Pomalé souboru kopie problémy](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>
- Rychlost čtení/zápis disku dostatek virtuálních počítačů. Další informace najdete v tématu [řešení potíží s Azure Storage](../storage/common/storage-e2e-troubleshooting.md).

## <a name="on-premises-device-external-facing-interface"></a>Místní zařízení externí protilehlé rozhraní
Pokud je součástí místního zařízení VPN internetové IP adresy [místní sítě](vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) definice v Azure, můžete se setkat, neschopnost následně odpojení sítě VPN, Občasná nebo problémy s výkonem.

## <a name="checking-latency"></a>Kontrola latence
Určí, jestli je zpoždění, nad 100 ms mezi segmenty směrování pomocí tracert trasovat zařízení Microsoft Azure Edge.

Z místní sítě, spusťte *tracert* do virtuální IP adresy brány Azure nebo virtuální počítač. Jakmile se zobrazí pouze * vrátila, znáte, jste dosáhli Azure edge. Když se zobrazí názvy DNS, které zahrnují "MSN" vrátila, víte, že jste dosáhli páteřní infrastrukturu Microsoftu.<br><br>
![Kontrola latence](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

## <a name="next-steps"></a>Další postup
Další informace a nápovědu najdete na následujících odkazech:

- [Optimalizace propustnosti sítě pro virtuální počítače Azure](../virtual-network/virtual-network-optimize-network-bandwidth.md)
- [Podpora Microsoftu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
