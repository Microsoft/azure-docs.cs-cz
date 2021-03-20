---
title: Vytvoření účtu NetApp pro přístup k Azure NetApp Files | Microsoft Docs
description: Popisuje způsob přístupu k Azure NetApp Files a vytvoření účtu NetApp, aby bylo možné nastavit fond kapacity a vytvořit svazek.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: e5e0c43aa8c4fbf0db89065e0fab93ee174a4ede
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96006589"
---
# <a name="create-a-netapp-account"></a>Vytvoření účtu NetApp
Vytvoření účtu NetApp umožňuje nastavit fond kapacity a následně vytvořit svazek. K vytvoření nového účtu NetApp se používá okno Azure NetApp Files.

## <a name="before-you-begin"></a>Než začnete
Je nutné, abyste obdrželi e-mail od Azure NetApp Files týmu potvrzujícího, že vám byl udělen přístup k této službě. Viz [odeslání žádosti o přístup ke službě pořadníku](azure-netapp-files-register.md#waitlist).

Musíte taky zaregistrovat předplatné pro používání poskytovatele prostředků NetApp. Viz [registrace poskytovatele prostředků NetApp](azure-netapp-files-register.md#resource-provider).

## <a name="steps"></a>Postup 

1. Přihlaste se k webu Azure Portal. 
2. Přejděte do okna Azure NetApp Files pomocí jedné z následujících metod:  
   * Vyhledejte **Azure NetApp Files** ve vyhledávacím poli webu Azure Portal.  
   * Klikněte na **Všechny služby** v navigaci a potom filtrujte Azure NetApp Files.  

   Okno Azure NetApp Files můžete označit jako oblíbené kliknutím na ikonu hvězdičky vedle něj. 

3. Kliknutím na **+ Přidat** vytvořte nový účet NetApp.  
   Zobrazí se okno Nový účet NetApp.  

4. Zadejte pro účet NetApp následující informace: 
   * **Název účtu**  
     Zadejte jedinečný název pro předplatné.
   * **Předplatné**  
     Vyberte předplatné z vašich stávajících předplatných.
   * **Skupina prostředků**   
     Použijte existující skupinu prostředků, nebo vytvořte novou.
   * **Umístění**  
     Vyberte oblast, kde má být umístěný účet a jeho podřízené prostředky.  

     ![Nový účet NetApp](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. Klikněte na **Vytvořit**.     
   Vytvořený účet NetApp se teď zobrazí v okně Azure NetApp Files. 

> [!NOTE] 
> Pokud jste neudělili přístup ke službě Azure NetApp Files, při pokusu o vytvoření prvního účtu NetApp se zobrazí následující chyba:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`

## <a name="next-steps"></a>Další kroky  

[Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)

