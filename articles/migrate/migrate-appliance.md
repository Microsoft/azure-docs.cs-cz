---
title: Zařízení Azure Migrate
description: Obsahuje přehled zařízení Azure Migrate používaného při hodnocení serveru a migraci.
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 1bb3372467919f1471fa9577cd60e9cecaf1750d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336949"
---
# <a name="azure-migrate-appliance"></a>Zařízení Azure Migrate

Tento článek shrnuje požadavky na požadavky na požadavky na požadavky na požadavky na požadavky na podporu zařízení Azure Migrate. 

## <a name="deployment-scenarios"></a>Scénáře nasazení

Zařízení Migrace Azure se používá v následujících scénářích.

**Scénář** | **Nástroj** | **Použití** 
--- | --- | ---
**Vyhodnocení virtuálního vana v vvware vv** | Migrace Azure:Vyhodnocení serveru | Objevte virtuální mise VMware<br/><br/> Zjišťování aplikací a závislostí počítače<br/><br/> Shromažďujte metadata počítače a metadata výkonu pro hodnocení.
**Migrace bez agentů virtuálního vavu VMware** | Migrace Azure:Migrace serveru | Objevte virtuální mise VMware <br/><br/> Replikujte virtuální virtuální maje v v oblasti VMware s migrací bez agenta.
**Vyhodnocení virtuálního aplikace Hyper-V** | Migrace Azure:Vyhodnocení serveru | Objevte virtuální aplikace Hyper-V<br/><br/> Shromažďujte metadata počítače a metadata výkonu pro hodnocení.
**Fyzikální hodnocení stroje** |  Migrace Azure:Vyhodnocení serveru |  Zjišťujte fyzické servery (nebo virtuální servery, které považujete za fyzické servery).<br/><br/> Shromažďujte metadata počítače a metadata výkonu pro hodnocení.

## <a name="appliance---vmware"></a>Spotřebič - VMware 

Následující tabulka shrnuje požadavky na zařízení Azure Migrate pro vmware.

