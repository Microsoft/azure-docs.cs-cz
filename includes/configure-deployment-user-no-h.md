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
ms.openlocfilehash: cd7fc7487a41979f37c9a55baeb0b8e172e808c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66133177"
---
Ve službě Azure Cloud Shell, nakonfigurovat přihlašovací údaje nasazení pomocí [ `az webapp deployment user set` ](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) příkazu. Tento uživatel nasazení je vyžadován pro nasazení pomocí FTP a místního Gitu do webové aplikace. Uživatelské jméno a heslo jsou na úrovni účtu. _Jsou to liší od přihlašovacích údajů vašeho předplatného Azure._

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
