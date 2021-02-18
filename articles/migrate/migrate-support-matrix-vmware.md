---
title: Podpora pro vyhodnocení VMware v Azure Migrate
description: Přečtěte si o podpoře vyhodnocení virtuálních počítačů VMware pomocí posouzení serveru Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: cea13d0037a8b8074a78177794c553668d3cd31b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590610"
---
# <a name="support-matrix-for-vmware-assessment"></a>Matice podpory pro vyhodnocení VMware 

Tento článek shrnuje požadavky a požadavky na podporu při zjišťování a hodnocení virtuálních počítačů VMware pro migraci do Azure pomocí nástroje [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) Tool. Pro vyhodnocení virtuálních počítačů VMware vytvoříte Azure Migrate projekt a přidáte Nástroj pro vyhodnocení serveru do projektu. Po přidání nástroje nasadíte zařízení Azure Migrate. Zařízení nepřetržitě zjišťuje místní počítače a odesílá do Azure metadata počítače a data o výkonu. Po dokončení zjišťování shromáždíte zjištěné počítače do skupin a spustíte posouzení pro skupinu. 

Pokud chcete migrovat virtuální počítače VMware do Azure, přečtěte si [matici podpora migrace](migrate-support-matrix-vmware-migration.md).



## <a name="limitations"></a>Omezení

