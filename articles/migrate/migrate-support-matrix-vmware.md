---
title: Podpora pro vyhodnocení a migraci VMware v Azure Migrate
description: Přečtěte si o podpoře vyhodnocení/migrace virtuálních počítačů VMware v Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 135680a9b0b6c8b5520958c884d99a83f1f87c88
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196275"
---
# <a name="support-matrix-for-vmware-assessment-and-migration"></a>Matice podpory pro vyhodnocení a migraci VMware

[Azure Migrate](migrate-overview.md) můžete použít k vyhodnocení a migraci počítačů do Microsoft Azure cloudu. Tento článek shrnuje nastavení podpory a omezení pro vyhodnocení a migraci místních virtuálních počítačů VMware.


## <a name="vmware-scenarios"></a>Scénáře VMware

Tabulka shrnuje podporované scénáře pro virtuální počítače VMware.

**Nasazení** | **Podrobnosti**
--- | ---
**Posouzení místních virtuálních počítačů VMware** | [Nastavte](tutorial-prepare-vmware.md) své první posouzení.<br/><br/> [Spusťte](scale-vmware-assessment.md) hodnocení ve velkém měřítku.
**Migrace virtuálních počítačů VMware** | Můžete migrovat pomocí migrace bez agenta nebo pomocí migrace založené na agentech. [Další informace](server-migrate-overview.md)


## <a name="azure-migrate-projects"></a>Azure Migrate projekty

