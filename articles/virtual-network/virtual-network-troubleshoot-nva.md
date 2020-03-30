---
title: Řešení problémů se síťovými virtuálními zařízeními v Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak řešit problémy se síťovými virtuálními zařízeními v Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2018
ms.author: genli
ms.openlocfilehash: b998043bc7d896989590ac21db5f309a81cc02bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71056839"
---
# <a name="network-virtual-appliance-issues-in-azure"></a>Problémy se síťovými virtuálními zařízeními v Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Při používání síťového virtuálního zařízení (NVA) třetí strany v Microsoft Azure může docházet k problémům s připojením virtuálního počítače nebo síti VPN a chybám. Tento článek obsahuje základní kroky, které vám pomohou ověřit základní požadavky platformy Azure pro konfigurace nva.

Technickou podporu pro národní virtuální virtuální počítače třetích stran a jejich integraci s platformou Azure poskytuje dodavatel nva.

> [!NOTE]
> Pokud máte problém s připojením nebo směrováním, který zahrnuje nva, měli byste [kontaktovat dodavatele nva](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) přímo.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>Kontrolní seznam pro řešení potíží s dodavatelem NVA

- Aktualizace softwaru pro software Virtuálního počítače NVA
- Nastavení a funkce účtu služby
- Uživatelem definované trasy (UDR) v podsítích virtuálních sítí, které směrují provoz do síťového virtuálního zařízení
- UDR v podsítích virtuálních sítí, které řídí provoz z síťového virtuálního zařízení
- Směrování tabulek a pravidel v rámci nva (například z NIC1 na NIC2)
- Trasování na síťových počítačích NVA za účelem ověření příjmu a odesílání síťového provozu
- Při použití standardní skladové položky a veřejné IP adresy, musí být vytvořen soubor služeb pro sítě sítě a explicitní pravidlo povolit provoz, který má být směrován do sítě pro sdílení vlivů na viceprezidenta.

## <a name="basic-troubleshooting-steps"></a>Základní kroky řešení potíží

- Zkontrolujte základní konfiguraci
- Kontrola výkonu NVA
- Řešení potíží s pokročilou sítí

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Kontrola minimálních požadavků na konfiguraci pro virtuální virtuální virtuální užitky v Azure

