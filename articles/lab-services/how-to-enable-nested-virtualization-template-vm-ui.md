---
title: Povolit vnořenou virtualizaci na virtuálním počítači šablony v Azure Lab Services (UI) | Microsoft Docs
description: Přečtěte si, jak vytvořit virtuální počítač šablony s několika virtuálními počítači v rámci.  Jinými slovy, povolit vnořenou virtualizaci na virtuálním počítači šablony v Azure Lab Services.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: f8135e11fb7b7ddb588ab3a8ed01227712072fd2
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647915"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-manually"></a>Povolit vnořenou virtualizaci na virtuálním počítači šablony v Azure Lab Services ručně

Vnořená virtualizace umožňuje vytvořit prostředí s několika virtuálními počítači v rámci virtuálních počítačů šablon testovacího prostředí. Publikování šablony poskytne každého uživatele v testovacím prostředí s virtuálním počítačem nastaveným s několika virtuálními počítači v rámci něj.  Další informace o vnořené virtualizaci a Azure Lab Services najdete v tématu [Povolení vnořené virtualizace na virtuálním počítači šablony v Azure Lab Services](how-to-enable-nested-virtualization-template-vm.md).

Tento článek popisuje, jak nastavit vnořenou virtualizaci na počítači šablony v Azure Lab Services přímo pomocí rolí a nástrojů systému Windows.  Aby mohla třída používat vnořenou virtualizaci, je potřeba provést několik věcí.  Následující postup popisuje, jak ručně nastavit šablonu počítače služby testovacího prostředí s technologií Hyper-V.  Kroky jsou určené pro Windows Server 2016 nebo Windows Server 2019.  

>[!IMPORTANT]
>Při vytváření testovacího prostředí vyberte možnost **Velká (vnořená virtualizace)** nebo **střední (vnořená virtualizace)** pro velikost virtuálního počítače.  Vnořená virtualizace nebude jinak fungovat.  

## <a name="enable-hyper-v-role"></a>Povolit roli technologie Hyper-V

Následující postup popisuje akce, které jsou potřebné k povolení technologie Hyper-V na Windows serveru, a to pomocí Správce serveru.  Po úspěšné instalaci bude Správce technologie Hyper-V k dispozici pro přidání, úpravu a odstranění klientských virtuálních počítačů.

1. V **Správce serveru** na stránce řídicí panel klikněte na **Přidat role a funkce**.
2. Na stránce **Než začnete** klikněte na **Další**.
3. Na stránce **Vybrat typ instalace** ponechte výchozí možnost instalace na základě rolí nebo na základě funkcí a pak klikněte na **Další**.
4. Na stránce **Vybrat cílový server** vyberte možnost vyberte server z fondu serverů.   Aktuální server bude již vybrán.  Klikněte na Další.
5. Na stránce **Vybrat role serveru** vyberte možnost **Hyper-V**.  
6. Zobrazí se automaticky otevírané okno **Průvodce přidáním rolí a funkcí** .  Vyberte možnost **Zahrnout nástroje pro správu (Pokud je k dispozici)**.  Klikněte na tlačítko **Přidat funkce** .
7. Na stránce **Vybrat role serveru** klikněte na **Další**.
8. Na **stránce Vybrat funkce** klikněte na **Další**.
9. Na stránce **Hyper-V** klikněte na **Další**.
10. Na stránce **vytvořit virtuální přepínače** přijměte výchozí hodnoty a klikněte na **Další**.
11. Na stránce **migrace virtuálního počítače** přijměte výchozí nastavení a klikněte na **Další**.
12. Na stránce **výchozí úložiště** přijměte výchozí hodnoty a klikněte na **Další**.
13. Na stránce **Potvrdit vybrané možnosti instalace** vyberte v **případě potřeby cílový server automaticky restartovat**.
14. Když se zobrazí automaticky otevírané okno **Průvodce přidáním rolí a funkcí** , klikněte na **Ano**.
15. Klikněte na **Install** (Nainstalovat).
16. Počkejte na stránku **průběh instalace** , která indikuje, že role Hyper-V je dokončená.  Počítač se v průběhu instalace může restartovat.
17. Klikněte na **Zavřít**.

