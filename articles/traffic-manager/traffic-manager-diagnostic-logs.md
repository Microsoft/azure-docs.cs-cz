---
title: Povolení protokolování diagnostiky v Azure Traffic Manager
description: Naučte se, jak povolit diagnostické protokolování pro profil Traffic Manager a přistupovat k souborům protokolu, které se vytvoří v důsledku.
services: traffic-manager
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: rohink
ms.openlocfilehash: 0ed2ecef86795f62aa3fe5798dcd0d07adbaf9cc
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938675"
---
# <a name="enable-diagnostic-logging-in-azure-traffic-manager"></a>Povolení protokolování diagnostiky v Azure Traffic Manager

Tento článek popisuje, jak povolit protokolování diagnostiky a přistupovat k datům protokolu pro profil Traffic Manager.

Diagnostické protokoly Azure Traffic Manager vám poskytnou přehled o chování prostředku profilu Traffic Manager. Data v protokolu profilu můžete například použít k určení, proč vypršel časový limit jednotlivých sond na koncový bod.

## <a name="enable-diagnostic-logging"></a>Povolení protokolování diagnostiky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Můžete spustit příkazy, které následují v [Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním PowerShellu z počítače. Azure Cloud Shell je bezplatné interaktivní prostředí. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Pokud spustíte PowerShell z počítače, budete potřebovat modul Azure PowerShell, 1.0.0 nebo novější. Nainstalovanou verzi můžete najít spuštěním `Get-Module -ListAvailable Az`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit `Login-AzAccount` pro přihlášení k Azure.

1. **Načíst profil Traffic Manager:**

    Chcete-li povolit protokolování diagnostiky, potřebujete ID Traffic Managerho profilu. Načtěte profil Traffic Manager, u kterého chcete povolit protokolování diagnostiky pomocí [Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile). Výstup obsahuje informace o ID profilu Traffic Manager.

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **Povolit protokolování diagnostiky pro Traffic Manager profil:**

    Povolte protokolování diagnostiky pro profil Traffic Manager pomocí ID získaného v předchozím kroku pomocí [set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest). Následující příkaz uloží podrobné protokoly pro profil Traffic Manager do zadaného Azure Storageho účtu. 

      ```azurepowershell-interactive
    Set-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **Ověřit nastavení diagnostiky:**

      Ověřte nastavení diagnostiky pro profil Traffic Manager pomocí [Get-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/get-azdiagnosticsetting?view=latest). Následující příkaz zobrazí kategorie, které jsou protokolovány pro určitý prostředek.

     ```azurepowershell-interactive
     Get-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Zajistěte, aby se všechny kategorie protokolů přidružené k prostředku profilu Traffic Manager zobrazovaly jako povolené. Ověřte také, že je účet úložiště správně nastavený.

## <a name="access-log-files"></a>Přístup k souborům protokolu
1. Přihlaste se k [Portálu Azure](https://portal.azure.com). 
1. Na portálu přejděte na účet Azure Storage.
2. Na stránce **Přehled** účtu úložiště Azure v části **služby** vyberte **objekty blob**.
3. V případě **kontejnerů**vyberte **Insights-logs-probehealthstatusevents**a přejděte dolů k souboru PT1H. JSON a kliknutím na **Stáhnout** Stáhněte a uložte kopii tohoto souboru protokolu.

    ![Přístup k souborům protokolu vašeho profilu Traffic Manager ze služby Blob Storage](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>Schéma Traffic Managerho protokolu

Všechny diagnostické protokoly dostupné prostřednictvím Azure Monitor sdílejí společné schéma nejvyšší úrovně s flexibilitou pro každou službu, aby vygenerovala jedinečné vlastnosti pro vlastní události. Schéma diagnostických protokolů nejvyšší úrovně najdete v tématu [podporované služby, schémata a kategorie pro diagnostické protokoly Azure](../azure-monitor/platform/tutorial-dashboards.md).

Následující tabulka obsahuje schéma protokolů specifické pro prostředek profilu Azure Traffic Manager.

|||||
|----|----|---|---|
|**Název pole**|**Typ pole**|**Definice**|**Příklad**|
|EndpointName|Řetězec|Název koncového bodu Traffic Manager, jehož stav se zaznamenává.|*myPrimaryEndpoint*|
|Stav|Řetězec|Stav Traffic Managerho koncového bodu, ve kterém byl zjištěn test. Stav může být buď **nahoru** , nebo **nižší**.|**Následn**|
|||||

## <a name="next-steps"></a>Další kroky

* Další informace o [monitorování Traffic Manager](traffic-manager-monitoring.md)

