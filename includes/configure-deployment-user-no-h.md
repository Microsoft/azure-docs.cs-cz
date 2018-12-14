---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 95d89da66935ce933fee082a5f53ee2e36ea953f
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344682"
---
Ve službě Cloud Shell, nakonfigurovat přihlašovací údaje nasazení pomocí [ `az webapp deployment user set` ](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) příkazu. Tento uživatel nasazení je vyžadován pro nasazení pomocí FTP a místního Gitu do webové aplikace. Uživatelské jméno a heslo jsou na úrovni účtu. _To znamená, že se liší od přihlašovacích údajů předplatného Azure._

V následujícím příkladu nahraďte hodnoty *\<username>* and *\<password>* (včetně závorek) novým uživatelským jménem a heslem. Uživatelské jméno musí být jedinečné v rámci Azure. Heslo musí obsahovat alespoň osm znaků a musí v něm být použity minimálně dva z následujících typů znaků: písmena, číslice a symboly. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Měl by se zobrazit výstup JSON se zobrazeným heslem `null`. Pokud se zobrazí chyba `'Conflict'. Details: 409`, změňte uživatelské jméno. Pokud se zobrazí chyba ` 'Bad Request'. Details: 400`, použijte silnější heslo.

Je nutné nakonfigurovat jen jednou; tohoto uživatele nasazení můžete ho použít pro všechna nasazení v Azure.

> [!NOTE]
> Uživatelské jméno a heslo si poznamenejte. Později je budete potřebovat k nasazení webové aplikace.
>
>