## <a name="enable-dhcp-role"></a>Povolit roli DHCP

Všechny vytvořené virtuální počítače klienta Hyper-V vyžadují IP adresu v síti NAT.  Síť překladu adres (NAT) vytvoříme později.  Jedním ze způsobů, jak přiřadit IP adresy, je nastavit hostitele v tomto případě šablonu virtuálního počítače testovacího prostředí jako server DHCP.  Níže jsou uvedené kroky potřebné k povolení role DHCP.

1. V **Správce serveru** na stránce **řídicí panel** klikněte na **Přidat role a funkce**.
2. Na stránce **Než začnete** klikněte na **Další**.
3. Na stránce **Vybrat typ instalace** vyberte instalace na **základě rolí nebo na základě funkcí** a pak klikněte na **Další**.
4. Na stránce **Vybrat cílový server** vyberte aktuální server z fondu serverů a pak klikněte na **Další**.
5. Na stránce **Vybrat role serveru** vyberte **DHCP Server**.  
6. Zobrazí se automaticky otevírané okno **Průvodce přidáním rolí a funkcí** .  Vyberte možnost **Zahrnout nástroje pro správu (Pokud je k dispozici)**.  Klikněte na **Přidat funkce**.

    >[!NOTE]
    >Může se zobrazit chyba ověření informující o tom, že nebyly nalezeny žádné statické IP adresy.  Toto upozornění se dá pro náš scénář ignorovat.

7. Na stránce **Vybrat role serveru** klikněte na **Další**.
8. Na stránce **Vybrat funkce** klikněte na **Další**.
9. Na stránce **Server DHCP** klikněte na **Další**.
10. Na stránce **Potvrdit vybrané možnosti instalace** klikněte na **Nainstalovat**.
11. Počkejte na **stránku průběh instalace** , která indikuje, že se role DHCP dokončila.
12. Klikněte na Zavřít.

## <a name="enable-routing-and-remote-access-role"></a>Povolit roli směrování a vzdálený přístup

1. V **Správce serveru** na stránce **řídicí panel** klikněte na **Přidat role a funkce**.
2. Na stránce **Než začnete** klikněte na **Další**.
3. Na stránce **Vybrat typ instalace** vyberte instalace na **základě rolí nebo na základě funkcí** a pak klikněte na **Další**.
4. Na stránce **Vybrat cílový server** vyberte aktuální server z fondu serverů a pak klikněte na **Další**.
5. Na stránce **Vybrat role serveru** vyberte **vzdálený přístup**. Klikněte na **OK**.
6. Na stránce **Vybrat funkce** klikněte na **Další**.
7. Na stránce **vzdálený přístup** klikněte na **Další**.
8. Na stránce **služby rolí** vyberte **Směrování**.
9. Zobrazí se automaticky otevírané okno **Průvodce přidáním rolí a funkcí** .  Vyberte možnost **Zahrnout nástroje pro správu (Pokud je k dispozici)**.  Klikněte na **Přidat funkce**.
10. Klikněte na **Next** (Další).
11. Na stránce **Web Server Role (IIS)** klikněte na **Další**.
12. Na stránce **Vybrat služby rolí** klikněte na **Další**.
13. Na stránce **Potvrdit vybrané možnosti instalace** klikněte na **Nainstalovat**.
14. Počkejte na stránku **průběh instalace** , aby označovala, že byla role vzdáleného přístupu dokončena.  
15. Klikněte na **Zavřít**.

## <a name="create-virtual-nat-network"></a>Vytvořit virtuální síť NAT

