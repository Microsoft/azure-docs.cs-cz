---
title: Povolení vnořené virtualizace ve službě Azure Virtual Machines | Dokumentace Microsoftu
description: Povolení vnořené virtualizace ve službě Azure Virtual Machines
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
ms.openlocfilehash: 6a116c2f3d92b19f266be4d3b467a93fdeaced2c
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54888968"
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Povolení vnořené virtualizace ve Virtuálním počítači Azure

Vnořená virtualizace se podporuje v několika virtuálních počítačů Azure řady. Tato funkce poskytuje flexibilitu při podpoře scénářů, jako je vývoj, testování, školení a ukázky prostředí.   

Tento článek vás provede povolením technologie Hyper-V na Virtuálním počítači Azure a konfigurací připojení k Internetu na tomto virtuálním počítači hosta.

## <a name="create-a-nesting-capable-azure-vm"></a>Vytvoření vnoření schopné virtuálního počítače Azure

Vytvoření nového systému Windows Server 2016 Azure virtuálního počítače.  Pro rychlé vztažnou podporovat všechny virtuální počítače v3 vnořená virtualizace. Pro úplný seznam všech virtuálních počítačů velikosti vnoření této podpory, projděte si [výpočetních jednotek Azure článku](acu.md).

Nezapomeňte vybrat velikost virtuálního počítače, který je dostatečně velký pro podporu požadavků hostovaném virtuálním počítači. V tomto příkladu používáme D3_v3 velikost virtuálního počítače Azure. 

