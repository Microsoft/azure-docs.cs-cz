---
title: Podpora hodnocení fyzického serveru v Azure Migrate
description: Informace o podpoře hodnocení fyzického serveru pomocí azure migrate server assessment
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: f6c70ac2517a29497f4f11073e4b16067bab9576
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336900"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Matice podpory pro posouzení fyzického serveru 

Tento článek shrnuje požadavky na požadavky na požadavky na požadavky na podporu pro posouzení fyzických serverů v rámci přípravy na migraci do Azure. Pokud chcete migrovat fyzické servery do Azure, zkontrolujte [matici podpory migrace](migrate-support-matrix-physical-migration.md).


Fyzické servery můžete vyhodnocovat pomocí nástroje [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool) Vytvoříte projekt Migrace Azure a pak přidáte nástroj do projektu. Po přidání nástroje nasadíte [zařízení Azure Migrate](migrate-appliance.md). Zařízení průběžně zjišťuje místní počítače a odesílá metadata a údaje o výkonu počítače do Azure. Po zjišťování počítače shromáždíte zjištěné počítače do skupin a spustíte hodnocení pro skupinu.


## <a name="limitations"></a>Omezení

**Podpora** | **Podrobnosti**
--- | ---
**Hodnotící limity** | Můžete zjistit a vyhodnotit až 35 000 fyzických serverů v jednom [projektu Migrace Azure](migrate-support-matrix.md#azure-migrate-projects).
**Limity projektu** | V předplaceně Azure můžete vytvořit více projektů. Kromě fyzických serverů může projekt zahrnovat virtuální zařízení VMware a virtuální chod y Hyper-V až do výše limitů hodnocení pro každý z nich.
**Objev** | Zařízení Azure Migrate může zjistit až 250 fyzických serverů.
**Posouzení** | V jedné skupině můžete přidat až 35 000 počítačů.<br/><br/> V jednom hodnocení můžete posoudit až 35 000 strojů.

[Přečtěte si další informace](concepts-assessment-calculation.md) o hodnoceních.

## <a name="physical-server-requirements"></a>Požadavky na fyzický server

| **Podpora**                | **Podrobnosti**               
| :-------------------       | :------------------- |
| **Fyzické nasazení serveru**       | Fyzický server může být samostatný nebo nasazený v clusteru. |
| **Oprávnění**           | **Windows:** Potřebujete místní nebo doménový uživatelský účet na všech serverech se systémem Windows, které chcete zjistit. Uživatelský účet by měl být přidán do těchto skupin: Uživatelé vzdálené plochy, Uživatelé sledování výkonu a Uživatelé protokolu výkonu. <br/><br/> **Linux:** Potřebujete kořenový účet na serverech Linuxu, které chcete objevit. |
| **Operační systém** | Všechny operační systémy [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) a [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) jsou podporovány s výjimkou systémů Windows Server 2003 a SUSE Linux.|


## <a name="azure-migrate-appliance-requirements"></a>Požadavky zařízení Azure Migrate

Azure Migrate používá [zařízení Azure Migrate](migrate-appliance.md) pro zjišťování a hodnocení. Zařízení pro fyzické servery můžete spustit na virtuálním počítači nebo fyzickém počítači. Nastavíte ji pomocí skriptu PowerShellu, který stáhnete z webu Azure Portal.

- Seznamte se s [požadavky na zařízení](migrate-appliance.md#appliance---physical) pro fyzické servery.
- Přečtěte si o [adresách URL,](migrate-appliance.md#url-access) ke kterým zařízení potřebuje přístup.

## <a name="port-access"></a>Přístup k portu

Následující tabulka shrnuje požadavky na portpro posouzení.

**Zařízení** | **Připojení**
--- | ---
**Přístroj** | Příchozí připojení na portu TCP 3389, která umožňují připojení ke vzdálené ploše k zařízení.<br/><br/> Příchozí připojení na portu 44368, pro vzdálený přístup k aplikaci pro správu zařízení pomocí adresy URL:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Odchozí připojení na portech 443 (HTTPS) pro odesílání metadat zjišťování a výkonu do Migrace Azure.
**Fyzické servery** | **Windows:** Příchozí připojení na portech WinRM 5985 (HTTP) a 5986 (HTTPS) pro vyžádat metadata konfigurace a výkonu ze serverů windows. <br/><br/> **Linux:**  Příchozí připojení na portu 22 (UDP), chcete-li stáhnout metadata konfigurace a výkonu ze serverů Linux. |

## <a name="agentless-dependency-analysis-requirements"></a>Požadavky na analýzu závislostí bez agenta

[Analýza závislostí](concepts-dependency-visualization.md) vám pomůže identifikovat závislosti mezi místními počítači, které chcete posoudit a migrovat do Azure. Tabulka shrnuje požadavky na nastavení analýzy závislostí bez agenta. 


**Požadavek** | **Podrobnosti** 
--- | --- 
**Před nasazením** | Měli byste mít projekt Migrace Azure na místě, s nástrojem Azure Migrate: Server Assessment přidané do projektu.<br/><br/>  Vizualizaci závislostí nasadíte po nastavení zařízení Azure Migrate, abyste zjistili místní počítače VMWare.<br/><br/> [Přečtěte si, jak](create-manage-projects.md) vytvořit projekt poprvé.<br/> [Přečtěte si, jak](how-to-assess.md) přidat nástroj pro hodnocení do existujícího projektu.<br/> [Přečtěte si, jak](how-to-set-up-appliance-vmware.md) nastavit zařízení Azure Migrate pro vyhodnocení virtuálních počítačů VMware.
**Podpora virtuálních her** | Momentálně podporované pouze pro virtuální měny VMware.
**Virtuální počítače s Windows** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64bitový).
**Účet systému Windows** |  Pro analýzu závislostí zařízení Azure Migrate potřebuje pro přístup k virtuálním účtům Windows účet místního správce nebo správce domény.
**Virtuální počítače s Linuxem** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14.04, 16.04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> Centos 5, 6, 7.
**Linuxový účet** | Pro analýzu závislostí na linuxových počítačích potřebuje zařízení Azure Migrate uživatelský účet s oprávněním Root.<br/><br/> Uživatelský účet obvykle potřebuje tato oprávnění pro soubory /bin/netstat a /bin/ls: CAP_DAC_READ_SEARCH a CAP_SYS_PTRACE.
**Požadované agenty** | Na počítačích, které chcete analyzovat, není vyžadován žádný agent.
**Nástroje VMware** |  Nástroje VMware (novější než 10.2) musí být nainstalovány a spuštěny na každém virtuálním počítači, který chcete analyzovat.
**vCenter Server** |  Vizualizace závislostí vyžaduje účet serveru vCenter s přístupem jen pro čtení a oprávněními povolenými pro virtuální počítače > operace hosta. **ESXi hostitelé:** Na ESXi hostitelů se spuštěných virtuálních počítačích, které chcete analyzovat, zařízení Azure Migrate musí být možné připojení k portu TCP 443.

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

[Připravte se na posouzení fyzického serveru](tutorial-prepare-physical.md).
