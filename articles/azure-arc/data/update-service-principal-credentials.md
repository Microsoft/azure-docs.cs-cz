---
title: Aktualizace přihlašovacích údajů instančního objektu
description: Aktualizovat přihlašovací údaje pro instanční objekt
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: mikeray
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: 1c7ccec6228a6dcfb457bda8f6ecd384775d4b27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "104669538"
---
# <a name="update-service-principal-credentials"></a>Aktualizace přihlašovacích údajů instančního objektu

Když se přihlašovací údaje instančního objektu změní, je potřeba aktualizovat tajné klíče v řadiči dat.

Pokud jste například nasadili řadič dat pomocí konkrétní sady hodnot ID tenanta instančního objektu, ID klienta a tajného klíče klienta a pak změníte jednu nebo více těchto hodnot, budete muset tajné klíče aktualizovat v řadiči dat.  Níže jsou uvedené pokyny k aktualizaci ID tenanta, ID klienta nebo tajného klíče klienta. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="background"></a>Pozadí

Instanční objekt byl vytvořen v [objektu Create Service instančního](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)objektu. 

## <a name="steps"></a>Postup

1. Přístup k hlavnímu názvu objektu služby ve výchozím editoru.

   ```console
   kubectl edit secret/upload-service-principal-secret -n <name of namespace>
   ```

   Chcete-li například upravit tajný klíč objektu služby na řadič dat v `arc` oboru názvů, spusťte následující příkaz:

   ```console
   kubectl edit secret/upload-service-principal-secret -n arc
   ```

   `kubecl edit`Příkaz otevře soubor s přihlašovacími údaji. yml ve výchozím editoru. 


1. Upravte tajný klíč objektu služby. 

   Ve výchozím editoru nahraďte hodnoty v části data aktualizovanými informacemi o přihlašovacích údajích.

   Například:

   ```console
   # Please edit the object below. Lines beginning with a '#' will be ignored,
   # and an empty file will abort the edit. If an error occurs while saving this file will be
   # reopened with the relevant failures.
   #
   apiVersion: v1
   data:
     authority: aHR0cHM6Ly9sb2dpbi5taWNyb3NvZnRvbmxpbmUuY29t
     clientId: NDNiNDcwYrFTGWYzOC00ODhkLTk0ZDYtNTc0MTdkN2YxM2Uw
     clientSecret: VFA2RH125XU2MF9+VVhXenZTZVdLdECXFlNKZi00Lm9NSw==
     tenantId: NzJmOTg4YmYtODZmMRFVBGTJLSATkxYWItMmQ3Y2QwMTFkYjQ3
   kind: Secret
   metadata:
     creationTimestamp: "2020-12-02T05:02:04Z"
     name: upload-service-principal-secret
     namespace: arc
     resourceVersion: "7235659"
     selfLink: /api/v1/namespaces/arc/secrets/upload-service-principal-secret
     uid: 7fb693ff-6caa-4a31-b83e-9bf22be4c112
   type: Opaque
   ```

   Upravte hodnoty pro `clientID` , `clientSecret` nebo `tenantID` podle potřeby. 

> [!NOTE]
>Hodnoty musí být kódované v kódování Base64. Neupravujte žádné další vlastnosti.

Pokud je k dispozici nesprávná hodnota pro `clientId` , `clientSecret` nebo se `tenantID` zobrazí chybová zpráva uvedená v `control-xxxx` protokolech kontejneru pod/Controller:

```output
YYYY-MM-DD HH:MM:SS.mmmm | ERROR | [AzureUpload] Upload task exception: A configuration issue is preventing authentication - check the error message from the server for details.You can modify the configuration in the application registration portal. See https://aka.ms/msal-net-invalid-client for details.  Original exception: AADSTS7000215: Invalid client secret is provided.
```



## <a name="next-steps"></a>Další kroky

[Načtení uživatelského jména a hesla pro připojení ke kontroleru dat ARC](retrieve-the-username-password-for-data-controller.md)

[Vytvoření instančního objektu](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)
