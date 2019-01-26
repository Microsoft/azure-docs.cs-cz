---
title: Povolit protokolování diagnostiky v Azure Traffic Manageru
description: Zjistěte, jak povolit protokolování diagnostiky pro váš profil Traffic Manageru a soubory protokolů, které jsou vytvořeny ve výsledku.
services: traffic-manager
author: KumudD
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: kumud
ms.openlocfilehash: abdc50d6d3d27ab7611994089345a997afc72cae
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55082498"
---
# <a name="enable-diagnostic-logging-in-azure-traffic-manager"></a>Povolit protokolování diagnostiky v Azure Traffic Manageru

Tento článek popisuje, jak povolit diagnostické protokolování a přístup data protokolu pro profil Traffic Manageru.

Diagnostické protokoly Azure Traffic Manager může poskytnout přehled o chování prostředků profilu Traffic Manageru. Například můžete použít data protokolu profil, který chcete-li zjistit, proč jednotlivé testy vypršel časový limit pro koncový bod.

## <a name="enable-diagnostic-logging"></a>Povolení protokolování diagnostiky

Můžete spouštět příkazy, které následují v [Azure Cloud Shell](https://shell.azure.com/powershell), nebo pomocí prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Při spuštění PowerShell z počítače, je nutné *AzureRM* modul prostředí PowerShell, 6.13.1 nebo novější. Můžete spustit `Get-Module -ListAvailable AzureRM` nainstalovanou verzi zjistíte. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Pokud používáte PowerShell místně, musíte také spustit `Login-AzureRmAccount` pro přihlášení k Azure.

1. **Načtěte profil služby Traffic Manager:**

    Pokud chcete povolit protokolování diagnostiky, budete potřebovat ID profilu služby Traffic Manager. Načíst profil Traffic Manageru, který chcete povolit diagnostické protokolování pomocí [Get-AzureRmTrafficManagerProfile](/powershell/module/AzureRM.TrafficManager/Get-AzureRmTrafficManagerProfile). Výstup obsahuje informace o ID profilu Traffic Manageru.

    ```azurepowershell-interactive
    Get-AzureRmTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **Povolte protokolování diagnostiky pro profil Traffic Manageru:**

    Povolit protokolování diagnostiky pro profil Traffic Manageru pomocí ID, kterou jste získali v předchozím kroku s [Set-AzureRmDiagnosticSetting](https://docs.microsoft.com/powershell/module/azurerm.insights/set-azurermdiagnosticsetting?view=latest). Následující příkaz uloží podrobné protokoly pro profil Traffic Manageru na zadaný účet Azure Storage. 

      ```azurepowershell-interactive
    Set-AzureRmDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **Ověřte nastavení diagnostiky:**

      Ověřte nastavení diagnostiky pro používání profilu Traffic Manageru [Get-AzureRmDiagnosticSetting](https://docs.microsoft.com/powershell/module/azurerm.insights/get-azurermdiagnosticsetting?view=latest). Následující příkaz zobrazí kategorie, které se protokolují pro prostředek.

     ```azurepowershell-interactive
     Get-AzureRmDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Ujistěte se, že všechny protokolu kategorií přidružených k zobrazení prostředků profilu Traffic Manageru jako povolené. Ověřte také, že účet úložiště je správně nastavená.

## <a name="access-log-files"></a>Přístup k souborům protokolu
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
1. Přejděte do svého účtu Azure Storage na portálu.
2. Na **přehled** stránky vaší služby Azure Storage account, v části **služby** vyberte **objekty BLOB**.
3. Pro **kontejnery**vyberte **přehledy. protokoly probehealthstatusevents**a přejděte dolů soubor PT1H.json a klikněte na tlačítko **Stáhnout** a stáhněte a uložte kopii tohoto protokolu soubor.

    ![Přístup k souborům protokolu z vašeho profilu Traffic Manageru z úložiště objektů blob](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>Schéma protokolu Traffic Manageru

Všechny diagnostické protokoly, které jsou k dispozici prostřednictvím služby Azure Monitor sdílejí společné schéma nejvyšší úrovně, s flexibilitou pro každou službu a vygenerovat jedinečné vlastnosti pro vlastní události. Schéma nejvyšší úrovně diagnostické protokoly, naleznete v tématu [podporované služby, schémat a kategorie pro diagnostické protokoly Azure](../azure-monitor/platform/tutorial-dashboards.md).

Následující tabulka obsahuje schéma protokoly specifické pro prostředek profilu Azure Traffic Manageru.

|||||
|----|----|---|---|
|**Název pole**|**Typ pole**|**Definice**|**Příklad**|
|EndpointName|Řetězec|Název sady koncových bodů Traffic Manageru, jejichž stav je zaznamenávány.|*myPrimaryEndpoint*|
|Status|Řetězec|Stav koncových bodů Traffic Manageru, který byl zjištěný. Stav může být buď **nahoru** nebo **dolů**.|**Nahoru**|
|||||

## <a name="next-steps"></a>Další postup

* Další informace o [monitorování Traffic Manageru](traffic-manager-monitoring.md)

