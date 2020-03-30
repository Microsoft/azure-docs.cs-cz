---
title: Povolení vnořené virtualizace na virtuálním počítači šablony ve službě Azure Lab Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvořit virtuální počítač šablony s více virtuálními počítači uvnitř.  Jinými slovy povolte vnořenou virtualizaci na virtuálním počítači šablony ve službě Azure Lab Services.
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 10f8d2760474631fbcabac4d66139aedf4c7560c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503072"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-manually"></a>Ruční povolení vnořené virtualizace na virtuálním počítači šablony ve službě Azure Lab Services

Vnořená virtualizace umožňuje vytvořit prostředí s více virtuálními počítači uvnitř virtuálního počítače šablony testovacího prostředí. Publikování šablony poskytne každému uživateli v testovacím prostředí virtuální počítač nastavený s více virtuálními počítači v něm.  Další informace o vnořené virtualizaci a službách Azure Lab services najdete [v tématu Povolení vnořené virtualizace na virtuálním počítači šablony ve službě Azure Lab Services](how-to-enable-nested-virtualization-template-vm.md).

Tento článek popisuje, jak nastavit vnořené virtualizace na počítači šablony ve službě Azure Lab Services pomocí rolí a nástrojů Systému Windows přímo.  Existuje několik věcí, které potřebujete povolit třídě používat vnořené virtualizace.  Následující postup popíše, jak ručně nastavit šablonu počítače Služby Lab pomocí technologie Hyper-V.  Kroky jsou určeny pro Windows Server 2016 nebo Windows Server 2019.  

>[!IMPORTANT]
>Vyberte **Velké (vnořené virtualizace)** nebo **Střední (vnořené virtualizace)** pro velikost virtuálního počítače při vytváření testovacího prostředí.  Vnořená virtualizace nebude fungovat jinak.  

## <a name="enable-hyper-v-role"></a>Povolit roli Hyper-V

Následující kroky popisují akce potřebné k povolení technologie Hyper-V v systému Windows Server pomocí Správce serveru.  Jakmile bude instalace úspěšná, správce technologie Hyper-V bude k dispozici pro přidání, úpravu a odstranění klientských virtuálních počítačů.

1. Ve **Správci serveru**klikněte na stránce Řídicí panel na **přidat role a funkce**.
2. Na stránce **Než začnete** klikněte na **Další**.
3. Na stránce **Vybrat typ instalace** ponechejte výchozí výběr instalace založené na rolích nebo funkcích a klepněte na tlačítko **Další**.
4. Na stránce **Vybrat cílový server** vyberte vybrat server z fondu serverů.   Aktuální server již bude vybrán.  Klikněte na Další.
5. Na stránce **Vybrat role serveru** vyberte možnost **Hyper-V**.  
6. Zobrazí se automaticky otevírané okno **Průvodce přidáním rolí a funkcí.**  Vyberte **Zahrnout nástroje pro správu (pokud je k dispozici)**.  Klikněte na tlačítko **Přidat funkce.**
7. Na stránce **Vybrat role serveru** klikněte na **Další**.
8. Na **stránce Vybrat funkce**klepněte na tlačítko **Další**.
9. Na stránce **Hyper-V** klikněte na **Další**.
10. Na stránce **Vytvořit virtuální přepínače** přijměte výchozí hodnoty a klepněte na tlačítko **Další**.
11. Na stránce **Migrace virtuálního počítače** přijměte výchozí hodnoty a klepněte na tlačítko **Další**.
12. Na stránce **Výchozí obchody** přijměte výchozí hodnoty a klepněte na tlačítko **Další**.
13. Na stránce **Potvrdit výběr instalace** vyberte v případě potřeby automaticky **restartovat cílový server**.
14. Po zobrazení automaticky otevírané okno **Průvodce přidáním rolí a funkcí** klepněte na tlačítko **Ano**.
15. Klepněte na tlačítko **Instalovat**.
16. Počkejte na stránku **průběhu instalace,** která označuje, že role Hyper-V je dokončena.  Zařízení se může restartovat uprostřed instalace.
17. Klikněte na **Zavřít**.

