---
title: Podpora hodnocení Technologie Hyper-V v Azure Migrate
description: Informace o podpoře hodnocení Hyper-V pomocí azure migrate server assessment
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 990d5026d9621c144c31635fabac4416eb9d20e6
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538167"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Matice podpory pro hodnocení Hyper-V

Tento článek shrnuje požadavky na požadavky na požadavky na požadavky na podporu při posuzování virtuálních počítačů Hyper-V pro migraci do Azure pomocí nástroje [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool) Pokud chcete migrovat hypervirtuální počítače do Azure, zkontrolujte [matici podpory migrace](migrate-support-matrix-hyper-v-migration.md).

Chcete-li nastavit hodnocení virtuálních počítačů Hyper-V, vytvořte projekt Migrace Azure a přidejte do projektu nástroj vyhodnocení serveru. Po přidání nástroje nasadíte [zařízení Azure Migrate](migrate-appliance.md). Zařízení průběžně zjišťuje místní počítače a odesílá metadata a údaje o výkonu počítače do Azure. Po dokončení zjišťování shromáždíte zjištěné počítače do skupin a spustíte hodnocení pro skupinu.


## <a name="limitations"></a>Omezení

**Podpora** | **Podrobnosti**
--- | ---
**Hodnotící limity** | Můžete zjistit a vyhodnotit až 35 000 virtuálních počítačů Hyper-V v jednom [projektu Migrace Azure](migrate-support-matrix.md#azure-migrate-projects).
**Limity projektu** | V předplaceně Azure můžete vytvořit více projektů. Kromě virtuálních měn Hyper-V může projekt zahrnovat virtuální zařízení VMware a fyzické servery až do limitů hodnocení pro každý z nich.
**Objev** | Zařízení Azure Migrate může zjistit až 5000 virtuálních počítačů Hyper-V.<br/><br/> Zařízení se může připojit až k 300 hostitelům Hyper-V.
**Posouzení** | V jedné skupině můžete přidat až 35 000 počítačů.<br/><br/> Můžete posoudit až 35 000 virtuálních< virtuálních mv v rámci jednoho hodnocení pro skupinu.

[Přečtěte si další informace](concepts-assessment-calculation.md) o hodnoceních.



## <a name="hyper-v-host-requirements"></a>Požadavky hostitele Hyper-V

| **Podpora**                | **Podrobnosti**               
| :-------------------       | :------------------- |
| **Hostitel Hyper-V**       | Hostitel Hyper-V může být samostatný nebo nasazený v clusteru.<br/><br/> Hostitel Hyper-V může spustit Windows Server 2019, Windows Server 2016 nebo Windows Server 2012 R2.<br/> Virtuální počítače na hostitelích Hyper-V s Windows Serverem 2012 hodnotit nemůžete.
| **Oprávnění**           | Potřebujete oprávnění správce pro hostitele Hyper-V. <br/> Pokud nechcete přiřazovat oprávnění správce, vytvořte uživatelský účet místní ho nebo domény a přidejte uživatelský účet do těchto skupin – uživatelé vzdálené správy, správci technologie Hyper-V a uživatelé sledování výkonu. |
| **Vzdálená komunikace PowerShellu**   | [Vzdálené komunikace prostředí PowerShell](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/enable-psremoting?view=powershell-7) musí být povolena na každém hostiteli Hyper-V. |
| **Replika technologie Hyper-V**       | Pokud používáte repliku Hyper-V (nebo máte více virtuálních počítačů se stejnými identifikátory virtuálních zařízení) a zjistíte původní i replikované virtuální počítače pomocí Migrace Azure, hodnocení generované azure migrate nemusí být přesné. |


## <a name="hyper-v-vm-requirements"></a>Požadavky na virtuální vana Hyper-V

| **Podpora**                  | **Podrobnosti**               
| :----------------------------- | :------------------- |
| **Operační systém** | Všechny operační systémy [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) a [Linux.](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) |
| **Služba Integration Services**       | [Služba Hyper-V Integration Services](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) musí být spuštěna na virtuálních počítačích, které vyhodnocujete, aby bylo možné zachytit informace o operačním systému. |


## <a name="azure-migrate-appliance-requirements"></a>Požadavky zařízení Azure Migrate

Azure Migrate používá [zařízení Azure Migrate](migrate-appliance.md) pro zjišťování a hodnocení. Zařízení můžete nasadit pomocí komprimovaného virtuálního pevného disku Hyper-V,, který stáhnete z portálu, nebo pomocí [skriptu prostředí PowerShell](deploy-appliance-script.md).

- Seznamte se s [požadavky na zařízení](migrate-appliance.md#appliance---hyper-v) pro technologie Hyper-V.
- Přečtěte si o adresách URL, ke kterým zařízení potřebuje přístup ve [veřejných](migrate-appliance.md#public-cloud-urls) a [vládních](migrate-appliance.md#government-cloud-urls) cloudech.
- Ve službě Azure Government je nutné nasadit zařízení pomocí skriptu.

## <a name="port-access"></a>Přístup k portu

Následující tabulka shrnuje požadavky na portpro posouzení.

**Zařízení** | **Připojení**
--- | ---
**Přístroj** | Příchozí připojení na portu TCP 3389, která umožňují připojení ke vzdálené ploše k zařízení.<br/><br/> Příchozí připojení na portu 44368 pro vzdálený přístup k aplikaci pro správu zařízení pomocí adresy URL:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Odchozí připojení na portech 443 (HTTPS) pro odesílání metadat zjišťování a výkonu do Migrace Azure.
**Hostitel/cluster Hyper-V** | Příchozí připojení na portech WinRM 5985 (HTTP) a 5986 (HTTPS) pro vytahování metadat a dat o výkonu pro virtuální počítači Hyper-V pomocí relace společného informačního modelu (CIM).

## <a name="agent-based-dependency-analysis-requirements"></a>Požadavky na analýzu závislostí na základě agenta

[Analýza závislostí](concepts-dependency-visualization.md) vám pomůže identifikovat závislosti mezi místními počítači, které chcete posoudit a migrovat do Azure. Tabulka shrnuje požadavky na nastavení analýzy závislostí založené na agentovi. Technologie Hyper-V aktuálně podporuje pouze vizualizaci závislostí založenou na agentovi. 

**Požadavek** | **Podrobnosti** 
--- | --- 
**Před nasazením** | Měli byste mít projekt Migrace Azure na místě, s nástrojem hodnocení serveru přidán do projektu.<br/><br/>  Vizualizace závislostí nasadíte po nastavení zařízení Azure Migrate ke zjištění místních počítačů.<br/><br/> [Přečtěte si, jak](create-manage-projects.md) vytvořit projekt poprvé.<br/> [Přečtěte si, jak](how-to-assess.md) přidat nástroj pro hodnocení do existujícího projektu.<br/> Přečtěte si, jak nastavit zařízení Azure Migrate pro vyhodnocení [virtuálních počítačích Hyper-V](how-to-set-up-appliance-hyper-v.md).
**Azure Government** | Vizualizace závislostí není ve službě Azure Government dostupná.
**Log Analytics** | Azure Migrate používá řešení [map služeb](../operations-management-suite/operations-management-suite-service-map.md) v [protokolech Azure Monitor](../log-analytics/log-analytics-overview.md) u vizualizace závislostí.<br/><br/> Přidružíte nový nebo existující pracovní prostor Log Analytics k projektu Migrace Azure. Pracovní prostor pro projekt Migrace Azure nelze změnit po jeho přidání. <br/><br/> Pracovní prostor musí být ve stejném předplatném jako projekt Migrace Azure.<br/><br/> Pracovní prostor musí být umístěn v oblastech východní CH VUSA, Jihovýchodní Asie nebo Západní Evropa. Pracovní prostory v jiných oblastech nelze přidružit k projektu.<br/><br/> Pracovní prostor musí být v oblasti, ve které [je podporována mapa služeb](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> V Log Analytics je pracovní prostor přidružený k migraci Azure označen klíčem Projektu migrace a názvem projektu.
**Požadované agenty** | Na každém počítači, který chcete analyzovat, nainstalujte následující agenty:<br/><br/> [Agent monitorování společnosti Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> [Agent závislostí](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Pokud místní počítače nejsou připojené k internetu, musíte si na ně stáhnout a nainstalovat bránu Log Analytics.<br/><br/> Další informace o instalaci [agenta závislostí](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) a [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Pracovní prostor služby Log Analytics** | Pracovní prostor musí být ve stejném předplatném jako projekt Migrace Azure.<br/><br/> Azure Migrate podporuje pracovní prostory s bydlištěm v oblastech východní USA, jihovýchodní Asie a západní Evropy.<br/><br/>  Pracovní prostor musí být v oblasti, ve které [je podporována mapa služeb](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> Pracovní prostor pro projekt Migrace Azure nelze změnit po jeho přidání.
**Náklady** | Řešení mapy služeb neúčtuje žádné poplatky za prvních 180 dní (ode dne, kdy přidružíte pracovní prostor Log Analytics k projektu Migrace Azure)/<br/><br/> Po uplynutí 180 dnů se začnou účtovat standardní poplatky za Log Analytics.<br/><br/> Použití jakéhokoli jiného řešení než mapy služeb v přidruženém pracovním prostoru Log Analytics bude účtovány [standardní poplatky za](https://azure.microsoft.com/pricing/details/log-analytics/) log analytics.<br/><br/> Když se odstraní projekt Migrace Azure, pracovní prostor se neodstraní spolu s ním. Po odstranění projektu není využití mapy služeb zdarma a každý uzel se bude účtovat podle placené úrovně pracovního prostoru Log Analytics.<br/><br/>Pokud máte projekty, které jste vytvořili před Azure Migrate obecné dostupnosti (GA- 28 Únor 2018), může vzniknout další poplatky mapy služeb. Chcete-li zajistit platbu pouze po 180 dnech, doporučujeme vytvořit nový projekt, protože stávající pracovní prostory před GA jsou stále zpoplatněny.
**správy** | Když zaregistrujete agenty do pracovního prostoru, použijete ID a klíč poskytované projektem Migrace Azure.<br/><br/> Pracovní prostor Log Analytics můžete použít mimo Azure Migrate.<br/><br/> Pokud odstraníte přidružený projekt Migrace Azure, pracovní prostor se neodstraní automaticky. [Odstraňte jej ručně](../azure-monitor/platform/manage-access.md).<br/><br/> Neodstraňujte pracovní prostor vytvořený Azure Migrate, pokud neodstraníte projekt Migrace Azure. Pokud tak učiníte, funkce vizualizace závislostí nebude fungovat podle očekávání.
**Připojení k Internetu** | Pokud počítače nejsou připojené k internetu, je třeba nainstalovat bránu Log Analytics na ně.
**Azure Government** | Analýza závislostí založená na agentovi není podporována.

## <a name="next-steps"></a>Další kroky

[Příprava na vyhodnocení virtuálního počítače Hyper-V](tutorial-prepare-hyper-v.md)
