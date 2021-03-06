---
title: Přehled serverů s podporou ARC Azure
description: Naučte se používat servery s podporou ARC Azure ke správě serverů hostovaných mimo Azure, jako je prostředek Azure.
keywords: Automatizace Azure, DSC, PowerShell, konfigurace požadovaného stavu, Správa aktualizací, sledování změn, inventarizace, Runbooky, Python, grafický, hybridní
ms.date: 04/21/2021
ms.topic: overview
ms.openlocfilehash: 324f6cc29bd9e4eca1a20413032c213c2618a11e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831991"
---
# <a name="what-is-azure-arc-enabled-servers"></a>Co jsou servery s podporou služby Azure Arc?

Servery s podporou ARC Azure umožňují spravovat fyzické servery a virtuální počítače s Windows a Linux hostovaných *mimo* Azure, ve vaší podnikové síti nebo v jiném poskytovateli cloudu. Tato prostředí pro správu jsou navržená tak, aby odpovídala způsobu správy nativních virtuálních počítačů Azure. Když je hybridní počítač připojený k Azure, bude se jednat o připojený počítač, který se považuje za prostředek v Azure. Každý připojený počítač má ID prostředku, je součástí skupiny prostředků a přináší výhody standardních konstrukcí Azure, jako jsou Azure Policy a použití značek. Poskytovatelé služeb, kteří spravují místní infrastrukturu zákazníka, můžou spravovat své hybridní počítače stejným způsobem jako v současnosti s nativními prostředky Azure, a to v rámci více zákaznických prostředí pomocí [Azure Lighthouse](../../lighthouse/how-to/manage-hybrid-infrastructure-arc.md) s využitím Azure ARC.

Aby bylo možné doručovat toto prostředí do vašich hybridních počítačů hostovaných mimo Azure, musí být na každém počítači, který plánujete připojit k Azure, nainstalovaný agent připojeného počítače Azure. Tento agent nedoručuje žádné jiné funkce a nenahrazuje [agenta Azure Log Analytics](../../azure-monitor/agents/log-analytics-agent.md). Agent Log Analytics pro systém Windows a Linux je nutný, pokud chcete aktivně monitorovat operační systém a úlohy běžící v počítači, spravovat je pomocí runbooků nebo řešení automatizace, jako je Update Management, nebo použít jiné služby Azure, jako je [Azure Security Center](../../security-center/security-center-introduction.md).

## <a name="supported-scenarios"></a>Podporované scénáře

Když počítač připojíte k serverům s podporou ARC Azure, umožní vám provádět následující úlohy správy a monitorování konfigurace:

