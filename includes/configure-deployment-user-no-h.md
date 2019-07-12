---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/14/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 4e699707db02de07f3d1ebb7d1fa8d0575a10aa3
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836924"
---
Pomocí FTP a místního Gitu můžete nasadit webovou aplikaci Azure s využitím *uživatele nasazení*. Po konfiguraci vašeho uživatele nasazení, můžete pro všechna nasazení v Azure. Nasazení na úrovni účtu uživatelské jméno a heslo se liší od přihlašovacích údajů předplatného Azure. 

Konfigurace uživatele nasazení, spusťte [az webapp deployment uživatele sadu](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) příkazu ve službě Azure Cloud Shell. Nahraďte \<uživatelské jméno > a \<heslo > nasazení uživatelské jméno a heslo. 

- Uživatelské jméno musí být jedinečný v rámci Azure a místní Git nesmí obsahovat nabízených oznámení, "@" symbol. 
- Heslo musí obsahovat alespoň osm znaků dlouhá a dva z následujících: písmena, číslice a symboly. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Výstup JSON ukazuje heslo jako `null`. Pokud se zobrazí chyba `'Conflict'. Details: 409`, změňte uživatelské jméno. Pokud se zobrazí chyba `'Bad Request'. Details: 400`, použijte silnější heslo. 

Zaznamenejte své uživatelské jméno a heslo použité pro nasazování webových aplikací.
