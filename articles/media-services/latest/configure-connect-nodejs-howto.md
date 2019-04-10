---
title: Připojení k rozhraní API služby Azure Media Services v3 – Node.js
description: Zjistěte, jak se připojit k rozhraní API služby Media Services v3 s využitím Node.js.
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
ms.openlocfilehash: 7f3afa59b4c8eaaeaf54576eb9fcaad626749683
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358912"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Připojení k rozhraní API služby Media Services v3 – Node.js

V tomto článku se dozvíte, jak se připojit k Azure Media Services v3 node.js SDK pomocí přihlašovacího instančního objektu služby v metodě.

## <a name="prerequisites"></a>Požadavky

- Nainstalujte [Node.js](https://nodejs.org/en/download/).
- [Vytvoření účtu Media Services](create-account-cli-how-to.md). Je potřeba pamatovat název skupiny prostředků a název účtu Media Services.

## <a name="create-packagejson"></a>Vytvořte soubor package.json

1. Vytvořte soubor package.json pomocí oblíbeného editoru.
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

Následující balíčky se musí nastavit:

|Balíček|Popis|
|---|---|
|`azure-arm-mediaservices`|Azure Media Services SDK. <br/>Chcete-li mít jistotu, že používáte nejnovější balíček Azure Media Services, zkontrolujte [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|Sada SDK úložiště. Při nahrávání souborů do prostředků.|
|`ms-rest-azure`| Používá k přihlášení.|

Můžete spustit následující příkaz, abyste měli jistotu, že používáte nejnovější balíček:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Připojte se ke klientovi Node.js

1. Vytvořte soubor .js pomocí oblíbeného editoru.
1. Otevřete soubor a vložte následující kód.
1. Nastavte hodnoty v oddílu "konfigurace koncového bodu" na hodnoty, jste získali z [přístup k rozhraním API](access-api-cli-how-to.md).

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

## <a name="see-also"></a>Další informace najdete v tématech

[.NET – referenční informace](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)

## <a name="next-steps"></a>Další postup

- [Koncepty služby Media Services](concepts-overview.md)
- [Referenční informace k Node.js](https://docs.microsoft.com/javascript/api/azure-arm-mediaservices/?view=azure-node-latest)
- [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)<br>
