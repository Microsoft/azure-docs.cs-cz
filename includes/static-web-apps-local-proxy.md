---
author: burkeholland
ms.service: static-web-apps
ms.topic: include
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 879d0c2e8c4cd66ce04c0298d00f7d6a1987acf5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "83597230"
---
#### <a name="local-proxy"></a>Místní proxy server

Můžete nakonfigurovat proxy server pro rozšíření Live Server Visual Studio Code, který směruje všechny požadavky na `/api` koncový bod rozhraní API na `http://127.0.0.1:7071/api` .

1. Otevřete soubor _. VSCode/settings.jsv_ souboru.

1. Přidejte následující nastavení pro určení proxy serveru pro živý Server.

   ```json
   "liveServer.settings.proxy": {
      "enable": true,
      "baseUri": "/api",
      "proxyUri": "http://127.0.0.1:7071/api"
   }
   ```

   Tato konfigurace je nejlépe uložena v souboru nastavení projektu, nikoli v souboru uživatelských nastavení.

   Použití nastavení projektu zaručuje, že se proxy server nepoužije pro všechny ostatní projekty otevřené v Visual Studio Code.