Teď, když jsou nainstalované všechny potřebné role, je čas vytvořit síť NAT.  Proces vytváření bude zahrnovat vytvoření přepínače a sítě NAT samotné.  Síť NAT (překlad síťových adres) přiřadí skupině virtuálních počítačů v privátní síti veřejnou IP adresu, která umožňuje připojení k Internetu.  V našem případě budou skupiny privátních virtuálních počítačů vnořené virtuální počítače.  Síť NAT umožní všem vnořeným virtuálním počítačům komunikovat mezi sebou. Přepínač je síťové zařízení, které zpracovává příjem a směrování provozu v síti.

### <a name="create-a-new-virtual-switch"></a>Vytvořit nový virtuální přepínač

1. Otevřete **Správce technologie Hyper-V** z nástrojů pro správu systému Windows.
2. V navigační nabídce vlevo vyberte aktuální server.
3. Klikněte na **Správce virtuálního přepínače...** v nabídce **Akce** na pravé straně **Správce technologie Hyper-V**.
4. V místní nabídce **Správce virtuálních přepínačů** vyberte **interní** pro typ přepínače, který chcete vytvořit.  Klikněte na **vytvořit virtuální přepínač**.
5. U nově vytvořeného virtuálního přepínače nastavte název na něco, co se zapamatovat.  V tomto příkladu použijeme "LabServicesSwitch".  Klikněte na **OK**.
6. Vytvoří se nový síťový adaptér.  Název bude podobný jako vEthernet (LabServicesSwitch).  Chcete-li ověřit otevření **ovládacích panelů**, klikněte na položku **síť a Internet**, klikněte na možnost **Zobrazit stav sítě a úlohy**.  Na levé straně klikněte na **změnit nastavení adaptéru**.

### <a name="create-a-nat-network"></a>Vytvoření sítě NAT

1. Z nástrojů pro správu systému Windows otevřete nástroj pro **Směrování a vzdálený přístup** .
2. Na levé navigační stránce vyberte místní server.
3. Vyberte **Akce**  ->  **Konfigurace a povolit směrování a vzdálený přístup**.
4. Když se zobrazí **Průvodce instalací serveru Směrování a vzdálený přístup** , klikněte na **Další**.
5. Na stránce **Konfigurace** vyberte konfigurace **překladu adres (NAT)** .  Klikněte na **Next** (Další).

    >[!WARNING]
    >Nevybírejte možnost přístup k virtuální privátní síti (VPN) a překlad adres (NAT).

6. Na stránce **Internet Connection NAT** klikněte na síť Ethernet.  Nevybírejte připojení vEthernet (LabServicesSwitch), které jsme vytvořili ve Správci technologie Hyper-V. Klikněte na **Next** (Další).
7. Na poslední stránce průvodce klikněte na **Dokončit** .
8. Po zobrazení dialogového okna **Spustit službu** klikněte na **Spustit službu**.
9. Počkejte na spuštění služby.

## <a name="update-network-adapter-settings"></a>Aktualizace nastavení síťového adaptéru

Síťový adaptér bude přidružen k IP adrese používané pro výchozí bránu IP pro síť NAT, kterou jste vytvořili dříve.  V tomto příkladu vytvoříme IP adresu 192.168.0.1 s maskou podsítě 255.255.255.0.  Použijeme dříve vytvořený virtuální přepínač.

1. Otevřete **Ovládací panely**, klikněte na **síť a Internet**, klikněte na **Zobrazit stav sítě a úlohy**.
2. Na levé straně klikněte na **změnit nastavení adaptéru**.  
3. V okně **Síťová připojení** poklikejte na VEthernet (LabServicesSwitch) a zobrazí se dialogové okno Podrobnosti o **stavu vEthernet (LabServicesSwitch)** .
4. Klikněte na tlačítko **vlastnosti** .
5. Vyberte položku **Internet Protocol verze 4 (TCP/IPv4)** a klikněte na tlačítko **vlastnosti** .
6. V dialogovém okně **vlastnosti Internet Protocol verze 4 (TCP/IPv4)** vyberte **použít následující IP adresu**.  V poli IP adresa zadejte 192.168.0.1. V poli Maska podsítě zadejte 255.255.255.0.  Nechte výchozí bránu prázdnou.  Servery DNS ponechte prázdné i.

    >[!NOTE]
    > Náš rozsah pro naši síť NAT bude v notaci CIDR, 192.168.0.0/24.  Tím se vytvoří rozsah použitelných IP adres z 192.168.0.1 do 192.168.0.254.  Podle konvence mají brány první IP adresu v rozsahu podsítě.

