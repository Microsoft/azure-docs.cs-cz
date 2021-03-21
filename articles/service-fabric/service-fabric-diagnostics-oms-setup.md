---
title: Nastavení monitorování pomocí protokolů Azure Monitor
description: Naučte se nastavit protokoly Azure Monitor pro vizualizaci a analýzu událostí pro monitorování clusterů Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: srrengar
ms.openlocfilehash: ba62ac80b2f8d318d0d13e81e88cc63a8d893a2b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570352"
---
# <a name="set-up-azure-monitor-logs-for-a-cluster"></a>Nastavení protokolů Azure Monitor pro cluster

Protokoly Azure Monitor jsou naše doporučení pro monitorování událostí na úrovni clusteru. Můžete nastavit Log Analytics pracovní prostor prostřednictvím Azure Resource Manager, PowerShellu nebo Azure Marketplace. Pokud udržujete aktualizovanou šablonu Správce prostředků nasazení pro budoucí použití, použijte stejnou šablonu k nastavení Azure Monitor prostředí protokolů. Nasazení prostřednictvím Marketplace je snazší, pokud už máte nasazený cluster s povolenou diagnostikou. Pokud nemáte přístup na úrovni předplatného v účtu, na který nasazujete, nasaďte pomocí PowerShellu nebo šablony Správce prostředků.

> [!NOTE]
> Chcete-li nastavit protokoly Azure Monitor pro monitorování clusteru, je nutné mít povolenou diagnostiku pro zobrazení událostí na úrovni clusteru nebo platformy. Další informace najdete [v tématu nastavení diagnostiky v clusterech Windows](service-fabric-diagnostics-event-aggregation-wad.md) a [Postup nastavení diagnostiky v clusterech se systémem Linux](service-fabric-diagnostics-oms-syslog.md) .

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Nasazení Log Analytics pracovního prostoru pomocí Azure Marketplace

Pokud chcete přidat Log Analytics pracovní prostor po nasazení clusteru, na portálu navštivte Azure Marketplace a hledejte **Service Fabric Analytics**. Toto je vlastní řešení pro Service Fabric nasazení, které má data specifická pro Service Fabric. V tomto procesu vytvoříte řešení (řídicí panel pro zobrazení přehledů) a pracovní prostor (agregaci podkladových dat clusteru).

1. V levé navigační nabídce vyberte **Nový** . 

2. Vyhledejte **Service Fabric Analytics**. Vyberte prostředek, který se zobrazí.

