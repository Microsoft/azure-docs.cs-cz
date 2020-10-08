---
title: Přehled serverů s podporou ARC Azure
description: Naučte se používat servery s podporou ARC Azure ke správě serverů hostovaných mimo Azure, jako je prostředek Azure.
keywords: Automatizace Azure, DSC, PowerShell, konfigurace požadovaného stavu, Správa aktualizací, sledování změn, inventarizace, Runbooky, Python, grafický, hybridní
ms.date: 10/07/2020
ms.topic: overview
ms.openlocfilehash: 881445b19baef4e025cf3bd32dc3b55b2d170ec3
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91818740"
---
# <a name="what-is-azure-arc-enabled-servers"></a>Co jsou servery s podporou služby Azure Arc?

Servery s podporou ARC Azure umožňují spravovat počítače se systémem Windows a Linux hostované mimo Azure, v podnikové síti nebo v jiném poskytovateli cloudu, podobně jako při správě nativních virtuálních počítačů Azure. Když je hybridní počítač připojený k Azure, bude se jednat o připojený počítač, který se považuje za prostředek v Azure. Každý připojený počítač má ID prostředku, je spravován jako součást skupiny prostředků v rámci předplatného a přináší výhody standardních konstrukcí Azure, jako je například Azure Policy a použití značek. Poskytovatelé služeb, kteří spravují místní infrastrukturu zákazníka, můžou spravovat své hybridní počítače stejným způsobem jako v současnosti s nativními prostředky Azure, a to v rámci více zákaznických prostředí pomocí [Azure Lighthouse](../../lighthouse/how-to/manage-hybrid-infrastructure-arc.md) s využitím Azure ARC.

Aby bylo možné doručovat toto prostředí do vašich hybridních počítačů hostovaných mimo Azure, musí být na každém počítači, který plánujete připojit k Azure, nainstalovaný agent připojeného počítače Azure. Tento agent nedoručuje žádné jiné funkce a nenahrazuje [agenta Azure Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Agent Log Analytics pro systém Windows a Linux je nutný, pokud chcete aktivně monitorovat operační systém a úlohy běžící v počítači, spravovat je pomocí runbooků nebo řešení automatizace, jako je Update Management, nebo použít jiné služby Azure, jako je [Azure Security Center](../../security-center/security-center-intro.md).

## <a name="supported-scenarios"></a>Podporované scénáře

Když počítač připojíte k serverům s podporou ARC Azure, umožní vám provádět následující úlohy správy a monitorování konfigurace:

- Přiřaďte [Azure Policy konfigurace hostů](../../governance/policy/concepts/guest-configuration.md) stejným způsobem jako přiřazení zásad pro virtuální počítače Azure. V současné době většina zásad konfigurace hostů nepoužívá konfigurace, Audituje pouze nastavení v rámci počítače. Pokud chcete pochopit náklady na používání zásad konfigurace Azure Policy hostů se servery s podporou ARC, přečtěte si téma [Cenová příručka](https://azure.microsoft.com/pricing/details/azure-policy/)Azure Policy.

- Pomocí Azure Automation [Change Tracking a inventarizace](../../automation/change-tracking.md)ohlaste změny konfigurace nainstalovaného softwaru, služeb Microsoftu, registru a souborů systému Windows a démony Linux na monitorovaných serverech.

- Monitorujte výkon hostovaného operačního systému a Prohlédněte si součásti aplikace a sledujte jejich procesy a závislosti s dalšími prostředky, které aplikace komunikuje pomocí [Azure monitor pro virtuální počítače](../../azure-monitor/insights/vminsights-overview.md).

- Zjednodušte nasazení s ostatními službami Azure, jako je [Konfigurace stavu](../../automation/automation-dsc-overview.md) Azure Automation, a Azure monitor Log Analytics pracovní prostor s použitím podporovaných [rozšíření virtuálních počítačů Azure](manage-vm-extensions.md) pro počítač se systémem Windows nebo Linux, který není Azure. To zahrnuje provádění konfigurace po nasazení nebo instalace softwaru pomocí rozšíření vlastních skriptů.

- Pomocí [Update Management](../../automation/update-management/update-mgmt-overview.md) v Azure Automation můžete spravovat aktualizace operačního systému pro servery s Windows a Linux. Nejdřív nasaďte roli [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) a pak postupujte podle pokynů a [Povolte Update Management](../../automation/update-management/update-mgmt-enable-portal.md) v počítači, na kterém není Azure Windows nebo Linux.

- Zahrňte servery mimo Azure pro detekci hrozeb a proaktivně monitorujte potenciální bezpečnostní hrozby pomocí [Azure Security Center](../../security-center/security-center-intro.md).

Data protokolu shromážděná a uložená v Log Analytics pracovním prostoru z hybridního počítače teď obsahují vlastnosti specifické pro daný počítač, jako je ID prostředku. To lze použít pro podporu přístupu k protokolu [kontextu prostředků](../../azure-monitor/platform/design-logs-deployment.md#access-mode) .

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Podporované oblasti

Konečný seznam podporovaných oblastí se servery s podporou ARC Azure najdete na stránce [produkty Azure podle oblastí](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc) .

Ve většině případů umístění, které vyberete při vytváření instalačního skriptu, by mělo být oblast Azure geograficky nejblíže umístění vašeho počítače. Uložená data se uloží v rámci geografické oblasti Azure obsahující oblast, kterou zadáte, což může mít vliv i na výběr oblasti, pokud máte požadavky na umístění dat. Pokud je oblast Azure, ke které je počítač připojený, ovlivněná výpadkem, připojený počítač to neovlivní, ale operace správy používající Azure možná nebude možné dokončit. Pokud máte v případě regionálního výpadku více umístění, která podporují geograficky redundantní služby, je nejlepší připojit počítače v každém umístění do jiné oblasti Azure.

### <a name="agent-status"></a>Stav agenta

Agent připojeného počítače pošle službě pravidelné zprávy prezenčního signálu každých 5 minut. Pokud služba přestane přijímat tyto zprávy prezenčního signálu z počítače, považuje se tento počítač za offline a na portálu se automaticky změní stav na **Odpojeno** během 15 až 30 minut. Po přijetí následné zprávy prezenčního signálu od připojeného agenta počítače se jeho stav automaticky změní na **připojeno**.

## <a name="next-steps"></a>Další kroky

Před vyhodnocením nebo povolením serverů s povoleným obloukem na více hybridních počítačích si Projděte [Přehled připojení agenta připojeného počítače](agent-overview.md) , abyste porozuměli požadavkům, technickým podrobnostem o agentovi a metodám
