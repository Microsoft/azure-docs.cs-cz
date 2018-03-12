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
ms.openlocfilehash: d6471796863a80e69fdaf740b68fb27d59503453
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
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
    ## e.g. if you VMSS has 1 instance(s) currently and you need 1 more the TotalCapacity should be set to 2
    $TotalCapacity = 2  

    # Get current scale set
    $vmss = Get-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -VMScaleSetName $ScaleSetName

    # Set and update the capacity
    $vmss.sku.capacity = $TotalCapacity
    Update-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -Name $ScaleSetName -VirtualMachineScaleSet $vmss 
  
    '''

> [!NOTE]
> This step can take a number of hours to complete depending on the type of role and the number of instances.
>
>

3. Monitor the status of the new role instances in the App Service Administration, to check the status of an individual role instance click the role type in the list.

## Add additional workers directly within the App Service Resource Provider Admin.

1. Log in to the Azure Stack administration portal as the service administrator.

2. Browse to **App Services**.

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Click **Roles**. Here you see the breakdown of all App Service roles deployed.

4. Right click on the row of the type you want to scale and then click **ScaleSet**.

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Click **Scaling**, select the number of instances you want to scale to, and then click **Save**.

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. App Service on Azure Stack will now add the additional VMs, configure them, install all the required software, and mark them as ready when this process is complete. This process can take approximately 80 minutes.

7. You can monitor the progress of the readiness of the new roles by viewing the workers in the **Roles** blade.

## Result

After they are fully deployed and ready, the workers become available for users to deploy their workload onto them. The following shows an example of the multiple pricing tiers available by default. If there are no available workers for a particular worker tier, the option to choose the corresponding pricing tier is unavailable.

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> To scale out Management, Front End or Publisher roles add you must scale out the corresponding role type. 
>
>

To scale out Management, Front End, or Publisher roles, follow the same steps selecting the appropriate role type. Controllers are not deployed as Scale Sets and therefore two should be deployed at Installation time for all production deployments.

### Next steps

[Configure deployment sources](azure-stack-app-service-configure-deployment-sources.md)
