---
title: "Škálování role pracovního procesu v App Services - zásobník Azure | Microsoft Docs"
description: "Podrobné pokyny pro škálování Azure zásobníku aplikační služby"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 680cb70777574d0ed88c5f83fb0a6fa20263b951
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="app-service-on-azure-stack-add-more-infrastructure-or-worker-roles"></a>V zásobníku Azure App Service: přidání další infrastrukturu nebo pracovních rolí

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*  

Tento dokument obsahuje pokyny pro škálování služby App Service na infrastrukturu a pracovní role Azure zásobníku. Obsahuje kroky pro vytváření rolí další pracovního procesu pro podporu aplikací jakékoli velikosti.

> [!NOTE]
> Pokud vaše prostředí zásobníku Azure nemá více než 96 GB paměti RAM, můžete mít problémy přidání dodatečnou kapacitu.

App Service v zásobníku Azure ve výchozím nastavení, podporuje vrstev volné a sdílené pracovního procesu. Pokud chcete přidat další vrstvy pracovního procesu, potřebujete přidat další role pracovního procesu.

Pokud si nejste jisti, co byla nasazena s výchozí služby App Service na instalaci zásobník Azure, najdete další informace v [služby App Service na přehled Azure zásobníku](azure-stack-app-service-overview.md).

Aplikační služba Azure v zásobníku Azure nasadí všech rolí pomocí sady škálování virtuálního počítače a jako takový využívá možnosti škálování této úlohy. Proto všechny škálování vrstvy pracovního procesu se provádí prostřednictvím správce aplikace služby.

> [!IMPORTANT]
> Aktuálně není možné škálovat sady škálování virtuálního počítače v portálu, jak identifikovat v poznámkách k verzi Azure zásobníku, proto použijte příklad PowerShell pro rozšíření Škálováním.
>
>

## <a name="add-additional-workers-with-powershell"></a>Přidat další pracovní procesy pomocí prostředí PowerShell

1. [Nastavení prostředí správce Azure zásobníku v prostředí PowerShell](azure-stack-powershell-configure-admin.md)

2. Použijte tento příklad pro rozšíření Škálováním škálovací sadu:
   ```powershell
   
    ##### Scale out the AppService Role instances ######
   
    # Set context to AzureStack admin.
    Login-AzureRMAccount -EnvironmentName AzureStackAdmin
                                                 
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
   > Tento krok může trvat několik hodin, v závislosti na typu role a počet instancí.
   >
   >

3. Monitorování stavu nové instance role v aplikaci služby správy, chcete-li zkontrolovat stav instance jednotlivých rolí, klikněte na typ role v seznamu.

## <a name="add-additional-workers-directly-within-the-app-service-resource-provider-admin"></a>Přidat další pracovníků přímo v rámci správce aplikace služby prostředků zprostředkovatele.

1. Přihlaste se k portálu pro správu zásobník Azure jako správce služeb.

2. Přejděte do **aplikační služby**.

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Klikněte na tlačítko **role**. Tady vidíte rozpis všech rolí služby App Service nasadit.

4. Klikněte pravým tlačítkem na řádek škálování a potom klikněte na požadovaný typ **ScaleSet**.

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Klikněte na tlačítko **škálování**, vyberte počet instancí, kterou chcete škálovat a potom klikněte na **Uložit**.

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. V zásobníku Azure App Service bude nyní přidávání dalších virtuálních počítačů, je nakonfigurovat, nainstalovat požadovaný software a označit je jako připravené po dokončení tohoto procesu. Tento proces může trvat přibližně 80 minut.

7. Můžete sledovat průběh připravenosti na nové role zobrazením zaměstnanci z **role** okno.

## <a name="result"></a>Výsledek

Jakmile jsou plně nasazen a připraven, zaměstnanců k dispozici pro uživatele k nasazení své úlohy do nich. Následující ukazuje příklad několika cenové úrovně dostupná ve výchozím nastavení. Pokud nejsou žádné dostupné pracovní procesy pro konkrétní pracovní vrstvy, není k dispozici možnost vybrat odpovídající cenovou úroveň.

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> Chcete-li škálovat správy, role Front-endu nebo vydavatele přidat že musí škálovat odpovídající typ role. 
>
>

Pro rozšíření Škálováním správy, Front-endu nebo role vydavatele, postupujte stejným způsobem výběr typu příslušnou roli. Řadiče se nenasadí jako sady škálování a proto dva musí být nasazené v průběhu instalace pro všechny nasazení v produkčním prostředí.

### <a name="next-steps"></a>Další postup

[Konfigurace zdrojů nasazení](azure-stack-app-service-configure-deployment-sources.md)
