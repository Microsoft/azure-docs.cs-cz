---
title: Nasazení Azure Monitor pro virtuální počítače (preview) | Dokumentace Microsoftu
description: Tento článek popisuje, jak nasadit a nakonfigurovat monitorování Azure pro virtuální počítače, abyste je mohli začít, pochopení, jaký je výkon distribuované aplikace a problémy se stavem, které byly identifikovány.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2019
ms.author: magoedte
ms.openlocfilehash: e97ac849fa0e590dd2462d8e64b761da23576833
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54845950"
---
# <a name="deploy-azure-monitor-for-vms-preview"></a>Nasazení Azure Monitor pro virtuální počítače (preview)
Tento článek popisuje, jak nastavit monitorování Azure pro virtuální počítače. Služba monitoruje stav operačního systému Azure virtuální počítače (VM) a škálovací sady virtuálních počítačů a virtuálních počítačů ve vašem prostředí. Toto monitorování zahrnuje zjišťování a mapování závislostí aplikace, které mohou být hostovány na nich. 

Azure Monitor povolíte pro virtuální počítače pomocí jedné z následujících metod:

* Povolit jednoho virtuálního počítače Azure tak, že vyberete **Insights (preview)** přímo z virtuálního počítače.
* Povolení dva nebo více virtuálních počítačů Azure pomocí služby Azure Policy. Prostřednictvím této metody jsou požadované závislosti stávající i nové virtuální počítače nainstalované a správně nakonfigurovaný. Nekompatibilní virtuální počítače označené, abyste se mohli rozhodnout, jestli je chcete povolit, a způsob jejich řešení.
* Povolit dvě nebo více virtuálních počítačů Azure nebo virtuální počítač škálovací sady v zadané předplatné nebo skupinu prostředků pomocí prostředí PowerShell.

Další informace o jednotlivých metod najdete dále v tomto článku.

## <a name="prerequisites"></a>Požadavky
Než začnete, ujistěte se, že rozumíte informace v následujících částech.

### <a name="log-analytics"></a>Log Analytics

Pracovní prostor Log Analytics je aktuálně podporované v těchto oblastech:

- Západní střed USA
- USA – východ
- Západní Evropa
- Jihovýchodní Asie<sup>1</sup>

<sup>1</sup> této oblasti nepodporuje aktuálně funkci stavu služby Azure Monitor pro virtuální počítače.

>[!NOTE]
>Azure virtual machines se dají nasadit z libovolné oblasti a nejsou omezené na podporovaných oblastí pro pracovní prostor Log Analytics.
>

Pokud nemáte pracovní prostor, můžete vytvořit jednu s jedním z následujících metod:
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Pokud aktivujete monitorování pro jeden virtuální počítač Azure na webu Azure Portal, můžete vytvořit pracovní prostor během tohoto procesu.

Pokud chcete povolit řešení pro scénář ve velkém měřítku, nejprve nakonfigurujte následující ve vašem pracovním prostoru Log Analytics:

* Nainstalujte řešení ServiceMap a InfrastructureInsights. Dokončení této instalace pouze s použitím šablony Azure Resource Manageru, která je k dispozici v tomto článku.
* Konfigurovat pracovní prostor Log Analytics ke shromažďování čítačů výkonu.

