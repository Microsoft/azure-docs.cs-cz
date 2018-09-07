---
title: 'App Service v Azure stacku: selhání aktualizace domény | Dokumentace Microsoftu'
description: Jak distribuovat napříč doménami selhání služby Azure App Service ve službě Azure Stack
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
ms.date: 09/05/2018
ms.author: anwestg
ms.openlocfilehash: acadd1adec93d10d64712a2fbedb89e098998294
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44025958"
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>Jak distribuovat napříč doménami selhání služby Azure App Service ve službě Azure Stack

*Platí pro: integrované systémy Azure Stack*

Aktualizace 1802 Azure Stack teď podporuje distribuci zatížení napříč doménami selhání, funkce, která je velmi důležité pro zajištění vysoké dostupnosti.

>[!IMPORTANT]
>Abyste mohli využívat podporu domény selhání, je třeba aktualizovat váš systém Azure Stack integrované na verzi 1802. Tento dokument se vztahuje pouze na nasazení poskytovatele prostředků App Service, které byly dokončeny před aktualizace 1802. Pokud jste nasadili služby App Service ve službě Azure Stack po aktualizace 1802 byla použita na služby Azure Stack, poskytovatel prostředků již distribuované napříč doménami selhání.

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>Napříč doménami selhání obnovit rovnováhu poskytovatele prostředků App Service

Opětovná distribuce škálovací sady nasadit pro poskytovatele prostředků App Service, musíte provést kroky v tomto článku pro každou škálovací sadu. Ve výchozím nastavení jsou název škálovací sady:

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

>[!NOTE]
> Pokud nemáte k dispozici instance nasazené v některé z škálovací sady vrstvy pracovního procesu, není nutné znovu vyvážit těchto škálovacích sadách. Škálovací sady se s vyrovnáváním správně, když je v budoucích škálujete.

Horizontální navýšení kapacity škálovací sady, postupujte podle těchto kroků:

1. Přihlaste se k portálu Azure Stack správce.
1. Vyberte **Všechny služby**.
2. V **COMPUTE** vyberte **škálovací sady virtuálních počítačů**. Zobrazí se existující škálovací sady, které jsou nasazované jako součást nasazení služby App Service s informacemi o počtu instancí. Následující snímek obrazovky ukazuje příklad škálovací sady.

      ![Azure App Service Škálovací sady uvedené v uživatelském prostředí virtuálního počítače Škálovací sady][1]

1. Horizontálně navýšit kapacitu jednotlivých sad. Například pokud máte tři existující instancí ve škálovací sadě můžete musí horizontální navýšení kapacity na 6 proto se tři nové instance nasazených napříč doménami selhání. Následující příklad PowerShell ukazuje navýšení kapacity pro horizontální navýšení kapacity škálovací sady.

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

1. V **role pro správu aplikace App Service**, sledovat stav nové instance role. Pokud chcete zkontrolovat stav role instance, v seznamu vyberte typ role

    ![Azure App Service v rolích Azure stacku][2]

1. Pokud je stav nové instance role **připravené**, vraťte se zpět do **Škálovací sady virtuálních počítačů** a **odstranit** staré instancí rolí.

1. Opakujte tyto kroky pro **každý** škálovací sadu virtuálních počítačů.

## <a name="next-steps"></a>Další postup

Můžete také vyzkoušet ostatní [platforma jako služba (PaaS) služby](azure-stack-tools-paas-services.md).

* [Poskytovatele prostředků SQL serveru](azure-stack-sql-resource-provider-deploy.md)
* [Poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
