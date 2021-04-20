---
title: Ukázky šablon Správce prostředků pro SQL Insights
description: Ukázky Azure Resource Manager šablon pro nasazení a konfiguraci SQL Insights
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 03/25/2021
ms.openlocfilehash: dafb7335ef211cb9173ec2fb4565243d603d35fe
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730519"
---
# <a name="resource-manager-template-samples-for-sql-insights"></a>Ukázky šablon Správce prostředků pro SQL Insights
Tento článek obsahuje vzorové [Azure Resource Manager šablony](../../azure-resource-manager/templates/template-syntax.md) , které umožňují SQL Insights pro monitorování SQL běžící v Azure.  Podrobnosti o nabídce a verzích SQL, které podporujeme, najdete v [dokumentaci k SQL Insights](sql-insights-overview.md) . Každá ukázka obsahuje soubor šablony a soubor parametrů s ukázkovými hodnotami, které se mají poskytnout šabloně.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="create-a-sql-insights-monitoring-profile"></a>Vytvořit profil monitorování SQL Insights
Následující ukázka vytvoří profil monitorování SQL Insights, který zahrnuje data monitorování SQL, která se mají shromáždit, četnost shromažďování dat a určuje pracovní prostor, do kterého se budou data odesílat.


### <a name="template-file"></a>Soubor šablony

Zobrazte [soubor šablony v centru Git](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Create%20new%20profile/CreateNewProfile.armtemplate).

### <a name="parameter-file"></a>Soubor parametrů

Podívejte se na [soubor parametrů na rozbočovači Git](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Create%20new%20profile/CreateNewProfile.parameters.json).


## <a name="add-a-monitoring-vm-to-a-sql-insights-monitoring-profile"></a>Přidání monitorovacího virtuálního počítače do profilu monitorování SQL Insights
Po vytvoření profilu monitorování musíte přidělit virtuální počítače Azure, které budou nakonfigurované na vzdálené shromažďování dat z prostředků SQL zadaných v konfiguraci tohoto virtuálního počítače.  Další podrobnosti najdete v dokumentaci k povolení SQL Insights.

Následující ukázka nakonfiguruje monitorovací virtuální počítač pro shromažďování dat ze zadaných prostředků SQL.


### <a name="template-file"></a>Soubor šablony

Zobrazte [soubor šablony v centru Git](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Add%20monitoring%20virtual%20machine/AddMonitoringVirtualMachine.armtemplate).

### <a name="parameter-file"></a>Soubor parametrů

Podívejte se na [soubor parametrů na rozbočovači Git](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Add%20monitoring%20virtual%20machine/AddMonitoringVirtualMachine.parameters.json).


## <a name="create-an-alert-rule-for-sql-insights"></a>Vytvoření pravidla výstrahy pro SQL Insights
Následující ukázka vytvoří pravidlo upozornění, které bude zahrnovat prostředky SQL v rámci rozsahu zadaného profilu monitorování.  Toto pravidlo upozornění se zobrazí v uživatelském rozhraní SQL Insights na panelu Místní uživatelské rozhraní výstrah.  

Soubor parametrů obsahuje hodnoty z jedné ze šablon upozornění, které poskytujeme v SQL Insights, můžete ho upravit a upozornit na další data, která shromažďujeme pro SQL.  Šablona neurčuje skupinu akcí pro pravidlo výstrahy.


#### <a name="template-file"></a>Soubor šablony

Zobrazte [soubor šablony v centru Git](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/Alerts/log-metric-noag.armtemplate).

### <a name="parameter-file"></a>Soubor parametrů

Podívejte se na [soubor parametrů na rozbočovači Git](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/Alerts/sql-cpu-utilization-percent.parameters.json).





## <a name="next-steps"></a>Další kroky

* [Získejte další ukázkové šablony pro Azure monitor](../resource-manager-samples.md).
* [Přečtěte si další informace o SQL Insights](sql-insights-overview.md).
