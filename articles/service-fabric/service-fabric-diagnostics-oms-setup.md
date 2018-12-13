---
title: Azure Service Fabric – nastavení monitorování pomocí Log Analytics | Dokumentace Microsoftu
description: Zjistěte, jak nastavit službu Log Analytics pro vizualizaci a analyzování události k monitorování clusterů Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 00fc04afd26da2ef5741eec308835bb8c897c26b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53077337"
---
# <a name="set-up-log-analytics-for-a-cluster"></a>Nastavení Log Analytics pro cluster

Log Analytics je naše doporučení pro monitorování událostí na úrovni clusteru. Můžete nastavit pracovní prostor Log Analytics prostřednictvím Azure Resource Manageru, Powershellu nebo Azure Marketplace. Pokud chcete zachovat aktualizovanou šablonu Resource Manageru pro budoucí použití vašeho nasazení, můžete stejnou šablonu použijte k nastavení prostředí Log Analytics. Nasazení prostřednictvím webu Marketplace a je jednodušší, pokud už máte cluster nasazený s povolenou diagnostikou. Pokud nemáte přístup na úrovni předplatného v účtu, do které nasazujete, nasaďte pomocí Powershellu nebo šablony Resource Manageru.

> [!NOTE]
> K nastavení Log Analytics pro monitorování vašeho clusteru, musíte mít zapnutou zobrazení událostí úrovně clusteru nebo na úrovni platformy diagnostiku. Odkazovat na [nastavení diagnostiky v clusterech Windows](service-fabric-diagnostics-event-aggregation-wad.md) a [nastavení diagnostiky v clusterech s Linuxem](service-fabric-diagnostics-event-aggregation-lad.md) Další informace

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Nasazení pracovního prostoru Log Analytics pomocí webu Azure Marketplace

Pokud chcete přidat pracovní prostor Log Analytics po nasazení clusteru, přejděte na Azure Marketplace na portálu a vyhledejte **analýza služby Service Fabric**. Toto je vlastní řešení pro Service Fabric nasazení, který obsahuje data specifická pro Service Fabric. V tomto procesu vytvoříte řešení (řídicí panel, aby insights) i pracovního prostoru (agregace podkladových dat clusteru).

1. Vyberte **nový** v levé navigační nabídce. 

2. Vyhledejte **Service Fabric Analytics**. Vyberte prostředek, který se zobrazí.

