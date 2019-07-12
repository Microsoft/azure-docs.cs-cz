---
title: Azure Migrate systém podpory replikace z VMware vyhodnocení a migrace
description: Obsahuje souhrn nastavení podpory a omezení pro vyhodnocení a migraci virtuálních počítačů VMware do Azure pomocí služby Azure Migrate.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: raynew
ms.openlocfilehash: 567a6582e193208a7ff4aa37bafefe1dec4f4e8f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811580"
---
# <a name="support-matrix-for-vmware-assessment-and-migration"></a>Systém podpory replikace z VMware vyhodnocení a migrace

Můžete použít [služby Azure Migrate](migrate-overview.md) vyhodnocovat a migrovat počítače do cloudu Microsoft Azure. Tento článek shrnuje podporu nastavení a omezení pro vyhodnocení a migrace místních virtuálních počítačů VMware.


## <a name="vmware-scenarios"></a>Scénáře VMware

Tabulka shrnuje Podporované scénáře pro virtuální počítače VMware.

**Nasazení** | **Podrobnosti** 
--- | --- 
**Posouzení místních virtuálních počítačů VMware** | [Nastavit](tutorial-prepare-vmware.md) první posouzení.<br/><br/> [Spustit](scale-vmware-assessment.md) ve velkém měřítku posouzení.
**Migrace virtuálních počítačů VMware** | Můžete migrovat pomocí migrace bez agenta, s určitými omezeními nebo použít při migraci založené na agentovi. [Víc se uč](server-migrate-overview.md)


    


## <a name="azure-migrate-projects"></a>Projekty Azure Migrate

**Podpora** | **Podrobnosti**
--- | ---
Oprávnění Azure | Potřebujete oprávnění přispěvatele nebo vlastníka v rámci předplatného pro vytvoření projektu Azure Migrate.
Omezení VMware  | Posouzení až 35 000 virtuálních počítačů VMware v jednom projektu.

Projekt může obsahovat virtuální počítače VMware a virtuálních počítačů Hyper-V, až do omezení hodnocení.

## <a name="assessment-vmware-server-requirements"></a>Požadavků na servery VMware posouzení

Tato tabulka shrnuje podporu posouzení a omezení pro virtualizaci serverů VMware.

**Podpora** | **Podrobnosti**
--- | ---
**Server vCenter** | Virtuální počítače VMware, které chcete posoudit se musí spravovat přes 5.5, 6.0, 6.5 nebo 6.7 jeden nebo více serverů vCenter.

## <a name="assessment-vcenter-server-permissions"></a>Oprávnění pro Server vCenter posouzení

Pro účely posouzení pouze potřebujete účet jen pro čtení pro vCenter Server.

## <a name="assessment-appliance-requirements"></a>Požadavky na vyhodnocení zařízení

**Podpora** | **Podrobnosti**
--- | ---
**ESXi** | Zařízení virtuálního počítače musí být nasazený na hostiteli ESXi 5.5 nebo novější verzí.
**Projekt Azure Migrate** | Zařízení můžou být spojené s jednoho projektu.
**vCenter Server** | Zařízení můžete zjistit, až 10 000 virtuálních počítačů VMware na serveru vCenter Server.<br/> Zařízení může připojit k jednomu vCenter serveru.


## <a name="assessment-url-access-requirements"></a>Adresa URL hodnocení požadavků na přístup

Zařízení Azure Migrate vyžaduje připojení k Internetu na Internetu.

- Při nasazování na zařízení Azure Migrate provede kontrolu připojení k adresám URL shrnuté v následující tabulce.
- Pokud používáte firewall.proxy založené na adrese URL, povolte přístup k těmto adresám URL, ujistěte se, že proxy server odstraňuje všechny záznamy CNAME přijaté při hledání adresy URL.

