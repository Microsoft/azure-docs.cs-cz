---
title: Horizontální navýšení kapacity rolí pracovního procesu v App Services – Azure Stack | Dokumentace Microsoftu
description: Podrobné pokyny pro škálování služby Azure Stack App Services
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: abfc75e40e146b1cf7cb237f18a1ff08626e5be1
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47092898"
---
# <a name="app-service-on-azure-stack-add-more-infrastructure-or-worker-roles"></a>App Service ve službě Azure Stack: přidejte další infrastrukturu nebo pracovní role

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*  

Tento dokument obsahuje pokyny ohledně toho, jak škálovat služby App Service na infrastrukturu a pracovních rolích Azure stacku. Obsahuje kroky pro vytvoření pracovní role pro podporu aplikací libovolné velikosti.

> [!NOTE]
> Pokud prostředí Azure Stack nemá více než 96 GB RAM, může mít potíže přidání dodatečnou kapacitu.

App Service ve službě Azure Stack, ve výchozím nastavení, podporuje vrstvy bezplatných a sdílených pracovních procesů. Přidání další vrstvy pracovních procesů, budete muset přidat další role pracovního procesu.

Pokud si nejste jisti, co byla nasazena s výchozí služby App Service na instalaci služby Azure Stack, najdete další informace v [služby App Service v Azure stacku přehled](azure-stack-app-service-overview.md).

Azure App Service ve službě Azure Stack nasazuje všech rolí pomocí Škálovací sady virtuálních počítačů a jako takový využívá možnosti škálování této úlohy. Proto všechny škálování vrstvy pracovních procesů se provádí prostřednictvím Správce App Service.

> [!IMPORTANT]
> Aktuálně není možné škálovat škálovací sady virtuálních počítačů na portálu jsme uvedli v poznámkách k verzi Azure Stack, proto použijte příklad Powershellu pro horizontální navýšení kapacity.
>
>

## <a name="add-additional-workers-with-powershell"></a>Přidat další pracovní procesy s využitím Powershellu

1. [Nastavení prostředí Azure stacku správu v prostředí PowerShell](azure-stack-powershell-configure-admin.md)

2. Použijte tento příklad pro horizontální navýšení kapacity škálovací sady:
   ```powershell
   
    ##### Scale out the AppService Role instances ######
   
    # Set context to AzureStack admin.
    Login-AzureRmAccount -EnvironmentName AzureStackAdmin
                                                 
    ## Name of the Resource group where AppService is deployed.
    $AppServiceResourceGroupName = "AppService.local"

    ## Name of the ScaleSet : e.g. FrontEndsScaleSet, ManagementServersScaleSet, PublishersScaleSet , LargeWorkerTierScaleSet,      MediumWorkerTierScaleSet, SmallWorkerTierScaleSet, SharedWorkerTierScaleSet
    $ScaleSetName = "SharedWorkerTierScaleSet"

    ## TotalCapacity is sum of the instances needed at the end of operation. 
    ## e.g. if your VMSS has 1 instance(s) currently and you need 1 more the TotalCapacity should be set to 2
    $TotalCapacity = 2  

    # Get current scale set
    $vmss = Get-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -VMScaleSetName $ScaleSetName

    # Set and update the capacity
    $vmss.sku.capacity = $TotalCapacity
    Update-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -Name $ScaleSetName -VirtualMachineScaleSet $vmss 
   ```    

   > [!NOTE]
   > Tento krok může trvat několik hodin v závislosti na typu role a počet instancí.
   >
   >

3. Monitorování stavu nové instance rolí v aplikaci Správa služby, chcete-li zkontrolovat stav instance jednotlivých rolí klikněte na typ role v seznamu.

## <a name="add-additional-workers-directly-within-the-app-service-resource-provider-admin"></a>Přidat další pracovní procesy přímo v rámci správce poskytovatele prostředků služby aplikace

1. Přihlaste se k portálu pro správu služby Azure Stack jako správce služeb.

2. Přejděte do **App Services**.

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Klikněte na tlačítko **role**. Tady vidíte rozpis všech rolí služby App Service nasazené.

4. Klikněte pravým tlačítkem na řádek škálování a potom klikněte na požadovaný typ **škálovací sady**.

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Klikněte na tlačítko **škálování**, vyberte počet instancí, kterou chcete škálovat a potom klikněte na **Uložit**.

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. App Service ve službě Azure Stack se nyní přidávání dalších virtuálních počítačů, je nakonfigurovat, nainstalovat požadovaný software a označit je jako připraven po dokončení tohoto procesu. Tento proces může trvat přibližně 80.

7. Průběh připravenosti nové role můžete monitorovat zobrazením pracovních procesů v **role** okno.

## <a name="result"></a>Výsledek

Jakmile jsou plně nasazený a připravený, zaměstnanců k dispozici pro uživatele k nasazení své úlohy do nich. Následující příklad několika cenových úrovních ve výchozím nastavení. Pokud neexistují žádné dostupné pracovní procesy pro konkrétní pracovní vrstvu, není k dispozici možnost si vybrat odpovídající cenové úrovně.

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> Pro horizontální navýšení kapacity správy, role front-endu nebo vydavatele přidat že musí horizontální navýšení kapacity odpovídající typ role. 
>
>

Pro horizontální navýšení kapacity správy, front-endu nebo role vydavatele, postupujte stejným způsobem výběru typu příslušné role. Řadiče nejsou nasazeni jako Škálovací sady a tedy dvě by měly být nasazeny v průběhu instalace pro všechna nasazení v produkčním prostředí.

### <a name="next-steps"></a>Další postup

[Konfigurace zdrojů nasazení](azure-stack-app-service-configure-deployment-sources.md)
