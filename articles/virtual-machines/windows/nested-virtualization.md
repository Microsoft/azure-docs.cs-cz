---
title: Postup povolení vnořené virtualizace v Azure Virtual Machines | Microsoft Docs
description: Postup povolení vnořené virtualizace v Azure Virtual Machines
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
ms.author: cynthn
ms.date: 10/09/2017
ms.topic: howto
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.openlocfilehash: 0648a7555cb94543dadf5d73e0187927a90f5b5a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31526514"
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Postup povolení vnořené virtualizaci ve virtuálním počítači Azure

Vnořené virtualizace je podporována v řadě Dv3 a Ev3 virtuální počítače Azure. Tato funkce poskytuje flexibilitu při podpoře scénáře, jako je vývoj, testování, školení a ukázkový prostředí. 

Tento článek obsahuje postup povolení vnořené virtualizace na virtuální počítač Azure a konfigurací připojení k Internetu k tomuto virtuálnímu počítači hosta.

## <a name="create-a-dv3-or-ev3-series-azure-vm"></a>Vytvoření Dv3 nebo Ev3 řad virtuálního počítače Azure

Vytvoření nového virtuálního Azure počítače s Windows Server 2016 a zvolte velikost z Dv3 nebo Ev3 řad. Zkontrolujte, zda že zvolte velikost dostatečně velký pro podporu požadavků virtuálního počítače hosta. V tomto příkladu používáme D3_v3 velikost virtuálního počítače Azure. 

