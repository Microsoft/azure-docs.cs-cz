---
title: Přehled serverů s podporou ARC Azure (Preview)
description: Naučte se používat servery s podporou Azure ARC (Preview) ke správě počítačů, které jsou hostované mimo Azure, jako by se jedná o prostředek Azure.
keywords: Automatizace Azure, DSC, PowerShell, konfigurace požadovaného stavu, Správa aktualizací, sledování změn, inventarizace, Runbooky, Python, grafický, hybridní
ms.custom: references_regions
ms.date: 08/06/2020
ms.topic: overview
ms.openlocfilehash: dfc4d8e046cef5af7c3eaf0d31ce2d6120dfc8f2
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2020
ms.locfileid: "88211318"
---
# <a name="what-is-azure-arc-enabled-servers-preview"></a>Co jsou servery s podporou Azure ARC (Preview)?

Servery s podporou ARC Azure (Preview) umožňují spravovat počítače se systémem Windows a Linux hostované mimo Azure v podnikové síti nebo jiném poskytovateli cloudu, podobně jako při správě nativních virtuálních počítačů Azure. Když je hybridní počítač připojený k Azure, bude se jednat o připojený počítač, který se považuje za prostředek v Azure. Každý připojený počítač má ID prostředku, je spravován jako součást skupiny prostředků v rámci předplatného a přináší výhody standardních konstrukcí Azure, jako je například Azure Policy a použití značek.

Aby bylo možné doručovat toto prostředí do vašich hybridních počítačů hostovaných mimo Azure, musí být na každém počítači, který plánujete připojit k Azure, nainstalovaný agent připojeného počítače Azure. Tento agent nedoručuje žádné jiné funkce a nenahrazuje [agenta Azure Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Agent Log Analytics pro systém Windows a Linux je nutný, pokud chcete aktivně monitorovat operační systém a úlohy běžící v počítači, spravovat je pomocí runbooků nebo řešení automatizace, jako je Update Management, nebo použít jiné služby Azure, jako je [Azure Security Center](../../security-center/security-center-intro.md).

>[!NOTE]
>Tato verze Preview je určená pro účely vyhodnocení a nedoporučujeme spravovat důležité provozní počítače.
>

## <a name="supported-scenarios"></a>Podporované scénáře

Když počítač připojíte k serverům s podporou ARC Azure (Preview), umožní vám provádět následující úlohy správy konfigurace:

- Přiřaďte [Azure Policy konfigurace hostů](../../governance/policy/concepts/guest-configuration.md) stejným způsobem jako přiřazení zásad pro virtuální počítače Azure.

- Monitorujte výkon hostovaného operačního systému a Prohlédněte si součásti aplikace a sledujte jejich procesy a závislosti s dalšími prostředky, které aplikace komunikuje pomocí [Azure monitor pro virtuální počítače](../../azure-monitor/insights/vminsights-overview.md).

- Zjednodušte nasazení s ostatními službami Azure, jako je konfigurace stavu Azure Automation, a Azure Monitor Log Analytics pracovní prostor s využitím podporovaných [rozšíření virtuálních počítačů Azure](manage-vm-extensions.md) pro počítače se systémem Windows nebo Linux mimo Azure. To zahrnuje provádění konfigurace po nasazení nebo instalace softwaru pomocí rozšíření vlastních skriptů.

Data protokolu shromážděná a uložená v Log Analytics pracovním prostoru z hybridního počítače teď obsahují vlastnosti specifické pro daný počítač, jako je ID prostředku. To lze použít pro podporu přístupu k protokolu [kontextu prostředků](../../azure-monitor/platform/design-logs-deployment.md#access-mode) .

## <a name="supported-regions"></a>Podporované oblasti

Se servery s podporou ARC Azure (Preview) jsou podporované jenom některé oblasti:

- EastUS
- WestUS2
- WestEurope
- SoutheastAsia

Ve většině případů umístění, které vyberete při vytváření instalačního skriptu, by mělo být oblast Azure geograficky nejblíže umístění vašeho počítače. Uložená data se uloží v rámci geografické oblasti Azure obsahující oblast, kterou zadáte, což může mít vliv i na výběr oblasti, pokud máte požadavky na umístění dat. Pokud je oblast Azure, ke které je počítač připojený, ovlivněná výpadkem, připojený počítač to neovlivní, ale operace správy používající Azure možná nebude možné dokončit. Pokud máte v případě regionálního výpadku více umístění, která podporují geograficky redundantní služby, je nejlepší připojit počítače v každém umístění do jiné oblasti Azure.

### <a name="agent-status"></a>Stav agenta

Agent připojeného počítače pošle službě pravidelné zprávy prezenčního signálu každých 5 minut. Pokud služba přestane přijímat tyto zprávy prezenčního signálu z počítače, považuje se tento počítač za offline a na portálu se automaticky změní stav na **Odpojeno** během 15 až 30 minut. Po přijetí následné zprávy prezenčního signálu od připojeného agenta počítače se jeho stav automaticky změní na **připojeno**.

## <a name="next-steps"></a>Další kroky

Před vyhodnocením nebo povolením serverů s povoleným ARC (ve verzi Preview) na více hybridních počítačích si přečtěte článek [Přehled agenta připojeného počítače](agent-overview.md) , abyste zjistili, co je potřeba, technické podrobnosti o agentovi a metodách nasazení.
