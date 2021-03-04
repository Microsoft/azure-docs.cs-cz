---
title: Jak povolit vnořenou virtualizaci v Azure Virtual Machines
description: Jak povolit vnořenou virtualizaci v Azure Virtual Machines
author: cynthn
ms.author: cynthn
ms.date: 10/09/2017
ms.topic: how-to
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.openlocfilehash: 924ee745804ef31e42dc21437dbb0459f6d37701
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693743"
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Jak povolit vnořenou virtualizaci na virtuálním počítači Azure

Vnořená virtualizace je podporovaná v několika rodinách virtuálních počítačů Azure. Tato funkce poskytuje skvělou flexibilitu při podpoře scénářů, jako je vývoj, testování, školení a demonstrační prostředí.   

Tento článek vás provede povolením technologie Hyper-V na VIRTUÁLNÍm počítači Azure a konfigurací připojení k Internetu na tento hostovaný virtuální počítač.

## <a name="create-a-nesting-capable-azure-vm"></a>Vytvoření vnořování podporujícího virtuální počítač Azure

Vytvořte nový virtuální počítač Azure s Windows serverem 2016 nebo Windows serverem 2019 pro hostitele. Ujistěte se, že jste zvolili velikost pro váš virtuální počítač, který podporuje vnořování, a je dostatečně velký pro splnění požadavků hostovaných virtuálních počítačů. Seznam velikostí virtuálních počítačů, které podporují vnořování, najdete v článku [výpočetní jednotky Azure](../acu.md) .

