---
title: Jak povolit vnořenou virtualizaci ve virtuálních počítačích Azure
description: Jak povolit vnořenou virtualizaci ve virtuálních počítačích Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
ms.author: cynthn
ms.date: 10/09/2017
ms.topic: conceptual
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.openlocfilehash: d127d3f9e66b7d2d40aa420e2116fee17d996514
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437292"
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Jak povolit vnořenou virtualizaci ve virtuálním počítači Azure

Vnořená virtualizace se podporuje v několika rodinách virtuálních strojů Azure. Tato funkce poskytuje velkou flexibilitu při podpoře scénářů, jako je vývoj, testování, školení a demonstrační prostředí.   

Tento článek vás provede povolením technologie Hyper-V na virtuálním počítači Azure a konfigurací připojení k Internetu k tomuto virtuálnímu počítači hosta.

## <a name="create-a-nesting-capable-azure-vm"></a>Vytvoření virtuálního počítače Azure s podporou vnoření

Vytvořte nový virtuální počítač Azure pro Windows Server 2016. Úplný seznam velikostí virtuálních strojů, které podporují vnoření, podívejte se na [článek Azure Compute Unit](acu.md).

Nezapomeňte zvolit velikost virtuálního počítače dostatečně velký, aby podporovalpožadavky hostvirtuálního počítače. V tomto příkladu používáme virtuální počítač Azure velikosti D3_v3. 

