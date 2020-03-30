---
title: Podpora hodnocení VMware v Azure Migrate
description: Přečtěte si o podpoře pro vyhodnocení virtuálních počítačích VMware pomocí Azure Migrate Server Assessment.
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: e0172656d06075f89a7c3a06e8d4e9be94e6f5d0
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389303"
---
# <a name="support-matrix-for-vmware-assessment"></a>Matice podpory pro hodnocení vmware 

Tento článek shrnuje požadavky na požadavky na požadavky na požadavky na požadavky na podporu při posuzování virtuálních počítačích VMware pro migraci do Azure pomocí nástroje Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool). Pokud chcete migrovat virtuální počítače VMware do Azure, zkontrolujte [matici podpory migrace](migrate-support-matrix-vmware-migration.md).

Chcete-li vyhodnotit virtuální počítače VMware, vytvořte projekt Migrace Azure a pak do projektu přidejte nástroj vyhodnocení serveru. Po přidání nástroje nasadíte [zařízení Azure Migrate](migrate-appliance.md). Zařízení průběžně zjišťuje místní počítače a odesílá metadata a údaje o výkonu počítače do Azure. Po dokončení zjišťování shromáždíte zjištěné počítače do skupin a spustíte hodnocení pro skupinu.

## <a name="limitations"></a>Omezení

