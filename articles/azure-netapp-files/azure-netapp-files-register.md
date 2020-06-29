---
title: Zaregistrujte se na Azure NetApp Files | Microsoft Docs
description: Popisuje, jak se zaregistrovat pro použití Azure NetApp Files.
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
ms.date: 06/09/2020
ms.author: b-juche
ms.openlocfilehash: 6d47da361303a0c421da035fc47608ba363ff82f
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2020
ms.locfileid: "85483529"
---
# <a name="register-for-azure-netapp-files"></a>Registrace pro Azure NetApp Files

> [!IMPORTANT] 
> Před registrací poskytovatele prostředků Azure NetApp Files musíte obdržet e-mail od Azure NetApp Files týmu potvrzující, že vám byl udělen přístup ke službě. 

V tomto článku se dozvíte, jak se zaregistrovat pro Azure NetApp Files, abyste mohli začít službu používat.

## <a name="submit-a-waitlist-request-for-accessing-the-service"></a><a name="waitlist"></a>Odeslat žádost o přístup ke službě pořadníku

1. Odešlete žádost o pořadníku k přístupu ke službě Azure NetApp Files prostřednictvím [stránky pro odeslání Azure NetApp Files pořadníku](https://aka.ms/azurenetappfiles). 

    Registrace pořadníku nezaručuje okamžitý přístup k službě. 

2. Než budete pokračovat s dalšími úkoly, počkejte na oficiální e-mail s potvrzením od Azure NetApp Files týmu. 

## <a name="register-the-netapp-resource-provider"></a><a name="resource-provider"></a>Registrace poskytovatele prostředků NetApp

Pokud chcete službu používat, musíte zaregistrovat poskytovatele prostředků Azure pro Azure NetApp Files.

> [!NOTE] 
> Poskytovatele prostředků NetApp budete moct úspěšně zaregistrovat i bez udělení přístupu ke službě. Bez přístupu k autorizaci ale nemusíte k vytvoření účtu NetApp nebo žádného jiného prostředku Azure NetApp Files zamítnout všechny Azure Portal ani požadavky na rozhraní API, a to s následující chybou:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. V Azure Portal klikněte na ikonu Azure Cloud Shell v pravém horním rohu:

      ![Ikona Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Pokud máte ve svém účtu Azure více předplatných, vyberte účet, který je povolený pro Azure NetApp Files:
    
        az account set --subscription <subscriptionId>

3. V konzole Azure Cloud Shell zadejte následující příkaz, který ověří, jestli je vaše předplatné v seznamu povolených:
    
        az feature list | grep NetApp

   Výstup příkazu se zobrazí takto:
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
       "name": "Microsoft.NetApp/ANFGA" 
       
   `<SubID>`je vaše ID vašeho předplatného.

    Pokud nevidíte název funkce, nemáte `Microsoft.NetApp/ANFGA` k této službě přístup. V tomto kroku zastavte. Než budete pokračovat, postupujte podle pokynů v tématu [odeslání žádosti pořadníku](#waitlist) o přístup ke službě, aby požádala o přístup k službě. 

4. V konzole Azure Cloud Shell zadejte následující příkaz pro registraci poskytovatele prostředků Azure: 
    
        az provider register --namespace Microsoft.NetApp --wait

   `--wait`Parametr dá konzole pokyn, aby čekala na dokončení registrace. Dokončení procesu registrace může trvat delší dobu.

5. V konzole Azure Cloud Shell zadejte následující příkaz, který ověří, jestli je poskytovatel prostředků Azure zaregistrovaný: 
    
        az provider show --namespace Microsoft.NetApp

   Výstup příkazu se zobrazí takto:
   
        {
        "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
        "namespace": "Microsoft.NetApp", 
        "registrationState": "Registered", 
        "resourceTypes": […. 

   `<SubID>`je vaše ID vašeho předplatného.  `state`Hodnota parametru označuje `Registered` .

6. V Azure Portal klikněte na okno **předplatná** .
7. V okně předplatná klikněte na své ID předplatného. 
8. V nastavení předplatného klikněte na **poskytovatelé prostředků** , abyste ověřili, že se pro poskytovatele Microsoft. NetApp označuje registrovaný stav: 

      ![Registrovaný Microsoft. NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>Další kroky

[Vytvoření účtu NetApp](azure-netapp-files-create-netapp-account.md)
