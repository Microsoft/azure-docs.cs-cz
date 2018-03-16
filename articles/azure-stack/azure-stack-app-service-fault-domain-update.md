---
title: "Služby App Service v Azure zásobníku: poruch aktualizace domény | Microsoft Docs"
description: "Jak znovu distribuovat Azure App Service v zásobníku Azure napříč doménami selhání"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: anwestg
ms.openlocfilehash: 851747263879aa89fabe8b168876238a004ea8b2
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>Jak znovu distribuovat Azure App Service v zásobníku Azure napříč doménami selhání

*Platí pro: Azure zásobníku integrované systémy*

K aktualizaci 1802 zásobník Azure nyní podporuje distribuci úloh napříč doménami selhání, funkci, která je velmi důležitá pro vysokou dostupnost.

> [!IMPORTANT]
> Musí jste aktualizovali systému Azure zásobníku integrované do 1802 mohli využít výhod podpory domény selhání.  Tento dokument se vztahuje pouze k nasazení zprostředkovatele prostředků služby App Service, které byly dokončeny před aktualizací 1802.  Pokud jste nasadili služby App Service v zásobníku Azure po 1802 aktualizace byla použita na Azure zásobníku, poskytovatel prostředků je již distribuovaný napříč doménami selhání.
>
>

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>Znovu vyvážit poskytovatele prostředků služby App Service napříč doménami selhání

Chcete-li znovu distribuovat sady škálování nasazuje pro poskytovatele prostředků služby App Service, musíte provést následující kroky pro každý sadě škálování.  Ve výchozím nastavení jsou názvy scaleset:

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

> [!NOTE]
> Pokud máte žádné instance nasazené v některých pracovních sad škálování vrstvy, není potřeba znovu vyvážit tyto sady škálování.  Sady škálování budou vyváženy správně při škálování je v budoucnosti.
>
>

1. Přejděte do sady škálování virtuálního počítače na portálu Azure zásobníku správce.  Objeví se existující sady škálování nasazen jako součást nasazení aplikace služby s informace o počtu instancí.

    ![Azure App Service Škálovací sady uvedené v UX sady škálování virtuálního počítače][1]

2. Každá sada škálování Další.  Například pokud máte tři existující instance v sadě škálování musí vertikální navýšení kapacity 6 tak, aby tři nové instance, se zřídí napříč doménami selhání.
    a. [Nastavení prostředí správce Azure zásobníku v prostředí PowerShell](azure-stack-powershell-configure-admin.md) b. Použijte tento příklad pro rozšíření Škálováním škálovací sadu:
        ```powershell
                Login-AzureRMAccount -EnvironmentName AzureStackAdmin 

                # Get current scale set
                $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

                # Set and update the capacity of your scale set
                $vmss.sku.capacity = 6
                Update-AzureRmVmss -ResourceGroupName "AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
        '''
> [!NOTE]
> Tento krok může trvat několik hodin, v závislosti na typu role a počet instancí.
>
>

3. Monitorujte stav instancí nové role v okně aplikace služby správy rolí.  Zkontrolujte stav instance jednotlivých rolí kliknutím typ role v seznamu

    ![Aplikační služba Azure pro role Azure zásobníku][2]

4. Jeden nové instance jsou v **připraven** stavu, přejděte zpět do okna sadu škálování virtuálního počítače a **odstranit** původní instancí.

5. Opakujte tyto kroky pro **každý** škálovací sadu virtuálních počítačů.

## <a name="next-steps"></a>Další kroky

Můžete také zkusit dalších [platforma jako služba (PaaS) služby](azure-stack-tools-paas-services.md).

* [Poskytovatel prostředků SQL serveru](azure-stack-sql-resource-provider-deploy.md)
* [Poskytovatel prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
