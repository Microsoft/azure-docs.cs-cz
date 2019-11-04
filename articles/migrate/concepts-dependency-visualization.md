---
title: Vizualizace závislostí ve službě Azure Migrate
description: Poskytuje přehled výpočtů posouzení ve službě hodnocení serveru v Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: hamusa
ms.openlocfilehash: 17ba06d6ac09f220b4343092292275a1cc315377
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489213"
---
# <a name="dependency-visualization"></a>Vizualizace závislostí

Tento článek popisuje funkci Vizualizace závislostí v Azure Migrate: posouzení serveru.

Vizualizace závislostí vám pomůže pochopit závislosti mezi počítači, které chcete vyhodnotit a migrovat. Mapování závislostí se obvykle používá, pokud chcete vyhodnotit počítače s vyšší úrovní spolehlivosti.

- V Azure Migrate: posouzení serveru shromažďujete počítače dohromady do skupin pro posouzení. Skupiny se obvykle skládají z počítačů, které chcete migrovat společně, a vizualizace závislostí vám pomůže kontrolovat závislosti počítačů, takže můžete počítače seskupit přesně.
- Pomocí vizualizace můžete zjistit vzájemně závislé systémy, které potřebují migrovat dohromady. Můžete určit, jestli se běžící systémy pořád používají, nebo jestli se můžou systémy vyřadit z provozu místo migrace.
- Vizualizace závislostí pomáhá zajistit, že nic není na pozadí, a během migrace se vyhnete nepřekročení výpadků.
- Tato funkce je užitečná hlavně v případě, že nejste zcela vědomi počítačů, které jsou součástí aplikací, a proto by se měly migrovat společně do Azure.


> [!NOTE]
> Funkce vizualizace závislosti není v Azure Government k dispozici.

## <a name="agent-based-and-agentless"></a>Založené na agentech a bez agentů

Existují dvě možnosti nasazení Vizualizace závislostí:

- **Vizualizace závislostí bez agentů**: Tato možnost je aktuálně ve verzi Preview. Nevyžaduje, abyste v počítačích nainstalovali žádné agenty. 
    - Funguje tak, že zachytává data připojení TCP z počítačů, pro které je povolená. [Další informace](how-to-create-group-machine-dependencies-agentless.md).
Po spuštění zjišťování závislostí zařízení shromáždí data z počítačů v intervalu dotazování po dobu pěti minut.
    - Shromažďují se následující data:
        - Připojení TCP
        - Názvy procesů, které mají aktivní připojení
        - Názvy nainstalovaných aplikací, které spouštějí výše uvedené procesy
        - Ne. připojení zjištěných při každém intervalu dotazování
- **Vizualizace závislostí na základě agenta**: Pokud chcete použít vizualizaci závislostí založenou na agentech, musíte si stáhnout a nainstalovat následující agenty na každý místní počítač, který chcete analyzovat.  
    - Na každém počítači musí být nainstalovaný [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows). [Přečtěte si další informace](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-mma) o tom, jak nainstalovat agenta MMA.
    - Na každém počítači je potřeba nainstalovat [agenta závislostí](../azure-monitor/platform/agents-overview.md#dependency-agent) . [Přečtěte si další informace](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-dependency-agent) o tom, jak nainstalovat agenta závislostí.
    - Pokud navíc máte počítače bez připojení k internetu, musíte na ně stáhnout a nainstalovat bránu Log Analytics.

## <a name="agent-based-requirements"></a>Požadavky založené na agentovi

### <a name="what-do-i-need-to-deploy-dependency-visualization"></a>Co potřebuji k nasazení Vizualizace závislostí?

Než nasadíte vizualizaci závislostí, měli byste mít Azure Migrate projekt, a to pomocí Azure Migrate: Nástroj pro vyhodnocení serveru přidaný do projektu. Vizualizace závislostí nasadíte po nastavení zařízení Azure Migrate pro zjišťování vašich místních počítačů.

[Přečtěte si další informace](how-to-assess.md) o přidání nástroje a nasazení zařízení pro [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)nebo fyzické servery.


### <a name="how-does-it-work"></a>Jak to funguje?

Azure Migrate používá řešení [Service map](../operations-management-suite/operations-management-suite-service-map.md) v [protokolech Azure monitor](../log-analytics/log-analytics-overview.md) pro vizualizaci závislostí.

- Aby bylo možné využít vizualizaci závislostí, je třeba k projektu Azure Migrate přidružit Log Analytics pracovní prostor (nové nebo existující).
- Pracovní prostor musí být ve stejném předplatném, jako by byl vytvořen Azure Migrate projekt.
- Azure Migrate podporuje pracovní prostory, které jsou umístěné v oblastech Východní USA, jihovýchodní Asie a Západní Evropa. Pracovní prostory v jiných oblastech nelze přidružit k projektu. Všimněte si také, že se pracovní prostor musí nacházet v oblasti, ve které [je podpora Service map podporována](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).
- Pracovní prostor pro Azure Migrate projekt nelze po přidání změnit.
- V Log Analytics je pracovní prostor přidružený k Azure Migrate označený klíčem projektu migrace a názvem projektu.

    ![Přejít Log Analytics pracovní prostor](./media/concepts-dependency-visualization/oms-workspace.png)



### <a name="do-i-need-to-pay-for-it"></a>Je nutné za ně platit?

Vizualizace závislosti vyžaduje Service Map a přidružený pracovní prostor Log Analytics. 

- V řešení Service Map se neúčtují žádné poplatky za prvních 180 dní. To je od data, kdy jste přidružit Log Analytics pracovní prostor k projektu Azure Migrate.
- Po 180 dnech budou platit standardní poplatky za Log Analytics.
- Použití jakéhokoli řešení, které je jiné než Service Map v přidruženém pracovním prostoru Log Analytics, bude mít za následek [standardní Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) poplatky.
- Když se projekt Azure Migrate odstraní, pracovní prostor se spolu s ním neodstraní. Po odstranění projektu Service Map využití není volné a každý uzel se bude účtovat podle placené úrovně Log Analytics pracovního prostoru.

Další informace o cenách služby Azure Migrate najdete [zde](https://azure.microsoft.com/pricing/details/azure-migrate/).

> [!NOTE]
> Pokud máte projekty, které jste vytvořili před Azure Migrateou obecnou dostupností ve 28. února 2018, mohly by vám být účtovány další Service Map poplatky. Abyste měli jistotu, že platíte jenom po 180 dnech, doporučujeme, abyste si vytvořili nový projekt, protože stávající pracovní prostory před obecnou dostupností jsou pořád Fakturovatelné.



### <a name="how-do-i-manage-the-workspace"></a>Návody spravovat pracovní prostor?

- Při registraci agentů do pracovního prostoru použijete ID a klíč poskytnutý Azure Migrate projektem.
- Pracovní prostor Log Analytics můžete použít mimo Azure Migrate.
- Pokud odstraníte přidružený Azure Migrate projekt, pracovní prostor se automaticky neodstraní. Musíte [ho odstranit ručně](../azure-monitor/platform/manage-access.md).
- Pokud neodstraníte projekt Azure Migrate, neodstraňujte pracovní prostor vytvořený pomocí Azure Migrate. Pokud to uděláte, funkce vizualizace závislosti nebude fungovat podle očekávání.

## <a name="next-steps"></a>Další kroky
- [Seskupení počítačů pomocí závislostí počítačů](how-to-create-group-machine-dependencies.md)
- [Přečtěte si další informace](https://docs.microsoft.com/azure/migrate/resources-faq#what-is-dependency-visualization) o nejčastějších dotazech k vizualizaci závislostí.


