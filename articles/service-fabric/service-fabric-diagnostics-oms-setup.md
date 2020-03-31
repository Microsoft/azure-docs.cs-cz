---
title: Nastavení monitorování pomocí protokolů Azure Monitoru
description: Zjistěte, jak nastavit protokoly Azure Monitoru pro vizualizaci a analýzu událostí pro monitorování clusterů Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: srrengar
ms.openlocfilehash: cf0fab9942dcbb7ee09e554f2c9ba8738f208009
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609923"
---
# <a name="set-up-azure-monitor-logs-for-a-cluster"></a>Nastavení protokolů Azure Monitor u clusteru

Protokoly Azure Monitor unás je naše doporučení ke sledování událostí na úrovni clusteru. Pracovní prostor Log Analytics můžete nastavit prostřednictvím Azure Resource Manager, PowerShellu nebo Azure Marketplace. Pokud udržujete aktualizovanou šablonu Správce prostředků vašeho nasazení pro budoucí použití, použijte stejnou šablonu k nastavení prostředí protokolů Azure Monitor. Nasazení přes Marketplace je jednodušší, pokud už máte cluster nasazený s povolenou diagnostikou. Pokud nemáte přístup na úrovni předplatného v účtu, ke kterému nasazujete, nasazujte pomocí Prostředí PowerShell nebo šablony Správce prostředků.

> [!NOTE]
> Chcete-li nastavit protokoly Azure Monitor pro monitorování clusteru, musíte mít povolenou diagnostiku pro zobrazení událostí na úrovni clusteru nebo platformy. Další informace naleznete [v tom, jak nastavit diagnostiku v clusterech Windows](service-fabric-diagnostics-event-aggregation-wad.md) a [jak nastavit diagnostiku v clusterech Linuxu.](service-fabric-diagnostics-oms-syslog.md)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Nasazení pracovního prostoru Log Analytics pomocí Azure Marketplace

Pokud chcete přidat pracovní prostor Analýzy protokolů po nasazení clusteru, přejděte na Azure Marketplace na portálu a vyhledejte **Service Fabric Analytics**. Toto je vlastní řešení pro nasazení Service Fabric, který má data specifická pro Service Fabric. V tomto procesu vytvoříte řešení (řídicí panel pro zobrazení přehledů) i pracovní prostor (agregaci podkladových dat clusteru).

1. V levé navigační nabídce vyberte **Nový.** 

2. Vyhledejte **službu Fabric Analytics**. Vyberte zdroj, který se zobrazí.

