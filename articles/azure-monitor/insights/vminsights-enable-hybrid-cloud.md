---
title: Povolit monitorování Azure (preview) pro hybridní prostředí | Dokumentace Microsoftu
description: Tento článek popisuje, jak povolit Azure Monitor pro virtuální počítače pro hybridní cloudové prostředí, která obsahuje jeden nebo více virtuálních počítačů.
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
ms.date: 06/07/2019
ms.author: magoedte
ms.openlocfilehash: 89970802ff21a254fae0604e50ea2b8b5901f95a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052007"
---
# <a name="enable-azure-monitor-for-vms-preview-for-a-hybrid-environment"></a>Povolit monitorování Azure pro hybridní prostředí pro virtuální počítače (preview)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Tento článek vysvětluje, jak povolit Azure Monitor pro virtuální počítače (preview) pro virtuální počítače nebo fyzické počítače, které jsou hostované ve vašem datovém centru nebo jiných cloudovém prostředí. Na konci tohoto procesu, který se úspěšně začali jste monitorování virtuálních počítačů ve vašem prostředí a další, pokud se vyskytly problémy s výkonem nebo dostupností. 

Před Začínáme pracovat, nezapomeňte se podívat [požadavky](vminsights-enable-overview.md) a ověřte vaše předplatné a prostředky splňují požadavky. Seznamte se s požadavky a metody nasazení pro [agenta Log Analytics Linux a Windows](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>Azure Monitor pro agenta závislostí mapování virtuálních počítačů nebude přenášet vlastní data a nevyžaduje žádné změny brány firewall nebo porty. Mapy dat je vždy přenášených v rámci agenta Log Analytics ve službě Azure Monitor, buď přímo nebo prostřednictvím [bránu OMS](../../azure-monitor/platform/gateway.md) Pokud zásady zabezpečení IT neumožňují počítače v síti pro připojení k Internetu.

Úkoly k dokončení této úlohy se dají shrnout takto:

1. Instalace agenta Log Analytics pro Windows nebo Linux. Před instalací agenta, zkontrolujte [přehled agenta Log Analytics](../platform/log-analytics-agent.md) článku vám pomohou pochopit požadavky na systém a metody nasazení.  

2. Stáhněte a nainstalujte Azure Monitor pro virtuální počítače mapu závislostí agenta pro [Windows](https://aka.ms/dependencyagentwindows) nebo [Linux](https://aka.ms/dependencyagentlinux).

3. Povolte shromažďování čítačů výkonu.

4. Nasazení pro virtuální počítače Azure Monitor.

## <a name="install-the-dependency-agent-on-windows"></a>Instalace agenta závislostí na Windows
Můžete nainstalovat agenta závislostí ručně počítačích s Windows spuštěním `InstallDependencyAgent-Windows.exe`. Pokud spustíte tento spustitelný soubor bez jakýchkoli možností, spustí Průvodce instalací, zvoleném pro instalaci agenta interaktivně.

>[!NOTE]
>*Správce* instalace nebo odinstalace agenta jsou nutná oprávnění.

Následující tabulka obsahuje parametry, které podporují instalaci agenta z příkazového řádku.

| Parametr | Popis |
|:--|:--|
| /? | Vrátí seznam možností příkazového řádku. |
| /S | Provádí tichou instalaci bez zásahu uživatele. |

Například spusťte instalační program se `/?` parametr, typ **InstallDependencyAgent Windows.exe /?** .

Soubory agenta závislostí Windows jsou nainstalované v *agenta závislostí C:\Program Files\Microsoft* ve výchozím nastavení. Pokud se nepodaří spustit po dokončení instalace agenta závislostí, zkontrolujte protokoly podrobné informace o chybě. Adresář protokolu je *%Programfiles%\Microsoft závislost Agent\logs*.

## <a name="install-the-dependency-agent-on-linux"></a>Instalace agenta závislostí v Linuxu
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

## <a name="enable-performance-counters"></a>Povolit čítače výkonu
Pokud pracovní prostor Log Analytics, který je odkazován řešení ještě nenakonfigurovala získat čítače výkonu, vyžadují řešení, musíte je povolit. Můžete tak učinit v některém ze dvou způsobů:
* Ručně, jak je popsáno v [Windows a Linuxem zdroje dat výkonu do Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Stažením a instalací, který je k dispozici skript prostředí PowerShell [Galerie prostředí PowerShell pro Azure](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>Nasazení pro virtuální počítače Azure Monitor
Tato metoda zahrnuje šablony JSON, který určuje konfiguraci pro povolení součásti řešení ve vašem pracovním prostoru Log Analytics.

Pokud nejste obeznámeni s nasazováním prostředků pomocí šablony, naleznete v tématu:
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/resource-group-template-deploy.md)
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Pokud se rozhodnete používat rozhraní příkazového řádku Azure, musíte nejprve nainstalovat a používat rozhraní příkazového řádku místně. Musíte používat Azure CLI verze 2.0.27 nebo novější. Zjistěte verzi, spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku Azure, najdete v článku [instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="create-and-execute-a-template"></a>Vytvoření a provedení šablony

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

1. Jste připraveni nasadit tuto šablonu pomocí následujícího příkazu Powershellu:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Změna konfigurace může trvat několik minut. Po dokončení se zobrazí zpráva, která je podobný následujícímu a zahrnuje výsledek:

    ```powershell
    provisioningState       : Succeeded
    ```
   Po povolení sledování, může trvat přibližně 10 minut, než můžete zobrazit stav a metriky pro počítač hybridní.

## <a name="next-steps"></a>Další postup

Teď, když je zapnuté monitorování pro vaše virtuální počítače, tyto informace jsou dostupné pro analýzy a monitorování Azure pro virtuální počítače. Další informace o použití funkce stavu, najdete v článku [zobrazení monitorování Azure pro virtuální počítače stav](vminsights-health.md). Chcete-li zobrazit závislosti zjištěných aplikací, najdete v článku [zobrazení monitorování Azure pro virtuální počítače mapu](vminsights-maps.md). Pokud chcete identifikovat problémová místa a celkové využití výkonu vašich virtuálních počítačů, přečtěte si téma [zobrazení výkonu virtuálních počítačů Azure](vminsights-performance.md), nebo chcete-li zobrazit závislosti zjištěných aplikací, najdete v článku [zobrazení monitorování Azure pro virtuální počítače mapu](vminsights-maps.md).