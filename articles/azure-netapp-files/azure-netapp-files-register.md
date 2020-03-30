---
title: Registrace k souborům Azure NetApp | Dokumenty společnosti Microsoft
description: Popisuje, jak se zaregistrovat k použití souborů Azure NetApp.
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
ms.date: 05/06/2019
ms.author: b-juche
ms.openlocfilehash: 6f5d84dea2e835fd12a062b628181354295ed9f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274057"
---
# <a name="register-for-azure-netapp-files"></a>Registrace pro Azure NetApp Files

> [!IMPORTANT] 
> Před registrací zprostředkovatele prostředků Azure NetApp Files musíte obdržet e-mail od týmu Soubory Azure NetApp potvrzující, že vám byl udělen přístup ke službě. 

V tomto článku se dozvíte, jak se zaregistrovat pro soubory Azure NetApp, abyste mohli začít používat službu.

## <a name="submit-a-waitlist-request-for-accessing-the-service"></a><a name="waitlist"></a>Odeslat požadavek na čekací listinu pro přístup ke službě

1. Odešlete žádost o čekací listinu pro přístup ke službě Azure NetApp Files prostřednictvím [stránky odeslání seznamu souborů Azure NetApp](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u). 

    Registrace seznamu čekánky nezaručuje okamžitý přístup ke službě. 

2. Než budete pokračovat v dalších úkolech, počkejte na oficiální potvrzovací e-mail od týmu Azure NetApp Files. 

## <a name="register-the-netapp-resource-provider"></a><a name="resource-provider"></a>Registrace zprostředkovatele prostředků NetApp

Chcete-li službu používat, musíte zaregistrovat zprostředkovatele prostředků Azure pro soubory Azure NetApp.

> [!NOTE] 
> Budete moci úspěšně zaregistrovat Poskytovatele prostředků NetApp i bez udělení přístupu ke službě. Bez autorizace přístupu však bude odmítnut jakýkoli portál Azure nebo požadavek rozhraní API na vytvoření účtu NetApp nebo jiného prostředku souborů Azure NetApp s následující chybou:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. Na webu Azure Portal klikněte na ikonu Azure Cloud Shell v pravém horním rohu:

      ![Ikona prostředí Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Pokud máte na účtu Azure víc předplatných, vyberte ten, který byl na seznamu povolených pro soubory Azure NetApp:
    
        az account set --subscription <subscriptionId>

3. V konzole Azure Cloud Shell zadejte následující příkaz a ověřte, že vaše předplatné bylo na seznamu povolených:
    
        az feature list | grep NetApp

   Výstup příkazu se zobrazí takto:
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
       "name": "Microsoft.NetApp/ANFGA" 
       
   `<SubID>`je vaše ID předplatného.

    Pokud název funkce nevidíte `Microsoft.NetApp/ANFGA`, nemáte přístup ke službě. Zastavte se u tohoto kroku. Postupujte podle pokynů v [Odeslat čekací listinu žádost o přístup ke službě](#waitlist) požádat o přístup ke službě před pokračováním. 

4. V konzole Azure Cloud Shell zadejte následující příkaz pro registraci zprostředkovatele prostředků Azure: 
    
        az provider register --namespace Microsoft.NetApp --wait

   Parametr `--wait` instruuje konzolu čekat na dokončení registrace. Proces registrace může nějakou dobu trvat.

5. V konzole Azure Cloud Shell zadejte následující příkaz a ověřte, že poskytovatel prostředků Azure byl zaregistrován: 
    
        az provider show --namespace Microsoft.NetApp

   Výstup příkazu se zobrazí takto:
   
        {
        "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
        "namespace": "Microsoft.NetApp", 
        "registrationState": "Registered", 
        "resourceTypes": […. 

   `<SubID>`je vaše ID předplatného.  Hodnota `state` parametru `Registered`označuje .

6. Na portálu Azure klikněte na okno **Předplatná.**
7. V okně Předplatná klikněte na ID předplatného. 
8. V nastavení předplatného klikněte na **Zprostředkovatelé prostředků** a ověřte, že poskytovatel Microsoft.NetApp označuje stav Registrovaný: 

      ![Registrovaný Microsoft.NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>Další kroky

[Vytvoření účtu NetApp](azure-netapp-files-create-netapp-account.md)