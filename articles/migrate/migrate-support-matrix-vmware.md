---
title: Podpora pro vyhodnocení VMware v Azure Migrate
description: Přečtěte si o podpoře vyhodnocení VMware v Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: b887508fb8e422bd83aa9d13e42085d7a6bd2283
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269585"
---
# <a name="support-matrix-for-vmware-assessment"></a>Matice podpory pro vyhodnocení VMware 

Tento článek shrnuje nastavení podpory a omezení pro vyhodnocování virtuálních počítačů VMware pomocí [Azure Migrate: posouzení serveru](migrate-services-overview.md#azure-migrate-server-migration-tool). Pokud hledáte informace o migraci virtuálních počítačů VMware do Azure, Projděte si [matici podpora pro migraci](migrate-support-matrix-vmware-migration.md).

## <a name="overview"></a>Přehled

K vyhodnocení místních počítačů pro migraci do Azure pomocí tohoto článku přidáte nástroj Azure Migrate: Server Assessment Tool do projektu Azure Migrate. Nasadíte [zařízení Azure Migrate](migrate-appliance.md). Zařízení nepřetržitě zjišťuje místní počítače a odesílá data o konfiguraci a výkonu do Azure. Po zjištění počítače shromáždíte zjištěné počítače do skupin a spustíte posouzení pro skupinu.


## <a name="limitations"></a>Omezení

**Podpora** | **Podrobnosti**
--- | ---
**Omezení hodnocení**| Vyhledejte a posuďte až 35 000 virtuálních počítačů VMware v jednom [projektu](migrate-support-matrix.md#azure-migrate-projects).
**Omezení projektu** | V předplatném Azure můžete vytvořit více projektů. Projekt může zahrnovat virtuální počítače VMware, virtuální počítače Hyper-V a fyzické servery až do limitů hodnocení.
**Rozpoznávání** | Zařízení Azure Migrate může na vCenter Server zjistit až 10 000 virtuálních počítačů VMware.
**Posouzení** | Do jedné skupiny můžete přidat až 35 000 počítačů.<br/><br/> V jednom posouzení můžete vyhodnotit až 35 000 virtuálních počítačů.

[Přečtěte si další informace](concepts-assessment-calculation.md) o posouzení.


## <a name="application-discovery"></a>Zjišťování aplikací

Kromě zjišťování počítačů Azure Migrate: posouzení serveru může zjišťovat aplikace, role a funkce běžící na počítačích. Zjišťování inventáře aplikací vám umožní identifikovat a naplánovat cestu migrace přizpůsobenou pro vaše místní úlohy. 

**Podpora** | **Podrobnosti**
--- | ---
**Rozpoznávání** | Zjišťování je bez agentů, používá přihlašovací údaje hosta počítače a vzdáleně přistupuje k počítačům pomocí volání WMI a SSH.
**Podporované počítače** | Místní virtuální počítače VMware.
**Operační systém počítače** | Všechny verze systému Windows a Linux.
**přihlašovací údaje pro vCenter** | Účet vCenter Server s přístupem jen pro čtení a oprávnění povolená pro Virtual Machines > operace hosta.
**Přihlašovací údaje virtuálního počítače** | Aktuálně podporuje použití jednoho pověření pro všechny servery se systémem Windows a jedno přihlašovací údaje pro všechny servery se systémem Linux.<br/><br/> Vytvoříte uživatelský účet hosta pro virtuální počítače s Windows a běžný/normální uživatelský účet (bez přístupu sudo) pro všechny virtuální počítače se systémem Linux.
**Nástroje VMware** | Na virtuálních počítačích, které chcete zjistit, musí být nainstalované a spuštěné nástroje VMware. <br/> Pokud je vaše verze nástrojů VMware mezi 9,10 – 10.2.0, ujistěte se, že jste ji upgradovali na rámec 10.2.0.
**Powershell** | Virtuální počítače musí mít PowerShell verze 2,0 nebo vyšší.
**Přístup k portu** | Na hostitelích ESXi, na kterých běží virtuální počítače, které chcete zjišťovat, musí být zařízení Azure Migrate schopné připojit se k portu TCP 443.
**Omezení** | Pro zjišťování aplikací můžete zjistit až 10000 na každé zařízení. 

## <a name="vmware-requirements"></a>Požadavky VMware

**VMware** | **Podrobnosti**
--- | ---
**vCenter Server** | Počítače, které chcete zjišťovat a hodnotit, se musí spravovat pomocí vCenter Server verze 5,5, 6,0, 6,5 nebo 6,7.
**Oprávnění (posouzení)** | vCenter Server účet jen pro čtení.
**Oprávnění (zjišťování aplikací)** | vCenter Server účet s přístupem jen pro čtení a oprávnění povolená pro **virtuální počítače > operace hostů**.
**Oprávnění (Vizualizace závislostí)** | Účet serveru Center s přístupem jen pro čtení a oprávnění povolených pro **virtuální počítače** > **operací hostů**.


## <a name="azure-migrate-appliance-requirements"></a>Požadavky zařízení Azure Migrate

Azure Migrate používá [zařízení Azure Migrate](migrate-appliance.md) ke zjišťování a hodnocení. Zařízení pro VMware je nasazeno pomocí šablony vajíček importované do vCenter Server. 

- Přečtěte si informace o [požadavcích na zařízení](migrate-appliance.md#appliance---vmware) pro VMware.
- Přečtěte si o [adresách URL](migrate-appliance.md#url-access) , ke kterým zařízení potřebuje mít přístup.

## <a name="port-access"></a>Přístup k portu

**Zařízení** | **Vázán**
--- | ---
Náplně | Příchozí připojení na portu TCP 3389 umožňující připojení ke vzdálené ploše zařízení.<br/><br/> Příchozí připojení na portu 44368 pro vzdálený přístup k aplikaci pro správu zařízení pomocí adresy URL: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Odchozí připojení na portu 443 (HTTPS), 5671 a 5672 (AMQP), která odesílají metadata zjišťování a výkonu Azure Migrate.
Server vCenter | Příchozí připojení na portu TCP 443, aby zařízení mohla shromažďovat metadata o konfiguraci a výkonu pro posouzení. <br/><br/> Ve výchozím nastavení se zařízení připojuje ke vCenter na portu 443. Pokud Server vCenter naslouchá na jiném portu, můžete změnit port při nastavení zjišťování.
Hostitelé ESXi | **Požadováno pouze pro [vizualizaci závislostí](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-dependency-visualization) [aplikace](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#application-discovery) a bez agentů** <br/><br/> Zařízení se připojí k hostitelům ESXi na portu TCP 443 a zjistí aplikace a spustí vizualizaci závislostí bez agentů na virtuálních počítačích běžících na hostitelích.

## <a name="agent-based-dependency-visualization"></a>Vizualizace závislostí založená na agentech

[Vizualizace závislosti](concepts-dependency-visualization.md) vám pomůže vizualizovat závislosti mezi počítači, které chcete vyhodnotit a migrovat. Pro vizualizaci založenou na agentech jsou požadavky a omezení shrnuté v následující tabulce.


**Požadavek** | **Podrobnosti**
--- | ---
**Nasazení** | Než nasadíte vizualizaci závislostí, měli byste mít Azure Migrate projekt, a to pomocí Azure Migrate: Nástroj pro vyhodnocení serveru přidaný do projektu. Vizualizace závislostí nasadíte po nastavení zařízení Azure Migrate pro zjišťování vašich místních počítačů.<br/><br/> Vizualizace závislostí není v Azure Government k dispozici.
**Mapa služeb** | Vizualizace závislostí založená na agentech používá řešení [Service map](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) v [protokolech Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).<br/><br/> K nasazení aplikace přidružíte nový nebo existující Log Analytics pracovní prostor k projektu Azure Migrate.
**Pracovní prostor služby Log Analytics** | Pracovní prostor musí být ve stejném předplatném jako projekt Azure Migrate.<br/><br/> Azure Migrate podporuje pracovní prostory, které jsou umístěné v oblastech Východní USA, jihovýchodní Asie a Západní Evropa.<br/><br/>  Pracovní prostor musí být v oblasti, ve které [je podporovaná Service map](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> Pracovní prostor pro Azure Migrate projekt nelze po přidání změnit.
**Poplatky za** | V řešení Service Map se neúčtují žádné poplatky za prvních 180 dní (od data, kdy jste přidružení pracovního prostoru Log Analytics k projektu Azure Migrate).<br/><br/> Po 180 dnech budou platit standardní poplatky za Log Analytics.<br/><br/> Použití jakéhokoli řešení, které je jiné než Service Map v přidruženém pracovním prostoru Log Analytics, bude mít za následek standardní Log Analytics poplatky.<br/><br/> Pokud odstraníte Azure Migrate projekt, pracovní prostor se s ním neodstraní. Po odstranění projektu Service Map není zadarmo a každý uzel se bude účtovat podle placené úrovně Log Analytics pracovního prostoru.
**Technici** | Vizualizace závislostí na základě agentů vyžaduje instalaci dvou agentů do každého počítače, který chcete analyzovat.<br/><br/> - [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> [Agent závislostí](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent)- . 
**Připojení k Internetu** | Pokud nejsou počítače připojené k Internetu, musíte na ně nainstalovat bránu Log Analytics.


## <a name="agentless-dependency-visualization"></a>Vizualizace závislostí bez agentů

Tato možnost je v současnosti v náhledové verzi. [Další informace](how-to-create-group-machine-dependencies-agentless.md). Požadavky jsou shrnuty v následující tabulce.

**Požadavek** | **Podrobnosti**
--- | ---
**Nasazení** | Než nasadíte vizualizaci závislostí, měli byste mít Azure Migrate projekt, a to pomocí Azure Migrate: Nástroj pro vyhodnocení serveru přidaný do projektu. Vizualizace závislostí nasadíte po nastavení zařízení Azure Migrate pro zjišťování vašich místních počítačů.
**Podpora virtuálních počítačů** | Aktuálně se podporuje jenom pro virtuální počítače VMware.
**Virtuální počítače s Windows** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64 bitů)
**Virtuální počítače s Linuxem** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14,04, 16,04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.
**Účet systému Windows** |  Vizualizace vyžaduje účet správce místní nebo doménového účtu.
**Účet Linux** | Vizualizace vyžaduje uživatelský účet s oprávněním root.<br/><br/> V alternativním případě uživatelský účet potřebuje tato oprávnění pro soubory/bin/netstat a/bin/ls: CAP_DAC_READ_SEARCH a CAP_SYS_PTRACE.
**Agenti virtuálních počítačů** | Na virtuálních počítačích není nutný žádný agent.
**Nástroje VMware** | Na virtuálních počítačích, které chcete analyzovat, musí být nainstalované a spuštěné nástroje VMware. <br/> Pokud je vaše verze nástrojů VMware mezi 9,10 – 10.2.0, ujistěte se, že jste ji upgradovali na rámec 10.2.0.
**Powershell** | Virtuální počítače musí mít PowerShell verze 2,0 nebo vyšší.
**přihlašovací údaje pro vCenter** | Účet vCenter Server s přístupem jen pro čtení a oprávnění povolená pro Virtual Machines > operace hosta.
**Přístup k portu** | Na hostitelích ESXi, na kterých běží virtuální počítače, které chcete analyzovat, musí být zařízení Azure Migrate schopné připojit se k portu TCP 443.



## <a name="next-steps"></a>Další kroky

- [Projděte si](best-practices-assessment.md) osvědčené postupy pro vytváření hodnocení.
- [Příprava na vyhodnocení VMware](tutorial-prepare-vmware.md) .
