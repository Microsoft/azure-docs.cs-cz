---
title: Povolení protokolování prostředků v Azure Traffic Manager
description: Naučte se, jak povolit protokolování prostředků pro profil Traffic Manager a přistupovat k souborům protokolu, které se vytvoří v důsledku.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: duau
ms.openlocfilehash: 4cf3709574e2055f40759fd2d7026c93ac9db098
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102608015"
---
# <a name="enable-resource-logging-in-azure-traffic-manager"></a>Povolení protokolování prostředků v Azure Traffic Manager

Tento článek popisuje, jak povolit shromažďování protokolů diagnostických prostředků a přístup k datům protokolu pro profil Traffic Manager.

Protokoly prostředků Azure Traffic Manager můžou poskytnout přehled o chování prostředku profilu Traffic Manager. Data v protokolu profilu můžete například použít k určení, proč vypršel časový limit jednotlivých sond na koncový bod.

## <a name="enable-resource-logging"></a>Povolení protokolování prostředků

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Můžete spustit příkazy, které následují v [Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním PowerShellu z počítače. Azure Cloud Shell je bezplatné interaktivní prostředí. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Pokud spustíte PowerShell z počítače, budete potřebovat modul Azure PowerShell, 1.0.0 nebo novější. `Get-Module -ListAvailable Az`Nainstalovanou verzi můžete najít spuštěním. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit `Login-AzAccount` pro přihlášení k Azure.

1. **Načíst profil Traffic Manager:**

    Pokud chcete povolit protokolování prostředků, potřebujete ID Traffic Managerho profilu. Načtěte profil Traffic Manager, u kterého chcete povolit protokolování prostředků pomocí [Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile). Výstup obsahuje informace o ID profilu Traffic Manager.

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **Povolit protokolování prostředků pro profil Traffic Manager:**

    Povolte protokolování prostředků pro profil Traffic Manager pomocí ID získaného v předchozím kroku pomocí [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting). Následující příkaz uloží podrobné protokoly pro profil Traffic Manager do zadaného Azure Storageho účtu. 

      ```azurepowershell-interactive
    Set-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **Ověřit nastavení diagnostiky:**

      Ověřte nastavení diagnostiky pro profil Traffic Manager pomocí [Get-AzDiagnosticSetting](/powershell/module/az.monitor/get-azdiagnosticsetting). Následující příkaz zobrazí kategorie, které jsou protokolovány pro určitý prostředek.

     ```azurepowershell-interactive
     Get-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Zajistěte, aby se všechny kategorie protokolů přidružené k prostředku profilu Traffic Manager zobrazovaly jako povolené. Ověřte také, že je účet úložiště správně nastavený.

## <a name="access-log-files"></a>Přístup k souborům protokolu
1. Přihlaste se na [Azure Portal](https://portal.azure.com). 
1. Na portálu přejděte na účet Azure Storage.
2. Na stránce **Přehled** účtu úložiště Azure v části **služby** vyberte **objekty blob**.
3. V části **kontejnery** vyberte **Insights-logs-probehealthstatusevents** a přejděte dolů na PT1H.jsv souboru a kliknutím na **Stáhnout** Stáhněte a uložte kopii tohoto souboru protokolu.

    ![Přístup k souborům protokolu vašeho profilu Traffic Manager ze služby Blob Storage](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>Schéma Traffic Managerho protokolu

Všechny protokoly prostředků dostupné prostřednictvím Azure Monitor sdílejí společné schéma nejvyšší úrovně s flexibilitou pro každou službu, aby vygenerovala jedinečné vlastnosti pro vlastní události. Schéma pro protokoly prostředků na nejvyšší úrovni najdete v tématu [podporované služby, schémata a kategorie pro protokoly prostředků Azure](../azure-monitor/essentials/resource-logs-schema.md).

Následující tabulka obsahuje schéma protokolů specifické pro prostředek profilu Azure Traffic Manager.

|Název pole|Typ pole|Definice|Příklad|
|----|----|---|---|
|Koncový bod|Řetězec|Název koncového bodu Traffic Manager, jehož stav se zaznamenává.|*myPrimaryEndpoint*|
|Status|Řetězec|Stav Traffic Managerho koncového bodu, ve kterém byl zjištěn test. Stav může být buď **nahoru** , nebo **nižší**.|**Nahoru**|
|||||

## <a name="next-steps"></a>Další kroky

* Další informace o [monitorování Traffic Manager](traffic-manager-monitoring.md)