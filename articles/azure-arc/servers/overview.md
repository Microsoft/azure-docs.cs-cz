---
title: Přehled služby Azure ARC pro servery (Preview)
description: Naučte se používat Azure ARC pro servery ke správě počítačů, které jsou hostované mimo Azure, jako by se jedná o prostředek Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
keywords: Automatizace Azure, DSC, PowerShell, konfigurace požadovaného stavu, Správa aktualizací, sledování změn, inventarizace, Runbooky, Python, grafický, hybridní
ms.date: 03/24/2020
ms.topic: overview
ms.openlocfilehash: 95a01db7d4d889df4695390bfd0d01510d83a817
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648040"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>Co je Azure ARC pro servery (Preview)

Azure ARC pro servery (Preview) umožňuje spravovat počítače se systémem Windows a Linux hostované mimo Azure v podnikové síti nebo jiném poskytovateli cloudu, podobně jako při správě nativních virtuálních počítačů Azure. Když je hybridní počítač připojený k Azure, bude se jednat o připojený počítač, který se považuje za prostředek v Azure. Každý připojený počítač má ID prostředku, je spravován jako součást skupiny prostředků v rámci předplatného a přináší výhody standardních konstrukcí Azure, jako je například Azure Policy a použití značek.

Aby bylo možné doručovat toto prostředí do vašich hybridních počítačů hostovaných mimo Azure, musí být na každém počítači, který plánujete připojit k Azure, nainstalovaný agent připojeného počítače Azure. Tento agent nedoručuje žádné jiné funkce a nenahrazuje [agenta Azure Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Agent Log Analytics pro systém Windows a Linux je nutný, pokud chcete aktivně monitorovat operační systém a úlohy běžící v počítači, spravovat je pomocí runbooků nebo řešení automatizace, jako je Update Management, nebo použít jiné služby Azure, jako je [Azure Security Center](../../security-center/security-center-intro.md).

>[!NOTE]
>Tato verze Preview je určená pro účely vyhodnocení a nedoporučujeme spravovat důležité provozní počítače.
>

## <a name="supported-scenarios"></a>Podporované scénáře

Azure ARC pro servery (verze Preview) podporuje následující scénáře s připojenými počítači:

- Přiřaďte [Azure Policy konfigurace hostů](../../governance/policy/concepts/guest-configuration.md) stejným způsobem jako přiřazení zásad pro virtuální počítače Azure.
- Data protokolu shromážděná agentem Log Analytics, která jsou uložená v pracovním prostoru Log Analytics, je počítač zaregistrovaný. Data protokolu z hybridního počítače nyní obsahují vlastnosti specifické pro daný počítač, například ID prostředku, které lze použít k podpoře přístupu k protokolu [kontextu prostředků](../../azure-monitor/platform/design-logs-deployment.md#access-mode) .

## <a name="supported-regions"></a>Podporované oblasti

U Azure ARC pro servery (Preview) jsou podporované jenom některé oblasti:

- WestUS2
- WestEurope
- WestAsia

Ve většině případů umístění, které vyberete při vytváření instalačního skriptu, by mělo být oblast Azure geograficky nejblíže umístění vašeho počítače. Uložená data se uloží v rámci geografické oblasti Azure obsahující oblast, kterou zadáte, což může mít vliv i na výběr oblasti, pokud máte požadavky na umístění dat. Pokud je oblast Azure, ke které je počítač připojený, ovlivněná výpadkem, připojený počítač to neovlivní, ale operace správy používající Azure možná nebude možné dokončit. V případě pružnosti v případě výpadku v oblasti regionu, pokud máte více umístění, která poskytují geograficky redundantní službu, je nejlepší připojit počítače v každém umístění do jiné oblasti Azure.

### <a name="agent-status"></a>Stav agenta

Agent připojeného počítače pošle službě pravidelné zprávy prezenčního signálu každých 5 minut. Pokud služba přestane přijímat tyto zprávy prezenčního signálu z počítače, považuje se tento počítač za offline a na portálu se automaticky změní stav na **Odpojeno** během 15 až 30 minut. Po přijetí následné zprávy prezenčního signálu od připojeného agenta počítače se jeho stav automaticky změní na **připojeno**.

## <a name="next-steps"></a>Další kroky

Před vyhodnocením nebo povolením ARC pro servery (ve verzi Preview) na více hybridních počítačích si přečtěte článek [Přehled agenta připojeného počítače](agent-overview.md) , abyste zjistili, co je potřeba, technické podrobnosti o agentovi a metody nasazení.