7. Klikněte na tlačítko OK.

## <a name="create-dhcp-scope"></a>Vytvořit obor DHCP

Následující kroky jsou pokyny k přidání oboru DHCP.  V tomto článku je naše síť NAT 192.168.0.0/24 v zápisu CIDR.  Tím se vytvoří rozsah použitelných IP adres z 192.168.0.1 do 192.168.0.254.  Rozsah, který se vytvořil, musí být v rozsahu použitelných adres, kromě IP adresy, která už je dřív vytvořená.

1. Otevřete **Nástroje pro správu** a otevřete nástroj pro správu **služby DHCP** .
2. V nástroji **DHCP** rozbalte uzel pro aktuální server a vyberte **IPv4**.
3. V nabídce Akce klikněte na možnost **Nový obor...**
4. Po zobrazení **Průvodce vytvořením oboru** klikněte na **úvodní** stránce na tlačítko **Další** .
5. Na stránce **název oboru** zadejte "LabServicesDhcpScope" nebo něco jiného, co je pro jméno zapamatovatelné.  Klikněte na **Next** (Další).
6. Na stránce **Rozsah IP adres** zadejte následující hodnoty.

    - 192.168.0.100 pro počáteční IP adresu
    - 192.168.0.200 pro koncovou IP adresu
    - 24 pro délku
    - 255.255.255.0 pro masku podsítě

7. Klikněte na **Next** (Další).
8. Na stránce **Přidat vyloučení a zpoždění** klikněte na **Další**.
9. Na stránce **trvání zapůjčení** klikněte na **Další**.
10. Na stránce **Konfigurovat možnosti DHCP** vyberte možnost **Ano, chci nyní tyto možnosti nakonfigurovat**. Klikněte na **Next** (Další).
11. Ve **směrovači (výchozí brána)**
12. Přidejte 192.168.0.1, pokud ještě neproběhla. Klikněte na **Next** (Další).
13. Na stránce **název domény a servery DNS** přidejte 168.63.129.16 jako IP adresu serveru DNS, pokud jste to ještě neudělali.  168.63.129.16 je IP adresa pro statický server DNS Azure. Klikněte na **Next** (Další).
14. Na stránce **servery WINS** klikněte na **Další**.
15. Na stránce **aktivovat obor** vyberte **Ano, chci teď tento rozsah aktivovat**.  Klikněte na **Next** (Další).
16. Na stránce **dokončení průvodce** vytvořením oboru klikněte na tlačítko **Dokončit**.

## <a name="conclusion"></a>Závěr

Teď je váš počítač šablon připravený k vytváření virtuálních počítačů s technologií Hyper-V.   Pokyny k vytváření virtuálních počítačů s technologií Hyper-V najdete v tématu [Vytvoření virtuálního počítače v technologii Hyper-v](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) .  Podívejte se také na [Zkušební centrum Microsoftu](https://www.microsoft.com/evalcenter/) , které vám umožní zjistit dostupné operační systémy a software.

## <a name="next-steps"></a>Další kroky

Další kroky jsou běžné pro nastavení testovacího prostředí.

- [Přidávání uživatelů](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Nastavit kvótu](how-to-configure-student-usage.md#set-quotas-for-users)
- [Nastavit plán](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Odkazy na registraci e-mailu studentům](how-to-configure-student-usage.md#send-invitations-to-users)