Místní dostupnost velikostí virtuálních počítačů můžete zobrazit na stránce [produkty dostupné v oblasti](https://azure.microsoft.com/regions/services/) .

>[!NOTE]
>
>Podrobné pokyny k vytvoření nového virtuálního počítače najdete v tématu [Vytvoření a správa virtuálních počítačů s Windows pomocí modulu Azure PowerShell](./tutorial-manage-vm.md) .
    
## <a name="connect-to-your-azure-vm"></a>Připojení k VIRTUÁLNÍmu počítači Azure

Vytvořte připojení ke vzdálené ploše virtuálního počítače.

1. Klikněte na tlačítko **Připojit** ve vlastnostech virtuálního počítače. Vytvoří a stáhne se soubor protokolu RDP (Remote Desktop Protocol) – soubor .rdp.

2. Chcete-li se připojit k virtuálnímu počítači, otevřete stažený soubor protokolu RDP. Pokud se zobrazí výzva, klikněte na **Připojit**. Na počítači Mac budete potřebovat klienta protokolu RDP, jako je například tento [Klient vzdálené plochy](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) na Mac App Storu.

3. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače, a potom klikněte na **Ok**.

4. Během procesu přihlášení se může zobrazit upozornění certifikátu. Klikněte na **Ano** nebo **Pokračovat** a pokračujte v připojení.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>Povolení funkce Hyper-V na virtuálním počítači Azure
Tato nastavení můžete nakonfigurovat ručně nebo vám poskytneme PowerShellový skript pro automatizaci konfigurace.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>Možnost 1: použití skriptu prostředí PowerShell ke konfiguraci vnořené virtualizace
Skript prostředí PowerShell, který umožňuje vnořenou virtualizaci na hostiteli s Windows serverem 2016, je k dispozici na [GitHubu](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested). Skript zkontroluje požadavky a potom nakonfiguruje vnořenou virtualizaci na virtuálním počítači Azure. K dokončení konfigurace je potřeba restartovat virtuální počítač Azure. Tento skript může fungovat v jiných prostředích, ale není zaručený. Podívejte se na Blogový příspěvek Azure s živou ukázkou videa na vnořené virtualizaci běžící v Azure. https://aka.ms/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>Možnost 2: ruční konfigurace vnořené virtualizace

1. Na virtuálním počítači Azure otevřete PowerShell jako správce. 

2. Povolte funkce a nástroje pro správu technologie Hyper-V.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >Tento příkaz restartuje virtuální počítač Azure. Během procesu restartování dojde ke ztrátě připojení RDP.
    
3. Po restartování virtuálního počítače Azure se znovu připojte k VIRTUÁLNÍmu počítači pomocí protokolu RDP.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>Nastavení připojení k Internetu pro hostovaný virtuální počítač
Vytvořte nový virtuální síťový adaptér pro hostovaný virtuální počítač a nakonfigurujte bránu NAT, aby se povolilo připojení k Internetu.

### <a name="create-a-nat-virtual-network-switch"></a>Vytvoření přepínače virtuální sítě NAT

1. Na virtuálním počítači Azure otevřete PowerShell jako správce.
   
2. Vytvořte interní přepínač.

    ```powershell
    New-VMSwitch -Name "InternalNAT" -SwitchType Internal
    ```

3. Zobrazte vlastnosti přepínače a poznamenejte si ifIndex pro nový adaptér.

    ```powershell
    Get-NetAdapter
    ```

    ![NetAdapter](./media/virtual-machines-nested-virtualization/get-netadapter.png)

    >[!NOTE] 
    >
    >Poznamenejte si "ifIndex" pro virtuální přepínač, který jste právě vytvořili.
    
4. Vytvořte IP adresu pro bránu NAT.
    
Aby bylo možné bránu nakonfigurovat, potřebujete nějaké informace o vaší síti:    
  * IP adresa – IP adresa brány NAT Určuje adresu protokolu IPv4 nebo IPv6, která se má použít jako adresa výchozí brány pro podsíť virtuální sítě. Obecný formulář je a. b. c. 1 (například "192.168.0.1"). I když poslední pozice nemusí být 1, obvykle je (na základě délky předpony). Obvykle byste měli použít adresní prostor privátní sítě RFC 1918. 
  * PrefixLength – délka předpony podsítě definuje velikost místní podsítě (maska podsítě). Délka předpony podsítě bude celočíselná hodnota mezi 0 a 32. 0 by namapoval celý Internet, 32 by povoloval jenom jednu namapovanou IP adresu. Rozsahy běžných hodnot od 24 do 12 v závislosti na tom, kolik IP adres je potřeba připojit k překladu adres (NAT). Běžná PrefixLength je 24 – jedná se o masku podsítě 255.255.255.0.
  * InterfaceIndex- **ifIndex** je index rozhraní virtuálního přepínače vytvořeného v předchozím kroku. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>Vytvoření sítě NAT

Aby bylo možné bránu nakonfigurovat, budete muset zadat informace o síti a bráně NAT:
  * Název – jedná se o název sítě NAT. 
  * InternalIPInterfaceAddressPrefix – předpona podsítě NAT popisuje předponu IP adresy brány NAT výše uvedenou výše a také délku předpony podsítě NAT. Obecný formulář bude a. b. c. 0/Délka předpony podsítě NAT. 

V PowerShellu vytvořte novou síť NAT.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>Vytvoření hostovaného virtuálního počítače

>[!IMPORTANT] 
>
>Agent hosta Azure není ve vnořených virtuálních počítačích podporován a může způsobovat problémy na hostitelském i vnořeném virtuálním počítači. Neinstalujte agenta Azure na vnořené virtuální počítače a nepoužívejte image pro vytváření vnořených virtuálních počítačů, na kterých už je nainstalovaný Agent hosta Azure.

1. Otevřete Správce technologie Hyper-V a vytvořte nový virtuální počítač. Nakonfigurujte virtuální počítač tak, aby používal novou interní síť, kterou jste vytvořili.
    
    ![NetworkConfig](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. Nainstalujte operační systém do hostovaného virtuálního počítače.
    
    >[!NOTE] 
    >
    >K instalaci na virtuální počítač budete potřebovat instalační médium pro operační systém. V tomto případě používáme Windows 10 Enterprise.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>Přiřaďte IP adresu hostovanému virtuálnímu počítači.

IP adresu můžete přiřadit k hostovanému virtuálnímu počítači, a to buď ručním nastavením statické IP adresy na hostovaném virtuálním počítači, nebo konfigurací DHCP na VIRTUÁLNÍm počítači Azure, aby se IP adresa přiřazoval dynamicky.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>Možnost 1: Konfigurace protokolu DHCP pro dynamické přiřazení IP adresy hostovanému virtuálnímu počítači
Pomocí následujících kroků nakonfigurujte DHCP na virtuálním počítači hostitele pro dynamické přiřazování adres.

#### <a name="install-dhcp-server-on-the-azure-vm"></a>Instalace serveru DHCP na virtuální počítač Azure

1. Spusťte Správce serveru. Na řídicím panelu klikněte na **Přidat role a funkce**. Zobrazí se Průvodce přidáním rolí a funkcí.
  
2. V průvodci klikněte na **Další** , dokud nepřejdete na stránku role serveru.
  
3. Zaškrtněte políčko **Server DHCP** , klikněte na **Přidat funkce** a potom klikněte na **Další** , dokud Průvodce nedokončíte.
  
4. Klikněte na **Install** (Nainstalovat).

#### <a name="configure-a-new-dhcp-scope"></a>Konfigurace nového oboru DHCP

1. Otevřete Správce DHCP.
  
2. V navigačním podokně rozbalte název serveru, klikněte pravým tlačítkem na **IPv4** a pak klikněte na **Nový obor**. Zobrazí se Průvodce vytvořením oboru, klikněte na tlačítko **Další**.
  
3. Zadejte název a popis oboru a klikněte na **Další**.
  
4. Zadejte rozsah IP adres pro server DHCP (například 192.168.0.100 to 192.168.0.200).
  
5. Klikněte na tlačítko **Další** , dokud nebude stránka výchozí brána. Jako výchozí bránu zadejte IP adresu, kterou jste dříve vytvořili (například 192.168.0.1), a pak klikněte na **Přidat**.
  
6. Klikněte na **Další** , dokud se průvodce nedokončí, ponechte všechny výchozí hodnoty a pak klikněte na **Dokončit**.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>Možnost 2: ručně nastavte statickou IP adresu v hostovaném virtuálním počítači.
Pokud jste nenakonfigurovali protokol DHCP tak, aby dynamicky přiřadil IP adresu hostovanému virtuálnímu počítači, nastavte statickou IP adresu pomocí těchto kroků.

1. Na virtuálním počítači Azure otevřete PowerShell jako správce.

2. Klikněte pravým tlačítkem na virtuální počítač hosta a klikněte na připojit.

3. Přihlaste se k hostovanému virtuálnímu počítači.

4. V hostovaném virtuálním počítači otevřete centrum sítě a sdílení.

5. Nakonfigurujte síťový adaptér pro adresu v rámci rozsahu sítě NAT, kterou jste vytvořili v předchozí části.

V tomto příkladu budete používat adresu v rozsahu 192.168.0.0/24.

## <a name="test-connectivity-in-guest-virtual-machine"></a>Testovat připojení v hostovaném virtuálním počítači

V hostovaném virtuálním počítači otevřete prohlížeč a přejděte na webovou stránku.
    ![GuestVM](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)

Pokyny, jak povolit transparentní připojení mezi virtuálními počítači hosta a virtuálními počítači Azure, najdete v [tomto dokumentu](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization).
