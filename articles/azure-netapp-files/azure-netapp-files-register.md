---
title: Zaregistrujte se soubory Azure NetApp | Dokumentace Microsoftu
description: Popisuje, jak odeslat žádost o registraci ve službě soubory Azure NetApp.
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
ms.date: 01/04/2018
ms.author: b-juche
ms.openlocfilehash: 86c016a5dbcc0d78378e59bc6b3606ddf2c54f64
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60452759"
---
# <a name="register-for-azure-netapp-files"></a>Registrace pro Azure NetApp Files
Než začnete používat soubory Azure NetApp, musíte odeslat žádost o registraci ve službě soubory Azure NetApp.  Po registraci je potom zaregistrovat k používání služby.

## <a name="request-to-enroll-in-the-service"></a>Požadavek na registraci ve službě
Musíte být součástí programu ve verzi Public Preview a pro přístup k poskytovateli prostředků Microsoft.NetApp na seznamu povolených. Podrobnosti o zapojení do programu verze Public Preview najdete na [registrační stránce Azure NetApp Files Public Preview](https://aka.ms/nfspublicpreview). 


## <a name="register-the-netapp-resource-provider"></a>Registrace poskytovatele prostředků NetApp

K používání služby, zaregistrujte poskytovatele prostředků Azure pro Azure NetApp Files. 

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