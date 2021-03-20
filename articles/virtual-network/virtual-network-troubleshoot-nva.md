---
title: Řešení potíží se síťovými virtuálními zařízení v Azure | Microsoft Docs
description: Řešení problémů se síťovým virtuálním zařízením (síťové virtuální zařízení) v Azure a ověření základních požadavků na platformu Azure pro konfigurace síťové virtuální zařízení.
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
ms.openlocfilehash: fe4c17b74cd786d03bd19257dea190a21ecaa9f5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99095637"
---
# <a name="network-virtual-appliance-issues-in-azure"></a>Problémy se síťovým virtuálním zařízením v Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Při použití síťového virtuálního zařízení (síťové virtuální zařízení) jiného výrobce v Microsoft Azure se může vyskytnout chyba a problémy s připojením k virtuálnímu počítači nebo k síti VPN. Tento článek popisuje základní kroky, které vám pomůžou ověřit základní požadavky na platformu Azure pro konfigurace síťové virtuální zařízení.

Technickou podporu pro síťová virtuální zařízení třetích stran a jejich integraci s platformou Azure zajišťuje dodavatel síťové virtuální zařízení.

> [!NOTE]
> Pokud máte potíže s připojením nebo směrováním, které zahrnuje síťové virtuální zařízení, měli byste [se obrátit na dodavatele síťové virtuální zařízení](https://mskb.pkisolutions.com/kb/2984655) přímo.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>Kontrolní seznam pro řešení potíží s dodavatelem síťové virtuální zařízení

- Aktualizace softwaru pro software síťové virtuální zařízení VM
- Nastavení a funkce účtu služby
- Trasy definované uživatelem (udr) v podsítích virtuální sítě, které směrují provoz do síťové virtuální zařízení
- Udr v podsítích virtuální sítě, které směrují provoz z síťové virtuální zařízení
- Směrování tabulek a pravidel v rámci síťové virtuální zařízení (například z NIC1 na NIC2)
- Trasování na síťových kartách síťové virtuální zařízení pro ověření příjmu a odesílání síťového provozu
- Pokud používáte standardní SKU a veřejné IP adresy, musí se vytvořit NSG a explicitní pravidlo, které umožní směrování provozu do síťové virtuální zařízení.

## <a name="basic-troubleshooting-steps"></a>Základní kroky pro řešení potíží

- Zkontroluje základní konfiguraci.
- Kontrolovat výkon síťové virtuální zařízení
- Pokročilé řešení potíží se sítí

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Ověřit minimální požadavky na konfiguraci pro síťová virtuální zařízení v Azure

Každý síťové virtuální zařízení má základní požadavky na konfiguraci pro správné fungování v Azure. Následující část uvádí postup ověření těchto základních konfigurací. Další informace získáte [od dodavatele síťové virtuální zařízení](https://mskb.pkisolutions.com/kb/2984655).

**Ověřte, jestli je v síťové virtuální zařízení povolené předávání IP.**

Použití webu Azure Portal

1. Vyhledejte prostředek síťové virtuální zařízení v [Azure Portal](https://portal.azure.com), vyberte sítě a pak vyberte síťové rozhraní.
2. Na stránce síťové rozhraní vyberte konfigurace protokolu IP.
3. Ujistěte se, že je povoleno předávání IP.

Použití prostředí PowerShell

1. Otevřete PowerShell a pak se přihlaste ke svému účtu Azure.
2. Spusťte následující příkaz (nahraďte hodnoty v závorkách vašimi informacemi):

   ```powershell
   Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   ```

3. Ověřte vlastnost **EnableIPForwarding** .
4. Pokud nepovolíte předávání IP, spusťte následující příkazy:

   ```powershell
   $nic2 = Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   $nic2.EnableIPForwarding = 1
   Set-AzNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

**Kontrolovat NSG při použití Pubilc IP adresy Standard SKU** Pokud používáte standardní SKU a veřejné IP adresy, musí se vytvořit NSG a explicitní pravidlo pro povolení přenosu do síťové virtuální zařízení.

**Ověřte, jestli je možné směrovat provoz do síťové virtuální ZAŘÍZENÍu.**

1. V [Azure Portal](https://portal.azure.com)otevřete **Network Watcher** vyberte **Další segment směrování**.
2. Zadejte virtuální počítač, který je nakonfigurovaný pro přesměrování provozu do síťové virtuální zařízení, a cílovou IP adresu, na které se má zobrazit další segment směrování. 
3. Pokud síťové virtuální zařízení není uveden jako **Další segment směrování**, ověřte a aktualizujte tabulky směrování Azure.

**Ověřte, jestli přenosy můžou dosáhnout pro síťové virtuální zařízení.**

1. V [Azure Portal](https://portal.azure.com)otevřete **Network Watcher** a vyberte možnost **ověření toku IP**. 
2. Zadejte virtuální počítač a IP adresu síťové virtuální zařízení a potom zkontrolujte, jestli jsou přenosy blokované všemi skupinami zabezpečení sítě (NSG).
3. Pokud existuje pravidlo NSG, které blokuje provoz, najděte NSG v **platných pravidlech zabezpečení** a pak ho aktualizujte, aby bylo možné předávání provozu. Pak znovu spusťte **kontrolu protokolu IP** a pomocí **řešení potíží s připojením** otestujte komunikaci TCP z virtuálního počítače na svou interní nebo externí IP adresu.

**Ověřte, jestli síťové virtuální zařízení a virtuální počítače naslouchají očekávanému provozu.**

1. Připojte se k síťové virtuální zařízení pomocí protokolu RDP nebo SSH a potom spusťte následující příkaz:

    Ve Windows:

    ```console
   netstat -an
    ```

    V Linuxu:

    ```console
   netstat -an | grep -i listen
    ```
2. Pokud nevidíte port TCP, který je používán softwarem síťové virtuální zařízení, který je uvedený ve výsledcích, musíte nakonfigurovat aplikaci na síťové virtuální zařízení a virtuálním počítači, aby naslouchaly a reagovaly na provoz, který dosahuje těchto portů. Pokud potřebujete [pomoc, obraťte se na dodavatele síťové virtuální zařízení](https://mskb.pkisolutions.com/kb/2984655).

## <a name="check-nva-performance"></a>Kontrolovat výkon síťové virtuální zařízení

### <a name="validate-vm-cpu"></a>Ověřit procesor virtuálního počítače

Pokud se využití CPU blíží 100%, může docházet k problémům, které mají vliv na zahození síťového paketu. Váš virtuální počítač hlásí průměrný procesor za určité časové období v Azure Portal. Během špičky procesoru zkontrolujte, který proces na virtuálním počítači hosta způsobuje vysoký procesor, a pokud je to možné, omezte jeho dopad. Možná budete muset změnit velikost virtuálního počítače na větší velikost SKU nebo pro sadu škálování virtuálního počítače zvýšit počet instancí nebo nastavit automatické škálování podle využití procesoru. U každého z těchto problémů [požádejte o pomoc dodavatele síťové virtuální zařízení](https://mskb.pkisolutions.com/kb/2984655), jak je to potřeba.

### <a name="validate-vm-network-statistics"></a>Ověřit statistiku sítě virtuálních počítačů

Pokud síť virtuálních počítačů využívá špičky nebo zobrazuje období vysokého využití, může být také nutné zvýšit velikost SKU virtuálního počítače, aby se získaly možnosti vyšší propustnosti. Virtuální počítač můžete také znovu nasadit pomocí aktivovaného urychlení sítě. Pokud chcete ověřit, jestli síťové virtuální zařízení podporuje funkci urychlené sítě, [požádejte o pomoc dodavatele síťové virtuální zařízení](https://mskb.pkisolutions.com/kb/2984655), jak je potřeba.

## <a name="advanced-network-administrator-troubleshooting"></a>Pokročilý poradce při potížích správce sítě

### <a name="capture-network-trace"></a>Zachytit trasování sítě
Zaznamenejte si souběžnou síťovou trasu na zdrojovém virtuálním počítači, síťové virtuální zařízení a cílový virtuální počítač, zatímco spouštíte **[PsPing](/sysinternals/downloads/psping)** nebo **nmap**, a pak trasování zastavte.

1. Pokud chcete zachytit síťové trasování, spusťte následující příkaz:

   **Pro Windows**

   netsh Trace Start capture = yes tracefile = c:\ server_IP. ETL scénář = NetConnection

   **Pro Linux**

   sudo tcpdump-S0-i eth0-X-w vmtrace. Cap

2. Použijte **PsPing** nebo **nmap** ze zdrojového virtuálního počítače do cílového virtuálního počítače (například: `PsPing 10.0.0.4:80` nebo `Nmap -p 80 10.0.0.4` ).
3. Otevřete trasování sítě z cílového virtuálního počítače pomocí [Sledování sítě](https://download.cnet.com/s/network-monitor) nebo tcpdump. Použijte filtr zobrazení pro IP adresu zdrojového virtuálního počítače, ze kterého jste spustili **PsPing** nebo **nmap** , jako je například `IPv4.address==10.0.0.4 (Windows netmon)` nebo `tcpdump -nn -r vmtrace.cap src or dst host 10.0.0.4` (Linux).

### <a name="analyze-traces"></a>Analyzovat trasování

Pokud nevidíte příchozí pakety pro trasování virtuálního počítače back-endu, pravděpodobně došlo ke konfliktu NSG nebo UDR, nebo jsou tabulky směrování síťové virtuální zařízení nesprávné.

Pokud se příchozí balíčky zobrazují, ale bezu odpovědi, pravděpodobně budete muset řešit potíže s bránou firewall nebo aplikací virtuálního počítače. U každého z těchto problémů se [podle potřeby obraťte na dodavatele síťové virtuální zařízení](https://mskb.pkisolutions.com/kb/2984655).