## <a name="enable-dhcp-role"></a>Povolit roli DHCP

Všechny vytvořené klientské virtuální počítače Hyper-V potřebují IP adresu v síti NAT.  Síť NAT vytvoříme později.  Jedním ze způsobů, jak přiřadit adresy IP, je nastavit hostitele, v tomto případě šablonu virtuálního počítače v testovacím prostředí, jako server DHCP.  Níže jsou uvedeny kroky potřebné k povolení role DHCP.

1. Ve **Správci serveru**klikněte na stránce **Řídicí panel** na přidat role **a funkce**.
2. Na stránce **Než začnete** klikněte na **Další**.
3. Na stránce **Vybrat typ instalace** vyberte instalaci **založenou na rolích nebo funkcích** a klepněte na tlačítko **Další**.
4. Na stránce **Vybrat cílový server** vyberte aktuální server z fondu serverů a klepněte na tlačítko **Další**.
5. Na stránce **Vybrat role serveru** vyberte možnost Server **DHCP**.  
6. Zobrazí se automaticky otevírané okno **Průvodce přidáním rolí a funkcí.**  Vyberte **Zahrnout nástroje pro správu (pokud je k dispozici)**.  Klepněte na **tlačítko Přidat funkce**.

    >[!NOTE]
    >Může se zobrazit ověřovací chyba oznamující, že nebyly nalezeny žádné statické adresy IP.  Toto upozornění lze ignorovat pro náš scénář.

7. Na stránce **Vybrat role serveru** klikněte na **Další**.
8. Na stránce **Vybrat funkce** klikněte na **další**.
9. Na stránce **Server DHCP** klepněte na tlačítko **Další**.
10. Na stránce **Potvrdit vybrané možnosti instalace** klikněte na **Nainstalovat**.
11. Počkejte na **stránku průběhu instalace,** která označuje, že role DHCP je dokončena.
12. Klikněte na Zavřít.

## <a name="enable-routing-and-remote-access-role"></a>Povolit roli směrování a vzdáleného přístupu

1. Ve **Správci serveru**klikněte na stránce **Řídicí panel** na přidat role **a funkce**.
2. Na stránce **Než začnete** klikněte na **Další**.
3. Na stránce **Vybrat typ instalace** vyberte instalaci **založenou na rolích nebo funkcích** a klepněte na tlačítko **Další**.
4. Na stránce **Vybrat cílový server** vyberte aktuální server z fondu serverů a klepněte na tlačítko **Další**.
5. Na stránce **Vybrat role serveru** vyberte možnost Vzdálený **přístup**. Klikněte na tlačítko **OK**.
6. Na stránce **Vybrat funkce** klikněte na **další**.
7. Na stránce **Vzdálený přístup** klepněte na tlačítko **Další**.
8. Na stránce **Služba role** vyberte **Směrování**.
9. Zobrazí se automaticky otevírané okno **Průvodce přidáním rolí a funkcí.**  Vyberte **Zahrnout nástroje pro správu (pokud je k dispozici)**.  Klepněte na **tlačítko Přidat funkce**.
10. Klikněte na **Další**.
11. Na stránce **Web Server Role (IIS)** klikněte na **Další**.
12. Na stránce **Vybrat služby rolí** klikněte na **Další**.
13. Na stránce **Potvrdit vybrané možnosti instalace** klikněte na **Nainstalovat**.
14. Počkejte na stránku **průběhu instalace,** která označuje, že role vzdáleného přístupu je dokončena.  
15. Klikněte na **Zavřít**.

## <a name="create-virtual-nat-network"></a>Vytvoření virtuální sítě NAT