**Adresa URL** | **Podrobnosti**  
--- | --- |
*.portal.azure.com  | Přejděte do Azure migrovat na webu Azure Portal.
*.windows.net | Přihlaste se ke svému předplatnému Azure.
*.microsoftonline.com | Vytvoření aplikace Active Directory pro zařízení komunikovat se službou Azure Migrate.
management.azure.com | Vytvoření aplikace Active Directory pro zařízení komunikovat se službou Azure Migrate.
dc.services.visualstudio.com | Nahrajte protokoly aplikace používá pro interní monitorování.
*.vault.azure.net | Správa tajných klíčů ve službě Azure Key Vault.
*.servicebus.windows.net | Komunikace mezi zařízení a služby Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Připojení k adresám URL služby Azure Migrate.
*.blob.core.windows.net | Nahrání dat do účtů úložiště.


## <a name="assessment-port-requirements"></a>Požadavky na porty posouzení

**zařízení** | **připojení**
--- | --- 
Zařízení | Příchozí připojení na portu TCP 3389 umožňující připojení ke vzdálené ploše do tohoto zařízení.<br/> Příchozí připojení na portu 44368 vzdálený přístup k aplikaci pro správu zařízení pomocí adresy URL: https://<appliance-ip-or-name>:44368 <br/>Odchozí připojení na portu 443 odeslat výkonu a zjišťování metadat pro Azure Migrate.
vCenter server | Příchozí připojení na portu TCP 443, aby zařízení se získat metadata konfigurace a výkonu pro posouzení. <br/> Zařízení se připojí k serveru vCenter na portu 443 ve výchozím nastavení. Pokud vCenter server naslouchá na jiném portu, můžete upravit port při nastavování zjišťování.


## <a name="agentless-migration-vmware-server-requirements"></a>Požadavky na server bez agentů migrace VMware

Tato tabulka shrnuje podporu posouzení a omezení pro virtualizaci serverů VMware.

**Podpora** | **Podrobnosti**
--- | ---
**Server vCenter** | Virtuální počítače VMware, které že migrujete, pomocí funkce agentless migrace se musí spravovat přes 5.5, 6.0, 6.5 nebo 6.7 jeden nebo více serverů vCenter.

## <a name="agentless-migration-vcenter-server-permissions"></a>Oprávnění v prostředí bez agentů migrace vCenter Server

**Oprávnění** | **Podrobnosti**
--- | --- 
Datastore.Browse | Povolí prohlížení protokolových souborů virtuálního počítače k řešení potíží s vytvoření snímku a odstranění.
Datastore.LowLevelFileOperations | Povolit operace čtení/zápis/odstranit nebo přejmenovat v prohlížeči úložiště dat, řešení potíží s vytvoření snímku a odstranění.
VirtualMachine.Configuration.DiskChangeTracking | Umožní povolit nebo zakázat řešení change tracking disků virtuálních počítačů, a o přijetí změn změněné bloky dat mezi snímky
VirtualMachine.Configuration.DiskLease | Povolit operace zapůjčení disku pro virtuální počítač, se číst z disku pomocí klientské konzole VMware vSphere virtuální Disk Development Kit (VDDK).
VirtualMachine.Provisioning.AllowReadOnlyDiskAccess | Povolit otevření disk na virtuálním počítači, se číst z disku VDDK pomocí.
VirtualMachine.Provisioning.AllowVirtualMachineDownload  | Umožňuje operace čtení na soubory přidružené k virtuálnímu počítači, ke stažení protokolů a řešení potíží, pokud dojde k selhání. 
VirtualMachine.SnapshotManagement.* | Povolte vytváření a správa snímky virtuálních počítačů pro replikaci. 
Virtuální Machine.Interaction.Power vypnuto | Povolte virtuální počítač vypnout napájení během migrace do Azure.


## <a name="agentless-migration-vmware-vm-requirements"></a>Požadavky na virtuální počítač bez agentů migrace VMware

