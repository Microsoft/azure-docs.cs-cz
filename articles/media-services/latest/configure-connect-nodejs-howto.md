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
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: c6ea238edd68413646dda59b22d1c0dc2557d57e
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2020
ms.locfileid: "94916827"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Připojení k rozhraní Media Services V3 API – Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

V tomto článku se dozvíte, jak se připojit ke službě Azure Media Services V3 node.js SDK pomocí metody přihlašování instančního objektu.

## <a name="prerequisites"></a>Požadavky

- Nainstalujte [Node.js](https://nodejs.org/en/download/).
- [Vytvořte účet Media Services](./create-account-howto.md). Nezapomeňte si pamatovat název skupiny prostředků a název účtu Media Services.

> [!IMPORTANT]
> Přečtěte si [zásady vytváření názvů](media-services-apis-overview.md#naming-conventions).

## <a name="create-packagejson"></a>Vytvořit package.jsna

1. Vytvořte package.jsv souboru pomocí oblíbeného editoru.
1. Otevřete soubor a vložte následující kód:

   Nezapomeňte získat nejnovější verzi sady [AZUREMEDIASERVICES SDK pro JavaScript](https://www.npmjs.com/package/@azure/arm-mediaservices).

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^8.0.0",
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

## <a name="connect-to-nodejs-client"></a>Připojení k Node.js klientovi

1. Pomocí oblíbeného editoru vytvořte soubor. js.
1. Otevřete soubor a vložte následující kód.
1. Nastavte hodnoty v části "konfigurace koncového bodu" na hodnoty, které jste získali z [rozhraní API pro přístup](./access-api-howto.md).

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

## <a name="run-your-app"></a>Spusťte aplikaci

Otevřete příkazový řádek. Přejděte do adresáře ukázky a spusťte následující příkazy:

```
npm install 
node index.js
```

## <a name="see-also"></a>Viz také

- [Media Services koncepty](concepts-overview.md)
- [Instalace azure-arm-mediaservices v NPM](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>Další kroky

Projděte si dokumentaci Media Services [Node.js reference](/javascript/api/overview/azure/mediaservices/management) a podívejte se na [ukázky](https://github.com/Azure-Samples/media-services-v3-node-tutorials) , které ukazují, jak používat rozhraní API Media Services s node.js.