3. Vyberte **Vytvořit**.

    ![Service Fabric Analytics na Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. V okně vytvoření service fabric analytics vyberte **pracovní prostor** pro pole **pracovního prostoru OMS** a pak **vytvořte nový pracovní prostor**. Vyplňte požadované položky. Jediným požadavkem je, že předplatné clusteru Service Fabric a pracovního prostoru je stejné. Po ověření položek se váš pracovní prostor začne nasazovat. Nasazení trvá jen několik minut.

5. Po dokončení vyberte **vytvořit** znovu v dolní části okna vytvoření Service Fabric Analytics. Ujistěte se, že se nový pracovní prostor zobrazuje v části **Pracovní prostor OMS**. Tato akce přidá řešení do pracovního prostoru, který jste vytvořili.

Pokud používáte Windows, pokračujte následujícími kroky pro připojení protokolů Azure Monitor k účtu úložiště, kde jsou uloženy události clusteru. 

>[!NOTE]
>Řešení Service Fabric Analytics je podporováno pouze pro clustery Windows. Pro linuxové clustery se podívejte na náš článek o [tom, jak nastavit protokoly Azure Monitoru pro clustery Linuxu](service-fabric-diagnostics-oms-syslog.md).  

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>Připojení pracovního prostoru Log Analytics ke clusteru 

1. Pracovní prostor musí být připojen k diagnostickým datům přicházejícím z vašeho clusteru. Přejděte do skupiny prostředků, ve které jste vytvořili řešení Service Fabric Analytics. Vyberte **ServiceFabric\<\> nameOfWorkspace** a přejděte na jeho stránku s přehledem. Odtud můžete změnit nastavení řešení, nastavení pracovního prostoru a přístup k pracovnímu prostoru Log Analytics.

2. V levé navigační nabídce vyberte v části **Zdroje dat pracovního prostoru** **protokoly účtů úložiště**.

3. Na stránce **Protokoly účtu úložiště** vyberte nahoře **přidat** protokoly clusteru do pracovního prostoru.

4. Vyberte **účet úložiště a** přidejte příslušný účet vytvořený v clusteru. Pokud jste použili výchozí název, účet úložiště je **sfdg\<\>resourceGroupName**. Můžete to také potvrdit pomocí šablony Azure Resource Manager, která se používá k nasazení clusteru, a to kontrolou hodnoty použité pro **aplikaci DiagnosticsStorageAccountName**. Pokud se název nezobrazí, posuňte se dolů a vyberte **Načíst více**. Vyberte název účtu úložiště.

5. Zadejte datový typ. Nastavte jej na **události service fabric**.

6. Ujistěte se, že zdroj je automaticky nastavena na **WADServiceFabric\*EventTable**.

7. Výběrem **možnosti OK** připojíte pracovní prostor k protokolům clusteru.

    ![Přidání protokolů účtů úložiště do protokolů Azure Monitoru](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

Účet se teď zobrazuje jako součást protokolů účtu úložiště ve zdrojích dat vašeho pracovního prostoru.

Přidali jste řešení Service Fabric Analytics v pracovním prostoru Analýzy protokolů, který je teď správně připojený k platformě clusteru a tabulce protokolu aplikací. Stejným způsobem můžete do pracovního prostoru přidat další zdroje.


## <a name="deploy-azure-monitor-logs-with-azure-resource-manager"></a>Nasazení protokolů Azure Monitor u Azure Správce prostředků

Když nasadíte cluster pomocí šablony Správce prostředků, šablona vytvoří nový pracovní prostor Log Analytics, přidá řešení Service Fabric do pracovního prostoru a nakonfiguruje ho tak, aby četl data z příslušných tabulek úložiště.

Tuto [ukázkovou šablonu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) můžete použít a upravit tak, aby splňovala vaše požadavky. Tato šablona provádí následující

* Vytvoří cluster prostředků service fabric s 5 uzmě.
* Vytvoří pracovní prostor analýzy protokolů a řešení Service Fabric
* Konfiguruje agenta Analýzy protokolů tak, aby shromažďoval a odesílá 2 ukázkové čítače výkonu do pracovního prostoru.
* Nakonfiguruje protokol WAD tak, aby shromažďoval fabric služby, a odesílá je do tabulek úložiště Azure (WADServiceFabric*EventTable).
* Konfiguruje pracovní prostor Log Analytics tak, aby četl události z těchto tabulek.


Šablonu můžete nasadit jako upgrade Správce prostředků `New-AzResourceGroupDeployment` do clusteru pomocí rozhraní API v modulu Azure PowerShell. Příkladem příkazu by bylo:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

Azure Resource Manager zjistí, že tento příkaz je aktualizace existujícího prostředku. Zpracovává pouze změny mezi šablonou, která řídí stávající nasazení, a novou poskytnutou šablonou.

## <a name="deploy-azure-monitor-logs-with-azure-powershell"></a>Nasazení protokolů Azure Monitor u Azure PowerShellu

Můžete také nasadit prostředek analýzy protokolů `New-AzOperationalInsightsWorkspace` prostřednictvím prostředí PowerShell pomocí příkazu. Chcete-li použít tuto metodu, ujistěte se, že máte nainstalovaný [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps). Tento skript slouží k vytvoření nového pracovního prostoru Analýzy protokolů a přidání řešení Service Fabric: 

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

Až budete hotovi, postupujte podle pokynů v předchozí části pro připojení protokolů Azure Monitor k příslušnému účtu úložiště.

Můžete také přidat další řešení nebo provést další změny v pracovním prostoru Log Analytics pomocí prostředí PowerShell. Další informace najdete v [tématu Správa protokolů Azure Monitor pomocí PowerShellu](../azure-monitor/platform/powershell-workspace-configuration.md).

## <a name="next-steps"></a>Další kroky
* [Nasazení agenta Log Analytics](service-fabric-diagnostics-oms-agent.md) do uzlů ke shromažďování čítačů výkonu a shromažďování statistik a protokolů dockeru pro vaše kontejnery
* Seznamte se s funkcemi [pro vyhledávání protokolů a dotazování](../log-analytics/log-analytics-log-searches.md) nabízenými jako součást protokolů Azure Monitoru
* [Vytvoření vlastních zobrazení v protokolech Azure Monitoru pomocí Návrháře zobrazení](../azure-monitor/platform/view-designer.md)
