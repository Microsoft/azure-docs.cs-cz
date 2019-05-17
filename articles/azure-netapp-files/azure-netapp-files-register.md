---
title: Zaregistrujte se soubory Azure NetApp | Dokumentace Microsoftu
description: Popisuje, jak se zaregistrovat k použití souborů NetApp Azure.
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
ms.openlocfilehash: fbe0b82008d7b15332c4e2cd62c49c611f20fe89
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794700"
---
# <a name="register-for-azure-netapp-files"></a>Registrace pro Azure NetApp Files

> [!IMPORTANT] 
> Před registrací zprostředkovatele prostředků Azure NetApp Files, jste od týmu Azure NetApp Files potvrzení, že vám byl udělen přístup ke službě dostali e-mailu. 

V tomto článku najdete informace o postupu registrace pro Azure NetApp Files tak, aby mohli začít používat službu.

## <a name="waitlist"></a>Odeslat žádost o waitlist pro přístupu ke službě

1. Odeslat žádost o waitlist pro přístup k službě soubory Azure NetApp přes [stránku pro odesílání souborů NetApp Azure waitlist](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u). 

    Registrace Waitlist nezaručuje service okamžitý přístup. 

2. Počkejte oficiální potvrzení e-mailu od týmu služby soubory Azure NetApp, než budete pokračovat s ostatními úkoly. 

## <a name="resource-provider"></a>Registrace poskytovatele prostředků NetApp

K používání služby, zaregistrujte poskytovatele prostředků Azure pro Azure NetApp Files.

> [!NOTE] 
> Budete moci úspěšně zaregistrovat poskytovatele prostředků NetApp i bez udělení přístupu pro službu. Ale bez povolení přístupu k jakékoli webu Azure portal nebo rozhraní API požadavek na vytvoření účtu NetApp nebo jiný prostředek Azure NetApp soubory budou odmítnuty došlo k následující chybě:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. Na webu Azure Portal klikněte na ikonu Azure Cloud Shell v pravém horním rohu:

      ![Ikona Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Pokud máte více předplatných na účtu Azure, vyberte ten, který byl přidat na seznam povolených souborů NetApp Azure:
    
        az account set --subscription <subscriptionId>

3. V konzole Azure Cloud Shell zadejte následující příkaz k ověření, že vaše předplatné bylo povolené:
    
        az feature list | grep NetApp

   Výstup tohoto příkazu se zobrazí takto:
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/publicPreviewADC",  
       "name": "Microsoft.NetApp/publicPreviewADC" 
       
   `<SubID>` je vaše ID předplatného.

    Pokud se nezobrazí název funkce `Microsoft.NetApp/publicPreviewADC`, nemáte přístup ke službě. Zastavte v tomto kroku. Postupujte podle pokynů v [odeslat waitlist žádost o přístup k službě](#waitlist) na žádost o přístup k službě než budete pokračovat. 

4. V konzole Azure Cloud Shell zadejte následující příkaz pro registraci poskytovatele prostředků Azure: 
    
        az provider register --namespace Microsoft.NetApp --wait

   `--wait` Parametr nastaví konzoly čekání na registraci dokončit. Proces registrace může trvat nějakou dobu.

5. V konzole Azure Cloud Shell zadejte následující příkaz k ověření, že zaregistrovaný poskytovatel prostředků Azure: 
    
        az provider show --namespace Microsoft.NetApp

   Výstup tohoto příkazu se zobrazí takto:
   
        {
        "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
        "namespace": "Microsoft.NetApp", 
        "registrationState": "Registered", 
        "resourceTypes": […. 

   `<SubID>` je vaše ID předplatného.  `state` Určuje hodnotu parametru `Registered`.

6. Na webu Azure Portal, klikněte na tlačítko **předplatná** okno.
7. V okně předplatná klikněte na ID vašeho předplatného. 
8. V nastavení předplatné, klikněte na tlačítko **poskytovatelů prostředků** k ověření, že poskytovatel Microsoft.NetApp označuje stav registrované: 

      ![Registered Microsoft.NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>Další postup

[Vytvoření účtu NetApp](azure-netapp-files-create-netapp-account.md)