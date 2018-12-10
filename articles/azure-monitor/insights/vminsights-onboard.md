---
title: Nasazení pro virtuální počítače ve verzi Preview Azure Monitor | Dokumentace Microsoftu
description: Tento článek popisuje, jak nasadit a nakonfigurovat monitorování Azure pro virtuální počítače, abyste je mohli začít, pochopení, jaký je výkon distribuované aplikace a problémy se stavem, které byly identifikovány.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/07/2018
ms.author: magoedte
ms.openlocfilehash: b78332933c7c406cd938091b578786467a73248f
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141777"
---
# <a name="deploy-azure-monitor-for-vms-preview"></a>Nasazení Azure Monitor pro virtuální počítače ve verzi Preview
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

V hybridním prostředí, můžete stáhnout a nainstalovat agenta závislostí v některém ze dvou způsobů: ručně nebo pomocí metody automatizované nasazení pro virtuální počítače, které jsou hostované mimo Azure. 

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

### <a name="enable-by-using-azure-policy"></a>Povolení s využitím zásad Azure
Pokud chcete povolit monitorování Azure pro virtuální počítače ve velkém měřítku tak, že pomáhá tak zajistit konzistentní dodržování předpisů a automatické povolení nově zřízených virtuálních počítačů, doporučujeme [Azure Policy](../../azure-policy/azure-policy-introduction.md). Tyto zásady:

* Nasaďte agenta Log Analytics a agenta závislostí. 
* Sestav o výsledcích dodržování předpisů. 
* Opravte nevyhovující virtuálních počítačů.

Povolení monitorování Azure pro virtuální počítače s využitím zásad Azure ve vašem tenantovi: 

- Přiřadit iniciativu obor: skupina pro správu, předplatné nebo skupinu prostředků 
- Zkontrolujte a opravte výsledků kontroly kompatibility  

