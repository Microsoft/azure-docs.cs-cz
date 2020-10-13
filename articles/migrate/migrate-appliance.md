---
title: Zařízení Azure Migrate
description: Poskytuje souhrn podpory pro zařízení Azure Migrate.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: ac3c90f1c09d290d5112a0e0d7abc5218788caf7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91450048"
---
# <a name="azure-migrate-appliance"></a>Zařízení Azure Migrate

Tento článek shrnuje požadavky a požadavky na podporu pro zařízení Azure Migrate. 

## <a name="deployment-scenarios"></a>Scénáře nasazení

Zařízení Azure Migrate se používá v následujících scénářích.

**Scénář** | **Nástroj** | **Použití** 
--- | --- | ---
**Posouzení virtuálního počítače VMware** | Azure Migrate: posouzení serveru | Zjišťování virtuálních počítačů VMware<br/><br/> Zjistit aplikace a závislosti počítačů<br/><br/> Shromažďovat metadata počítače a metadata výkonu pro posouzení.
**Migrace bez agentů virtuálního počítače VMware** | Azure Migrate: Migrace serveru | Zjišťování virtuálních počítačů VMware <br/><br/> Replikace virtuálních počítačů VMware s migrací bez agentů
**Posouzení virtuálního počítače Hyper-V** | Azure Migrate: posouzení serveru | Zjišťování virtuálních počítačů Hyper-V<br/><br/> Shromažďovat metadata počítače a metadata výkonu pro posouzení.
**Posouzení fyzického počítače** |  Azure Migrate: posouzení serveru |  Objevte fyzické servery (nebo virtuální počítače, které považujete za fyzické servery).<br/><br/> Shromažďovat metadata počítače a metadata výkonu pro posouzení.

## <a name="deployment-methods"></a>Metody nasazení

Zařízení je možné nasadit pomocí několika metod:

- Zařízení se dá nasadit pomocí šablony pro virtuální počítače VMware a virtuální počítače Hyper-V (šablona vajíček pro VMware nebo VHD pro Hyper-V).
- Pokud nechcete šablonu používat, můžete zařízení nasadit pro VMware nebo Hyper-V pomocí skriptu PowerShellu.
- V Azure Government byste zařízení měli nasadit pomocí skriptu.
- U fyzických serverů vždy nasadíte zařízení pomocí skriptu.
- Odkazy ke stažení jsou k dispozici v následujících tabulkách.


## <a name="appliance---vmware"></a>Zařízení – VMware 

Následující tabulka shrnuje požadavky na zařízení Azure Migrate pro VMware.

