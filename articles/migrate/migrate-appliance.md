---
title: Architektura zařízení Azure Migrate | Microsoft Docs
description: Poskytuje přehled zařízení Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: 249cbea173afe1671118446e0714b721b8c7f72b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685091"
---
# <a name="azure-migrate-appliance"></a>Zařízení Azure Migrate

Tento článek popisuje zařízení Azure Migrate. Zařízení nasazujete při použití Azure Migrate nástrojů pro posuzování a migraci ke zjišťování, hodnocení a migraci aplikací, infrastruktury a úloh do Microsoft Azure. 

[Azure Migrate](migrate-services-overview.md) poskytuje centrální centrum pro sledování zjišťování, hodnocení a migrace vašich místních aplikací a úloh a virtuálních a veřejných cloudových virtuálních počítačů do Azure. Centrum poskytuje Azure Migrate nástroje pro posuzování a migraci i nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců.



## <a name="appliance-overview"></a>Přehled zařízení

Typy a využití Azure Migrate zařízení jsou následující.

**Nasazeno jako** | **Používá se pro** | **Podrobnosti**
--- | --- |  ---
Virtuální počítač VMware | Posouzení virtuálního počítače VMware pomocí nástroje pro vyhodnocení Azure Migrate.<br/><br/> Migrace bez agentů virtuálního počítače VMware pomocí nástroje pro migraci Azure Migrate serveru | Stáhněte si šablonu pro sadu vajíček a importujte ji vCenter Server k vytvoření virtuálního počítače zařízení.
Virtuální počítač Hyper-V | Posouzení virtuálního počítače Hyper-V pomocí nástroje pro vyhodnocení Azure Migrate. | Pokud chcete vytvořit virtuální počítač zařízení, Stáhněte si VHD a importujte ho do Hyper-V.

## <a name="appliance-access"></a>Přístup k zařízení

Po nakonfigurování zařízení můžete vzdáleně přistupovat k virtuálnímu počítači zařízení přes port TCP 3389. Můžete také vzdáleně přistupovat k aplikaci pro správu webu pro zařízení na portu 44368 s adresou URL: `https://<appliance-ip-or-name>:44368`.

## <a name="appliance-license"></a>Licence na zařízení
Zařízení obsahuje zkušební licenci Windows Server 2016, která je platná po dobu 180 dnů. Pokud se zkušební období blíží vypršení platnosti, doporučujeme, abyste si stáhli a nasadili nové zařízení nebo aktivovali licenci k operačnímu systému virtuálního počítače zařízení.

## <a name="appliance-agents"></a>Agenti zařízení
Zařízení má nainstalované tyto agenty.

**Agent** | **Podrobnosti**
--- | ---
Agent zjišťování | Shromažďuje konfigurační data místních virtuálních počítačů.
Agent posouzení | Profiluje místní prostředí pro shromažďování dat o výkonu virtuálních počítačů.
Adaptér migrace | Orchestruje replikaci virtuálních počítačů a koordinuje komunikaci mezi virtuálními počítači a Azure.
Brána migrace | Odesílá replikovaná data virtuálního počítače do Azure.


## <a name="appliance-deployment-requirements"></a>Požadavky na nasazení zařízení

- [Zkontrolujte](migrate-support-matrix-vmware.md#assessment-appliance-requirements) požadavky na nasazení pro zařízení VMware a adresy URL, ke kterým má zařízení přístup.
- [Zkontrolujte](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) požadavky na nasazení pro zařízení s technologií Hyper-V a adresy URL, ke kterým musí zařízení přistupovat.


## <a name="collected-performance-data-vmware"></a>Shromážděná data o výkonu – VMware

Tady je údaje o výkonu virtuálních počítačů VMware, které zařízení shromažďuje a odesílá do Azure.

**Data** | **Objektů** | **Dopad posouzení**
--- | --- | ---
Využití procesoru | CPU. Usage. Average | Doporučená velikost virtuálního počítače/náklady
Využití paměti | mem. Usage. Average | Doporučená velikost virtuálního počítače/náklady
Propustnost čtení z disku (MB za sekundu) | virtualDisk. Read. Average | Výpočet velikosti disku, nákladů na úložiště, velikosti virtuálního počítače
Propustnost zápisu na disk (MB za sekundu) | virtualDisk. Write. Average | Výpočet velikosti disku, nákladů na úložiště, velikosti virtuálního počítače
Operace čtení z disku za sekundu | virtualDisk. numberReadAveraged. Average | Výpočet velikosti disku, nákladů na úložiště, velikosti virtuálního počítače
Počet operací zápisu na disk za sekundu | virtualDisk. numberWriteAveraged. Average  | Výpočet velikosti disku, nákladů na úložiště, velikosti virtuálního počítače
Propustnost čtení síťových adaptérů (MB za sekundu) | NET. Received. Average | Výpočet pro velikost virtuálního počítače
Propustnost zápisu síťových adaptérů (MB za sekundu) | NET. přenášeno. Average  |Výpočet pro velikost virtuálního počítače


## <a name="collected-metadata-vmware"></a>Shromážděná metadata – VMware

> [!NOTE]
> Metadata zjištěná zařízením Azure Migrate se používají k usnadnění správné velikosti vašich aplikací při jejich migraci do Azure, k provedení analýzy vhodnosti Azure, analýze závislostí aplikací a plánování nákladů. Společnost Microsoft nepoužívá tato data ve vztahu k auditu dodržování předpisů v licencích.

Tady je úplný seznam metadat virtuálních počítačů VMware, které zařízení shromažďuje a odesílá do Azure.

**Data** | **Objektů**
--- | --- 
**Podrobnosti o počítači** | 
ID VIRTUÁLNÍHO POČÍTAČE | síť. Config. InstanceUuid 
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
Využití paměti |mem. Usage. Average
**Podrobnosti o jednotlivých discích** | 
Hodnota klíč disku | disk. Zkrat
Dikunit číslo | disk. UnitNumber
Hodnota klíče řadiče disku | disk. ControllerKey. Value
Zřízené gigabajty | virtualDisk. DeviceInfo. Summary
Název disku | Hodnota generovaná pomocí disku UnitNumber, disk. Klíč, disk. ControllerKey. VAlue
Operace čtení za sekundu | virtualDisk. numberReadAveraged. Average
Operace zápisu za sekundu | virtualDisk. numberWriteAveraged. Average
Propustnost čtení (MB za sekundu) | virtualDisk. Read. Average
Propustnost zápisu (MB za sekundu) | virtualDisk. Write. Average
**Podrobnosti na NIC** | 
Název síťového adaptéru | síťových. Zkrat
Adresa MAC | ((VirtualEthernetCard) síťová karta). MacAddress
Adresy IPv4 | síť. Guest.Net
IPv6 adresy | síť. Guest.Net
Propustnost čtení (MB za sekundu) | NET. Received. Average
Propustnost zápisu (MB za sekundu) | NET. přenášeno. Average
**Podrobnosti o cestě inventáře** | 
Název | vnitřního. GetType (). Jméno
Typ podřízeného objektu | vnitřního. ChildType
Referenční informace | vnitřního. MoRef
Podrobnosti nadřazené položky | Kontejner. Parent
Podrobnosti složky na virtuální počítač | (Složka) kontejneru). ChildEntity. Type
Podrobnosti datového centra na virtuální počítač | (Datacenter) kontejner). VmFolder
Podrobnosti datacentra na složku hostitelů | (Datacenter) kontejner). HostFolder
Podrobnosti o clusteru na hostitele | ((ClusterComputeResource) kontejner). Provoz
Podrobnosti o hostiteli na virtuálním počítači | ((HostSystem)container).VM



