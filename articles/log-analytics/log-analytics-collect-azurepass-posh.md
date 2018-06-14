---
title: Shromažďování metrik Azure PaaS prostředků s Log Analytics | Microsoft Docs
description: Zjistěte, jak povolit kolekce metriky prostředků Azure PaaS pomocí prostředí PowerShell pro uchování a analýzu v analýzy protokolů.
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: magoedte
ms.openlocfilehash: 8a2c04c2f79f310b7e70e7add7a8d5f318f056d2
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31593280"
---
# <a name="configure-collection-of-azure-paas-resource-metrics-with-log-analytics"></a>Konfigurace shromažďování metrik Azure PaaS prostředků s analýzy protokolů

Azure platforma jako služba (PaaS) prostředky, jako Azure SQL a webových serverů (webové aplikace), můžete posílat data metriky výkonu nativně k analýze protokolů. Tento skript umožňuje uživatelům povolíte protokolování pro prostředky PaaS nasazené v určité skupiny zdrojů nebo v celé předplatné. 

V současné době neexistuje žádný způsob, jak povolit metriky protokolování pro PaaS prostředky prostřednictvím portálu Azure. Proto musíte použít skript prostředí PowerShell. Tato funkce protokolování nativní metriky společně s analýzy protokolů monitorování, umožňují sledovat prostředky Azure ve velkém měřítku. 

## <a name="prerequisites"></a>Požadavky
Ověřte, zda že máte následující moduly Azure Resource Manager nainstalován na váš počítač před pokračováním:

- AzureRM.Insights
- AzureRM.OperationalInsights
- AzureRM.Resources
- AzureRM.profile

>[!NOTE]
>Doporučujeme vám, že jsou všechny moduly Azure Resource Manager stejnou verzi, aby se zajistila kompatibilita při spuštění Správce prostředků Azure příkazy z prostředí PowerShell.
>
Nainstalujte nejnovější verzi moduly Azure Resource Manager v počítači, najdete v tématu [instalace a konfigurace prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.1#update-azps).  

## <a name="enable-azure-diagnostics"></a>Povolit Azure Diagnostics  
Konfigurace Azure Diagnostics pro PaaS prostředky se provádí spuštěním skriptu, **povolit AzureRMDiagnostics.ps1**, který je k dispozici [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/Enable-AzureRMDiagnostics/2.52/DisplayScript).  Skript podporuje následující scénáře:
  
* Určení prostředek související s jednu nebo více skupin prostředků v předplatném.  
* Určení prostředek související s určité skupiny zdrojů v předplatném.  
* Překonfigurujte prostředek k předávání do různých pracovního prostoru

Jsou podporovány pouze prostředky, které podporují shromažďování metrik pomocí diagnostiky Azure a odeslat přímo k Log Analytics.  Podrobný seznam, zkontrolujte [protokoly služby Azure shromažďovat a metriky pro použití v analýzy protokolů](log-analytics-azure-storage.md) 

Proveďte následující kroky ke stažení a spuštění skriptu.

1.  Na úvodní obrazovce Windows, zadejte **prostředí PowerShell** a ve výsledcích hledání klikněte pravým tlačítkem na prostředí PowerShell.  Vyberte v nabídce **spustit jako správce**.   
2. Uložit **povolit AzureRMDiagnostics.ps1** soubor skriptu místně tak, že spustíte následující příkaz a poskytnutí cesty k uložení skriptu.    

    ```
    PS C:\> save-script -Name Enable-AzureRMDiagnostics -Path "C:\users\<username>\desktop\temp"
    ```

3. Spuštěním příkazu `Connect-AzureRmAccount` vytvořte připojení k Azure.   
4. Spusťte následující skript `.\Enable-AzureRmDiagnostics.ps1` bez parametrů Povolit shromažďování dat z konkrétní prostředek v rámci vašeho předplatného, nebo s parametrem `-ResourceGroup <myResourceGroup>` určete prostředku v určité skupiny zdrojů.   
5. Ze seznamu vyberte odpovídající předplatné, pokud máte více než jednu, tak, že zadáte správnou hodnotu.<br><br> ![Vyberte předplatné, které vrátil skript](./media/log-analytics-collect-azurepass-posh/script-select-subscription.png)<br> Jinak automaticky vybere jeden odběr k dispozici.
6. V dalším kroku skript vrátí seznam pracovních prostorů analýzy protokolů zaregistrován v rámci předplatného.  Vyberte příslušný ze seznamu.<br><br> ![Vyberte pracovní prostor vrátil skript](./media/log-analytics-collect-azurepass-posh/script-select-workspace.png)<br> 
7. Vyberte, které chcete povolit kolekci z prostředku Azure. Pokud zadáte 5, je třeba povolit shromažďování dat pro databáze SQL Azure.<br><br> ![Typ vyberte prostředku vrácená skriptem](./media/log-analytics-collect-azurepass-posh/script-select-resource.png)<br>
   Můžete zvolit pouze prostředky, které podporují shromažďování metrik pomocí diagnostiky Azure a odesílání přímo k Log Analytics.  Skript se zobrazí hodnota **True** pod **metriky** sloupec pro seznam zdrojů, které nalezne v předplatného nebo zadaná skupina prostředků.    
8. Zobrazí se výzva k potvrzení výběru.  Zadejte **Y** povolení protokolování metriky pro všechny vybrané prostředky pro obor definovaný, které v našem příkladu jsou všechny databáze SQL v rámci předplatného.  

Skript spustí u každého prostředku odpovídající vybraných kritérií a Povolit shromažďování metrik pro ně. Po jeho dokončení, zobrazí se zpráva oznamující, že konfigurace je hotová.  

Krátce po dokončení se spustí, když chcete zobrazit data z prostředku Azure PaaS do úložiště analýzy protokolů.  Záznam s typem `AzureMetrics` je vytvořena a analýza tyto záznamy jsou podporována [Azure SQL Analytics](log-analytics-azure-sql.md) a [Azure Web Apps Analytics](log-analytics-azure-web-apps-analytics.md) řešení pro správu.   

## <a name="update-a-resource-to-send-data-to-another-workspace"></a>Provést upgrade prostředku pro odesílání dat do jiného pracovního prostoru
Pokud máte na prostředek, který je již odesílání dat do pracovního prostoru analýzy protokolů a později se rozhodnete překonfigurovat tak, aby odkazovaly jiného pracovního prostoru, můžete spustit skript se `-Update` parametr.  

**Příklad:** 
`PS C:\users\<username>\Desktop\temp> .\Enable-AzureRMDiagnostics.ps1 -Update`

Zobrazí se výzva k zodpovězení stejné informace jako při spuštění skriptu na provedení počáteční konfigurace.  

## <a name="next-steps"></a>Další postup

* Další informace o [protokolu hledání](log-analytics-log-searches.md) analyzovat data shromážděná ze zdrojů dat a řešení. 

* Použití [vlastní pole](log-analytics-custom-fields.md)(k analýze záznamů událostí do jednotlivých polí.

* Zkontrolujte [vytvořit vlastní řídicí panel pro použití v analýzy protokolů](log-analytics-dashboards.md) pochopit, jak můžete vizualizovat váš protokol vyhledá smysluplnějšími způsoby pro organizaci.
