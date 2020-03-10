---
title: Vizualizace závislostí ve službě Azure Migrate
description: Poskytuje přehled výpočtů posouzení ve službě hodnocení serveru v Azure Migrate
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: f24656d02e19f422ff26e6b06d1631a9128dff43
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361979"
---
# <a name="dependency-visualization"></a>Vizualizace závislostí

Tento článek popisuje vizualizaci závislostí v Azure Migrate: posouzení serveru.

## <a name="what-is-dependency-visualization"></a>Co je Vizualizace závislostí?

Vizualizace závislostí vám pomůže identifikovat závislosti mezi místními počítači, které chcete vyhodnotit a migrovat do Azure. 

- V Azure Migrate: vyhodnocení serveru shromažďujete počítače do skupiny a pak ji vyhodnotí. Vizualizace závislostí umožňuje přesněji seskupit počítače a zajistit vysokou spolehlivost pro posouzení.
- Vizualizace závislostí umožňuje identifikovat počítače, které se musí migrovat dohromady. Můžete určit, jestli se počítače používají, nebo jestli je možné je vyřadit z provozu místo migrace.
- Vizualizace závislostí pomáhá zajistit, že nic není na pozadí, a během migrace se vyhnete nepřekročení výpadků.
- Vizualizace je užitečná hlavně v případě, že si nejste jistí, jestli jsou počítače součástí nasazení aplikace, které chcete migrovat do Azure.


> [!NOTE]
> Vizualizace závislostí není v Azure Government k dispozici.

## <a name="agent-basedagentless-visualization"></a>Vizualizace založená na agentech nebo bez agentů

Existují dvě možnosti nasazení Vizualizace závislostí:

- **Založený na agentovi**: Vizualizace závislostí založená na agentech vyžaduje instalaci agentů na každý místní počítač, který chcete analyzovat.
- Bez **agenta**: s touto možností není nutné instalovat agenty na počítačích, které chcete křížově kontrolovat. Tato možnost je momentálně ve verzi Preview a je dostupná jenom pro virtuální počítače VMware.


## <a name="agent-based-visualization"></a>Vizualizace založená na agentech