Místní dostupnost virtuálních počítačů řady Dv3 nebo Ev3 si můžete prohlédnout [zde](https://azure.microsoft.com/regions/services/).

>[!NOTE]
>
>Podrobné pokyny k vytvoření nového virtuálního počítače najdete v tématu [Vytvoření a správa virtuálních aplikací Windows pomocí modulu Azure PowerShell.](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
    
## <a name="connect-to-your-azure-vm"></a>Připojení k virtuálnímu počítači Azure

Vytvořte připojení ke vzdálené ploše virtuálního počítače.

1. Klikněte na tlačítko **Připojit** ve vlastnostech virtuálního počítače. Vytvoří a stáhne se soubor protokolu RDP (Remote Desktop Protocol) – soubor .rdp.

2. Chcete-li se připojit k virtuálnímu počítači, otevřete stažený soubor protokolu RDP. Pokud se zobrazí výzva, klikněte na **Připojit**. Na počítači Mac budete potřebovat klienta protokolu RDP, jako je například tento [Klient vzdálené plochy](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) na Mac App Storu.

3. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače, a potom klikněte na **Ok**.

4. Během procesu přihlášení se může zobrazit upozornění certifikátu. Klikněte na **Ano** nebo **Pokračovat** a pokračujte v připojení.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>Povolení funkce Hyper-V na virtuálním počítači Azure
Tato nastavení můžete nakonfigurovat ručně nebo jsme poskytli skript prostředí PowerShell pro automatizaci konfigurace.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>Možnost 1: Konfigurace vnořené virtualizace pomocí skriptu Prostředí PowerShell
Skript Prostředí PowerShell umožňující vnořenou virtualizaci na hostiteli Windows Serveru 2016 je dostupný na [GitHubu](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested). Skript kontroluje předpoklady a pak nakonfiguruje vnořenou virtualizaci na virtuálním počítači Azure. Restartování virtuálního počítače Azure je nutné k dokončení konfigurace. Tento skript může fungovat v jiných prostředích, ale není zaručena. Podívejte se na blogový příspěvek Azure s živou ukázkou videa na vnořené virtualizaci spuštěnou v Azure! https://aka.ms/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>Možnost 2: Ruční konfigurace vnořené virtualizace

1. Na virtuálním počítači Azure otevřete PowerShell jako správce. 

2. Povolte funkci Hyper-V a nástroje pro správu.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >Tento příkaz restartuje virtuální počítač Azure. Během procesu restartování ztratíte připojení RDP.
    
3. Po restartování virtuálního počítače Azure se znovu připojte k virtuálnímu počítači pomocí rdp.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>Nastavení připojení k internetu pro virtuální počítač hosta
Vytvořte nový virtuální síťový adaptér pro hostovaný virtuální počítač a nakonfigurujte bránu NAT pro povolení připojení k Internetu.

### <a name="create-a-nat-virtual-network-switch"></a>Vytvoření přepínače virtuální sítě NAT

1. Na virtuálním počítači Azure otevřete PowerShell jako správce.
   
2. Vytvořte interní přepínač.

    ```powershell
    New-VMSwitch -Name "InternalNAT" -SwitchType Internal
    ```

3. Zobrazení vlastností přepínače a poznamenejte si ifIndex pro nový adaptér.

    ```powershell
    Get-NetAdapter
    ```

    ![Síťový adaptér](./media/virtual-machines-nested-virtualization/get-netadapter.png)

    >[!NOTE] 
    >
    >Poznamenejte si "ifIndex" pro virtuální přepínač, který jste právě vytvořili.
    
4. Vytvořte IP adresu pro bránu NAT.
    
Chcete-li nakonfigurovat bránu, potřebujete některé informace o síti:    
  * IPAddress - Adresa IP brány NAT určuje adresu IPv4 nebo IPv6, která se má použít jako výchozí adresa brány pro podsíť virtuální sítě. Obecný formulář je a.b.c.1 (například "192.168.0.1"). Zatímco konečná pozice nemusí být .1, obvykle je (na základě délky předpony). Obvykle byste měli použít privátní síťový prostor rfc 1918. 
  * PrefixLength - Délka předpony podsítě definuje velikost místní podsítě (masku podsítě). Délka předpony podsítě bude celá hodnota mezi 0 a 32. 0 by mapovat celý internet, 32 by umožnilo pouze jednu mapovanou IP. Běžné hodnoty se pohybují od 24 do 12 v závislosti na tom, kolik IP adresy je třeba připojit k NAT. Společná délka předpony je 24 -- toto je maska podsítě 255.255.255.0.
  * InterfaceIndex - **ifIndex** je index rozhraní virtuálního přepínače vytvořeného v předchozím kroku. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>Vytvoření sítě NAT

Chcete-li nakonfigurovat bránu, budete muset poskytnout informace o síti a bráně NAT:
  * Název - Toto je název sítě NAT. 
  * InternalIPInterfaceAddressPrefix - Předpona podsítě NAT popisuje jak předponu IP brány NAT shora, tak délku předpony podsítě NAT shora. Obecný formulář bude a.b.c.0/NAT Délka předpony podsítě. 

V Prostředí PowerShell vytvořte novou síť NAT.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>Vytvoření virtuálního počítače hosta

>[!IMPORTANT] 
>
>Agent hosta Azure není podporován na vnořených virtuálních počítačích a může způsobit problémy na hostiteli i vnořených virtuálních počítačích. Neinstalujte agenta Azure na vnořené virtuální počítače a nepoužívejte image pro vytváření vnořených virtuálních počítačů, které už mají nainstalovaného agenta hosta Azure.

1. Otevřete Správce technologie Hyper-V a vytvořte nový virtuální počítač. Nakonfigurujte virtuální počítač tak, aby používal novou interní síť, kterou jste vytvořili.
    
    ![Síťová konfigurace](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. Nainstalujte operační systém do hostovaného virtuálního počítače.
    
    >[!NOTE] 
    >
    >K instalaci operačního systému na virtuální počítač potřebujete instalační médium. V tomto případě používáme Windows 10 Enterprise.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>Přiřazení IP adresy k hostovanému virtuálnímu počítači

Virtuálnímu počítači hosta můžete přiřadit IP adresu buď ručním nastavením statické IP adresy na virtuálním počítači hosta, nebo konfigurací DHCP na virtuálním počítači Azure tak, aby přiřazovala IP adresu dynamicky.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>Možnost 1: Konfigurace služby DHCP pro dynamické přiřazení ADRESY IP virtuálnímu počítači hosta
Podle následujících kroků nakonfigurujte službu DHCP na hostitelském virtuálním počítači pro dynamické přiřazení adres.

#### <a name="install-dchp-server-on-the-azure-vm"></a>Instalace serveru DCHP na virtuální počítač Azure

1. Spusťte Správce serveru. Na řídicím panelu klikněte na **Přidat role a funkce**. Zobrazí se Průvodce přidáním rolí a funkcí.
  
2. V průvodci klikněte na **Další** až do stránky Role serveru.
  
3. Klepnutím zaškrtněte políčko **Server DHCP,** klepněte na tlačítko **Přidat funkce**a potom klepněte na tlačítko **Další,** dokud průvodce nedokončíte.
  
4. Klepněte na tlačítko **Instalovat**.

#### <a name="configure-a-new-dhcp-scope"></a>Konfigurace nového oboru DHCP

1. Otevřete správce DHCP.
  
2. V navigačním podokně rozbalte název serveru, klepněte pravým tlačítkem myši na **iPv4**a klepněte na příkaz **Nový obor**. Zobrazí se Průvodce novým oborem, klepněte na tlačítko **Další**.
  
3. Zadejte název a popis oboru a klepněte na tlačítko **Další**.
  
4. Definujte rozsah IP adres pro váš dchp server (například 192.168.0.100 až 192.168.0.200).
  
5. Až do výchozí stránky Brána klikněte na **Další.** Zadejte dříve vytvořenou IP adresu (například 192.168.0.1) jako výchozí bránu a klepněte na tlačítko **Přidat**.
  
6. Až průvodce dokončí, klepněte na tlačítko **Další** a nezanechte všechny výchozí hodnoty a klepněte na tlačítko **Dokončit**.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>Možnost 2: Ruční nastavení statické IP adresy na virtuálním počítači hosta
Pokud jste službu DHCP nenakonfigurovali tak, aby dynamicky přiřazovala virtuálnímu počítači hosta adresu IP, nastavte statickou adresu IP následujícím postupem.

1. Na virtuálním počítači Azure otevřete PowerShell jako správce.

2. Klikněte pravým tlačítkem myši na virtuální počítač hosta a klikněte na Připojit.

3. Přihlaste se k virtuálnímu počítači hosta.

4. Na hostovaném virtuálním počítači otevřete Centrum sítě a sdílení.

5. Nakonfigurujte síťový adaptér pro adresu v rozsahu sítě NAT, kterou jste vytvořili v předchozí části.

V tomto příkladu použijete adresu v rozsahu 192.168.0.0/24.

## <a name="test-connectivity-in-guest-virtual-machine"></a>Testování připojení ve virtuálním počítači hosta

Ve virtuálním počítači hosta otevřete prohlížeč a přejděte na webovou stránku.
    ![HostvM](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)

## <a name="set-up-intranet-connectivity-for-the-guest-virtual-machine"></a>Nastavení připojení intranetu pro virtuální počítač hosta

Pokyny k povolení transparentního připojení mezi virtuálními počítači hosta a virtuálními počítači Azure najdete v [tomto dokumentu](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization-azure-virtual-network).
