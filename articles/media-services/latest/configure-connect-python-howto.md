---
title: Připojení k Azure Media Services V3 API – Python
description: Tento článek ukazuje, jak se připojit k rozhraní Media Services V3 API pomocí Pythonu.
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
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74888457"
---
# <a name="connect-to-media-services-v3-api---python"></a>Připojení k Media Services V3 API – Python

V tomto článku se dozvíte, jak se připojit k sadě Azure Media Services V3 Python SDK pomocí metody přihlašování instančního objektu.

## <a name="prerequisites"></a>Předpoklady

- Stažení Pythonu z [Python.org](https://www.python.org/downloads/)
- Ujistěte se, že jste nastavili proměnnou prostředí `PATH`.
- [Vytvoření účtu Media Services](create-account-cli-how-to.md). Nezapomeňte si pamatovat název skupiny prostředků a název účtu Media Services.
- Postupujte podle kroků v tématu [rozhraní API pro přístup](access-api-cli-how-to.md) . Poznamenejte si ID předplatného, ID aplikace (ID klienta), ověřovací klíč (tajný klíč) a ID tenanta, které budete potřebovat v pozdějším kroku.

> [!IMPORTANT]
> Přečtěte si [zásady vytváření názvů](media-services-apis-overview.md#naming-conventions).

## <a name="install-the-modules"></a>Instalace modulů

Pokud chcete pracovat s Azure Media Services pomocí Pythonu, musíte tyto moduly nainstalovat.

* Modul `azure-mgmt-resource`, který zahrnuje moduly Azure pro službu Active Directory.
* Modul `azure-mgmt-media`, který obsahuje entity Media Services.

Otevřete nástroj příkazového řádku a pomocí následujících příkazů nainstalujte moduly.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>Připojení k klientovi Python

1. Vytvoření souboru s rozšířením `.py`
1. Otevřete soubor ve svém oblíbeném editoru.
1. Do souboru přidejte kód, který následuje. Kód importuje požadované moduly a vytvoří objekt přihlašovacích údajů služby Active Directory, ke kterému se potřebujete připojit Media Services.

      Nastavte hodnoty proměnných na hodnoty, které jste získali z [rozhraní API pro přístup](access-api-cli-how-to.md) .

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

1. Spustit soubor

## <a name="next-steps"></a>Další kroky

- Použijte [Python SDK](https://aka.ms/ams-v3-python-sdk).
- Přečtěte si referenční dokumentaci k Media Services [Pythonu](https://aka.ms/ams-v3-python-ref) .