**Podpora** | **Podrobnosti**
--- | ---
**Podporované operační systémy** | [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) a [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) operační systémy, které se nepodporuje v Azure je možné migrovat pomocí migrace bez agenta.
**Požadované změny pro Azure** | Některé virtuální počítače můžou vyžadovat změny, aby mohla spustit v Azure. Azure Migrate provede tyto změny automaticky pro následující operační systémy:<br/> – Red Hat Enterprise Linux verze 6.5 + 7.0 +<br/> -CentOS 6.5 + 7.0 +</br> -Operačním systémem SUSE Linux Enterprise Server 12 SP1 +<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> -Debian 7,8<br/><br/> Pro další operační systémy budete muset provést úpravy ručně před migrací. Související články obsahují pokyny ohledně toho, jak to udělat.
**Spouštění systému Linux** | Je-li Boot na vyhrazené oddílů, by měl být uložený na disk s operačním systémem a nesmí být rozděleny mezi několik disků.<br/> Pokud Boot v rámci oddílu kořenový adresář (/), pak oddílu '/' by měl být na disku s operačním systémem a ostatní disky nejsou span.
**Spouštění UEFI** | Virtuální počítače se spouštěním UEFI se pro migraci podporováno.
**Disky a svazky šifrované** | Virtuální počítače s disky nebo svazky šifrované nejsou pro migraci podporováno.
**RDM/průchozí disky** | Pokud virtuální počítače mají RDM nebo průchozí disky, nebude možné tyto disky replikovat do Azure.
**NFS** | Připojit jako svazky na virtuálních počítačích svazky systému souborů NFS, se replikovat nebudou.
**Cílový disk** | Virtuální počítače je možné migrovat pouze na spravované disky (HHD standard, premium SSD) v Azure.


## <a name="agentless-migration-appliance-requirements"></a>Požadavky agenta migrace zařízení


**Podpora** | **Podrobnosti**
--- | ---
**ESXi** | Zařízení virtuálního počítače musí být nasazený na hostiteli ESXi 5.5 nebo novější verzí.
**Projekt Azure Migrate** | Zařízení můžou být spojené s jednoho projektu.
**vCenter Server** | Zařízení můžete zjistit, až 10 000 virtuálních počítačů VMware na serveru vCenter Server.<br/> Zařízení může připojit k jednomu vCenter serveru.
**VDDK** | Pokud používáte bez agentů migrace s Azure Migrate serveru migrace, klientské konzole VMware vSphere virtuální Disk Development Kit (VDDK) musí být nainstalovaný na zařízení virtuálního počítače.

## <a name="agentless-migration-url-access-requirements"></a>Požadavky na přístup bez agentů-adresu URL pro migraci

Zařízení Azure Migrate vyžaduje připojení k Internetu na Internetu.

- Při nasazování na zařízení Azure Migrate provede kontrolu připojení k adresám URL shrnuté v následující tabulce.
- Pokud používáte firewall.proxy založené na adrese URL, povolte přístup k těmto adresám URL, ujistěte se, že proxy server odstraňuje všechny záznamy CNAME přijaté při hledání adresy URL.

**Adresa URL** | **Podrobnosti**  
--- | --- 
*.portal.azure.com | Přejděte do Azure migrovat na webu Azure Portal.
*.windows.net | Přihlaste se ke svému předplatnému Azure.
*.microsoftonline.com | Vytvoření aplikace Active Directory pro zařízení komunikovat se službou Azure Migrate.
management.azure.com | Vytvoření aplikace Active Directory pro zařízení komunikovat se službou Azure Migrate.
dc.services.visualstudio.com | Nahrajte protokoly aplikace používá pro interní monitorování.
*.vault.azure.net | Správa tajných klíčů ve službě Azure Key Vault.
*.servicebus.windows.net | Komunikace mezi zařízení a služby Azure Migrate.
*.discoverysrv.windowsazure.com<br/> *.migration.windowsazure.com<br/> *.hypervrecoverymanager.windowsazure.com | Připojení k adresám URL služby Azure Migrate.
*.blob.core.windows.net | Nahrání dat do účtů úložiště.


## <a name="agentless-migration-port-requirements"></a>Požadavky na funkce agentless migrace portu

**zařízení** | **připojení**
--- | --- 
Zařízení | Odchozí port TCP 3389 k odesílání replikovaných dat do Azure a ke komunikaci s Azure Migrate pro replikaci a migrace.
vCenter server | Příchozí připojení na portu TCP 443, aby zařízení k orchestraci replikace – vytvořit snímky, kopírování dat, uvolněte snímky
hostitele vSphere/ESXI | Příchozí na portu TCP 902 pro zařízení k replikaci dat ze snímků. 


