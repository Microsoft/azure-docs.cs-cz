---
title: Podpora pro vyhodnocení VMware v Azure Migrate
description: Další informace o podpoře vyhodnocování pro servery běžící v prostředí VMware s Azure Migrate zjišťování a posouzení
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 4d51fc13e3587c21a7340b35db10d3cf36ab74b5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557543"
---
# <a name="support-matrix-for-vmware-assessment"></a>Matice podpory pro vyhodnocení VMware 

Tento článek shrnuje požadavky a požadavky na podporu při zjišťování a hodnocení serverů běžících v prostředí VMware pro migraci do Azure pomocí nástroje [Azure Migrate: Discovery and Assessment](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) Tool. Chcete-li vyhodnotit servery, vytvořte projekt, který do projektu přidá nástroj Azure Migrate: Discovery and Assessment. Po přidání nástroje nasadíte zařízení Azure Migrate. Zařízení nepřetržitě zjišťuje místní servery a odesílá metadata o konfiguraci a výkonu do Azure. Po dokončení zjišťování budete shromažďovat zjištěné servery do skupin a spustit posouzení pro skupinu.

Pokud chcete migrovat servery VMware do Azure, přečtěte si [matici podpora migrace](migrate-support-matrix-vmware-migration.md).



## <a name="limitations"></a>Omezení

