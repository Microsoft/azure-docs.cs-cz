---
title: Připojení k rozhraní API služby Azure Media Services v3 – Python
description: Zjistěte, jak se připojit k rozhraní API služby Media Services v3 s využitím Pythonu.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 971e36b600a2c6be516e39ce84ca5780a2f23bbd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733092"
---
# <a name="connect-to-media-services-v3-api---python"></a>Připojení k rozhraní API služby Media Services v3 – Python

Tento článek popisuje, jak se připojit k Azure Media Services v3 Python SDK pomocí přihlašovacího instančního objektu služby v metodě.

## <a name="prerequisites"></a>Požadavky

- Stáhněte si Python z [python.org](https://www.python.org/downloads/)
- Nezapomeňte nastavit `PATH` proměnné prostředí
- [Vytvoření účtu Media Services](create-account-cli-how-to.md). Je potřeba pamatovat název skupiny prostředků a název účtu Media Services.
- Postupujte podle pokynů [přístup k rozhraní API](access-api-cli-how-to.md) tématu. Zaznamenejte ID předplatného, ID aplikace (ID klienta), ověřovací klíč (tajný klíč) a ID tenanta, který budete potřebovat v pozdějším kroku.

## <a name="install-the-modules"></a>Instalace modulů

Pro práci s Azure Media Services pomocí Pythonu, budete muset nainstalovat tyto moduly.

* `azure-mgmt-resource` Modul, který zahrnuje moduly Azure pro Active Directory.
* `azure-mgmt-media` Modulu, které zahrnuje entity Media Services.

Otevřete nástroj příkazového řádku a pomocí následujících příkazů nainstalujte moduly.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>Připojte se ke klientovi Pythonu

1. Vytvořte soubor s `.py` rozšíření
1. Otevřete soubor ve svém oblíbeném editoru
1. Přidejte kód, který následuje do souboru. Kód importuje požadované moduly. a vytvoří objekt přihlašovacích údajů služby Active Directory, který je nutné se připojit ke službě Media Services.

      Nastavte hodnoty proměnné na hodnoty, které jste získali z [přístup k rozhraní API](access-api-cli-how-to.md)

      ```
      import adal
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
      from azure.mgmt.media import AzureMediaServices
      from azure.mgmt.media.models import MediaService

      RESOURCE = 'https://management.core.windows.net/'
      # Tenant ID for your Azure Subscription
      TENANT_ID = '00000000-0000-0000-000000000000'
      # Your Service Principal App ID
      CLIENT = '00000000-0000-0000-000000000000'
      # Your Service Principal Password
      KEY = '00000000-0000-0000-000000000000'
      # Your Azure Subscription ID
      SUBSCRIPTION_ID = '00000000-0000-0000-000000000000'
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = 'amsv3account'
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = 'AMSv3ResourceGroup'

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials,
          RESOURCE,
          CLIENT,
          KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print (client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. Spusťte soubor

## <a name="next-steps"></a>Další postup

- Použití [Python SDK](https://aka.ms/ams-v3-python-sdk).
- Projděte si Media Services [Python ref](https://aka.ms/ams-v3-python-ref) dokumentaci.