## <a name="agent-based-migration-vmware-server-requirements"></a>Požadavků na servery založené na agentovi migrace VMware

Tato tabulka shrnuje podporu posouzení a omezení pro virtualizaci serverů VMware.

**Podpora** | **Podrobnosti**
--- | ---
**server vCenter/ESXI** | Virtuální počítače VMware, které migrujete se musí spravovat přes jeden nebo více serverů vCenter běží 5.5, 6.0, 6.5 nebo 6.7 nebo hostiteli ESXI verze vSphere 5.5, 6.0, 6.7 nebo 6.5.

### <a name="agent-based-migration-vcenter-server-permissions"></a>Oprávnění v prostředí založené na agentovi migrace vCenter Server

**Oprávnění** | **Podrobnosti**
--- | --- 
Datastore.AllocateSpace | Povolit přidělování místa v úložišti dat přístupném pro virtuální počítač, snímku, klonování nebo virtuální disk. 
Datastore.Browse | Povolí prohlížení protokolových souborů virtuálního počítače k řešení potíží s vytvoření snímku a odstranění.
Datastore.LowLevelFileOperations | Povolit čtení, zápis, odstraňují a přejmenovávají operací v prohlížeči úložiště dat snímku vytváření/odstraňování potíží.
Datastore.UpdateVirtualMachineFiles | Povolit aktualizaci cesty k souborům virtuálního počítače na úložiště dat po resignatured úložiště.
Network.AssignNetwork | Povolte síť přiřazení k prostředku virtuálního počítače.
AssignVirtualMachineToResourcePool | Povolit přiřazení virtuálního počítače do fondu zdrojů.
Resource.MigratePoweredOffVirtualMachine | Povolit migraci vypnutý virtuální počítač do jiného fondu zdrojů nebo hostitele.
Resource.MigratePoweredOnVirtualMachine | Povolit migraci pomocí řešení vMotion provozu virtuálního počítače do jiného fondu zdrojů nebo hostitele.
Tasks.CreateTask | Povolit rozšíření vytvořit uživatelský úkol.
Tasks.UpdateTask | Povolit rozšíření aktualizovat uživatelský úkol.
VirtualMachine.Configuration. | Povolte konfiguraci možností virtuálního počítače a zařízení.
Virtual Machine.Interaction.AnswerQuestion | Povolit řešení problémů s přechodů mezi stavy virtuálního počítače nebo chyby za běhu.
Virtual Machine.Interaction.DeviceConnection | Povolit změnu připojených stavu Virtuálního počítače odpojitelné virtuální zařízení.
Virtual Machine.Interaction.ConfigureCDMedia | Povolit konfiguraci virtuální zařízení DVD nebo disk CD-ROM.
Virtual Machine.Interaction.ConfigureFloppyMedia | Povolit konfiguraci virtuální disketová zařízení.
Virtuální Machine.Interaction.PowerOff | Umožňuje pro virtuální počítač vypnout napájení během migrace do Azure.
Virtual Machine.Interaction.PowerOn | Povolte zapnutím virtuálního počítače odpojí a obnovení pozastaveného virtuálního počítače.
Virtual Machine.Interaction.VMwareToolsInstall | Povolte připojení a odpojení instalační disk CD Nástroje VMware jako CD-ROM pro hostovaný operační systém.
VirtualMachine.Inventory.CreateNew | Povolte vytváření virtuálního počítače a přidělení požadované prostředky.
VirtualMachine.Inventory.Register | Povolit přidání existujícího virtuálního počítače k serveru vCenter nebo hostiteli inventáře.
VirtualMachine.Inventory.Unregister | Povolte zrušení registrace VMe ze serveru vCenter nebo hostiteli inventáře.
VirtualMachine.Provisioning.AllowVirtualMachineFilesUpload | Povolit soubory přidružené k virtuálnímu počítači, včetně vmx, disky, protokoly a nvram operace zápisu.
VirtualMachine.Provisioning.AllowVirtualMachineDownload | Povolit operace čtení na soubory přidružené k virtuálnímu počítači, ke stažení protokolů pro řešení potíží.
VirtualMachine.SnapshotManagement.RemoveSnapshot | Povolit odstranění snímku ze snímků historie.

