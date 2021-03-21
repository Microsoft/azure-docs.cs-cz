---
title: Konfigurace pracovního prostoru Log Analytics pro virtuální počítač Insights
description: Popisuje, jak vytvořit a nakonfigurovat pracovní prostor Log Analytics používaný službou VM Insights.
ms.topic: conceptual
ms.custom: references_regions
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.openlocfilehash: 5a0e04772cfc1c9be77c0ad8b32b0e93be8b9f54
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046716"
---
# <a name="configure-log-analytics-workspace-for-vm-insights"></a>Konfigurace pracovního prostoru Log Analytics pro virtuální počítač Insights
Služby VM Insights shromažďují svá data z jednoho nebo více Log Analytics pracovních prostorů v Azure Monitor. Před registrací agentů musíte vytvořit a nakonfigurovat pracovní prostor. Tento článek popisuje požadavky pracovního prostoru a jeho konfiguraci pro službu VM Insights.

## <a name="overview"></a>Přehled
Jedno předplatné může v závislosti na vašich požadavcích používat libovolný počet pracovních prostorů. Jediným požadavkem pracovního prostoru je, že se nachází v podporovaném umístění a bude nakonfigurovaný s řešením *VMInsights* .

Po nakonfigurování pracovního prostoru můžete pomocí kterékoli z dostupných možností nainstalovat požadované agenty na virtuální počítač a sadu škálování virtuálního počítače a zadat pracovní prostor pro odesílání svých dat. Přehledy virtuálních počítačů budou shromažďovat data z libovolného nakonfigurovaného pracovního prostoru ve svém předplatném.

> [!NOTE]
> Pokud povolíte službu VM Insights na jednom virtuálním počítači nebo v sadě škálování virtuálních počítačů pomocí Azure Portal, budete mít možnost vybrat si existující pracovní prostor nebo vytvořit nový. Řešení *VMInsights* se nainstaluje do tohoto pracovního prostoru, pokud ještě není. Tento pracovní prostor pak můžete použít pro jiné agenty.


## <a name="create-log-analytics-workspace"></a>Vytvoření pracovního prostoru služby Log Analytics

>[!NOTE]
>Informace popsané v této části se vztahují také na [řešení Service map](service-map.md). 

Z nabídky **Log Analytics pracovní prostory** získáte přístup k pracovním prostorům Log Analytics v Azure Portal.