Proveďte konfiguraci pracovního prostoru pro scénář ve velkém měřítku, naleznete v tématu [nastavení pracovního prostoru Log Analytics pro nasazení ve velkém měřítku](#setup-log-analytics-workspace).

### <a name="supported-operating-systems"></a>Podporované operační systémy

Následující tabulka uvádí operační systémy Windows a Linuxem, které jsou podporovány službou Azure Monitor pro virtuální počítače. Úplný seznam, který podrobně popisuje verze operačního systému Linux hlavní a dílčí a podporované verze jádra je v této části dozvíte později.

|Verze operačního systému |Výkon |Maps |Stav |
|-----------|------------|-----|-------|
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X| |
|Red Hat Enterprise Linux (RHEL) 7, 6| X | X| X |
|Ubuntu 18.04, 16.04, 14.04 | X | X | X |
|Linux centOS 7, 6 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X | X |
|Oracle Linux 7 | X<sup>1</sup> | | X |
|Oracle Linux 6 | X | X | X |
|Debian 9.4, 8 | X<sup>1</sup> | | X |

<sup>1</sup> the výkonu funkce služby Azure Monitor pro virtuální počítače je k dispozici pouze ze služby Azure Monitor. Není k dispozici, když se dostanete přímo z levého podokna virtuálního počítače Azure.

>[!NOTE]
>Následující informace platí pro podporu operačního systému Linux:
> - Jsou podporované jen verze s výchozím a SMP jádrem Linuxu.
> - Používá se nestandardní jádra vyjde nová verze, jako například rozšíření fyzické adresy (PAE) a Xen, nejsou podporovány pro libovolnou distribuci Linuxu. Například systém s řetězec verze *2.6.16.21-0.8-xen* se nepodporuje.
> - Vlastní jádrech, včetně překompilování standardní jádra, nejsou podporovány.
> - CentOSPlus jádra není podporován.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Verze operačního systému | Verze jádra |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Verze operačního systému | Verze jádra |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |
| 6.9 | 2.6.32-696 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Verze operačního systému | Verze jádra |
|:--|:--|
| Ubuntu 18.04 | jádra 4.15. * |
| Ubuntu 16.04.3 | jádra 4.15. * |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="oracle-enterprise-linux-6-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 6 s nedělitelné Enterprise jádra
| Verze operačního systému | Verze jádra
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-enterprise-linux-5-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 5 s nedělitelné Enterprise jádra

| Verze operačního systému | Verze jádra
|:--|:--|
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Verze operačního systému | Verze jádra
|:--|:--|
|12 SP2 | 4.4. * |
|12 SP3 | 4.4. * |

### <a name="the-microsoft-dependency-agent"></a>Agent Microsoft Dependency
Azure Monitor pro funkci mapování virtuálních počítačů získává data od agenta Microsoft Dependency. Agent závislostí závisí na agenta Log Analytics pro připojení k Log Analytics. Proto musí mít váš systém agenta Log Analytics, instalaci a konfiguraci agenta závislostí.

Povolit monitorování Azure pro virtuální počítače pro jeden virtuální počítač Azure nebo používat metodu nasazení ve velkém měřítku, budete muset použít rozšíření agenta závislosti virtuálních počítačů Azure jako součást možností instalace agenta.

V hybridním prostředí můžete stáhnout a nainstalovat agenta závislostí v některém ze dvou způsobů: Ručně nebo pomocí metody automatizované nasazení pro virtuální počítače, které jsou hostované mimo Azure.

Následující tabulka popisuje připojené zdroje, které podporuje funkce mapy v hybridním prostředí.

| Připojený zdroj | Podporováno | Popis |
|:--|:--|:--|
| Agenti systému Windows | Ano | Kromě [agenta Log Analytics pro Windows](../../azure-monitor/platform/log-analytics-agent.md), agenti Windows vyžadují agent služby Microsoft Dependency. Úplný seznam verzí operačního systému najdete v tématu [podporované operační systémy](#supported-operating-systems). |
| Agenti systému Linux | Ano | Kromě [agenta Log Analytics pro Linux](../../azure-monitor/platform/log-analytics-agent.md), vyžadují agent služby Microsoft Dependency agenti systému Linux. Úplný seznam verzí operačního systému najdete v tématu [podporované operační systémy](#supported-operating-systems). |
| Skupina pro správu nástroje System Center Operations Manager | Ne | |

Agent závislostí si můžete stáhnout z následujícího umístění:

| File | Operační systém | Verze | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |

## <a name="role-based-access-control"></a>Řízení přístupu na základě role
Pokud chcete povolit a přístup k funkcím ve službě Azure Monitor pro virtuální počítače, je potřeba přiřadit přístup následující role:

- Pokud chcete řešení povolit, musíte mít *Přispěvatel Log Analytics* role.

- Zobrazení výkonu, stavu, a mapování dat, musíte mít *Čtenář monitorování* role virtuálního počítače Azure. Pracovní prostor Log Analytics musí být nakonfigurovaný pro monitorování Azure pro virtuální počítače.

Další informace o tom, jak řídit přístup k pracovnímu prostoru Log Analytics najdete v tématu [Správa pracovních prostorů](../../azure-monitor/platform/manage-access.md).

## <a name="enable-monitoring-in-the-azure-portal"></a>Povolit monitorování na webu Azure Portal
Pokud chcete povolit monitorování virtuálního počítače Azure na webu Azure Portal, postupujte takto:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Vyberte **virtuálních počítačů**.

1. V seznamu vyberte virtuální počítač.

1. Na stránce virtuální počítač v **monitorování** vyberte **Insights (preview)**.

1. Na **Insights (preview)** stránce **vyzkoušet**.

    ![Povolit monitorování Azure pro virtuální počítače pro virtuální počítač](./media/vminsights-onboard/enable-vminsights-vm-portal-01.png)
1. Na **připojování Přehled monitorování Azure** stránky, pokud máte existující Log Analytics vyberte pracovní prostor v rámci stejného předplatného, v rozevíracím seznamu.  
    V seznamu vybrána hodnota výchozího pracovního prostoru a umístění, který se nasazuje virtuální počítač v rámci předplatného. 

    >[!NOTE]
    >Pokud chcete vytvořit nový pracovní prostor Log Analytics pro ukládání dat monitorování z virtuálního počítače, postupujte podle pokynů v [vytvořit pracovní prostor Log Analytics](../../azure-monitor/learn/quick-create-workspace.md) v jednom z podporovaných oblastech uvedených výše.

Po povolení sledování, může trvat přibližně 10 minut, než můžete zobrazit stav metriky pro virtuální počítač.

![Povolit Azure Monitor pro monitorování zpracování nasazení virtuálních počítačů](./media/vminsights-onboard/onboard-vminsights-vm-portal-status.png)


## <a name="deploy-at-scale"></a>Nasazení ve velkém měřítku
V této části nasadíte Azure Monitor pro virtuální počítače ve velkém měřítku s využitím zásad Azure nebo Azure Powershellu.

Před nasazením virtuálních počítačů předem nakonfigurujte váš pracovní prostor Log Analytics následujícím způsobem:

1. Pokud ještě nemáte pracovní prostor, vytvořte, který může podporovat monitorování Azure pro virtuální počítače.  
    Než budete pokračovat, naleznete v tématu [Správa pracovních prostorů](../../log-analytics/log-analytics-manage-access.md?toc=/azure/azure-monitor/toc.json) zvážení všech faktorů náklady, správy a dodržování předpisů.

1. Vytvořte nový pracovní prostor, pokud již neexistuje, který slouží k podpoře monitorování Azure pro virtuální počítače. Kontrola [Správa pracovních prostorů](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json) před vytvořením nového pracovního prostoru zvážení všech faktorů náklady, správy a dodržování předpisů než budete pokračovat.

1. Povolte čítače výkonu v pracovním prostoru pro kolekci na virtuální počítače s Windows a Linux.

1. Instalace a povolení ServiceMap a InfrastructureInsights řešení ve vašem pracovním prostoru.

### <a name="set-up-a-log-analytics-workspace"></a>Nastavte pracovní prostor Log Analytics
Pokud nemáte pracovní prostor Log Analytics, vytvořte ho revize metody, které jsou navržené v ["Požadavky"](#log-analytics) oddílu.

#### <a name="enable-performance-counters"></a>Povolit čítače výkonu
Pokud pracovní prostor Log Analytics, který je odkazován řešení ještě nenakonfigurovala získat čítače výkonu, vyžadují řešení, musíte je povolit. Můžete tak učinit v některém ze dvou způsobů:
* Ručně, jak je popsáno v [Windows a Linuxem zdroje dat výkonu do Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Stažením a instalací, který je k dispozici skript prostředí PowerShell [Galerie prostředí PowerShell pro Azure](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

#### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>Nainstalujte řešení ServiceMap a InfrastructureInsights
Tato metoda zahrnuje šablony JSON, který určuje konfiguraci pro povolení součásti řešení ve vašem pracovním prostoru Log Analytics.

Pokud nejste obeznámeni s nasazováním prostředků pomocí šablony, naleznete v tématu:
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/resource-group-template-deploy.md)
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Pokud se rozhodnete používat rozhraní příkazového řádku Azure, musíte nejprve nainstalovat a používat rozhraní příkazového řádku místně. Musíte používat Azure CLI verze 2.0.27 nebo novější. Zjistěte verzi, spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku Azure, najdete v článku [instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Zkopírujte a vložte do souboru následující syntaxi JSON:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Uložte soubor jako *installsolutionsforvminsights.json* do místní složky.

1. Zachycení hodnoty *WorkspaceName*, *ResourceGroupName*, a *WorkspaceLocation*. Hodnota pro *WorkspaceName* je název pracovního prostoru Log Analytics. Hodnota pro *WorkspaceLocation* je pracovní prostor je definována v oblasti.

1. Jste připraveni k nasazení této šablony.
 
    * Do složky obsahující šablonu použijte následující příkazy Powershellu:

        ```powershell
        New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName <ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
        ```

        Změna konfigurace může trvat několik minut. Po dokončení se zobrazí zpráva, která je podobný následujícímu a zahrnuje výsledek:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Chcete-li pomocí Azure CLI, spusťte následující příkaz:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>

        The configuration change can take a few minutes to complete. When it's completed, a message is displayed that's similar to the following and includes the result:

        ```azurecli
        provisioningState       : Succeeded

### Enable by using Azure Policy
To enable Azure Monitor for VMs at scale in a way that helps ensure consistent compliance and the automatic enabling of the newly provisioned VMs, we recommend [Azure Policy](../../azure-policy/azure-policy-introduction.md). These policies:

* Deploy the Log Analytics agent and the Dependency agent.
* Report on compliance results.
* Remediate for non-compliant VMs.

To enable Azure Monitor for VMs by using Azure Policy in your tenant:

- Assign the initiative to a scope: management group, subscription, or resource group
- Review and remediate compliance results

For more information about assigning Azure Policy, see [Azure Policy overview](../../governance/policy/overview.md#policy-assignment) and review the [overview of management groups](../../governance/management-groups/index.md) before you continue.

The policy definitions are listed in the following table:

|Name |Description |Type |
|-----|------------|-----|
|[Preview]: Enable Azure Monitor for VMs |Enable Azure Monitor for the Virtual Machines (VMs) in the specified scope (management group, subscription, or resource group). Takes Log Analytics workspace as a parameter. |Initiative |
|[Preview]: Audit Dependency Agent Deployment – VM Image (OS) unlisted |Reports VMs as non-compliant if the VM Image (OS) isn't defined in the list and the agent isn't installed. |Policy |
|[Preview]: Audit Log Analytics Agent Deployment – VM Image (OS) unlisted |Reports VMs as non-compliant if the VM Image (OS) isn't defined in the list and the agent isn't installed. |Policy |
|[Preview]: Deploy Dependency Agent for Linux VMs |Deploy Dependency Agent for Linux VMs if the VM Image (OS) is defined in the list and the agent isn't installed. |Policy |
|[Preview]: Deploy Dependency Agent for Windows VMs |Deploy Dependency Agent for Windows VMs if the VM Image (OS) is defined in the list and the agent isn't installed. |Policy |
|[Preview]: Deploy Log Analytics Agent for Linux VMs |Deploy Log Analytics Agent for Linux VMs if the VM Image (OS) is defined in the list and the agent isn't installed. |Policy |
|[Preview]: Deploy Log Analytics Agent for Windows VMs |Deploy Log Analytics Agent for Windows VMs if the VM Image (OS) is defined in the list and the agent isn't installed. |Policy |

Standalone policy (not included with the initiative) is described here:

|Name |Description |Type |
|-----|------------|-----|
|[Preview]: Audit Log Analytics Workspace for VM - Report Mismatch |Report VMs as non-compliant if they aren't logging to the Log Analytics workspace specified in the policy/initiative assignment. |Policy |

#### Assign the Azure Monitor initiative
With this initial release, you can create the policy assignment only in the Azure portal. To understand how to complete these steps, see [Create a policy assignment from the Azure portal](../../governance/policy/assign-policy-portal.md).

1. To launch the Azure Policy service in the Azure portal, select **All services**, and then search for and select **Policy**.

1. In the left pane of the Azure Policy page, select **Assignments**.  
    An assignment is a policy that has been assigned to take place within a specific scope.
    
1. At the top of the **Policy - Assignments** page, select **Assign Initiative**.

1. On the **Assign Initiative** page, select the **Scope** by clicking the ellipsis (...), and select a management group or subscription.  
    In our example, a scope limits the policy assignment to a grouping of virtual machines for enforcement.
    
1. At the bottom of the **Scope** page, save your changes by selecting **Select**.

1. (Optional) To remove one or more resources from the scope, select **Exclusions**.

1. Select the **Initiative definition** ellipsis (...) to display the list of available definitions, select **[Preview] Enable Azure Monitor for VMs**, and then select **Select**.  
    The **Assignment name** box is automatically populated with the initiative name you selected, but you can change it. You can also add an optional description. The **Assigned by** box is automatically populated based on who is logged in, and this value is optional.
    
1. In the **Log Analytics workspace** drop-down list for the supported region, select a workspace.

    >[!NOTE]
    >If the workspace is beyond the scope of the assignment, grant *Log Analytics Contributor* permissions to the policy assignment's Principal ID. If you don't do this, you might see a deployment failure such as: `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ... `
    >To grant access, review [how to manually configure the managed identity](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
    >  
    The **Managed Identity** check box is selected, because the initiative being assigned includes a policy with the *deployIfNotExists* effect.
    
1. In the **Manage Identity location** drop-down list, select the appropriate region.

1. Select **Assign**.

#### Review and remediate the compliance results

You can learn how to review compliance results by reading [identify non-compliance results](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). In the left pane, select **Compliance**, and then locate the **[Preview] Enable Azure Monitor for VMs** initiative for VMs that aren't compliant according to the assignment you created.

![Policy compliance for Azure VMs](./media/vminsights-onboard/policy-view-compliance-01.png)

Based on the results of the policies included with the initiative, VMs are reported as non-compliant in the following scenarios:

* Log Analytics or the Dependency agent isn't deployed.  
    This scenario is typical for a scope with existing VMs. To mitigate it, deploy the required agents by [creating remediation tasks](../../governance/policy/how-to/remediate-resources.md) on a non-compliant policy.  
    - [Preview]: Deploy Dependency Agent for Linux VMs
    - [Preview]: Deploy Dependency Agent for Windows VMs
    - [Preview]: Deploy Log Analytics Agent for Linux VMs
    - [Preview]: Deploy Log Analytics Agent for Windows VMs

* VM Image (OS) isn't identified in the policy definition.  
    The criteria of the deployment policy include only VMs that are deployed from well-known Azure VM images. Check the documentation to see whether the VM OS is supported. If it isn't supported, duplicate the deployment policy and update or modify it to make the image compliant.  
    - [Preview]: Audit Dependency Agent Deployment – VM Image (OS) unlisted
    - [Preview]: Audit Log Analytics Agent Deployment – VM Image (OS) unlisted

* VMs aren't logging in to the specified Log Analytics workspace.  
    It's possible that some VMs in the initiative scope are logging in to a Log Analytics workspace other than the one that's specified in the policy assignment. This policy is a tool to identify which VMs are reporting to a non-compliant workspace.  
    - [Preview]: Audit Log Analytics Workspace for VM - Report Mismatch

### Enable with PowerShell
To enable Azure Monitor for VMs for multiple VMs or virtual machine scale sets, you can use the PowerShell script [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0), available from the Azure PowerShell Gallery. This script iterates through every virtual machine and virtual machine scale set in your subscription, in the scoped resource group that's specified by *ResourceGroup*, or to a single VM or virtual machine scale set that's specified by *Name*. For each VM or virtual machine scale set, the script verifies whether the VM extension is already installed. If the VM extension is not installed, the script tries to reinstall it. If the VM extension is installed, the script installs the Log Analytics and Dependency agent VM extensions.

This script requires Azure PowerShell module version 5.7.0 or later. Run `Get-Module -ListAvailable AzureRM` to find the version. If you need to upgrade, see [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps). If you're running PowerShell locally, you also need to run `Connect-AzureRmAccount` to create a connection with Azure.

To get a list of the script's argument details and example usage, run `Get-Help`.

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and the Dependency agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or re-configures following on VMs and VM Scale Sets:
    - Log Analytics VM Extension configured to supplied Log Analytics Workspace
    - Dependency Agent VM Extension

    Can be applied to:
    - Subscription
    - Resource Group in a Subscription
    - Specific VM/VM Scale Set
    - Compliance results of a policy for a VM or VM Extension

    Script will show you list of VMs/VM Scale Sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed will not install again.
    Use -ReInstall switch if you need to for example update the workspace.

    Use -WhatIf if you would like to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VMs are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VMs to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VMs/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Supported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VMs in a Resource Group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source, and to reinstall (move to a new workspace)
```

Následující příklad ukazuje pomocí příkazů Powershellu ve složce povolit monitorování Azure pro virtuální počítače a pochopení očekávaný výstup:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or VM ScaleSets matching criteria specified

VMs or VM ScaleSets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on above 2 VMs or VM Scale Sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example to update workspace

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already Onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="enable-for-a-hybrid-environment"></a>Povolit pro hybridní prostředí
Tato část vysvětluje, jak nasadit virtuální počítače nebo fyzické počítače, které jsou hostované ve vašem datovém centru nebo jiných cloudových prostředích pro monitorování Azure Monitor pro virtuální počítače.

Azure Monitor pro agenta závislostí mapování virtuálních počítačů nebude přenášet vlastní data a nevyžaduje žádné změny brány firewall nebo porty. Mapy dat je vždy přenášených v rámci agenta Log Analytics ve službě Azure Monitor, buď přímo nebo prostřednictvím [bránu OMS](../../azure-monitor/platform/gateway.md) Pokud zásady zabezpečení IT neumožňují počítače v síti pro připojení k Internetu.

Seznamte se s požadavky a metody nasazení pro [agenta Log Analytics Linux a Windows](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Kroky jsou v souhrnu následující:

1. Instalace agenta Log Analytics pro Windows nebo Linux.

1. Stáhněte a nainstalujte Azure Monitor pro virtuální počítače mapu závislostí agenta pro [Windows](https://aka.ms/dependencyagentwindows) nebo [Linux](https://aka.ms/dependencyagentlinux).

1. Povolte shromažďování čítačů výkonu.

1. Nasazení pro virtuální počítače Azure Monitor.

### <a name="install-the-dependency-agent-on-windows"></a>Instalace agenta závislostí na Windows
Můžete nainstalovat agenta závislostí ručně počítačích s Windows spuštěním `InstallDependencyAgent-Windows.exe`. Pokud spustíte tento spustitelný soubor bez jakýchkoli možností, spustí Průvodce instalací, zvoleném pro instalaci agenta interaktivně.

>[!NOTE]
>*Správce* instalace nebo odinstalace agenta jsou nutná oprávnění.

Následující tabulka obsahuje parametry, které podporují instalaci agenta z příkazového řádku.

| Parametr | Popis |
|:--|:--|
| /? | Vrátí seznam možností příkazového řádku. |
| /S | Provádí tichou instalaci bez zásahu uživatele. |

Například spusťte instalační program se `/?` parametr, typ **InstallDependencyAgent Windows.exe /?**.

Soubory agenta závislostí Windows jsou nainstalované v *agenta závislostí C:\Program Files\Microsoft* ve výchozím nastavení. Pokud se nepodaří spustit po dokončení instalace agenta závislostí, zkontrolujte protokoly podrobné informace o chybě. Adresář protokolu je *%Programfiles%\Microsoft závislost Agent\logs*.

### <a name="install-the-dependency-agent-on-linux"></a>Instalace agenta závislostí v Linuxu
Agent závislostí je nainstalovaný na servery s Linuxem z *InstallDependencyAgent Linux64.bin*, skript prostředí samorozbalovací binárním souborem. Soubor můžete spustit pomocí `sh` nebo přidejte oprávnění k souboru, samotné.

>[!NOTE]
> K instalaci nebo konfiguraci tohoto agenta se vyžaduje přístup uživatele root.
>

| Parametr | Popis |
|:--|:--|
| – Nápověda | Získá seznam parametrů příkazového řádku. |
| -s | Provede tichou instalaci bez zobrazení výzev uživateli. |
| – Zkontrolujte | Kontrola oprávnění a operačního systému, ale nechcete nainstalovat agenta. |

Například ke spuštění instalačního programu s `-help` parametr, typ **InstallDependencyAgent Linux64.bin – Nápověda**.

Instalace agenta závislostí Linux jako uživatel root spuštěním následujícího příkazu `sh InstallDependencyAgent-Linux64.bin`.

Pokud agenta závislostí nespustí, zkontrolujte protokoly podrobné informace o chybě. U agentů Linuxu adresáři protokolu není */var/opt/microsoft/dependency-agent/log*.

Soubory pro agenta závislostí jsou umístěny v následujících adresářích:

| Soubory | Umístění |
|:--|:--|
| Základní soubory | /opt/microsoft/dependency-agent |
| Soubory protokolu | /var/opt/microsoft/dependency-agent/log |
| Konfigurační soubory | /etc/opt/microsoft/dependency-agent/config |
| Spustitelné soubory služby | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Binární soubory úložiště | /var/opt/microsoft/dependency-agent/storage |

### <a name="enable-performance-counters"></a>Povolit čítače výkonu
Pokud pracovní prostor Log Analytics, který je odkazován řešení ještě nenakonfigurovala získat čítače výkonu, vyžadují řešení, musíte je povolit. Můžete tak učinit v některém ze dvou způsobů:
* Ručně, jak je popsáno v [Windows a Linuxem zdroje dat výkonu do Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Stažením a instalací, který je k dispozici skript prostředí PowerShell [Galerie prostředí PowerShell pro Azure](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

### <a name="deploy-azure-monitor-for-vms"></a>Nasazení pro virtuální počítače Azure Monitor
Tato metoda zahrnuje šablony JSON, který určuje konfiguraci pro povolení součásti řešení ve vašem pracovním prostoru Log Analytics.

Pokud nejste obeznámeni s nasazováním prostředků pomocí šablony, naleznete v tématu:
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/resource-group-template-deploy.md)
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Pokud se rozhodnete používat rozhraní příkazového řádku Azure, musíte nejprve nainstalovat a používat rozhraní příkazového řádku místně. Musíte používat Azure CLI verze 2.0.27 nebo novější. Zjistěte verzi, spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku Azure, najdete v článku [instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

#### <a name="create-and-execute-a-template"></a>Vytvoření a provedení šablony

1. Zkopírujte a vložte do souboru následující syntaxi JSON:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Uložte soubor jako *installsolutionsforvminsights.json* do místní složky.

1. Upravte hodnoty *WorkspaceName*, *ResourceGroupName*, a *WorkspaceLocation*. Hodnota pro *WorkspaceName* je úplné ID prostředku pracovního prostoru Log Analytics, která zahrnuje název pracovního prostoru. Hodnota pro *WorkspaceLocation* je pracovní prostor je definována v oblasti.

1. Jste připraveni nasadit tuto šablonu pomocí následujícího příkazu Powershellu:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Změna konfigurace může trvat několik minut. Po dokončení se zobrazí zpráva, která je podobný následujícímu a zahrnuje výsledek:

    ```powershell
    provisioningState       : Succeeded
    ```
Po povolení sledování, může trvat přibližně 10 minut, než můžete zobrazit stav a metriky pro počítač hybridní.

## <a name="performance-counters-enabled"></a>Čítače výkonu povolena
Azure Monitor pro virtuální počítače lze konfigurovat pracovní prostor Log Analytics ke shromažďování čítačů výkonu, které jsou používány řešení. V následující tabulce jsou uvedeny objekty a čítače nakonfigurované řešení, které byly shromážděny každých 60 sekund.

### <a name="windows-performance-counters"></a>Čítače výkonu Windows

|Název objektu |Název čítače |
|------------|-------------|
|Logický disk |% Volného místa |
|Logický disk |Střední Doba disku/čtení |
|Logický disk |Střední Doba disku/přenos |
|Logický disk |Střední Doby disku/zápis |
|Logický disk |Bajty disku/s |
|Logický disk |Bajty čtení z disku/s |
|Logický disk |Čtení disku/s |
|Logický disk |Přenosy disku/s |
|Logický disk |Bajty zapisování na disk/s |
|Logický disk |Zápis disku/s |
|Logický disk |Volné megabajty |
|Memory (Paměť) |Počet MB k dispozici |
|Síťový adaptér |Přijaté bajty/s |
|Síťový adaptér |Odeslané bajty/s |
|Procesor |% Času procesoru |

### <a name="linux-performance-counters"></a>Čítače výkonu Linuxu

|Název objektu |Název čítače |
|------------|-------------|
|Logický Disk |% Využitého místa |
|Logický Disk |Bajty čtení z disku/s |
|Logický Disk |Čtení disku/s |
|Logický Disk |Přenosy disku/s |
|Logický Disk |Bajty zapisování na disk/s |
|Logický Disk |Zápis disku/s |
|Logický Disk |Volné megabajty |
|Logický Disk |Bajtů logického disku/s |
|Memory (Paměť) |Dostupná paměť v MB |
|Síť |Celkový počet přijatých bajtů |
|Síť |Celkový počet bajtů přenesených |
|Procesor |% Času procesoru |

## <a name="diagnostic-and-usage-data"></a>Diagnostická data a data použití
Microsoft automaticky shromažďuje data o využití a výkonu prostřednictvím používání služby Azure Monitor. Tato data Microsoft používá k poskytování a vylepšování kvality, zabezpečení a integrity služby. Poskytnout přesné a efektivní možnosti pro odstraňování potíží, data z mapování funkce obsahuje informace o konfiguraci vašeho softwaru, jako je operační systém a verze, IP adresu, název DNS a název pracovní stanice. Společnost Microsoft nebude shromažďovat jména, adresy ani jiné kontaktní údaje.

Další informace o shromažďování a používání dat najdete v článku [prohlášení o ochraně osobních údajů Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]
## <a name="next-steps"></a>Další postup

Teď, když pro váš virtuální počítač je zapnuté monitorování, tyto informace jsou dostupné pro analýzy a monitorování Azure pro virtuální počítače. Další informace o použití funkce stavu, najdete v článku [zobrazení monitorování Azure pro virtuální počítače stav](vminsights-health.md). Chcete-li zobrazit závislosti zjištěných aplikací, najdete v článku [zobrazení monitorování Azure pro virtuální počítače mapu](vminsights-maps.md).
