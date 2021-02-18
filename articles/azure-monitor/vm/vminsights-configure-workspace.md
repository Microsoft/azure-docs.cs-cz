---
title: Konfigurace pracovního prostoru Log Analytics pro Azure Monitor pro virtuální počítače
description: V této části najdete popis postupu vytvoření a konfigurace pracovního prostoru Log Analytics používaného v Azure Monitor pro virtuální počítače.
ms.subservice: ''
ms.topic: conceptual
ms.custom: references_regions
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.openlocfilehash: b84f9cae848d53cf04e1b77810b347786e122c5b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100612239"
---
# <a name="configure-log-analytics-workspace-for-azure-monitor-for-vms"></a>Konfigurace pracovního prostoru Log Analytics pro Azure Monitor pro virtuální počítače
Azure Monitor pro virtuální počítače shromažďuje data z jednoho nebo více Log Analytics pracovních prostorů v Azure Monitor. Před registrací agentů musíte vytvořit a nakonfigurovat pracovní prostor. Tento článek popisuje požadavky pracovního prostoru a jeho konfiguraci pro Azure Monitor pro virtuální počítače.

## <a name="overview"></a>Přehled
Jedno předplatné může v závislosti na vašich požadavcích používat libovolný počet pracovních prostorů. Jediným požadavkem pracovního prostoru je, že se nachází v podporovaném umístění a bude nakonfigurovaný s řešením *VMInsights* .

Po nakonfigurování pracovního prostoru můžete pomocí kterékoli z dostupných možností nainstalovat požadované agenty na virtuální počítač a sadu škálování virtuálního počítače a zadat pracovní prostor pro odesílání svých dat. Azure Monitor pro virtuální počítače bude shromažďovat data z libovolného nakonfigurovaného pracovního prostoru ve svém předplatném.

> [!NOTE]
> Pokud povolíte Azure Monitor pro virtuální počítače na jednom virtuálním počítači nebo sadě škálování virtuálních počítačů pomocí Azure Portal, budete mít možnost vybrat si existující pracovní prostor nebo vytvořit nový. Řešení *VMInsights* se nainstaluje do tohoto pracovního prostoru, pokud ještě není. Tento pracovní prostor pak můžete použít pro jiné agenty.


## <a name="create-log-analytics-workspace"></a>Vytvoření pracovního prostoru služby Log Analytics

>[!NOTE]
>Informace popsané v této části se vztahují také na [řešení Service map](service-map.md). 

Z nabídky **Log Analytics pracovní prostory** získáte přístup k pracovním prostorům Log Analytics v Azure Portal.