3. Vyberte **Vytvořit**.

    ![Analýza služby Service Fabric na webu Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. V okně vytváření analýza služby Service Fabric, zvolte **vyberte pracovní prostor** pro **pracovní prostor OMS** pole a potom **vytvořit nový pracovní prostor**. Vyplňte požadované položky. Jediným požadavkem je, že předplatné pro cluster Service Fabric a pracovní prostor je stejný. Když ověříte zadané pracovního prostoru začne nasazovat. Nasazení trvá jenom pár minut.

5. Až budete hotovi, vyberte **vytvořit** znovu v dolní části okna vytváření analýza služby Service Fabric. Ujistěte se, že nový pracovní prostor se zobrazí v části **pracovní prostor OMS**. Tato akce přidá řešení do pracovního prostoru, který jste vytvořili.

Pokud používáte Windows, pokračujte následujícími kroky pro připojení Log Analytics do účtu úložiště, kde jsou uložené vaše události clusteru. 

>[!NOTE]
>Povolení této funkce pro clustery s Linuxem ještě není k dispozici. 

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>Připojení k vašemu clusteru pracovní prostor Log Analytics 

1. Pracovní prostor se musí být připojen k diagnostická data z vašeho clusteru. Přejděte do skupiny prostředků, ve které jste vytvořili řešení analýza služby Service Fabric. Vyberte **ServiceFabric\<nameOfWorkspace\>**  a přejít na stránku jeho Přehled. Odtud můžete změnit nastavení řešení, pracovní prostor nastavení a přístup k pracovnímu prostoru Log Analytics.

2. V levé navigační nabídce v části **zdroje dat pracovního prostoru**vyberte **protokoly účtů úložiště**.

3. Na **protokoly účtu úložiště** stránce **přidat** v horní části, chcete-li přidat váš cluster protokoly do pracovního prostoru.

4. Vyberte **účtu úložiště** přidat příslušný účet vytvořený ve vašem clusteru. Pokud jste použili výchozí název, účet úložiště je **sfdg\<resourceGroupName\>**. Můžete to ověřit také pomocí šablony Azure Resource Manageru použít k nasazení clusteru, kontrolou hodnoty používané pro **applicationDiagnosticsStorageAccountName**. Pokud název není uveden, posuňte se dolů a vyberte **načíst další**. Vyberte název účtu úložiště.

5. Určení datového typu. Nastavte ho na **události Service Fabric**.

6. Ujistěte se, že zdroji se automaticky nastaví na **WADServiceFabric\*EventTable**.

7. Vyberte **OK** k připojení vašeho pracovního prostoru do vašeho clusteru protokolů.

    ![Přidejte protokoly účtu úložiště do služby Log Analytics](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

Účet se teď zobrazí se jako součást vašeho účtu úložiště protokolů v zdroje dat pracovního prostoru.

Analýza služby Service Fabric řešení jste přidali v pracovním prostoru Log Analytics, která je teď správně připojený k platforma vašeho clusteru a tabulku protokolu aplikace. Stejným způsobem můžete přidat další zdroje do pracovního prostoru.


## <a name="deploy-log-analytics-with-azure-resource-manager"></a>Nasadit Log Analytics pomocí Azure Resource Manageru

Při nasazování clusteru pomocí šablony Resource Manageru šablony vytvoří nový pracovní prostor Log Analytics, přidá řešení Service Fabric do pracovního prostoru a nakonfiguruje ho na čtení dat z tabulky odpovídající úložiště.

Můžete použít a upravit [Tato ukázková šablona](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) podle svých požadavků. Tato šablona provádí tyto kroky

* Vytvoří cluster Service Fabric s 5 uzly
* Vytvoří pracovní prostor Log Analytics a řešení v Service Fabric
* Nakonfiguruje agenta Log Analytics pro shromažďování a odesílání 2 ukázky čítače výkonu do pracovního prostoru
* Nakonfiguruje WAD collect Service Fabric a odesílá je do tabulky v úložišti Azure (WADServiceFabric * EventTable)
* Konfiguruje pracovní prostor Log Analytics ke čtení událostí z těchto tabulek


Šablony můžete nasadit jako upgrade Resource Manageru do clusteru s použitím `New-AzureRmResourceGroupDeployment` rozhraní API v modul AzureRM Powershellu. Příklad příkazu by byl:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

Azure Resource Manager zjistí, že tento příkaz je aktualizace pro existující prostředek. Zpracuje pouze změny mezi šablony řízení stávající nasazení a novou šablonu, která je k dispozici.

## <a name="deploy-log-analytics-with-azure-powershell"></a>Nasadit Log Analytics pomocí Azure Powershellu

Prostředku Log Analytics prostřednictvím prostředí PowerShell můžete také nasadit pomocí `New-AzureRmOperationalInsightsWorkspace` příkazu. Pokud chcete použít tuto metodu, ujistěte se, že jste si nainstalovali [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.1.1). Pomocí tohoto skriptu můžete vytvořit nový pracovní prostor Log Analytics a k němu přidejte řešení pro Service Fabric: 

```PowerShell

$SubscriptionName = "<Name of your subscription>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<Log Analytics workspace name>"
$solution = "ServiceFabric"

# Log in to Azure and access the correct subscription
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

Jakmile budete hotovi, postupujte podle kroků v předchozí části pro připojení k účtu pro příslušné úložiště Log Analytics.

Můžete také přidat další řešení a provádět další úpravy do pracovního prostoru Log Analytics pomocí Powershellu. Další informace najdete v tématu [spravovat Log Analytics pomocí Powershellu](../azure-monitor/platform/powershell-workspace-configuration.md).

## <a name="next-steps"></a>Další postup
* [Nasadit agenta Log Analytics](service-fabric-diagnostics-oms-agent.md) na svých uzlech shromažďování čítačů výkonu a shromáždit statistiky dockeru a protokoly pro vaše kontejnery
* Seznamte se s [prohledávání protokolů a dotazování](../log-analytics/log-analytics-log-searches.md) funkce nabízená v rámci služby Log Analytics
* [Návrhář zobrazení použít k vytváření vlastních zobrazení v Log Analytics](../azure-monitor/platform/view-designer.md)