**Požadavek** | **VMware** 
--- | ---
**Oprávnění** | Pokud chcete přistupovat k webové aplikaci místně nebo vzdáleně, musíte být správcem domény nebo místním správcem na počítači zařízení.
**Součásti zařízení** | Zařízení má následující součásti:<br/><br/> - **Aplikace pro správu**: Toto je webová aplikace pro vstup uživatele během nasazování zařízení. Používá se při posuzování počítačů pro migraci do Azure.<br/> - **Agent zjišťování**: Agent shromáždí data konfigurace počítače. Používá se při posuzování počítačů pro migraci do Azure.<br/>- **Agent kolektoru**: Agent shromažďuje údaje o výkonu. Používá se při posuzování počítačů pro migraci do Azure.<br/>- **Služba automatické aktualizace**: aktualizuje součásti zařízení (spouští se každých 24 hodin).<br/>- **Agent DRA**: orchestruje replikaci virtuálních počítačů a koordinuje komunikaci mezi replikovanými počítači a Azure. Používá se jenom při replikaci virtuálních počítačů VMware do Azure pomocí migrace bez agentů.<br/>- **Brána**: odesílá replikovaná data do Azure. Používá se jenom při replikaci virtuálních počítačů VMware do Azure pomocí migrace bez agentů.
**Podporované nasazení** | Nasaďte jako virtuální počítač VMware pomocí šablony vajíček.<br/><br/> Nasaďte jako virtuální počítač VMware nebo fyzický počítač pomocí instalačního skriptu PowerShellu.
**Podpora projektu** |  Zařízení může být přidruženo k jednomu projektu. <br/> K jednomu projektu může být přidružen libovolný počet zařízení.<br/> 
**Omezení zjišťování** | Zařízení může na vCenter Server zjistit až 10 000 virtuálních počítačů VMware.<br/> Zařízení se může připojit k jednomu vCenter Server.
**Šablona vajíček** | Stáhnout z portálu nebo [odsud](https://go.microsoft.com/fwlink/?linkid=2140333)<br/><br/> Velikost ke stažení je 11,9 GB.<br/><br/> Stažená šablona zařízení je součástí zkušební licence Windows Server 2016, která je platná po dobu 180 dnů. Pokud se zkušební období blíží vypršení platnosti, doporučujeme, abyste si stáhli a nasadili nové zařízení nebo aktivovali licenci k operačnímu systému virtuálního počítače zařízení.
**Skript prostředí PowerShell** | Informace najdete v tomto [článku](./deploy-appliance-script.md#set-up-the-appliance-for-vmware).<br/><br/> 
**Software a hardware** |  Zařízení by mělo běžet na počítači s Windows serverem 2016, 32-GB RAM, 8 vCPU, kolem 80 GB diskového úložiště a externím virtuálním přepínačem.<br/> Zařízení vyžaduje přístup k Internetu, a to buď přímo, nebo prostřednictvím proxy serveru.<br/><br/> Pokud spustíte zařízení na virtuálním počítači VMware, budete potřebovat dostatek prostředků na vCenter Server k přidělení virtuálního počítače, který splňuje požadavky.<br/><br/> Pokud zařízení spouštíte na fyzickém počítači, ujistěte se, že je spuštěný systém Windows Server 2016 a splňuje požadavky na hardware.
**Požadavky VMware** | Pokud zařízení nasadíte jako virtuální počítač VMware, musí být nasazen na hostiteli ESXi se spuštěnou verzí 5,5 nebo novější.<br/><br/> vCenter Server se spouští 5,5, 6,0, 6,5 nebo 6,7.
**VDDK (migrace bez agenta)** | Pokud zařízení nasadíte jako virtuální počítač VMware a máte spuštěnou migraci bez agenta, musí být na virtuálním počítači zařízení nainstalovaná VMware vSphere VDDK.
**Hodnota hash – VAJÍČKa** | [Ověřte](tutorial-discover-vmware.md#verify-security) hodnoty hash šablony vajíček.
**Hodnota hash – skript prostředí PowerShell** | [Ověřte](deploy-appliance-script.md#verify-file-security) hodnoty hash skriptu PowerShellu.




## <a name="appliance---hyper-v"></a>Zařízení – Hyper-V

**Požadavek** | **Hyper-V** 
--- | ---
**Oprávnění** | Pokud chcete přistupovat k webové aplikaci místně nebo vzdáleně, musíte být správcem domény nebo místním správcem na počítači zařízení.
**Součásti zařízení** | Zařízení má následující součásti:<br/><br/>- **Aplikace pro správu**: Toto je webová aplikace pro vstup uživatele během nasazování zařízení. Používá se při posuzování počítačů pro migraci do Azure.<br/> - **Agent zjišťování**: Agent shromáždí data konfigurace počítače. Používá se při posuzování počítačů pro migraci do Azure.<br/>- **Agent kolektoru**: Agent shromažďuje údaje o výkonu. Používá se při posuzování počítačů pro migraci do Azure.<br/>- **Služba automatické aktualizace**: aktualizuje součásti zařízení (spouští se každých 24 hodin).
**Podporované nasazení** | Nasaďte jako virtuální počítač Hyper-V pomocí šablony VHD.<br/><br/> Nasaďte jako virtuální počítač Hyper-V nebo fyzický počítač pomocí instalačního skriptu PowerShellu.
**Podpora projektu** |  Zařízení může být přidruženo k jednomu projektu. <br/> K jednomu projektu může být přidružen libovolný počet zařízení.<br/> 
**Omezení zjišťování** | Zařízení může zjistit až 5000 virtuálních počítačů Hyper-V.<br/> Zařízení se může připojit k až 300 hostitelům Hyper-V.
**Šablona VHD** | Složka zip, včetně VHD. Stáhněte si ho z portálu nebo [tady](https://go.microsoft.com/fwlink/?linkid=2140422).<br/><br/> Velikost ke stažení je 8,91 GB.<br/><br/> Stažená šablona zařízení je součástí zkušební licence Windows Server 2016, která je platná po dobu 180 dnů. Pokud se zkušební období blíží vypršení platnosti, doporučujeme, abyste si stáhli a nasadili nové zařízení nebo aktivovali licenci k operačnímu systému virtuálního počítače zařízení.
**Skript prostředí PowerShell** | Informace najdete v tomto [článku](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v).<br/><br/> 
**Software a hardware***   |  Zařízení by mělo běžet na počítači s Windows serverem 2016, 16 GB paměti RAM, 8 vCPU, přibližně 80 GB diskového úložiště a externím virtuálním přepínačem.<br/> Zařízení potřebuje statickou nebo dynamickou IP adresu a vyžaduje přístup k Internetu, a to buď přímo, nebo prostřednictvím proxy serveru.<br/><br/> Pokud zařízení spouštíte jako virtuální počítač Hyper-V, budete potřebovat dostatek prostředků na hostiteli Hyper-V, abyste mohli přidělit hardwarové požadavky.<br/><br/> Pokud zařízení spouštíte na fyzickém počítači, ujistěte se, že je spuštěný systém Windows Server 2016 a splňuje požadavky na hardware. 
**Požadavky technologie Hyper-V** | Pokud nasadíte zařízení se šablonou VHD, virtuální počítač zařízení, který poskytuje Azure Migrate, je Hyper-V VM verze 5,0.<br/><br/> Na hostiteli Hyper-V musí běžet Windows Server 2012 R2 nebo novější. 
**Hodnota hash – VHD** | [Ověřit](tutorial-discover-hyper-v.md#verify-security) Hodnoty hash šablony VHD
**Hodnota hash – skript prostředí PowerShell** | [Ověřte](deploy-appliance-script.md#verify-file-security) hodnoty hash skriptu PowerShellu.


## <a name="appliance---physical"></a>Zařízení – fyzické

**Požadavek** | **Fyzické** 
--- | ---
**Oprávnění** | Pokud chcete přistupovat k webové aplikaci místně nebo vzdáleně, musíte být správcem domény nebo místním správcem na počítači zařízení.
**Součásti zařízení** | Zařízení má následující součásti: <br/><br/> - **Aplikace pro správu**: Toto je webová aplikace pro vstup uživatele během nasazování zařízení. Používá se při posuzování počítačů pro migraci do Azure.<br/> - **Agent zjišťování**: Agent shromáždí data konfigurace počítače. Používá se při posuzování počítačů pro migraci do Azure.<br/>- **Agent kolektoru**: Agent shromažďuje údaje o výkonu. Používá se při posuzování počítačů pro migraci do Azure.<br/>- **Služba automatické aktualizace**: aktualizuje součásti zařízení (spouští se každých 24 hodin).
**Podporované nasazení** | Nasaďte jako vyhrazený fyzický počítač nebo virtuální počítač pomocí instalačního skriptu PowerShellu. Skript je k dispozici ke stažení na portálu.
**Podpora projektu** |  Zařízení může být přidruženo k jednomu projektu. <br/> K jednomu projektu může být přidružen libovolný počet zařízení.<br/> 
**Omezení zjišťování** | Zařízení může zjistit až 1000 fyzických serverů.
**Skript prostředí PowerShell** | Stáhněte si skript (AzureMigrateInstaller.ps1) ve složce zip z portálu nebo z [tohoto místa](https://go.microsoft.com/fwlink/?linkid=2140334). [Další informace](tutorial-discover-physical.md).<br/><br/> Velikost ke stažení je 85,8 MB.
**Software a hardware** |  Zařízení by mělo běžet na počítači s Windows serverem 2016, 16 GB paměti RAM, 8 vCPU, přibližně 80 GB diskového úložiště.<br/> Zařízení potřebuje statickou nebo dynamickou IP adresu a vyžaduje přístup k Internetu, a to buď přímo, nebo prostřednictvím proxy serveru.<br/><br/> Pokud zařízení spouštíte na fyzickém počítači, ujistěte se, že je spuštěný systém Windows Server 2016 a splňuje požadavky na hardware.<br/>_(V současné době se nasazení zařízení podporuje jenom v systému Windows Server 2016.)_
**Hodnota hash** | [Ověřte](tutorial-discover-physical.md#verify-security) hodnoty hash skriptu PowerShellu.

## <a name="url-access"></a>Přístup URL

Zařízení Azure Migrate potřebuje připojení k Internetu.

- Když zařízení nasadíte, Azure Migrate provede kontrolu připojení k požadovaným adresám URL.
- Je potřeba, abyste povolili přístup ke všem adresám URL v seznamu. Pokud provádíte pouze hodnocení, můžete přeskočit adresy URL, které jsou označeny jako povinné pouze pro migraci bez agentů VMware.
-  Pokud pro připojení k Internetu používáte proxy server založený na adrese URL, ujistěte se, že proxy překládá všechny záznamy CNAME přijaté při vyhledávání adres URL.

### <a name="public-cloud-urls"></a>Adresy URL veřejného cloudu

**Adresa URL** | **Podrobnosti**  
--- | --- |
*.portal.azure.com  | Přejděte na Azure Portal.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *. office.com | Přihlaste se ke svému předplatnému Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Vytvořte Azure Active Directory (AD) aplikace pro zařízení, které budou komunikovat s Azure Migrate.
management.azure.com | Vytvořte aplikace služby Azure AD, aby zařízení komunikovalo se službou Azure Migrate.
*.services.visualstudio.com | Nahrávat protokoly aplikací používané pro interní monitorování
*.vault.azure.net | Správa tajných kódů v Azure Key Vault. Poznámka: Zajistěte, aby k nim měli přístup počítače, ke kterým mají replikace.
aka.ms/* | Povolí přístup k odkazům. Používá se k aktualizaci Azure Migrate zařízení.
download.microsoft.com/download | Povolí stahování ze služby Stažení softwaru společnosti Microsoft.
*.servicebus.windows.net | Komunikace mezi zařízením a službou Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Připojte se k adresám URL služby Azure Migrate.
*.hypervrecoverymanager.windowsazure.com | **Používá se pro migraci bez agentů VMware.**<br/><br/> Připojte se k adresám URL služby Azure Migrate.
*.blob.core.windows.net |  **Používá se pro migraci bez agentů VMware.**<br/><br/>Nahrajte data do úložiště pro migraci.

### <a name="government-cloud-urls"></a>Adresy URL cloudu pro státní správu

**Adresa URL** | **Podrobnosti**  
--- | --- |
*. portal.azure.us  | Přejděte na Azure Portal.
graph.windows.net | Přihlaste se ke svému předplatnému Azure.
login.microsoftonline.us  | Vytvořte Azure Active Directory (AD) aplikace pro zařízení, které budou komunikovat s Azure Migrate.
management.usgovcloudapi.net | Vytvořte aplikace služby Azure AD, aby zařízení komunikovalo se službou Azure Migrate.
*.services.visualstudio.com | Nahrávat protokoly aplikací používané pro interní monitorování
*. vault.usgovcloudapi.net | Správa tajných kódů v Azure Key Vault.
aka.ms/* | Povolí přístup k odkazům. Používá se k aktualizaci Azure Migrate zařízení.
download.microsoft.com/download | Povolí stahování ze služby Stažení softwaru společnosti Microsoft.
*. servicebus.usgovcloudapi.net  | Komunikace mezi zařízením a službou Azure Migrate.
*. discoverysrv.windowsazure.us <br/> *. migration.windowsazure.us | Připojte se k adresám URL služby Azure Migrate.
*. hypervrecoverymanager.windowsazure.us | **Používá se pro migraci bez agentů VMware.**<br/><br/> Připojte se k adresám URL služby Azure Migrate.
*. blob.core.usgovcloudapi.net  |  **Používá se pro migraci bez agentů VMware.**<br/><br/>Nahrajte data do úložiště pro migraci.
*. applicationinsights.us | Nahrávat protokoly aplikací používané pro interní monitorování





## <a name="collected-data---vmware"></a>Shromážděná data – VMware

Zařízení shromažďuje metadata, údaje o výkonu a data analýzy závislostí (Pokud se používá [Analýza závislostí](concepts-dependency-visualization.md) bez agentů).

### <a name="metadata"></a>Metadata

Metadata zjištěná zařízením Azure Migrate vám pomůžou zjistit, jestli jsou počítače a aplikace připravené na migraci do Azure, počítačů a aplikací určených pro správnou velikost, plánů a analýz závislostí aplikace. Společnost Microsoft nepoužívá tato data v žádném auditu dodržování předpisů v licencích.

Tady je úplný seznam metadat virtuálních počítačů VMware, které zařízení shromažďuje a odesílá do Azure.

**ÚDAJŮ** | **OBJEKTŮ**
--- | --- 
**Podrobnosti o počítači** | 
ID virtuálního počítače | vm.Config. InstanceUuid 
název virtuálního počítače | vm.Config. Jméno
ID vCenter Server | VMwareClient. instance. UUID
Popis virtuálního počítače | vm.Summary.Config. Poznámky
Název licenčního produktu | síť. Client. ServiceContent. about. LicenseProductName
Typ operačního systému | síť. SummaryConfig.GuestFullName
Typ spouštění | vm.Config. Firmwar
Počet jader | vm.Config. Hardware. NumCPU
Paměť (MB) | vm.Config. Hardware. MemoryMB
Počet disků | vm.Config. Hardware. Device. ToList – (). FindAll (x => je VirtualDisk). Count
Seznam velikostí disků | vm.Config. Hardware. Device. ToList – (). FindAll (x => je VirtualDisk)
Seznam síťových adaptérů | vm.Config. Hardware. Device. ToList – (). FindAll (x => je VirtualEthernet). Count
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
Name | vnitřního. GetType (). Jméno
Typ podřízeného objektu | vnitřního. ChildType
Referenční informace | vnitřního. MoRef
Podrobnosti nadřazené položky | Kontejner. Parent
Podrobnosti složky na virtuální počítač | (Složka) kontejneru). ChildEntity. Type
Podrobnosti datového centra na virtuální počítač | (Datacenter) kontejner). VmFolder
Podrobnosti datacentra na složku hostitelů | (Datacenter) kontejner). HostFolder
Podrobnosti o clusteru na hostitele | ((ClusterComputeResource) kontejner). Provoz
Podrobnosti o hostiteli na virtuálním počítači | ((HostSystem) kontejner). SÍŤ

### <a name="performance-data"></a>Data výkonu


Tady je údaje o výkonu virtuálních počítačů VMware, které zařízení shromažďuje a odesílá do Azure.

**Data** | **Čítač** | **Dopad posouzení**
--- | --- | ---
Využití procesoru | CPU. Usage. Average | Doporučená velikost virtuálního počítače/náklady
Využití paměti | mem. Usage. Average | Doporučená velikost virtuálního počítače/náklady
Propustnost čtení z disku (MB za sekundu) | virtualDisk. Read. Average | Výpočet velikosti disku, nákladů na úložiště, velikosti virtuálního počítače
Propustnost zápisů na disk (MB za sekundu) | virtualDisk. Write. Average | Výpočet velikosti disku, nákladů na úložiště, velikosti virtuálního počítače
Operace čtení z disku za sekundu | virtualDisk. numberReadAveraged. Average | Výpočet velikosti disku, nákladů na úložiště, velikosti virtuálního počítače
Počet operací zápisu na disk za sekundu | virtualDisk. numberWriteAveraged. Average  | Výpočet velikosti disku, nákladů na úložiště, velikosti virtuálního počítače
Propustnost čtení síťových adaptérů (MB za sekundu) | NET. Received. Average | Výpočet pro velikost virtuálního počítače
Síťová karta zapisuje propustnost (MB za sekundu) | NET. přenášeno. Average  |Výpočet pro velikost virtuálního počítače


### <a name="installed-apps-metadata"></a>Metadata instalovaných aplikací

Funkce zjišťování aplikací shromažďuje nainstalované aplikace a data operačního systému.

#### <a name="windows-vm-apps-data"></a>Data aplikací pro virtuální počítače s Windows

Tady je nainstalovaná data aplikace, která zařízení shromažďuje z každého virtuálního počítače povoleného pro zjišťování aplikací. Tato data se odesílají do Azure.

**Data** | **Umístění registru** | **Klíč**
--- | --- | ---
Název aplikace  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\* <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayName
Verze  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayVersion 
Poskytovatel  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Publisher

#### <a name="windows-vm-features-data"></a>Data funkcí virtuálních počítačů s Windows

Tady jsou funkce data, která zařízení shromažďuje z každého virtuálního počítače, který je povolený pro zjišťování aplikací. Tato data se odesílají do Azure.

**Data**  | **Rutina PowerShellu** | **Vlastnost**
--- | --- | ---
Name  | Get-WindowsFeature  | Name
Typ funkce | Get-WindowsFeature  | FeatureType
Nadřazený  | Get-WindowsFeature  | Nadřazený

#### <a name="windows-vm-sql-server-metadata"></a>Metadata SQL Server virtuálních počítačů s Windows

Tady jsou metadata SQL serveru, která zařízení shromažďuje z virtuálních počítačů se systémem Microsoft SQL Server, který je povolený pro zjišťování aplikací. Tato data se odesílají do Azure.

**Data**  | **Umístění registru**  | **Klíč**
--- | --- | ---
Name  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server\Instance Names\SQL  | installedInstance
Edice  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | Edice 
Aktualizace Service Pack  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | SP
Verze  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | Verze 

#### <a name="windows-vm-operating-system-data"></a>Data operačního systému Windows VM

Zde jsou data operačního systému, která zařízení shromažďují každý virtuální počítač, který je povolený pro zjišťování aplikací. Tato data se odesílají do Azure.

Data  | Třída WMI  | Vlastnost třídy WMI
--- | --- | ---
Name  | Win32_operatingsystem  | Titulek
Verze  | Win32_operatingsystem  | Verze
Architektura  | Win32_operatingsystem  | OSArchitecture

#### <a name="linux-vm-apps-data"></a>Data aplikací pro virtuální počítače se systémem Linux

Tady je nainstalovaná data aplikace, která zařízení shromažďuje z každého virtuálního počítače povoleného pro zjišťování aplikací. V závislosti na operačním systému virtuálního počítače se spustí jeden nebo více příkazů. Tato data se odesílají do Azure.

Data  | Příkaz
--- | --- 
Name | ot./min., bázi dpkg – dotaz, přichycení
Verze | ot./min., bázi dpkg – dotaz, přichycení
Poskytovatel | ot./min., bázi dpkg – dotaz, přichycení

#### <a name="linux-vm-operating-system-data"></a>Data operačního systému Linux VM

Zde jsou data operačního systému, která zařízení shromažďují každý virtuální počítač, který je povolený pro zjišťování aplikací. Tato data se odesílají do Azure.

**Data**  | **Příkaz** 
--- | --- | ---
Name <br/> verze | Shromážděno z jednoho nebo více následujících souborů:<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oracle-release  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version 
Architektura | uname


### <a name="app-dependencies-metadata"></a>Metadata závislostí aplikace

Analýza závislosti bez agentů shromažďuje data o připojení a zpracování.

#### <a name="windows-vm-app-dependencies-data"></a>Data závislostí aplikací virtuálních počítačů s Windows

Tady jsou data připojení, která zařízení shromažďuje z každého virtuálního počítače, který je povolený pro analýzu závislostí bez agentů. Tato data se odesílají do Azure.

**Data** | **Použitý příkaz** 
--- | --- 
Místní port | netstat
Místní IP adresa | netstat
Vzdálený port | netstat
Vzdálená IP adresa | netstat
Stav připojení TCP | netstat
ID procesu | netstat
Počet aktivních připojení | netstat


Tady jsou data procesu, která zařízení shromažďuje z každého virtuálního počítače, který je povolený pro analýzu závislostí bez agentů. Tato data se odesílají do Azure.

**Data** | **Třída WMI** | **Vlastnost třídy WMI**
--- | --- | ---
Název procesu | Win32_Process | ExecutablePath
Argumenty procesu | Win32_Process | CommandLine
Název aplikace | Win32_Process | VersionInfo. ProductName – parametr vlastnosti ExecutablePath

#### <a name="linux-vm-app-dependencies-data"></a>Data závislostí aplikací pro virtuální počítače se systémem Linux

Tady je připojení a zpracování dat, která zařízení shromažďuje z každého virtuálního počítače Linux povoleného pro analýzu závislostí bez agentů. Tato data se odesílají do Azure.

**Data** | **Použitý příkaz** 
--- | ---
Místní port | netstat 
Místní IP adresa | netstat 
Vzdálený port | netstat 
Vzdálená IP adresa | netstat 
Stav připojení TCP | netstat 
Počet aktivních připojení | netstat
ID procesu  | netstat 
Název procesu | PS
Argumenty procesu | PS
Název aplikace | bázi dpkg nebo ot./min.



## <a name="collected-data---hyper-v"></a>Shromážděná data – Hyper-V

Zařízení shromažďuje metadata, údaje o výkonu a data analýzy závislostí (Pokud se používá [Analýza závislostí](concepts-dependency-visualization.md) bez agentů).

### <a name="metadata"></a>Metadata
Metadata zjištěná zařízením Azure Migrate vám pomůžou zjistit, jestli jsou počítače a aplikace připravené na migraci do Azure, počítačů a aplikací určených pro správnou velikost, plánů a analýz závislostí aplikace. Společnost Microsoft nepoužívá tato data v žádném auditu dodržování předpisů v licencích.

Tady je úplný seznam metadat virtuálních počítačů Hyper-V, které zařízení shromažďuje a odesílá do Azure.

**ÚDAJŮ** | **TŘÍDA WMI** | **VLASTNOST TŘÍDY WMI**
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
IP adresy (syntetické síťové adaptéry) | Msvm_GuestNetworkAdapterConfiguration | IP adresy
Protokol DHCP povolen (syntetické síťové adaptéry) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
ID síťové karty (syntetické síťové adaptéry) | Msvm_SyntheticEthernetPortSettingData | InstanceID
Adresa MAC síťové karty (syntetické síťové adaptéry) | Msvm_SyntheticEthernetPortSettingData | Adresa
ID síťové karty (starší síťové karty) | MsvmEmulatedEthernetPortSetting data | InstanceID
ID MAC síťové karty (starší síťové karty) | MsvmEmulatedEthernetPortSetting data | Adresa

### <a name="performance-data"></a>Data výkonu

Tady je údaje o výkonu virtuálního počítače Hyper, které zařízení shromažďuje a odesílá do Azure.

**Třída čítače výkonu** | **Čítač** | **Dopad posouzení**
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


## <a name="collected-data---physical"></a>Shromážděná data – fyzické

Zařízení shromažďuje metadata, údaje o výkonu a data analýzy závislostí (Pokud se používá [Analýza závislostí](concepts-dependency-visualization.md) bez agentů).

### <a name="windows-metadata"></a>Metadata Windows

Metadata zjištěná zařízením Azure Migrate vám pomůžou zjistit, jestli jsou počítače a aplikace připravené na migraci do Azure, počítačů a aplikací určených pro správnou velikost, plánů a analýz závislostí aplikace. Společnost Microsoft nepoužívá tato data v žádném auditu dodržování předpisů v licencích.

Tady je úplný seznam metadat Windows serveru, které zařízení shromažďuje a odesílá do Azure.

**ÚDAJŮ** | **TŘÍDA WMI** | **VLASTNOST TŘÍDY WMI**
--- | --- | ---
FQDN | Win32_ComputerSystem | Doména, název, PartOfDomain
Počet jader procesoru | Win32_PRocessor | NumberOfCores
Přidělená paměť | Win32_ComputerSystem | TotalPhysicalMemory
Sériové číslo systému BIOS | Win32_ComputerSystemProduct | IdentifyingNumber
GUID SYSTÉMU BIOS | Win32_ComputerSystemProduct | IDENTIFIKÁTOR
Typ spouštění | Win32_DiskPartition | Podívejte se na oddíl s typem = **GPT: systém** pro EFI/BIOS.
Název operačního systému | Win32_OperatingSystem | Titulek
Verze operačního systému |Win32_OperatingSystem | Verze
Architektura operačního systému | Win32_OperatingSystem | OSArchitecture
Počet disků | Win32_DiskDrive | Model, velikost, DeviceID, název média, název
Velikost disku | Win32_DiskDrive | Velikost
Seznam síťových karet | Win32_NetworkAdapterConfiguration | Popis, index
IP adresa NIC | Win32_NetworkAdapterConfiguration | IPAddress
Adresa MAC síťové karty | Win32_NetworkAdapterConfiguration | MACAddress

### <a name="linux-metadata"></a>Metadata pro Linux

Tady je úplný seznam metadat serveru Linux, které zařízení shromažďuje a odesílá do Azure.

**ÚDAJŮ** | **LINUX** 
--- | --- 
FQDN | Cat/proc/sys/kernel/hostname, hostname-f
Počet jader procesoru |  /Proc/cpuinfo \| awk mají '/^ procesor/{print $3} ' \| WC-l
Přidělená paměť | Cat/proc/meminfo \| grep MemTotal \| awk mají {printf "%. hodnotami 0f", $2/1024}
Sériové číslo systému BIOS | lshw \| grep "série:" \| head-N1 \| awk mají "{Print $2} <br/> /usr/sbin/dmidecode-t 1 \| grep ' sériový ' \| awk mají ' {$1 = ""; $2 = ""; Print} "
GUID SYSTÉMU BIOS | product_uuid Cat/sys/Class/DMI/ID/
Typ spouštění | [-d/sys/firmware/EFI]  && echo EFI \| \| echo BIOS
Název/verze operačního systému | K těmto souborům přistupujeme pro verzi a název operačního systému:<br/><br/> /etc/os-release<br/> /usr/lib/os-release <br/> /etc/enterprise-release <br/> /etc/redhat-release<br/> /etc/oracle-release<br/>  /etc/SuSE-release<br/>  /etc/lsb-release  <br/> /etc/debian_version
Architektura operačního systému | Uname-m
Počet disků | fdisk-l \| egrep ' disk. * bytes ' \| awk mají ' {Print $2} ' \| Vyjmout-F1-d ': '
Spouštěcí disk | DF/Boot \| SED-n 2p \| awk mají {Print $1}
Velikost disku | fdisk-l \| egrep ' disk. * bytes ' \| egrep $disk: \| awk mají ' {Print $5} '
Seznam síťových karet | IP-o-4 addr show \| awk mají {print $2}
IP adresa NIC | IP adresa show $nic \| grep inet \| awk mají ' {Print $2} ' \| Vyjmout-F1-d "/" 
Adresa MAC síťové karty | IP adresa show $nic \| grep ether  \| awk mají {Print $2}

### <a name="windows-performance-data"></a>Údaje o výkonu systému Windows

Zde jsou údaje o výkonu Windows serveru, které zařízení shromažďuje a odesílá do Azure.

**Data** | **Třída WMI** | **Vlastnost třídy WMI**
--- | --- | ---
Využití procesoru | Win32_PerfFormattedData_PerfOS_Processor | PercentIdleTime
Využití paměti | Win32_PerfFormattedData_PerfOS_Memory | AvailableMBytes
Počet síťových adaptérů | Win32_PerfFormattedData_Tcpip_NetworkInterface | Získá počet síťových zařízení.
Přijatá data na síťový adaptér | Win32_PerfFormattedData_Tcpip_NetworkInterface  | BytesReceivedPerSec
Data přenášená na síťový adaptér | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BytesSentPersec
Počet disků | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | Počet disků
Podrobnosti o disku | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec, DiskWriteBytesPerSec, DiskReadsPerSec, DiskReadBytesPerSec.

### <a name="linux-performance-data"></a>Data o výkonu pro Linux

Tady je údaje o výkonu serveru pro Linux, které zařízení shromažďuje a odesílá do Azure.

**Data** | **Linux** 
--- | --- 
Využití procesoru | /Proc/stat/Cat| grep ' CPU '/proc/stat
Využití paměti | bezplatný \| grep mem \| awk mají ' {Print $3/$ 2 * 100,0} '
Počet síťových adaptérů | lshw – třída \| grep ETH [0-60] \| WC-l
Přijatá data na síťový adaptér | Cat/sys/Class/NET/ETH $ nic/Statistika/rx_bytes
Data přenášená na síťový adaptér | Cat/sys/Class/NET/ETH $ nic/Statistika/tx_bytes
Počet disků | fdisk-l \| egrep ' disk. * bytes ' \| awk mají ' {Print $2} ' \| Vyjmout-F1-d ': '
Podrobnosti o disku | /Proc/diskstats Cat


## <a name="appliance-upgrades"></a>Upgrady zařízení

Zařízení se upgraduje, protože Azure Migrate agenti, kteří běží na zařízení, se aktualizují. K tomu dojde automaticky, protože ve výchozím nastavení je na zařízení povolená Automatická aktualizace. Toto výchozí nastavení můžete změnit tak, aby se služby zařízení aktualizovaly ručně.

### <a name="turn-off-auto-update"></a>Vypnout automatické aktualizace

1. V počítači, na kterém je zařízení spuštěno, otevřete Editor registru.
2. Přejděte na **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Pokud chcete vypnout automatické aktualizace, vytvořte klíč registru **AutoUpdate** s hodnotou DWORD 0.

    ![Nastavení klíče registru](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>Zapnout automatické aktualizace

Automatické aktualizace můžete zapnout pomocí některé z těchto metod:

- Odstraněním klíče registru AutoUpdate z HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance.
- Pokud chcete zapnout automatické aktualizace, klikněte na **Zobrazit služby zařízení** z nejnovějších kontrol aktualizací na panelu **nastavit předpoklady** .

Postup odstranění klíče registru:

1. V počítači, na kterém je zařízení spuštěno, otevřete Editor registru.
2. Přejděte na **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Odstraňte klíč registru **AutoUpdate**, který byl dříve vytvořen pro vypnutí automatických aktualizací.

Zapnutí z Configuration Manager zařízení po dokončení zjišťování:

1. V nástroji Správce konfigurace zařízení, přejít na panel **nastavení požadavků**
2. V části nejnovější aktualizace klikněte na **Zobrazit služby zařízení** a kliknutím na odkaz zapněte automatické aktualizace.

    ![Zapnout automatické aktualizace](./media/migrate-appliance/autoupdate-off.png)

### <a name="check-the-appliance-services-version"></a>Ověřit verzi služby zařízení

Verzi služby zařízení můžete ověřit pomocí některé z těchto metod:

- V nástroji Configuration Manager pro zařízení klikněte na panel **nastavit požadované součásti** .
- Na počítači zařízení v **Ovládacích panelech**  >  **programy a funkce**.

Vrácení se změnami do Správce konfigurace zařízení:

1. V nástroji Správce konfigurace zařízení, přejít na panel **nastavení požadavků**
2. V části nejnovější aktualizace klikněte na **Zobrazit služby zařízení**.

    ![Ověřit verzi](./media/migrate-appliance/versions.png)

Chcete-li se vrátit do ovládacích panelů, postupujte takto:

1. Na zařízení klikněte na **Start**  >  **Control Panel**  >  **programy a funkce** .
2. Podívejte se na verze služeb zařízení v seznamu.

    ![Kontrola verze v Ovládacích panelech](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>Ruční aktualizace starší verze

Pokud používáte starší verzi nějaké součásti, musíte službu odinstalovat a ručně aktualizovat na nejnovější verzi.

1. Chcete-li vyhledat nejnovější verze služby zařízení, [stáhněte](https://aka.ms/latestapplianceservices) LatestComponents.jsv souboru.
2.    Po stažení otevřete LatestComponents.jsv souboru poznámkového bloku.
3. Vyhledejte nejnovější verzi služby v souboru a odkaz pro stažení. Například:

    "Name": "ASRMigrationWebApp"; "DownloadLink": " https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi ", "Version": "6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a81e75469b84"

4.    Stáhněte si nejnovější verzi zastaralé služby pomocí odkazu ke stažení v souboru.
5. Po stažení spusťte následující příkaz v okně příkazového řádku správce, abyste ověřili integritu staženého souboru MSI.

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ``` Příklad: C: \> certutil-HashFile C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. Ověřte, že výstup příkazu odpovídá položce hodnoty hash pro službu v souboru (například hodnota hash MD5 výše).
6. Nyní spusťte instalační službu MSI a nainstalujte ji. Je to tichá instalace a po dokončení se okno instalace zavře.
7. Po dokončení instalace ověřte verzi služby v části **Control panel**  >  **programy a funkce**v Ovládacích panelech. Verze služby by teď měla být upgradována na nejnovější verzi uvedenou v souboru JSON.



## <a name="next-steps"></a>Další kroky

- [Přečtěte si, jak](how-to-set-up-appliance-vmware.md) nastavit zařízení pro VMware.
- [Přečtěte si, jak](how-to-set-up-appliance-hyper-v.md) nastavit zařízení pro Hyper-V.
- [Přečtěte si, jak](how-to-set-up-appliance-physical.md) nastavit zařízení pro fyzické servery.