Můžete zobrazit regionální dostupnosti virtuálních počítačů Dv3 a Ev3 series [tady](https://azure.microsoft.com/regions/services/).

>[!NOTE]
>
>Podrobné pokyny pro vytvoření nového virtuálního počítače najdete v tématu [vytvoření a správa virtuálních počítačů Windows pomocí modulu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
    
## <a name="connect-to-your-azure-vm"></a>Připojte se ke svému virtuálnímu počítači Azure

Vytvořte připojení ke vzdálené ploše virtuálního počítače.

1. Klikněte na tlačítko **Připojit** ve vlastnostech virtuálního počítače. Vytvoří a stáhne se soubor protokolu RDP (Remote Desktop Protocol) – soubor .rdp.

2. Chcete-li se připojit k virtuálnímu počítači, otevřete stažený soubor protokolu RDP. Pokud se zobrazí výzva, klikněte na **Připojit**. Na počítači Mac budete potřebovat klienta protokolu RDP, jako je například tento [Klient vzdálené plochy](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) na Mac App Storu.

3. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače, a potom klikněte na **Ok**.

4. Během procesu přihlášení se může zobrazit upozornění certifikátu. Klikněte na **Ano** nebo **Pokračovat** a pokračujte v připojení.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>Povolit funkci Hyper-V na virtuálním počítači Azure
Tato nastavení můžete nakonfigurovat ručně nebo uvádíme skript prostředí PowerShell k automatizaci konfigurace.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>Option 1: Použít skript prostředí PowerShell ke konfiguraci vnořené virtualizace
Skript prostředí PowerShell pro povolení vnořené virtualizace na hostitelském Windows serveru 2016 je k dispozici na [Githubu](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested). Skript zkontroluje požadavky a pak nakonfiguruje vnořená virtualizace ve virtuálním počítači Azure. Restartování virtuálního počítače Azure jsou nezbytné pro dokončení konfigurace. Tento skript může fungovat v jiných prostředích, ale není zaručena. Přečtěte si příspěvek blogu Azure se živé video ukázku na vnořenou virtualizaci běžící v Azure! https://aka.ms/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>Option 2: Ruční konfigurace vnořené virtualizace

1. Na virtuálním počítači Azure otevřete PowerShell jako správce. 

2. Povolte funkce Hyper-V a nástroje pro správu.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >Tento příkaz umožňuje restartovat virtuální počítač Azure. Dojde ke ztrátě připojení RDP během procesu restartování.
    
3. Po restartování virtuálního počítače Azure se znovu připojte k virtuálnímu počítači pomocí protokolu RDP.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>Nastavit připojení k Internetu pro virtuální počítač hosta
Vytvořit nový virtuální síťový adaptér pro virtuální počítač hosta a konfigurovat bránu překladu adres umožňující připojení k Internetu.

### <a name="create-a-nat-virtual-network-switch"></a>Vytvořit virtuální síťový přepínač NAT

1. Na virtuálním počítači Azure otevřete PowerShell jako správce.
   
2. Vytvoření interní přepínač.

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
    
4. Vytvoření IP adresy pro bránu překladu adres.
    
Aby bylo možné konfigurovat bránu, budete muset některé informace o vaší síti:    
  * IP adresa – IP adres NAT brány určuje adresa IPv4 nebo IPv6, které chcete použít jako adresu výchozí brány pro podsíť virtuální sítě. Obecný formulář je a.b.c.1 (například "192.168.0.1"). Při poslední pozice nemusí být.1, je obvykle je (na základě délka předpony). Obvykle byste měli použít ve specifikaci RFC 1918 adresní prostor privátní sítě. 
  * PrefixLength – délka předpony podsítě definuje velikost místní podsítě (maska podsítě). Délka předpony podsítě bude celočíselnou hodnotu od 0 do 32. 0 bude mapovat celou internet, 32 by Povolit jenom jedna IP adresa pro mapovanou. Běžné hodnoty spadají do rozsahu od 24 do 12 v závislosti na tom, kolik IP adres musí být připojené k NAT. Běžné PrefixLength je 24 – to je maskou podsítě 255.255.255.0.
  * InterfaceIndex - **ifIndex** je index rozhraní virtuální přepínače, který vytvořili v předchozím kroku. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>Vytvoření sítě NAT

Aby bylo možné konfigurovat bránu, budete muset zadat informace o sítě a překladu adres brány:
  * Název – jde o název síti NAT. 
  * InternalIPInterfaceAddressPrefix – Předpona podsítě NAT popisuje předpony adres IP pro NAT brány výše i délka předpony podsítě NAT výše. Obecný formulář bude a.b.c.0/NAT délka předpony podsítě. 

V prostředí PowerShell vytvořte novou síť překladu adres.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>Vytvoření virtuálního počítače hosta

1. Otevřete Správce technologie Hyper-V a vytvoření nového virtuálního počítače. Nakonfigurujte virtuální počítač a použít nový interní síti, kterou jste vytvořili.
    
    ![NetworkConfig](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. Nainstalujte operační systém na virtuálním počítači hosta.
    
    >[!NOTE] 
    >
    >Budete potřebovat instalační médium pro operační systém pro instalaci na virtuálním počítači. V tomto případě používáme Windows 10 Enterprise.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>Přiřadit IP adresu do hostovaného virtuálního počítače.

Na virtuálním počítači hosta ručně nastavení statické IP adresy na virtuálním počítači hosta nebo konfigurace DHCP na virtuálním počítači Azure dynamicky přiřadit IP adresu můžete přiřadit IP adresu.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>Option 1: Konfigurace protokolu DHCP v dynamicky přiřadit IP adresu do hostovaného virtuálního počítače.
Postupujte podle následujících kroků, abyste konfigurace DHCP na hostiteli virtuálního počítače pro dynamické přiřazení adres.

#### <a name="install-dchp-server-on-the-azure-vm"></a>Instalace serveru DHCP na virtuálním počítači Azure

1. Otevřete správce serveru. Na řídicím panelu, klikněte na tlačítko **přidat role a funkce**. Přidat role a funkce Průvodce se zobrazí.
  
2. V průvodci klikněte na tlačítko **Další** až do stránce role serveru.
  
3. Kliknutím vyberte **DHCP Server** zaškrtávacího políčka, klikněte na tlačítko **přidat funkce**a potom klikněte na tlačítko **Další** až do dokončení průvodce.
  
4. Klikněte na **Nainstalovat**.

#### <a name="configure-a-new-dhcp-scope"></a>Konfigurace nového oboru DHCP

1. Otevřete Správce služby DHCP.
  
2. V navigačním podokně rozbalte název serveru, klikněte pravým tlačítkem na **IPv4**a klikněte na tlačítko **nový obor**. Zobrazí se Průvodce vytvořením oboru, klikněte na tlačítko **Další**.
  
3. Zadejte název a popis pro obor a klikněte na tlačítko **Další**.
  
4. Zadejte rozsah IP adres serveru DHCP (například 192.168.0.100 k 192.168.0.200).
  
5. Klikněte na tlačítko **Další** až do stránky výchozí brány. Zadejte IP adresu vytvořenou dříve (například 192.168.0.1) jako výchozí brány a potom klikněte na **přidat**.
  
6. Klikněte na tlačítko **Další** až po dokončení průvodce, byste museli opustit všechny výchozí hodnoty, klikněte na **Dokončit**.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>Option 2: Ručně nastavte statickou IP adresu ve virtuálním počítači hosta
Pokud jste nenakonfigurovali DHCP v dynamicky přiřadit IP adresu na hostovaném virtuálním počítači, postupujte podle těchto pokynů nastavte statickou IP adresu.

1. Na virtuálním počítači Azure otevřete PowerShell jako správce.

2. Klikněte pravým tlačítkem na hostovaném virtuálním počítači a kliknete na připojit.

3. Přihlaste se virtuálním počítači hosta.

4. Na hostovaném virtuálním počítači otevřete Centrum sítí a sdílení.

5. Nakonfigurujte síťový adaptér pro adresu v rozsahu síti NAT, kterou jste vytvořili v předchozí části.

V tomto příkladu použijete adresu v rozsahu 192.168.0.0/24.

## <a name="test-connectivity-in-guest-virtual-machine"></a>Test připojení v hostovaném virtuálním počítači

V hostovaném virtuálním počítači otevřete prohlížeč a přejděte na webovou stránku.
    ![GuestVM](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)

## <a name="set-up-intranet-connectivity-for-the-guest-virtual-machine"></a>Nastavit připojení k intranetu pro hostovaného virtuálního počítače.

Pokyny o tom, jak povolit transparentní propojení virtuálních počítačů hosta a virtuální počítače Azure, použijte odkaz [tento dokument](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization-azure-virtual-network).
