---
title: Řešení potíží s síťové virtuální zařízení v Azure | Dokumentace Microsoftu
description: Zjistěte, jak řešit problémy síťové virtuální zařízení v Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2018
ms.author: genli
ms.openlocfilehash: 13cec39278577a818ef43f1215fd2e6653f15ed2
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52678436"
---
#  <a name="network-virtual-appliance-issues-in-azure"></a>Problémy se síťovým virtuálním zařízením v Azure

Může docházet virtuálního počítače nebo problémy s připojením VPN a chyb při použití třetí strany síťové virtuální zařízení (NVA) ve službě Microsoft Azure. Tento článek popisuje základní kroky pro ověření základní požadavky na platformu Azure pro konfigurace síťového virtuálního zařízení.

Technická podpora pro dodavatelů síťových virtuálních zařízení a jejich integrace s platformou Azure je poskytován dodavatelem síťového virtuálního zařízení. 

> [!NOTE]
> Pokud máte připojení nebo směrováním, která zahrnuje síťové virtuální zařízení, měli byste [obraťte se na dodavatele síťové virtuální zařízení](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) přímo.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>Kontrolní seznam pro řešení potíží s dodavatelem síťového virtuálního zařízení

- Aktualizace softwaru pro virtuální počítač s NVA softwaru
- Nastavení účtu služby a funkce
- Trasy definované uživatelem (udr) v podsítích virtuální sítě, které směrovat provoz do síťových virtuálních zařízení
- Trasy definované uživatelem v podsítích virtuální sítě, které směrování provozu od síťového virtuálního zařízení
- Směrovací tabulky a pravidel v rámci síťové virtuální zařízení (např. z NIC1 k NIC2)
- Trasování na síťových adaptérů síťové virtuální zařízení chcete ověřit přijímání a odesílání síťového provozu
- Při použití standardní SKU a veřejná IP adresa musí být NSG vytvořené a explicitní pravidla umožňovat provoz směrovat do síťového virtuálního zařízení.

## <a name="basic-troubleshooting-steps"></a>Základní postup řešení potíží

- Zkontrolovat základní konfigurace
- Kontrola výkonu síťového virtuálního zařízení
- Řešení potíží s rozšířeného sítě

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Zkontrolujte požadavky na minimální konfiguraci pro síťová virtuální zařízení v Azure