## <a name="agent-based-migration-replication-appliance-requirements"></a>Požadavky na zařízení migrace replikace založené na agentovi

Požadavky [replikace zařízení](migrate-replication-appliance.md) používá pro agenty migraci virtuálních počítačů VMware a fyzické servery s Azure Migrate migrace serveru je shrnuto v tabulce.

> [!NOTE]
> Při nastavování zařízení replikace pomocí šablony OVA k dispozici v centru Azure Migrate na zařízení běží systém Windows Server 2016 a v souladu s požadavky na podporu. Pokud jste nastavili replikaci zařízení ručně na fyzickém serveru, zkontrolujte, zda je v souladu s požadavky.



**Komponenta** | **Požadavek** 
--- | ---
 | **Nastavení VMware** (zařízení virtuální počítač VMware)
**PowerCLI** | [Nástroj PowerCLI verze 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) by měly být nainstalovány, pokud replikace zařízení běží na virtuálním počítači VMware.
**Typ NIC** | VMXNET3 (Pokud je toto zařízení má virtuální počítač VMware)
 | **Nastavení hardwaru** 
Procesorová jádra | 8 
Paměť RAM | 16 GB
Počet disků | Tři: Operační systém disku, disk mezipaměti procesového serveru a jednotka pro uchování.
Volné místo na disku (mezipaměť) | 600 GB
Volné místo na disku (disk pro uchování) | 600 GB
**Nastavení softwaru** | 
Operační systém | Windows Server 2016 nebo Windows Server 2012 R2
Národní prostředí operačního systému | Angličtina (en-us)
TLS | Povolit protokol TLS 1.2.
.NET Framework | Rozhraní .NET framework 4.6 nebo novější musí být nainstalován na tento počítač (pomocí silného šifrování povolené.
MySQL | MySQL je třeba nainstalovat na zařízení.<br/> Je třeba nainstalovat MySQL. Můžete nainstalovat ručně, nebo Site Recovery můžete nainstalovat během nasazování zařízení. 
Ostatní aplikace | Ostatní aplikace byste neměli spouštět na zařízení replikace.
Role Windows Serveru | Nepovolí pracovníci v těchto rolích: <br> – Active Directory Domain Services <br>– Internet Information Service <br> – Hyper-V 
Zásady skupiny | Nepovolí tyto zásady skupiny: <br> -Zabránit přístupu do příkazového řádku. <br> -Zabránit přístupu k nástrojům pro úpravu registru. <br> – Logika důvěryhodnosti pro přiložené soubory. <br> -Zapnutí provádění skriptů. <br> [Víc se uč](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Žádné existující výchozí web <br> -Žádné stávající web/aplikace naslouchá na portu 443 <br>-Aktivovat [anonymní ověřování](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Aktivovat [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) nastavení 
**Nastavení sítě** | 
Typ IP adresy | Static 
Porty | 443 (orchestrace řídicího kanálu)<br>9443 (přenos dat) 
Typ NIC | VMXNET3 

### <a name="replication-appliance-url-access"></a>Přístup k adrese URL replikace zařízení

Replikace zařízení potřebuje přístup k těmto adresám URL.

**Adresa URL** | **Podrobnosti**
--- | ---
\*.backup.windowsazure.com | Používá pro koordinaci a přenosu replikovaných dat
\*.store.core.windows.net | Používá pro koordinaci a přenosu replikovaných dat
\*.blob.core.windows.net | Používá pro přístup k účtu úložiště, který ukládá replikovaná data
\*.hypervrecoverymanager.windowsazure.com | Používá pro koordinaci a operace správy replikací.
https:\//management.azure.com | Používá pro koordinaci a operace správy replikací. 
*.services.visualstudio.com | Používá pro účely telemetrie (je volitelný)
time.nist.gov | Používá se ke kontrole synchronizace mezi systémovým a globálním časem.
time.windows.com | Používá se ke kontrole synchronizace mezi systémovým a globálním časem.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | Instalační program OVF potřebuje přístup k těmto adresám URL. Používají se pro přístup k řízení a správě identit Azure Active Directory
protokol https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | K dokončení stahování MySQL


#### <a name="mysql-installation-options"></a>Možnosti instalace MySQL

MySQL můžete nainstalovat na zařízení replikace pomocí jedné z těchto metod.

**Instalace** | **Podrobnosti**
--- | ---
Stáhnout a nainstalovat ručně | Stažení aplikace MySQL a umístěte do složky C:\Temp\ASRSetup a potom nainstalovat ručně.<br/> Při nastavování zařízení se zobrazí MySQL, které jsou už nainstalované. 
Nestahovat online | Umístěte do složky C:\Temp\ASRSetup instalační aplikace sady MySQL. Když nainstalujete na zařízení a klikněte na tlačítko pro stažení a instalaci MySQL, instalační program použije instalační program, který jste přidali. 
Stažení Azure Migrate | Při instalaci zařízení a zobrazí se výzva k zadání MySQL, vyberte **stáhnout a nainstalovat**.



## <a name="agent-based-migration-vmware-vm-requirements"></a>Požadavky virtuálního počítače založené na agentovi migrace VMware

**Podpora** | **Podrobnosti**
--- | ---
**Úlohám počítače** | Azure Migrate podporuje migrace pro každou úlohu (třeba Active Directory, SQL server atd.) běží na podporovaném počítači.
**Operační systémy** | Nejnovější informace najdete v článku [podporovaných operačních systémech](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) pro Site Recovery. Azure Migrate nabízí stejné podporu operačního systému virtuálního počítače.
**Úložiště systému/hostů soubor Linuxu** | Nejnovější informace najdete v článku [podporu systému Linux souboru](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) pro Site Recovery. Azure Migrate má stejné podpora systému souborů v Linuxu.
**Network/Storage** | Nejnovější informace najdete v článku [sítě](../site-recovery/vmware-physical-azure-support-matrix.md#network) a [úložiště](../site-recovery/vmware-physical-azure-support-matrix.md#storage) předpoklady pro Site Recovery. Azure Migrate obsahuje požadavky na stejné sítě nebo úložiště.
**Požadavky na Azure** | Nejnovější informace najdete v článku [síť Azure](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [úložiště](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage), a [compute](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) požadavky pro Site Recovery. Azure Migrate má stejné požadavky na migraci VMware.
**Služba Mobility** | Agent služby Mobility musíte nainstalovat na jednotlivých virtuálních počítačích, které chcete migrovat.
**Cílový disk** | Virtuální počítače je možné migrovat pouze na spravované disky (HHD standard, premium SSD) v Azure.

   

## <a name="agent-based-migration-url-access-requirements"></a>Požadavky na přístup na základě agenta-adresu URL pro migraci

Služby Mobility spuštěné na virtuálních počítačích VMware vyžaduje připojení k Internetu na Internetu.

- Při nasazení služby Mobility, provede kontrola připojení k adresám URL shrnuté v následující tabulce.
- Pokud používáte firewall.proxy založené na adrese URL, povolte přístup k těmto adresám URL, ujistěte se, že proxy server odstraňuje všechny záznamy CNAME přijaté při hledání adresy URL.

**Adresa URL** | **Podrobnosti**  
--- | --- 
*.portal.azure.com | Přejděte do Azure migrovat na webu Azure Portal.
*.windows.net | Přihlaste se ke svému předplatnému Azure.
*.microsoftonline.com | Vytvoření aplikace Active Directory pro zařízení komunikovat se službou Azure Migrate. 
management.azure.com | Vytvoření aplikace Active Directory pro zařízení komunikovat se službou Azure Migrate.
dc.services.visualstudio.com | Nahrajte protokoly aplikace používá pro interní monitorování.
*.vault.azure.net | Správa tajných klíčů ve službě Azure Key Vault.
*.servicebus.windows.net | Komunikace mezi zařízení a služby Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Připojení k adresám URL služby Azure Migrate.
*.blob.core.windows.net | Nahrání dat do účtů úložiště.

## <a name="agent-based-migration-port-requirements"></a>Požadavky na port migrace založené na agentovi

**zařízení** | **připojení**
--- | --- 
Virtuální počítače | Služby Mobility spuštěné na virtuálních počítačích komunikuje s místní konfigurační server na portu HTTPS 443 příchozí kvůli správě replikace.<br/><br/> Příchozí data replikace k procesového serveru (běžícího na počítači serveru configuration) na příchozím portu HTTPS 9443 poslat virtuálních počítačů. Tento port je možné upravit.
Replikace zařízení | Zařízení replikace orchestruje replikaci pomocí Azure přes odchozí port HTTPS 443.
Procesový server | Procesový server přijímá data replikace, optimalizuje je zašifruje a odesílá je do úložiště Azure přes port 443 odchozí.<br/> Ve výchozím nastavení procesový server běží na zařízení replikace.

## <a name="azure-vm-requirements"></a>Požadavky virtuálních počítačů Azure

Všechny místní virtuální počítače replikované do Azure musí splňovat požadavky na virtuální počítač Azure uvedené v této tabulce. Kontrola požadavků pro replikaci spuštění Site Recovery je kontrola selže, pokud nejsou splněny některé požadavky.

**Komponenta** | **Požadavky** | **Podrobnosti**
--- | --- | ---
Hostovaný operační systém | Ověřte podporované operační systémy pro [virtuálních počítačů VMware pomocí funkce agentless replikace](#agentless-migration-vmware-vm-requirements)a pro [virtuálních počítačů VMware pomocí replikace založené na agentovi](#agent-based-migration-vmware-vm-requirements).<br/> Můžete migrovat jakoukoli úlohu spuštěnou na podporovaném operačním systému. | Kontrola selže, pokud není podporován.
Architektura operačního systému hosta | 64-bit. | Kontrola selže, pokud není podporován.
Velikost disku operačního systému | Až 2 048 GB. | Kontrola selže, pokud není podporován.
Počet disků operačního systému | 1 | Kontrola selže, pokud není podporován.
Počet datových disků | 64 nebo méně. | Kontrola selže, pokud není podporován.
Velikost datového disku | Až 4 095 GB | Kontrola selže, pokud není podporován.
Síťové adaptéry | Podporuje se více adaptérů. | 
Sdílený virtuální pevný disk | Nepodporuje se. | Kontrola selže, pokud není podporován.
FC disk | Nepodporuje se. | Kontrola selže, pokud není podporován.
BitLocker | Nepodporuje se. | Dříve než povolíte replikaci pro počítač, musí se zakázat nástroj BitLocker. 
název virtuálního počítače | Od 1 do 63 znaků.<br/> Pouze písmena, číslice a pomlčky.<br/><br/> Název počítače musí začínat a končit písmenem nebo číslicí. |  Aktualizujte hodnotu v vlastnosti počítače ve službě Site Recovery.
Připojit po migraci Windows | Připojení k virtuálním počítačům Azure s Windows po migraci:<br/> -Před migrací povolte protokol RDP na virtuálních počítačích na místě. Ujistěte se, že jsou přidaná pravidla TCP a UDP pro **Veřejný** profil a že v části **Brána Windows Firewall** > **Povolené aplikace** je pro všechny profily povolený protokol RDP.<br/> Pro přístup k síti VPN site-to-site, povolte protokol RDP a povolit RDP v **brány Windows Firewall** -> **povolené aplikace a funkce** pro **doménovou a privátní** sítě. Dále zkontrolujte, že je zásada SAN operačního systému nastavená na **OnlineAll**. [Další informace](https://support.microsoft.com/kb/3031135). | 
Připojit po migraci – Linux | Připojení k virtuálním počítačům Azure po migraci pomocí protokolu SSH:<br/> Před migrací na místním počítači zkontrolujte, že služba Secure Shell je nastavena na začátku a že pravidla brány firewall umožňují připojení SSH.<br/> Na virtuálním počítači Azure po převzetí služeb při selhání, povolte příchozí připojení k portu SSH pro pravidla skupiny zabezpečení sítě na virtuální počítač převzal a v podsíti Azure, ke kterému je připojený. Kromě toho přidejte veřejnou IP adresu pro virtuální počítač. |  


## <a name="next-steps"></a>Další kroky

[Příprava VMware](tutorial-prepare-vmware.md) vyhodnocení a migraci.