**Požadavek** | **Vmware** 
--- | ---
**Součásti spotřebičů** | Přístroj má následující součásti:<br/><br/> - **Aplikace pro správu**: Jedná se o webovou aplikaci pro vstup uživatele během nasazení zařízení. Používá se při posuzování počítačů pro migraci do Azure.<br/> - **Agent zjišťování**: Agent shromažďuje data konfigurace počítače. Používá se při posuzování počítačů pro migraci do Azure.<br/>- **Agent hodnocení**: Agent shromažďuje údaje o výkonu. Používá se při posuzování počítačů pro migraci do Azure.<br/>- **Služba automatické aktualizace**: Aktualizuje součásti zařízení (běží každých 24 hodin).<br/>- **DRA agent:** Orchestruje replikaci virtuálních počítačů a koordinuje komunikaci mezi replikované počítače a Azure. Používá se pouze při replikaci virtuálních počítačů VMware do Azure pomocí migrace bez agenta.<br/>- **Brána:** Odešle replikovaná data do Azure. Používá se pouze při replikaci virtuálních počítačů VMware do Azure pomocí migrace bez agenta.
**Podporované nasazení** | Nasazení jako virtuální ho disponující virtuálním počítače VM pomocí šablony OVA.<br/><br/> Nasazení jako virtuální počítač VMware nebo fyzického počítače pomocí instalačního skriptu prostředí PowerShell.
**Podpora projektu** |  Zařízení může být přidruženo k jednomu projektu. <br/> K jednomu projektu lze přidružit libovolný počet zařízení.<br/> 
**Omezení zjišťování** | Zařízení může na serveru vCenter zjistit až 10 000 virtuálních počítačů VMware.<br/> Zařízení se může připojit k jednomu serveru vCenter.
**Šablona OVA** | Stáhnout z portálu nebo z aplikace https://aka.ms/migrate/appliance/vmware.<br/><br/> Velikost stahování je 11,2 GB.<br/><br/> Šablona staženého zařízení je dodávána s vyhodnocovací licencí pro Windows Server 2016, která je platná po dobu 180 dnů. Pokud se zkušební doba blíží vypršení platnosti, doporučujeme stáhnout a nasadit nové zařízení nebo aktivovat licenci operačního systému virtuálního zařízení.
**Skript PowerShellu** | Skript [ke stažení](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> 
**Software/hardware** |  Zařízení by mělo běžet na počítači se systémem Windows Server 2016, 32 GB paměti RAM, 8 virtuálními procesory, přibližně 80 GB diskového úložiště a externím virtuálním přepínačem.<br/> Zařízení vyžaduje přístup k internetu, a to buď přímo, nebo prostřednictvím proxy serveru.<br/><br/> Pokud spustíte zařízení na virtuálním počítači VMware, budete potřebovat dostatek prostředků na serveru vCenter přidělit virtuální počítač, který splňuje požadavky.<br/><br/> Pokud zařízení spouštěte na fyzickém počítači, ujistěte se, že zařízení používá Windows Server 2016 a splňuje hardwarové požadavky. 
**Požadavky na vmware** | Pokud nasadíte zařízení jako virtuální počítač VMware, musí být nasazenna na hostitele ESXi se spuštěnou verzí 5.5 nebo novější.<br/><br/> vCenter Server se systémem 5.5, 6.0, 6.5 nebo 6.7.
**VDDK (migrace bez agenta)** | Pokud nasadíte zařízení jako virtuální počítač VMware a používáte migraci bez agenta, musí být v virtuálním počítači zařízení nainstalována vM voblasti VM vM vM vM.
**Hodnota hash-OVA** | [Ověřte](tutorial-assess-vmware.md#verify-security) hodnoty hash šablony OVA.
**Skript hodnoty hash powershellu** | [Ověřte](deploy-appliance-script.md#verify-file-security) hodnoty hodnoty hash skriptu prostředí PowerShell.




## <a name="appliance---hyper-v"></a>Spotřebič - Hyper-V

**Požadavek** | **Hyper-V** 
--- | ---
**Součásti spotřebičů** | Přístroj má následující součásti:<br/><br/>- **Aplikace pro správu**: Jedná se o webovou aplikaci pro vstup uživatele během nasazení zařízení. Používá se při posuzování počítačů pro migraci do Azure.<br/> - **Agent zjišťování**: Agent shromažďuje data konfigurace počítače. Používá se při posuzování počítačů pro migraci do Azure.<br/>- **Agent hodnocení**: Agent shromažďuje údaje o výkonu. Používá se při posuzování počítačů pro migraci do Azure.<br/>- **Služba automatické aktualizace**: Aktualizuje součásti zařízení (běží každých 24 hodin).
**Podporované nasazení** | Nasazení jako virtuální ho sou- virtuálního počítače pomocí šablony virtuálního pevného disku<br/><br/> Nasazení jako virtuální počítač Hyper-V nebo fyzického počítače pomocí instalačního skriptu prostředí PowerShell.
**Podpora projektu** |  Zařízení může být přidruženo k jednomu projektu. <br/> K jednomu projektu lze přidružit libovolný počet zařízení.<br/> 
**Omezení zjišťování** | Zařízení může objevit až 5000 virtuálních zařízení Hyper-V.<br/> Zařízení se může připojit až k 300 hostitelům technologie Hyper-V.
**Šablona VHD** | Zip složku včetně VHD. Stáhnout z portálu nebo z aplikace https://aka.ms/migrate/appliance/hyperv.<br/><br/> Velikost stahování je 10 GB.<br/><br/> Šablona staženého zařízení je dodávána s vyhodnocovací licencí pro Windows Server 2016, která je platná po dobu 180 dnů. Pokud se zkušební doba blíží vypršení platnosti, doporučujeme stáhnout a nasadit nové zařízení nebo aktivovat licenci operačního systému virtuálního zařízení.
**Skript PowerShellu** | Skript [ke stažení](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> 
**Software/hardware***   |  Zařízení by mělo běžet na počítači se systémem Windows Server 2016, 32 GB paměti RAM, 8 virtuálními procesory, přibližně 80 GB diskového úložiště a externím virtuálním přepínačem.<br/> Zařízení potřebuje statickou nebo dynamickou IP adresu a vyžaduje přístup k internetu, a to buď přímo, nebo prostřednictvím proxy serveru.<br/><br/> Pokud zařízení spustíte jako virtuální počítač Hyper-V, potřebujete dostatek prostředků na hostiteli Hyper-V k přidělení 16 GB paměti RAM, 8 virtuálních procesorů, přibližně 80 GB úložného prostoru a externího přepínače pro virtuální počítač zařízení.<br/><br/> Pokud zařízení spouštěte na fyzickém počítači, ujistěte se, že zařízení používá Windows Server 2016 a splňuje hardwarové požadavky. 
**Požadavky na hyper-V** | Pokud nasadíte zařízení se šablonou virtuálního pevného disku, virtuální počítač zařízení poskytovaný azure migrate je Hyper-V V VM verze 5.0.<br/><br/> Hostitel i Hyper-V musí mít systém Windows Server 2012 R2 nebo novější. 
**Hodnota hash-VHD** | [Ověřte](tutorial-assess-hyper-v.md#verify-security) hodnoty hodnoty hash šablony virtuálního pevného disku.
**Skript hodnoty hash powershellu** | [Ověřte](deploy-appliance-script.md#verify-file-security) hodnoty hodnoty hash skriptu prostředí PowerShell.


## <a name="appliance---physical"></a>Spotřebič - Fyzikální

**Požadavek** | **Fyzické** 
--- | ---
**Součásti spotřebičů** | Přístroj má následující součásti: <br/><br/> - **Aplikace pro správu**: Jedná se o webovou aplikaci pro vstup uživatele během nasazení zařízení. Používá se při posuzování počítačů pro migraci do Azure.<br/> - **Agent zjišťování**: Agent shromažďuje data konfigurace počítače. Používá se při posuzování počítačů pro migraci do Azure.<br/>- **Agent hodnocení**: Agent shromažďuje údaje o výkonu. Používá se při posuzování počítačů pro migraci do Azure.<br/>- **Služba automatické aktualizace**: Aktualizuje součásti zařízení (běží každých 24 hodin).
**Podporované nasazení** | Nasazení jako vyhrazený fyzický počítač nebo virtuální počítač pomocí instalačního skriptu prostředí PowerShell.
**Podpora projektu** |  Zařízení může být přidruženo k jednomu projektu. <br/> K jednomu projektu lze přidružit libovolný počet zařízení.<br/> 
**Omezení zjišťování** | Zařízení může objevit až 250 fyzických serverů.
**Skript PowerShellu** | Stáhněte si skript (AzureMigrateInstaller.ps1) do složky zip z portálu. [Další informace](tutorial-assess-physical.md#set-up-the-appliance). Případně [si můžete stáhnout přímo](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> Velikost stahování je 59,7 MB.
**Software/hardware** |  Zařízení by mělo běžet na počítači se systémem Windows Server 2016, 32 GB paměti RAM, 8 virtuálními procesory, přibližně 80 GB diskového úložiště a externím virtuálním přepínačem.<br/> Zařízení potřebuje statickou nebo dynamickou IP adresu a vyžaduje přístup k internetu, a to buď přímo, nebo prostřednictvím proxy serveru.<br/><br/> Pokud zařízení spouštěte na fyzickém počítači, ujistěte se, že zařízení používá Windows Server 2016 a splňuje hardwarové požadavky. 
**Hodnota hash** | [Ověřte](deploy-appliance-script.md#verify-file-security) hodnoty hodnoty hash skriptu prostředí PowerShell.

## <a name="url-access"></a>Přístup k adrese URL

Zařízení Azure Migrate potřebuje připojení k internetu.

- Když zařízení nasadíte, Azure Migrate probere kontrolu připojení k adresám URL shrnutým v následující tabulce.
- Pokud pro připojení k Internetu používáte proxy server založený na adrese URL, povolte přístup k těmto adresám URL a ujistěte se, že proxy server vyřeší všechny záznamy CNAME přijaté při vyhlížení adres URL.

**Adresa URL** | **Podrobnosti**  
--- | --- |
*.portal.azure.com  | Přejděte na Azure Portal.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | Přihlaste se ke svému předplatnému Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Vytvořte aplikace služby Active Directory pro komunikaci se zařízením Azure Migrate.
management.azure.com | Vytvořte aplikace služby Active Directory pro zařízení pro komunikaci se službou Azure Migrate.
dc.services.visualstudio.com | Nahrajte protokoly aplikací používané pro interní monitorování.
*.vault.azure.net | Správa tajných kódů v trezoru klíčů Azure.
aka.ms/* | Povolit přístup k aka odkazy. Používá se pro aktualizace zařízení Azure Migrate.
download.microsoft.com/download | Povolit stahování ze stažení společnosti Microsoft.
*.servicebus.windows.net | Komunikace mezi zařízením a službou Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Připojte se k adresám URL služby Azure Migrate.
*.hypervrecoverymanager.windowsazure.com | **Používá se pro migraci bez agentů VMware**<br/><br/> Připojte se k adresám URL služby Azure Migrate.
*.blob.core.windows.net |  **Používá se pro migraci bez agentů VMware**<br/><br/>Nahrajte data do úložiště pro migraci.




## <a name="collected-data---vmware"></a>Shromážděná data - VMware

Zařízení shromažďuje metadata, data o výkonu a data analýzy závislostí (pokud se používá analýza závislostí bez [agenta).](concepts-dependency-visualization.md)

### <a name="metadata"></a>Metadata

Metadata zjištěná zařízením Azure Migrate vám pomohou zjistit, jestli jsou počítače a aplikace připravené k migraci do Azure, počítačů mno h–windows a aplikacím správné velikosti, plánuje náklady a analyzuje závislosti aplikací. Společnost Microsoft nepoužívá tato data při žádném auditu dodržování předpisů licencí.

Tady je úplný seznam metadat virtuálního počítače VMware, které zařízení shromažďuje a odesílá do Azure.

**Dat** | **Čítač**
--- | --- 
**Podrobnosti o stroji** | 
ID virtuálního počítače | Vm. Config.InstanceUuid 
název virtuálního počítače | Vm. Config.Name
ID serveru vCenter | VMwareClient.Instance.Uuid
Popis virtuálního virtuálního mísy | Vm. Summary.Config.Annotation
Název licenčního produktu | Vm. Client.serviceContent.About.LicenseProductName
Typ operačního systému | Vm. SummaryConfig.GuestFullName
Typ spouštění | Vm. Config.Firmware
Počet jader | Vm. Config.Hardware.NumCPU
Paměť (MB) | Vm. Config.Hardware.MemoryMB
Počet disků | Vm. Config.Hardware.Device.ToList(). FindAll(x => je VirtualDisk).počet
Seznam velikostí disku | Vm. Config.Hardware.Device.ToList(). FindAll(x => je VirtualDisk)
Seznam síťových adaptérů | Vm. Config.Hardware.Device.ToList(). FindAll(x => je VirtualEthernet).počet
Využití procesoru | cpu.usage.average
Využití paměti |mem.usage.average
**Podrobnosti o disku** | 
Hodnota diskového klíče | Disku. Klíč
Dikunit číslo | Disku. Číslo jednotky
Hodnota klíče řadiče disku | Disku. ControllerKey.Value
Zřízené gigabajty | virtualDisk.DeviceInfo.Summary
Název disku | Hodnota generovaná pomocí disku. UnitNumber, disk. Klíč, disk. ControllerKey.VAlue
Operace čtení za sekundu | virtualDisk.numberReadAveraged.average
Operace zápisu za sekundu | virtualDisk.numberWriteAveraged.average
Propustnost čtení (MB za sekundu) | virtualDisk.read.average
Propustnost zápisu (MB za sekundu) | virtualDisk.write.average
**Podrobnosti o nic** | 
Název síťového adaptéru | nic. Klíč
Adresa MAC | ((VirtualEthernetCard)nic). MacAdresa
Adresy IPv4 | Vm. Guest.Net
Adresy IPv6 | Vm. Guest.Net
Propustnost čtení (MB za sekundu) | net.received.average
Propustnost zápisu (MB za sekundu) | net.transmitted.average
**Podrobnosti cesty zásob** | 
Name (Název) | Kontejner. GetType(). Jméno
Typ podřízeného objektu | Kontejner. ChildType
Referenční údaje | Kontejner. MoRef
Podrobnosti nadřazeného objekt | Kontejner.Nadřazený
Podrobnosti o složce na virtuální mkérna | ((Folder)kontejner). ChildEntity.Typ
Podrobnosti datového centra pro jeden virtuální virtuální ms | ((Datacenter)kontejner). Složka Vm
Podrobnosti datového centra pro hostitelskou složku | ((Datacenter)kontejner). Složka host
Podrobnosti o clusteru na hostitele | ((ClusterComputeResource)kontejner). Hostitele
Podrobnosti o hostiteli na virtuální ms | ((HostSystem)). Vm

### <a name="performance-data"></a>Data výkonu


Tady jsou data o výkonu virtuálního počítače VMware, která zařízení shromažďuje a odesílá do Azure.

**Data** | **Čítač** | **Dopad posouzení**
--- | --- | ---
Využití procesoru | cpu.usage.average | Doporučená velikost/náklady virtuálního počítače
Využití paměti | mem.usage.average | Doporučená velikost/náklady virtuálního počítače
Propustnost čtení disku (MB za sekundu) | virtualDisk.read.average | Výpočet velikosti disku, nákladů na úložiště, velikosti virtuálního počítače
Propustnost zápisu disku (MB za sekundu) | virtualDisk.write.average | Výpočet velikosti disku, nákladů na úložiště, velikosti virtuálního počítače
Operace čtení disku za sekundu | virtualDisk.numberReadAveraged.average | Výpočet velikosti disku, nákladů na úložiště, velikosti virtuálního počítače
Operace zápisu na disk za sekundu | virtualDisk.numberWriteAveraged.average  | Výpočet velikosti disku, nákladů na úložiště, velikosti virtuálního počítače
Propustnost čtení nic (MB za sekundu) | net.received.average | Výpočet pro velikost virtuálního počítače
Propustnost propustnost nic (MB za sekundu) | net.transmitted.average  |Výpočet pro velikost virtuálního počítače

### <a name="app-dependencies-metadata"></a>Metadata závislostí aplikací

Analýza závislostí bez agenta shromažďuje data připojení a zpracování.

#### <a name="connection-data"></a>Data připojení

Tady jsou data o připojení, která zařízení shromažďuje z každého virtuálního aplikace povoleného pro analýzu závislostí bez agenta. Tato data se posílají do Azure.

**Data** | **Použitý příkaz** 
--- | --- 
Místní port | Netstat
Místní IP adresa | Netstat
Vzdálený port | Netstat
Vzdálená IP adresa | Netstat
Stav připojení TCP | Netstat
ID procesu | Netstat
Ne. aktivních připojení | Netstat

#### <a name="process-data"></a>Zpracování dat
Tady jsou procesní data, která zařízení shromažďuje z každého virtuálního aplikace povoleného pro analýzu závislostí bez agentů. Tato data se posílají do Azure.

**Data** | **Třída WMI** | **Vlastnost třídy WMI**
--- | --- | ---
Název procesu | Win32_Process | Cesta spustitelného souboru
Zpracovat argumenty | Win32_Process | Commandline
Název aplikace | Win32_Process | VersionInfo.ProductName parametr vlastnosti ExecutablePath

#### <a name="linux-vm-data"></a>Data virtuálních počítačů s Linuxem

Tady jsou data o připojení a zpracování, která zařízení shromažďuje z každého virtuálního počítače s Linuxem povoleného pro analýzu závislostí bez agentů. Tato data se posílají do Azure.

**Data** | **Použitý příkaz** 
--- | ---
Místní port | Netstat 
Místní IP adresa | Netstat 
Vzdálený port | Netstat 
Vzdálená IP adresa | Netstat 
Stav připojení TCP | Netstat 
Ne. aktivních připojení | Netstat
ID procesu  | Netstat 
Název procesu | Ps
Zpracovat argumenty | Ps
Název aplikace | dpkg nebo rpm



## <a name="collected-data---hyper-v"></a>Shromážděná data - Hyper-V

Zařízení shromažďuje metadata, data o výkonu a data analýzy závislostí (pokud se používá analýza závislostí bez [agenta).](concepts-dependency-visualization.md)

### <a name="metadata"></a>Metadata
Metadata zjištěná zařízením Azure Migrate vám pomohou zjistit, jestli jsou počítače a aplikace připravené k migraci do Azure, počítačů mno h–windows a aplikacím správné velikosti, plánuje náklady a analyzuje závislosti aplikací. Společnost Microsoft nepoužívá tato data při žádném auditu dodržování předpisů licencí.

Tady je úplný seznam metadat virtuálního počítače Hyper-V, které zařízení shromažďuje a odesílá do Azure.

**Dat* | **Třída WMI** | **VLASTNOST TŘÍDY WMI**
--- | --- | ---
**Podrobnosti o stroji** | 
Sériové číslo systému BIOS _ Msvm_BIOSElement | BiosSerialNumber
Typ virtuálního zařízení (Gen 1 nebo 2) | Msvm_VirtualSystemSettingData | Typ subsystému VirtualSystem
Zobrazovaný název virtuálního virtuálního_kmonta | Msvm_VirtualSystemSettingData | ElementName
Verze virtuálního virtuálního aplikace | Msvm_ProcessorSettingData | VirtualQuantity
Paměť (bajty) | Msvm_MemorySettingData | VirtualQuantity
Maximální počet paměti, kterou může virtuální montovana spotřebovat | Msvm_MemorySettingData | Omezení
Dynamická paměť povolena | Msvm_MemorySettingData | DynamicMemoryEnabled
Název operačního systému/verze/fqdn | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems Název data
Stav napájení virtuálního virtuálního pracovního symbolu | Msvm_ComputerSystem | EnabledState
**Podrobnosti o disku** | 
Identifikátor disku | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Typ virtuálního pevného disku | Msvm_VirtualHardDiskSettingData | Typ
Velikost virtuálního pevného disku | Msvm_VirtualHardDiskSettingData | Maximální vnitřní velikost
Nadřazený virtuální pevný disk | Msvm_VirtualHardDiskSettingData | ParentPath
**Podrobnosti o nic** | 
IP adresy (syntetické síťové karty) | Msvm_GuestNetworkAdapterConfiguration | Adresy IP
Dhcp povoleno (syntetické síťové karty) | Msvm_GuestNetworkAdapterConfiguration | DhcpEnabled
ID síťové karty (syntetické síťové karty) | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC MAC adresa (syntetické nic) | Msvm_SyntheticEthernetPortSettingData | Adresa
ID síťové karty (starší síťové karty) | MsvmEmulatedEthernetPortSetting Data | InstanceID
ID rozhraní NIC MAC (starší síťové karty) | MsvmEmulatedEthernetPortSetting Data | Adresa

### <a name="performance-data"></a>Data výkonu

Tady jsou data o výkonu hypervirtuálního počítače, která zařízení shromažďuje a odesílá do Azure.

**Třída čítače výkonu** | **Čítač** | **Dopad posouzení**
--- | --- | ---
Virtuální procesor Hyper-V Hypervisor | % času běhu hosta | Doporučená velikost/náklady virtuálního počítače
Virtuální prostor dynamické paměti Hyper-V | Aktuální tlak (%)<br/> Viditelná fyzická paměť hosta (MB) | Doporučená velikost/náklady virtuálního počítače
Virtuální paměťové zařízení Hyper-V | Čtení bajtů za sekundu | Výpočet velikosti disku, nákladů na úložiště, velikosti virtuálního počítače
Virtuální paměťové zařízení Hyper-V | Zapsat bajty/s | Výpočet velikosti disku, nákladů na úložiště, velikosti virtuálního počítače
Virtuální síťový adaptér Hyper-V | Přijaté bajty za sekundu | Výpočet pro velikost virtuálního počítače
Virtuální síťový adaptér Hyper-V | Odeslané bajty za sekundu | Výpočet pro velikost virtuálního počítače

- Využití procesoru je součtem veškerého využití pro všechny virtuální procesory připojené k virtuálnímu počítači.
- Využití paměti je (Aktuální tlak * Host viditelné fyzické paměti) / 100.
- Hodnoty využití disku a sítě jsou shromažďovány z uvedených čítačů výkonu technologie Hyper-V.

## <a name="appliance-upgrades"></a>Vylepšení zařízení

Zařízení je upgradováno jako agenti Azure Migrate spuštěné na zařízení jsou aktualizovány. K tomu dochází automaticky, protože ve výchozím nastavení je v zařízení povolena automatická aktualizace. Toto výchozí nastavení můžete změnit tak, aby agenti aktualizovali ručně.

- **Vypnout automatickou aktualizaci**: Automatickou aktualizaci v registru vypnete nastavením HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft\AzureAppliance "AutoUpdate" na 0 (DWORD). Pokud se rozhodnete použít ruční aktualizace, je důležité aktualizovat všechny agenty v zařízení současně pomocí tlačítka **Aktualizovat** pro každého zastaralého agenta v zařízení.
- **Ruční aktualizace**: Při ručních aktualizacích se ujistěte, že aktualizujete všechny agenty v zařízení pomocí tlačítka **Aktualizovat** pro každého zastaralého agenta v zařízení. Nastavení aktualizace můžete kdykoli přepnout zpět na automatické aktualizace.

![Automatická aktualizace zařízení](./media/migrate-appliance/autoupdate.png)

## <a name="next-steps"></a>Další kroky

- [Přečtěte si, jak](how-to-set-up-appliance-vmware.md) nastavit zařízení pro vmware.
- [Přečtěte si, jak](how-to-set-up-appliance-hyper-v.md) nastavit zařízení pro technologie Hyper-V.
- [Přečtěte si, jak](how-to-set-up-appliance-physical.md) nastavit zařízení pro fyzické servery.

