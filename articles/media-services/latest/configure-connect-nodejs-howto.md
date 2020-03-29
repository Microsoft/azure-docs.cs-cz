---
title: Připojení k rozhraní API Azure Media Services v3 – node.js
description: Tento článek ukazuje, jak se připojit k rozhraní API Media Services v3 s Node.js.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896099"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Připojení k rozhraní API media services v3 – node.js

Tento článek ukazuje, jak se připojit k Azure Media Services v3 node.js SDK pomocí metody přihlášení instanční ho instančního objektu.

## <a name="prerequisites"></a>Požadavky

- Nainstalujte [soubor Node.js](https://nodejs.org/en/download/).
- [Vytvořte účet mediálních služeb](create-account-cli-how-to.md). Nezapomeňte si zapamatovat název skupiny prostředků a název účtu Služby Media Services.

> [!IMPORTANT]
> Zkontrolujte [konvence pojmenování](media-services-apis-overview.md#naming-conventions).

## <a name="create-packagejson"></a>Vytvořit soubor package.json

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

Je třeba specifikovat následující balíčky:

|Balíček|Popis|
|---|---|
|`azure-arm-mediaservices`|Azure Media Services SDK. <br/>Chcete-li se ujistit, že používáte nejnovější balíček Azure Media Services, zkontrolujte, zda [npm nainstalovat azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|Úložiště Sady SDK. Používá se při nahrávání souborů do datových zdrojů.|
|`ms-rest-azure`| Používá se k přihlášení.|

Můžete spustit následující příkaz, abyste se ujistili, že používáte nejnovější balíček:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Připojení ke klientovi Node.js

1. Vytvořte soubor JS pomocí oblíbeného editoru.
1. Otevřete soubor a vložte následující kód.
1. Nastavte hodnoty v části "konfigurace koncového bodu" na hodnoty, které jste získali z [přístupových api](access-api-cli-how-to.md).

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

Otevřete příkazový řádek. Přejděte do adresáře ukázky a proveďte následující příkazy:

```
npm install 
node index.js
```

## <a name="see-also"></a>Viz také

- [Koncepty mediálních služeb](concepts-overview.md)
- [Instalace azure-arm-mediaservices v NPM](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>Další kroky

Prozkoumejte dokumentaci [ref.js node.js](/javascript/api/overview/azure/mediaservices/management) mediálních služeb a prohlédněte si [ukázky,](https://github.com/Azure-Samples/media-services-v3-node-tutorials) které ukazují, jak používat rozhraní API mediálních služeb s souborem node.js.

