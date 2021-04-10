---
title: Podpora pro vyhodnocení technologie Hyper-V v Azure Migrate
description: Další informace o podpoře pro vyhodnocování technologie Hyper-V pomocí Azure Migrateho zjišťování a posouzení
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 8531d0b2252e6ddff75509046b5a4576b99d339f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870785"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Matice podpory pro posouzení technologie Hyper-V

Tento článek shrnuje požadavky a požadavky na podporu při zjišťování a hodnocení místních serverů, které běží v prostředí Hyper-V pro migraci do Azure, pomocí nástroje [Azure Migrate: Discovery and Assessment](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) Tool. Pokud chcete migrovat servery, které používají technologii Hyper-V do Azure, zkontrolujte [matrici podpory pro migraci](migrate-support-matrix-hyper-v-migration.md).

Chcete-li nastavit zjišťování a posouzení serverů běžících na technologii Hyper-V, vytvořte projekt a přidejte do projektu nástroj Azure Migrate: Discovery and Assessment. Po přidání nástroje nasadíte [zařízení Azure Migrate](migrate-appliance.md). Zařízení nepřetržitě zjišťuje místní servery a do Azure odesílá metadata serveru a data o výkonu. Po dokončení zjišťování budete shromažďovat zjištěné servery do skupin a spustit posouzení pro skupinu.

## <a name="limitations"></a>Omezení

