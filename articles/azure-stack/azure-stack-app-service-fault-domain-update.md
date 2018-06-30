---
title: 'Služby App Service v Azure zásobníku: poruch aktualizace domény | Microsoft Docs'
description: Jak znovu distribuovat Azure App Service v zásobníku Azure napříč doménami selhání
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2018
ms.author: anwestg
ms.openlocfilehash: ce57e153dcab6a386150ebefe1ecb4a018514247
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130366"
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>Jak znovu distribuovat Azure App Service v zásobníku Azure napříč doménami selhání

*Platí pro: Azure zásobníku integrované systémy*

K aktualizaci 1802 zásobník Azure nyní podporuje distribuci zatížení napříč doménami selhání funkce, která je důležité pro zajištění vysoké dostupnosti.

>[!IMPORTANT]
>Abyste mohli využívat podpora domén selhání, je třeba aktualizovat na 1802 zásobník Azure integrovaný systém. Tento dokument se vztahuje pouze k nasazení zprostředkovatele prostředků služby App Service, které byly dokončeny před aktualizací 1802. Pokud jste nasadili služby App Service v zásobníku Azure po 1802 aktualizace byla použita na Azure zásobníku, poskytovatel prostředků je již distribuovaný napříč doménami selhání.

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>Znovu vyvážit poskytovatele prostředků služby App Service napříč doménami selhání

Chcete-li distribuovat sady škálování nasazuje pro poskytovatele prostředků služby App Service, musíte provést kroky v tomto článku každé sadě škálování. Ve výchozím nastavení scaleset názvů:

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

>[!NOTE]
> Pokud nemáte instance nasazené v některých sad škálování vrstvy pracovního procesu, nemusíte znovu vyvážit tyto sady škálování. Sady škálování budou vyváženy správně při škálování je v budoucnosti.

Chcete-li škálovat sady škálování, postupujte takto:

1. Přihlaste se k portálu Azure zásobníku správce.
2. Vyberte **další služby**.
3. V části výpočetní, vyberte **sady škálování virtuálního počítače**. Objeví se existující sady škálování nasazen jako součást nasazení aplikace služby s informace o počtu instancí. Následující snímek obrazovky ukazuje příklad sady škálování.

      ![Azure App Service Škálovací sady uvedené v UX sady škálování virtuálního počítače][1]

4. Každá sada škálování. Například pokud máte tři existující instance v sadě škálování musí vertikální navýšení kapacity 6 tak tři nové instance nasazených napříč doménami selhání. Následující příklad PowerShell ukazuje se pro rozšíření Škálováním byly sadou škálování.

   ```powershell
   Add-AzureRmAccount -EnvironmentName AzureStackAdmin 

   # Get current scale set
   $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

   # Set and update the capacity of your scale set
   $vmss.sku.capacity = 6
   Update-AzureRmVmss -ResourceGroupName AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
   ```

   >[!NOTE]
   >Tento krok může trvat několik hodin, v závislosti na typu role a počet instancí.

5. V **aplikace služby správy rolí**, sledovat stav nové instance role. Pokud chcete zkontrolovat stav instanci role, vyberte v seznamu Typ role

    ![Aplikační služba Azure pro role Azure zásobníku][2]

6. Když se stav nové instance role **připraven**, přejděte zpět na **sadu škálování virtuálního počítače** a **odstranit** původní instance rolí.

7. Opakujte tyto kroky pro **každý** škálovací sadu virtuálních počítačů.

## <a name="next-steps"></a>Další postup

Můžete také zkusit dalších [platforma jako služba (PaaS) služby](azure-stack-tools-paas-services.md).

* [Poskytovatel prostředků SQL serveru](azure-stack-sql-resource-provider-deploy.md)
* [Poskytovatel prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