Můžete zobrazit místní dostupnost Dv3 nebo Ev3 řady virtuálních počítačů [zde](https://azure.microsoft.com/regions/services/).

>[!NOTE]
>
>Podrobné pokyny pro vytvoření nového virtuálního počítače, naleznete v části [vytvořit a spravovat virtuální počítače Windows s modulu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
    
## <a name="connect-to-your-azure-vm"></a>Připojení k virtuálnímu počítači Azure

Vytvořte připojení ke vzdálené ploše virtuálního počítače.

1. Klikněte na tlačítko **Připojit** ve vlastnostech virtuálního počítače. Vytvoří a stáhne se soubor protokolu RDP (Remote Desktop Protocol) – soubor .rdp.

2. Chcete-li se připojit k virtuálnímu počítači, otevřete stažený soubor protokolu RDP. Pokud se zobrazí výzva, klikněte na **Připojit**. Na počítači Mac budete potřebovat klienta protokolu RDP, jako je například tento [Klient vzdálené plochy](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) na Mac App Storu.

3. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače, a potom klikněte na **Ok**.

4. Během procesu přihlášení se může zobrazit upozornění certifikátu. Klikněte na **Ano** nebo **Pokračovat** a pokračujte v připojení.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>Povolení této funkce technologie Hyper-V na virtuálním počítači Azure
Toto nastavení můžete nakonfigurovat ručně nebo uvádíme skript prostředí PowerShell k automatizaci konfigurace.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>Možnost 1: Konfigurace vnořené virtualizace pomocí skriptu prostředí PowerShell
Skript prostředí PowerShell povolit vnořené virtualizace v hostiteli systému Windows Server 2016 je k dispozici na [Githubu](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested). Skript zkontroluje požadavky a poté konfiguruje vnořené virtualizace na virtuální počítač Azure. Restartování virtuálního počítače Azure je nutné k dokončení konfigurace. Tento skript může fungovat v jiných prostředích, ale není zaručena. Podívejte se na Azure blogu s za provozu Videoukázka na vnořené virtualizace spuštěné v Azure! https://aka.ms/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>Možnost 2: Ruční konfigurace vnořené virtualizace

1. Ve virtuálním počítači Azure otevřete PowerShell jako správce. 

2. Povolení funkce Hyper-V a nástroje pro správu.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >Tento příkaz restartuje virtuální počítač Azure. Dojde ke ztrátě připojení RDP během procesu restartování.
    
3. Po restartování virtuálního počítače Azure, znovu se připojte k virtuálnímu počítači pomocí protokolu RDP.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>Nastavit připojení k Internetu pro hostovaného virtuálního počítače.
Vytvořte nový virtuální síťový adaptér pro virtuální počítač hostovaný a nakonfigurujte bránu NAT umožňující připojení k Internetu.

### <a name="create-a-nat-virtual-network-switch"></a>Vytvořit virtuální síťový přepínač NAT

1. Ve virtuálním počítači Azure otevřete PowerShell jako správce.
   
2. Vytvořte interní přepínač.

    ```powershell
    New-VMSwitch -Name "InternalNATSwitch" -SwitchType Internal
    ```

3. Zobrazení vlastností přepínače a poznamenejte si ifIndex pro nový adaptér.

    ```powershell
    Get-NetAdapter
    ```

    ![NetAdapter](./media/virtual-machines-nested-virtualization/get-netadapter.png)

    >[!NOTE] 
    >
    >Poznamenejte si "ifIndex" pro virtuální přepínač, který jste právě vytvořili.
    
4. Vytvořte adresu IP pro NAT bránu.
    
Chcete-li konfigurovat bránu, budete muset některé informace o vaší síti:    
  * IP adresa – IP adres NAT brány určuje adresu IPv4 nebo IPv6, které chcete použít jako adresu výchozí brány pro podsítě virtuální sítě. Obecný formulář je a.b.c.1 (například "192.168.0.1"). Při poslední pozice nemusí být.1, je obvykle je (na základě délka předpony). Obvykle byste měli používat RFC 1918 privátní síť adresní prostor. 
  * PrefixLength - délka předpony podsítě definuje velikost místní podsítě (masky podsítě). Délka předpony podsítě bude celočíselnou hodnotu mezi 0 a 32. 0 by mapování celý internet, 32 by Povolit jenom jedna IP adresa namapované. Běžné hodnoty jsou v rozsahu od 24 do 12 v závislosti na tom, kolik IP adresy musí být připojen k NAT. Běžné PrefixLength je 24 – to je maskou podsítě 255.255.255.0.
  * InterfaceIndex - **ifIndex** je index virtuálního přepínače vytvořili v předchozím kroku. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>Vytvoření sítě NAT

Chcete-li konfigurovat bránu, budete muset zadat informace o sítě a brány NAT:
  * Název – to je název sítě NAT. 
  * InternalIPInterfaceAddressPrefix – Předpona podsítě NAT popisuje předponu IP adres NAT brány z výše jak délka předpony podsítě NAT z výše. Obecný formulář bude a.b.c.0/NAT délka předpony podsítě. 

V prostředí PowerShell vytvořte novou síť NAT.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>Vytvoření hostovaného virtuálního počítače.

1. Otevřete Správce technologie Hyper-V a vytvoření nového virtuálního počítače. Konfigurace virtuálního počítače používat nový interní síti, kterou jste vytvořili.
    
    ![NetworkConfig](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. Nainstalujte operační systém na virtuálním počítači hosta.
    
    >[!NOTE] 
    >
    >Je třeba instalační médium pro operační systém pro instalaci na virtuálním počítači. V tomto případě používáme Windows 10 Enterprise.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>Přiřadit IP adresu do hostovaného virtuálního počítače.

Hostovaný virtuální počítač ručně nastavení statické IP adresy na virtuálním počítači hosta nebo konfigurace DHCP na virtuálním počítači Azure dynamicky přiřadit IP adresu můžete přiřadit IP adresu.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>Možnost 1: Konfigurace protokolu DHCP k dynamickému přidělení IP adresu do hostovaného virtuálního počítače.
Postupujte podle následujících kroků a konfigurace DHCP na hostiteli virtuálního počítače pro dynamické přiřazení adres.

#### <a name="install-dchp-server-on-the-azure-vm"></a>Server DHCP nainstalovat na virtuální počítač Azure

1. Otevřete správce serveru. Na řídicím panelu klikněte na tlačítko **přidat role a funkce**. Přidat role a funkce Průvodce se zobrazí.
  
2. V průvodci klikněte na tlačítko **Další** dokud stránce role serveru.
  
3. Kliknutím vyberte **DHCP Server** zaškrtávací políčko, klikněte na tlačítko **přidat funkce**a potom klikněte na **Další** až do dokončení průvodce.
  
4. Klikněte na **Nainstalovat**.

#### <a name="configure-a-new-dhcp-scope"></a>Konfigurace nového oboru DHCP

1. Otevřete správce serveru DHCP.
  
2. V navigačním podokně rozbalte název serveru, klikněte pravým tlačítkem na **IPv4**a klikněte na tlačítko **nového oboru**. Zobrazí se Průvodce vytvořením oboru, klikněte na tlačítko **Další**.
  
3. Zadejte název a popis pro obor a klikněte na **Další**.
  
4. Zadejte rozsah adres IP pro váš Server DHCP (například 192.168.0.100 k 192.168.0.200).
  
5. Klikněte na tlačítko **Další** dokud stránce výchozí brány. Zadejte adresu IP, který jste vytvořili dříve (například 192.168.0.1) jako výchozí brána.
  
6. Klikněte na tlačítko **Další** až po dokončení průvodce, a všechny výchozí hodnoty, pak klikněte na tlačítko **Dokončit**.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>Možnost 2: Ručně nastavte statickou IP adresu ve virtuálním počítači hosta
Pokud jste nenakonfigurovali DHCP k dynamickému přidělení IP adresy na virtuálním počítači hosta, postupujte podle těchto kroků můžete nastavte statickou IP adresu.

1. Ve virtuálním počítači Azure otevřete PowerShell jako správce.

2. Klikněte pravým tlačítkem na virtuálním počítači hosta a klikněte na tlačítko Připojit.

3. Přihlaste se k virtuálnímu počítači hosta.

4. Na virtuálním počítači hosta otevřete Centru sítí a sdílení.

5. Nakonfigurujte síťový adaptér pro adresu v rozsahu NAT sítě, kterou jste vytvořili v předchozí části.

V tomto příkladu použijete adresu v rozsahu 192.168.0.0/24.

## <a name="test-connectivity-in-guest-virtual-machine"></a>Test připojení v hostovaném virtuálním počítači

Ve virtuálním počítači hosta otevřete prohlížeč a přejděte na webovou stránku.
    ![GuestVM](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)
