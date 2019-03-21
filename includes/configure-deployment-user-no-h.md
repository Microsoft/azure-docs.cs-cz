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
ms.openlocfilehash: e8888a0505a3a38d2844f82c0f7fff255d05353d
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58261336"
---
Ve službě Azure Cloud Shell, nakonfigurovat přihlašovací údaje nasazení pomocí [ `az webapp deployment user set` ](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) příkazu. Tento uživatel nasazení je vyžadován pro nasazení pomocí FTP a místního Gitu do webové aplikace. Uživatelské jméno a heslo jsou na úrovni účtu. _Jsou to liší od přihlašovacích údajů vašeho předplatného Azure._

V následujícím příkladu nahraďte  *\<uživatelské jméno >* a  *\<heslo >*, včetně závorkách a s nové uživatelské jméno a heslo. Uživatelské jméno musí být jedinečný v rámci Azure. Heslo musí obsahovat alespoň osm znaků dlouhá a dva z následujících: písmena, číslice a symboly. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Získáte výstup JSON se zobrazeným heslem `null`. Pokud se zobrazí chyba `'Conflict'. Details: 409`, změňte uživatelské jméno. Pokud se zobrazí chyba ` 'Bad Request'. Details: 400`, použijte silnější heslo.

Konfigurace tohoto uživatele nasazení pouze jednou. Můžete ho použít pro všechna nasazení v Azure.

> [!NOTE]
> Uživatelské jméno a heslo si poznamenejte. Později je budete potřebovat k nasazení webové aplikace.
>
>
