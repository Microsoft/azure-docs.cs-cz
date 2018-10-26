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
ms.openlocfilehash: b5512ed5810ebd05596dc3ca7a29957b4c595c59
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50035384"
---
Ve službě Cloud Shell vytvořte přihlašovací údaje nasazení pomocí příkazu [`az webapp deployment user set`](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set). Tento uživatel nasazení je vyžadován pro nasazení pomocí FTP a místního Gitu do webové aplikace. Uživatelské jméno a heslo jsou na úrovni účtu. _To znamená, že se liší od přihlašovacích údajů předplatného Azure._

V následujícím příkladu nahraďte hodnoty *\<username>* and *\<password>* (včetně závorek) novým uživatelským jménem a heslem. Uživatelské jméno musí být jedinečné v rámci Azure. Heslo musí obsahovat alespoň osm znaků a musí v něm být použity minimálně dva z následujících typů znaků: písmena, číslice a symboly. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Měl by se zobrazit výstup JSON se zobrazeným heslem `null`. Pokud se zobrazí chyba `'Conflict'. Details: 409`, změňte uživatelské jméno. Pokud se zobrazí chyba ` 'Bad Request'. Details: 400`, použijte silnější heslo.

Tohoto uživatele nasazení vytvoříte jenom jednou a potom ho můžete použít pro všechna nasazení v Azure.

> [!NOTE]
> Uživatelské jméno a heslo si poznamenejte. Později je budete potřebovat k nasazení webové aplikace.
>
>