[![Log Anlytics pracovní prostory](media/vminsights-configure-workspace/log-analytics-workspaces.png)](media/vminsights-configure-workspace/log-analytics-workspaces.png#lightbox)

Nový pracovní prostor Log Analytics můžete vytvořit pomocí kterékoli z následujících metod. Pokyny k určení počtu pracovních prostorů, které byste měli ve svém prostředí používat a jak navrhnout strategii přístupu, najdete v tématu [navrhování Azure Monitorch protokolů nasazení](../logs/design-logs-deployment.md) .


* [Azure Portal](../logs/quick-create-workspace.md)
* [Azure CLI](../logs/quick-create-workspace-cli.md)
* [PowerShell](../logs/powershell-workspace-configuration.md)
* [Azure Resource Manager](../logs/resource-manager-workspace.md)

## <a name="supported-regions"></a>Podporované oblasti
Služba VM Insights podporuje Log Analytics pracovní prostor v jakékoli [oblasti podporované Log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all) s výjimkou následujících:

- Německo – středozápad
- Jižní Korea – střed

>[!NOTE]
>Virtuální počítače Azure můžete monitorovat v libovolné oblasti. Samotné virtuální počítače nejsou omezeny na oblasti podporované pracovním prostorem Log Analytics.

## <a name="azure-role-based-access-control"></a>Řízení přístupu na základě role v Azure
Pokud chcete funkce v rámci virtuálních počítačů Insights povolit a používat, musíte mít v pracovním prostoru [roli přispěvatel Log Analytics](../logs/manage-access.md#manage-access-using-azure-permissions) . Chcete-li zobrazit data o výkonu, stavu a mapování, musíte mít [roli Čtenář monitorování](../roles-permissions-security.md#built-in-monitoring-roles) pro virtuální počítač Azure. Další informace o tom, jak řídit přístup k pracovnímu prostoru Log Analytics, najdete v tématu [Správa pracovních prostorů](../logs/manage-access.md).

## <a name="add-vminsights-solution-to-workspace"></a>Přidat řešení VMInsights do pracovního prostoru
Než bude možné použít pracovní prostor Log Analytics s přehledem virtuálních počítačů, musí mít nainstalované řešení *VMInsights* . Metody pro konfiguraci pracovního prostoru jsou popsány v následujících částech.

> [!NOTE]
> Když do pracovního prostoru přidáte řešení *VMInsights* , začnou se všechny stávající virtuální počítače připojené k pracovnímu prostoru posílat data do InsightsMetrics. Data pro ostatní datové typy nebudou shromažďována, dokud nepřidáte Dependency Agent do existujících virtuálních počítačů připojených k pracovnímu prostoru.

### <a name="azure-portal"></a>portál Azure
Existují tři možnosti konfigurace stávajícího pracovního prostoru pomocí Azure Portal. Jednotlivé jsou popsány níže.

Pokud chcete nakonfigurovat jeden pracovní prostor, přejděte v nabídce **Azure monitor** na možnost **Virtual Machines** , vyberte **Další možnosti připojování** a pak **nakonfigurujte pracovní prostor**. Vyberte předplatné a pracovní prostor a pak klikněte na **Konfigurovat**.

[![Konfigurace pracovního prostoru](../vm/media/vminsights-enable-policy/configure-workspace.png)](../vm/media/vminsights-enable-policy/configure-workspace.png#lightbox)

Chcete-li konfigurovat více pracovních prostorů, vyberte kartu **Konfigurace pracovního prostoru** v nabídce **Virtual Machines** v nabídce **monitorování** v Azure Portal. Nastavte hodnoty filtru tak, aby se zobrazil seznam existujících pracovních prostorů. Zaškrtněte políčko u každého pracovního prostoru, který chcete povolit, a pak klikněte na **Konfigurovat vybrané**.

[![Konfigurace pracovního prostoru](../vm/media/vminsights-enable-policy/workspace-configuration.png)](../vm/media/vminsights-enable-policy/workspace-configuration.png#lightbox)


Pokud povolíte službu VM Insights na jednom virtuálním počítači nebo v sadě škálování virtuálních počítačů pomocí Azure Portal, budete mít možnost vybrat si existující pracovní prostor nebo vytvořit nový. Řešení *VMInsights* se nainstaluje do tohoto pracovního prostoru, pokud ještě není. Tento pracovní prostor pak můžete použít pro jiné agenty.

[![Povolit jeden virtuální počítač na portálu](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png)](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png#lightbox)


### <a name="resource-manager-template"></a>Šablona Resource Manageru
Šablony Azure Resource Manager pro službu VM Insights jsou k dispozici v souboru archivu (. zip), který si můžete [stáhnout z našeho úložiště GitHub](https://aka.ms/VmInsightsARMTemplates). To zahrnuje šablonu s názvem **ConfigureWorkspace** , která konfiguruje pracovní prostor Log Analytics pro službu VM Insights. Tuto šablonu nasadíte pomocí kterékoli ze standardních metod, včetně ukázkových příkazů PowerShellu a rozhraní příkazového řádku níže: 

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli
az deployment group create --name ConfigureWorkspace --resource-group my-resource-group --template-file CreateWorkspace.json  --parameters workspaceResourceId='/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace' workspaceLocation='eastus'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
New-AzResourceGroupDeployment -Name ConfigureWorkspace -ResourceGroupName my-resource-group -TemplateFile ConfigureWorkspace.json -workspaceResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace -location eastus
```

---



## <a name="next-steps"></a>Další kroky
- Informace o připojení agentů k VIRTUÁLNÍm počítačům najdete v tématu připojení [agentů k virtuálním](vminsights-enable-overview.md) počítačům.
- V tématu [cílení řešení monitorování v Azure monitor (Preview)](../insights/solution-targeting.md) můžete omezit množství dat odesílaných z řešení do pracovního prostoru.
