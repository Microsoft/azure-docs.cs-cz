---
title: Vizualizace závislostí v Azure Migrate | Microsoft Docs
description: Poskytuje přehled výpočtů posouzení ve službě hodnocení serveru v Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: hamusa
ms.openlocfilehash: 4b6a140ec428ce3b053c41074f02f65f19b8dc72
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102894"
---
# <a name="dependency-visualization"></a>Vizualizace závislostí

Azure Migrate: Posouzení serveru vyhodnocuje skupiny místních počítačů pro migraci do Azure. K vytvoření skupin můžete použít funkci Vizualizace závislostí v nástroji hodnocení serveru. Tento článek poskytuje informace o této funkci.

> [!NOTE]
> Funkce vizualizace závislosti není v Azure Government k dispozici.

## <a name="overview"></a>Přehled

Vizualizace závislostí v nástroji vyhodnocování serveru umožňuje vytvořit vysoce spolehlivé skupiny pro vyhodnocení migrace. Pomocí Vizualizace závislostí můžete zobrazit síťové závislosti počítačů a identifikovat související počítače, které je potřeba migrovat společně do Azure. Tato funkce je užitečná ve scénářích, kdy si nejste naprosto vědomi počítačů, které tvoří vaši aplikaci, a je třeba je migrovat společně do Azure.

## <a name="before-you-start"></a>Než začnete

- Ujistěte se, že jste [vytvořili](how-to-add-tool-first-time.md) projekt Azure Migrate.
- Pokud jste již vytvořili projekt, ujistěte se, že jste [přidali](how-to-assess.md) Azure Migrate: Nástroj pro vyhodnocení serveru.
- Ujistěte se, že jste počítače zjistili v Azure Migrate. to můžete provést nastavením zařízení Azure Migrate pro [VMware](how-to-set-up-appliance-vmware.md) nebo [Hyper-V](how-to-set-up-appliance-hyper-v.md). Zařízení zjišťuje místní počítače a odesílá data o metadatech a výkonu Azure Migrate: Hodnocení serverů. [Další informace](migrate-appliance.md).

## <a name="how-does-it-work"></a>Jak to funguje?

Azure Migrate používá řešení [Service map](../operations-management-suite/operations-management-suite-service-map.md) v [protokolech Azure monitor](../log-analytics/log-analytics-overview.md) pro vizualizaci závislostí.
- Aby bylo možné využít vizualizaci závislostí, je nutné k projektu Azure Migrate přidružit Log Analytics pracovní prostor, a to buď nové, nebo existující.
- Pracovní prostor můžete vytvořit nebo připojit pouze ve stejném předplatném, ve kterém je vytvořen Azure Migrate projekt.
- Připojení pracovního prostoru Log Analytics k projektu:
    1. Na kartě **servery** v **Azure Migrate: Na dlaždici posouzení** serveru klikněte na **Přehled**.
    2. V části **Přehled**klikněte na šipku dolů a rozbalte **základy**.
    3. V **pracovním prostoru OMS**klikněte na **vyžaduje konfiguraci**.
    4. V **oblasti konfigurovat pracovní prostor**určete, jestli chcete vytvořit nový pracovní prostor, nebo použijte existující:
    
    ![Přidat pracovní prostor](./media/how-to-create-group-machine-dependencies/workspace.png)

