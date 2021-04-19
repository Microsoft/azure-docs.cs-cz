---
title: Zařízení Azure Migrate
description: Poskytuje souhrn podpory pro zařízení Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: d7fc04e65e2b79d43c48acd5a8c621f28d5c0403
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714663"
---
# <a name="azure-migrate-appliance"></a>Zařízení Azure Migrate

Tento článek shrnuje požadavky a požadavky na podporu pro zařízení Azure Migrate.

## <a name="deployment-scenarios"></a>Scénáře nasazení

Zařízení Azure Migrate se používá v následujících scénářích.

**Scénář** | **Nástroj** | **Používá se pro**
--- | --- | ---
**Zjišťování a posouzení serverů běžících v prostředí VMware** | Azure Migrate: zjišťování a posouzení | Zjišťování serverů běžících ve vašem prostředí VMware<br/><br/> Proveďte zjišťování nainstalovaného inventáře softwaru, analýzu závislostí bez agentů a Objevte SQL Server instance a databáze.<br/><br/> Shromažďovat metadata o konfiguraci serveru a výkonu pro posouzení.
**Migrace serverů se systémem v prostředí VMware bez agentů** | Azure Migrate: Migrace serveru | Objevte servery běžící v prostředí VMware. <br/><br/> Replikace serverů bez instalace agentů.
**Zjišťování a posouzení serverů, které běží v prostředí Hyper-V** | Azure Migrate: zjišťování a posouzení | Objevte servery, které jsou spuštěné ve vašem prostředí Hyper-V.<br/><br/> Shromažďovat metadata o konfiguraci serveru a výkonu pro posouzení.
**Zjišťování a hodnocení fyzických nebo virtualizovaných serverů v místním prostředí** |  Azure Migrate: zjišťování a posouzení |  Objevte fyzické nebo virtualizované servery místně.<br/><br/> Shromažďovat metadata o konfiguraci serveru a výkonu pro posouzení.

## <a name="deployment-methods"></a>Metody nasazení

Zařízení je možné nasadit pomocí několika metod:

- Zařízení se dá nasadit pomocí šablony pro servery, které běží v prostředí VMware nebo Hyper-V ([Šablona vajíček pro VMware](how-to-set-up-appliance-vmware.md) nebo [VHD pro Hyper-v](how-to-set-up-appliance-hyper-v.md)).
- Pokud nechcete používat šablonu, můžete zařízení nasadit pro prostředí VMware nebo Hyper-V pomocí [skriptu instalačního programu PowerShell](deploy-appliance-script.md).
- V Azure Government byste zařízení měli nasadit pomocí skriptu instalačního programu PowerShell. [Tady](deploy-appliance-script-government.md)najdete postup nasazení.
- U fyzických nebo virtualizovaných serverů v místním prostředí nebo v jakémkoli jiném cloudu vždy nasadíte zařízení pomocí skriptu instalačního programu PowerShell. [Tady](how-to-set-up-appliance-physical.md)najdete postup nasazení.
- Odkazy ke stažení jsou k dispozici v následujících tabulkách.

## <a name="appliance---vmware"></a>Zařízení – VMware

Následující tabulka shrnuje požadavky na zařízení Azure Migrate pro VMware.