Další informace o přiřazování zásad Azure najdete v tématu [Přehled služby Azure Policy](../../governance/policy/overview.md#policy-assignment) a zkontrolujte [přehled skupin pro správu](../../governance/management-groups/index.md) předtím, než budete pokračovat. 

Definice zásad jsou uvedeny v následující tabulce: 

|Název |Popis |Typ |  
|-----|------------|-----|  
|[Preview]: Povolit monitorování Azure pro virtuální počítače |Povolte monitorování Azure pro virtuální počítače (VM) v zadaném oboru (skupiny pro správu, předplatné nebo skupinu prostředků). Jako parametr používá pracovní prostor Log Analytics. |Iniciativa |  
|[Preview]: nasazení agenta závislostí auditu – obrázku (operačního systému virtuálního počítače) neuvedené v seznamu |Virtuální počítače ohlásí jako nedodržující předpisy, pokud Image virtuálního počítače (OS) není definována v seznamu a agent není nainstalovaný. |Zásada |  
|[Preview]: nasazení agenta auditu Log Analytics – obrázku (operačního systému virtuálního počítače) neuvedené v seznamu |Virtuální počítače ohlásí jako nedodržující předpisy, pokud Image virtuálního počítače (OS) není definována v seznamu a agent není nainstalovaný. |Zásada |  
|[Preview]: nasazení agenta závislosti pro virtuální počítače s Linuxem |Nasazení agenta závislostí pro virtuální počítače s Linuxem, pokud Image virtuálního počítače (OS) je definován v seznamu a agent není nainstalovaný. |Zásada |  
|[Preview]: nasazení agenta závislosti pro virtuální počítače s Windows |Nasazení agenta závislosti pro Windows VMs Pokud Image virtuálního počítače (OS) je definován v seznamu a agent není nainstalovaný. |Zásada |  
|[Preview]: nasadit agenta Log Analytics pro virtuální počítače s Linuxem |Nasaďte agenta Log Analytics pro virtuální počítače s Linuxem, když v seznamu je definována Image virtuálního počítače (OS) a agent není nainstalovaný. |Zásada |  
|[Preview]: nasadit agenta Log Analytics pro virtuální počítače s Windows |Nasaďte agenta Log Analytics pro Windows VMs, když v seznamu je definována Image virtuálního počítače (OS) a agent není nainstalovaný. |Zásada |  

Samostatné zásady (není zahrnutá v iniciativě) je popsaný tady: 

|Název |Popis |Typ |  
|-----|------------|-----|  
|[Preview]: Auditovat pracovní prostor Log Analytics pro virtuální počítač –, nahlásit neshodu |Sestavy virtuálních počítačů jako nedodržující předpisy, pokud nejsou protokolování do pracovního prostoru Log Analytics zadaná v přiřazení zásady nebo iniciativa. |Zásada |

#### <a name="assign-the-azure-monitor-initiative"></a>Přiřadit iniciativu Azure Monitor
Tato počáteční verze můžete vytvořit přiřazení zásady jenom na webu Azure portal. Jak provést tyto kroky najdete v tématu [vytvoření přiřazení zásady z portálu Azure portal](../../governance/policy/assign-policy-portal.md). 

1. Chcete-li spustit službu Azure Policy na webu Azure Portal, vyberte **všechny služby**a poté vyhledejte a vyberte **zásady**. 
1. V levém podokně na stránku služby Azure Policy vyberte **přiřazení**.  
    Přiřazení je zásada, která byla přiřazena, aby proběhla v rámci zadaného oboru.
1. V horní části **zásady – přiřazení** stránce **přiřazení iniciativy**.
1. Na **přiřazení iniciativy** stránky, vyberte **oboru** tím, že kliknete na tři tečky (...) a vyberte skupinu pro správu nebo předplatného.  
    V našem příkladu omezuje obor přiřazení zásady pro seskupení virtuálních počítačů pro vynucení.
1. V dolní části **oboru** stránky uložte provedené změny tak, že vyberete **vyberte**.
1. (Volitelné) Chcete-li odebrat jednu nebo více prostředků z oboru, vyberte **vyloučení**. 
1. Vyberte **definice iniciativy** vyberte tři tečky (...) zobrazte seznam dostupných definic  **[Preview] povolit monitorování Azure pro virtuální počítače**a pak vyberte  **Vyberte**.  
    **Název přiřazení** pole se automaticky vyplní název iniciativy jste vybrali, ale můžete ho změnit. Můžete také přidat volitelný popis. **Přiřadil** pole se vyplní automaticky, podle který je přihlášen, a tato hodnota je volitelná.
1. V **pracovní prostor Log Analytics** rozevírací seznam pro podporované oblasti, vyberte pracovní prostor.

    >[!NOTE]
    >Pokud pracovní prostor je mimo rozsah přiřazení, udělit *Přispěvatel Log Analytics* oprávnění ID přiřazení zásady instanční objekt. Pokud to neuděláte, může se zobrazit selhání nasazení jako například: `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ... ` Pokud chcete udělit přístup, zkontrolujte [jak ručně nakonfigurovat spravovanou identitu](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
    >  
    **Identity spravované** zaškrtávací políčko je zaškrtnuto, protože obsahuje přiřazení iniciativy zásad s *deployIfNotExists* vliv. 
1. V **spravovat Identity umístění** rozevíracího seznamu vyberte příslušnou oblast. 
1. Vyberte **Přiřadit**.

#### <a name="review-and-remediate-the-compliance-results"></a>Zkontrolujte a opravte výsledků kontroly kompatibility 

Můžete zjistěte, jak zkontrolovat výsledky kontroly dodržování předpisů načtením [identifikovat nedodržení předpisů výsledky](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). V levém podokně vyberte **dodržování předpisů**a potom  **[Preview] povolit monitorování Azure pro virtuální počítače** iniciativy pro virtuální počítače, které nedodržují předpisy, podle přiřazení jste vytvořili.

![Zásady dodržování předpisů pro virtuální počítače Azure](./media/vminsights-onboard/policy-view-compliance-01.png)

Na základě výsledků Zásady iniciativy je součástí virtuálních počítačů označené jako nedodržující předpisy v následujících scénářích:  
  
* Log Analytics nebo agenta závislostí není nasazený. 
   Tento scénář je typický pro obor s existující virtuální počítače. Jak ji zmírnit, nasazení požadovaných agentů podle [vytváření úloh nápravy](../../governance/policy/how-to/remediate-resources.md) nekompatibilní zásady.   
 
    - [Preview]: Deploy Dependency Agent for Linux VMs   
    - [Preview]: Deploy Dependency Agent for Windows VMs  
    - [Preview]: Deploy Log Analytics Agent for Linux VMs  
    - [Preview]: Deploy Log Analytics Agent for Windows VMs  

* Virtuální počítač Image operačního systému není identifikované v definici zásad. 
   Kritéria zásad nasazení zahrnovat jenom virtuální počítače, které jsou nasazeny z dobře známé imagí virtuálních počítačů Azure. Vyhledejte v dokumentaci najdete v článku, zda je podporován operační systém virtuálního počítače. Pokud není podporován, duplicitní zásady nasazení a aktualizace nebo upravte ho tak, aby image kompatibilní. 
  
    - [Preview]: nasazení agenta závislostí auditu – obrázku (operačního systému virtuálního počítače) neuvedené v seznamu  
    - [Preview]: nasazení agenta auditu Log Analytics – obrázku (operačního systému virtuálního počítače) neuvedené v seznamu

* Virtuální počítače nejsou přihlášení k zadanému pracovnímu prostoru Log Analytics.  
    Je možné, že některé virtuální počítače v rámci iniciativy jsou přihlášení k pracovnímu prostoru Log Analytics, než je ten, který je zadaná v přiřazení zásady. Tato zásada je nástroj, který určíte, které virtuální počítače se hlásí nekompatibilní pracovního prostoru. 
 
    - [Preview]: Audit Log Analytics Workspace for VM - Report Mismatch  

### <a name="enable-with-powershell"></a>Povolit pomocí Powershellu
Povolit monitorování Azure pro virtuální počítače pro více virtuálních počítačů nebo škálovacích sad virtuálních počítačů, můžete použít skript prostředí PowerShell [instalace VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0), která je dostupná v galerii Azure Powershellu. Tento skript projde všechny virtuální počítače a virtuální počítač škálovací sady v rámci vašeho předplatného, ve skupině prostředků s vymezeným oborem, která je zadána *ResourceGroup*, nebo do jednoho virtuálního počítače nebo virtuálního počítače škálovací sady, která je zadána *Název*. Pro každý virtuální počítač nebo virtuální počítač ve škálovací sadě skript ověří, zda je rozšíření virtuálního počítače již nainstalován. Pokud není nainstalované rozšíření virtuálního počítače, skript se pokusí znovu nainstalovat. Pokud je nainstalované rozšíření virtuálního počítače, skript nainstaluje rozšíření virtuálních počítačů agenta Log Analytics a závislostí.  

Tento skript vyžaduje modul Azure PowerShell verze 5.7.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, musíte také spustit `Connect-AzureRmAccount` vytvořit připojení k Azure.

Pokud chcete získat seznam argumentů podrobnosti a příklady použití skriptu, spusťte `Get-Help`.  

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
