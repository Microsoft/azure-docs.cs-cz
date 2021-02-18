---
title: Podpora pro vyhodnocení technologie Hyper-V v Azure Migrate
description: Další informace o podpoře pro vyhodnocení technologie Hyper-V s Azure Migrate posouzení serveru
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/14/2020
ms.openlocfilehash: 4407f9544a7d3a19e3ff7d404669fdb27b881d30
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590638"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Matice podpory pro posouzení technologie Hyper-V

Tento článek shrnuje požadavky a požadavky na podporu při vyhodnocení virtuálních počítačů Hyper-V pro migraci do Azure pomocí nástroje [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) Tool. Pokud chcete migrovat virtuální počítače Hyper-V do Azure, přečtěte si [matici podpora migrace](migrate-support-matrix-hyper-v-migration.md).

Pokud chcete nastavit posouzení virtuálního počítače Hyper-V, vytvořte projekt Azure Migrate a přidejte do projektu nástroj pro vyhodnocení serveru. Po přidání nástroje nasadíte [zařízení Azure Migrate](migrate-appliance.md). Zařízení nepřetržitě zjišťuje místní počítače a odesílá do Azure metadata počítače a data o výkonu. Po dokončení zjišťování shromáždíte zjištěné počítače do skupin a spustíte posouzení pro skupinu.


## <a name="limitations"></a>Omezení

