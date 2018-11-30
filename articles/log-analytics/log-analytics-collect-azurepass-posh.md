---
title: Shromažďovat metriky prostředků Azure PaaS s využitím Log Analytics | Dokumentace Microsoftu
description: Zjistěte, jak chcete zapnout shromažďování metrik prostředků Azure PaaS pomocí prostředí PowerShell pro uchovávání dat a analýzy v Log Analytics.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: a9635a7c9bad9079814750dc4be945701ba80451
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52632309"
---
# <a name="configure-collection-of-azure-paas-resource-metrics-with-log-analytics"></a>Konfigurace shromažďování metrik prostředků Azure PaaS s využitím Log Analytics

Platformu Azure jako služba (PaaS) prostředky, jako je Azure SQL a webových serverů (webové aplikace), můžete posílat data metrik výkonu nativně ke službě Log Analytics. Tento skript umožňuje uživatelům k zapnutí metrik pro PaaS prostředky, které jsou již nasazeny v konkrétní skupině prostředků nebo v celé předplatné protokolování. 

V současné době neexistuje žádný způsob, jak povolit protokolování pro PaaS prostředků na webu Azure portal metriky. Proto budete muset použít skript prostředí PowerShell. Tato možnost protokolování nativní metriky spolu s Log Analytics, monitorování, umožňují snadno monitorovat prostředky Azure ve velkém měřítku. 

## <a name="prerequisites"></a>Požadavky
Ověřte, že máte následující moduly Azure Resource Manageru nainstalované v počítači, než budete pokračovat:

- AzureRM.Insights
- AzureRM.OperationalInsights
- AzureRM.Resources
- AzureRM.profile

>[!NOTE]
>Doporučujeme vám, že všechny moduly Azure Resource Manageru jsou stejné verze pro zajištění kompatibilitě, když spustíte příkazy Azure Resource Manageru v Powershellu.
>
Nainstalujte nejnovější moduly Azure Resource Manageru ve vašem počítači, naleznete v tématu [instalace a konfigurace Azure Powershellu](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.1#update-azps).  

## <a name="enable-azure-diagnostics"></a>Povolení diagnostiky Azure  
Konfigurace diagnostiky Azure pro prostředky PaaS dosahuje spuštěním skriptu **povolit AzureRMDiagnostics.ps1**, který je k dispozici [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/Enable-AzureRMDiagnostics/2.52).  Tento skript podporuje následující scénáře:
  
* Určení prostředků související s jeden nebo více skupin prostředků v předplatném  
* Určení prostředků související s konkrétní skupině prostředků v předplatném  
* Změna konfigurace prostředků pro předávání s jiným pracovním prostorem

Podporují se jenom prostředky, které podporují shromažďování metrik pomocí diagnostiky Azure a odeslat přímo do služby Log Analytics.  Podrobný seznam najdete v tématu [protokoly služby Azure shromažďovat a metriky pro použití v Log Analytics](log-analytics-azure-storage.md) 

Proveďte následující kroky a stáhněte a spusťte skript.

1.  Na úvodní obrazovce Windows zadejte **Powershellu** a ve výsledcích hledání klikněte pravým tlačítkem na prostředí PowerShell.  Vyberte z nabídky **spustit jako správce**.   
2. Uložit **povolit AzureRMDiagnostics.ps1** soubor skriptu místně tak, že spustíte následující příkaz a poskytuje cestu pro ukládání skriptu.    

    ```
    PS C:\> save-script -Name Enable-AzureRMDiagnostics -Path "C:\users\<username>\desktop\temp"
    ```

3. Spuštěním příkazu `Connect-AzureRmAccount` vytvořte připojení k Azure.   
4. Spusťte následující skript `.\Enable-AzureRmDiagnostics.ps1` bez parametrů povolíte shromažďování dat z konkrétního prostředku ve vašem předplatném nebo s parametrem `-ResourceGroup <myResourceGroup>` zadat prostředek v konkrétní skupině prostředků.   
5. Ze seznamu vyberte odpovídající předplatné, pokud máte více než jeden, tak, že zadáte správnou hodnotu.<br><br> ![Vyberte předplatné, které vrátil skript](./media/log-analytics-collect-azurepass-posh/script-select-subscription.png)<br> V opačném případě automaticky vybere jedno předplatné, které jsou k dispozici.
6. V dalším kroku skript vrátí seznam pracovních prostorů Log Analytics zaregistrován v rámci předplatného.  Vyberte příslušné předplatné ze seznamu.<br><br> ![Vyberte pracovní prostor, které vrátil skript](./media/log-analytics-collect-azurepass-posh/script-select-workspace.png)<br> 
7. Vyberte prostředek Azure, který chcete povolit shromažďování z. Pokud zadáte 5, například povolíte shromažďování dat pro databáze SQL Azure.<br><br> ![Vyberte prostředek typu vrátil skript](./media/log-analytics-collect-azurepass-posh/script-select-resource.png)<br>
   Můžete vybrat jenom prostředky, které podporují shromažďování metrik pomocí diagnostiky Azure a odesílání přímo ke službě Log Analytics.  Skript se zobrazí hodnota **True** pod **metriky** sloupců pro seznam prostředků zjistí v zadané skupině prostředků nebo předplatného.    
8. Zobrazí se výzva k potvrzení výběru.  Zadejte **Y** povolení protokolování metriky pro všechny vybrané prostředky oboru definována, které v našem příkladu jsou všechny databáze SQL v předplatném.  

Skript spustit proti každého prostředku odpovídající vybraným kritériím a Povolit shromažďování metrik pro ně. Až se dokončí, zobrazí se zpráva oznamující, že konfigurace je hotová.  

Krátce po dokončení se začnou zobrazovat data z Azure PaaS prostředku v úložišti Log Analytics.  Záznam s typem `AzureMetrics` se vytvoří a analýza tyto záznamy jsou podporovány [Azure SQL Analytics](log-analytics-azure-sql.md) a [Azure Web Apps Analytics](log-analytics-azure-web-apps-analytics.md) řešení pro správu.   

## <a name="update-a-resource-to-send-data-to-another-workspace"></a>Provést upgrade prostředku pro odesílání dat do jiného pracovního prostoru
Pokud máte prostředek, který je už odesílání dat do pracovního prostoru Log Analytics a později se rozhodnete překonfigurování odkazovat na jiný pracovní prostor, můžete spustit skript `-Update` parametru.  

**Příklad:** 
`PS C:\users\<username>\Desktop\temp> .\Enable-AzureRMDiagnostics.ps1 -Update`

Zobrazí výzva k zodpovězení stejné informace, jako když jste spustili skript, který chcete provést počáteční konfiguraci.  

## <a name="next-steps"></a>Další postup

* Další informace o [prohledávání protokolů](log-analytics-queries.md) analyzovat data shromážděná ze zdrojů dat a jejich řešení. 

* Použití [vlastní pole](log-analytics-custom-fields.md)(k analýze záznamů událostí do jednotlivých polí.

* Kontrola [vytvořit vlastní řídicí panel pro použití v Log Analytics](../azure-monitor/platform/dashboards.md) pochopit, jak vizualizovat váš protokol vyhledá smysluplnějšími způsoby pro organizaci.