**Požadavek** | **Podrobnosti**
--- | ---
**Omezení projektu** | V předplatném Azure můžete vytvořit více projektů.<br/><br/> Můžete vyhledat a posoudit až 50 000 serverů z prostředí VMware v jednom [projektu](migrate-support-matrix.md#project). Projekt může také zahrnovat fyzické servery a servery z prostředí Hyper-V, a to až do limitů hodnocení.
**Zjišťování** | Zařízení Azure Migrate může na vCenter Server zjistit až 10 000 serverů.
**Posouzení** | Do jedné skupiny můžete přidat až 35 000 serverů.<br/><br/> V jednom posouzení můžete posoudit až 35 000 serverů.

[Přečtěte si další informace](concepts-assessment-calculation.md) o posouzení.


## <a name="vmware-requirements"></a>Požadavky VMware

**VMware** | **Podrobnosti**
--- | ---
**vCenter Server** | Servery, které chcete zjišťovat a hodnotit, se musí spravovat pomocí vCenter Server verze 5,5, 6,0, 6,5, 6,7 nebo 7,0.<br/><br/> Zjišťování serverů poskytováním podrobností o hostiteli ESXi v zařízení v současnosti není podporováno.
**Oprávnění** | Azure Migrate: zjišťování a vyhodnocení potřebuje vCenter Server účet jen pro čtení pro zjišťování a posouzení.<br/><br/> Pokud chcete provést zjišťování inventáře softwaru a analýzy závislostí bez agenta, musí mít účet oprávnění povolená pro **Virtual Machines**  >  **operace hostů**.

## <a name="server-requirements"></a>Požadavky na server
**VMware** | **Podrobnosti**
--- | ---
**Podporovaný operační systém** | Pro všechny operační systémy Windows a Linux se dá zhodnotit migrace.
**Storage** | Podporují se disky připojené k řadičům SCSI, IDE a SATA.


## <a name="azure-migrate-appliance-requirements"></a>Požadavky zařízení Azure Migrate

Azure Migrate používá [zařízení Azure Migrate](migrate-appliance.md) ke zjišťování a hodnocení. Zařízení můžete nasadit jako server ve vašem prostředí VMware pomocí šablony pro VAJÍČKa, kterou jste naimportovali do vCenter Server nebo pomocí [skriptu PowerShellu](deploy-appliance-script.md).

- Přečtěte si informace o [požadavcích na zařízení](migrate-appliance.md#appliance---vmware) pro VMware.
- V Azure Government musíte zařízení nasadit [pomocí skriptu](deploy-appliance-script-government.md).
- Zkontrolujte adresy URL, které zařízení potřebuje pro přístup k [veřejným](migrate-appliance.md#public-cloud-urls) a [státním](migrate-appliance.md#government-cloud-urls) cloudům.


## <a name="port-access-requirements"></a>Požadavky na přístup k portu

**Zařízení** | **Připojení**
--- | ---
**Náplně** | Příchozí připojení na portu TCP 3389 umožňující připojení ke vzdálené ploše zařízení.<br/><br/> Příchozí připojení na portu 44368 pro vzdálený přístup k aplikaci pro správu zařízení pomocí adresy URL: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Odchozí připojení na portu 443 (HTTPS) k odeslání metadat zjišťování a výkonu do Azure Migrate.
**Server vCenter** | Příchozí připojení na portu TCP 443, aby zařízení mohla shromažďovat metadata o konfiguraci a výkonu pro posouzení. <br/><br/> Ve výchozím nastavení se zařízení připojuje ke vCenter na portu 443. Pokud Server vCenter naslouchá na jiném portu, můžete změnit port při nastavení zjišťování.
**Hostitelé ESXi** | Pokud chcete provést [zjišťování inventáře softwaru](how-to-discover-applications.md)nebo [analýzu závislostí bez agenta](concepts-dependency-visualization.md#agentless-analysis), připojí se zařízení k hostitelům ESXi na portu TCP 443, aby bylo možné zjistit inventář softwaru a závislosti na serverech.

## <a name="application-discovery-requirements"></a>Požadavky na zjišťování aplikací

Kromě zjišťování serverů Azure Migrate: zjišťování a hodnocení může zjistit inventář softwaru běžící na serverech. Funkce zjišťování aplikací umožňuje identifikovat a naplánovat cestu migrace přizpůsobenou pro vaše místní úlohy.

**Podpora** | **Podrobnosti**
--- | ---
**Podporované servery** | Aktuálně se podporuje jenom pro servery ve vašem prostředí VMware. Zjišťování aplikací můžete provádět na až 10000 serverech, od každého zařízení Azure Migrate.
**Operační systémy** | Podporují se servery, na kterých běží všechny verze Windows a Linux.
**Požadavky na virtuální počítače** | Aby bylo možné provést zjišťování inventáře softwaru, musí být na serverech nainstalovány a spuštěny nástroje VMware. <br/><br/> Verze nástrojů VMware musí být pozdější než 10.2.0.<br/><br/> Windows servery musí mít nainstalované prostředí PowerShell verze 2,0 nebo novější.
**Zjišťování** | Zjišťování aplikací na serverech se provádí z vCenter Server pomocí nástrojů VMware nainstalovaných na serverech. Zařízení shromažďuje informace o inventáři softwaru z vCenter Server pomocí rozhraní API vSphere. Zjišťování aplikací je bez agenta. Na serveru není nainstalovaný žádný agent a zařízení se nepřipojuje přímo k serverům. V uvedeném pořadí by měla být povolená a dostupná služba WMI a SSH v serverech se systémem Windows a Linux.
**vCenter Server uživatelský účet** | VCenter Server účet jen pro čtení, který se používá pro posouzení, potřebuje oprávnění povolená pro **Virtual Machines**  >  **operace hostů**, aby bylo možné komunikovat se servery pro zjišťování aplikací.
**Přístup k serveru** | Do Správce konfigurace zařízení pro zjišťování aplikací můžete přidat více přihlašovacích údajů k doméně a nedoménám (Windows/Linux).<br/><br/> Pro všechny servery se systémem Linux potřebujete uživatelský účet hosta pro servery s Windows a běžný/normální uživatelský účet (přístup bez sudo).
**Přístup k portu** | Zařízení Azure Migrate musí být schopné se připojit k portu TCP 443 na hostitelích ESXi, na kterých běží servery, na kterých chcete zjišťování aplikací provést. VCenter Server vrátí připojení hostitele ESXi pro stažení souboru, který obsahuje podrobnosti o inventáři softwaru.

## <a name="requirements-for-discovery-of-sql-server-instances-and-databases"></a>Požadavky na zjišťování instancí SQL Server a databází

[Zjišťování aplikací](how-to-discover-applications.md) identifikuje instance SQL Server. Pomocí těchto informací se zařízení pokusí připojit k příslušným instancím SQL Server prostřednictvím přihlašovacích údajů pro ověřování systému Windows nebo přihlašovacích údajů SQL Serverch ověřování uvedených na zařízení. Po připojení zařízení shromáždí údaje o konfiguraci a výkonu SQL Server instance a databáze. Konfigurační data SQL Server se aktualizují každých 24 hodin a data o výkonu se zaznamenávají každých 30 sekund.

**Podpora** | **Podrobnosti**
--- | ---
**Podporované servery** | Aktuálně se podporuje jenom pro SQL servery ve vašem prostředí VMware. Můžete zjistit až 300 instancí SQL Server nebo 6000 databází SQL, podle toho, co je nižší.
**Windows servery** | Jsou podporovány systémy Windows Server 2008 a novější.
**Servery Linux** | Aktuálně se nepodporuje.
**Mechanismus ověřování** | Podporuje se ověřování Windows i SQL Server. Přihlašovací údaje obou typů ověřování můžete zadat v nástroji Configuration Manager pro zařízení.
**Přístup k serveru SQL** | Azure Migrate vyžaduje uživatelský účet systému Windows, který je členem role serveru sysadmin.
**Verze SQL Serveru** | Jsou podporovány SQL Server 2008 a novější.
**Edice SQL Server** | Podporují se edice Enterprise, Standard, Developer a Express.
**Podporovaná konfigurace SQL** | V současné době je podporováno zjišťování pouze samostatných instancí SQL Server a odpovídajících databází.<br/> Identifikace clusteru s podporou převzetí služeb při selhání a skupin dostupnosti Always On není podporovaná.
**Podporované služby SQL** | Podporuje se jenom SQL Server databázový stroj. <br/> Zjišťování služby SQL Server Reporting Services (SSRS), služba SSIS (SQL Server Integration Services) (SSIS) a Služba Analysis Services serveru SQL (SSAS) není podporováno.

> [!Note]
> Azure Migrate zašifruje komunikaci mezi Azure Migrate zařízením a instancemi SQL Server zdrojového kódu (s vlastností šifrovat připojení nastavenou na hodnotu TRUE). Tato připojení jsou šifrovaná pomocí [**TrustServerCertificate**](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) (nastavená na hodnotu true); Transportní vrstva bude používat protokol SSL k šifrování kanálu a obejít řetěz certifikátů k ověření vztahu důvěryhodnosti. Server zařízení musí být nastavený tak, aby [**důvěřoval kořenové autoritě certifikátu**](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).<br/>
Pokud se při spuštění na serveru nezřídí žádný certifikát, SQL Server vygeneruje certifikát podepsaný svým držitelem, který se používá k šifrování přihlašovacích paketů. [**Další informace**](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)

## <a name="dependency-analysis-requirements-agentless"></a>Požadavky na analýzu závislostí (bez agentů)

[Analýza závislostí](concepts-dependency-visualization.md) vám pomůže identifikovat závislosti mezi místními servery, které chcete vyhodnotit a migrovat do Azure. Tabulka shrnuje požadavky na nastavení analýzy závislostí bez agentů.

**Podpora** | **Podrobnosti**
--- | --- 
**Podporované servery** | Aktuálně se podporuje jenom pro servery ve vašem prostředí VMware.
**Windows servery** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64 bitů).<br/>Microsoft Windows Server 2008 (32).
**Servery s Linuxem** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14,04, 16,04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.<br/> SUSE Linux Enterprise Server 11 a novější.
**Požadavky na server** | Nástroje VMware (novější než 10.2.0) musí být nainstalované a spuštěné na serverech, které chcete analyzovat.<br/><br/> Servery musí mít nainstalované prostředí PowerShell verze 2,0 nebo novější.
**Metoda zjišťování** |  Informace o závislostech mezi servery se shromažďují z vCenter Server pomocí nástrojů VMware nainstalovaných na serveru. Zařízení shromažďuje informace z vCenter Server pomocí rozhraní API vSphere. Na serveru není nainstalovaný žádný agent a zařízení se nepřipojuje přímo k serverům. V uvedeném pořadí by měla být povolená a dostupná služba WMI a SSH v serverech se systémem Windows a Linux.
**účet vCenter** | Účet jen pro čtení používaný Azure Migrate for Assessment vyžaduje oprávnění povolená pro **operace Virtual Machines > hostů**.
**Oprávnění pro Windows Server** |  Uživatelský účet (místní nebo doména) s oprávněními správce na serverech.
**Účet Linux** | Kořenový uživatelský účet nebo účet s těmito oprávněními pro soubory/bin/netstat a/bin/ls: CAP_DAC_READ_SEARCH a CAP_SYS_PTRACE.<br/><br/> Tyto možnosti nastavte pomocí následujících příkazů: <br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/LS<br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/netstat
**Přístup k portu** | Zařízení Azure Migrate musí být schopné se připojit k portu TCP 443 na hostitelích ESXi, na kterých běží servery, jejichž závislosti chcete zjistit. VCenter Server vrátí připojení hostitele ESXi pro stažení souboru obsahujícího data závislostí.


## <a name="dependency-analysis-requirements-agent-based"></a>Požadavky na analýzu závislostí (založené na agentovi)

[Analýza závislostí](concepts-dependency-visualization.md) vám pomůže identifikovat závislosti mezi místními servery, které chcete vyhodnotit a migrovat do Azure. Tabulka shrnuje požadavky na nastavení analýzy závislostí založené na agentech.

**Požadavek** | **Podrobnosti** 
--- | --- 
**Před nasazením** | Měli byste mít projekt na místě, a to pomocí nástroje Azure Migrate: Discovery and Assessment Tool přidaný do projektu.<br/><br/>  Vizualizace závislostí nasadíte po nastavení zařízení Azure Migrate pro zjišťování vašich místních serverů.<br/><br/> [Naučte](create-manage-projects.md) se, jak poprvé vytvořit projekt.<br/> [Naučte](how-to-assess.md) se, jak přidat nástroj pro zjišťování a vyhodnocení do existujícího projektu.<br/> Naučte se, jak nastavit zařízení Azure Migrate pro posouzení [technologie Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)nebo fyzických serverů.
**Podporované servery** | Podporováno pro všechny servery ve vašem místním prostředí.
**Log Analytics** | Azure Migrate používá řešení [Service map](../azure-monitor/insights/service-map.md) v [protokolech Azure monitor](../azure-monitor/log-query/log-query-overview.md) pro vizualizaci závislostí.<br/><br/> K projektu přidružíte nový nebo existující Log Analytics pracovní prostor. Pracovní prostor pro projekt nelze po přidání změnit. <br/><br/> Pracovní prostor musí být ve stejném předplatném jako projekt.<br/><br/> Pracovní prostor se musí nacházet v oblastech Východní USA, jihovýchodní Asie nebo Západní Evropa. Pracovní prostory v jiných oblastech nelze přidružit k projektu.<br/><br/> Pracovní prostor musí být v oblasti, ve které [je podporovaná Service map](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> V Log Analytics je pracovní prostor přidružený k Azure Migrate označený klíčem projektu a názvem projektu.
**Vyžadovaná agenti** | Na každém serveru, který chcete analyzovat, nainstalujte následující agenty:<br/><br/> [Microsoft Monitoring Agent (MMA)](../azure-monitor/platform/agent-windows.md).<br/> [Agent závislostí](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Pokud nejsou místní servery připojené k Internetu, musíte na ně stáhnout a nainstalovat bránu Log Analytics.<br/><br/> Přečtěte si další informace o instalaci [agenta závislostí](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) a [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Pracovní prostor Log Analytics** | Pracovní prostor musí být ve stejném předplatném jako projekt.<br/><br/> Azure Migrate podporuje pracovní prostory, které jsou umístěné v oblastech Východní USA, jihovýchodní Asie a Západní Evropa.<br/><br/>  Pracovní prostor musí být v oblasti, ve které [je podporovaná Service map](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> Pracovní prostor pro projekt nelze po přidání změnit.
**Náklady** | V řešení Service Map se neúčtují žádné poplatky za prvních 180 dní (od dne, kdy přiřadíte pracovní prostor Log Analytics k projektu)/<br/><br/> Po uplynutí 180 dnů se začnou účtovat standardní poplatky za Log Analytics.<br/><br/> Použití jiného řešení než Service Map v přidruženém pracovním prostoru Log Analytics bude účtovat [standardní poplatky](https://azure.microsoft.com/pricing/details/log-analytics/) za Log Analytics.<br/><br/> Po odstranění projektu se pracovní prostor neodstraní spolu s ním. Po odstranění projektu Service Map využití není volné a každý uzel se bude účtovat podle placené úrovně Log Analytics pracovního prostoru/<br/><br/>Pokud máte projekty, které jste vytvořili před Azure Migrate všeobecné dostupnosti (GA-28 února 2018), mohly by vám být účtovány další Service Map poplatky. Aby se zajistila platba jenom po 180 dnech, doporučujeme vytvořit nový projekt, protože stávající pracovní prostory před GAm jsou stále Fakturovatelné.
**správy** | Při registraci agentů do pracovního prostoru použijete ID a klíč poskytnutý projektem.<br/><br/> Pracovní prostor Log Analytics můžete použít mimo Azure Migrate.<br/><br/> Pokud odstraníte přidružený projekt, pracovní prostor se automaticky neodstraní. [Odstraňte ji ručně](../azure-monitor/platform/manage-access.md).<br/><br/> Neodstraňujte pracovní prostor vytvořený pomocí Azure Migrate, pokud projekt neodstraníte. Pokud to uděláte, funkce vizualizace závislosti nebude fungovat podle očekávání.
**Připojení k internetu** | Pokud nejsou servery připojené k Internetu, musíte na ně nainstalovat bránu Log Analytics.
**Azure Government** | Analýza závislostí založená na agentech není podporována.


## <a name="next-steps"></a>Další kroky

- [Projděte si](best-practices-assessment.md) osvědčené postupy pro vytváření hodnocení.
- [Příprava na vyhodnocení VMware](./tutorial-discover-vmware.md) .