## <a name="collected-performance-data-hyper-v"></a>Shromážděná data o výkonu – Hyper-V

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

## <a name="collected-metadata-hyper-v"></a>Shromážděná metadata – Hyper-V

Tady je úplný seznam metadat virtuálních počítačů Hyper-V, které zařízení shromažďuje a odesílá do Azure.

**Data** | **Třída WMI** | **Vlastnost třídy WMI**
--- | --- | ---
**Podrobnosti o počítači** | 
Sériové číslo systému BIOS _ Msvm_BIOSElement | BIOSSerialNumber
Typ virtuálního počítače (FIN 1 nebo 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Zobrazovaný název virtuálního počítače | Msvm_VirtualSystemSettingData | ElementName
Verze virtuálního počítače | Msvm_ProcessorSettingData | VirtualQuantity
Paměť (bajty) | Msvm_MemorySettingData | VirtualQuantity
Maximální velikost paměti, kterou může virtuální počítač spotřebovat | Msvm_MemorySettingData | škálování
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
3. **Odeslat data**: zařízení odesílá shromážděná data pro Azure Migrate posouzení serveru a migraci Azure Migrate serveru přes SSL port 443.
    - Pro data o výkonu zařízení shromažďuje data o využití v reálném čase.
        - Údaje o výkonu se shromažďují každých 20 sekund pro VMware a každých 30 sekund pro Hyper-V pro každou metriku výkonu.
        - Shromážděná data jsou zahrnuta k vytvoření jednoho datového bodu po dobu deseti minut.
        - Hodnota maximálního využití je vybrána ze všech bodů 20/30 sekund a byla odeslána do Azure pro výpočet vyhodnocení.
        - Na základě hodnoty percentilu uvedené ve vlastnostech posouzení (50/90/95./99) se ve vzestupném pořadí seřadí body o deseti minutách a příslušná hodnota percentilu se použije k výpočtu posouzení.
    - U migrace serveru začne zařízení shromažďovat data virtuálních počítačů a replikuje je do Azure.
4. **Vyhodnocení a migrace**: teď můžete z metadat shromážděných zařízením vytvořit posouzení pomocí Azure Migrate posouzení serveru. Kromě toho můžete také začít migrovat virtuální počítače VMware pomocí migrace serveru Azure Migrate pro orchestraci replikace virtuálních počítačů bez agentů.


![Architektura](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>Upgrady zařízení

Zařízení se upgraduje, protože Azure Migrate agenti, kteří běží na zařízení, se aktualizují.

- K tomu dochází automaticky, protože ve výchozím nastavení je automaticky povolena automatická aktualizace na zařízení.
- Toto výchozí nastavení můžete změnit tak, aby se agenti aktualizovaly ručně.
- Automatické aktualizace zakážete tak, že přejdete do editoru registru > HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance a nastavíte klíč registru-"AutoUpdate" na hodnotu 0 (DWORD).
 
### <a name="set-agent-updates-to-manual"></a>Nastavit aktualizace agenta na ruční

V případě ručních aktualizací nezapomeňte aktualizovat všechny agenty na zařízení ve stejnou dobu, a to pomocí tlačítka **aktualizovat** pro každého zastaralého agenta na zařízení. Nastavení aktualizace můžete kdykoli přepnout zpátky na automatické aktualizace.

## <a name="next-steps"></a>Další kroky

[Přečtěte si, jak](tutorial-assess-vmware.md#set-up-the-appliance-vm) nastavit zařízení pro VMware.
[Přečtěte si, jak](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) nastavit zařízení pro Hyper-V.

