---
title: Připojení k rozhraní API Azure Media Services v3 – Python
description: Tento článek ukazuje, jak se připojit k rozhraní API Media Services v3 s Pythonem.
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
ms.openlocfilehash: 98a8cdf4120cf56184eb5735249640e3423acdf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888457"
---
# <a name="connect-to-media-services-v3-api---python"></a>Připojení k rozhraní API Media Services v3 – Python

Tento článek ukazuje, jak se připojit k Azure Media Services v3 Python SDK pomocí metody přihlášení instančního objektu.

## <a name="prerequisites"></a>Požadavky

- Stáhnout Python z [python.org](https://www.python.org/downloads/)
- Ujistěte se, `PATH` že jste nastavili proměnnou prostředí
- [Vytvořte účet mediálních služeb](create-account-cli-how-to.md). Nezapomeňte si zapamatovat název skupiny prostředků a název účtu Služby Media Services.
- Postupujte podle pokynů v tématu [Přístupová api.](access-api-cli-how-to.md) Zaznamenejte ID předplatného, ID aplikace (ID klienta), ověřovací klíč (tajný klíč) a ID klienta, které potřebujete v pozdějším kroku.

> [!IMPORTANT]
> Zkontrolujte [konvence pojmenování](media-services-apis-overview.md#naming-conventions).

## <a name="install-the-modules"></a>Instalace modulů

Chcete-li pracovat s Azure Media Services pomocí Pythonu, musíte nainstalovat tyto moduly.

* Modul, `azure-mgmt-resource` který obsahuje moduly Azure pro službu Active Directory.
* Modul, `azure-mgmt-media` který zahrnuje entity Media Services.

Otevřete nástroj příkazového řádku a k instalaci modulů použijte následující příkazy.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>Připojení ke klientovi Pythonu

1. Vytvoření souboru `.py` s příponou
1. Otevření souboru v oblíbeném editoru
1. Přidejte kód, který následuje do souboru. Kód importuje požadované moduly a vytvoří objekt pověření služby Active Directory, který potřebujete pro připojení ke službě Media Services.

      Nastavení hodnot proměnných na hodnoty, které jste získali z [přístupových api](access-api-cli-how-to.md)

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

1. Spuštění souboru

## <a name="next-steps"></a>Další kroky

- Použijte [Python SDK](https://aka.ms/ams-v3-python-sdk).
- Projděte si dokumentaci [k ref-](https://aka.ms/ams-v3-python-ref) Pythonu mediálních služeb.