3. Vyberte **Vytvořit**.

    ![Service Fabric Analytics na webu Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. V okně vytváření Service Fabric Analytics vyberte **vybrat pracovní prostor** pro pole **pracovní prostor OMS** a pak **vytvořte nový pracovní prostor**. Vyplňte požadované položky. Jediným požadavkem je, že předplatné pro Service Fabric cluster a pracovní prostor jsou stejné. Po ověření vašich záznamů se váš pracovní prostor začne nasazovat. Nasazení trvá jenom několik minut.

5. Po dokončení vyberte **vytvořit** znovu v dolní části okna pro vytváření Service Fabric Analytics. Ujistěte se, že se nový pracovní prostor zobrazuje v **pracovním prostoru OMS**. Tato akce přidá řešení do pracovního prostoru, který jste vytvořili.

Pokud používáte Windows, pokračujte podle následujících pokynů a připojte protokoly Azure Monitor k účtu úložiště, ve kterém jsou uložené události clusteru. 

>[!NOTE]
>Řešení Service Fabric Analytics je podporováno pouze pro clustery systému Windows. Clustery se systémem Linux najdete v našem článku o [nastavení protokolů Azure monitor pro clustery se systémem Linux](service-fabric-diagnostics-oms-syslog.md).  

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>Připojení pracovního prostoru Log Analytics k vašemu clusteru 

1. Pracovní prostor musí být připojený ke diagnostická data, která přicházejí z vašeho clusteru. Přejít do skupiny prostředků, ve které jste vytvořili řešení Service Fabric Analytics. Vyberte **ServiceFabric \<nameOfWorkspace\>** a přejít na jeho stránku s přehledem. Odtud můžete změnit nastavení řešení, nastavení pracovního prostoru a získat přístup k pracovnímu prostoru Log Analytics.

2. V levé navigační nabídce v části **zdroje dat pracovního prostoru** vyberte **protokoly účtů úložiště**.

3. Na stránce **protokoly účtu úložiště** vyberte **Přidat** v horní části a přidejte do pracovního prostoru protokoly vašeho clusteru.

4. Vyberte **účet úložiště** a přidejte příslušný účet vytvořený v clusteru. Pokud jste použili výchozí název, účet úložiště je **sfdg \<resourceGroupName\>**. Můžete to také ověřit pomocí šablony Azure Resource Manager používané k nasazení clusteru, a to tak, že zkontrolujete hodnotu použitou pro **applicationDiagnosticsStorageAccountName**. Pokud se název nezobrazuje, posuňte se dolů a vyberte **načíst další**. Vyberte název účtu úložiště.

5. Zadejte datový typ. Nastavte ji na **Service Fabric události**.

6. Zajistěte, aby byl zdroj automaticky nastaven na **WADServiceFabricou \* událost**.

7. Vyberte **OK** a připojte svůj pracovní prostor k protokolům vašeho clusteru.

    ![Přidání protokolů účtu úložiště do protokolů Azure Monitor](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

Účet se teď zobrazí jako součást protokolů účtu úložiště ve zdrojích dat v pracovním prostoru.

Přidali jste řešení Service Fabric Analytics do pracovního prostoru Log Analytics, který je nyní správně připojen ke své platformě a tabulce protokolů aplikace vašeho clusteru. Do pracovního prostoru můžete přidat další zdroje stejným způsobem.


## <a name="deploy-azure-monitor-logs-with-azure-resource-manager"></a>Nasazení protokolů Azure Monitor s využitím Azure Resource Manager

Když nasadíte cluster pomocí šablony Správce prostředků, šablona vytvoří nový pracovní prostor Log Analytics, přidá do pracovního prostoru řešení Service Fabric a nakonfiguruje ho pro čtení dat z příslušných tabulek úložiště.

[Tuto ukázkovou šablonu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) můžete použít a upravit tak, aby splňovala vaše požadavky. Tato šablona provede následující

* Vytvoří cluster Service Fabric 5 uzlů.
* Vytvoří Log Analytics pracovní prostor a řešení Service Fabric.
* Nakonfiguruje agenta Log Analytics ke shromáždění a odeslání 2 ukázkových čítačů výkonu do pracovního prostoru.
* Nakonfiguruje WAD ke shromáždění Service Fabric a pošle je do tabulek Azure Storage (WADServiceFabric * Event).
* Nakonfiguruje Log Analytics pracovní prostor pro čtení událostí z těchto tabulek.


Šablonu můžete nasadit jako Správce prostředků upgrade na svůj cluster pomocí `New-AzResourceGroupDeployment` rozhraní API v modulu Azure PowerShell. Příklad příkazu by byl:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

Azure Resource Manager zjistí, že tento příkaz je aktualizací existujícího prostředku. Zpracovává jenom změny mezi šablonou, která řídí stávající nasazení a novou šablonou.

## <a name="deploy-azure-monitor-logs-with-azure-powershell"></a>Nasazení protokolů Azure Monitor s využitím Azure PowerShell

Prostředek Log Analytics můžete nasadit taky přes PowerShell pomocí `New-AzOperationalInsightsWorkspace` příkazu. Pokud chcete použít tuto metodu, ujistěte se, že máte nainstalovanou [Azure PowerShell](/powershell/azure/install-az-ps). Pomocí tohoto skriptu vytvořte nový pracovní prostor Log Analytics a přidejte do něj řešení Service Fabric: 

```powershell

$SubID = "<subscription ID>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<Log Analytics workspace name>"
$solution = "ServiceFabric"

# Sign in to Azure and access the correct subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

Až budete hotovi, připojte Azure Monitor protokoly k příslušnému účtu úložiště podle kroků v předchozí části.

Pomocí prostředí PowerShell můžete také přidat další řešení nebo provádět další úpravy pracovního prostoru Log Analytics. Další informace najdete v tématu [Správa protokolů Azure monitor pomocí prostředí PowerShell](../azure-monitor/logs/powershell-workspace-configuration.md).

## <a name="next-steps"></a>Další kroky
* [Nasaďte agenta Log Analytics](service-fabric-diagnostics-oms-agent.md) do svých uzlů, abyste mohli shromažďovat čítače výkonu a shromažďovat statistiky a protokoly Docker pro vaše kontejnery.
* Seznámení s funkcemi [prohledávání protokolů a dotazování](../azure-monitor/logs/log-query-overview.md) , které nabízí jako součást protokolů Azure monitor
* [Použití návrháře zobrazení k vytváření vlastních zobrazení v protokolech Azure Monitor](../azure-monitor/visualize/view-designer.md)