**Požadavek** | **Podrobnosti**
--- | ---
**Omezení projektu** | V předplatném Azure můžete vytvořit více projektů.<br/><br/> V jednom [projektu](migrate-support-matrix.md#azure-migrate-projects)můžete zjistit a posoudit až 35 000 virtuálních počítačů VMware. Projekt může také zahrnovat fyzické servery a virtuální počítače Hyper-V, a to až do limitů hodnocení pro každé z nich.
**Zjišťování** | Zařízení Azure Migrate může na vCenter Server zjistit až 10 000 virtuálních počítačů VMware.
**Posouzení** | Do jedné skupiny můžete přidat až 35 000 počítačů.<br/><br/> V jednom posouzení můžete vyhodnotit až 35 000 virtuálních počítačů.

[Přečtěte si další informace](concepts-assessment-calculation.md) o posouzení.


## <a name="vmware-requirements"></a>Požadavky VMware

**VMware** | **Podrobnosti**
--- | ---
**vCenter Server** | Počítače, které chcete zjišťovat a hodnotit, se musí spravovat pomocí vCenter Server verze 5,5, 6,0, 6,5, 6,7 nebo 7,0.<br/><br/> Zjišťování virtuálních počítačů VMware poskytnutím podrobností hostitele ESXi v zařízení není aktuálně podporováno.
**Oprávnění** | Vyhodnocování serveru vyžaduje pro zjišťování a hodnocení účet vCenter Server jen pro čtení.<br/><br/> Pokud chcete provádět zjišťování aplikací nebo vizualizaci závislostí, musí mít účet povolená oprávnění pro **Virtual Machines**  >  **operace hostů**.

## <a name="vm-requirements"></a>Požadavky na virtuální počítače
**VMware** | **Podrobnosti**
--- | ---
**Virtuální počítače VMware** | Pro migraci se dají zhodnotit všechny operační systémy. 
**Storage** | Podporují se disky připojené k řadičům SCSI, IDE a SATA.


## <a name="azure-migrate-appliance-requirements"></a>Požadavky zařízení Azure Migrate

Azure Migrate používá [zařízení Azure Migrate](migrate-appliance.md) ke zjišťování a hodnocení. Zařízení můžete nasadit jako virtuální počítač VMware pomocí šablony vajíček, naimportovat do vCenter Server nebo pomocí [skriptu PowerShellu](deploy-appliance-script.md).

- Přečtěte si informace o [požadavcích na zařízení](migrate-appliance.md#appliance---vmware) pro VMware.
- V Azure Government musíte zařízení nasadit [pomocí skriptu](deploy-appliance-script-government.md).
- Zkontrolujte adresy URL, které zařízení potřebuje pro přístup k [veřejným](migrate-appliance.md#public-cloud-urls) a [státním](migrate-appliance.md#government-cloud-urls) cloudům.


## <a name="port-access-requirements"></a>Požadavky na přístup k portu

**Zařízení** | **Připojení**
--- | ---
**Náplně** | Příchozí připojení na portu TCP 3389 umožňující připojení ke vzdálené ploše zařízení.<br/><br/> Příchozí připojení na portu 44368 pro vzdálený přístup k aplikaci pro správu zařízení pomocí adresy URL: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Odchozí připojení na portu 443 (HTTPS) k odeslání metadat zjišťování a výkonu do Azure Migrate.
**Server vCenter** | Příchozí připojení na portu TCP 443, aby zařízení mohla shromažďovat metadata o konfiguraci a výkonu pro posouzení. <br/><br/> Ve výchozím nastavení se zařízení připojuje ke vCenter na portu 443. Pokud Server vCenter naslouchá na jiném portu, můžete změnit port při nastavení zjišťování.
**Hostitelé ESXi** | Pokud chcete provést [zjišťování aplikací](how-to-discover-applications.md)nebo [analýzu závislostí bez agenta](concepts-dependency-visualization.md#agentless-analysis), zařízení se připojí k hostitelům ESXi na portu TCP 443, aby bylo možné zjišťovat aplikace a spouštět vizualizaci závislostí bez agentů na virtuálních počítačích.

## <a name="application-discovery-requirements"></a>Požadavky na zjišťování aplikací

Kromě zjišťování počítačů může posouzení serveru zjišťovat aplikace, role a funkce běžící na počítačích. Zjišťování inventáře aplikací vám umožní identifikovat a naplánovat cestu migrace přizpůsobenou pro vaše místní úlohy. 

**Podpora** | **Podrobnosti**
--- | ---
**Podporované počítače** | Aktuálně se podporuje jenom pro virtuální počítače VMware. Z každého zařízení Azure Migrate můžete zjišťovat aplikace nainstalované až do 10000 virtuálních počítačů VMware.
**Operační systémy** | Podpora pro virtuální počítače, na kterých běží všechny verze systému Windows a Linux.
**Požadavky na virtuální počítače** | Na virtuálních počítačích, na kterých chcete zjišťovat aplikace, musí být nainstalované a spuštěné nástroje VMware. <br/><br/> Verze nástrojů VMware musí být pozdější než 10.2.0.<br/><br/> Virtuální počítače musí mít nainstalované prostředí PowerShell verze 2,0 nebo novější.
**Zjišťování** | Informace o aplikacích nainstalovaných na virtuálním počítači se shromažďují z vCenter Server pomocí nástrojů VMware nainstalovaných na virtuálním počítači. Zařízení shromažďuje informace o aplikaci z vCenter Server pomocí rozhraní API vSphere. Zjišťování aplikací je bez agenta. Na virtuálních počítačích není nic nainstalované a zařízení se nepřipojuje přímo k virtuálním počítačům. Na virtuálních počítačích by měla být povolená a dostupná služba WMI/SSH.
**vCenter** | VCenter Server účet jen pro čtení, který se používá pro posouzení, potřebuje oprávnění povolená pro **Virtual Machines**  >  **operace hosta**, aby bylo možné pracovat s virtuálním počítačem pro zjišťování aplikací.
**Přístup k virtuálnímu počítači** | Zjišťování aplikací vyžaduje místní uživatelský účet na virtuálním počítači pro zjišťování aplikací.<br/><br/> Azure Migrate aktuálně podporuje použití jednoho pověření pro všechny servery se systémem Windows a jedno přihlašovací údaje pro všechny servery se systémem Linux.<br/><br/> Vytvoříte uživatelský účet hosta pro virtuální počítače s Windows a běžný/normální uživatelský účet (bez přístupu sudo) pro všechny virtuální počítače se systémem Linux.
**Přístup k portu** | Zařízení Azure Migrate musí být schopné se připojit k portu TCP 443 na hostiteli ESXi, na kterém běží virtuální počítače, na kterých chcete zjišťovat aplikace. VCenter Server vrátí připojení hostitele ESXI pro stažení souboru obsahujícího informace o aplikaci.



## <a name="dependency-analysis-requirements-agentless"></a>Požadavky na analýzu závislostí (bez agentů)

[Analýza závislostí](concepts-dependency-visualization.md) vám pomůže identifikovat závislosti mezi místními počítači, které chcete vyhodnotit a migrovat do Azure. Tabulka shrnuje požadavky na nastavení analýzy závislostí bez agentů. 

**Podpora** | **Podrobnosti**
--- | --- 
**Podporované počítače** | Aktuálně se podporuje jenom pro virtuální počítače VMware.
**Virtuální počítače s Windows** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64 bitů).<br/>Microsoft Windows Server 2008 (32). 
**Virtuální počítače s Linuxem** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14,04, 16,04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.<br/> SUSE Linux Enterprise Server 11 a novější.
**Požadavky na virtuální počítače** | Nástroje VMware (novější než 10.2.0) musí být nainstalované a spuštěné na virtuálních počítačích, které chcete analyzovat.<br/><br/> Virtuální počítače musí mít nainstalované prostředí PowerShell verze 2,0 nebo novější.
**Metoda zjišťování** |  Informace o závislostech mezi virtuálními počítači se shromažďují z vCenter Server pomocí nástrojů VMware nainstalovaných na virtuálním počítači. Zařízení shromažďuje informace z vCenter Server pomocí rozhraní API vSphere. Zjišťování je bez agentů. Na virtuálním počítači není nic nainstalované a zařízení se nepřipojuje přímo k virtuálním počítačům. Na virtuálních počítačích by měla být povolená a dostupná služba WMI/SSH.
**účet vCenter** | Účet jen pro čtení používaný Azure Migrate for Assessment vyžaduje oprávnění povolená pro **operace Virtual Machines > hostů**.
**Oprávnění virtuálních počítačů s Windows** |  Účet (místní správce nebo doména) s oprávněními místního správce na virtuálních počítačích.
**Účet Linux** | Kořenový uživatelský účet nebo účet s těmito oprávněními pro soubory/bin/netstat a/bin/ls: CAP_DAC_READ_SEARCH a CAP_SYS_PTRACE.<br/><br/> Tyto možnosti nastavte pomocí následujících příkazů: <br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/LS<br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/netstat
**Přístup k portu** | Zařízení Azure Migrate musí být schopné se připojit k portu TCP 443 na hostitelích ESXI, na kterých běží virtuální počítače, u kterých chcete zjistit závislosti. VCenter Server vrátí připojení hostitele ESXI pro stažení souboru obsahujícího informace o závislostech.


## <a name="dependency-analysis-requirements-agent-based"></a>Požadavky na analýzu závislostí (založené na agentovi)

[Analýza závislostí](concepts-dependency-visualization.md) vám pomůže identifikovat závislosti mezi místními počítači, které chcete vyhodnotit a migrovat do Azure. Tabulka shrnuje požadavky na nastavení analýzy závislostí založené na agentech. 

**Požadavek** | **Podrobnosti** 
--- | --- 
**Před nasazením** | Měli byste mít Azure Migrate projekt, a to pomocí Azure Migrate: Nástroj pro vyhodnocení serveru přidaný do projektu.<br/><br/>  Vizualizace závislostí nasadíte po nastavení zařízení Azure Migrate pro zjištění vašich místních počítačů.<br/><br/> [Naučte](create-manage-projects.md) se, jak poprvé vytvořit projekt.<br/> [Přečtěte si, jak](how-to-assess.md) přidat nástroj pro vyhodnocení do existujícího projektu.<br/> Naučte se, jak nastavit zařízení Azure Migrate pro posouzení [technologie Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)nebo fyzických serverů.
**Podporované počítače** | Podporováno pro všechny počítače.
**Azure Government** | Vizualizace závislostí není v Azure Government k dispozici.
**Log Analytics** | Azure Migrate používá řešení [Service map](../azure-monitor/vm/service-map.md) v [protokolech Azure monitor](../azure-monitor/logs/log-query-overview.md) pro vizualizaci závislostí.<br/><br/> K projektu Azure Migrate přidružíte nový nebo existující Log Analytics pracovní prostor. Pracovní prostor pro Azure Migrate projekt nelze po přidání změnit. <br/><br/> Pracovní prostor musí být ve stejném předplatném jako projekt Azure Migrate.<br/><br/> Pracovní prostor se musí nacházet v oblastech Východní USA, jihovýchodní Asie nebo Západní Evropa. Pracovní prostory v jiných oblastech nelze přidružit k projektu.<br/><br/> Pracovní prostor musí být v oblasti, ve které [je podporovaná Service map](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> V Log Analytics je pracovní prostor přidružený k Azure Migrate označený klíčem projektu migrace a názvem projektu.
**Vyžadovaná agenti** | Na každém počítači, který chcete analyzovat, nainstalujte následující agenty:<br/><br/> [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md).<br/> [Agent závislostí](../azure-monitor/agents/agents-overview.md#dependency-agent).<br/><br/> Pokud nejsou místní počítače připojené k Internetu, musíte na ně stáhnout a nainstalovat bránu Log Analytics.<br/><br/> Přečtěte si další informace o instalaci [agenta závislostí](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) a [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Pracovní prostor Log Analytics** | Pracovní prostor musí být ve stejném předplatném jako projekt Azure Migrate.<br/><br/> Azure Migrate podporuje pracovní prostory, které jsou umístěné v oblastech Východní USA, jihovýchodní Asie a Západní Evropa.<br/><br/>  Pracovní prostor musí být v oblasti, ve které [je podporovaná Service map](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> Pracovní prostor pro Azure Migrate projekt nelze po přidání změnit.
**Náklady** | V řešení Service Map se neúčtují žádné poplatky za prvních 180 dní (od dne, kdy přidružíte pracovní prostor Log Analytics k projektu Azure Migrate)/<br/><br/> Po uplynutí 180 dnů se začnou účtovat standardní poplatky za Log Analytics.<br/><br/> Použití jiného řešení než Service Map v přidruženém pracovním prostoru Log Analytics bude účtovat [standardní poplatky](https://azure.microsoft.com/pricing/details/log-analytics/) za Log Analytics.<br/><br/> Když se projekt Azure Migrate odstraní, pracovní prostor se spolu s ním neodstraní. Po odstranění projektu Service Map využití není volné a každý uzel se bude účtovat podle placené úrovně Log Analytics pracovního prostoru/<br/><br/>Pokud máte projekty, které jste vytvořili před Azure Migrate všeobecné dostupnosti (GA-28 února 2018), mohly by vám být účtovány další Service Map poplatky. Aby se zajistila platba jenom po 180 dnech, doporučujeme vytvořit nový projekt, protože stávající pracovní prostory před GAm jsou stále Fakturovatelné.
**správy** | Při registraci agentů do pracovního prostoru použijete ID a klíč poskytnutý Azure Migrate projektem.<br/><br/> Pracovní prostor Log Analytics můžete použít mimo Azure Migrate.<br/><br/> Pokud odstraníte přidružený Azure Migrate projekt, pracovní prostor se automaticky neodstraní. [Odstraňte ji ručně](../azure-monitor/logs/manage-access.md).<br/><br/> Pokud neodstraníte projekt Azure Migrate, neodstraňujte pracovní prostor vytvořený pomocí Azure Migrate. Pokud to uděláte, funkce vizualizace závislosti nebude fungovat podle očekávání.
**Připojení k internetu** | Pokud nejsou počítače připojené k Internetu, musíte na ně nainstalovat bránu Log Analytics.
**Azure Government** | Analýza závislostí založená na agentech není podporována.


## <a name="next-steps"></a>Další kroky

- [Projděte si](best-practices-assessment.md) osvědčené postupy pro vytváření hodnocení.
- [Příprava na vyhodnocení VMware](./tutorial-discover-vmware.md) .