---
title: Architektura zařízení služby Azure Migrate | Dokumentace Microsoftu
description: Obsahuje základní informace o zařízení Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: c2c9ca3082aa9c2067a63f8d6304e8a229dac14a
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811450"
---
# <a name="azure-migrate-appliance"></a>Zařízení Azure Migrate

Tento článek popisuje zařízení Azure Migrate. Můžete nasadit na zařízení při použití nástroje pro posouzení migrace Azure a migraci pro zjišťování, vyhodnocení a migraci aplikací, infrastruktury a úloh do služby Microsoft Azure. 

[Azure Migrate](migrate-services-overview.md) poskytuje centrální rozbočovač pro sledování zjišťování, vyhodnocení a migraci místních aplikací a úloh a soukromého a veřejného cloudu virtuálních počítačů do Azure. Centrum poskytuje Azure Migrate nástroje pro vyhodnocení a migrace, jakož i nabídky softwaru třetích stran výrobce (ISV).



## <a name="appliance-overview"></a>Přehled zařízení

Azure Migrate typy zařízení a využití jsou následující.

**Nasadit jako** | **Používá pro** | **Podrobnosti**
--- | --- |  ---
Virtuální počítač VMware | Posouzení virtuálních počítačů VMware pomocí Azure Migrate Assessment tool.<br/><br/> Migrace bez agenta virtuálního počítače VMware pomocí nástroje Azure Migrate migrace serveru | Stáhnout šablonu pro soubory OVA a importovat do systému vCenter Server toto zařízení vytvoříte virtuální počítač.
Virtuální počítač Hyper-V | Posouzení virtuálních počítačů Hyper-V pomocí nástroje pro posouzení migrace Azure. | Stáhněte si komprimovaný virtuálního pevného disku a importovat do technologie Hyper-V na toto zařízení vytvoříte virtuální počítač.

## <a name="appliance-access"></a>Přístup k zařízení

Po konfiguraci zařízení, můžete vzdáleně přistupovat zařízení virtuálního počítače přes TCP port 3389. Můžete také vzdálený přístup k webové aplikaci správy pro zařízení, na portu 44368 s adresou URL: ``` https://<appliance-ip-or-name>:44368 ```.

## <a name="appliance-license"></a>Licence zařízení
Zařízení se dodává s zkušební licence Windows serveru 2016, který je platný po dobu 180 dnů. Pokud zkušební období blíží vypršení platnosti, doporučujeme stáhnout a nasadit nové zařízení nebo aktivaci licencí operačního systému zařízení virtuálního počítače.

## <a name="appliance-agents"></a>Agenti zařízení
Zařízení má tyto agenty nainstalované.

**Agent** | **Podrobnosti**
--- | ---
Agent zjišťování | Shromažďuje konfigurační data z místních virtuálních počítačů.
Posouzení agenta | Profily v místním prostředí pro shromažďování dat o výkonu virtuálního počítače.
Adaptér migrace | Orchestruje replikaci virtuálních počítačů a koordinuje komunikaci mezi virtuálními počítači a Azure.
Migrace brány | Odešle replikovaných dat virtuálního počítače do Azure.


## <a name="appliance-deployment-requirements"></a>Požadavky na nasazení zařízení

