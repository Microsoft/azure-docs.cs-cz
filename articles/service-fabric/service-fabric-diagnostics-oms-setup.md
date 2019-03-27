---
title: Azure Service Fabric – nastavení monitorování pomocí protokolů Azure Monitor | Dokumentace Microsoftu
description: Zjistěte, jak nastavit protokoly Azure monitoru pro vizualizaci a analyzování události k monitorování clusterů Azure Service Fabric.
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
ms.date: 02/20/2019
ms.author: srrengar
ms.openlocfilehash: ca5dd4a7c12a68a549f081ad62db1736c9c68837
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58483161"
---
# <a name="set-up-azure-monitor-logs-for-a-cluster"></a>Nastavte si protokoly Azure monitoru pro cluster

Protokoly služby Azure Monitor je naše doporučení pro monitorování událostí na úrovni clusteru. Můžete nastavit pracovní prostor Log Analytics prostřednictvím Azure Resource Manageru, Powershellu nebo Azure Marketplace. Pokud chcete zachovat aktualizovanou šablonu Resource Manageru pro budoucí použití vašeho nasazení, můžete stejnou šablonu použijte k nastavení prostředí protokoly Azure monitoru. Nasazení prostřednictvím webu Marketplace a je jednodušší, pokud už máte cluster nasazený s povolenou diagnostikou. Pokud nemáte přístup na úrovni předplatného v účtu, do které nasazujete, nasaďte pomocí Powershellu nebo šablony Resource Manageru.

> [!NOTE]
> Pokud chcete nastavit protokoly Azure Monitor k monitorování vašeho clusteru, musíte mít zapnutou zobrazení událostí úrovně clusteru nebo na úrovni platformy diagnostiku. Odkazovat na [nastavení diagnostiky v clusterech Windows](service-fabric-diagnostics-event-aggregation-wad.md) a [nastavení diagnostiky v clusterech s Linuxem](service-fabric-diagnostics-event-aggregation-lad.md) Další informace

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Nasazení pracovního prostoru Log Analytics pomocí webu Azure Marketplace

Pokud chcete přidat pracovní prostor Log Analytics po nasazení clusteru, přejděte na Azure Marketplace na portálu a vyhledejte **analýza služby Service Fabric**. Toto je vlastní řešení pro Service Fabric nasazení, který obsahuje data specifická pro Service Fabric. V tomto procesu vytvoříte řešení (řídicí panel, aby insights) i pracovního prostoru (agregace podkladových dat clusteru).

1. Vyberte **nový** v levé navigační nabídce. 

2. Vyhledejte **Service Fabric Analytics**. Vyberte prostředek, který se zobrazí.

3. Vyberte **Vytvořit**.

    ![Analýza služby Service Fabric na webu Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. V okně vytváření analýza služby Service Fabric, zvolte **vyberte pracovní prostor** pro **pracovní prostor OMS** pole a potom **vytvořit nový pracovní prostor**. Vyplňte požadované položky. Jediným požadavkem je, že předplatné pro cluster Service Fabric a pracovní prostor je stejný. Když ověříte zadané pracovního prostoru začne nasazovat. Nasazení trvá jenom pár minut.

5. Až budete hotovi, vyberte **vytvořit** znovu v dolní části okna vytváření analýza služby Service Fabric. Ujistěte se, že nový pracovní prostor se zobrazí v části **pracovní prostor OMS**. Tato akce přidá řešení do pracovního prostoru, který jste vytvořili.

Pokud používáte Windows, pokračujte následujícími kroky pro připojení protokoly Azure monitoru k účtu úložiště, kde jsou uložené vaše události clusteru. 

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

    ![Přidejte protokoly účtu úložiště pro protokoly Azure monitoru](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

Účet se teď zobrazí se jako součást vašeho účtu úložiště protokolů v zdroje dat pracovního prostoru.

Analýza služby Service Fabric řešení jste přidali v pracovním prostoru Log Analytics, která je teď správně připojený k platforma vašeho clusteru a tabulku protokolu aplikace. Stejným způsobem můžete přidat další zdroje do pracovního prostoru.


## <a name="deploy-azure-monitor-logs-with-azure-resource-manager"></a>Nasazení Azure Monitor protokolů pomocí Azure Resource Manageru

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

## <a name="deploy-azure-monitor-logs-with-azure-powershell"></a>Nasazení Azure Monitor protokolů pomocí Azure Powershellu

Váš prostředek log analytics prostřednictvím prostředí PowerShell můžete také nasadit pomocí `New-AzureRmOperationalInsightsWorkspace` příkazu. Pokud chcete použít tuto metodu, ujistěte se, že jste si nainstalovali [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps). Pomocí tohoto skriptu můžete vytvořit nový pracovní prostor Log Analytics a k němu přidejte řešení pro Service Fabric: 

```powershell

$SubID = "<subscription ID>"
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

Jakmile budete hotovi, postupujte podle kroků v předchozí části pro připojení k účtu úložiště odpovídající protokoly Azure monitoru.

Můžete také přidat další řešení a provádět další úpravy do pracovního prostoru Log Analytics pomocí Powershellu. Další informace najdete v tématu [spravovat Azure Monitor protokolů pomocí prostředí PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md).

## <a name="next-steps"></a>Další postup
* [Nasadit agenta Log Analytics](service-fabric-diagnostics-oms-agent.md) na svých uzlech shromažďování čítačů výkonu a shromáždit statistiky dockeru a protokoly pro vaše kontejnery
* Seznamte se s [prohledávání protokolů a dotazování](../log-analytics/log-analytics-log-searches.md) funkce nabízí jako součást protokoly Azure monitoru
* [Použít návrháře zobrazení pro vytváření vlastních zobrazení v protokoly Azure monitoru](../azure-monitor/platform/view-designer.md)