- Přiřaďte [Azure Policy konfigurace hostů](../../governance/policy/concepts/guest-configuration.md) stejným způsobem jako přiřazení zásad pro virtuální počítače Azure. V současné době většina zásad konfigurace hostů nepoužívá konfigurace, Audituje pouze nastavení v rámci počítače. Pokud chcete pochopit náklady na používání zásad konfigurace Azure Policy hostů se servery s podporou ARC, přečtěte si téma [Cenová příručka](https://azure.microsoft.com/pricing/details/azure-policy/)Azure Policy.

- Nahlaste se o změnách konfigurace nainstalovaného softwaru, služeb Microsoftu, registru a souborů Windows a démonech Linux na monitorovaných serverech pomocí Azure Automation [Change Tracking a](../../automation/change-tracking/overview.md) [monitorování Integrity souborů a Azure Security Center](../../security-center/security-center-file-integrity-monitoring.md)pro servery, které jsou pro [Azure Defender pro servery](../../security-center/defender-for-servers-introduction.md)povolené.

- Monitorujte výkon hostovaného operačního systému a Prohlédněte si komponenty aplikace a sledujte jejich procesy a závislosti s dalšími prostředky, které aplikace komunikuje s využitím služby [VM Insights](../../azure-monitor/vm/vminsights-overview.md).

- Zjednodušte nasazení pomocí jiných služeb Azure, jako je [Konfigurace stavu](../../automation/automation-dsc-overview.md) Azure Automation, a Azure monitor Log Analytics pracovní prostor s použitím podporovaných [rozšíření virtuálních počítačů Azure](manage-vm-extensions.md) pro počítač se systémem Windows nebo Linux, který není Azure. To zahrnuje provádění konfigurace po nasazení nebo instalace softwaru pomocí rozšíření vlastních skriptů.

- Použití [Update Management](../../automation/update-management/overview.md) v Azure Automation ke správě aktualizací operačního systému pro servery s Windows a Linux

    > [!NOTE]
    > V tuto chvíli není podporována povolení Update Management přímo ze serveru s podporou ARC. V tématu [povolení Update Management ze svého účtu Automation](../../automation/update-management/enable-from-automation-account.md) můžete pochopit požadavky a povolit server.

- Zahrňte servery mimo Azure pro detekci hrozeb a proaktivně monitorujte potenciální bezpečnostní hrozby pomocí [Azure Security Center](../../security-center/security-center-introduction.md).

Data protokolu shromážděná a uložená v Log Analytics pracovním prostoru z hybridního počítače teď obsahují vlastnosti specifické pro daný počítač, jako je ID prostředku. To lze použít pro podporu přístupu k protokolu [kontextu prostředků](../../azure-monitor/logs/design-logs-deployment.md#access-mode) .

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Podporované oblasti

Konečný seznam podporovaných oblastí se servery s podporou ARC Azure najdete na stránce [produkty Azure podle oblastí](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc) .

Ve většině případů umístění, které vyberete při vytváření instalačního skriptu, by mělo být oblast Azure geograficky nejblíže umístění vašeho počítače. Uložená data se ukládají do geografické oblasti Azure, která obsahuje oblast, kterou určíte, což může mít vliv na zvolenou oblast, i když máte požadavky na data na úrovni. Pokud je oblast Azure, ke které je počítač připojený, ovlivněná výpadkem, připojený počítač to neovlivní, ale operace správy používající Azure nemusí být možné dokončit. Pokud dojde k oblastnímu výpadku a pokud máte více umístění, která podporují geograficky redundantní službu, je nejlepší připojit počítače v každém umístění do jiné oblasti Azure.

Následující informace o metadatech připojeného počítače se shromažďují a ukládají v oblasti, ve které je nakonfigurovaný prostředek počítače Azure ARC:

- Název a verze operačního systému
- Název počítače
- Plně kvalifikovaný název domény počítače (FQDN)
- Verze agenta připojeného počítače

Například pokud je počítač zaregistrován pomocí ARC Azure v oblasti Východní USA, tato data jsou uložena v oblasti USA.

### <a name="supported-environments"></a>Podporovaná prostředí

Servery s podporou ARC podporují správu fyzických serverů a virtuálních počítačů hostovaných *mimo* Azure. Konkrétní informace o tom, která hybridní cloudová prostředí hostující virtuální počítače jsou podporovaná, najdete v tématu [požadavky na agenta připojených počítačů](agent-overview.md#supported-environments).

> [!NOTE]
> Servery s podporou ARC nejsou navržené ani nepodporované a umožňují správu virtuálních počítačů spuštěných v Azure.

### <a name="agent-status"></a>Stav agenta

Agent připojeného počítače pošle službě pravidelné zprávy prezenčního signálu každých 5 minut. Pokud služba přestane přijímat tyto zprávy prezenčního signálu z počítače, považuje se tento počítač za offline a na portálu se automaticky změní stav na **Odpojeno** během 15 až 30 minut. Po přijetí následné zprávy prezenčního signálu od připojeného agenta počítače se jeho stav automaticky změní na **připojeno**.

## <a name="next-steps"></a>Další kroky

Před vyhodnocením nebo povolením serverů s povoleným obloukem na více hybridních počítačích si Projděte [Přehled připojení agenta připojeného počítače](agent-overview.md) , abyste porozuměli požadavkům, technickým podrobnostem o agentovi a metodám