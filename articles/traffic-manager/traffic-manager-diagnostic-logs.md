---
title: Povolení protokolování diagnostiky ve Službě Azure Traffic Manager
description: Zjistěte, jak povolit diagnostické protokolování pro váš profil Traffic Manageru a získat přístup k souborům protokolu, které jsou v důsledku toho vytvořeny.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938675"
---
# <a name="enable-diagnostic-logging-in-azure-traffic-manager"></a>Povolení protokolování diagnostiky ve Službě Azure Traffic Manager

Tento článek popisuje, jak povolit diagnostické protokolování a přístup k datům protokolu pro profil Traffic Manager.

Diagnostické protokoly Azure Traffic Manageru můžou poskytnout přehled o chování prostředku profilu Traffic Manageru. Můžete například použít data protokolu profilu k určení, proč jednotlivé sondy mají časový čas proti koncovému bodu.

## <a name="enable-diagnostic-logging"></a>Povolení protokolování diagnostiky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Můžete spustit příkazy, které následují v [Prostředí Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním Prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatná interaktivní prostředí. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Pokud spustíte PowerShell z počítače, budete potřebovat modul Azure PowerShell, 1.0.0 nebo novější. Můžete spustit `Get-Module -ListAvailable Az` a najít nainstalovanou verzi. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, musíte taky `Login-AzAccount` spustit pro přihlášení k Azure.

1. **Načíst profil Traffic Manageru:**

    Chcete-li povolit protokolování diagnostiky, potřebujete ID profilu traffic manageru. Načtěte profil Traffic Manager, pro který chcete povolit protokolování diagnostiky pomocí [nástroje Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile). Výstup obsahuje id informace o id profilu Traffic Manageru.

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **Povolte protokolování diagnostiky pro profil Traffic Manager:**

    Povolte protokolování diagnostiky pro profil traffic manageru pomocí ID získaného v předchozím kroku pomocí [nastavení Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest). Následující příkaz ukládá podrobné protokoly pro profil Traffic Manager u zadaného účtu Úložiště Azure. 

      ```azurepowershell-interactive
    Set-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **Ověřte nastavení diagnostiky:**

      Ověřte nastavení diagnostiky pro profil Traffic Managerpomocí [get-azdiagnosticsetting](https://docs.microsoft.com/powershell/module/az.monitor/get-azdiagnosticsetting?view=latest). Následující příkaz zobrazí kategorie, které jsou zaznamenány pro prostředek.

     ```azurepowershell-interactive
     Get-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Ujistěte se, že všechny kategorie protokolu přidružené k prostředku profilu traffic manageru se zobrazují jako povolené. Ověřte také, zda je účet úložiště správně nastaven.

## <a name="access-log-files"></a>Přístup k souborům protokolu
1. Přihlaste se k [portálu Azure](https://portal.azure.com). 
1. Přejděte na svůj účet Azure Storage na portálu.
2. Na stránce **Přehled** vašeho účtu úložiště Azure včásti **Služby** vyberte **Objekty blob .**
3. V **případě kontejnerů**vyberte **insights-logs-probehealthstatusevents**a přejděte dolů do souboru PT1H.json a klepnutím na tlačítko **Stáhnout** stáhněte a uložte kopii tohoto souboru protokolu.

    ![Přístup k souborům protokolu profilu Traffic Manageru z úložiště objektů blob](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>Schéma protokolu správce provozu

Všechny diagnostické protokoly dostupné prostřednictvím Služby Azure Monitor sdílejí společné schéma nejvyšší úrovně s flexibilitou pro každou službu k vyzařování jedinečných vlastností pro své vlastní události. Schéma diagnostických protokolů nejvyšší úrovně najdete v tématu [Podporované služby, schémata a kategorie pro diagnostické protokoly Azure](../azure-monitor/platform/tutorial-dashboards.md).

Následující tabulka obsahuje schéma protokolů specifické pro prostředek profilu Azure Traffic Manager.

|||||
|----|----|---|---|
|**Název pole**|**Typ pole**|**Definice**|**Příklad**|
|Název koncového bodu|Řetězec|Název koncového bodu Traffic Manager, jehož stav se zaznamenává.|*myPrimaryEndpoint*|
|Status|Řetězec|Stav koncového bodu Traffic Manager, který byl sondonován. Stav může být **nahoru** nebo **dolů**.|**Nahoru**|
|||||

## <a name="next-steps"></a>Další kroky

* Další informace o [sledování traffic manageru](traffic-manager-monitoring.md)

