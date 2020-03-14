---
title: Zařízení Azure Migrate
description: Poskytuje přehled zařízení Azure Migrate používaných při posuzování a migraci serveru.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 1b1e35c3b7a9d98e57ec4261f6f913c370bbb365
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269572"
---
# <a name="azure-migrate-appliance"></a>Zařízení Azure Migrate

Tento článek popisuje zařízení Azure Migrate. Zařízení nasazujete při použití [Azure Migrate: Nástroj pro vyhodnocení serveru](migrate-services-overview.md#azure-migrate-server-assessment-tool) , který umožňuje zjišťovat a vyhodnocovat aplikace, infrastrukturu a úlohy pro migraci do Microsoft Azure. Zařízení se používá i při migraci virtuálních počítačů VMware do Azure pomocí [Azure Migrate: posouzení serveru](migrate-services-overview.md#azure-migrate-server-migration-tool) s [migrací bez agenta](server-migrate-overview.md).

## <a name="appliance-overview"></a>Přehled zařízení

Zařízení Azure Migrate se používá v následujících scénářích.

**Scénář** | **Nástroj** | **Používá se pro** 
--- | --- | ---
Virtuální počítač VMware | Azure Migrate: posouzení serveru<br/><br/> Azure Migrate: Migrace serveru | Zjistit virtuální počítače VMware<br/><br/> Zjistit aplikace a závislosti počítačů<br/><br/> Shromažďovat metadata počítače a metadata výkonu pro posouzení.<br/><br/> Replikace virtuálních počítačů VMware s migrací bez agentů
Virtuální počítač Hyper-V | Azure Migrate: posouzení serveru | Zjistit virtuální počítače Hyper-V<br/><br/> Shromažďovat metadata počítače a metadata výkonu pro posouzení.
Fyzický počítač |  Azure Migrate: posouzení serveru |  Zjistit fyzické servery<br/><br/> Shromažďovat metadata počítače a metadata výkonu pro posouzení.

## <a name="appliance---vmware"></a>Zařízení – VMware 

**Požadavek** | **VMware** 
--- | ---
**Formát stažení** | . VAJÍČK 
**Odkaz ke stažení** | https://aka.ms/migrate/appliance/vmware 
**Velikost ke stažení** | 11,2 GB
**Průkaz** | Stažená šablona zařízení je součástí zkušební licence Windows Server 2016, která je platná po dobu 180 dnů. Pokud se zkušební období blíží vypršení platnosti, doporučujeme, abyste si stáhli a nasadili nové zařízení nebo aktivovali licenci k operačnímu systému virtuálního počítače zařízení.
**Nasazení** | Zařízení nasadíte jako virtuální počítač VMware. Potřebujete dostatek prostředků na vCenter Server k přidělení virtuálního počítače s 32 GB paměti RAM, 8 vCPU, přibližně 80 GB diskového úložiště a externím virtuálním přepínačem.<br/> Zařízení vyžaduje přístup k Internetu, a to buď přímo, nebo prostřednictvím proxy serveru.<br/> Zařízení se může připojit k jednomu vCenter Server.
**Hardware** | Prostředky v vCenter pro přidělení virtuálního počítače s 32 GB paměti RAM 8 vCPU, přibližně 80 GB diskového úložiště a externím virtuálním přepínačem. 
**Hodnota hash** | Zaškrtnout [](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware#verify-security)
**Server vCenter/hostitel** | Virtuální počítač zařízení musí být nasazen na hostiteli ESXi, na kterém běží verze 5,5 nebo novější.<br/><br/> vCenter Server se spouští 5,5, 6,0, 6,5 nebo 6,7.
**Azure Migrate projekt** | Zařízení může být přidruženo k jednomu projektu. <br/> K jednomu projektu může být přidružen libovolný počet zařízení.<br/> 
**Rozpoznávání** | Zařízení může na vCenter Server zjistit až 10 000 virtuálních počítačů VMware.<br/> Zařízení se může připojit k jednomu vCenter Server.
**Součásti zařízení** | Aplikace pro správu: webová aplikace v zařízení pro vstup uživatele během nasazování.<br/> Agent zjišťování: shromažďuje data konfigurace počítače.<br/> Agent hodnocení: shromažďování údajů o výkonu.<br/> DRA: orchestruje replikaci virtuálních počítačů a koordinuje komunikaci mezi počítači a Azure.<br/> Brána: odesílá replikovaná data do Azure.<br/> Služba automatické aktualizace: aktualizovat součásti (spouští se každých 24 hodin).
**VDDK (migrace bez agenta)** | Pokud při migraci Azure Migrate serveru používáte migraci bez agenta, musí být na virtuálním počítači zařízení nainstalovaná VMware vSphere VDDK.


## <a name="appliance---hyper-v"></a>Zařízení – Hyper-V

**Požadavek** | **Hyper-V** 
--- | ---
**Formát stažení** | Složka zip (s virtuálním pevným diskem)
**Odkaz ke stažení** | https://aka.ms/migrate/appliance/hyperv 
**Velikost ke stažení** | 10 GB
**Průkaz** | Stažená šablona zařízení je součástí zkušební licence Windows Server 2016, která je platná po dobu 180 dnů. Pokud se zkušební období blíží vypršení platnosti, doporučujeme, abyste si stáhli a nasadili nové zařízení nebo aktivovali licenci k operačnímu systému virtuálního počítače zařízení.
**Nasazení zařízení**   |  Zařízení nasadíte jako virtuální počítač Hyper-V.<br/> Virtuální počítač zařízení, který poskytuje Azure Migrate, je virtuální počítač Hyper-V verze 5,0.<br/> Na hostiteli Hyper-V musí běžet Windows Server 2012 R2 nebo novější.<br/> Hostitel potřebuje dostatek místa pro přidělení 16 GB paměti RAM, 8 vCPU, přibližně 80 GB úložného prostoru a externí přepínač pro virtuální počítač zařízení.<br/> Zařízení potřebuje statickou nebo dynamickou IP adresu a přístup k Internetu.
**Hardware** | Prostředky na hostiteli Hyper-V, které přidělují 16 GB paměti RAM, 8 vCPU, přibližně 80 GB úložného prostoru a externím přepínači pro virtuální počítač zařízení.
**Hodnota hash** | Zaškrtnout [](https://docs.microsoft.com/azure/migrate/tutorial-assess-hyper-v#verify-security)
**Hostitel Hyper-V** | Se systémem Windows Server 2012 R2 nebo novějším.
**Azure Migrate projekt** | Zařízení může být přidruženo k jednomu projektu. <br/> K jednomu projektu může být přidružen libovolný počet zařízení.<br/> 
**Rozpoznávání** | Zařízení může zjistit až 5000 virtuálních počítačů Hyper-V.<br/> Zařízení se může připojit k až 300 hostitelům Hyper-V.
**Součásti zařízení** | Aplikace pro správu: webová aplikace v zařízení pro vstup uživatele během nasazování.<br/> Agent zjišťování: shromažďuje data konfigurace počítače.<br/> Agent hodnocení: shromažďování údajů o výkonu.<br/>  Služba automatické aktualizace: aktualizovat součásti (spouští se každých 24 hodin).


## <a name="appliance---physical"></a>Zařízení – fyzické

**Požadavek** | **Skutečný** 
--- | ---
**Formát stažení** | Zip složka (pomocí skriptu instalačního programu založeného na prostředí PowerShell)
**Odkaz ke stažení** | [Odkaz ke stažení](https://go.microsoft.com/fwlink/?linkid=2105112)
**Velikost ke stažení** | 59,7 MB
**Hardware** | Vyhrazený fyzický počítač nebo použít virtuální počítač. Počítač, na kterém běží zařízení, potřebuje 16 GB paměti RAM, 8 vCPU, přibližně 80 GB úložného prostoru a externí přepínač.<br/> Zařízení potřebuje statickou nebo dynamickou IP adresu a přístup k Internetu.
**Hodnota hash** | Zaškrtnout [](https://docs.microsoft.com/azure/migrate/tutorial-assess-physical#verify-security)
**Operační systém** | Počítač zařízení by měl běžet s Windows serverem 2016. 
**Nasazení zařízení**   |  Skript instalačního programu zařízení se stáhne z portálu (ve složce zip). <br/> Rozbalte složku a spusťte PowerShellový skript (AzureMigrateInstaller. ps1).
**Rozpoznávání** | Zařízení může zjistit až 250 fyzických serverů.
**Součásti zařízení** | Aplikace pro správu: webová aplikace v zařízení pro vstup uživatele během nasazování.<br/> Agent zjišťování: shromažďuje data konfigurace počítače.<br/> Agent hodnocení: shromažďování údajů o výkonu.<br/>  Služba automatické aktualizace: aktualizovat součásti (spouští se každých 24 hodin).


## <a name="url-access"></a>Přístup URL

Zařízení Azure Migrate potřebuje připojení k Internetu.

- Když zařízení nasadíte, Azure Migrate provede kontrolu připojení k adresám URL, které jsou shrnuté v následující tabulce.
- Pokud pro připojení k Internetu používáte proxy server založený na adrese URL, povolte přístup k těmto adresám URL a ujistěte se, že proxy překládá všechny záznamy CNAME přijaté při vyhledávání adres URL.

**Adresa URL** | **Podrobnosti**  
--- | --- |
*.portal.azure.com  | Přejděte na Azure Portal.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com | Přihlaste se ke svému předplatnému Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Vytvořte pro zařízení služby Active Directory, které budou komunikovat s Azure Migrate.
management.azure.com | Vytvořte pro zařízení služby Active Directory, které budou komunikovat se službou Azure Migrate.
dc.services.visualstudio.com | Nahrávat protokoly aplikací používané pro interní monitorování
*.vault.azure.net | Správa tajných kódů v Azure Key Vault.
aka.ms/* | Povolí přístup k odkazům. Používá se k aktualizaci Azure Migrate zařízení.
download.microsoft.com/download | Povolí stahování ze služby Stažení softwaru společnosti Microsoft.
*.servicebus.windows.net | Komunikace mezi zařízením a službou Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Připojte se k adresám URL služby Azure Migrate.
*.hypervrecoverymanager.windowsazure.com | **Používá se pro migraci bez agentů VMware.**<br/><br/> Připojte se k adresám URL služby Azure Migrate.
*.blob.core.windows.net |  **Používá se pro migraci bez agentů VMware.**<br/><br/>Nahrajte data do úložiště pro migraci.




## <a name="collected-data---vmware"></a>Shromážděná data – VMware

### <a name="collected-performance-data-vmware"></a>Shromážděná data o výkonu – VMware

Tady je údaje o výkonu virtuálních počítačů VMware, které zařízení shromažďuje a odesílá do Azure.

**Data** | **Objektů** | **Dopad posouzení**
--- | --- | ---
Využití procesoru | CPU. Usage. Average | Doporučená velikost virtuálního počítače/náklady
Využití paměti | mem.usage.average | Doporučená velikost virtuálního počítače/náklady
Propustnost čtení z disku (MB za sekundu) | virtualDisk.read.average | Výpočet velikosti disku, nákladů na úložiště, velikosti virtuálního počítače
Propustnost zápisů na disk (MB za sekundu) | virtualDisk.write.average | Výpočet velikosti disku, nákladů na úložiště, velikosti virtuálního počítače
Operace čtení z disku za sekundu | virtualDisk.numberReadAveraged.average | Výpočet velikosti disku, nákladů na úložiště, velikosti virtuálního počítače
Počet operací zápisu na disk za sekundu | virtualDisk. numberWriteAveraged. Average  | Výpočet velikosti disku, nákladů na úložiště, velikosti virtuálního počítače
Propustnost čtení síťových adaptérů (MB za sekundu) | net.received.average | Výpočet pro velikost virtuálního počítače
Síťová karta zapisuje propustnost (MB za sekundu) | net.transmitted.average  |Výpočet pro velikost virtuálního počítače


### <a name="collected-metadata-vmware"></a>Shromážděná metadata – VMware

> [!NOTE]
> Metadata zjištěná zařízením Azure Migrate se používají k usnadnění správné velikosti vašich aplikací při jejich migraci do Azure, k provedení analýzy vhodnosti Azure, analýze závislostí aplikací a plánování nákladů. Společnost Microsoft nepoužívá tato data ve vztahu k auditu dodržování předpisů v licencích.

Tady je úplný seznam metadat virtuálních počítačů VMware, které zařízení shromažďuje a odesílá do Azure.

**Data** | **Objektů**
--- | --- 
**Podrobnosti o počítači** | 
ID VIRTUÁLNÍHO POČÍTAČE | vm.Config.InstanceUuid 
název virtuálního počítače | síť. Config.Name
ID vCenter Server | VMwareClient. instance. UUID
Popis virtuálního počítače | síť. Summary. config. Annotation
Název licenčního produktu | síť. Client. ServiceContent. about. LicenseProductName
Typ operačního systému | síť. SummaryConfig.GuestFullName
Typ spouštění | síť. Config. firmware
Počet jader | síť. Config. hardware. NumCPU
Paměť (MB) | síť. Config. hardware. MemoryMB
Počet disků | síť. Config. hardware. Device. ToList – (). FindAll (x = > je VirtualDisk). Count
Seznam velikostí disků | síť. Config. hardware. Device. ToList – (). FindAll (x = > je VirtualDisk)
Seznam síťových adaptérů | síť. Config. hardware. Device. ToList – (). FindAll (x = > je VirtualEthernet). Count
Využití procesoru | CPU. Usage. Average
Využití paměti |mem.usage.average
**Podrobnosti o jednotlivých discích** | 
Hodnota klíč disku | disk. Zkrat
Dikunit číslo | disk. UnitNumber
Hodnota klíče řadiče disku | disk. ControllerKey. Value
Zřízené gigabajty | virtualDisk. DeviceInfo. Summary
Název disku | Hodnota generovaná pomocí disku UnitNumber, disk. Klíč, disk. ControllerKey. VAlue
Operace čtení za sekundu | virtualDisk.numberReadAveraged.average
Operace zápisu za sekundu | virtualDisk. numberWriteAveraged. Average
Propustnost čtení (MB za sekundu) | virtualDisk.read.average
Propustnost zápisu (MB za sekundu) | virtualDisk.write.average
**Podrobnosti na NIC** | 
Název síťového adaptéru | síťových. Zkrat
Adresa MAC | ((VirtualEthernetCard)nic).MacAddress
IPv4 adresy | síť. Guest.Net
IPv6 adresy | síť. Guest.Net
Propustnost čtení (MB za sekundu) | net.received.average
Propustnost zápisu (MB za sekundu) | net.transmitted.average
**Podrobnosti o cestě inventáře** | 
Název | container.GetType().Name
Typ podřízeného objektu | container.ChildType
Referenční informace | vnitřního. MoRef
Podrobnosti nadřazené položky | Kontejner. Parent
Podrobnosti složky na virtuální počítač | ((Folder)container).ChildEntity.Type
Podrobnosti datového centra na virtuální počítač | ((Datacenter)container).VmFolder
Podrobnosti datacentra na složku hostitelů | ((Datacenter)container).HostFolder
Podrobnosti o clusteru na hostitele | ((ClusterComputeResource) kontejner). Provoz
Podrobnosti o hostiteli na virtuálním počítači | ((HostSystem)container).VM

## <a name="collected-data---hyper-v"></a>Shromážděná data – Hyper-V

### <a name="collected-performance-data-hyper-v"></a>Shromážděná data o výkonu – Hyper-V

> [!NOTE]
> Metadata zjištěná zařízením Azure Migrate se používají k usnadnění správné velikosti vašich aplikací při jejich migraci do Azure, k provedení analýzy vhodnosti Azure, analýze závislostí aplikací a plánování nákladů. Společnost Microsoft nepoužívá tato data ve vztahu k auditu dodržování předpisů v licencích.

Tady je údaje o výkonu virtuálního počítače Hyper, které zařízení shromažďuje a odesílá do Azure.

**Třída čítače výkonu** | **Objektů** | **Dopad posouzení**
--- | --- | ---
Virtuální procesor hypervisoru technologie Hyper-V | % Doby běhu hosta | Doporučená velikost virtuálního počítače/náklady
Hyper-V Dynamická paměť virtuální počítač | Aktuální tlak (%)<br/> Fyzická paměť viditelná pro hosta (MB) | Doporučená velikost virtuálního počítače/náklady
Virtuální úložné zařízení Hyper-V | Přečtené bajty za sekundu | Výpočet velikosti disku, nákladů na úložiště, velikosti virtuálního počítače
Virtuální úložné zařízení Hyper-V | Bajty zápisu za sekundu | Výpočet velikosti disku, nákladů na úložiště, velikosti virtuálního počítače
Hyper-V Virtual Network adaptér | Přijaté bajty za sekundu | Výpočet pro velikost virtuálního počítače
Hyper-V Virtual Network adaptér | Odeslané bajty za sekundu | Výpočet pro velikost virtuálního počítače

- Využití CPU je součtem veškerého využití pro všechny virtuální procesory připojené k virtuálnímu počítači.
- Využití paměti je (aktuální tlak × viditelnost fyzické paměti hosta)/100.
- Hodnoty využití disku a sítě se shromažďují ze seznamu čítačů výkonu technologie Hyper-V.

### <a name="collected-metadata-hyper-v"></a>Shromážděná metadata – Hyper-V

Tady je úplný seznam metadat virtuálních počítačů Hyper-V, které zařízení shromažďuje a odesílá do Azure.

**Data** | **Třída WMI** | **Vlastnost třídy WMI**
--- | --- | ---
**Podrobnosti o počítači** | 
Sériové číslo systému BIOS _ Msvm_BIOSElement | BIOSSerialNumber
Typ virtuálního počítače (FIN 1 nebo 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Zobrazovaný název virtuálního počítače | Msvm_VirtualSystemSettingData | ElementName
Verze virtuálního počítače | Msvm_ProcessorSettingData | VirtualQuantity
Paměť (bajty) | Msvm_MemorySettingData | VirtualQuantity
Maximální velikost paměti, kterou může virtuální počítač spotřebovat | Msvm_MemorySettingData | Omezení
Dynamická paměť je povolena | Msvm_MemorySettingData | DynamicMemoryEnabled
Název/verze operačního systému/plně kvalifikovaný název domény | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems data o názvech
Stav napájení virtuálního počítače | Msvm_ComputerSystem | EnabledState
**Podrobnosti o jednotlivých discích** | 
Identifikátor disku | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Typ virtuálního pevného disku | Msvm_VirtualHardDiskSettingData | Typ
Velikost virtuálního pevného disku | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Nadřazený virtuální pevný disk | Msvm_VirtualHardDiskSettingData | ParentPath
**Podrobnosti na NIC** | 
IP adresy (syntetické síťové adaptéry) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
Protokol DHCP povolen (syntetické síťové adaptéry) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
ID síťové karty (syntetické síťové adaptéry) | Msvm_SyntheticEthernetPortSettingData | InstanceID
Adresa MAC síťové karty (syntetické síťové adaptéry) | Msvm_SyntheticEthernetPortSettingData | Adresa
ID síťové karty (starší síťové karty) | MsvmEmulatedEthernetPortSetting data | InstanceID
ID MAC síťové karty (starší síťové karty) | MsvmEmulatedEthernetPortSetting data | Adresa




## <a name="discovery-and-collection-process"></a>Proces zjišťování a shromažďování

Zařízení komunikuje s vCenter servery a hostiteli nebo clustery Hyper-V pomocí následujícího procesu.

1. **Spustit zjišťování**:
    - Když zahájíte zjišťování na zařízení Hyper-V, komunikuje s hostiteli Hyper-V na portech WinRM 5985 (HTTP) a 5986 (HTTPS).
    - Když spustíte zjišťování na zařízení VMware, ve výchozím nastavení komunikuje s vCenter serverem na portu TCP 443. Pokud Server vCenter naslouchá na jiném portu, můžete ho nakonfigurovat ve webové aplikaci zařízení.
2. **Shromážděte metadata a data o výkonu**:
    - Zařízení používá relaci model CIM (Common Information Model) (CIM) ke shromažďování dat virtuálních počítačů Hyper-V z hostitele Hyper-V na portech 5985 a 5986.
    - Zařízení ve výchozím nastavení komunikuje s portem 443, aby mohla shromažďovat data virtuálních počítačů VMware z vCenter Server.
3. **Odeslat data**: zařízení odesílá shromážděná data pro Azure Migrate posouzení serveru a migraci Azure Migrate serveru přes SSL port 443. Zařízení se může připojit k Azure přes Internet nebo můžete použít ExpressRoute s veřejným partnerským vztahem/Microsoftu.
    - Pro data o výkonu zařízení shromažďuje data o využití v reálném čase.
        - Údaje o výkonu se shromažďují každých 20 sekund pro VMware a každých 30 sekund pro Hyper-V pro každou metriku výkonu.
        - Shromážděná data jsou zahrnuta k vytvoření jednoho datového bodu po dobu 10 minut.
        - Hodnota maximálního využití se vybere ze všech 20/30 sekundových datových bodů a pošle se do Azure pro výpočet vyhodnocení.
        - Na základě hodnoty percentilu uvedené ve vlastnostech posouzení (50/90/95./99) se ve vzestupném pořadí seřadí body o deseti minutách a příslušná hodnota percentilu se použije k výpočtu posouzení.
    - U migrace serveru začne zařízení shromažďovat data virtuálních počítačů a replikuje je do Azure.
4. **Vyhodnocení a migrace**: teď můžete z metadat shromážděných zařízením vytvořit posouzení pomocí Azure Migrate posouzení serveru. Kromě toho můžete také začít migrovat virtuální počítače VMware pomocí migrace serveru Azure Migrate pro orchestraci replikace virtuálních počítačů bez agentů.


![Architektura](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>Upgrady zařízení

Zařízení se upgraduje, protože Azure Migrate agenti, kteří běží na zařízení, se aktualizují.

- K tomu dochází automaticky, protože ve výchozím nastavení je automaticky povolena automatická aktualizace na zařízení.
- Toto výchozí nastavení můžete změnit tak, aby se agenti aktualizovaly ručně.
- Automatické aktualizace zakážete tak, že přejdete do editoru registru > HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\AzureAppliance a nastavíte klíč registru-"AutoUpdate" na hodnotu 0 (DWORD).
 
### <a name="set-agent-updates-to-manual"></a>Nastavit aktualizace agenta na ruční

V případě ručních aktualizací nezapomeňte aktualizovat všechny agenty na zařízení ve stejnou dobu, a to pomocí tlačítka **aktualizovat** pro každého zastaralého agenta na zařízení. Nastavení aktualizace můžete kdykoli přepnout zpátky na automatické aktualizace.

## <a name="next-steps"></a>Další kroky

[Přečtěte si, jak](tutorial-assess-vmware.md#set-up-the-appliance-vm) nastavit zařízení pro VMware.
[Přečtěte si, jak](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) nastavit zařízení pro Hyper-V.