**Podpora** | **Podrobnosti**
--- | ---
**Limity projektu** | V předplaceně Azure můžete vytvořit více projektů.<br/><br/> Můžete objevit a vyhodnotit až 35 000 virtuálních virtuálních měn VMware v jednom [projektu](migrate-support-matrix.md#azure-migrate-projects). Projekt může také zahrnovat fyzické servery a virtuální chod hyper-V až do limitů hodnocení pro každý.
**Objev** | Zařízení Migrace Azure může zjistit až 10 000 virtuálních počítačů VMware na serveru vCenter.
**Posouzení** | V jedné skupině můžete přidat až 35 000 počítačů.<br/><br/> Můžete vyhodnotit až 35 000 virtuálních můe v rámci jednoho hodnocení.

[Přečtěte si další informace](concepts-assessment-calculation.md) o hodnoceních.


## <a name="application-discovery"></a>Zjišťování aplikací

Kromě zjišťování počítačů může server Assessment zjišťovat aplikace, role a funkce spuštěné na počítačích. Zjišťování inventáře aplikací vám umožní identifikovat a naplánovat cestu migrace přizpůsobenou místním úlohám. 

**Podpora** | **Podrobnosti**
--- | ---
**Podporované stroje** | Zjišťování aplikací je aktuálně podporované pouze pro virtuální majech VMware.
**Objev** | Zjišťování aplikací je bez agenta. Používá pověření pro hosta počítače a vzdáleně přistupuje k počítačům pomocí volání WMI a SSH.
**Podpora virtuálních her** | Zjišťování aplikací je podporováno pro všechny verze Windows a Linux.
**pověření aplikace vCenter** | Zjišťování aplikací vyžaduje účet vCenter Serveru s přístupem jen pro čtení a oprávněními povolenými pro virtuální počítače > operace hosta.
**Pověření virtuálních ms** | Zjišťování aplikací v současné době podporuje použití jednoho pověření pro všechny servery Windows a jedno pověření pro všechny servery Linux.<br/><br/> Vytvoříte uživatelský účet hosta pro virtuální počítače se systémem Windows a běžný/běžný uživatelský účet (přístup bez sudo) pro všechny virtuální počítače s Linuxem.
**Nástroje VMware** | Nástroje VMware musí být nainstalované a spuštěné na virtuálních počítačích, které chcete zjistit. <br/> Verze nástrojů VMware musí být pozdější než 10.2.0.
**PowerShell** | Virtuální aplikace musí mít nainstalovanou powershellovou verzi 2.0 nebo novější.
**Přístup k portu** | Na hostitelích ESXi se spuštěným virtuálním počítačem, které chcete zjistit, musí být zařízení Azure Migrate schopno se připojit k portu TCP 443.
**Limity** | Pro zjišťování aplikací můžete zjistit až 10000 virtuálních počítačů na každém zařízení Migrace Azure.



## <a name="vmware-requirements"></a>Požadavky na vmware

**Vmware** | **Podrobnosti**
--- | ---
**Virtuální počítače VMware** | Hodnocení je podporováno pro všechny operační systémy Windows a Linux.
**vCenter Server** | Počítače, které chcete objevit a vyhodnotit, musí být spravovány serverem vCenter Server verze 5.5, 6.0, 6.5 nebo 6.7.
**Oprávnění (hodnocení)** | účet jen pro čtení serveru vCenter.
**Oprávnění (zjišťování aplikací)** | Účet serveru vCenter s přístupem jen pro čtení a oprávněními povolenými pro **virtuální počítače > operace hosta**.
**Oprávnění (vizualizace závislostí)** | Účet serveru Center s přístupem jen pro čtení a oprávněními povolenými pro**operace hosta** **virtuálních počítačů** > .


## <a name="azure-migrate-appliance-requirements"></a>Požadavky zařízení Azure Migrate

Azure Migrate používá [zařízení Azure Migrate](migrate-appliance.md) pro zjišťování a hodnocení. Zařízení můžete nasadit jako virtuální hod VMWare pomocí šablony OVA, importované na server vCenter nebo pomocí [skriptu prostředí PowerShell](deploy-appliance-script.md).

- Seznamte se s [požadavky na zařízení](migrate-appliance.md#appliance---vmware) pro společnost VMware.
- Přečtěte si o [adresách URL,](migrate-appliance.md#url-access) ke kterým zařízení potřebuje přístup.

## <a name="port-access"></a>Přístup k portu

**Zařízení** | **Připojení**
--- | ---
Přístroj | Příchozí připojení na portu TCP 3389, která umožňují připojení ke vzdálené ploše k zařízení.<br/><br/> Příchozí připojení na portu 44368 pro vzdálený přístup k aplikaci pro správu zařízení pomocí adresy URL:```https://<appliance-ip-or-name>:44368``` <br/><br/>Odchozí připojení na portu 443 (HTTPS) pro odesílání metadat zjišťování a výkonu do Migrace Azure.
Server vCenter | Příchozí připojení na portu TCP 443, aby zařízení mohlo shromažďovat metadata konfigurace a výkonu pro hodnocení. <br/><br/> Zařízení se ve výchozím nastavení připojí k vCenter na portu 443. Pokud server vCenter naslouchá na jiném portu, můžete port upravit při nastavení zjišťování.
Hostitelé ESXi (analýza zjišťování/závislostí bez agentů) | Pokud chcete provést [zjišťování aplikací](how-to-discover-applications.md) nebo [analýzu závislostí bez agenta](concepts-dependency-visualization.md#agentless-analysis), pak se zařízení připojí k hostitelům ESXi na portu TCP 443, aby zjišťovalo aplikace, až po vizualizaci závislostí bez agenta na virtuálních počítačích.

## <a name="agentless-dependency-analysis-requirements"></a>Požadavky na analýzu závislostí bez agenta

[Analýza závislostí](concepts-dependency-visualization.md) vám pomůže identifikovat závislosti mezi místními počítači, které chcete posoudit a migrovat do Azure. Tabulka shrnuje požadavky na nastavení analýzy závislostí bez agenta. 

**Požadavek** | **Podrobnosti**
--- | --- 
**Před nasazením** | Měli byste mít projekt Migrace Azure na místě, s nástrojem hodnocení serveru přidán do projektu.<br/><br/>  Vizualizaci závislostí nasadíte po nastavení zařízení Azure Migrate, abyste zjistili místní počítače VMWare.<br/><br/> [Přečtěte si, jak](create-manage-projects.md) vytvořit projekt poprvé.<br/> [Přečtěte si, jak](how-to-assess.md) přidat nástroj pro hodnocení do existujícího projektu.<br/> [Přečtěte si, jak](how-to-set-up-appliance-vmware.md) nastavit zařízení Azure Migrate pro vyhodnocení virtuálních počítačů VMware.
**Podpora virtuálních her** | Momentálně podporované pouze pro virtuální měny VMware.
**Virtuální počítače s Windows** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64bitový).
**Účet systému Windows** |  Pro analýzu závislostí zařízení Azure Migrate potřebuje pro přístup k virtuálním účtům Windows účet místního správce nebo správce domény.
**Virtuální počítače s Linuxem** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14.04, 16.04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> Centos 5, 6, 7.
**Linuxový účet** | Pro analýzu závislostí na linuxových počítačích potřebuje zařízení Azure Migrate uživatelský účet s oprávněním Root.<br/><br/> Uživatelský účet obvykle potřebuje tato oprávnění pro soubory /bin/netstat a /bin/ls: CAP_DAC_READ_SEARCH a CAP_SYS_PTRACE.
**Požadované agenty** | Na počítačích, které chcete analyzovat, není vyžadován žádný agent.
**Nástroje VMware** | Nástroje VMware (novější než 10.2) musí být nainstalovány a spuštěny na každém virtuálním počítači, který chcete analyzovat.
**Pověření serveru vCenter** | Vizualizace závislostí vyžaduje účet serveru vCenter s přístupem jen pro čtení a oprávněními povolenými pro virtuální počítače > operace hosta. 
**PowerShell** | Virtuální aplikace musí mít nainstalovanou powershellu verze 2.0 nebo vyšší.
**Přístup k portu** | Na hostitelích ESXi se spuštěným virtuálním počítačem, které chcete analyzovat, musí být zařízení Azure Migrate schopno se připojit k portu TCP 443.

## <a name="agent-based-dependency-analysis-requirements"></a>Požadavky na analýzu závislostí na základě agenta

[Analýza závislostí](concepts-dependency-visualization.md) vám pomůže identifikovat závislosti mezi místními počítači, které chcete posoudit a migrovat do Azure. Tabulka shrnuje požadavky na nastavení analýzy závislostí založené na agentovi. 

**Požadavek** | **Podrobnosti** 
--- | --- 
**Před nasazením** | Měli byste mít projekt Migrace Azure na místě, s nástrojem Azure Migrate: Server Assessment přidané do projektu.<br/><br/>  Vizualizace závislostí nasadíte po nastavení zařízení Azure Migrate ke zjištění místních počítačů.<br/><br/> [Přečtěte si, jak](create-manage-projects.md) vytvořit projekt poprvé.<br/> [Přečtěte si, jak](how-to-assess.md) přidat nástroj pro hodnocení do existujícího projektu.<br/> Zjistěte, jak nastavit zařízení Azure Migrate pro posouzení [serverů Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)nebo fyzických serverů.
**Azure Government** | Vizualizace závislostí není ve službě Azure Government dostupná.
**Log Analytics** | Azure Migrate používá řešení [map služeb](../operations-management-suite/operations-management-suite-service-map.md) v [protokolech Azure Monitor](../log-analytics/log-analytics-overview.md) u vizualizace závislostí.<br/><br/> Přidružíte nový nebo existující pracovní prostor Log Analytics k projektu Migrace Azure. Pracovní prostor pro projekt Migrace Azure nelze změnit po jeho přidání. <br/><br/> Pracovní prostor musí být ve stejném předplatném jako projekt Migrace Azure.<br/><br/> Pracovní prostor musí být umístěn v oblastech východní CH VUSA, Jihovýchodní Asie nebo Západní Evropa. Pracovní prostory v jiných oblastech nelze přidružit k projektu.<br/><br/> Pracovní prostor musí být v oblasti, ve které [je podporována mapa služeb](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> V Log Analytics je pracovní prostor přidružený k migraci Azure označen klíčem Projektu migrace a názvem projektu.
**Požadované agenty** | Na každém počítači, který chcete analyzovat, nainstalujte následující agenty:<br/><br/> [Agent monitorování společnosti Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> [Agent závislostí](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Pokud místní počítače nejsou připojené k internetu, musíte si na ně stáhnout a nainstalovat bránu Log Analytics.<br/><br/> Další informace o instalaci [agenta závislostí](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) a [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Pracovní prostor Log Analytics** | Pracovní prostor musí být ve stejném předplatném jako projekt Migrace Azure.<br/><br/> Azure Migrate podporuje pracovní prostory s bydlištěm v oblastech východní USA, jihovýchodní Asie a západní Evropy.<br/><br/>  Pracovní prostor musí být v oblasti, ve které [je podporována mapa služeb](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> Pracovní prostor pro projekt Migrace Azure nelze změnit po jeho přidání.
**Náklady** | Řešení mapy služeb neúčtuje žádné poplatky za prvních 180 dní (ode dne, kdy přidružíte pracovní prostor Log Analytics k projektu Migrace Azure)/<br/><br/> Po uplynutí 180 dnů se začnou účtovat standardní poplatky za Log Analytics.<br/><br/> Použití jakéhokoli jiného řešení než mapy služeb v přidruženém pracovním prostoru Log Analytics bude účtovány [standardní poplatky za](https://azure.microsoft.com/pricing/details/log-analytics/) log analytics.<br/><br/> Když se odstraní projekt Migrace Azure, pracovní prostor se neodstraní spolu s ním. Po odstranění projektu není využití mapy služeb zdarma a každý uzel se bude účtovat podle placené úrovně pracovního prostoru Log Analytics.<br/><br/>Pokud máte projekty, které jste vytvořili před Azure Migrate obecné dostupnosti (GA- 28 Únor 2018), může vzniknout další poplatky mapy služeb. Chcete-li zajistit platbu pouze po 180 dnech, doporučujeme vytvořit nový projekt, protože stávající pracovní prostory před GA jsou stále zpoplatněny.
**správy** | Když zaregistrujete agenty do pracovního prostoru, použijete ID a klíč poskytované projektem Migrace Azure.<br/><br/> Pracovní prostor Log Analytics můžete použít mimo Azure Migrate.<br/><br/> Pokud odstraníte přidružený projekt Migrace Azure, pracovní prostor se neodstraní automaticky. [Odstraňte jej ručně](../azure-monitor/platform/manage-access.md).<br/><br/> Neodstraňujte pracovní prostor vytvořený Azure Migrate, pokud neodstraníte projekt Migrace Azure. Pokud tak učiníte, funkce vizualizace závislostí nebude fungovat podle očekávání.
**Připojení k Internetu** | Pokud počítače nejsou připojené k internetu, je třeba nainstalovat bránu Log Analytics na ně.


## <a name="next-steps"></a>Další kroky

- [Projděte si](best-practices-assessment.md) osvědčené postupy pro vytváření hodnocení.
- [Připravte se na hodnocení společnosti VMware.](tutorial-prepare-vmware.md)