**Podpora** | **Podrobnosti**
--- | ---
**Oprávnění Azure** | Chcete-li vytvořit projekt Azure Migrate, potřebujete oprávnění přispěvatele nebo vlastníka v rámci předplatného.
**Omezení VMware**  | Vyhodnoťte až 35 000 virtuálních počítačů VMware v jednom projektu. V předplatném Azure můžete vytvořit více projektů. Projekt může zahrnovat virtuální počítače VMware i virtuální počítače Hyper-V, a to až do limitů hodnocení.
**Zeměpisné oblasti** | [Kontrola](migrate-support-matrix.md#supported-geographies) podporovaných geografických oblastí.

**Zeměpisné oblasti** | **Umístění úložiště metadat**
--- | ---
Azure Government | USA (Gov) – Virginia
Asie a Tichomoří | Východní Asie nebo jihovýchodní Asie
Austrálie | Austrálie – východ nebo Austrálie – jihovýchod
Brazílie | Brazílie – jih
Kanada | Kanada – střed nebo Kanada – východ
Evropa | Severní Evropa nebo Západní Evropa
Francie | Francie – střed
Indie | Střed Indie nebo Jižní Indie
Japonsko |  Japonsko – východ nebo Japonsko – západ
Jižní Korea | Korea – střed nebo Korea – jih
Spojené království | Velká Británie – jih nebo Velká Británie – západ
Spojené státy | Střed USA nebo Západní USA 2


 > [!NOTE]
 > Podpora Azure Government je v tuto chvíli dostupná jenom pro [starší verze](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) Azure Migrate.


## <a name="application-discovery"></a>Zjišťování aplikací

Azure Migrate: posouzení serveru může zjišťovat aplikace, role a funkce. Zjišťování inventáře aplikací vám umožní identifikovat a naplánovat cestu migrace přizpůsobenou pro vaše místní úlohy. Azure Migrate: posouzení serveru poskytuje zjišťování bez agentů, používá přihlašovací údaje hosta počítače a vzdáleně přistupuje k počítačům pomocí volání WMI a SSH.

**Podpora** | **Podrobnosti**
--- | ---
Podporované počítače | Místní virtuální počítače VMware
Operační systém počítače | Všechny verze systému Windows a Linux
Přihlašovací údaje | Aktuálně podporuje použití jednoho pověření pro všechny servery se systémem Windows a jedno přihlašovací údaje pro všechny servery se systémem Linux. Vytvoříte uživatelský účet hosta pro virtuální počítače s Windows a běžný/normální uživatelský účet (bez přístupu sudo) pro všechny virtuální počítače se systémem Linux.
Omezení počtu počítačů pro zjišťování aplikací | 10000 na zařízení. 35000 na projekt

## <a name="assessment-vcenter-server-requirements"></a>Posouzení – požadavky na vCenter Server

Tato tabulka shrnuje podporu a omezení hodnocení pro virtualizační servery VMware.

**Podpora** | **Podrobnosti**
--- | ---
**Server vCenter** | Virtuální počítače VMware, které chcete vyhodnotit, se musí spravovat na jednom nebo víc serverech vCenter, na kterých běží 5,5, 6,0, 6,5 nebo 6,7.

## <a name="assessment-vcenter-server-permissions"></a>Vyhodnocení – vCenter Server oprávnění

Azure Migrate musí mít přístup k vCenter Server, aby se zjistily virtuální počítače pro účely posouzení a migrace bez agentů.

- Pokud máte v úmyslu zjišťovat aplikace nebo vizualizovat závislost bez agentů, vytvořte účet vCenter Server s přístupem jen pro čtení společně s oprávněními povolenými pro **virtuální počítače** > **operacemi hostů**.

  ![oprávnění účtu vCenter Server](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- Pokud neplánujete funkci zjišťování aplikací a vizualizaci závislostí bez agentů, nastavte pro vCenter Server účet jen pro čtení.

## <a name="assessment-appliance-requirements"></a>Posouzení – požadavky na zařízení

Azure Migrate spouští odlehčené zařízení pro zjišťování virtuálních počítačů VMware a posílání metadat a dat o výkonu virtuálních počítačů do Azure Migrate. Zařízení pro VMware je nasazeno pomocí šablony vajíček importované do vCenter Server. Následující tabulka shrnuje požadavky na zařízení.

**Podpora** | **Podrobnosti**
--- | ---
**Nasazení zařízení** | Zařízení nasadíte jako virtuální počítač VMware. Potřebujete dostatek prostředků na vCenter Server k přidělení virtuálního počítače s 32 GB RAM, 8 vCPU, přibližně 80 GB diskového úložiště a externím virtuálním přepínačem.<br/><br/> Zařízení vyžaduje přístup k Internetu, a to buď přímo, nebo prostřednictvím proxy serveru.<br/> Virtuální počítač zařízení musí být nasazen na hostiteli ESXi, na kterém běží verze 5,5 nebo novější.
**Azure Migrate projekt** | Zařízení může být přidruženo k jednomu projektu. <br/> K jednomu projektu může být přidružen libovolný počet zařízení.<br/> V projektu můžete vyhodnotit až 35 000 virtuálních počítačů.
**Rozpoznávání** | Zařízení může na vCenter Server zjistit až 10 000 virtuálních počítačů VMware.<br/> Zařízení se může připojit k jednomu vCenter Server.
**Skupina posouzení** | Do jedné skupiny můžete přidat až 35 000 počítačů.
**Posouzení** | V jednom posouzení můžete vyhodnotit až 35 000 virtuálních počítačů.


## <a name="assessment-url-access-requirements"></a>Posouzení – požadavky na přístup k adresám URL

Zařízení Azure Migrate potřebuje připojení k Internetu.

- Když zařízení nasadíte, Azure Migrate provede kontrolu připojení k adresám URL, které jsou shrnuté v následující tabulce.
- Pokud pro připojení k Internetu používáte proxy server založený na adrese URL, povolte přístup k těmto adresám URL a ujistěte se, že proxy překládá všechny záznamy CNAME přijaté při vyhledávání adres URL.

**Adresa URL** | **Podrobnosti**  
--- | --- |
*.portal.azure.com  | V Azure Portal přejděte na Azure Migrate.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com | Přihlaste se ke svému předplatnému Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Vytvořte pro zařízení služby Active Directory, které budou komunikovat se službou Azure Migrate.
management.azure.com | Vytvořte pro zařízení služby Active Directory, které budou komunikovat se službou Azure Migrate.
dc.services.visualstudio.com | Nahrávat protokoly aplikací používané pro interní monitorování
*.vault.azure.net | Správa tajných kódů v Azure Key Vault.
*.servicebus.windows.net | Komunikace mezi zařízením a službou Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Připojte se k adresám URL služby Azure Migrate.
*.blob.core.windows.net | Nahrajte data do účtů úložiště.
https://aka.ms/latestapplianceservices<br/><br/> https://download.microsoft.com/download | Používá se k aktualizaci Azure Migrate zařízení.

## <a name="assessment-port-requirements"></a>Posouzení – požadavky na port

**Zařízení** | **Vázán**
--- | ---
Náplně | Příchozí připojení na portu TCP 3389 umožňující připojení ke vzdálené ploše zařízení.<br/><br/> Příchozí připojení na portu 44368 pro vzdálený přístup k aplikaci pro správu zařízení pomocí adresy URL: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Odchozí připojení na portu 443, 5671 a 5672 pro odeslání metadat zjišťování a výkonu pro Azure Migrate.
Server vCenter | Příchozí připojení na portu TCP 443, aby zařízení mohla shromažďovat metadata o konfiguraci a výkonu pro posouzení. <br/><br/> Ve výchozím nastavení se zařízení připojuje ke vCenter na portu 443. Pokud Server vCenter naslouchá na jiném portu, můžete změnit port při nastavení zjišťování.

## <a name="assessment-dependency-visualization"></a>Posouzení – vizualizace závislosti

Vizualizace závislosti vám pomůže vizualizovat závislosti mezi počítači, které chcete vyhodnotit a migrovat. Mapování závislostí se obvykle používá, pokud chcete vyhodnotit počítače s vyšší úrovní spolehlivosti. V případě virtuálních počítačů VMware je Vizualizace závislostí podporována následujícím způsobem:

- **Vizualizace závislostí bez agentů**: Tato možnost je aktuálně ve verzi Preview. Nevyžaduje, abyste v počítačích nainstalovali žádné agenty.
    - Funguje tak, že zachytává data připojení TCP z počítačů, pro které je povolená. Po spuštění zjišťování závislostí zařízení shromáždí data z počítačů v intervalu dotazování po dobu pěti minut.
    - Shromažďují se následující data:
        - Připojení TCP
        - Názvy procesů, které mají aktivní připojení
        - Názvy nainstalovaných aplikací, které spouštějí výše uvedené procesy
        - Ne. připojení zjištěných při každém intervalu dotazování
- **Vizualizace závislostí na základě agenta**: Pokud chcete použít vizualizaci závislostí založenou na agentech, musíte si stáhnout a nainstalovat následující agenty na každý místní počítač, který chcete analyzovat.
    - Na každém počítači je potřeba nainstalovat agenta Microsoft Monitoring Agent (MMA). [Přečtěte si další informace](how-to-create-group-machine-dependencies.md#install-the-mma) o tom, jak nainstalovat agenta MMA.
    - Na každém počítači je potřeba nainstalovat agenta závislostí. [Přečtěte si další informace](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) o tom, jak nainstalovat agenta závislostí.
    - Pokud navíc máte počítače bez připojení k internetu, musíte na ně stáhnout a nainstalovat bránu Log Analytics.

## <a name="migration---limitations"></a>Migrace – omezení
Pro replikaci můžete vybrat až 10 virtuálních počítačů najednou. Pokud chcete migrovat více počítačů, proveďte replikaci do skupin po 10. Pro migraci bez agentů VMware můžete současně spustit až 100 replikace.

## <a name="agentless-migration-vmware-server-requirements"></a>Migrace bez agentů – požadavky na server VMware

Tato tabulka shrnuje podporu a omezení hodnocení pro virtualizační servery VMware.

**Podpora** | **Podrobnosti**
--- | ---
vCenter Server | Verze 5,5, 6,0, 6,5 nebo 6,7.
VMware vSphere | Verze 5,5, 6,0, 6,5 nebo 6,7,

## <a name="agentless-migration-vcenter-server-permissions"></a>Migrace bez agenta – vCenter Server oprávnění

**Oprávnění** | **Podrobnosti**
--- | ---
Datastore.Browse | Povolí procházení souborů protokolu virtuálních počítačů, aby bylo možné řešit problémy při vytváření a odstraňování snímků.
Datastore.LowLevelFileOperations | V prohlížeči úložiště dat povolte operace čtení, zápisu, odstranění a přejmenování, abyste mohli řešit problémy při vytváření a odstraňování snímků.
VirtualMachine.Configuration.DiskChangeTracking | Povolit nebo zakázat sledování změn disků virtuálních počítačů, aby bylo možné načíst změněné bloky dat mezi snímky
VirtualMachine.Configuration.DiskLease | Povolte operace zapůjčení disku pro virtuální počítač, abyste mohli číst disk pomocí VMware vSphere Virtual disk Development Kit (VDDK).
VirtualMachine.Provisioning.AllowReadOnlyDiskAccess | Povolí otevření disku na virtuálním počítači, aby se disk mohl přečíst pomocí VDDK.
VirtualMachine.Provisioning.AllowVirtualMachineDownload  | Povoluje operace čtení souborů přidružených k virtuálnímu počítači ke stažení protokolů a řešení potíží, pokud dojde k selhání.
VirtualMachine.SnapshotManagement.* | Povolí vytváření a správu snímků virtuálních počítačů pro replikaci.
Virtuální počítač. interakce. napájení vypnuto | Umožněte, aby byl virtuální počítač vypnutý během migrace do Azure.


## <a name="agentless-migration-vmware-vm-requirements"></a>Migrace bez agenta – požadavky na virtuální počítače VMware

**Podpora** | **Podrobnosti**
--- | ---
**Podporované operační systémy** | Operační systémy [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) a [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) podporované službou Azure je možné migrovat pomocí migrace bez agentů.
**Požadované změny pro Azure** | Některé virtuální počítače můžou vyžadovat změny, aby je bylo možné spouštět v Azure. Azure Migrate provede tyto změny automaticky pro následující operační systémy:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> – CentOS 6.5 +, 7.0 +</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> – Debian 7, 8<br/><br/> Pro jiné operační systémy je nutné provést úpravy ručně před migrací. Příslušné články obsahují pokyny k tomu, jak to provést.
**Spouštění ze systému Linux** | Pokud je/Boot ve vyhrazeném oddílu, měl by být umístěn na disku s operačním systémem a nesmí být rozložen na více disků.<br/> Pokud je/Boot součástí kořenového oddílu (/), musí být oddíl '/' na disku s operačním systémem a nesmí zabírat jiné disky.
**Spouštění UEFI** | Virtuální počítače se spouštěním UEFI se pro migraci nepodporují.
**Velikost disku** | 2 TB disk s operačním systémem; 4 TB datových disků
**Omezení disku** |  Až 60 disků na virtuální počítač.
**Šifrované disky/svazky** | Virtuální počítače se zašifrovanými disky nebo svazky se nepodporují pro migraci.
**Cluster sdíleného disku** | Nepodporuje se.
**Nezávislé disky** | Nepodporuje se.
**RDM/průchozí disky** | Pokud virtuální počítače mají RDM nebo průchozí disky, tyto disky se nebudou replikovat do Azure.
**NFS** | Svazky NFS připojené jako svazky na virtuálních počítačích se nebudou replikovat.
**cíle iSCSI** | Virtuální počítače s cíli iSCSI nejsou podporované pro migraci bez agenta.
**Multipath v/v** | Nepodporuje se.
**VMotion úložiště** | Nepodporuje se. Pokud virtuální počítač používá úložiště vMotion, replikace nebude fungovat.
**Seskupené síťové adaptéry** | Nepodporuje se.
**Protokolů** | Nepodporuje se.
**Cílový disk** | Virtuální počítače se dají migrovat jenom na spravované disky (Standard HDD, Premium SSD) v Azure.
**Současná replikace** | 100 virtuálních počítačů na vCenter Server. Pokud máte víc, migrujte je v dávkách 100.


## <a name="agentless-migration-appliance-requirements"></a>Migrace bez agentů – požadavky na zařízení


**Podpora** | **Podrobnosti**
--- | ---
**ESXi** | Virtuální počítač zařízení musí být nasazen na hostiteli ESXi, na kterém běží verze 5,5 nebo novější.
**Azure Migrate projekt** | Zařízení může být přidruženo k jednomu projektu.
**vCenter Server** | Zařízení může na vCenter Server zjistit až 10 000 virtuálních počítačů VMware.<br/> Zařízení se může připojit k jednomu vCenter Server.
**VDDK** | Pokud při migraci Azure Migrate serveru používáte migraci bez agenta, musí být na virtuálním počítači zařízení nainstalovaná VMware vSphere VDDK.

## <a name="agentless-migration-url-access-requirements"></a>Migrace bez agentů – požadavky na přístup k adresám URL

Zařízení Azure Migrate potřebuje připojení k Internetu.

- Když zařízení nasadíte, Azure Migrate provede kontrolu připojení k adresám URL, které jsou shrnuté v následující tabulce.
- Pokud používáte proxy server založený na adrese URL, povolte přístup k těmto adresám URL a ujistěte se, že proxy překládá všechny záznamy CNAME přijaté při vyhledávání adres URL.

**Adresa URL** | **Podrobnosti**  
--- | ---
*.portal.azure.com | V Azure Portal přejděte na Azure Migrate.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com  | Přihlaste se ke svému předplatnému Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Vytvořte pro zařízení služby Active Directory, které budou komunikovat se službou Azure Migrate.
management.azure.com | Vytvořte pro zařízení služby Active Directory, které budou komunikovat se službou Azure Migrate.
dc.services.visualstudio.com | Nahrávat protokoly aplikací používané pro interní monitorování
*.vault.azure.net | Správa tajných kódů v Azure Key Vault.
*.servicebus.windows.net | Komunikace mezi zařízením a službou Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Připojte se k adresám URL služby Azure Migrate.
*.blob.core.windows.net | Nahrajte data do účtů úložiště.
https://aka.ms/latestapplianceservices<br/><br/> https://download.microsoft.com/download | Používá se k aktualizaci Azure Migrate zařízení.


## <a name="agentless-migration-port-requirements"></a>Migrace bez agentů – požadavky na porty

**Zařízení** | **Vázán**
--- | ---
Náplně | Odchozí připojení na portu 443 pro nahrání replikovaných dat do Azure a komunikaci s Azure Migrate službami orchestrace replikace a migrace.
Server vCenter | Příchozí připojení na portu 443, aby zařízení mohla orchestrovat replikaci – vytvářet snímky, kopírovat data, snímky verzí
Hostitel vSphere/EXSI | Příchozí na portu TCP 902, aby zařízení mohl replikovat data ze snímků.


## <a name="agent-based-migration-vmware-server-requirements"></a>Migrace založená na agentech – požadavky na server VMware

Tato tabulka shrnuje podporu a omezení hodnocení pro virtualizační servery VMware.

**Podpora** | **Podrobnosti**
--- | ---
vCenter Server | Verze 5,5, 6,0, 6,5 nebo 6,7.
VMware vSphere | Verze 5,5, 6,0, 6,5 nebo 6,7.

### <a name="agent-based-migration-vcenter-server-permissions"></a>Migrace založená na agentech – vCenter Server oprávnění

Účet jen pro čtení pro vCenter Server.

## <a name="agent-based-migration-replication-appliance-requirements"></a>Migrace založená na agentech – požadavky na zařízení replikace

V tabulce jsou shrnuty požadavky na [zařízení replikace](migrate-replication-appliance.md) používané pro migraci virtuálních počítačů VMware a fyzických serverů VMware s Azure Migrate serverem.

> [!NOTE]
> Při nastavování zařízení replikace pomocí šablony vajíček, která je k dispozici v centru Azure Migrate, zařízení spustí systém Windows Server 2016 a splňuje požadavky na podporu. Pokud se zařízení replikace nastavuje ručně na fyzickém serveru, ujistěte se, že splňuje požadavky.



**Komponenta** | **Požadavek**
--- | ---
 | **Nastavení VMware** (zařízení VMware VM)
PowerCLI | Pokud je na virtuálním počítači VMware spuštěno zařízení replikace, měla by být nainstalovaná [verze PowerCLI 6,0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) .
Typ síťové karty | VMXNET3 (Pokud je zařízení virtuálním počítačem VMware)
 | **Nastavení hardwaru**
Procesorová jádra | 8
Paměť RAM | 16 GB
Počet disků | Tři: disk s operačním systémem, disk mezipaměti procesového serveru a jednotka pro uchovávání.
Volné místo na disku (mezipaměť) | 600 GB
Volné místo na disku (disk pro uchovávání) | 600 GB
**Nastavení softwaru** |
Operační systém | Windows Server 2016 nebo Windows Server 2012 R2
Národní prostředí operačního systému | Angličtina (en-us)
TLS | Je třeba povolit protokol TLS 1,2.
.NET Framework | Na počítači by se měla nainstalovat .NET Framework 4,6 nebo novější (se zapnutým silným kryptografií.
MySQL | Na zařízení by měl být nainstalován MySQL.<br/> Je potřeba nainstalovat MySQL. Instalaci můžete provést ručně, nebo ji Site Recovery můžete nainstalovat během nasazování zařízení.
Další aplikace | Na zařízení replikace nespouštějte jiné aplikace.
Role Windows Serveru | Nepovolujte tyto role: <br> – Active Directory Domain Services <br>– Internet Information Service <br> – Hyper-V
Zásady skupiny | Nepovolujte tyto zásady skupiny: <br> – Zabraňte přístupu k příkazovému řádku. <br> – Zabraňte přístup k nástrojům pro úpravu registru. <br> – Logika vztahu důvěryhodnosti pro přílohy souborů. <br> -Zapnout provádění skriptu. <br> [Další informace](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Žádný předdefinovaný výchozí web <br> -Žádný existující web nebo aplikace nenaslouchá na portu 443. <br>-Povolit [anonymní ověřování](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Povolit nastavení [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)
**Nastavení sítě** |
Typ IP adresy | Statická
Porty | 443 (orchestrace řídicího kanálu)<br>9443 (přenos dat)
Typ síťové karty | VMXNET3

### <a name="replication-appliance-url-access"></a>Přístup k adresám URL zařízení replikace

Zařízení replikace potřebuje k těmto adresám URL přístup.

**Adresa URL** | **Podrobnosti**
--- | ---
\*.backup.windowsazure.com | Slouží k přenosu replikovaných dat a jejich koordinaci.
\*.store.core.windows.net | Slouží k přenosu replikovaných dat a jejich koordinaci.
\*.blob.core.windows.net | Používá se pro přístup k účtu úložiště, který ukládá replikovaná data.
\*.hypervrecoverymanager.windowsazure.com | Slouží k operacím správy replikace a jejich koordinaci.
https:\//management.azure.com | Slouží k operacím správy replikace a jejich koordinaci.
*.services.visualstudio.com | Používá se pro účely telemetrie (je volitelné).
time.nist.gov | Používá se ke kontrole synchronizace mezi systémovým a globálním časem.
time.windows.com | Používá se ke kontrole synchronizace mezi systémovým a globálním časem.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | Instalační program OVF potřebuje přístup k těmto adresám URL. Používají se k řízení přístupu a správě identit pomocí Azure Active Directory
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Dokončení stažení MySQL


#### <a name="mysql-installation-options"></a>Možnosti instalace MySQL

MySQL se dá nainstalovat na zařízení replikace pomocí jedné z těchto metod.

**Metoda** | **Podrobnosti**
--- | ---
Stáhnout a nainstalovat ručně | Stáhněte si aplikaci MySQL & umístěte ji do složky C:\Temp\ASRSetup a pak ji nainstalujte ručně.<br/> Když nastavíte zařízení MySQL, bude se zobrazovat jako již nainstalované.
Bez online stažení | Umístěte aplikaci instalačního programu MySQL do složky C:\Temp\ASRSetup. Když nainstalujete zařízení a kliknete na stažení a instalaci MySQL, instalační program použije instalační program, který jste přidali.
Stažení a instalace v Azure Migrate | Po instalaci zařízení a zobrazení výzvy k MySQL vyberte **Stáhnout a nainstalovat**.



## <a name="agent-based-migration-vmware-vm-requirements"></a>Migrace založená na agentech – požadavky na virtuální počítač VMware

**Podpora** | **Podrobnosti**
--- | ---
**Zatížení počítače** | Azure Migrate podporuje migraci jakékoli úlohy (například Active Directory, SQL Server atd.) běžící v podporovaném počítači.
**Operační systémy** | Nejnovější informace najdete v části [Podpora operačního systému](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) pro Site Recovery. Azure Migrate poskytuje stejnou podporu operačního systému virtuálního počítače.
**Systém souborů Linux/úložiště hostů** | Nejnovější informace najdete v části [Podpora systému souborů Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) pro Site Recovery. Azure Migrate má stejnou podporu systému souborů Linux.
**Síť/úložiště** | Nejnovější informace najdete v části požadavky na [síť](../site-recovery/vmware-physical-azure-support-matrix.md#network) a [úložiště](../site-recovery/vmware-physical-azure-support-matrix.md#storage) pro Site Recovery. Azure Migrate poskytuje identické požadavky na síť a úložiště.
**Požadavky na Azure** | Nejnovější informace najdete v části požadavky na [síť](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [úložiště](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)a [výpočetní](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) prostředky Azure pro Site Recovery. Azure Migrate má stejné požadavky na migraci VMware.
**Služba Mobility** | Na každý virtuální počítač, který chcete migrovat, musí být nainstalovaný agent služby mobility.
**Spouštění UEFI** | Migrovaný virtuální počítač v Azure se automaticky převede na spouštěcí virtuální počítač se systémem BIOS.<br/><br/> Disk s operačním systémem by měl mít až čtyři oddíly a svazky by měly být naformátované pomocí systému souborů NTFS.
**Cílový disk** | Virtuální počítače se dají migrovat jenom na spravované disky (Standard HDD, Premium SSD) v Azure.
**Velikost disku** | 2 TB disk s operačním systémem; 8 TB pro datové disky.
**Omezení disku** |  Až 63 disků na virtuální počítač.
**Šifrované disky/svazky** | Virtuální počítače se zašifrovanými disky nebo svazky se nepodporují pro migraci.
**Cluster sdíleného disku** | Nepodporuje se.
**Nezávislé disky** | Podporuje se.
**Průchozí disky** | Podporuje se.
**NFS** | Svazky NFS připojené jako svazky na virtuálních počítačích se nebudou replikovat.
**cíle iSCSI** | Virtuální počítače s cíli iSCSI nejsou podporované pro migraci bez agenta.
**Multipath v/v** | Nepodporuje se.
**VMotion úložiště** | Podporuje se
**Seskupené síťové adaptéry** | Nepodporuje se.
**Protokolů** | Nepodporuje se.




## <a name="agent-based-migration-url-access-requirements"></a>Migrace založená na agentech – požadavky na přístup k adresám URL

Služba mobility běžící na virtuálních počítačích VMware vyžaduje připojení k Internetu.

Když nasadíte službu mobility, provedete kontrolu připojení k adresám URL, které jsou shrnuté v následující tabulce.


**Adresa URL** | **Podrobnosti**  
--- | ---
*.portal.azure.com | V Azure Portal přejděte na Azure Migrate.
*.windows.net | Přihlaste se ke svému předplatnému Azure.
*.microsoftonline.com | Vytvořte pro zařízení služby Active Directory, které budou komunikovat se službou Azure Migrate.
management.azure.com | Vytvořte pro zařízení služby Active Directory, které budou komunikovat se službou Azure Migrate.
dc.services.visualstudio.com | Nahrávat protokoly aplikací používané pro interní monitorování
*.vault.azure.net | Správa tajných kódů v Azure Key Vault.
*.servicebus.windows.net | Komunikace mezi zařízením a službou Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Připojte se k adresám URL služby Azure Migrate.
*.blob.core.windows.net | Nahrajte data do účtů úložiště.

## <a name="agent-based-migration-port-requirements"></a>Migrace založená na agentech – požadavky na port

**Zařízení** | **Vázán**
--- | ---
Virtuální počítače | Služba mobility spuštěná na virtuálních počítačích komunikuje s místním zařízením replikace (konfiguračním serverem) na portu HTTPS 443 příchozím pro správu replikací.<br/><br/> Virtuální počítače odesílají data replikace na procesový Server (spuštěný na počítači konfiguračního serveru) na portu HTTPS 9443 příchozí. Tento port lze změnit.
Replikační zařízení | Zařízení replikace orchestruje replikaci pomocí Azure přes odchozí port HTTPS 443.
Procesový Server | Procesový server přijímá data replikace, optimalizuje je a šifruje je a odesílá je do Azure Storage přes odchozí port 443.<br/> Ve výchozím nastavení běží na zařízení replikace procesový Server.

## <a name="azure-vm-requirements"></a>Požadavky na virtuální počítače Azure

Všechny místní virtuální počítače replikované do Azure musí splňovat požadavky na virtuální počítače Azure shrnuté v této tabulce. Když Site Recovery spustí kontrolu předpokladů pro replikaci, tato akce se nezdaří, pokud nejsou splněné některé z těchto požadavků.

**Komponenta** | **Požadavky** | **Podrobnosti**
--- | --- | ---
Hostovaný operační systém | Ověřte podporované operační systémy pro [virtuální počítače VMware pomocí replikace bez agentů](#agentless-migration-vmware-vm-requirements)a pro [virtuální počítače VMware pomocí replikace založené na agentech](#agent-based-migration-vmware-vm-requirements).<br/> Můžete migrovat libovolné úlohy běžící v podporovaném operačním systému. | Pokud je tato operace Nepodporovaná, ověřte chybu.
Architektura hostovaného operačního systému | 64-bit. | Pokud je tato operace Nepodporovaná, ověřte chybu.
Velikost disku operačního systému | Až 2 048 GB. | Pokud je tato operace Nepodporovaná, ověřte chybu.
Počet disků operačního systému | 1 | Pokud je tato operace Nepodporovaná, ověřte chybu.
Počet datových disků | 64 nebo méně. | Pokud je tato operace Nepodporovaná, ověřte chybu.
Velikost datového disku | Až 4 095 GB | Pokud je tato operace Nepodporovaná, ověřte chybu.
Síťové adaptéry | Podporuje se několik adaptérů. |
Sdílený virtuální pevný disk | Nepodporuje se. | Pokud je tato operace Nepodporovaná, ověřte chybu.
Disk FC | Nepodporuje se. | Pokud je tato operace Nepodporovaná, ověřte chybu.
BitLocker | Nepodporuje se. | Před povolením replikace pro počítač musí být BitLocker zakázán.
název virtuálního počítače | Od 1 do 63 znaků.<br/> Pouze písmena, číslice a pomlčky.<br/><br/> Název počítače musí začínat a končit písmenem nebo číslicí. |  Aktualizujte hodnotu ve vlastnostech počítače v Site Recovery.
Připojit po migraci – Windows | Připojení k virtuálním počítačům Azure s Windows po migraci:<br/> – Před migrací povolí RDP na místním virtuálním počítači. Ujistěte se, že jsou přidaná pravidla TCP a UDP pro **Veřejný** profil a že v části **Brána Windows Firewall** > **Povolené aplikace** je pro všechny profily povolený protokol RDP.<br/> V případě přístupu typu Site-to-site k síti VPN Povolte protokol RDP a Povolte protokol RDP v **bráně Windows Firewall** -> **povolené aplikace a funkce** pro **domény a privátní** sítě. Dále ověřte, že je zásada SAN operačního systému nastavená na **OnlineAll**. [Další informace](prepare-for-migration.md). |
Připojit po migraci – Linux | Připojení k virtuálním počítačům Azure po migraci pomocí SSH:<br/> Před migrací na místním počítači ověřte, že je služba Secure Shell nastavená na Start a že pravidla brány firewall umožňují připojení SSH.<br/> Po převzetí služeb při selhání povolte na virtuálním počítači Azure příchozí připojení k portu SSH pro pravidla skupiny zabezpečení sítě na virtuálním počítači, u kterého došlo k převzetí služeb při selhání, a pro podsíť Azure, ke které je připojený. Kromě toho přidejte veřejnou IP adresu pro virtuální počítač. |  


## <a name="next-steps"></a>Další kroky

[Připravte se na](tutorial-prepare-vmware.md) vyhodnocení a migraci VMware.
