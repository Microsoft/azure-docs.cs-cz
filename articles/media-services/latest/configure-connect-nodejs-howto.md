---
title: Připojení k rozhraní Azure Media Services V3 API – Node.js
description: Tento článek ukazuje, jak se připojit k rozhraní Media Services V3 API pomocí Node.js.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/17/2021
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: 9628e46281e267bd1c1fd8277a3a975bc338c6dc
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101096062"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Připojení k rozhraní Media Services V3 API – Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

V tomto článku se dozvíte, jak se připojit ke službě Azure Media Services V3 node.js SDK pomocí metody přihlašování instančního objektu.

## <a name="prerequisites"></a>Požadavky

- Nainstalujte [Node.js](https://nodejs.org/en/download/).
- Nainstalujte [TypeScript](https://www.typescriptlang.org/download).
- [Vytvořte účet Media Services](./create-account-howto.md). Nezapomeňte si pamatovat název skupiny prostředků a název účtu Media Services.

> [!IMPORTANT]
> Přečtěte si Azure Media Services [konvence pojmenování](media-services-apis-overview.md#naming-conventions) a pochopte důležitá omezení pojmenování entit. 

## <a name="reference-documentation-for-azurearm-mediaservices"></a>Referenční dokumentace pro @Azure/arm-mediaservices
- [Referenční dokumentace k modulům Azure Media Services pro Node.js](https://docs.microsoft.com/javascript/api/overview/azure/media-services?view=azure-node-latest)

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Další dokumentaci pro vývojáře k Node.js v Azure
- [Azure pro JavaScript & Node.js vývojáři](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [Media Services zdrojový kód v @azure/azure-sdk-for-js úložišti centra Git](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Dokumentace k balíčku Azure pro vývojáře Node.js](https://docs.microsoft.com/javascript/api/overview/azure/?view=azure-node-latest)

## <a name="install-the-packages"></a>Nainstalovat balíčky

1. Vytvořte package.jsv souboru pomocí oblíbeného editoru.
1. Otevřete soubor a vložte následující kód:

   Nezapomeňte získat nejnovější verzi sady [AZUREMEDIASERVICES SDK pro JavaScript](https://www.npmjs.com/package/@azure/arm-mediaservices).

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.ts",
  "dependencies": {
    "@azure/arm-mediaservices": "^8.0.0",
    "@azure/abort-controller": "^1.0.2",
    "@azure/ms-rest-nodeauth": "^3.0.6",
    "@azure/storage-blob": "^12.4.0",
  }
}
```

Je třeba zadat následující balíčky:

|Balíček|Popis|
|---|---|
|`@azure/arm-mediaservices`|Azure Media Services SDK. <br/>Pokud chcete mít jistotu, že používáte nejnovější balíček Azure Media Services, zkontrolujte [instalaci @azure/arm-mediaservices npm ](https://www.npmjs.com/package/@azure/arm-mediaservices).|
|`@azure/ms-rest-nodeauth` | Vyžadováno pro ověřování AAD pomocí instančního objektu nebo spravované identity|
|`@azure/storage-blob`|Sada SDK pro úložiště Používá se při nahrávání souborů do assetů.|
|`@azure/ms-rest-js`| Slouží k přihlášení.|
|`@azure/storage-blob` | Slouží k nahrání a stažení souborů do assetů v Azure Media Services pro kódování.|
|`@azure/abort-controller`| Používá se společně s klientem úložiště k vypršení časového limitu dlouho spuštěných operací stahování.|


Abyste měli jistotu, že používáte nejnovější balíček, můžete spustit následující příkaz:

### <a name="install-azurearm-mediaservices"></a>Znovu @azure/arm-mediaservices
```
npm install @azure/arm-mediaservices
```

### <a name="install-azurems-rest-nodeauth"></a>Znovu @azure/ms-rest-nodeauth

Nainstalujte prosím minimální verzi " @azure/ms-rest-nodeauth ": "^ 3.0.0".

```
npm install @azure/ms-rest-nodeauth@"^3.0.0"
```

## <a name="connect-to-nodejs-client-using-typescript"></a>Připojení k Node.js klienta pomocí TypeScriptu

1. Pomocí oblíbeného editoru vytvořte soubor TypeScript. TS.
1. Otevřete soubor a vložte následující kód.
1. Vytvořte soubor. ENV a vyplňte podrobnosti z Azure Portal. Viz [rozhraní API pro přístup](./access-api-howto.md).

### <a name="sample-env-file"></a>Ukázkový soubor. env
```
# copy the content of this file to a file named ".env". It should be stored at the root of the repo.
# The values can be obtained from the API Access page for your Media Services account in the portal.
AZURE_CLIENT_ID=""
AZURE_CLIENT_SECRET= ""
AZURE_TENANT_ID= ""

# Change this to match your AAD Tenant domain name. 
AAD_TENANT_DOMAIN = "microsoft.onmicrosoft.com"

# Set this to your Media Services Account name, resource group it is contained in, and location
AZURE_MEDIA_ACCOUNT_NAME = ""
AZURE_LOCATION= ""
AZURE_RESOURCE_GROUP= ""

# Set this to your Azure Subscription ID
AZURE_SUBSCRIPTION_ID= ""

# You must change these if you are using Gov Cloud, China, or other non standard cloud regions
AZURE_ARM_AUDIENCE= "https://management.core.windows.net"
AZURE_ARM_ENDPOINT="https://management.azure.com"

# DRM Testing
DRM_SYMMETRIC_KEY="add random base 64 encoded string here"
```

## <a name="typescript---hello-world---list-assets"></a>TypeScript-Hello World – seznam prostředků
V této ukázce se dozvíte, jak se připojit k klientovi Media Services pomocí instančního objektu a seznamu prostředků v účtu. Pokud používáte nový účet, seznam se vrátí jako prázdný. Pokud chcete zobrazit výsledky, můžete na portálu nahrát několik prostředků.

```ts
import * as msRestNodeAuth from "@azure/ms-rest-nodeauth";
import { AzureMediaServices } from '@azure/arm-mediaservices';
import { AzureMediaServicesOptions } from "@azure/arm-mediaservices/esm/models";
// Load the .env file if it exists
import * as dotenv from "dotenv";
dotenv.config();

export async function main() {
  // Copy the samples.env file and rename it to .env first, then populate it's values with the values obtained 
  // from your Media Services account's API Access page in the Azure portal.
  const clientId = process.env.AZURE_CLIENT_ID as string;
  const secret = process.env.AZURE_CLIENT_SECRET as string;
  const tenantDomain = process.env.AAD_TENANT_DOMAIN as string;
  const subscriptionId = process.env.AZURE_SUBSCRIPTION_ID as string;
  const resourceGroup = process.env.AZURE_RESOURCE_GROUP as string;
  const accountName = process.env.AZURE_MEDIA_ACCOUNT_NAME as string;

  let clientOptions: AzureMediaServicesOptions = {
    longRunningOperationRetryTimeout: 5, // set the time out for retries to 5 seconds
    noRetryPolicy: false // use the default retry policy.
  }

  const creds = await msRestNodeAuth.loginWithServicePrincipalSecret(clientId, secret, tenantDomain);
  const mediaClient = new AzureMediaServices(creds, subscriptionId, clientOptions);

  // List Assets in Account
  console.log("Listing Assets Names in account:")
  var assets = await mediaClient.assets.list(resourceGroup, accountName);

  assets.forEach(asset => {
    console.log(asset.name);
  });

  if (assets.odatanextLink) {
    console.log("There are more than 1000 assets in this account, use the assets.listNext() method to continue listing more assets if needed")
    console.log("For example:  assets = await mediaClient.assets.listNext(assets.odatanextLink)");
  }
}

main().catch((err) => {
  console.error("Error running sample:", err.message);
});
```

## <a name="run-the-sample-application-helloworld-listassets"></a>Spusťte ukázkovou aplikaci HelloWorld-ListAssets

Naklonujte úložiště pro ukázky Node.js.

```git
git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
```

Změnit adresář na složku AMSv3Samples
```bash
cd AMSv3Samples
```

Nainstalovat balíčky používané v packages.js
```
npm install 
```

Změnit adresář na složku HelloWorld-ListAssets
```bash
cd HelloWorld-ListAssets
```

Spusťte Visual Studio Code ze složky AMSv3Samples. Tato možnost je nutná ke spuštění ze složky, kde se nachází složka ". VSCode" a tsconfig.jsv souborech.
```dotnetcli
cd ..
code .
```

Otevřete složku pro HelloWorld-ListAssets a otevřete soubor index. TS v editoru Visual Studio Code.
V souboru index. TS stisknutím klávesy F5 spusťte ladicí program. Pokud již máte prostředky v účtu, měl by se zobrazit seznam prostředků. Pokud je účet prázdný, zobrazí se prázdný seznam.  Pokud si chcete zobrazit výsledky, nahrajte na portálu několik prostředků.

## <a name="more-samples"></a>Další ukázky

V [úložišti](https://github.com/Azure-Samples/media-services-v3-node-tutorials) jsou k dispozici následující ukázky.

|Název projektu|Případ použití|
|---|---|
|Live/index. TS| Příklad základního streamování v reálném čase **Upozornění**, zkontrolujte, že všechny prostředky se vyčistí a už se neúčtují na portálu, když se používá Live.|
|StreamFilesSample/index. TS| Základní příklad pro nahrání místního souboru nebo kódování ze zdrojové adresy URL Ukázka ukazuje, jak použít sadu SDK pro úložiště ke stažení obsahu a ukazuje, jak se streamovat do přehrávače. |
|StreamFilesWithDRMSample/index. TS| Ukazuje, jak kódovat a streamovat pomocí Widevine a PlayReady DRM. |
|VideoIndexerSample/index. TS| Příklad použití přednastavení video a audio Analyzer k vygenerování metadat a přehledů z videa nebo zvukového souboru |

## <a name="see-also"></a>Viz také

- [Media Services koncepty](concepts-overview.md)
- [instalace npm @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Azure pro JavaScript & Node.js vývojáři](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [Media Services zdrojový kód v @azure/azure-sdk-for-js úložišti](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Další kroky

Projděte si dokumentaci Media Services [Node.js reference](/javascript/api/overview/azure/mediaservices/management) a podívejte se na [ukázky](https://github.com/Azure-Samples/media-services-v3-node-tutorials) , které ukazují, jak používat rozhraní API Media Services s node.js.