[![Log Anlytics pracovní prostory](media/vminsights-configure-workspace/log-analytics-workspaces.png)](media/vminsights-configure-workspace/log-analytics-workspaces.png#lightbox)

Nový pracovní prostor Log Analytics můžete vytvořit pomocí kterékoli z následujících metod. Pokyny k určení počtu pracovních prostorů, které byste měli ve svém prostředí používat a jak navrhnout strategii přístupu, najdete v tématu [navrhování Azure Monitorch protokolů nasazení](../platform/design-logs-deployment.md) .


* [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../platform/powershell-workspace-configuration.md)
* [Azure Resource Manager](../samples/resource-manager-workspace.md)

## <a name="supported-regions"></a>Podporované oblasti
Azure Monitor pro virtuální počítače podporuje pracovní prostor Log Analytics v některé oblasti, kterou [podporuje Log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all) s výjimkou následujících:

- Německo – středozápad
- Jižní Korea – střed

>[!NOTE]
>Virtuální počítače Azure můžete monitorovat v libovolné oblasti. Samotné virtuální počítače nejsou omezeny na oblasti podporované pracovním prostorem Log Analytics.

## <a name="azure-role-based-access-control"></a>Řízení přístupu na základě role v Azure
Chcete-li povolit a přistupovat k funkcím v Azure Monitor pro virtuální počítače, musíte mít v pracovním prostoru [roli přispěvatele Log Analytics](../platform/manage-access.md#manage-access-using-azure-permissions) . Chcete-li zobrazit data o výkonu, stavu a mapování, musíte mít [roli Čtenář monitorování](../platform/roles-permissions-security.md#built-in-monitoring-roles) pro virtuální počítač Azure. Další informace o tom, jak řídit přístup k pracovnímu prostoru Log Analytics, najdete v tématu [Správa pracovních prostorů](../platform/manage-access.md).

## <a name="add-vminsights-solution-to-workspace"></a>Přidat řešení VMInsights do pracovního prostoru
Než bude možné použít pracovní prostor Log Analytics s Azure Monitor pro virtuální počítače, musí mít nainstalované řešení *VMInsights* . Metody pro konfiguraci pracovního prostoru jsou popsány v následujících částech.

> [!NOTE]
> Když do pracovního prostoru přidáte řešení *VMInsights* , začnou se všechny stávající virtuální počítače připojené k pracovnímu prostoru posílat data do InsightsMetrics. Data pro ostatní datové typy nebudou shromažďována, dokud nepřidáte Dependency Agent do existujících virtuálních počítačů připojených k pracovnímu prostoru.

### <a name="azure-portal"></a>portál Azure
Existují tři možnosti konfigurace stávajícího pracovního prostoru pomocí Azure Portal. Jednotlivé jsou popsány níže.

Pokud chcete nakonfigurovat jeden pracovní prostor, přejděte v nabídce **Azure monitor** na možnost **Virtual Machines** , vyberte **Další možnosti připojování** a pak **nakonfigurujte pracovní prostor**. Vyberte předplatné a pracovní prostor a pak klikněte na **Konfigurovat**.

[![Konfigurace pracovního prostoru](../vm/media/vminsights-enable-policy/configure-workspace.png)](../vm/media/vminsights-enable-policy/configure-workspace.png#lightbox)

Chcete-li konfigurovat více pracovních prostorů, vyberte kartu **Konfigurace pracovního prostoru** v nabídce **Virtual Machines** v nabídce **monitorování** v Azure Portal. Nastavte hodnoty filtru tak, aby se zobrazil seznam existujících pracovních prostorů. Zaškrtněte políčko u každého pracovního prostoru, který chcete povolit, a pak klikněte na **Konfigurovat vybrané**.

[![Konfigurace pracovního prostoru](../vm/media/vminsights-enable-policy/workspace-configuration.png)](../vm/media/vminsights-enable-policy/workspace-configuration.png#lightbox)


Pokud povolíte Azure Monitor pro virtuální počítače na jednom virtuálním počítači nebo sadě škálování virtuálních počítačů pomocí Azure Portal, budete mít možnost vybrat si existující pracovní prostor nebo vytvořit nový. Řešení *VMInsights* se nainstaluje do tohoto pracovního prostoru, pokud ještě není. Tento pracovní prostor pak můžete použít pro jiné agenty.

[![Povolit jeden virtuální počítač na portálu](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png)](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png#lightbox)


### <a name="resource-manager-template"></a>Šablona Resource Manageru
Šablony Azure Resource Manager pro Azure Monitor pro virtuální počítače jsou k dispozici v souboru archivu (. zip), který si můžete [stáhnout z našeho úložiště GitHub](https://aka.ms/VmInsightsARMTemplates). To zahrnuje šablonu s názvem **ConfigureWorkspace** , která konfiguruje pracovní prostor Log Analytics pro Azure monitor pro virtuální počítače. Tuto šablonu nasadíte pomocí kterékoli ze standardních metod, včetně ukázkových příkazů PowerShellu a rozhraní příkazového řádku níže: 

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
- Další informace najdete v tématu [agenti Azure monitor pro virtuální počítače](vminsights-enable-overview.md) pro připojení agentů k Azure monitor pro virtuální počítače.
- V tématu [cílení řešení monitorování v Azure monitor (Preview)](../insights/solution-targeting.md) můžete omezit množství dat odesílaných z řešení do pracovního prostoru.