Nyní, když byly nainstalovány všechny potřebné role, je čas vytvořit síť NAT.  Proces vytváření bude zahrnovat vytvoření přepínače a samotné sítě NAT.  Síť NAT (překlad síťových adres) přiřadí veřejnou IP adresu skupině virtuálních počítačů v privátní síti, aby bylo možné připojení k internetu.  V našem případě bude skupina soukromých virtuálních virtuálních bylvnořovaných virtuálních bylin.  Síť NAT umožní vnořeným virtuálním mům vzájemnou komunikaci. Přepínač je síťové zařízení, které zpracovává příjem a směrování přenosů v síti.

### <a name="create-a-new-virtual-switch"></a>Vytvoření nového virtuálního přepínače

1. Otevřete **Správce technologie Hyper-V** z nástrojů pro správu systému Windows.
2. Vyberte aktuální server v levé navigační nabídce.
3. Klikněte na **Správce virtuálních přepínačů...** z nabídky **Akce** na pravé straně **správce technologie Hyper-V**.
4. V automaticky otevíraném prostředí **Správce virtuálních přepínačů** vyberte pro typ přepínače, který chcete vytvořit, možnost **Interní.**  Klepněte na **tlačítko Vytvořit virtuální přepínač**.
5. Pro nově vytvořený virtuální přepínač nastavte název na něco nezapomenutelného.  V tomto příkladu použijeme "LabServicesSwitch".  Klikněte na tlačítko **OK**.
6. Bude vytvořen nový síťový adaptér.  Název bude podobný 'vEthernet (LabServicesSwitch)'.  Chcete-li otevřít **ovládací panel**, klepněte na položku Síť **a Internet**, klepněte na tlačítko Zobrazit stav sítě **a úkoly**.  Vlevo klepněte na **tlačítko Změnit nastavení adaptéru**.

### <a name="create-a-nat-network"></a>Vytvoření sítě NAT

1. Otevřete nástroj **Směrování a vzdálený přístup** z nástrojů pro správu systému Windows.
2. Vyberte místní server na levé navigační stránce.
3. Zvolte **Konfigurovat akci** -> **a povolit směrování a vzdálený přístup**.
4. Po **zobrazení Průvodce instalací serveru směrování a vzdáleného přístupu** klepněte na tlačítko **Další**.
5. Na stránce **Konfigurace** vyberte konfiguraci **překladu síťových adres (NAT).**  Klikněte na **Další**.

    >[!WARNING]
    >Nevybírejte možnost "Přístup k virtuální privátní síti (VPN) a NAT.Not choose the 'Virtual private network (VPN) access and NAT' option.

6. Na stránce **Připojení k internetu na NAT** zvolte "Ethernet".  Nevybírejte připojení 'vEthernet (LabServicesSwitch)', které jsme vytvořili ve Správci Hyper-V. Klikněte na **Další**.
7. Na poslední stránce průvodce klikněte na **Dokončit.**
8. Po zobrazení dialogového okna **Spustit službu** klepněte na tlačítko **Spustit službu**.
9. Počkejte, až bude služba spuštěna.

## <a name="update-network-adapter-settings"></a>Aktualizace nastavení síťového adaptéru

Síťový adaptér bude přidružen k protokolu IP použitému pro výchozí adresu IP brány pro dříve vytvořenou síť NAT.  V tomto příkladu vytvoříme IP adresu 192.168.0.1 s maskou podsítě 255.255.255.0.  Budeme používat virtuální přepínač vytvořený dříve.