Každá služba nva má základní požadavky na konfiguraci, aby správně fungovala v Azure. Následující část obsahuje postup ověření těchto základních konfigurací. Další informace [získáte od dodavatele nva](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

**Zkontrolujte, zda je v zařízení NVA povoleno přesměrování IP adres.**

Použití webu Azure Portal

1. Vyhledejte prostředek síťového virtuálního zařízení na [webu Azure Portal](https://portal.azure.com), vyberte Síť a pak vyberte síťové rozhraní.
2. Na stránce Síťové rozhraní vyberte konfiguraci IP adresy.
3. Ujistěte se, že je povoleno předávání IP adres.

Použití prostředí PowerShell

1. Otevřete PowerShell a přihlaste se ke svému účtu Azure.
2. Spusťte následující příkaz (nahraďte hodnoty v závorkách informacemi):

   ```powershell
   Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   ```

3. Zkontrolujte vlastnost **EnableIPForwarding.**
4. Pokud předávání IP není povoleno, povolte následující příkazy:

   ```powershell
   $nic2 = Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   $nic2.EnableIPForwarding = 1
   Set-AzNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

**Kontrola nsg při použití standardní sku pubilc IP** Při použití standardní skladové položky a veřejné IP adresy, musí být vytvořen soubor služeb sítě sítě a explicitní pravidlo povolit přenos do nvA.

**Zkontrolujte, zda lze provoz směrovat do sítě Pro**

1. Na [webu Azure Portal](https://portal.azure.com)otevřete Sledování **sítě**a vyberte Další **směrování**.
2. Zadejte virtuální ho virtuálního počítače, který je nakonfigurovaný pro přesměrování přenosů na službu NVA, a cílovou adresu IP, na které chcete zobrazit další směrování. 
3. Pokud není služba NVA uvedena jako **další směrování**, zkontrolujte a aktualizujte tabulky tras Azure.

**Zkontrolujte, zda provoz může dosáhnout NVA**

1. Na [webu Azure Portal](https://portal.azure.com)otevřete Sledování **sítě**a pak vyberte **ověření toku IP**. 
2. Zadejte virtuální ho virtuálního uživatele a IP adresu síťového virtuálního zařízení a zkontrolujte, zda je provoz blokován všemi skupinami zabezpečení sítě (NSG).
3. Pokud existuje pravidlo skupiny zabezpečení sítě, které blokuje provoz, vyhledejte skupinu zabezpečení sítě v **účinných** pravidlech zabezpečení a aktualizujte jej tak, aby přenos mohl projít. Pak znovu spusťte **ověření toku IP** a pomocí **řešení potíží s připojením** otestujte komunikaci TCP z virtuálního počítače na interní nebo externí IP adresu.

**Zkontrolujte, jestli nva a virtuální chody naslouchají očekávanému provozu.**

1. Připojte se k nva pomocí RDP nebo SSH a spusťte následující příkaz:

    Ve Windows:

        netstat -an

    Pro Linux:

        netstat -an | grep -i listen
2. Pokud nevidíte port TCP, který používá software NVA, který je uveden ve výsledcích, musíte nakonfigurovat aplikaci na nva a virtuálním počítači, aby naslouchala a reagovala na provoz, který se k těmto portům dostane. [Podle potřeby požádejte o pomoc dodavatele nva](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

## <a name="check-nva-performance"></a>Kontrola výkonu NVA

### <a name="validate-vm-cpu"></a>Ověření procesoru virtuálního počítače

Pokud se využití procesoru přiblíží 100 procentům, může dojít k problémům, které mají vliv na pokles síťových paketů. Váš virtuální počítač hlásí průměrný procesor pro určitý časový rozsah na webu Azure Portal. Během špičky procesoru zjistěte, který proces na virtuálním počítači hosta způsobuje vysoký procesor, a pokud je to možné, zmírňte ho. Možná budete muset změnit velikost virtuálního počítače na větší velikost skladové položky nebo pro škálovací sadu virtuálních strojů zvýšit počet instancí nebo nastavit automatické škálování na využití procesoru. V případě obou těchto problémů [požádejte o pomoc dodavatele nva](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

### <a name="validate-vm-network-statistics"></a>Ověřit statistiku sítě virtuálních montovny

Pokud síť virtuálních zařízení používá špičky nebo zobrazuje období vysoké využití, budete muset také zvětšit velikost skladové položky virtuálního počítače získat vyšší možnosti propustnost. Virtuální ho můžete znovu nasadit také tak, že máte povolenou funkci Accelerated Networking. Chcete-li ověřit, zda síťové virtuální zařízení podporuje funkci Akcelerované sítě, obraťte se podle potřeby [na dodavatele síťového virtuálního zařízení](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

## <a name="advanced-network-administrator-troubleshooting"></a>Řešení potíží s pokročilým správcem sítě

### <a name="capture-network-trace"></a>Zachytit trasování sítě
Zachyťte souběžné trasování sítě na zdrojovém virtuálním počítači, síťovém virtuálním počítači a cílovém virtuálním počítači při spuštění **[příkazu PsPing](https://docs.microsoft.com/sysinternals/downloads/psping)** nebo **Nmap**a pak trasování zastavte.

1. Chcete-li zachytit souběžné trasování v síti, spusťte následující příkaz:

   **Pro Windows**

   netsh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection NetSh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection NetSh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection Net

   **Pro Linux**

   sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap

2. Použijte **PsPing** nebo **Nmap** ze zdrojového virtuálního virtuálního `PsPing 10.0.0.4:80` `Nmap -p 80 10.0.0.4`virtuálního provozu do cílového virtuálního virtuálního provozu (například: nebo ).
3. Otevřete trasování sítě z cílového virtuálního aplikace pomocí [programu Sledování sítě](https://www.microsoft.com/download/details.aspx?id=4865) nebo tcpdump. Použití filtru zobrazení pro IP adresu zdrojového virtuálního počítače, ze `IPv4.address==10.0.0.4 (Windows netmon)` které `tcpdump -nn -r vmtrace.cap src or dst host 10.0.0.4` jste **spustili PsPing** nebo **Nmap,** například nebo (Linux).

### <a name="analyze-traces"></a>Analyzovat trasování

Pokud nevidíte pakety příchozí do trasování back-endového virtuálního soudu, je pravděpodobné, že narušení nsg nebo UDR nebo směrovací tabulky NVA jsou nesprávné.

Pokud se příchozí balíčky zobrazují, ale bezu odpovědi, pravděpodobně budete muset řešit potíže s bránou firewall nebo aplikací virtuálního počítače. V případě obou těchto problémů [požádejte o pomoc dodavatele nva](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)podle potřeby .