> [!Note]
> Zjišťování a hodnocení instancí SQL Server a databází spuštěných ve vašem prostředí VMware je teď ve verzi Preview. Pokud chcete tuto funkci vyzkoušet, použijte [**tento odkaz**](https://aka.ms/AzureMigrate/SQL) a vytvořte projekt v oblasti **Austrálie – východ**. Pokud již máte projekt v oblasti Austrálie – východ a chcete tuto funkci vyzkoušet, na portálu se ujistěte, že jste splnili tyto [**požadavky**](how-to-discover-sql-existing-project.md).

**Požadavek** | **VMware**
--- | ---
**Oprávnění** | Chcete-li získat přístup k nástroji Configuration Manager pro zařízení místně nebo vzdáleně, je nutné mít na serveru zařízení místní nebo doménový uživatelský účet s oprávněními správce.
**Služby zařízení** | Zařízení má následující služby:<br/><br/> - **Správce konfigurace zařízení**: Jedná se o webovou aplikaci, která se dá nakonfigurovat se zdrojovými podrobnostmi pro zahájení zjišťování a posouzení serverů.<br/> - **Agent zjišťování VMware**: Agent shromáždí metadata konfigurace serveru, která se dají použít k vytvoření jako místních posouzení.<br/>- **Agent vyhodnocení VMware**: Agent shromažďuje metadata výkonu serveru, která lze použít k vytvoření posouzení na základě výkonu.<br/>- **Služba automatické aktualizace**: služba udržuje všechny agenty spuštěné v zařízení v aktuálním stavu. Automaticky se spustí každých 24 hodin.<br/>- **Agent DRA**: orchestruje replikaci serveru a koordinuje komunikaci mezi replikovanými servery a Azure. Používá se jenom při replikaci serverů do Azure pomocí migrace bez agentů.<br/>- **Brána**: odesílá replikovaná data do Azure. Používá se jenom při replikaci serverů do Azure pomocí migrace bez agentů.<br/>- **SQL Discovery and Assessment agent**: odesílá metadata konfigurace a výkonu SQL Server instancí a databází do Azure.
**Omezení projektu** |  Zařízení se dá registrovat jenom v jednom projektu.<br/> Jeden projekt může mít několik registrovaných zařízení.
**Omezení zjišťování** | Zařízení může zjistit až 10 000 serverů běžících na vCenter Server.<br/> Zařízení se může připojit k jednomu vCenter Server.
**Podporované nasazení** | Nasadit jako nový server běžící na vCenter Server pomocí šablony pro VAJÍČKy.<br/><br/> Nasaďte na stávající server se systémem Windows Server 2016 pomocí skriptu instalačního programu prostředí PowerShell.
**Šablona vajíček** | Stáhnout z projektu nebo [odsud](https://go.microsoft.com/fwlink/?linkid=2140333)<br/><br/> Velikost ke stažení je 11,9 GB.<br/><br/> Stažená šablona zařízení je součástí zkušební licence Windows Server 2016, která je platná po dobu 180 dnů.<br/>Pokud se zkušební období blíží vypršení platnosti, doporučujeme, abyste si stáhli a nasadili nové zařízení pomocí šablony vajíček, nebo aktivujete licenci operačního systému serveru zařízení.
**Ověření vajíček** | [Ověřte](tutorial-discover-vmware.md#verify-security) , že se šablona vajíčka stažená z projektu kontroluje pomocí hodnot hash.
**Skript prostředí PowerShell** | Informace o tom, jak nasadit zařízení pomocí skriptu instalačního programu PowerShellu, najdete v tomto [článku](./deploy-appliance-script.md#set-up-the-appliance-for-vmware) .<br/><br/> 
**Požadavky na hardware a síť** |  Zařízení by mělo běžet na serveru se systémem Windows Server 2016, 32-GB RAM, 8 vCPU, okolo 80 GB diskového úložiště a externím virtuálním přepínačem.<br/> Zařízení vyžaduje přístup k Internetu, a to buď přímo, nebo prostřednictvím proxy serveru.<br/><br/> Pokud zařízení nasadíte pomocí šablony vajíček, budete potřebovat dostatek prostředků na vCenter Server k vytvoření serveru, který splňuje požadavky na hardware.<br/><br/> Pokud zařízení spustíte na stávajícím serveru, ujistěte se, že běží na Windows serveru 2016 a splňuje požadavky na hardware.<br/>_(V současné době se nasazení zařízení podporuje jenom v systému Windows Server 2016.)_
**Požadavky VMware** | Pokud zařízení nasadíte jako server na vCenter Server, musí být nasazená na vCenter Server, na kterém běží 5,5, 6,0, 6,5 nebo 6,7 a hostitel ESXi, na kterém běží verze 5,5 nebo novější.<br/><br/> 
**VDDK (migrace bez agenta)** | Pokud chcete zařízení využít k migraci serverů bez agenta, musí být na serveru zařízení nainstalovaná VMware vSphere VDDK.

## <a name="appliance---hyper-v"></a>Zařízení – Hyper-V

**Požadavek** | **Hyper-V**
--- | ---
**Oprávnění** | Chcete-li získat přístup k nástroji Configuration Manager pro zařízení místně nebo vzdáleně, je nutné mít na serveru zařízení místní nebo doménový uživatelský účet s oprávněními správce.
**Služby zařízení** | Zařízení má následující služby:<br/><br/> - **Správce konfigurace zařízení**: Jedná se o webovou aplikaci, která se dá nakonfigurovat se zdrojovými podrobnostmi pro zahájení zjišťování a posouzení serverů.<br/> - **Agent zjišťování**: Agent shromažďuje metadata konfigurace serveru, která lze použít k vytvoření jako místní hodnocení.<br/>- **Agent hodnocení**: Agent shromažďuje metadata výkonu serveru, která lze použít k vytvoření posouzení na základě výkonu.<br/>- **Služba automatické aktualizace**: služba udržuje všechny agenty spuštěné v zařízení v aktuálním stavu. Automaticky se spustí každých 24 hodin.
**Omezení projektu** |  Zařízení se dá registrovat jenom v jednom projektu.<br/> Jeden projekt může mít několik registrovaných zařízení.
**Omezení zjišťování** | Zařízení může zjistit až 5000 serverů, které běží v prostředí Hyper-V.<br/> Zařízení se může připojit k až 300 hostitelům Hyper-V.
**Podporované nasazení** | Nasaďte jako server, který běží na hostiteli Hyper-V pomocí šablony VHD.<br/><br/> Nasaďte na stávající server se systémem Windows Server 2016 pomocí skriptu instalačního programu prostředí PowerShell.
**Šablona VHD** | Soubor zip, který obsahuje virtuální pevný disk. Stáhněte si z projektu nebo [tady](https://go.microsoft.com/fwlink/?linkid=2140422).<br/><br/> Velikost ke stažení je 8,91 GB.<br/><br/> Stažená šablona zařízení je součástí zkušební licence Windows Server 2016, která je platná po dobu 180 dnů.<br/> Pokud se zkušební období blíží vypršení platnosti, doporučujeme, abyste si stáhli a nasadili nové zařízení nebo aktivovali licenci operačního systému serveru zařízení.
**Ověření VHD** | Kontrolou hodnot hash [Ověřte, zda](tutorial-discover-hyper-v.md#verify-security) je šablona VHD stažená z projektu.
**Skript prostředí PowerShell** | Informace o tom, jak nasadit zařízení pomocí skriptu instalačního programu PowerShellu, najdete v tomto [článku](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v) .<br/>
**Požadavky na hardware a síť**  |  Zařízení by mělo běžet na serveru s Windows serverem 2016, 16 GB paměti RAM, 8 vCPU, přibližně 80 GB diskového úložiště a externím virtuálním přepínačem.<br/> Zařízení potřebuje statickou nebo dynamickou IP adresu a vyžaduje přístup k Internetu, a to buď přímo, nebo prostřednictvím proxy serveru.<br/><br/> Pokud zařízení spouštíte jako server, na kterém běží na hostiteli Hyper-V, potřebujete na hostiteli dostatek prostředků, aby bylo možné vytvořit server, který splňuje požadavky na hardware.<br/><br/> Pokud zařízení spustíte na stávajícím serveru, ujistěte se, že běží na Windows serveru 2016 a splňuje požadavky na hardware.<br/>_(V současné době se nasazení zařízení podporuje jenom v systému Windows Server 2016.)_
**Požadavky technologie Hyper-V** | Pokud zařízení nasadíte pomocí šablony VHD, zařízení, které poskytuje Azure Migrate, má virtuální počítač Hyper-V verze 5,0.<br/><br/> Na hostiteli Hyper-V musí běžet Windows Server 2012 R2 nebo novější.

## <a name="appliance---physical"></a>Zařízení – fyzické

**Požadavek** | **Fyzické**
--- | ---
**Oprávnění** | Chcete-li získat přístup k nástroji Configuration Manager pro zařízení místně nebo vzdáleně, je nutné mít na serveru zařízení místní nebo doménový uživatelský účet s oprávněními správce.
**Služby zařízení** | Zařízení má následující služby:<br/><br/> - **Správce konfigurace zařízení**: Jedná se o webovou aplikaci, která se dá nakonfigurovat se zdrojovými podrobnostmi pro zahájení zjišťování a posouzení serverů.<br/> - **Agent zjišťování**: Agent shromažďuje metadata konfigurace serveru, která lze použít k vytvoření jako místní hodnocení.<br/>- **Agent hodnocení**: Agent shromažďuje metadata výkonu serveru, která lze použít k vytvoření posouzení na základě výkonu.<br/>- **Služba automatické aktualizace**: služba udržuje všechny agenty spuštěné v zařízení v aktuálním stavu. Automaticky se spustí každých 24 hodin.
**Omezení projektu** |  Zařízení se dá registrovat jenom v jednom projektu.<br/> Jeden projekt může mít několik registrovaných zařízení.<br/>
**Omezení zjišťování** | Zařízení může zjistit až 1000 fyzických serverů.
**Podporované nasazení** | Nasaďte na stávající server se systémem Windows Server 2016 pomocí skriptu instalačního programu prostředí PowerShell.
**Skript prostředí PowerShell** | Stáhněte si skript (AzureMigrateInstaller.ps1) do souboru zip z projektu nebo z [tohoto místa](https://go.microsoft.com/fwlink/?linkid=2140334). [Další informace](tutorial-discover-physical.md).<br/><br/> Velikost ke stažení je 85,8 MB.
**Ověření skriptu** | Pomocí kontroly hodnot hash [Ověřte](tutorial-discover-physical.md#verify-security) , že se skript instalačního programu PowerShellu stažený z projektu stáhl.
**Požadavky na hardware a síť** |  Zařízení by mělo běžet na serveru se systémem Windows Server 2016, 16 GB paměti RAM, 8 vCPU, přibližně 80 GB diskového úložiště.<br/> Zařízení potřebuje statickou nebo dynamickou IP adresu a vyžaduje přístup k Internetu, a to buď přímo, nebo prostřednictvím proxy serveru.<br/><br/> Pokud zařízení spustíte na stávajícím serveru, ujistěte se, že běží na Windows serveru 2016 a splňuje požadavky na hardware.<br/>_(V současné době se nasazení zařízení podporuje jenom v systému Windows Server 2016.)_

## <a name="url-access"></a>Přístup URL

Zařízení Azure Migrate potřebuje připojení k Internetu.

- Když zařízení nasadíte, Azure Migrate provede kontrolu připojení k požadovaným adresám URL.
- Je potřeba, abyste povolili přístup ke všem adresám URL v seznamu. Pokud provádíte pouze hodnocení, můžete přeskočit adresy URL, které jsou označeny jako vyžadované pro migraci bez agentů VMware.
- Pokud pro připojení k Internetu používáte proxy server založený na adrese URL, ujistěte se, že proxy překládá všechny záznamy CNAME přijaté při vyhledávání adres URL.

### <a name="public-cloud-urls"></a>Adresy URL veřejného cloudu

**Adresa URL** | **Podrobnosti**  
--- | --- |
*.portal.azure.com  | Přejděte na Azure Portal.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *. office.com | Přihlaste se ke svému předplatnému Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Vytvořte Azure Active Directory (AD) aplikace pro zařízení, které budou komunikovat s Azure Migrate.
management.azure.com | Vytvořte aplikace služby Azure AD, aby zařízení komunikovalo s Azure Migrate.
*.services.visualstudio.com | Nahrávat protokoly zařízení používané pro interní monitorování
*.vault.azure.net | Správa tajných kódů v Azure Key Vault.<br/> Poznámka: Zajistěte, aby k nim měli přístup servery, ke kterým mají replikace.
aka.ms/* | Umožněte přístup i k odkazům. slouží ke stažení a instalaci nejnovějších aktualizací pro služby zařízení.
download.microsoft.com/download | Povolí stahování z webu Microsoft Download Center.
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
*.services.visualstudio.com | Nahrávat protokoly zařízení používané pro interní monitorování
*. vault.usgovcloudapi.net | Správa tajných kódů v Azure Key Vault.
aka.ms/* | Umožněte přístup i k odkazům. slouží ke stažení a instalaci nejnovějších aktualizací pro služby zařízení.
download.microsoft.com/download | Povolí stahování z webu Microsoft Download Center.
*. servicebus.usgovcloudapi.net  | Komunikace mezi zařízením a službou Azure Migrate.
*. discoverysrv.windowsazure.us <br/> *. migration.windowsazure.us | Připojte se k adresám URL služby Azure Migrate.
*. hypervrecoverymanager.windowsazure.us | **Používá se pro migraci bez agentů VMware.**<br/><br/> Připojte se k adresám URL služby Azure Migrate.
*. blob.core.usgovcloudapi.net  |  **Používá se pro migraci bez agentů VMware.**<br/><br/>Nahrajte data do úložiště pro migraci.
*. applicationinsights.us | Nahrávat protokoly zařízení používané pro interní monitorování  

### <a name="public-cloud-urls-for-private-link-connectivity"></a>Adresy URL veřejného cloudu pro připojení privátního propojení

Zařízení musí mít přístup k následujícím adresám URL (přímo nebo prostřednictvím proxy) přes a nad přístupem privátního propojení. 

**Adresa URL** | **Podrobnosti**  
--- | --- | 
*.portal.azure.com  | Přejděte na Azure Portal.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *. office.com | Přihlaste se ke svému předplatnému Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Vytvořte Azure Active Directory (AD) aplikace pro zařízení, které budou komunikovat s Azure Migrate.
management.azure.com | Vytvořte aplikace služby Azure AD, aby zařízení komunikovalo s Azure Migrate.
*. services.visualstudio.com (volitelné) | Nahrávat protokoly zařízení používané pro interní monitorování
aka.ms/* (volitelné) | Umožněte přístup i k odkazům. slouží ke stažení a instalaci nejnovějších aktualizací pro služby zařízení.
download.microsoft.com/download | Povolí stahování z webu Microsoft Download Center.
*.servicebus.windows.net | **Používá se pro migraci bez agentů VMware.**<br/><br/> Komunikace mezi zařízením a službou Azure Migrate.
*.vault.azure.net | **Používá se pro migraci bez agentů VMware.**<br/><br/>  Zajistěte, aby k tomuto serveru měli přístup servery pro replikaci.
*.hypervrecoverymanager.windowsazure.com | **Používá se pro migraci bez agentů VMware.**<br/><br/> Připojte se k adresám URL služby Azure Migrate.
*.blob.core.windows.net |  **Používá se pro migraci bez agentů VMware.**<br/><br/>Nahrajte data do úložiště pro migraci.

### <a name="government-cloud-urls-for-private-link-connectivity"></a>Adresy URL cloudu pro státní správu pro připojení privátních odkazů   

Zařízení musí mít přístup k následujícím adresám URL (přímo nebo prostřednictvím proxy) přes a nad přístupem privátního propojení. 

**Adresa URL** | **Podrobnosti**  
--- | --- |
*. portal.azure.us  | Přejděte na Azure Portal.
graph.windows.net | Přihlaste se ke svému předplatnému Azure.
login.microsoftonline.us  | Vytvořte Azure Active Directory (AD) aplikace pro zařízení, které budou komunikovat s Azure Migrate.
management.usgovcloudapi.net | Vytvořte aplikace služby Azure AD, aby zařízení komunikovalo se službou Azure Migrate.
*. services.visualstudio.com (volitelné) | Nahrávat protokoly zařízení používané pro interní monitorování
aka.ms/* (volitelné) | Umožněte přístup i k odkazům. slouží ke stažení a instalaci nejnovějších aktualizací pro služby zařízení.
download.microsoft.com/download | Povolí stahování z webu Microsoft Download Center.
*. servicebus.usgovcloudapi.net  | **Používá se pro migraci bez agentů VMware.**<br/><br/> Komunikace mezi zařízením a službou Azure Migrate. 
*. vault.usgovcloudapi.net | **Používá se pro migraci bez agentů VMware.**<br/><br/> Správa tajných kódů v Azure Key Vault.
*. hypervrecoverymanager.windowsazure.us | **Používá se pro migraci bez agentů VMware.**<br/><br/> Připojte se k adresám URL služby Azure Migrate.
*. blob.core.usgovcloudapi.net  |  **Používá se pro migraci bez agentů VMware.**<br/><br/>Nahrajte data do úložiště pro migraci.
*. applicationinsights.us (volitelné) | Nahrávat protokoly zařízení používané pro interní monitorování  

## <a name="collected-data---vmware"></a>Shromážděná data – VMware

Zařízení shromažďuje metadata o konfiguraci, metadata výkonu a data závislostí serveru (Pokud se používá [Analýza závislostí](concepts-dependency-visualization.md) bez agenta).

### <a name="metadata"></a>Metadata

Metadata zjištěná zařízením Azure Migrate vám pomůžou zjistit, jestli jsou servery připravené na migraci do Azure, servery pro pravou velikost, náklady na plánování a analyzovat závislosti aplikací. Společnost Microsoft nepoužívá tato data v žádném auditu dodržování předpisů v licencích.

Tady je úplný seznam metadat serveru, které zařízení shromažďuje a odesílá do Azure.

**ÚDAJŮ** | **OBJEKTŮ**
--- | ---
**Podrobnosti serveru** |
ID serveru | vm.Config. InstanceUuid
Název serveru | vm.Config. Jméno
ID vCenter Server | VMwareClient. instance. UUID
Popis serveru | vm.Summary.Config. Poznámky
Název licenčního produktu | VM. Client. ServiceContent. about. LicenseProductName
Typ operačního systému | VM. SummaryConfig. GuestFullName
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
Adresy IPv4 | vm.Guest.Net
IPv6 adresy | vm.Guest.Net
Propustnost čtení (MB za sekundu) | NET. Received. Average
Propustnost zápisu (MB za sekundu) | NET. přenášeno. Average
**Podrobnosti o cestě inventáře** |
Name | vnitřního. GetType (). Jméno
Typ podřízeného objektu | vnitřního. ChildType
Referenční informace | vnitřního. MoRef
Podrobnosti nadřazené položky | Kontejner. Parent
Podrobnosti složky na server | (Složka) kontejneru). ChildEntity. Type
Podrobnosti datacentra na server | (Datacenter) kontejner). VmFolder
Podrobnosti datacentra na složku hostitelů | (Datacenter) kontejner). HostFolder
Podrobnosti o clusteru na hostitele | ((ClusterComputeResource) kontejner). Provoz
Podrobnosti o hostiteli na server | ((HostSystem) kontejner). SÍŤ

### <a name="performance-data"></a>Data výkonu

Zde jsou údaje o výkonu, které zařízení shromažďuje pro Server běžící na VMware a odesílá je do Azure.

**Data** | **Čítač** | **Dopad posouzení**
--- | --- | ---
Využití procesoru | CPU. Usage. Average | Doporučená velikost serveru/náklady
Využití paměti | mem. Usage. Average | Doporučená velikost serveru/náklady
Propustnost čtení z disku (MB za sekundu) | virtualDisk. Read. Average | Výpočet pro velikost disku, náklady na úložiště, velikost serveru
Propustnost zápisů na disk (MB za sekundu) | virtualDisk. Write. Average | Výpočet pro velikost disku, náklady na úložiště, velikost serveru
Operace čtení z disku za sekundu | virtualDisk. numberReadAveraged. Average | Výpočet pro velikost disku, náklady na úložiště, velikost serveru
Počet operací zápisu na disk za sekundu | virtualDisk. numberWriteAveraged. Average  | Výpočet pro velikost disku, náklady na úložiště, velikost serveru
Propustnost čtení síťových adaptérů (MB za sekundu) | NET. Received. Average | Výpočet pro velikost serveru
Síťová karta zapisuje propustnost (MB za sekundu) | NET. přenášeno. Average  |Výpočet pro velikost serveru

### <a name="installed-software-inventory"></a>Nainstalovaný inventář softwaru

Zařízení shromažďuje data o nainstalovaném inventáři softwaru na serverech.

#### <a name="windows-server-software-inventory-data"></a>Data inventáře softwaru Windows serveru

Zde jsou data inventáře softwaru, která zařízení shromažďuje z každého zjištěného Windows serveru ve vašem prostředí VMware.

**Data** | **Umístění registru** | **Klíč**
--- | --- | ---
Název aplikace  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\* <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayName
Verze  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayVersion
Poskytovatel  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Publisher

#### <a name="windows-server-features-data"></a>Data funkcí Windows serveru

Tady jsou funkce data, která zařízení shromažďuje z každého zjištěného Windows serveru ve vašem prostředí VMware.

**Data**  | **Rutina PowerShellu** | **Vlastnost**
--- | --- | ---
Name  | Get-WindowsFeature  | Name
Typ funkce | Get-WindowsFeature  | FeatureType
Nadřazený  | Get-WindowsFeature  | Nadřazený

#### <a name="sql-server-metadata"></a>SQL Server metadata

Zde jsou SQL Serverá data, která zařízení shromažďuje z každého zjištěného Windows serveru ve vašem prostředí VMware.

**Data**  | **Umístění registru**  | **Klíč**
--- | --- | ---
Name  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server\Instance Names\SQL  | installedInstance
Edice  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | Edice
Aktualizace Service Pack  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | SP
Verze  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | Verze

#### <a name="windows-server-operating-system-data"></a>Data operačního systému Windows Server

Zde jsou data operačního systému, která zařízení shromažďuje z každého zjištěného Windows serveru ve vašem prostředí VMware.

**Data**  | **Třída WMI**  | **Vlastnost třídy WMI**
--- | --- | ---
Name  | Win32_operatingsystem  | Titulek
Verze  | Win32_operatingsystem  | Verze
Architektura  | Win32_operatingsystem  | OSArchitecture

#### <a name="linux-server-software-inventory-data"></a>Data inventáře softwaru pro Linux Server

Zde jsou data inventáře softwaru, která zařízení shromažďuje z každého serveru Linux zjištěného v prostředí VMware. V závislosti na operačním systému serveru se spustí jeden nebo více příkazů.

**Data**  | **Příkazy**
--- | ---
Name | ot./min., bázi dpkg – dotaz, přichycení
Verze | ot./min., bázi dpkg – dotaz, přichycení
Poskytovatel | ot./min., bázi dpkg – dotaz, přichycení

#### <a name="linux-server-operating-system-data"></a>Data operačního systému Linux Server

Zde jsou data operačního systému, která zařízení shromažďuje z každého serveru Linux zjištěného v prostředí VMware.

**Data**  | **Příkazy**
--- | ---
Name <br/> verze | Shromážděno z jednoho nebo více následujících souborů:<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oracle-release  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version
Architektura | uname

### <a name="sql-server-instances-and-databases-data"></a>Data SQL Server instance a databází

Zařízení shromažďuje data o SQL Server instancích a databázích.

> [!Note]
> Zjišťování a hodnocení instancí SQL Server a databází spuštěných ve vašem prostředí VMware je teď ve verzi Preview. Pokud chcete tuto funkci vyzkoušet, použijte [**tento odkaz**](https://aka.ms/AzureMigrate/SQL) a vytvořte projekt v oblasti **Austrálie – východ**. Pokud již máte projekt v oblasti Austrálie – východ a chcete tuto funkci vyzkoušet, na portálu se ujistěte, že jste splnili tyto [**požadavky**](how-to-discover-sql-existing-project.md).

#### <a name="sql-database-metadata"></a>Metadata SQL Database

**Metadata databáze** | **Vlastnosti zobrazení/SQL Server**
--- | ---
Jedinečný identifikátor databáze | sys.databases
ID databáze definované serverem | sys.databases
Název databáze | sys.databases
Úroveň kompatibility databáze | sys.databases
Název kolace databáze | sys.databases
Stav databáze | sys.databases
Velikost databáze (v MB) | sys.master_files
Písmeno jednotky umístění obsahujícího datové soubory | SERVERPROPERTY a Software\Microsoft\MSSQLServer\MSSQLServer
Seznam databázových souborů | sys. databases, sys.master_files
Služba Service Broker je povolená nebo ne. | sys.databases
Databáze je povolena pro Change Data Capture nebo ne. | sys.databases

#### <a name="sql-server-metadata"></a>SQL Server metadata

**Metadata serveru** | **Zobrazení/vlastnosti SQL serveru**
--- | ---
Název serveru |SERVERPROPERTY
FQDN | Připojovací řetězec odvozený ze zjišťování nainstalovaných aplikací
ID instalace | sys.dm_server_registry
Verze serveru | SERVERPROPERTY
Edice serveru | SERVERPROPERTY
Platforma hostitele serveru (Windows/Linux) | SERVERPROPERTY
Úroveň produktu serveru (RTM SP CTP) | SERVERPROPERTY
Výchozí cesta zálohování | SERVERPROPERTY
Výchozí cesta k datovým souborům | SERVERPROPERTY a Software\Microsoft\MSSQLServer\MSSQLServer
Výchozí cesta souborů protokolu | SERVERPROPERTY a Software\Microsoft\MSSQLServer\MSSQLServer
No. jader na serveru | sys.dm_os_schedulers sys.dm_os_sys_info
Název kolace serveru | SERVERPROPERTY
No. jader na serveru s VIDITELNÝm ONLINE stavem | sys.dm_os_schedulers
Jedinečné ID serveru | sys.dm_server_registry
HA povoleno nebo ne | SERVERPROPERTY
Rozšíření fondu vyrovnávací paměti je povolené nebo ne. | sys.dm_os_buffer_pool_extension_configuration
Cluster s podporou převzetí služeb při selhání nakonfigurovaný nebo ne | SERVERPROPERTY
Server jenom v režimu ověřování systému Windows | SERVERPROPERTY
Server nainstaluje základnu. | SERVERPROPERTY
No. logických procesorů v systému | sys.dm_server_registry sys.dm_os_sys_info
Poměr počtu logických nebo fyzických jader, které jsou vystaveny jedním balíčkem fyzického procesoru | sys.dm_os_schedulers sys.dm_os_sys_info
Žádný fyzický procesor v systému | sys.dm_os_schedulers sys.dm_os_sys_info
Datum a čas posledního spuštění serveru | sys.dm_server_registry
Maximální využití paměti serveru (v MB) | sys.dm_os_process_memory
Celková hodnota uživatelů napříč všemi databázemi | sys. databases, sys. Logins
Celková velikost všech uživatelských databází | sys.databases
Velikost dočasné databáze | sys.master_files sys.configurations sys.dm_os_sys_info
No. přihlášení | přihlášení sys.
Seznam propojených serverů | sys. Servers
Seznam úloh agenta | [msdb]. [dbo]. [tabulka sysjobs], [sys]. [syslogins], [msdb]. [dbo]. [syscategories]

### <a name="performance-metadata"></a>Metadata výkonu

**Výkon** | **Zobrazení/vlastnosti SQL serveru** | **Dopad posouzení**
--- | --- | ---
Využití CPU SQL Server| sys.dm_os_ring_buffers| Doporučená velikost SKU (dimenze CPU)
Počet logických PROCESORů SQL| sys.dm_os_sys_info| Doporučená velikost SKU (dimenze CPU)
Použitá fyzická paměť SQL| sys.dm_os_process_memory| Nepoužitý
Procento využití paměti SQL| sys.dm_os_process_memory | Nepoužitý
Využití CPU databáze| sys.dm_exec_query_stats sys.dm_exec_plan_attributes| Doporučená velikost SKU (dimenze CPU)
Využitá paměť databáze (fond vyrovnávacích pamětí)| sys.dm_os_buffer_descriptors| Doporučená velikost SKU (dimenze paměti)
Vstupně-výstupní operace čtení/zápisu| sys.dm_io_virtual_file_stats sys.master_files| Doporučená velikost SKU (dimenze v/v)
Počet souborů čtení a zápisů| sys.dm_io_virtual_file_stats sys.master_files| Doporučená velikost SKU (dimenze propustnosti)
Vstup/výstup do souboru/zápis (MS)| sys.dm_io_virtual_file_stats sys.master_files| Doporučená velikost SKU (dimenze latence v/v)
Velikost souboru| sys.master_files| Doporučená velikost SKU (dimenze úložiště)


### <a name="application-dependency-data"></a>Data závislostí aplikací

Analýza závislostí bez agentů shromažďuje připojení a zpracovává data.

#### <a name="windows-server-dependencies-data"></a>Data závislostí Windows serveru

Tady jsou data připojení, která zařízení shromažďuje z každého Windows serveru a která jsou povolená pro analýzu závislostí bez agentů.

**Data** | **Příkazy**
--- | ---
Místní port | netstat
Místní IP adresa | netstat
Vzdálený port | netstat
Vzdálená IP adresa | netstat
Stav připojení TCP | netstat
ID procesu | netstat
Počet aktivních připojení | netstat

Tady jsou data připojení, která zařízení shromažďuje z každého Windows serveru a která jsou povolená pro analýzu závislostí bez agentů.

**Data** | **Třída WMI** | **Vlastnost třídy WMI**
--- | --- | ---
Název procesu | Win32_Process | ExecutablePath
Argumenty procesu | Win32_Process | CommandLine
Název aplikace | Win32_Process | VersionInfo. ProductName – parametr vlastnosti ExecutablePath

#### <a name="linux-server-dependencies-data"></a>Data závislostí serveru Linux

Tady jsou data připojení, která zařízení shromažďuje z každého serveru Linux a která jsou povolená pro analýzu závislostí bez agentů.

**Data** | **Příkazy**
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

Zařízení shromažďuje metadata o konfiguraci a výkonu ze serverů, které běží v prostředí Hyper-V.

### <a name="metadata"></a>Metadata
Metadata zjištěná zařízením Azure Migrate vám pomůžou zjistit, jestli jsou servery připravené na migraci do Azure, servery pro pravou velikost a náklady na plánování. Společnost Microsoft nepoužívá tato data v žádném auditu dodržování předpisů v licencích.

Tady je úplný seznam metadat serveru, které zařízení shromažďuje a odesílá do Azure.

**Data** | **Třída WMI** | **Vlastnost třídy WMI**
--- | --- | ---
**Podrobnosti serveru** | 
Sériové číslo systému BIOS | Msvm_BIOSElement | BIOSSerialNumber
Typ serveru (obecná 1 nebo 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Zobrazovaný název serveru | Msvm_VirtualSystemSettingData | ElementName
Verze serveru | Msvm_ProcessorSettingData | VirtualQuantity
Paměť (bajty) | Msvm_MemorySettingData | VirtualQuantity
Maximální velikost paměti, kterou může server spotřebovat | Msvm_MemorySettingData | Omezení
Dynamická paměť je povolena | Msvm_MemorySettingData | DynamicMemoryEnabled
Název/verze operačního systému/plně kvalifikovaný název domény | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems data o názvech
Stav napájení serveru | Msvm_ComputerSystem | EnabledState
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

Tady je údaje o výkonu serveru, které zařízení shromažďuje a odesílá do Azure.

**Třída čítače výkonu** | **Čítač** | **Dopad posouzení**
--- | --- | ---
Virtuální procesor hypervisoru technologie Hyper-V | % Doby běhu hosta | Doporučená velikost serveru/náklady
Hyper-V Dynamická paměť Server | Aktuální tlak (%)<br/> Fyzická paměť viditelná pro hosta (MB) | Doporučená velikost serveru/náklady
Virtuální úložné zařízení Hyper-V | Přečtené bajty za sekundu | Výpočet pro velikost disku, náklady na úložiště, velikost serveru
Virtuální úložné zařízení Hyper-V | Bajty zápisu za sekundu | Výpočet pro velikost disku, náklady na úložiště, velikost serveru
Hyper-V Virtual Network adaptér | Přijaté bajty za sekundu | Výpočet pro velikost serveru
Hyper-V Virtual Network adaptér | Odeslané bajty za sekundu | Výpočet pro velikost serveru

- Využití CPU je součtem veškerého využití pro všechny virtuální procesory připojené k serveru.
- Využití paměti je (aktuální tlak × viditelnost fyzické paměti hosta)/100.
- Hodnoty využití disku a sítě se shromažďují ze seznamu čítačů výkonu technologie Hyper-V.

## <a name="collected-data---physical"></a>Shromážděná data – fyzické

Zařízení shromažďuje metadata o konfiguraci a výkonu z fyzických nebo virtuálních serverů, které jsou místně spuštěné.

### <a name="metadata"></a>Metadata

Metadata zjištěná zařízením Azure Migrate vám pomůžou zjistit, jestli jsou servery připravené na migraci do Azure, servery pro pravou velikost a náklady na plánování. Společnost Microsoft nepoužívá tato data v žádném auditu dodržování předpisů v licencích.

### <a name="windows-server-metadata"></a>Metadata Windows serveru

Tady je úplný seznam metadat Windows serveru, které zařízení shromažďuje a odesílá do Azure.

**Data** | **Třída WMI** | **Vlastnost třídy WMI**
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

### <a name="linux-server-metadata"></a>Metadata serveru Linux

Tady je úplný seznam metadat serveru Linux, které zařízení shromažďuje a odesílá do Azure.

**Data** | **Příkazy**
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

| **Data** | **Příkazy** |
| --- | --- |
| Využití procesoru | Cat/proc/stat/ \| grep "CPU"/proc/stat |
| Využití paměti | bezplatný \| grep mem \| awk mají ' {Print $3/$ 2 * 100,0} ' |
| Počet síťových adaptérů | lshw – třída \| grep ETH [0-60] \| WC-l |
| Přijatá data na síťový adaptér | Cat/sys/Class/NET/ETH $ nic/Statistika/rx_bytes |
| Data přenášená na síťový adaptér | Cat/sys/Class/NET/ETH $ nic/Statistika/tx_bytes |
| Počet disků | fdisk-l \| egrep ' disk. \* bajty ' \| awk mají ' {print $2} ' \| Vyjmout-F1-d ': ' |
| Podrobnosti o disku | /Proc/diskstats Cat |

## <a name="appliance-upgrades"></a>Upgrady zařízení

Zařízení se upgraduje, protože Azure Migrate služby spuštěné na zařízení se aktualizují. K tomu dojde automaticky, protože ve výchozím nastavení je na zařízení povolená Automatická aktualizace. Toto výchozí nastavení můžete změnit tak, aby se služby zařízení aktualizovaly ručně.

### <a name="turn-off-auto-update"></a>Vypnout automatické aktualizace

1. Na serveru, na kterém je spuštěno zařízení, otevřete Editor registru.
2. Přejděte na **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Pokud chcete vypnout automatické aktualizace, vytvořte klíč registru **AutoUpdate** s hodnotou DWORD 0.

    ![Nastavení klíče registru](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>Zapnout automatické aktualizace

Automatické aktualizace můžete zapnout pomocí některé z těchto metod:

- Odstraněním klíče registru AutoUpdate z HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance.
- Pokud chcete zapnout automatické aktualizace, klikněte na **Zobrazit služby zařízení** z nejnovějších kontrol aktualizací na panelu **nastavit předpoklady** .

Postup odstranění klíče registru:

1. Na serveru, na kterém je spuštěno zařízení, otevřete Editor registru.
2. Přejděte na **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Odstraňte klíč registru **AutoUpdate**, který byl dříve vytvořen pro vypnutí automatických aktualizací.

Zapnutí z Configuration Manager zařízení po dokončení zjišťování:

1. V nástroji Správce konfigurace zařízení, přejít na panel **nastavení požadavků**
2. V části nejnovější aktualizace klikněte na **Zobrazit služby zařízení** a kliknutím na odkaz zapněte automatické aktualizace.

    ![Zapnout automatické aktualizace](./media/migrate-appliance/autoupdate-off.png)

### <a name="check-the-appliance-services-version"></a>Ověřit verzi služby zařízení

Verzi služby zařízení můžete ověřit pomocí některé z těchto metod:

- V nástroji Configuration Manager pro zařízení klikněte na panel **nastavit požadované součásti** .
- Na zařízení v **Ovládacích panelech**  >  **programy a funkce**.

Vrácení se změnami do Správce konfigurace zařízení:

1. V nástroji Správce konfigurace zařízení, přejít na panel **nastavení požadavků**
2. V části nejnovější aktualizace klikněte na **Zobrazit služby zařízení**.

    ![Ověřit verzi](./media/migrate-appliance/versions.png)

Chcete-li se vrátit do ovládacích panelů, postupujte takto:

1. Na zařízení klikněte na **Start**  >  **Control Panel**  >  **programy a funkce** .
2. Podívejte se na verze služeb zařízení v seznamu.

    ![Kontrola verze v Ovládacích panelech](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>Ruční aktualizace starší verze

Pokud používáte starší verzi pro některou ze služeb, musíte službu odinstalovat a ručně aktualizovat na nejnovější verzi.

1. Chcete-li vyhledat nejnovější verze služby zařízení, [stáhněte](https://aka.ms/latestapplianceservices) LatestComponents.jsv souboru.
2. Po stažení otevřete LatestComponents.jsv souboru poznámkového bloku.
3. Vyhledejte nejnovější verzi služby v souboru a odkaz pro stažení. Například:

    "Name": "ASRMigrationWebApp"; "DownloadLink": " https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi ", "Version": "6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a81e75469b84"

4. Stáhněte si nejnovější verzi zastaralé služby pomocí odkazu ke stažení v souboru.
5. Po stažení spusťte následující příkaz v okně příkazového řádku správce, abyste ověřili integritu staženého souboru MSI.

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ``` Příklad: C: \> certutil-HashFile C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. Ověřte, že výstup příkazu odpovídá položce hodnoty hash pro službu v souboru (například hodnota hash MD5 výše).
6. Nyní spusťte instalační službu MSI a nainstalujte ji. Je to tichá instalace a po dokončení se okno instalace zavře.
7. Po dokončení instalace ověřte verzi služby v části   >  **programy a funkce** v Ovládacích panelech. Verze služby by teď měla být upgradována na nejnovější verzi uvedenou v souboru JSON.

## <a name="next-steps"></a>Další kroky

- [Přečtěte si, jak](how-to-set-up-appliance-vmware.md) nastavit zařízení pro VMware.
- [Přečtěte si, jak](how-to-set-up-appliance-hyper-v.md) nastavit zařízení pro Hyper-V.
- [Přečtěte si, jak](how-to-set-up-appliance-physical.md) nastavit zařízení pro fyzické servery.
