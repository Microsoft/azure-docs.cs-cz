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
ms.openlocfilehash: ebea8bfd69a4df605142ab82f3efbc7d97d34529
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2019
ms.locfileid: "67143976"
---
Ve službě Azure Cloud Shell, nakonfigurovat přihlašovací údaje nasazení pomocí [ `az webapp deployment user set` ](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) příkazu. Pomocí tohoto uživatele nasazení pro FTP a místní nasazení z Gitu do webové aplikace. Uživatelské jméno a heslo jsou na úrovni účtu. _Jsou to liší od přihlašovacích údajů vašeho předplatného Azure._

V následujícím příkladu nahraďte  *\<uživatelské jméno >* a  *\<heslo >* , včetně závorkách a s nové uživatelské jméno a heslo. Uživatelské jméno musí být jedinečný v rámci Azure. Heslo musí obsahovat alespoň osm znaků dlouhá a dva z následujících: písmena, číslice a symboly.

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Získáte výstup JSON se zobrazeným heslem `null`. Pokud se zobrazí chyba `'Conflict'. Details: 409`, změňte uživatelské jméno. Pokud se zobrazí chyba `'Bad Request'. Details: 400`, použijte silnější heslo. Nesmí obsahovat uživatelské jméno pro nasazení "@" symbol pro místní Git nabízených oznámení.

Konfigurace tohoto uživatele nasazení pouze jednou. Můžete ho použít pro všechna nasazení v Azure.

> [!NOTE]
> Uživatelské jméno a heslo si poznamenejte. Později je budete potřebovat k nasazení webové aplikace.
>
>