- Když přidružujete pracovní prostor, získáte možnost vytvořit nový pracovní prostor nebo připojit nějaký existující:
  - Když vytváříte nový pracovní prostor, musíte zadat název pracovního prostoru. Pracovní prostor se pak vytvoří v oblasti ve stejném geografickém regionu [Azure](https://azure.microsoft.com/global-infrastructure/geographies/) jako projekt migrace.
  - Když připojíte existující pracovní prostor, můžete vybrat ze všech dostupných pracovních prostorů ve stejném předplatném jako projekt migrace. Všimněte si, že jsou uvedeny pouze ty pracovní prostory, které byly vytvořeny v oblasti, kde [je podpora Service map podporována](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions). Aby bylo možné připojit pracovní prostor, ujistěte se, že máte přístup čtenář k pracovnímu prostoru.

  > [!NOTE]
  > Jakmile připojíte pracovní prostor k projektu, nemůžete ho později změnit.

- Přidružený pracovní prostor je označený jako **projekt migrace**klíče a hodnota **název projektu**, kterou můžete použít k vyhledávání v Azure Portal.
- Pokud chcete přejít k pracovnímu prostoru přidruženému k projektu, můžete přejít na oddíl **základy** na stránce **Přehled** projektu a získat přístup k pracovnímu prostoru.

    ![Přejít Log Analytics pracovní prostor](./media/concepts-dependency-visualization/oms-workspace.png)

Pokud chcete používat vizualizaci závislostí, potřebujete si stáhnout a nainstalovat agenty na každý místní počítač, který chcete analyzovat.  

- Na každém počítači je potřeba nainstalovat [agenta Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) . [Přečtěte si další informace](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-mma) o tom, jak nainstalovat agenta MMA.
- Na každém počítači je potřeba nainstalovat [agenta závislostí](../azure-monitor/platform/agents-overview.md#dependency-agent) . [Přečtěte si další informace](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-dependency-agent) o tom, jak nainstalovat agenta závislostí.
- Pokud navíc máte počítače bez připojení k internetu, musíte na ně stáhnout a nainstalovat bránu Log Analytics.

Pokud nepoužíváte vizualizaci závislostí, nepotřebujete tyto agenty na počítačích, které chcete vyhodnotit.

## <a name="do-i-need-to-pay-for-it"></a>Je nutné za ně platit?

Funkce vizualizace závislosti je k dispozici bez dalších poplatků. Použití funkce Vizualizace závislostí v nástroji vyhodnocování serveru vyžaduje Service Map a vyžaduje, abyste k projektu Azure Migrate přiřadili pracovní prostor Log Analytics, a to buď nové, nebo existující. Funkce Vizualizace závislostí v posouzení serveru je po dobu prvních 180 dnů zdarma.

1. Pomocí jiných řešení, než je Service Map v tomto pracovním prostoru Log Analytics, se budou účtovat [standardní Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) poplatky.
2. Za účelem podpory migračních scénářů bez dalších poplatků se Service Map řešení neúčtují žádné poplatky za prvních 180 dní od data přidružení Log Analytics pracovního prostoru k projektu Azure Migrate. Po 180 dnech budou platit standardní poplatky za Log Analytics.

Při registraci agentů do pracovního prostoru použijte ID a klíč uvedený v projektu na stránce instalovat kroky agenta.

Když se projekt Azure Migrate odstraní, pracovní prostor se spolu s ním neodstraní. Po odstranění projektu bude použití Service Map nevolné a každý uzel se bude účtovat podle placené úrovně Log Analytics pracovního prostoru.

> [!NOTE]
> Funkce vizualizace závislosti používá Service Map přes pracovní prostor Log Analytics. Od 28. února 2018 s oznámením Azure Migrateou obecnou dostupnost je tato funkce teď dostupná bez dalších poplatků. Abyste mohli využít pracovní prostor bezplatného využití, budete muset vytvořit nový projekt. Stávající pracovní prostory před všeobecnou dostupností jsou stále Fakturovatelné, proto doporučujeme přejít na nový projekt.

Další informace o cenách služby Azure Migrate najdete [zde](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="how-do-i-manage-the-workspace"></a>Návody spravovat pracovní prostor?

Pracovní prostor Log Analytics můžete použít mimo Azure Migrate. Neodstraní se, pokud odstraníte Azure Migrate projekt, ve kterém byl vytvořen. Pokud pracovní prostor již nepotřebujete, [odstraňte jej](../azure-monitor/platform/manage-access.md) ručně.

Pokud neodstraníte projekt Azure Migrate, neodstraňujte pracovní prostor vytvořený pomocí Azure Migrate. Pokud to uděláte, funkce vizualizace závislosti nebude fungovat podle očekávání.

## <a name="next-steps"></a>Další postup
- [Seskupení počítačů pomocí závislostí počítačů](how-to-create-group-machine-dependencies.md)
- [Přečtěte si další informace](https://docs.microsoft.com/azure/migrate/resources-faq#what-is-dependency-visualization) o nejčastějších dotazech k vizualizaci závislostí.