- [Kontrola](migrate-support-matrix-vmware.md#assessment-appliance-requirements) požadavky na nasazení zařízení VMware a adresy URL, které potřebuje přístup k zařízení.
- [Kontrola](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) požadavky na nasazení technologie Hyper-V zařízení a adresy URL, které potřebuje přístup k zařízení.


## <a name="collected-performance-data-vmware"></a>Výkonu shromážděných dat – VMware

Tady se data o výkonu virtuálních počítačů VMware, který zařízení shromažďuje a odesílá do Azure.

**Data** | **Counter** | **Hodnocení dopadu**
--- | --- | ---
Využití procesoru | cpu.usage.average | Doporučená velikost a náklady na virtuální počítač
Využití paměti v aplikaci | mem.usage.average | Doporučená velikost a náklady na virtuální počítač
Čtení z disku propustnost (MB za sekundu) | virtualDisk.read.average | Výpočet velikosti disků, náklady na úložiště, velikost virtuálního počítače
Zápis propustnost disku (MB za sekundu) | virtualDisk.write.average | Výpočet velikosti disků, náklady na úložiště, velikost virtuálního počítače
Čtení z disku operací za sekundu | virtualDisk.numberReadAveraged.average | Výpočet velikosti disků, náklady na úložiště, velikost virtuálního počítače
Operací za sekundu zápisu disku | virtualDisk.numberWriteAveraged.average  | Výpočet velikosti disků, náklady na úložiště, velikost virtuálního počítače
Propustnost (MB za sekundu) čtení síťové karty | net.received.average | Výpočet velikosti virtuálního počítače
Zápis propustností síťové karty (MB za sekundu) | net.transmitted.average  |Výpočet velikosti virtuálního počítače


## <a name="collected-metadata-vmware"></a>Shromážděná metadata VMware

Tady je úplný seznam virtuálních počítačů VMware metadata, která zařízení shromažďuje a odesílá do Azure.

**Data** | **Counter**
--- | --- 
**Podrobnosti o počítači** | 
ID VIRTUÁLNÍHO POČÍTAČE | vm.Config.InstanceUuid 
název virtuálního počítače | vm.Config.Name
vCenter Server ID | VMwareClient.Instance.Uuid
Popis virtuálního počítače | virtuální počítač. Summary.Config.Annotation
Název produktu licencí | vm.Client.ServiceContent.About.LicenseProductName
Typ operačního systému | virtuální počítač. SummaryConfig.GuestFullName
Typ spuštění | vm.Config.Firmware
Počet jader | vm.Config.Hardware.NumCPU
Paměť (MB) | virtuální počítač. Config.Hardware.MemoryMB
Počet disků | virtuální počítač. Config.Hardware.Device.ToList(). FindAll(x => is VirtualDisk).count
Seznam velikost disků | virtuální počítač. Config.Hardware.Device.ToList(). FindAll (x = > je VirtualDisk)
Seznamu síťových adaptérů | virtuální počítač. Config.Hardware.Device.ToList(). FindAll(x => is VirtualEthernet).count
Využití procesoru | cpu.usage.average
Využití paměti v aplikaci |mem.usage.average
**Na podrobnosti o disku** | 
Hodnota klíče disku | disk. Klíč
Číslo Dikunit | disk. UnitNumber
Hodnota klíče kontroleru disku | disk.ControllerKey.Value
GB zřízené | virtualDisk.DeviceInfo.Summary
Název disku | Hodnota generována pomocí disku. UnitNumber, disk. Klíč disku. ControllerKey.VAlue
Operace čtení za sekundu | virtualDisk.numberReadAveraged.average
Operace zápisu za sekundu | virtualDisk.numberWriteAveraged.average
Propustnost pro čtení (MB za sekundu) | virtualDisk.read.average
Zápis propustnost (MB za sekundu) | virtualDisk.write.average
**Za síťovou kartu Podrobnosti** | 
Název síťového adaptéru | síťový adaptér Klíč
Adresa MAC | ((VirtualEthernetCard)nic).MacAddress
Adresy protokolu IPv4 | virtuální počítač. Guest.Net
IPv6 adresy | virtuální počítač. Guest.Net
Propustnost pro čtení (MB za sekundu) | net.received.average
Zápis propustnost (MB za sekundu) | net.transmitted.average
**Podrobnosti o cestě inventáře** | 
Name | container.GetType().Name
Typ podřízený objekt | container.ChildType
Podrobné referenční informace | container.MoRef
Podrobnosti o nadřazené | Container.Parent
Podrobnosti složky na virtuální počítač | ((Folder)container).ChildEntity.Type
Podrobnosti datového centra na virtuální počítač | ((Datacenter)container).VmFolder
Podrobnosti o datovém centru na složku hostitele | ((Datacenter)container).HostFolder
Podrobnosti o clusteru na hostiteli | ((ClusterComputeResource)container).Host
Podrobnosti hostitele na virtuální počítač | ((HostSystem)container).VM



## <a name="collected-performance-data-hyper-v"></a>Výkonu shromážděných dat Hyper-V

Tady se data o výkonu virtuálních počítačů VMware, který zařízení shromažďuje a odesílá do Azure.

**Třídy čítačů výkonu** | **Counter** | **Hodnocení dopadu**
--- | --- | ---
Technologie Hyper-V hypervisoru virtuálního procesoru | % Doby běhu hosta | Doporučená velikost a náklady na virtuální počítač
Dynamické paměti technologie Hyper-V virtuálního počítače | Aktuální zatížení (%)<br/> Host viditelné fyzická paměť (MB) | Doporučená velikost a náklady na virtuální počítač
Zařízení virtuálního úložiště technologie Hyper-V | Číst bajty za sekundu | Výpočet velikosti disků, náklady na úložiště, velikost virtuálního počítače
Zařízení virtuálního úložiště technologie Hyper-V | Zápis bajtů za sekundu | Výpočet velikosti disků, náklady na úložiště, velikost virtuálního počítače
Technologie Hyper-V virtuální síťový adaptér | Bajtů přijatých za sekundu | Výpočet velikosti virtuálního počítače
Technologie Hyper-V virtuální síťový adaptér | Bajtů odeslaných za sekundu | Výpočet velikosti virtuálního počítače

- Využití procesoru je součtem všechno využití pro všechny virtuální procesory připojené k virtuálnímu počítači.
- Využití paměti (aktuální tlak * hostovanou fyzickou paměť viditelné) / 100.
- Hodnoty využití disku a sítě se shromažďují z uvedených čítačů výkonu technologie Hyper-V.

## <a name="collected-metadata-hyper-v"></a>Shromážděná metadata Hyper-V

Tady je úplný seznam virtuálních počítačů Hyper-V metadata, která zařízení shromažďuje a odesílá do Azure.

**Data** | **Klientská třída služby WMI** | **Vlastnost třídy služby WMI**
--- | --- | ---
**Podrobnosti o počítači** | 
Sériové číslo systému BIOS _ Msvm_BIOSElement | BIOSSerialNumber
Typ virtuálního počítače (generace 1 nebo 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Zobrazovaný název virtuálního počítače | Msvm_VirtualSystemSettingData | ElementName
Verze virtuálního počítače | Msvm_ProcessorSettingData | VirtualQuantity
Paměť (v bajtech) | Msvm_MemorySettingData | VirtualQuantity
Maximální velikost paměti, které mohou být spotřebovány virtuálního počítače | Msvm_MemorySettingData | Omezení
Dynamická paměť povolena | Msvm_MemorySettingData | DynamicMemoryEnabled
Operační systém název/verze nebo plně kvalifikovaný název domény | Msvm_KvpExchangeComponent | GuestIntrinsciExchangeItems Name Data
Stav napájení virtuálního počítače | Msvm_ComputerSystem | Vlastnosti EnabledState
**Na podrobnosti o disku** | 
Identifikátor disku | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Typ virtuálního pevného disku | Msvm_VirtualHardDiskSettingData | type
Velikost virtuálního pevného disku | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Nadřazený virtuální pevný disk | Msvm_VirtualHardDiskSettingData | ParentPath
**Za síťovou kartu Podrobnosti** | 
IP adresy (syntetických síťových adaptérů) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
Server DHCP (syntetických síťových adaptérů) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
ID síťové karty (syntetických síťových adaptérů) | Msvm_SyntheticEthernetPortSettingData | InstanceID
Adresa MAC síťové karty (syntetických síťových adaptérů) | Msvm_SyntheticEthernetPortSettingData | Adresa
ID síťové karty (síťové adaptéry starší verze) | MsvmEmulatedEthernetPortSetting dat | InstanceID
ID MAC síťové karty (síťové adaptéry starší verze) | MsvmEmulatedEthernetPortSetting dat | Adresa




## <a name="discovery-and-collection-process"></a>Proces zjišťování a kolekce

Zařízení komunikuje se servery vCenter a hostitele nebo clusteru Hyper-V pomocí následujícího postupu.


1. **Spustit vyhledávání**:
    - Při spuštění zjišťování technologie Hyper-V zařízení s komunikuje s hostiteli Hyper-V na portech WinRM 5985 (HTTP) a 5986 (HTTPS).
    - Při spuštění zjišťování VMware zařízení s komunikuje se serverem vCenter na portu TCP 443 ve výchozím nastavení. Pokud vCenter server naslouchá na jiném portu, můžete ji nakonfigurovat ve službě web app zařízení.
2. **Shromažďování dat metadata a výkonu**:
    - Toto zařízení používá ke shromažďování dat technologie Hyper-V virtuálního počítače z hostitele Hyper-V na portech 5985 a 5986 relaci Common Information Model (CIM).
    - Zařízení komunikuje s portem 443 ve výchozím nastavení, shromažďovat data virtuálních počítačů VMware ze systému vCenter Server.
3. **Odesílání dat**: Zařízení odešle shromážděná data posouzení migrace serveru Azure a Azure Migrate migrace serveru přes SSL port 443.
    - Zařízení shromáždí údaje o výkonu, data o využití v reálném čase.
        - Údaje o výkonu shromažďovaných každých 20 sekund pro VMware a každých 30 sekund pro Hyper-V, pro každou metriku výkonu.
        - Shromážděná data je zajišťován vytvořit jeden datový bod pro deset minut.
        - Hodnota využití ve špičce je vybrán ze všech 20/30 druhé datové body a poslaná do Azure pro výpočet posouzení.
        - Na základě percentilu hodnoty zadané ve vlastnostech posouzení (50/90. percentil/95/99th), 10 minut body jsou seřazené ve vzestupném pořadí a hodnota odpovídající percentilu se používá pro výpočet posouzení
    - Pro migraci serveru na zařízení spustí shromažďování dat virtuálního počítače a replikuje ji do Azure.
4. **Posoudit a migrovat**: Nyní můžete vytvořit posouzení z metadat shromážděných zařízení pomocí Azure Migrate Server Assessment. Kromě toho můžete spustit také migraci virtuálních počítačů VMware pomocí Azure Migrate migrace serveru pro orchestraci replikace bez agenta virtuálního počítače.


![Architektura](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>Upgrade zařízení

Zařízení se upgraduje se aktualizují agenty Azure Migrate běžící na zařízení.

- To je způsobeno automaticky na zařízení je standardně povolená Automatická aktualizace.
- Můžete změnit toto výchozí nastavení, chcete-li aktualizovat agenty ručně.
- Chcete-li zakázat automatické aktualizace v HKLM\SOFTWAREMicrosoft\Azure nastavte klíč registru zařízení automatické aktualizace.

### <a name="set-agent-updates-to-manual"></a>Nastavte agenta na ruční aktualizace

Pro ruční aktualizace, ujistěte se, že aktualizovat všechny agenty na zařízení ve stejnou dobu, pomocí **aktualizovat** tlačítko pro každého agenta zastaralé na zařízení. Aktualizace nastavení můžete přepnout zpět na automatické aktualizace v každém okamžiku.

## <a name="next-steps"></a>Další postup

[Zjistěte, jak](tutorial-assess-vmware.md#set-up-the-appliance-vm) nastavit zařízení pro VMware.
[Zjistěte, jak](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) nastavit zařízení pro technologii Hyper-V.

