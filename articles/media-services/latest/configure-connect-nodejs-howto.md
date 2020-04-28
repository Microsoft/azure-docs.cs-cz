---
title: Připojení k Azure Media Services V3 API – Node. js
description: Tento článek ukazuje, jak se připojit k rozhraní Media Services V3 API pomocí Node. js.
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
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: 0381a2e2b8fd2a8b60e7cb702e0336a5678df057
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74896099"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Připojení k Media Services V3 API – Node. js

V tomto článku se dozvíte, jak se připojit k sadě Azure Media Services V3 Node. js SDK pomocí metody přihlašování instančního objektu.

## <a name="prerequisites"></a>Požadavky

- Nainstalujte [Node. js](https://nodejs.org/en/download/).
- [Vytvořte účet Media Services](create-account-cli-how-to.md). Nezapomeňte si pamatovat název skupiny prostředků a název účtu Media Services.

> [!IMPORTANT]
> Přečtěte si [zásady vytváření názvů](media-services-apis-overview.md#naming-conventions).

## <a name="create-packagejson"></a>Vytvořit soubor Package. JSON

1. Pomocí oblíbeného editoru vytvořte soubor Package. JSON.
1. Otevřete soubor a vložte následující kód:

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^4.1.0",
    "azure-storage": "^2.8.0",
    "ms-rest": "^2.3.3",
    "ms-rest-azure": "^2.5.5"
  }
}
```

Je třeba zadat následující balíčky:

|Balíček|Popis|
|---|---|
|`azure-arm-mediaservices`|Azure Media Services SDK. <br/>Pokud chcete mít jistotu, že používáte nejnovější balíček Azure Media Services, zkontrolujte [npm instalaci Azure-ARM-MediaServices](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|Sada SDK pro úložiště Používá se při nahrávání souborů do assetů.|
|`ms-rest-azure`| Slouží k přihlášení.|

Abyste měli jistotu, že používáte nejnovější balíček, můžete spustit následující příkaz:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Připojení k klientovi Node. js

1. Pomocí oblíbeného editoru vytvořte soubor. js.
1. Otevřete soubor a vložte následující kód.
1. Nastavte hodnoty v části "konfigurace koncového bodu" na hodnoty, které jste získali z [rozhraní API pro přístup](access-api-cli-how-to.md).

```js
'use strict';

const MediaServices = require('azure-arm-mediaservices');
const msRestAzure = require('ms-rest-azure');
const msRest = require('ms-rest');
const azureStorage = require('azure-storage');

// endpoint config
// make sure your URL values end with '/'
const armAadAudience = "";
const aadEndpoint = "";
const armEndpoint = "";
const subscriptionId = "";
const accountName = "";
const region = "";
const aadClientId = "";
const aadSecret = "";
const aadTenantId = "";
const resourceGroup = "";

let azureMediaServicesClient;

///////////////////////////////////////////
//     Entrypoint for sample script      //
///////////////////////////////////////////

msRestAzure.loginWithServicePrincipalSecret(aadClientId, aadSecret, aadTenantId, {
  environment: {
    activeDirectoryResourceId: armAadAudience,
    resourceManagerEndpointUrl: armEndpoint,
    activeDirectoryEndpointUrl: aadEndpoint
  }
}, async function(err, credentials, subscriptions) {
    if (err) return console.log(err);
    azureMediaServicesClient = new MediaServices(credentials, subscriptionId, armEndpoint, { noRetryPolicy: true });
    
    console.log("connected");

});
```

## <a name="run-your-app"></a>Spuštění aplikace

Otevřete příkazový řádek. Přejděte do adresáře ukázky a spusťte následující příkazy:

```
npm install 
node index.js
```

## <a name="see-also"></a>Viz také

- [Media Services koncepty](concepts-overview.md)
- [Instalace azure-arm-mediaservices v NPM](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>Další kroky

Prozkoumejte referenční dokumentaci Media Services [Node. js](/javascript/api/overview/azure/mediaservices/management) a podívejte se na [ukázky](https://github.com/Azure-Samples/media-services-v3-node-tutorials) , které ukazují, jak používat rozhraní API pro Media Services s Node. js.