1. Otevřete **Ovládací panely**, klepněte na **položku Síť a Internet**, klepněte na tlačítko Zobrazit stav sítě a **úkoly**.
2. Vlevo klepněte na **tlačítko Změnit nastavení adaptéru**.  
3. V okně **Síťová připojení** poklepejte na 'vEthernet (LabServicesSwitch)' a zobrazilo dialogové okno podrobnosti stavu **vEthernet (LabServicesSwitch).**
4. Klepněte na tlačítko **Vlastnosti.**
5. Vyberte **položku Protokolu IP verze 4 (TCP/IPv4)** a klepněte na tlačítko **Vlastnosti.**
6. V dialogovém okně **Vlastnosti protokolu IP verze 4 (TCP/IPv4)** vyberte **použít následující adresu IP**.  Pro IP adresu zadejte 192.168.0.1. Maska podsítě zadejte hodnotu 255.255.255.0.  Výchozí bránu ponechejte prázdnou.  Ponechte servery DNS také prázdné.

    >[!NOTE]
    > Náš sortiment pro naši síť NAT bude v cidr notaci 192.168.0.0/24.  Tím se vytvoří rozsah použitelných IP adres od 192.168.0.1 do 192.168.0.254.  Podle konvence mají brány první IP adresu v rozsahu podsítě.

7. Klikněte na tlačítko OK.

## <a name="create-dhcp-scope"></a>Vytvořit obor DHCP

Následující kroky jsou instrukce pro přidání oboru DHCP.  V tomto článku je naše síť NAT 192.168.0.0/24 v zápisu CIDR.  Tím se vytvoří rozsah použitelných IP adres od 192.168.0.1 do 192.168.0.254.  Vytvořený obor musí být v tomto rozsahu použitelných adres s výjimkou adresy IP, která již byla vytvořena dříve.

1. Otevřete **Nástroje pro správu** a otevřete nástroj pro správu **DHCP.**
2. V nástroji **DHCP** rozbalte uzel pro aktuální server a vyberte **IPv4**.
3. V nabídce Akce zvolte **Nový obor...**
4. Po zobrazení **Průvodce novým oborem** klepněte na úvodní stránce na **tlačítko Další.** **Welcome**
5. Na stránce **Název oboru** zadejte "LabServicesDhcpScope" nebo něco jiného zapamatovatelné pro název.  Klikněte na **Další**.
6. Na stránce **Rozsah IP adres** zadejte následující hodnoty.

    - 192.168.0.100 pro počáteční IP adresu
    - 192.168.0.200 pro koncovou IP adresu
    - 24 pro délku
    - 255.255.255.0 pro masku podsítě

7. Klikněte na **Další**.
8. Na stránce **Přidat vyloučení a zpoždění** klikněte na **další**.
9. Na stránce **Doba trvání zapůjčení** klepněte na tlačítko **Další**.
10. Na stránce **Konfigurovat možnosti DHCP** vyberte **ano, chci tyto možnosti konfigurovat nyní**. Klikněte na **Další**.
11. Na **routeru (výchozí brána)**
12. Přidejte 192.168.0.1, pokud to ještě není hotovo. Klikněte na **Další**.
13. Na stránce **Název domény a Servery DNS** přidejte 168.63.129.16 jako IP adresu serveru DNS, pokud již není hotovo.  168.63.129.16 je IP adresa statického serveru DNS Azure. Klikněte na **Další**.
14. Na stránce **Wins Servers** klepněte na tlačítko **Další**.
15. Jedna na stránce **Aktivovat obor,** vyberte **Ano, chci tento obor aktivovat nyní**.  Klikněte na **Další**.
16. Na stránce **Průvodce dokončením nového oboru** klepněte na tlačítko **Dokončit**.

## <a name="conclusion"></a>Závěr

Nyní je váš počítač šablony připraven k vytvoření virtuálních počítačů Hyper-V.   Pokyny k vytvoření virtuálních počítačů [v technologii Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) najdete v tématu Vytvoření virtuálního počítače v hyperv.  Podívejte se také na [Centrum hodnocení společnosti Microsoft,](https://www.microsoft.com/evalcenter/) kde si můžete prohlédnout dostupné operační systémy a software.

## <a name="next-steps"></a>Další kroky

Další kroky jsou společné pro nastavení libovolného testovacího prostředí.

- [Přidávání uživatelů](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Nastavit kvótu](how-to-configure-student-usage.md#set-quotas-for-users)
- [Nastavení plánu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mailové registrační odkazy pro studenty](how-to-configure-student-usage.md#send-invitations-to-users)