**Požadavek** | **Podrobnosti** | **Další informace**
--- | --- | ---
**Před nasazením** | Měli byste mít Azure Migrate projekt, a to pomocí Azure Migrate: Nástroj pro vyhodnocení serveru přidaný do projektu.<br/><br/>  Vizualizace závislostí nasadíte po nastavení zařízení Azure Migrate pro zjišťování vašich místních počítačů. | [Naučte](create-manage-projects.md) se, jak poprvé vytvořit projekt.<br/><br/> [Přečtěte si, jak](how-to-assess.md) přidat nástroj pro vyhodnocení do existujícího projektu.<br/><br/> Naučte se, jak nastavit zařízení Azure Migrate pro posouzení [technologie Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)nebo fyzických serverů.
**Vyžadovaná agenti** | Na každém počítači, který chcete analyzovat, nainstalujte následující agenty:<br/><br/> [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/><br/> [Agent závislostí](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Pokud nejsou místní počítače připojené k Internetu, musíte na ně stáhnout a nainstalovat bránu Log Analytics. | Přečtěte si další informace o instalaci [agenta závislostí](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) a [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics** | Azure Migrate používá řešení [Service map](../operations-management-suite/operations-management-suite-service-map.md) v [protokolech Azure monitor](../log-analytics/log-analytics-overview.md) pro vizualizaci závislostí.<br/><br/> K projektu Azure Migrate přidružíte nový nebo existující Log Analytics pracovní prostor. Pracovní prostor pro Azure Migrate projekt nelze po přidání změnit. <br/><br/> Pracovní prostor musí být ve stejném předplatném jako projekt Azure Migrate.<br/><br/> Pracovní prostor se musí nacházet v oblastech Východní USA, jihovýchodní Asie nebo Západní Evropa. Pracovní prostory v jiných oblastech nelze přidružit k projektu.<br/><br/> Pracovní prostor musí být v oblasti, ve které [je podporovaná Service map](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> V Log Analytics je pracovní prostor přidružený k Azure Migrate označený klíčem projektu migrace a názvem projektu.
**Ceny** | V řešení Service Map se neúčtují žádné poplatky za prvních 180 dní (od dne, kdy přidružíte pracovní prostor Log Analytics k projektu Azure Migrate)/<br/><br/> Po 180 dnech budou platit standardní poplatky za Log Analytics.<br/><br/> Použití jiného řešení než Service Map v přidruženém pracovním prostoru Log Analytics bude účtovat [standardní poplatky](https://azure.microsoft.com/pricing/details/log-analytics/) za Log Analytics.<br/><br/> Když se projekt Azure Migrate odstraní, pracovní prostor se spolu s ním neodstraní. Po odstranění projektu Service Map využití není volné a každý uzel se bude účtovat podle placené úrovně Log Analytics pracovního prostoru/<br/><br/>Pokud máte projekty, které jste vytvořili před Azure Migrate všeobecné dostupnosti (GA-28 února 2018), mohly by vám být účtovány další Service Map poplatky. Aby se zajistila platba jenom po 180 dnech, doporučujeme vytvořit nový projekt, protože stávající pracovní prostory před GAm jsou stále Fakturovatelné.
**Správu** | Při registraci agentů do pracovního prostoru použijete ID a klíč poskytnutý Azure Migrate projektem.<br/><br/> Pracovní prostor Log Analytics můžete použít mimo Azure Migrate.<br/><br/> Pokud odstraníte přidružený Azure Migrate projekt, pracovní prostor se automaticky neodstraní. [Odstraňte ji ručně](../azure-monitor/platform/manage-access.md).<br/><br/> Pokud neodstraníte projekt Azure Migrate, neodstraňujte pracovní prostor vytvořený pomocí Azure Migrate. Pokud to uděláte, funkce vizualizace závislosti nebude fungovat podle očekávání.

## <a name="agentless-visualization"></a>Vizualizace bez agentů


**Požadavek** | **Podrobnosti** | **Další informace**
--- | --- | ---
**Před nasazením** | Měli byste mít Azure Migrate projekt, a to pomocí Azure Migrate: Nástroj pro vyhodnocení serveru přidaný do projektu.<br/><br/>  Vizualizace závislostí nasadíte po nastavení zařízení Azure Migrate pro zjišťování místních počítačů VMWare. | [Naučte](create-manage-projects.md) se, jak poprvé vytvořit projekt.<br/><br/> [Přečtěte si, jak](how-to-assess.md) přidat nástroj pro vyhodnocení do existujícího projektu.<br/><br/> Přečtěte si, jak nastavit zařízení Azure Migrate pro posouzení virtuálních počítačů [VMware](how-to-set-up-appliance-vmware.md) .
**Vyžadovaná agenti** | Na počítačích, které chcete analyzovat, není vyžadován žádný agent.
**Podporované operační systémy** | Projděte si [operační systémy](migrate-support-matrix-vmware.md#agentless-dependency-visualization) , které jsou podporované pro vizualizaci bez agentů.
**Virtuální počítače** | **Nástroje VMware**: na virtuálních počítačích, které chcete analyzovat, musí být nainstalované a spuštěné nástroje VMware.<br/><br/> **Účet**: na zařízení Azure Migrate musíte přidat uživatelský účet, který se dá použít pro přístup k virtuálním počítačům pro účely analýzy.<br/><br/> **Virtuální počítače s Windows**: uživatelský účet musí být na počítači místním správcem nebo správcem domény.<br/><br/> **Virtuální počítače se systémem Linux**: na účtu se vyžaduje oprávnění root. Tento uživatelský účet také vyžaduje tyto dvě funkce na/bin/netstat a/bin/LS soubory: CAP_DAC_READ_SEARCH a CAP_SYS_PTRACE. | [Přečtěte si o](migrate-appliance.md) zařízení Azure Migrate.
**VMware** | **vCenter**: zařízení vyžaduje účet vCenter Server s přístupem jen pro čtení a oprávnění povolená pro operace hosta Virtual Machines >.<br/><br/> **Hostitelé ESXi**: na hostiteli ESXi, na kterých běží virtuální počítače, které chcete analyzovat, musí být zařízení Azure Migrate schopné připojit se k portu TCP 443.
**Shromážděná data** |  Vizualizace závislostí bez agentů funguje tak, že zachytí data připojení TCP z počítačů, pro které je povolená. Po zahájení zjišťování závislosti zařízení shromáždí tato data z počítačů pomocí cyklického dotazování každých pět minut:<br/> – Připojení TCP.<br/> – Názvy procesů, které mají aktivní připojení.<br/> – Názvy nainstalovaných aplikací, které spouštějí proces s aktivními připojeními.<br/> – Počet připojení zjištěných při každém intervalu dotazování.


## <a name="next-steps"></a>Další kroky
- [Nastavení Vizualizace závislostí](how-to-create-group-machine-dependencies.md)
- [Vyzkoušejte vizualizaci závislostí bez agentů](how-to-create-group-machine-dependencies-agentless.md) pro virtuální počítače VMware.
- Přečtěte si [běžné otázky](common-questions-discovery-assessment.md#what-is-dependency-visualization) k vizualizaci závislostí.


