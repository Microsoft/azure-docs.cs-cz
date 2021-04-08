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
ms.openlocfilehash: a972b4738ce5646a1ee9eed6495bdc43a40826fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102219028"
---
FTP a místní Git se můžou nasadit do webové aplikace Azure pomocí *uživatele nasazení*. Jakmile nakonfigurujete uživatele nasazení, můžete ho použít pro všechna nasazení Azure. Uživatelské jméno a heslo nasazení na úrovni účtu se liší od přihlašovacích údajů předplatného Azure. 

Pokud chcete nakonfigurovat uživatele nasazení, spusťte v Azure Cloud Shell příkaz [AZ WebApp Deployment User set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) . Nahraďte \<username> a \<password> pomocí uživatelského jména a hesla pro nasazení. 

- Uživatelské jméno musí být v rámci Azure jedinečné a pro místní nabízená oznámení Git nesmí obsahovat \@ symbol. 
- Heslo musí mít délku alespoň osm znaků a dva z následujících tří prvků: písmena, číslice a symboly. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Výstup JSON zobrazuje heslo jako `null` . Pokud se zobrazí chyba `'Conflict'. Details: 409`, změňte uživatelské jméno. Pokud se zobrazí chyba `'Bad Request'. Details: 400`, použijte silnější heslo. 

Poznamenejte si uživatelské jméno a heslo, které chcete použít k nasazení webových aplikací.