**Podpora** | **Podrobnosti**
--- | ---
**Omezení hodnocení** | V jednom [projektu](migrate-support-matrix.md#project)můžete vyhledat a posoudit až 35 000 serverů.
**Omezení projektu** | V předplatném Azure můžete vytvořit více projektů. Kromě serverů v technologii Hyper-V může projekt zahrnovat servery na VMware a fyzické servery, a to až do omezení hodnocení pro každý z nich.
**Zjišťování** | Zařízení Azure Migrate může zjistit až 5000 serverů běžících na technologii Hyper-V.<br/><br/> Zařízení se může připojit k až 300 hostitelům Hyper-V.
**Posouzení** | Do jedné skupiny můžete přidat až 35 000 serverů.<br/><br/> V jednom posouzení pro skupinu můžete posoudit až 35 000 serverů.

[Přečtěte si další informace](concepts-assessment-calculation.md) o posouzení.

## <a name="hyper-v-host-requirements"></a>Požadavky na hostitele Hyper-V

| **Podpora**                | **Podrobnosti**
| :-------------------       | :------------------- |
| **Hostitel Hyper-V**       | Hostitel Hyper-V může být samostatný nebo nasazený v clusteru.<br/><br/> Hostitel Hyper-V může používat Windows Server 2019, Windows Server 2016 nebo Windows Server 2012 R2. Instalace jádra serveru těchto operačních systémů jsou také podporovány. <br/>Nelze vyhodnotit servery umístěné na hostitelích Hyper-V se systémem Windows Server 2012.
| **Oprávnění**           | Na hostiteli Hyper-V potřebujete oprávnění správce. <br/> Pokud nechcete přiřazovat oprávnění správce, vytvořte místní účet nebo uživatelský účet domény a přidejte do těchto skupin uživatelský účet – Uživatelé vzdálené správy, Správci technologie Hyper-V a uživatelé nástroje Performance Monitor. |
| **Vzdálená komunikace PowerShellu**   | Na každém hostiteli Hyper-V musí být povolená [Vzdálená komunikace PowerShellu](/powershell/module/microsoft.powershell.core/enable-psremoting) . |
| **Replika technologie Hyper-V**       | Pokud používáte repliku technologie Hyper-V (nebo máte více serverů se stejnými identifikátory serveru) a zjistíte původní i replikované servery pomocí Azure Migrate, hodnocení vygenerované Azure Migrate nemusí být přesné. |

## <a name="server-requirements"></a>Požadavky na server

| **Podpora**                  | **Podrobnosti**
| :----------------------------- | :------------------- |
| **Operační systém** | Pro migraci se dají zhodnotit všechny operační systémy.  |
| **Služba Integration Services**       | Aby bylo možné zachytit informace o operačním systému, musí být na serverech, které vyhodnotily, spuštěny [integrační služby technologie Hyper-v](/virtualization/hyper-v-on-windows/reference/integration-services) . |
| **Storage** | Místní disk, DAS, JBOD, prostory úložiště, CSV, SMB. Podporují se tyto úložiště hostitelů Hyper-V, na kterých jsou uložené disky VHD/VHDX. <br/> Podporují se virtuální řadiče IDE a SCSI|

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
**Hostitel nebo cluster Hyper-V** | Příchozí připojení na portu WinRM 5985 (HTTP) nebo 5986 (HTTPS) pro vyžádání metadat a dat o výkonu pro servery na technologii Hyper-V s použitím relace model CIM (Common Information Model) (CIM).

## <a name="agent-based-dependency-analysis-requirements"></a>Požadavky na analýzu závislostí na základě agentů

[Analýza závislostí](concepts-dependency-visualization.md) vám pomůže identifikovat závislosti mezi místními servery, které chcete vyhodnotit a migrovat do Azure. Tabulka shrnuje požadavky na nastavení analýzy závislostí založené na agentech. Technologie Hyper-V aktuálně podporuje vizualizaci závislostí založenou na agentech.

**Požadavek** | **Podrobnosti**
--- | --- 
**Před nasazením** | Měli byste mít projekt na místě, a to pomocí nástroje Azure Migrate: Discovery and Assessment Tool přidaný do projektu.<br/><br/>  Vizualizace závislostí nasadíte po nastavení zařízení Azure Migrate pro zjišťování vašich místních serverů.<br/><br/> [Naučte](create-manage-projects.md) se, jak poprvé vytvořit projekt.<br/> [Naučte](how-to-assess.md) se, jak přidat Azure Migrate: Nástroj pro zjišťování a vyhodnocení do existujícího projektu.<br/> Naučte se, jak nastavit zařízení pro zjišťování a posuzování [serverů v Hyper-V](how-to-set-up-appliance-hyper-v.md).
**Azure Government** | Vizualizace závislostí není v Azure Government k dispozici.
**Log Analytics** | Azure Migrate používá řešení [Service map](../azure-monitor/vm/service-map.md) v [protokolech Azure monitor](../azure-monitor/logs/log-query-overview.md) pro vizualizaci závislostí.<br/><br/> K projektu přidružíte nový nebo existující Log Analytics pracovní prostor. Pracovní prostor pro projekt nelze po přidání změnit. <br/><br/> Pracovní prostor musí být ve stejném předplatném jako projekt.<br/><br/> Pracovní prostor se musí nacházet v oblastech Východní USA, jihovýchodní Asie nebo Západní Evropa. Pracovní prostory v jiných oblastech nelze přidružit k projektu.<br/><br/> Pracovní prostor musí být v oblasti, ve které [je podporovaná Service map](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> V Log Analytics je pracovní prostor přidružený k Azure Migrate označený klíčem projektu migrace a názvem projektu.
**Vyžadovaná agenti** | Na každém serveru, který chcete analyzovat, nainstalujte následující agenty:<br/><br/> [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md).<br/> [Agent závislostí](../azure-monitor/agents/agents-overview.md#dependency-agent).<br/><br/> Pokud nejsou místní servery připojené k Internetu, musíte na ně stáhnout a nainstalovat bránu Log Analytics.<br/><br/> Přečtěte si další informace o instalaci [agenta závislostí](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) a [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Pracovní prostor Log Analytics** | Pracovní prostor musí být ve stejném předplatném jako projekt.<br/><br/> Azure Migrate podporuje pracovní prostory, které jsou umístěné v oblastech Východní USA, jihovýchodní Asie a Západní Evropa.<br/><br/>  Pracovní prostor musí být v oblasti, ve které [je podporovaná Service map](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> Pracovní prostor pro projekt nelze po přidání změnit.
**Náklady** | V řešení Service Map se neúčtují žádné poplatky za prvních 180 dní (od dne, kdy přiřadíte pracovní prostor Log Analytics k projektu)/<br/><br/> Po uplynutí 180 dnů se začnou účtovat standardní poplatky za Log Analytics.<br/><br/> Použití jiného řešení než Service Map v přidruženém pracovním prostoru Log Analytics bude účtovat [standardní poplatky](https://azure.microsoft.com/pricing/details/log-analytics/) za Log Analytics.<br/><br/> Po odstranění projektu se pracovní prostor neodstraní spolu s ním. Po odstranění projektu Service Map využití není volné a každý uzel se bude účtovat podle placené úrovně Log Analytics pracovního prostoru/<br/><br/>Pokud máte projekty, které jste vytvořili před Azure Migrate všeobecné dostupnosti (GA-28 února 2018), mohly by vám být účtovány další Service Map poplatky. Aby se zajistila platba jenom po 180 dnech, doporučujeme vytvořit nový projekt, protože stávající pracovní prostory před GAm jsou stále Fakturovatelné.
**správy** | Při registraci agentů do pracovního prostoru použijete ID a klíč poskytnutý projektem.<br/><br/> Pracovní prostor Log Analytics můžete použít mimo Azure Migrate.<br/><br/> Pokud odstraníte přidružený projekt, pracovní prostor se automaticky neodstraní. [Odstraňte ji ručně](../azure-monitor/logs/manage-access.md).<br/><br/> Neodstraňujte pracovní prostor vytvořený pomocí Azure Migrate, pokud projekt neodstraníte. Pokud to uděláte, funkce vizualizace závislosti nebude fungovat podle očekávání.
**Připojení k internetu** | Pokud nejsou servery připojené k Internetu, musíte na ně nainstalovat bránu Log Analytics.
**Azure Government** | Analýza závislostí založená na agentech není podporována.

## <a name="next-steps"></a>Další kroky

[Příprava na posouzení serverů, které používají technologii Hyper-v](./tutorial-discover-hyper-v.md).