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
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 25cae58663f6fa7ef27995c10509eb33e49dd4c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70012575"
---
# <a name="create-a-netapp-account"></a>Vytvoření účtu NetApp
Vytvoření účtu NetApp umožňuje nastavit fond kapacity a následně vytvořit svazek. K vytvoření nového účtu NetApp se používá okno Azure NetApp Files.

## <a name="before-you-begin"></a>Než začnete
Musíte obdržet e-mail od týmu Soubory Azure NetApp potvrzující, že vám byl udělen přístup ke službě. Viz [Odeslání žádosti o čekací listinu pro přístup ke službě](azure-netapp-files-register.md#waitlist).

Musíte také zaregistrovat své předplatné pro používání poskytovatele prostředků NetApp. Viz [Registrace poskytovatele prostředků NetApp](azure-netapp-files-register.md#resource-provider).

## <a name="steps"></a>Kroky 

1. Přihlaste se k portálu Azure. 
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
> Pokud vám nebyl udělen přístup ke službě Azure NetApp Files, zobrazí se při pokusu o vytvoření prvního účtu NetApp následující chyba:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`

## <a name="next-steps"></a>Další kroky  

[Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)

