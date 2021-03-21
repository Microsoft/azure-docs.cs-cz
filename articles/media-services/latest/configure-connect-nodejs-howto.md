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
ms.openlocfilehash: 33d84ca86ac3cd4696dce3797b015b861884182a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216424"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Připojení k rozhraní Media Services V3 API – Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

V tomto článku se dozvíte, jak se připojit ke službě Azure Media Services V3 node.js SDK pomocí metody přihlašování instančního objektu. Budete pracovat se soubory v úložišti ukázek *Media-Services-V3-Node-kurzy* . Ukázka *HelloWorld-ListAssets* obsahuje kód pro připojení a pak vypíše prostředky v účtu.

## <a name="prerequisites"></a>Předpoklady

- Instalace Visual Studio Code.
- Nainstalujte [Node.js](https://nodejs.org/en/download/).
- Nainstalujte [TypeScript](https://www.typescriptlang.org/download).
- [Vytvořte účet Media Services](./create-account-howto.md). Nezapomeňte si pamatovat název skupiny prostředků a název účtu Media Services.
- Vytvořte instanční objekt pro vaši aplikaci. Viz [rozhraní API pro přístup](./access-api-howto.md).<br/>**Tip pro** Nechejte toto okno otevřené nebo zkopírujte všechno na kartě JSON do poznámkového bloku. 
- Nezapomeňte získat nejnovější verzi sady [AZUREMEDIASERVICES SDK pro JavaScript](https://www.npmjs.com/package/@azure/arm-mediaservices).

> [!IMPORTANT]
> Přečtěte si Azure Media Services [konvence pojmenování](media-services-apis-overview.md#naming-conventions) a pochopte důležitá omezení pojmenování entit.

## <a name="clone-the-nodejs-samples-repo"></a>Klonování úložiště ukázek Node.JS

V ukázkách Azure budete pracovat s některými soubory. Naklonujte úložiště ukázek Node.JS.

```git
git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
```

## <a name="install-the-packages"></a>Nainstalovat balíčky

### <a name="install-azurearm-mediaservices"></a>Znovu @azure/arm-mediaservices

```bash
npm install @azure/arm-mediaservices
```

### <a name="install-azurems-rest-nodeauth"></a>Znovu @azure/ms-rest-nodeauth

Nainstalujte prosím minimální verzi " @azure/ms-rest-nodeauth ": "^ 3.0.0".

```bash
npm install @azure/ms-rest-nodeauth@"^3.0.0"
```

V tomto příkladu použijete následující balíčky v `package.json` souboru.

|Balíček|Popis|
|---|---|
|`@azure/arm-mediaservices`|Azure Media Services SDK. <br/>Pokud chcete mít jistotu, že používáte nejnovější balíček Azure Media Services, zkontrolujte [instalaci @azure/arm-mediaservices npm ](https://www.npmjs.com/package/@azure/arm-mediaservices).|
|`@azure/ms-rest-nodeauth` | Vyžadováno pro ověřování AAD pomocí instančního objektu nebo spravované identity|
|`@azure/storage-blob`|Sada SDK pro úložiště Používá se při nahrávání souborů do assetů.|
|`@azure/ms-rest-js`| Slouží k přihlášení.|
|`@azure/storage-blob` | Slouží k nahrání a stažení souborů do assetů v Azure Media Services pro kódování.|
|`@azure/abort-controller`| Používá se společně s klientem úložiště k vypršení časového limitu dlouho spuštěných operací stahování.|

### <a name="create-the-packagejson-file"></a>Vytvořit package.jsv souboru

1. `package.json`Pomocí oblíbeného editoru vytvořte soubor.
1. Otevřete soubor a vložte následující kód:

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

## <a name="connect-to-nodejs-client-using-typescript"></a>Připojení k Node.js klienta pomocí TypeScriptu



### <a name="sample-env-file"></a>Ukázkový soubor *. env*

Zkopírujte obsah tohoto souboru do souboru s názvem *. env*. Měl by být uložený v kořenovém adresáři pracovního úložiště. Jedná se o hodnoty, které jste získali ze stránky přístup k rozhraní API pro váš Media Services účet na portálu.

Po vytvoření souboru *. env* můžete začít pracovat s ukázkami.

```nodejs
# Values from the API Access page in the portal
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

## <a name="run-the-sample-application-helloworld-listassets"></a>Spuštění ukázkové aplikace *HelloWorld – ListAssets*

1. Změnit adresář na složku *AMSv3Samples*

```bash
cd AMSv3Samples
```

2. Nainstaluje balíčky používané v *packages.js* souboru.

```
npm install 
```

3. Změňte adresář na složku *HelloWorld-ListAssets* .

```bash
cd HelloWorld-ListAssets
```

4. Spusťte Visual Studio Code ze složky AMSv3Samples. Tato možnost je nutná ke spuštění ze složky, kde se nachází složka ". VSCode" a tsconfig.jsv souborech.

```dotnetcli
cd ..
code .
```

Otevřete složku pro *HelloWorld-ListAssets* a otevřete soubor *index. ts* v editoru Visual Studio Code.

V souboru *index. TS* stisknutím klávesy F5 spusťte ladicí program. Pokud již máte prostředky v účtu, měl by se zobrazit seznam prostředků. Pokud je účet prázdný, zobrazí se prázdný seznam.  

K rychlému zobrazení uvedených prostředků použijte portál k nahrání několika videosouborů. Assety se automaticky vytvoří každou z nich a znovu spustí tento skript a pak vrátí jejich názvy.

### <a name="a-closer-look-at-the-helloworld-listassets-sample"></a>Podrobnější přehled o ukázce *HelloWorld-ListAssets*

Ukázka *HelloWorld-ListAssets* vám ukáže, jak se připojit k Media Services klienta s objektem služby a vypsat prostředky v účtu. Podrobné vysvětlení toho, co dělá, najdete v komentářích v kódu.

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

## <a name="more-samples"></a>Další ukázky

V [úložišti](https://github.com/Azure-Samples/media-services-v3-node-tutorials) jsou k dispozici následující ukázky.

|Název projektu|Případ použití|
|---|---|
|Live/index. TS| Příklad základního streamování v reálném čase **Upozornění**, zkontrolujte, že všechny prostředky se vyčistí a už se neúčtují na portálu, když se používá Live.|
|StreamFilesSample/index. TS| Základní příklad pro nahrání místního souboru nebo kódování ze zdrojové adresy URL Ukázka ukazuje, jak použít sadu SDK pro úložiště ke stažení obsahu a ukazuje, jak se streamovat do přehrávače. |
|StreamFilesWithDRMSample/index. TS| Ukazuje, jak kódovat a streamovat pomocí Widevine a PlayReady DRM. |
|VideoIndexerSample/index. TS| Příklad použití přednastavení video a audio Analyzer k vygenerování metadat a přehledů z videa nebo zvukového souboru |

## <a name="see-also"></a>Viz také

- [Referenční dokumentace k modulům Azure Media Services pro Node.js](/javascript/api/overview/azure/media-services)
- [Azure pro JavaScript & Node.js vývojáři](/azure/developer/javascript/)
- [Media Services zdrojový kód v @azure/azure-sdk-for-js úložišti centra Git](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Dokumentace k balíčku Azure pro vývojáře Node.js](/javascript/api/overview/azure/)
- [Media Services koncepty](concepts-overview.md)
- [instalace npm @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Azure pro JavaScript & Node.js vývojáři](/azure/developer/javascript/)
- [Media Services zdrojový kód v @azure/azure-sdk-for-js úložišti](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Další kroky

Projděte si dokumentaci Media Services [Node.js reference](/javascript/api/overview/azure/mediaservices/management) a podívejte se na [ukázky](https://github.com/Azure-Samples/media-services-v3-node-tutorials) , které ukazují, jak používat rozhraní API Media Services s node.js.