Každé síťové virtuální zařízení má požadavky na konfiguraci základní správné fungování v Azure. Následující část obsahuje kroky k ověření tyto základní konfigurace. Další informace najdete [obraťte se na dodavatele síťové virtuální zařízení](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

**Zkontrolujte, jestli je povolené předávání IP na síťové virtuální zařízení**

Použití webu Azure Portal

1.  Vyhledejte prostředek síťového virtuálního zařízení v [webu Azure portal](https://portal.azure.com), vyberte sítě a pak vyberte síťové rozhraní.
2.  Na stránce rozhraní sítě vyberte konfigurace IP adresy.
3.  Ujistěte se, že je povolené předávání IP.

Použití prostředí PowerShell

1. Otevřete PowerShell a pak se přihlaste ke svému účtu Azure.
2. Spuštěním následujícího příkazu (s informacemi o nahraďte hodnoty v závorkách):

        Get-AzureRmNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>  

3. Zkontrolujte, **EnableIPForwarding** vlastnost.
 
4. Pokud není povolené předávání IP, spusťte následující příkazy, aby je:

          $nic2 = Get-AzureRmNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
          $nic2.EnableIPForwarding = 1
          Set-AzureRmNetworkInterface -NetworkInterface $nic2
          Execute: $nic2 #and check for an expected output:
          EnableIPForwarding   : True
          NetworkSecurityGroup : null

**Zkontrolujte skupiny zabezpečení sítě při použití standardní SKU Pubilc IP** při použití standardní SKU a veřejná IP adresa musí být vytvořené NSG a explicitní pravidla umožňují provoz na síťové virtuální zařízení.

**Zkontrolujte, zda je možné směrovat provoz do síťového virtuálního zařízení**

1. Na [webu Azure portal](https://portal.azure.com), otevřete **Network Watcher**vyberte **dalšího segmentu směrování**.
2. Zadejte virtuální počítač, který je nakonfigurovaný přesměrovat provoz na síťové virtuální zařízení a cílová IP adresa, na který chcete zobrazit další segment směrování. 
3. Pokud síťové virtuální zařízení není nastavena **dalšího segmentu směrování**, zkontrolujte a aktualizujte směrovací tabulky Azure.

**Zkontrolujte, jestli provoz se může spojit síťové virtuální zařízení**

1.  V [webu Azure portal](https://portal.azure.com), otevřete **Network Watcher**a pak vyberte **IP tok ověřit**. 
2.  Zadejte virtuální počítač a IP adresu síťové virtuální zařízení a potom zkontrolujte, jestli provoz blokuje všechny skupiny zabezpečení sítě (NSG).
3.  Pokud je pravidlo NSG, které blokuje provoz, vyhledejte NSG v **efektivní zabezpečení** pravidla a pak aktualizujte ji, aby povolit průchod přenosů. Potom spusťte **IP tok ověřit** znovu a použijte **řešení potíží s připojením** otestovat komunikaci TCP z virtuálního počítače na interní nebo externí IP adresu.

**Zkontrolujte, zda síťové virtuální zařízení a virtuální počítače naslouchají očekávaném provozu**

1.  Připojit k síťové virtuální zařízení pomocí protokolu RDP nebo SSH a pak spusťte následující příkaz:

    Ve Windows:

        netstat -an

    Pro Linux:

        netstat -an | grep -i listen
2.  Pokud nevidíte v portu TCP, který je používán síťové virtuální zařízení software, který je uvedený ve výsledcích musíte nakonfigurovat aplikaci na síťové virtuální zařízení a virtuální počítač přijímat a reagovat na provoz, kterou půjde používat tyto porty. [Požádejte o pomoc dodavatele síťové virtuální zařízení podle potřeby](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

## <a name="check-nva-performance"></a>Kontrola výkonu síťového virtuálního zařízení

### <a name="validate-vm-cpu"></a>Ověření procesoru virtuálního počítače

Pokud využití procesoru blíží 100 % jeho obsahu, může docházet potíže, které by ovlivnily drops síťových paketů. Sestavy virtuálního počítače průměrné využití procesoru pro konkrétní časové období na webu Azure Portal. Během špička využití procesoru prozkoumejte proces, který na hostovaný virtuální počítač je příčinou vysoké využití procesoru a zmírnit, pokud je to možné. Budete také muset změnit velikost virtuálního počítače na větší velikost SKU nebo pro škálovací sadu virtuálních počítačů, zvýšení počtu instancí nebo nastavení automatického škálování na využití procesoru. Pro žádnou z těchto problémů [požádat o pomoc dodavatele síťové virtuální zařízení](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines), podle potřeby.

### <a name="validate-vm-network-statistics"></a>Ověření statistiky síť virtuálních počítačů 

Pokud používáte síť virtuálních počítačů špičky nebo zobrazí období vysokého využití, že možná budete také muset zvýšit velikost SKU virtuálního počítače na získání vyšší propustnost možnosti. Můžete také znovu nasadit virtuální počítač tak, že Akcelerovanými síťovými službami povolena. Chcete-li ověřit, zda síťové virtuální zařízení podporuje akcelerované síťové funkce [požádat o pomoc dodavatele síťové virtuální zařízení](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines), podle potřeby.

## <a name="advanced-network-administrator-troubleshooting"></a>Řešení potíží s správce rozšířeného sítě

### <a name="capture-network-trace"></a>Zachycení síťových trasování
Zaznamenání současné trasování sítě do zdrojových virtuálních počítačů, síťové virtuální zařízení a cílového virtuálního počítače při spuštění **[PsPing](https://docs.microsoft.com/sysinternals/downloads/psping)** nebo **Nmap**a potom toto trasování zastavte.

1. K zachycení současné trasování sítě, spusťte následující příkaz:

    Ve Windows:

        netsh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection

    Pro Linux:

        sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap

2. Použití **PsPing** nebo **Nmap** ze zdrojového virtuálního počítače na cílovém virtuálním počítači (například: `PsPing 10.0.0.4:80` nebo `Nmap -p 80 10.0.0.4`).

3. Otevřete trasování sítě z cílového virtuálního počítače s použitím [sledování sítě](https://www.microsoft.com/download/details.aspx?id=4865) nebo tcpdump. Použijte filtr zobrazení pro IP adresu zdrojového virtuálního počítače jste spustili **PsPing** nebo **Nmap** , jako například `IPv4.address==10.0.0.4 (Windows netmon)` nebo `tcpdump -nn -r vmtrace.cap src or dst host 10.0.0.4` (Linux).

### <a name="analyze-traces"></a>Analyzovat trasování

Pokud nevidíte příchozí pakety pro trasování virtuálního počítače back-endu, pravděpodobně je skupina zabezpečení sítě nebo UDR překáží nebo směrovacích tabulek síťové virtuální zařízení jsou nesprávná.

Pokud se příchozí balíčky zobrazují, ale bezu odpovědi, pravděpodobně budete muset řešit potíže s bránou firewall nebo aplikací virtuálního počítače. Pro žádnou z těchto problémů [požádat o pomoc dodavatele síťové virtuální zařízení podle potřeby](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