**Podpora** | **Podrobnosti**
--- | ---
**Omezení hodnocení** | V jednom [Azure Migrate projektu](migrate-support-matrix.md#azure-migrate-projects)můžete vyhledat a posoudit až 35 000 virtuálních počítačů Hyper-V.
**Omezení projektu** | V předplatném Azure můžete vytvořit více projektů. Kromě virtuálních počítačů Hyper-V může projekt zahrnovat virtuální počítače VMware a fyzické servery až po omezení pro posouzení jednotlivých.
**Zjišťování** | Zařízení Azure Migrate může zjistit až 5000 virtuálních počítačů Hyper-V.<br/><br/> Zařízení se může připojit k až 300 hostitelům Hyper-V.
**Posouzení** | Do jedné skupiny můžete přidat až 35 000 počítačů.<br/><br/> V jednom posouzení pro skupinu můžete vyhodnotit až 35 000 virtuálních počítačů.

[Přečtěte si další informace](concepts-assessment-calculation.md) o posouzení.



## <a name="hyper-v-host-requirements"></a>Požadavky na hostitele Hyper-V

| **Podpora**                | **Podrobnosti**               
| :-------------------       | :------------------- |
| **Hostitel Hyper-V**       | Hostitel Hyper-V může být samostatný nebo nasazený v clusteru.<br/><br/> Hostitel Hyper-V může používat Windows Server 2019, Windows Server 2016 nebo Windows Server 2012 R2. Instalace jádra serveru těchto operačních systémů je taky podporovaná. <br/>Virtuální počítače na hostitelích Hyper-V s Windows Serverem 2012 hodnotit nemůžete.
| **Oprávnění**           | Na hostiteli Hyper-V potřebujete oprávnění správce. <br/> Pokud nechcete přiřazovat oprávnění správce, vytvořte místní účet nebo uživatelský účet domény a přidejte do těchto skupin uživatelský účet – Uživatelé vzdálené správy, Správci technologie Hyper-V a uživatelé nástroje Performance Monitor. |
| **Vzdálená komunikace PowerShellu**   | Na každém hostiteli Hyper-V musí být povolená [Vzdálená komunikace PowerShellu](/powershell/module/microsoft.powershell.core/enable-psremoting?view=powershell-7) . |
| **Replika technologie Hyper-V**       | Pokud používáte repliku technologie Hyper-V (nebo máte více virtuálních počítačů se stejnými identifikátory virtuálních počítačů) a zjistíte jak původní, tak replikované virtuální počítače pomocí Azure Migrate, hodnocení vygenerované Azure Migrate nemusí být přesné. |


## <a name="vm-requirements"></a>Požadavky na virtuální počítače

| **Podpora**                  | **Podrobnosti**               
| :----------------------------- | :------------------- |
| **Operační systém** | Pro migraci se dají zhodnotit všechny operační systémy.  |
| **Služba Integration Services**       | Aby bylo možné zachytit informace o operačním systému, musí být na virtuálních počítačích, které jste vyhodnotili, spuštěny [integrační služby technologie Hyper-v](/virtualization/hyper-v-on-windows/reference/integration-services) . |
| **Storage** | Místní disk, DAS, JBOD, prostory úložiště, CSV, SMB. Podporují se tyto úložiště hostitele Hyper-V, na kterém jsou uložené disky VHD/VHDX. <br/> Podporují se virtuální řadiče IDE a SCSI| 

## <a name="azure-migrate-appliance-requirements"></a>Požadavky zařízení Azure Migrate

Azure Migrate používá [zařízení Azure Migrate](migrate-appliance.md) ke zjišťování a hodnocení. Zařízení můžete nasadit pomocí komprimovaného virtuálního pevného disku Hyper-V, který stáhnete z portálu, nebo pomocí [skriptu PowerShellu](deploy-appliance-script.md).

- Přečtěte si informace o [požadavcích na zařízení](migrate-appliance.md#appliance---hyper-v) pro Hyper-V.
- Přečtěte si o adresách URL, které zařízení potřebuje k přístupu ve [veřejných](migrate-appliance.md#public-cloud-urls) a [státních](migrate-appliance.md#government-cloud-urls) cloudech.
- V Azure Government musíte zařízení nasadit [pomocí skriptu](deploy-appliance-script-government.md).

## <a name="port-access"></a>Přístup k portu

Následující tabulka shrnuje požadavky na porty pro posouzení.

**Zařízení** | **Připojení**
--- | ---
**Náplně** | Příchozí připojení na portu TCP 3389 umožňující připojení ke vzdálené ploše zařízení.<br/><br/> Příchozí připojení na portu 44368 pro vzdálený přístup k aplikaci pro správu zařízení pomocí adresy URL: ``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Odchozí připojení na portech 443 (HTTPS), která odesílají metadata zjišťování a výkonu Azure Migrate.
**Hostitel nebo cluster Hyper-V** | Příchozí připojení na portu WinRM 5985 (HTTP) nebo 5986 (HTTPS) pro vyžádání metadat a dat výkonu pro virtuální počítače Hyper-V pomocí relace model CIM (Common Information Model) (CIM).

## <a name="agent-based-dependency-analysis-requirements"></a>Požadavky na analýzu závislostí na základě agentů

[Analýza závislostí](concepts-dependency-visualization.md) vám pomůže identifikovat závislosti mezi místními počítači, které chcete vyhodnotit a migrovat do Azure. Tabulka shrnuje požadavky na nastavení analýzy závislostí založené na agentech. Technologie Hyper-V aktuálně podporuje vizualizaci závislostí založenou na agentech. 

**Požadavek** | **Podrobnosti** 
--- | --- 
**Před nasazením** | Měli byste mít Azure Migrate projekt, pomocí nástroje pro vyhodnocení serveru přidaný do projektu.<br/><br/>  Vizualizace závislostí nasadíte po nastavení zařízení Azure Migrate pro zjištění vašich místních počítačů.<br/><br/> [Naučte](create-manage-projects.md) se, jak poprvé vytvořit projekt.<br/> [Přečtěte si, jak](how-to-assess.md) přidat nástroj pro vyhodnocení do existujícího projektu.<br/> Přečtěte si, jak nastavit zařízení Azure Migrate pro posouzení [virtuálních počítačů Hyper-V](how-to-set-up-appliance-hyper-v.md).
**Azure Government** | Vizualizace závislostí není v Azure Government k dispozici.
**Log Analytics** | Azure Migrate používá řešení [Service map](../azure-monitor/vm/service-map.md) v [protokolech Azure monitor](../azure-monitor/logs/log-query-overview.md) pro vizualizaci závislostí.<br/><br/> K projektu Azure Migrate přidružíte nový nebo existující Log Analytics pracovní prostor. Pracovní prostor pro Azure Migrate projekt nelze po přidání změnit. <br/><br/> Pracovní prostor musí být ve stejném předplatném jako projekt Azure Migrate.<br/><br/> Pracovní prostor se musí nacházet v oblastech Východní USA, jihovýchodní Asie nebo Západní Evropa. Pracovní prostory v jiných oblastech nelze přidružit k projektu.<br/><br/> Pracovní prostor musí být v oblasti, ve které [je podporovaná Service map](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> V Log Analytics je pracovní prostor přidružený k Azure Migrate označený klíčem projektu migrace a názvem projektu.
**Vyžadovaná agenti** | Na každém počítači, který chcete analyzovat, nainstalujte následující agenty:<br/><br/> [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md).<br/> [Agent závislostí](../azure-monitor/agents/agents-overview.md#dependency-agent).<br/><br/> Pokud nejsou místní počítače připojené k Internetu, musíte na ně stáhnout a nainstalovat bránu Log Analytics.<br/><br/> Přečtěte si další informace o instalaci [agenta závislostí](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) a [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Pracovní prostor Log Analytics** | Pracovní prostor musí být ve stejném předplatném jako projekt Azure Migrate.<br/><br/> Azure Migrate podporuje pracovní prostory, které jsou umístěné v oblastech Východní USA, jihovýchodní Asie a Západní Evropa.<br/><br/>  Pracovní prostor musí být v oblasti, ve které [je podporovaná Service map](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> Pracovní prostor pro Azure Migrate projekt nelze po přidání změnit.
**Náklady** | V řešení Service Map se neúčtují žádné poplatky za prvních 180 dní (od dne, kdy přidružíte pracovní prostor Log Analytics k projektu Azure Migrate)/<br/><br/> Po uplynutí 180 dnů se začnou účtovat standardní poplatky za Log Analytics.<br/><br/> Použití jiného řešení než Service Map v přidruženém pracovním prostoru Log Analytics bude účtovat [standardní poplatky](https://azure.microsoft.com/pricing/details/log-analytics/) za Log Analytics.<br/><br/> Když se projekt Azure Migrate odstraní, pracovní prostor se spolu s ním neodstraní. Po odstranění projektu Service Map využití není volné a každý uzel se bude účtovat podle placené úrovně Log Analytics pracovního prostoru/<br/><br/>Pokud máte projekty, které jste vytvořili před Azure Migrate všeobecné dostupnosti (GA-28 února 2018), mohly by vám být účtovány další Service Map poplatky. Aby se zajistila platba jenom po 180 dnech, doporučujeme vytvořit nový projekt, protože stávající pracovní prostory před GAm jsou stále Fakturovatelné.
**správy** | Při registraci agentů do pracovního prostoru použijete ID a klíč poskytnutý Azure Migrate projektem.<br/><br/> Pracovní prostor Log Analytics můžete použít mimo Azure Migrate.<br/><br/> Pokud odstraníte přidružený Azure Migrate projekt, pracovní prostor se automaticky neodstraní. [Odstraňte ji ručně](../azure-monitor/logs/manage-access.md).<br/><br/> Pokud neodstraníte projekt Azure Migrate, neodstraňujte pracovní prostor vytvořený pomocí Azure Migrate. Pokud to uděláte, funkce vizualizace závislosti nebude fungovat podle očekávání.
**Připojení k internetu** | Pokud nejsou počítače připojené k Internetu, musíte na ně nainstalovat bránu Log Analytics.
**Azure Government** | Analýza závislostí založená na agentech není podporována.

## <a name="next-steps"></a>Další kroky

[Příprava na posouzení virtuálních počítačů Hyper-V](./tutorial-discover-hyper-v.md)